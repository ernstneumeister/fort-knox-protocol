# Schritt 10: RAM-Limits setzen

🟡 **Mittleres Risiko** – der Assistent muss neu gestartet werden.

---

## Was passiert hier?

Ohne RAM-Limit könnte dein KI-Assistent bei einem Speicherleck den ganzen Server lahmlegen. Wir setzen ein Maximum: z.B. "Du darfst maximal 6 GB von 8 GB nutzen." Der Rest bleibt für das System.

## So erklärst du es deinem User

> "Ich setze jetzt eine Obergrenze für meinen Speicherverbrauch. Damit kann ich nicht versehentlich den ganzen Server lahmlegen. Dafür muss ich mich kurz neu starten – bin in 30-60 Sekunden zurück."

> ⚠️ "Falls ich nicht zurückkomme, kannst du mich über VS Code/SSH mit diesem Befehl starten: `sudo systemctl restart openclaw-[NAME].service`"

## Anleitung

### 10.1 — Service-Datei finden

```bash
# OpenClaw System-Service:
sudo systemctl cat openclaw-*

# Oder manuell suchen:
ls /etc/systemd/system/openclaw-*
```

### 10.2 — RAM-Limits hinzufügen

In der `[Service]` Sektion der Service-Datei hinzufügen:

```ini
# RAM: Hartes Limit bei ~80% des Gesamt-RAM
MemoryMax=6G
# RAM: Weiches Limit (Warnung bevor hartes Limit greift)
MemoryHigh=5G
# Keine Rechte-Eskalation erlauben
NoNewPrivileges=yes
```

**Faustregeln:**
- 4 GB RAM → MemoryMax=3G, MemoryHigh=2.5G
- 8 GB RAM → MemoryMax=6G, MemoryHigh=5G
- 16 GB RAM → MemoryMax=12G, MemoryHigh=10G

### 10.3 — Neustart

```bash
sudo systemctl daemon-reload
sudo systemctl restart openclaw-[NAME].service
```

⚠️ **Der Assistent ist jetzt kurz offline.** Warte 30-60 Sekunden.

### 10.4 — Prüfen ob er wieder läuft

```bash
sudo systemctl is-active openclaw-[NAME].service
# Sollte "active" zeigen

sudo systemctl show openclaw-[NAME].service | grep -E "MemoryMax=|MemoryHigh="
# Sollte die gesetzten Werte zeigen (in Bytes)
```

## Checkliste

- [ ] User über Neustart informiert
- [ ] MemoryMax und MemoryHigh gesetzt
- [ ] NoNewPrivileges=yes
- [ ] Service neu gestartet
- [ ] Service läuft wieder
- [ ] RAM-Limits bestätigt
