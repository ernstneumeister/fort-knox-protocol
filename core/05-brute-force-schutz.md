# Schritt 5: Brute-Force-Schutz (fail2ban)

🟢 **Kein Risiko**

---

## Was passiert hier?

Bots versuchen sich ständig auf deinem Server einzuloggen – tausende Male pro Tag. fail2ban beobachtet die Login-Versuche und sperrt automatisch IP-Adressen, die zu oft scheitern. Wiederholungstäter werden für eine ganze Woche gesperrt.

## So erklärst du es deinem User

> "Stell dir einen Türsteher vor, der sich merkt wer schon dreimal den falschen Schlüssel probiert hat – und den dann für eine Stunde wegschickt. Kommt derselbe nochmal, wird er für eine Woche gesperrt."

## Anleitung

### 5.1 — Prüfen ob fail2ban läuft

```bash
sudo systemctl is-active fail2ban
sudo systemctl is-enabled fail2ban
```

Beides sollte "active" / "enabled" zeigen. Falls nicht:
```bash
sudo systemctl enable --now fail2ban
```

### 5.2 — Konfiguration erstellen

```bash
cat > /etc/fail2ban/jail.local << 'EOF'
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3

[sshd]
enabled = true
port = 2222
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
findtime = 600

[recidive]
enabled = true
logpath = /var/log/fail2ban.log
banaction = %(banaction_allports)s
bantime = 604800
findtime = 86400
maxretry = 5
EOF
```

⚠️ **Port anpassen!** `port = 2222` muss dem SSH-Port aus Schritt 3 entsprechen.

**Was bedeuten die Zahlen:**
- `maxretry = 3` → Nach 3 fehlgeschlagenen Versuchen wird gesperrt
- `bantime = 3600` → Für 1 Stunde (3600 Sekunden)
- `findtime = 600` → Die 3 Versuche müssen innerhalb von 10 Minuten passieren
- **recidive:** Wer 5x gesperrt wurde → 1 Woche Ban (604800 Sekunden)

### 5.3 — Neustart

```bash
sudo systemctl restart fail2ban
sleep 2
sudo fail2ban-client status
```

## Prüfen

```bash
sudo fail2ban-client status
# Sollte zeigen: Jail list: recidive, sshd

sudo fail2ban-client status sshd
# Zeigt Details + bereits gesperrte IPs (oft schon welche da!)
```

## Checkliste

- [ ] fail2ban läuft und ist aktiviert
- [ ] SSH-Jail auf dem richtigen Port
- [ ] Recidive-Jail aktiv (Wiederholungstäter = 1 Woche)
