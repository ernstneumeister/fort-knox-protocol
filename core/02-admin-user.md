# Schritt 2: Admin-User anlegen

🟢 **Kein Risiko** – wir erstellen nur einen neuen Benutzer.

---

## Was passiert hier?

Aktuell bist du als "root" angemeldet – das ist der Superuser, der alles darf. Das ist wie mit dem Generalschlüssel durch die Firma zu laufen. Wir erstellen einen normalen Admin-User, der nur bei Bedarf Root-Rechte bekommt.

## So erklärst du es deinem User

> "Stell dir vor, root ist der Generalschlüssel – damit kannst du alles machen, auch versehentlich etwas Wichtiges löschen. Ich erstelle jetzt einen normalen Admin-User. Der kann alles was nötig ist, aber muss sich für kritische Aktionen extra bestätigen."

## Anleitung

### 2.1 — User erstellen

```bash
sudo adduser admin --disabled-password --gecos ""
sudo usermod -aG sudo admin
```

### 2.2 — SSH-Key kopieren

Damit du dich als admin per SSH einloggen kannst (ohne Passwort):

```bash
sudo mkdir -p /home/admin/.ssh
sudo cp /root/.ssh/authorized_keys /home/admin/.ssh/
sudo chown -R admin:admin /home/admin/.ssh
sudo chmod 700 /home/admin/.ssh
sudo chmod 600 /home/admin/.ssh/authorized_keys
```

### 2.3 — Sudo ohne Passwort

Da wir kein Passwort gesetzt haben, muss sudo ohne Passwort funktionieren:

```bash
echo "admin ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/admin
sudo chmod 440 /etc/sudoers.d/admin
```

### 2.4 — Testen

```bash
# Als admin einloggen (neue SSH-Verbindung!)
ssh admin@[SERVER_IP]

# Prüfen
whoami           # Sollte "admin" zeigen
sudo whoami      # Sollte "root" zeigen (sudo funktioniert)
```

⚠️ **Alte SSH-Verbindung offen lassen** bis die neue funktioniert!

## Checkliste

- [ ] User "admin" erstellt
- [ ] SSH-Key kopiert
- [ ] Sudo funktioniert
- [ ] Login als admin getestet
