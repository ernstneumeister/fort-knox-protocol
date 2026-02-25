# Changelog

Alle Änderungen am Fort Knox Protocol werden hier dokumentiert.

---

## 2026-02-25 — v1.1

**SSH-Schritt überarbeitet: Root-Login bleibt per Key erreichbar**

- `PermitRootLogin` geändert: `no` → `prohibit-password` (Key-only)
- `AllowUsers` geändert: `admin` → `admin root`
- Neuer Abschnitt 3.8: Warnung vor VS Code Migration-Falle
- VS Code empfohlen als `root` zu verbinden (nicht `admin`)
- Admin-User bleibt als Backup-Zugang
- Audit aktualisiert: Erwartete Werte angepasst

**Warum?** OpenClaw läuft als System-Service unter root. Wenn VS Code als `admin` verbindet, sieht der Assistent den Workspace unter `/root/` nicht und versucht alles nach `/home/admin/` zu migrieren — das erzeugt eine defekte Doppel-Installation.

`prohibit-password` ist sicher: Kein Passwort-Login, kein Brute-Force. Nur SSH-Key.

---

## 2026-02-24 — v1.0

**Erstveröffentlichung**

- 11 Schritte zur Server-Absicherung
- Übersetzt und angepasst für nicht-technische Nutzer mit KI-Assistenten
- Abschlusstest (Audit) mit Copy-Paste Prüfblock
- Lizenz: CC BY-NC 4.0
