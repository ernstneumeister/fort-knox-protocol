# Schritt 10: RAM-Limits setzen

⚠️ **Fragen!** – Der Chat wird kurz unterbrochen.

---

## WICHTIG: Vor diesem Schritt den User fragen!

Sage dem User:
> "Ich setze jetzt eine Obergrenze für meinen Speicherverbrauch, damit ich nicht den ganzen Server lahmlegen kann. Dafür muss ich mich kurz neu starten."
>
> "**Der Chat wird dabei unterbrochen.** Wenn ich mich nicht automatisch zurückmelde, tippe einfach WEITER ein – dann fahre ich fort."
>
> "Soll ich loslegen?"

**Warte auf Bestätigung!**

## Anleitung

### 10.1 — Service-Datei finden

```bash
systemctl cat openclaw-*
ls /etc/systemd/system/openclaw-*
```

### 10.2 — RAM-Limits hinzufügen

In der `[Service]` Sektion der Service-Datei hinzufügen:

```ini
# RAM: Hartes Limit bei ~80% des Gesamt-RAM
MemoryMax=6G
# RAM: Weiches Limit
MemoryHigh=5G
# Keine Rechte-Eskalation
NoNewPrivileges=yes
```

**Faustregeln:**
- 4 GB RAM → MemoryMax=3G, MemoryHigh=2.5G
- 8 GB RAM → MemoryMax=6G, MemoryHigh=5G
- 16 GB RAM → MemoryMax=12G, MemoryHigh=10G

### 10.3 — Neustart

```bash
systemctl daemon-reload
systemctl restart openclaw-[NAME].service
```

⚠️ **Der Chat wird jetzt unterbrochen.** Warte 30-60 Sekunden.

### 10.4 — Nach dem Neustart prüfen

Wenn der User "WEITER" schreibt oder die Verbindung wiederhergestellt ist:

```bash
systemctl is-active openclaw-[NAME].service
systemctl show openclaw-[NAME].service | grep -E "MemoryMax=|MemoryHigh="
```

Berichte dem User: "Bin wieder da! RAM-Limits sind gesetzt ✅"

## Checkliste

- [ ] User informiert und bestätigt
- [ ] MemoryMax und MemoryHigh gesetzt
- [ ] NoNewPrivileges=yes
- [ ] Service neu gestartet
- [ ] Service läuft wieder
