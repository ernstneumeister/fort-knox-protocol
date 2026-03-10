# Schritt 11: Monitoring

🟢 **Automatisch** – kein Risiko.

---

## Kurz-Info für den User

> "Automatische Überwachung einrichten: Speicher-Check alle 15 Minuten + Log-Dateien begrenzen."

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

### 11.3 — Log-Größe begrenzen

```bash
mkdir -p /etc/systemd/journald.conf.d
cat > /etc/systemd/journald.conf.d/size-limit.conf << 'EOF'
[Journal]
SystemMaxUse=500M
RuntimeMaxUse=100M
EOF

systemctl restart systemd-journald
```

### 11.4 — Festplatte prüfen

```bash
df -h /
```

## Prüfen

```bash
/usr/local/bin/check-memory.sh
cat /etc/cron.d/memory-monitor
journalctl --disk-usage
df -h / | awk 'NR==2{print $5}'
```

## Checkliste

- [ ] Memory-Check Script erstellt
- [ ] Cron-Job läuft alle 15 Minuten
- [ ] Journal-Logs auf 500MB begrenzt
- [ ] Festplatten-Nutzung geprüft
