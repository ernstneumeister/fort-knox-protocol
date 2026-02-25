# Schritt 8: Kernel absichern

🟢 **Geringes Risiko** – keine Auswirkung auf normalen Betrieb.

---

## Was passiert hier?

Der Kernel ist das Betriebssystem unter der Haube. Wir ändern Einstellungen die Angriffe erschweren: keine Weiterleitungen, keine Info-Leaks, kein unnötiges IPv6, und weniger Speicherverbrauch durch optimiertes Swapping.

## So erklärst du es deinem User

> "Ich stelle jetzt das Betriebssystem so ein, dass es weniger Informationen nach außen gibt und bestimmte Angriffsarten blockiert. Das ist wie Vorhänge zuziehen und die Hintertür abschließen – von innen merkst du keinen Unterschied."

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

⚠️ **Docker-User:** Wenn Docker auf dem Server läuft, `net.ipv4.ip_forward` NICHT auf 0 setzen – Docker braucht das!

### 8.2 — Einstellungen aktivieren

```bash
export PATH="/usr/sbin:/usr/bin:/sbin:/bin:$PATH"
sudo sysctl --system
```

### 8.3 — Boot-Service erstellen

Manche Einstellungen werden beim Neustart zurückgesetzt. Dieser Service wendet sie nach jedem Boot erneut an:

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

sudo systemctl daemon-reload
sudo systemctl enable sysctl-hardening.service
```

### 8.4 — Prozesse zwischen Usern verstecken

Verhindert, dass ein User die Prozesse anderer User sehen kann:

```bash
sudo mount -o remount,hidepid=2 /proc
echo "proc /proc proc defaults,hidepid=2 0 0" | sudo tee -a /etc/fstab
```

### 8.5 — IPv6 in der Firewall deaktivieren

```bash
sudo sed -i 's/^IPV6=yes/IPV6=no/' /etc/default/ufw
sudo ufw reload
```

## Prüfen

**Wichtig: LAUFENDE Werte prüfen, nicht nur die Config-Datei!**

```bash
export PATH="/usr/sbin:/usr/bin:/sbin:/bin:$PATH"
sysctl net.ipv4.conf.all.accept_redirects     # Sollte 0 sein
sysctl net.ipv4.tcp_syncookies                # Sollte 1 sein
sysctl kernel.randomize_va_space              # Sollte 2 sein
sysctl kernel.dmesg_restrict                  # Sollte 1 sein
sysctl vm.swappiness                          # Sollte 10 sein
sysctl net.ipv6.conf.all.disable_ipv6         # Sollte 1 sein
mount | grep hidepid                          # Sollte hidepid zeigen
```

## Checkliste

- [ ] Sysctl-Config erstellt
- [ ] Einstellungen aktiviert
- [ ] Boot-Service eingerichtet
- [ ] /proc versteckt (hidepid)
- [ ] IPv6 deaktiviert (sysctl + UFW)
- [ ] Alle Laufzeit-Werte geprüft
