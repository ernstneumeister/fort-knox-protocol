# Schritt 8: Kernel absichern

🟢 **Automatisch** – keine Auswirkung auf normalen Betrieb.

---

## Kurz-Info für den User

> "Betriebssystem-Einstellungen härten: weniger Informationen nach außen, bekannte Angriffe blockieren."

## Anleitung

### 8.1 — Sicherheits-Einstellungen erstellen

```bash
cat > /etc/sysctl.d/99-hardening.conf << 'EOF'
# Fort Knox Protocol — Kernel-Absicherung

# --- Netzwerk ---
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv4.tcp_syncookies = 1
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1
net.ipv4.icmp_echo_ignore_broadcasts = 1
net.ipv4.icmp_ignore_bogus_error_responses = 1

# --- Kernel ---
kernel.randomize_va_space = 2
kernel.dmesg_restrict = 1
kernel.kptr_restrict = 1
kernel.yama.ptrace_scope = 2

# --- Speicher ---
vm.swappiness = 10
vm.overcommit_memory = 0

# --- IPv6 deaktivieren ---
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
EOF
```

⚠️ **Docker-User:** Wenn Docker auf dem Server läuft, `net.ipv4.ip_forward` NICHT auf 0 setzen!

### 8.2 — Einstellungen aktivieren

```bash
export PATH="/usr/sbin:/usr/bin:/sbin:/bin:$PATH"
sysctl --system
```

### 8.3 — Boot-Service erstellen

```bash
cat > /etc/systemd/system/sysctl-hardening.service << 'EOF'
[Unit]
Description=Sysctl Hardening nach Boot anwenden
After=network.target

[Service]
Type=oneshot
ExecStart=/sbin/sysctl --system
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable sysctl-hardening.service
```

### 8.4 — Prozesse verstecken

```bash
mount -o remount,hidepid=2 /proc
echo "proc /proc proc defaults,hidepid=2 0 0" | tee -a /etc/fstab
```

### 8.5 — IPv6 in der Firewall deaktivieren

```bash
sed -i 's/^IPV6=yes/IPV6=no/' /etc/default/ufw
ufw reload
```

## Prüfen

```bash
export PATH="/usr/sbin:/usr/bin:/sbin:/bin:$PATH"
sysctl net.ipv4.conf.all.accept_redirects     # 0
sysctl net.ipv4.tcp_syncookies                # 1
sysctl kernel.randomize_va_space              # 2
sysctl kernel.dmesg_restrict                  # 1
sysctl vm.swappiness                          # 10
sysctl net.ipv6.conf.all.disable_ipv6         # 1
mount | grep hidepid
```

## Checkliste

- [ ] Sysctl-Config erstellt + aktiviert
- [ ] Boot-Service eingerichtet
- [ ] /proc versteckt (hidepid)
- [ ] IPv6 deaktiviert (sysctl + UFW)
