# Schritt 9: Swap einrichten (Notfall-Speicher)

🟢 **Kein Risiko**

---

## Was passiert hier?

Dein Server hat Arbeitsspeicher (RAM) – das ist wie der Schreibtisch, auf dem alles liegt was gerade läuft. Wenn der voll wird, hat Linux ohne Swap keinen Plan B und killt einfach das größte Programm. Bei einem KI-Assistenten heißt das: Sessions, Nachrichten und Arbeit gehen verloren.

Swap ist ein Notfall-Puffer auf der Festplatte. Langsamer als RAM, aber besser als ein Absturz.

## So erklärst du es deinem User

> "Dein Server hat 8 GB Arbeitsspeicher – wie ein Schreibtisch. Wenn der voll wird, schmeißt das System gerade einfach Sachen runter. Ich richte jetzt eine 'Schublade' ein (4 GB auf der Festplatte). Die wird nur im Notfall genutzt, aber verhindert Abstürze."

## Anleitung

### 9.1 — Prüfen ob Swap bereits existiert

```bash
swapon --show
free -h
```

Wenn Swap bereits vorhanden und groß genug ist (4GB+ bei 8GB RAM) → diesen Schritt überspringen.

### 9.2 — Swap erstellen

```bash
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Beim nächsten Neustart automatisch laden
echo "/swapfile none swap sw 0 0" | sudo tee -a /etc/fstab
```

**Faustregeln:**
- 4-8 GB RAM → 4 GB Swap
- 16+ GB RAM → 4-8 GB Swap

### 9.3 — Swappiness prüfen

Wurde bereits in Schritt 8 auf 10 gesetzt. Das heißt: Swap wird erst genutzt wenn der RAM fast voll ist.

```bash
sysctl vm.swappiness
# Sollte 10 zeigen
```

## Prüfen

```bash
swapon --show           # Sollte /swapfile mit 4G zeigen
free -h                 # Sollte Swap-Zeile zeigen
grep swap /etc/fstab    # Sollte /swapfile Eintrag zeigen
```

## Checkliste

- [ ] Swap vorhanden (4GB+)
- [ ] In /etc/fstab eingetragen (überlebt Neustart)
- [ ] Swappiness auf 10
