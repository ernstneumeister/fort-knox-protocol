# Changelog

Alle Änderungen am Fort Knox Protocol werden hier dokumentiert.

---

## 2026-02-25 — v1.1

**SSH-Schritt überarbeitet: Konsistent mit neuem Setup-Prompt**

- Setup-Prompt installiert OpenClaw jetzt als `admin` User (nicht root)
- Workspace liegt unter `/home/admin/`, nicht `/root/`
- Daher kann `PermitRootLogin no` sicher verwendet werden
- VS Code verbindet als `admin` → sieht alles
- Neuer Abschnitt 3.8: Erklärt warum das funktioniert + Warnung für Alt-Installationen
- Audit: Erwartete Werte zurück auf `PermitRootLogin no` + `AllowUsers admin`

**Hintergrund:** Der alte Setup-Prompt installierte OpenClaw als root. Das führte zu Konflikten wenn Fort Knox den Root-Login deaktivierte — VS Code konnte den Workspace nicht mehr sehen. Jetzt wird OpenClaw von Anfang an als admin installiert, und alles passt zusammen.

---

## 2026-02-24 — v1.0

**Erstveröffentlichung**

- 11 Schritte zur Server-Absicherung
- Übersetzt und angepasst für nicht-technische Nutzer mit KI-Assistenten
- Abschlusstest (Audit) mit Copy-Paste Prüfblock
- Lizenz: CC BY-NC 4.0
