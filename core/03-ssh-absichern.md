# Schritt 3: SSH absichern

⚠️ **Mittleres Risiko** – hier ändert sich dein Server-Zugang. Fehler können aussperren.

**Goldene Regel:** Alte SSH-Verbindung IMMER offen lassen. Neue Einstellungen in einem NEUEN Fenster testen. Erst wenn das neue Fenster funktioniert, ist es sicher.

---

## Was passiert hier?

SSH ist die Tür zu deinem Server. Gerade steht sie auf dem Standard-Port (22) offen – da klopfen automatische Bots innerhalb von Sekunden an. Wir:
1. Ändern den Port (von 22 auf z.B. 2222) – wie eine versteckte Hintertür
2. Erlauben nur SSH-Keys – keine Passwörter mehr

## So erklärst du es deinem User

> "SSH ist wie die Haustür deines Servers. Gerade hat sie die Standard-Adresse und jeder Einbrecher-Bot weiß wo sie ist. Ich ändere die Adresse und erlaube nur noch deinen Schlüssel (SSH-Key). Danach: gleicher Zugang für dich, aber unsichtbar für Angreifer."

> ⚠️ "Wichtig: Nach diesem Schritt musst du deine SSH-Verbindung anpassen – neuer Port. Ich zeige dir genau was du ändern musst."

## Anleitung

### 3.1 — Backup der Config

```bash
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

Das ist deine Rettungsleine falls etwas schiefgeht.

### 3.2 — Auf Ubuntu 24.04: Socket-Aktivierung prüfen

```bash
systemctl is-active ssh.socket
```

**🪤 Falle:** Wenn hier "active" steht (Ubuntu 24.04+), wird der Port NICHT über die SSH-Config geändert sondern über systemd:

```bash
mkdir -p /etc/systemd/system/ssh.socket.d
cat > /etc/systemd/system/ssh.socket.d/port.conf << 'EOF'
[Socket]
ListenStream=
ListenStream=0.0.0.0:2222
ListenStream=[::]:2222
EOF
```

Das leere `ListenStream=` löscht den alten Standard. Ohne das bleibt Port 22 zusätzlich offen.

### 3.3 — SSH-Config anpassen

```bash
cat >> /etc/ssh/sshd_config << 'EOF'

# ============================================
# HARDENED SSH CONFIG
# ============================================
Port 2222
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
LoginGraceTime 30
ClientAliveInterval 300
ClientAliveCountMax 2
X11Forwarding no
PermitEmptyPasswords no
EOF
```

Alte Werte auskommentieren (falls vorhanden):
```bash
sed -i 's/^PasswordAuthentication yes/# PasswordAuthentication yes  # DEAKTIVIERT/' /etc/ssh/sshd_config
```

### 3.4 — Config testen BEVOR neu gestartet wird

```bash
sshd -t
```

Wenn hier **irgendein Fehler** kommt: **NICHT weitermachen.** Erst den Fehler beheben.

### 3.5 — Firewall ZUERST aktualisieren

**🪤 Falle:** Wenn die Firewall nur Port 22 erlaubt und du SSH auf 2222 änderst → ausgesperrt!

```bash
ufw allow 2222/tcp comment "SSH neuer Port"
# Den alten Port 22 noch NICHT löschen – erst wenn 2222 funktioniert
```

**Hetzner Cloud Firewall:** Falls du eine Hetzner Firewall hast, muss Port 2222 auch DORT freigeschaltet werden (im Hetzner Cloud Dashboard oder per API). Die Server-Firewall (UFW) und die Hetzner-Firewall sind zwei verschiedene Ebenen.

### 3.6 — SSH neu starten

```bash
# Bei Socket-Aktivierung (Ubuntu 24.04):
systemctl daemon-reload
systemctl restart ssh.socket
systemctl restart ssh

# Ohne Socket-Aktivierung:
systemctl restart ssh
```

### 3.7 — In NEUEM Fenster testen

```bash
ssh root@[SERVER_IP] -p 2222
```

**Funktioniert?** → Dann alten Port 22 aus der Firewall entfernen:
```bash
ufw delete allow 22/tcp
```

**Funktioniert NICHT?** → Im alten (noch offenen!) Fenster:
```bash
cp /etc/ssh/sshd_config.backup /etc/ssh/sshd_config
rm -rf /etc/systemd/system/ssh.socket.d
systemctl daemon-reload && systemctl restart ssh.socket ssh
```

### 3.8 — User informieren

Sage deinem User:
> "SSH-Zugang geändert. Ab jetzt verbindest du dich so:
> - **Port:** 2222 (statt 22)
> 
> In VS Code SSH Config:
> ```
> Host mein-server
>   HostName [SERVER_IP]
>   User root
>   Port 2222
> ```"

## Prüfen

```bash
ss -tlnp | grep sshd          # Sollte Port 2222 zeigen
grep "^PasswordAuthentication" /etc/ssh/sshd_config    # Sollte "no" zeigen
```

## Checkliste

- [ ] Backup der SSH-Config erstellt
- [ ] Socket-Aktivierung geprüft (und Override erstellt falls nötig)
- [ ] Neue Config geschrieben
- [ ] `sshd -t` ohne Fehler
- [ ] Firewall-Port 2222 geöffnet (UFW + Hosting-Anbieter!)
- [ ] SSH neu gestartet
- [ ] Neuer Zugang getestet (Port 2222)
- [ ] Alter Port 22 aus Firewall entfernt
- [ ] User über neue Zugangsdaten informiert
