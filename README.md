# 🏰 Fort Knox Protocol

**Das Sicherheitsprotokoll für deinen KI-Assistenten.**

Dein KI-Assistent läuft auf einem Server – aber ist der auch sicher? Das Fort Knox Protocol ist eine Schritt-für-Schritt Anleitung, die dein Assistent selbst abarbeitet. Du gibst den Auftrag, er sichert den Server.

---

## Für wen ist das?

Du bist **kein Entwickler** – und musst auch keiner sein. Dein KI-Assistent arbeitet dieses Protokoll ab. Du gibst ihm den Auftrag und sagst "Go".

**So geht's:**
1. Gib deinem Assistenten den Link zu diesem Repository
2. Sag: *"Arbeite das Fort Knox Protocol durch. Erkläre mir jeden Schritt bevor du ihn ausführst und warte auf mein OK."*
3. Dein Assistent erklärt dir jeden Schritt in einfacher Sprache und führt ihn nach deinem OK aus
4. Nach ca. 30-45 Minuten ist dein Server abgesichert

---

## Was wird gemacht?

| Schritt | Was passiert | Risiko |
|---------|-------------|--------|
| 0 | Backup erstellen | 🟢 Kein Risiko |
| 1 | System-Updates | 🟢 Kein Risiko |
| 3 | SSH absichern (Port + Key-Only) | 🟡 Mittel – SSH-Zugang ändert sich |
| 4 | Firewall einrichten | 🟡 Mittel |
| 5 | Brute-Force-Schutz | 🟢 Kein Risiko |
| 6 | VPN-Lockdown (optional) | 🔴 Hoch – nur für Fortgeschrittene |
| 7 | Dienste aufräumen | 🟢 Gering |
| 8 | Kernel absichern | 🟢 Gering |
| 9 | Notfall-Speicher | 🟢 Kein Risiko |
| 10 | RAM-Limits setzen | 🟡 Mittel – Neustart nötig |
| 11 | Monitoring | 🟢 Kein Risiko |

**Danach:** Abschlusstest (Audit) bestätigt, dass alles korrekt umgesetzt wurde.

---

## Wichtige Regeln

**Für den Assistenten:**
- ⚠️ Jeden Schritt **erklären bevor ausführen** – in einfacher Sprache
- ⚠️ Bei SSH-Änderungen (Schritt 3): **Alte Verbindung offen lassen**, neue testen
- ⚠️ Bei Schritt 10: **User warnen** dass der Assistent kurz offline geht
- ⚠️ Immer erst **Backup/Snapshot** erstellen (Schritt 0)

**Für dich:**
- Du brauchst SSH-Zugang zu deinem Server (z.B. über VS Code)
- Erstelle vorher einen Snapshot bei deinem Hosting-Anbieter
- Plane ca. 30-45 Minuten ein

---

## Protokoll-Dateien

```
core/
  00-vorbereitung.md       ← Backup, Zugang prüfen
  01-system-updates.md     ← Updates installieren
  03-ssh-absichern.md      ← SSH-Port ändern, Key-Only Auth
  04-firewall.md           ← Nur nötige Ports öffnen
  05-brute-force-schutz.md ← Angreifer automatisch sperren
  06-tailscale.md          ← VPN (optional, für Fortgeschrittene)
  07-dienste-aufraeumen.md ← Unnötige Dienste stoppen
  08-kernel.md             ← Betriebssystem absichern
  09-swap.md               ← Notfall-Speicher einrichten
  10-ram-limits.md         ← Speicher-Grenzen setzen
  11-monitoring.md         ← Überwachung einrichten

audit/
  AUDIT.md                 ← Abschlusstest
```

---


---

## Lizenz

CC BY-NC 4.0 – Frei nutzbar für private Zwecke. Kommerzielle Nutzung nicht gestattet.

---

*Ein Projekt von [Ernst Neumeister](https://www.ernstneumeister.de) / Neumeister Consulting GmbH*
