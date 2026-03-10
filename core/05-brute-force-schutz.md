# Schritt 5: Brute-Force-Schutz (fail2ban)

🟢 **Automatisch** – einrichten und kurz berichten.

---

## Kurz-Info für den User

> "fail2ban sperrt automatisch jeden, der zu oft das falsche Passwort eingibt. Wiederholungstäter werden für eine Woche gesperrt."

## Anleitung

### 5.1 — Prüfen ob fail2ban läuft

```bash
systemctl is-active fail2ban
```

Falls nicht aktiv:
```bash
systemctl enable --now fail2ban
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

⚠️ `port = 2222` muss dem SSH-Port aus Schritt 3 entsprechen.

### 5.3 — Neustart

```bash
systemctl restart fail2ban
sleep 2
fail2ban-client status
```

## Prüfen

```bash
fail2ban-client status
fail2ban-client status sshd
```

## Checkliste

- [ ] fail2ban läuft
- [ ] SSH-Jail auf richtigem Port
- [ ] Recidive-Jail aktiv
