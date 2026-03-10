# Schritt 0: Vorbereitung

🟢 **Automatisch** – Backup und Zugang prüfen.

---

## Kurz-Info für den User

> "Ich sichere den aktuellen Stand deines Servers und prüfe ob alles bereit ist."

## Anleitung

### 0.1 — Snapshot beim Hosting-Anbieter erstellen

**Hetzner:** Cloud Console → Server → Snapshots → "Snapshot erstellen"

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
journalctl --vacuum-size=500M
apt autoremove -y
apt clean
```

### 0.3 — Zugang prüfen

```bash
ssh root@[SERVER_IP]
whoami  # Sollte "root" zeigen
```

## Checkliste

- [ ] Snapshot/Backup erstellt
- [ ] Festplatte unter 80%
- [ ] SSH-Zugang funktioniert
