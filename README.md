# 🏰 Fort Knox Protocol

**Das Sicherheitsprotokoll für deinen KI-Assistenten.**

Dein KI-Assistent läuft auf einem Server – aber ist der auch sicher? Das Fort Knox Protocol ist eine Schritt-für-Schritt Anleitung, die dein Assistent selbst abarbeitet. Du gibst den Auftrag, er sichert den Server.

---

## Für wen ist das?

Du bist **kein Entwickler** – und musst auch keiner sein. Dein KI-Assistent arbeitet dieses Protokoll ab. Du gibst ihm den Auftrag und sagst "Go".

**So geht's:**
1. Gib deinem Assistenten den Link zu diesem Repository
2. Er arbeitet die Schritte automatisch durch
3. Nur bei kritischen Änderungen fragt er nach
4. Nach ca. 15-20 Minuten ist dein Server abgesichert

---

## Was wird gemacht?

| Schritt | Was passiert | Modus |
|---------|-------------|-------|
| 0 | Backup erstellen | 🟢 Automatisch |
| 1 | System-Updates | 🟢 Automatisch |
| 3 | SSH absichern (Port + Key-Only) | 🔍 Prüfen, nur bei Bedarf |
| 4 | Firewall einrichten | 🟢 Automatisch |
| 5 | Brute-Force-Schutz | 🟢 Automatisch |
| 6 | VPN-Lockdown (optional) | ⏭️ Überspringen (nur für Fortgeschrittene) |
| 7 | Dienste aufräumen | 🟢 Automatisch |
| 8 | Kernel absichern | 🟢 Automatisch |
| 9 | Notfall-Speicher | 🟢 Automatisch |
| 10 | RAM-Limits setzen | ⚠️ Fragen (Chat wird kurz unterbrochen) |
| 11 | Monitoring | 🟢 Automatisch |

**Danach:** Abschlusstest (Audit) bestätigt, dass alles korrekt umgesetzt wurde.

---

## Wichtige Regeln für den Assistenten

### Ablauf
- 🟢 **Automatisch:** Einfach machen, kurz berichten was gemacht wurde. NICHT fragen ob du es machen sollst.
- 🔍 **Prüfen:** Erst checken ob bereits erledigt. Nur wenn nötig durchführen.
- ⚠️ **Fragen:** Vorher den User informieren und auf Bestätigung warten.

### Allgemein
- Erkläre kurz in 1-2 Sätzen was jeder Schritt bewirkt – einfache Sprache, keine Fachvorträge
- Bei SSH-Änderungen (Schritt 3): **Alte Verbindung offen lassen**, neue testen
- Bei Schritt 10: **Vorher fragen!** Der Chat wird unterbrochen
- Am Ende den Audit (audit/AUDIT.md) durchlaufen lassen

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

## Lizenz

CC BY-NC 4.0 – Frei nutzbar für private Zwecke. Kommerzielle Nutzung nicht gestattet.

---

*Ein Projekt von [Ernst Neumeister](https://www.ernstneumeister.de) / Neumeister Consulting GmbH*
