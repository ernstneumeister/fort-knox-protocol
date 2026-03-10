# Schritt 1: System-Updates

🟢 **Automatisch** – einfach durchführen.

---

## Kurz-Info für den User

> "Updates installieren und automatische Sicherheits-Updates einrichten."

## Anleitung

### 1.1 — Updates installieren

```bash
apt update && apt upgrade -y
```

### 1.2 — Sicherheits-Tools installieren

```bash
apt install -y fail2ban ufw unattended-upgrades
```

### 1.3 — Automatische Updates einrichten

```bash
dpkg-reconfigure -plow unattended-upgrades
```

Wähle "Yes" wenn gefragt.

### 1.4 — Automatischen Neustart konfigurieren

Manche Updates (Kernel) brauchen einen Neustart. Das soll nachts passieren:

```bash
cat > /etc/apt/apt.conf.d/51unattended-upgrades-custom << 'EOF'
// Automatischer Neustart bei Kernel-Updates
Unattended-Upgrade::Automatic-Reboot "true";
// Neustart um 04:00 UTC
Unattended-Upgrade::Automatic-Reboot-Time "04:00";
EOF
```

## Prüfen

```bash
systemctl is-active unattended-upgrades
ls /etc/apt/apt.conf.d/51*
```

## Checkliste

- [ ] System-Updates installiert
- [ ] fail2ban, ufw, unattended-upgrades installiert
- [ ] Automatische Updates aktiviert
- [ ] Auto-Reboot auf 04:00 UTC konfiguriert
