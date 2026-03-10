# Schritt 4: Firewall

🟢 **Automatisch** – einrichten und kurz berichten.

---

## Kurz-Info für den User

> "Firewall einrichten: Alle Türen schließen, nur SSH und Webserver offen lassen."

## Anleitung

### 4.1 — Aktuellen Status prüfen

```bash
ufw status verbose
```

### 4.2 — Grundeinstellung setzen

```bash
ufw default deny incoming
ufw default allow outgoing
```

### 4.3 — Nötige Ports öffnen

```bash
# SSH (Port aus Schritt 3)
ufw allow 2222/tcp comment "SSH"

# Webserver (nur wenn Caddy/nginx läuft)
ufw allow 80/tcp comment "HTTP"
ufw allow 443/tcp comment "HTTPS"
```

### 4.4 — Firewall aktivieren

```bash
ufw enable
```

### 4.5 — Alte Regeln aufräumen

```bash
ufw delete allow 22/tcp 2>/dev/null
```

## Prüfen

```bash
ufw status verbose
```

## Checkliste

- [ ] Default: deny incoming, allow outgoing
- [ ] SSH-Port erlaubt
- [ ] HTTP/HTTPS erlaubt (wenn Webserver läuft)
- [ ] UFW aktiviert
- [ ] Keine alten/unnötigen Regeln
