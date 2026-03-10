# Schritt 7: Dienste aufräumen

🟢 **Automatisch** – prüfen und aufräumen.

---

## Kurz-Info für den User

> "Unnötige Dienste abschalten – weniger Angriffsfläche."

## Anleitung

### 7.1 — Alle offenen Dienste finden

```bash
ss -tlnp | grep LISTEN
```

Alles was auf `0.0.0.0` hört, ist von außen erreichbar.

### 7.2 — CUPS entfernen (Druckdienst)

CUPS hat auf einem Server nichts zu suchen:

```bash
snap list | grep cups && snap remove cups
systemctl is-active cups 2>/dev/null && systemctl stop cups && systemctl disable cups
```

### 7.3 — Dienste auf localhost beschränken

Wenn ein Dienst nur intern gebraucht wird (z.B. hinter Caddy), sollte er nur auf `127.0.0.1` hören – nicht auf `0.0.0.0`.

### 7.4 — Prüfen ob noch unerwartete Dienste laufen

```bash
ss -tlnp | grep "0.0.0.0" | grep -vE "sshd|caddy|nginx|tailscale"
```

## Checkliste

- [ ] Alle offenen Ports geprüft
- [ ] CUPS entfernt (falls vorhanden)
- [ ] Backend-Dienste auf localhost beschränkt
- [ ] Keine unerwarteten öffentlichen Listener
