# Schritt 4: Firewall

🟡 **Mittleres Risiko** – falsche Einstellung kann Dienste blockieren.

---

## Was passiert hier?

Eine Firewall ist wie ein Türsteher: Alles was reinkommt wird erstmal geblockt, außer du sagst explizit "der darf rein". Wir machen alles dicht und öffnen nur die Ports, die wir brauchen.

## So erklärst du es deinem User

> "Dein Server hat gerade viele offene Türen – manche brauchst du, viele nicht. Ich schließe jetzt alle und öffne nur die drei die nötig sind: Webseiten (80/443), SSH-Zugang (2222). Alles andere wird geblockt."

## Anleitung

### 4.1 — Aktuellen Status prüfen

```bash
sudo ufw status verbose
```

**🪤 Falle:** UFW könnte schon aktiv sein mit Regeln die du nicht kennst. Erst verstehen was da ist.

### 4.2 — Grundeinstellung setzen

```bash
sudo ufw default deny incoming    # Alles eingehende blocken
sudo ufw default allow outgoing   # Alles ausgehende erlauben
```

### 4.3 — Nötige Ports öffnen

```bash
# SSH (auf dem Port aus Schritt 3!)
sudo ufw allow 2222/tcp comment "SSH"

# Webserver (nur wenn ein Webserver läuft, z.B. Caddy, nginx)
sudo ufw allow 80/tcp comment "HTTP"
sudo ufw allow 443/tcp comment "HTTPS"
```

### 4.4 — Firewall aktivieren

```bash
sudo ufw enable
```

⚠️ **Immer NACH dem SSH-Port-Allow, NIEMALS davor!**

### 4.5 — Alte Regeln aufräumen

```bash
# Port 22 entfernen (falls noch vorhanden)
sudo ufw delete allow 22/tcp
```

## Prüfen

```bash
sudo ufw status verbose
```

Erwartet:
- Default: deny (incoming), allow (outgoing)
- Nur deine explizit erlaubten Ports (SSH, HTTP, HTTPS)
- Keine überraschenden Regeln

## Checkliste

- [ ] Default: deny incoming, allow outgoing
- [ ] SSH-Port erlaubt
- [ ] HTTP/HTTPS erlaubt (wenn Webserver läuft)
- [ ] UFW aktiviert
- [ ] Keine alten/unnötigen Regeln
