# Schritt 11: Monitoring

🟢 **Kein Risiko**

---

## Was passiert hier?

Absicherung ist kein einmaliger Vorgang – der Server muss dauerhaft überwacht werden. Wir richten einen automatischen Check ein, der alle 15 Minuten den Speicherverbrauch prüft und bei Problemen eine Warnung ins System-Log schreibt. Außerdem begrenzen wir die Log-Dateien, damit sie nicht die Festplatte vollschreiben.

## So erklärst du es deinem User

> "Ich richte jetzt eine automatische Überwachung ein. Alle 15 Minuten prüft der Server seinen Speicher. Außerdem begrenze ich die Log-Dateien – die können sonst mehrere Gigabyte groß werden."

## Anleitung

### 11.1 — Speicher-Check Script

```bash
cat > /usr/local/bin/check-memory.sh << 'SCRIPT'
#!/bin/bash
export PATH="/usr/sbin:/usr/bin:/sbin:/bin:$PATH"

RAM_TOTAL=$(free -m | awk '/^Mem:/{print $2}')
RAM_USED=$(free -m | awk '/^Mem:/{print $3}')
RAM_PCT=$((RAM_USED * 100 / RAM_TOTAL))

SWAP_TOTAL=$(free -m | awk '/^Swap:/{print $2}')
SWAP_USED=$(free -m | awk '/^Swap:/{print $3}')
if [ "$SWAP_TOTAL" -gt 0 ]; then
    SWAP_PCT=$((SWAP_USED * 100 / SWAP_TOTAL))
else
    SWAP_PCT=0
fi

ALERT=""
if [ "$RAM_PCT" -gt 80 ]; then
    ALERT="RAM bei ${RAM_PCT}% (${RAM_USED}/${RAM_TOTAL}MB)"
fi
if [ "$SWAP_PCT" -gt 50 ]; then
    ALERT="${ALERT} SWAP bei ${SWAP_PCT}% (${SWAP_USED}/${SWAP_TOTAL}MB)"
fi

if [ -n "$ALERT" ]; then
    echo "WARNUNG: $ALERT"
    exit 1
else
    echo "OK: RAM ${RAM_PCT}% SWAP ${SWAP_PCT}%"
    exit 0
fi
SCRIPT
chmod +x /usr/local/bin/check-memory.sh
```

### 11.2 — Automatischer Check alle 15 Minuten

```bash
cat > /etc/cron.d/memory-monitor << 'EOF'
*/15 * * * * root /usr/local/bin/check-memory.sh > /dev/null 2>&1 || logger -t memory-alert "$(/usr/local/bin/check-memory.sh 2>&1)"
EOF
```

Warnungen landen in `/var/log/syslog`.

### 11.3 — Log-Größe begrenzen

```bash
sudo mkdir -p /etc/systemd/journald.conf.d
cat > /etc/systemd/journald.conf.d/size-limit.conf << 'EOF'
[Journal]
SystemMaxUse=500M
RuntimeMaxUse=100M
EOF

sudo systemctl restart systemd-journald
```

### 11.4 — Festplatte prüfen

```bash
df -h /
```

Wenn über 80% belegt → aufräumen (siehe Schritt 0).

## Prüfen

```bash
/usr/local/bin/check-memory.sh      # Sollte "OK" zeigen
cat /etc/cron.d/memory-monitor      # Sollte den Cron-Job zeigen
journalctl --disk-usage             # Sollte unter 500MB sein
df -h / | awk 'NR==2{print $5}'    # Sollte unter 80% sein
```

## Checkliste

- [ ] Memory-Check Script erstellt
- [ ] Cron-Job läuft alle 15 Minuten
- [ ] Journal-Logs auf 500MB begrenzt
- [ ] Festplatten-Nutzung geprüft
