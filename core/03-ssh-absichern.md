# Schritt 3: SSH absichern

🔍 **Prüfen** – erst checken ob bereits abgesichert, nur bei Bedarf durchführen.

---

## Kurz-Info für den User

> "SSH ist die Tür zu deinem Server. Ich prüfe ob sie schon abgesichert ist – falls nicht, ändere ich den Port und erlaube nur noch deinen Schlüssel."

## ZUERST PRÜFEN

```bash
ss -tlnp | grep sshd
grep "^PasswordAuthentication" /etc/ssh/sshd_config
```

**Wenn SSH bereits auf Port 2222 läuft UND PasswordAuthentication=no → Schritt überspringen!**
Sage dem User: "SSH ist schon abgesichert ✅ – weiter zum nächsten Schritt."

Falls nicht → weiter mit der Anleitung:

---

**Goldene Regel:** Alte SSH-Verbindung IMMER offen lassen. Neue Einstellungen in einem NEUEN Fenster testen.

## Anleitung

### 3.1 — Backup der Config

```bash
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

### 3.2 — Auf Ubuntu 24.04: Socket-Aktivierung prüfen

```bash
systemctl is-active ssh.socket
```

**🪤 Falle:** Wenn "active" (Ubuntu 24.04+), wird der Port über systemd geändert:

```bash
mkdir -p /etc/systemd/system/ssh.socket.d
cat > /etc/systemd/system/ssh.socket.d/port.conf << 'EOF'
[Socket]
ListenStream=
ListenStream=0.0.0.0:2222
ListenStream=[::]:2222
EOF
```

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

Alte Werte auskommentieren:
```bash
sed -i 's/^PasswordAuthentication yes/# PasswordAuthentication yes  # DEAKTIVIERT/' /etc/ssh/sshd_config
```

### 3.4 — Config testen

```bash
sshd -t
```

Fehler? → NICHT weitermachen. Erst beheben.

### 3.5 — Firewall aktualisieren

```bash
ufw allow 2222/tcp comment "SSH neuer Port"
# Alten Port 22 noch NICHT löschen!
```

**Hetzner Cloud Firewall:** Port 2222 muss auch dort freigeschaltet werden!

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

**Funktioniert?** → Alten Port entfernen:
```bash
ufw delete allow 22/tcp
```

**Funktioniert NICHT?** → Im alten Fenster zurückrollen:
```bash
cp /etc/ssh/sshd_config.backup /etc/ssh/sshd_config
rm -rf /etc/systemd/system/ssh.socket.d
systemctl daemon-reload && systemctl restart ssh.socket ssh
```

### 3.8 — VS Code Config aktualisieren

Sage dem User:
> "Ich habe den SSH-Zugang geändert. Damit VS Code weiterhin verbinden kann, gib bitte diesen Prompt in VS Code ein:"

Dann gib dem User folgenden Prompt zum Kopieren:
```
Mein Server SSH-Port wurde von 22 auf 2222 geändert. Bitte aktualisiere meine SSH-Config (~/.ssh/config) für den Host openclaw-server: Ändere den Port auf 2222. Falls der Eintrag noch nicht existiert, erstelle ihn mit HostName [SERVER_IP], User root, Port 2222.
```

## Prüfen

```bash
ss -tlnp | grep sshd          # Sollte Port 2222 zeigen
grep "^PasswordAuthentication" /etc/ssh/sshd_config    # Sollte "no" zeigen
```

## Checkliste

- [ ] SSH bereits abgesichert? → Überspringen
- [ ] Backup der SSH-Config erstellt
- [ ] Socket-Aktivierung geprüft
- [ ] Neue Config geschrieben + getestet
- [ ] Firewall-Port 2222 geöffnet
- [ ] SSH neu gestartet + getestet
- [ ] Alter Port 22 entfernt
- [ ] User hat VS Code Prompt bekommen
