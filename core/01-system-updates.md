# Schritt 1: System-Updates

🟢 **Kein Risiko** – nur Updates installieren.

---

## Was passiert hier?

Wir bringen das Betriebssystem auf den neuesten Stand und stellen sicher, dass Sicherheits-Updates in Zukunft automatisch kommen.

## So erklärst du es deinem User

> "Ich installiere jetzt alle verfügbaren Updates für dein Betriebssystem – wie App-Updates auf dem Handy. Danach richte ich automatische Sicherheits-Updates ein, damit der Server sich selbst aktuell hält."

## Anleitung

### 1.1 — Updates installieren

```bash
sudo apt update && sudo apt upgrade -y
```

Das kann ein paar Minuten dauern. Normal.

### 1.2 — Sicherheits-Tools installieren

```bash
sudo apt install -y fail2ban ufw unattended-upgrades
```

- **fail2ban** = sperrt Angreifer automatisch nach zu vielen fehlgeschlagenen Login-Versuchen
- **ufw** = einfache Firewall
- **unattended-upgrades** = automatische Sicherheits-Updates

### 1.3 — Automatische Updates einrichten

```bash
sudo dpkg-reconfigure -plow unattended-upgrades
```

Wähle "Yes" wenn gefragt.

### 1.4 — Automatischen Neustart konfigurieren

Manche Updates (Kernel) brauchen einen Neustart. Das soll nachts passieren, nicht tagsüber:

```bash
cat > /etc/apt/apt.conf.d/51unattended-upgrades-custom << 'EOF'
// Automatischer Neustart bei Kernel-Updates
Unattended-Upgrade::Automatic-Reboot "true";
// Neustart um 04:00 UTC (06:00 deutscher Zeit im Winter, 05:00 im Sommer)
Unattended-Upgrade::Automatic-Reboot-Time "04:00";
EOF
```

⚠️ **Wichtig:** Drop-in Datei (`51-...`) verwenden, nicht die Hauptdatei editieren – sonst werden unsere Einstellungen beim nächsten Update überschrieben.

## Prüfen

```bash
systemctl is-active unattended-upgrades
# Sollte "active" zeigen

ls /etc/apt/apt.conf.d/51*
# Sollte unsere Datei zeigen
```

## Checkliste

- [ ] System-Updates installiert
- [ ] fail2ban, ufw, unattended-upgrades installiert
- [ ] Automatische Updates aktiviert
- [ ] Auto-Reboot auf 04:00 UTC konfiguriert
