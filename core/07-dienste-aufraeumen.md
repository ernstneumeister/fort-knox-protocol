# Schritt 7: Dienste aufräumen

🟢 **Geringes Risiko**

---

## Was passiert hier?

Auf deinem Server laufen möglicherweise Dienste die du nicht brauchst – und die trotzdem von außen erreichbar sind. Das ist wie offene Fenster in Räumen die du nie benutzt. Wir finden und schließen sie.

## So erklärst du es deinem User

> "Ich schaue jetzt welche Programme auf deinem Server laufen und von außen erreichbar sind. Alles was nicht gebraucht wird, mache ich zu. Das verkleinert die Angriffsfläche."

## Anleitung

### 7.1 — Alle offenen Dienste finden

```bash
sudo ss -tlnp | grep LISTEN
```

Alles was auf `0.0.0.0` hört, ist von außen erreichbar. Prüfe jeden Eintrag:

| Port | Dienst | Sollte öffentlich sein? |
|------|--------|------------------------|
| 22/2222 | SSH | Ja (dein Zugang) |
| 80/443 | Webserver (Caddy/nginx) | Ja (Webseiten) |
| 631 | CUPS (Druckdienst) | **NEIN** – entfernen |
| 3000-9999 | App-Backends | Meistens **NEIN** |

### 7.2 — CUPS entfernen (Druckdienst)

CUPS hat auf einem Server nichts zu suchen:

```bash
# Prüfen ob installiert
snap list | grep cups

# Entfernen
sudo snap remove cups

# Oder als normaler Dienst:
sudo systemctl stop cups
sudo systemctl disable cups
```

### 7.3 — Dienste auf localhost beschränken

Wenn ein Dienst nur intern gebraucht wird (z.B. hinter einem Reverse Proxy wie Caddy), sollte er nur auf `127.0.0.1` hören – nicht auf `0.0.0.0`.

**Beispiel: Ein Node.js Server auf Port 3030**

In der systemd-Service Datei ändern:
```ini
# VORHER (öffentlich erreichbar):
Environment=HOST=0.0.0.0

# NACHHER (nur lokal):
Environment=HOST=127.0.0.1
```

Dann neu starten:
```bash
sudo systemctl daemon-reload
sudo systemctl restart [SERVICE_NAME]
```

### 7.4 — Prüfen ob noch unerwartete Dienste laufen

```bash
sudo ss -tlnp | grep "0.0.0.0" | grep -vE "sshd|caddy|nginx|tailscale"
```

Wenn hier noch etwas auftaucht → prüfen ob es gebraucht wird.

## Checkliste

- [ ] Alle offenen Ports geprüft
- [ ] CUPS entfernt (falls vorhanden)
- [ ] Backend-Dienste auf localhost beschränkt
- [ ] Keine unerwarteten öffentlichen Listener
