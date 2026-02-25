# Schritt 0: Vorbereitung

🟢 **Kein Risiko** – wir machen erstmal nur ein Backup.

---

## Was passiert hier?

Bevor wir irgendetwas am Server ändern, machen wir ein Backup. Falls etwas schiefgeht, können wir jederzeit auf diesen Stand zurück.

## So erklärst du es deinem User

> "Ich sichere jetzt den aktuellen Stand deines Servers. Das ist wie ein Foto vom Ist-Zustand – falls wir es brauchen, können wir jederzeit zurück."

## Anleitung

### 0.1 — Snapshot beim Hosting-Anbieter erstellen

**Hetzner:** Cloud Console → Server → Snapshots → "Snapshot erstellen"
**Contabo:** Snapshots im Control Panel
**Andere Anbieter:** Im jeweiligen Dashboard nach "Snapshot" oder "Backup" suchen

⚠️ **Der User muss das selbst machen** (im Browser, beim Hosting-Anbieter). Das kannst du als Assistent nicht über SSH.

**Ausnahme:** Wenn der User einen Hetzner API Token hat, kannst du den Snapshot per API erstellen:
```bash
curl -s -X POST \
  -H "Authorization: Bearer $HETZNER_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"description": "pre-hardening-backup", "type": "snapshot"}' \
  "https://api.hetzner.cloud/v1/servers/$SERVER_ID/actions/create_image"
```

### 0.2 — Festplatten-Check

```bash
df -h /
```

Wenn mehr als 80% belegt: Erst aufräumen.

```bash
# Journal-Logs bereinigen (oft mehrere GB)
sudo journalctl --vacuum-size=500M

# Alte Pakete entfernen
sudo apt autoremove -y
sudo apt clean
```

### 0.3 — Zugang prüfen

Stelle sicher, dass du per SSH auf den Server kommst:
```bash
ssh root@[SERVER_IP]
whoami  # Sollte "root" zeigen
```

## Checkliste

- [ ] Snapshot/Backup erstellt
- [ ] Festplatte unter 80%
- [ ] SSH-Zugang funktioniert
