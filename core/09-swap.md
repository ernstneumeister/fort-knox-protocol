# Schritt 9: Swap einrichten (Notfall-Speicher)

🟢 **Automatisch** – kein Risiko.

---

## Kurz-Info für den User

> "Notfall-Speicher einrichten, damit der Server nicht abstürzt wenn der Arbeitsspeicher voll wird."

## Anleitung

### 9.1 — Prüfen ob Swap bereits existiert

```bash
swapon --show
free -h
```

Wenn Swap bereits vorhanden und groß genug (4GB+ bei 8GB RAM) → überspringen.

### 9.2 — Swap erstellen

```bash
fallocate -l 4G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo "/swapfile none swap sw 0 0" | tee -a /etc/fstab
```

**Faustregeln:**
- 4-8 GB RAM → 4 GB Swap
- 16+ GB RAM → 4-8 GB Swap

### 9.3 — Swappiness prüfen

Wurde in Schritt 8 auf 10 gesetzt:
```bash
sysctl vm.swappiness  # Sollte 10 zeigen
```

## Prüfen

```bash
swapon --show
free -h
grep swap /etc/fstab
```

## Checkliste

- [ ] Swap vorhanden (4GB+)
- [ ] In /etc/fstab eingetragen
- [ ] Swappiness auf 10
