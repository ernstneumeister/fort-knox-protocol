# Schritt 6: Tailscale VPN (Optional)

🔴 **Hohes Risiko** – nach diesem Schritt ist SSH nur noch über VPN erreichbar.

**Empfehlung:** Überspringen, wenn du Tailscale nicht bereits nutzt. Dein Server ist mit den Schritten 1-5 bereits gut geschützt.

---

## Was passiert hier?

Tailscale ist ein VPN-Netzwerk – wie ein privater Tunnel zwischen deinen Geräten. Wenn wir SSH auf Tailscale beschränken, ist dein Server komplett unsichtbar im Internet. Nur Geräte in deinem Tailscale-Netzwerk können sich verbinden.

## Wann sinnvoll?

- Du nutzt Tailscale bereits auf deinen Geräten
- Du willst maximale Sicherheit
- Du bist okay damit, dass SSH nur mit aktivem Tailscale funktioniert

## Wann NICHT?

- Du nutzt kein Tailscale
- Du willst dich auch von fremden Geräten verbinden können
- Du willst es einfach halten

## Kurzanleitung (nur wenn du es wirklich willst)

```bash
# Prüfen ob Tailscale läuft
tailscale status

# SSH via Tailscale testen (VORHER!)
ssh admin@[TAILSCALE_IP] -p 2222

# Erst wenn das funktioniert:
sudo ufw delete allow 2222/tcp
sudo ufw allow in on tailscale0 to any port 2222 proto tcp comment "SSH nur via Tailscale"
```

## Rollback (falls ausgesperrt)

1. Hetzner Console öffnen (im Browser)
2. Als root einloggen
3. `sudo ufw allow 2222/tcp` ausführen

## Checkliste

- [ ] Tailscale läuft auf Server UND deinem Gerät
- [ ] SSH via Tailscale-IP getestet
- [ ] Öffentlicher SSH-Port entfernt
- [ ] SSH nur auf tailscale0 Interface erlaubt
