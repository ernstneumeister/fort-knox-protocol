# Abschlusstest (Audit)

Nachdem alle Schritte abgearbeitet sind, prüfe ob wirklich alles korrekt umgesetzt wurde.

**Wichtig:** Prüfe die LAUFENDEN Werte, nicht nur Config-Dateien. Config-Dateien können falsch sein – was zählt ist, was tatsächlich aktiv ist.

---

## Alle Checks auf einmal

Kopiere diesen Block und führe ihn aus:

```bash
export PATH="/usr/sbin:/usr/bin:/sbin:/bin:$PATH"

echo "=============================="
echo "  FORT KNOX PROTOCOL — AUDIT"
echo "=============================="

echo ""
echo "--- SSH ---"
ss -tlnp | grep sshd
grep "^PermitRootLogin" /etc/ssh/sshd_config
grep "^PasswordAuthentication" /etc/ssh/sshd_config
grep "^AllowUsers" /etc/ssh/sshd_config

echo ""
echo "--- Firewall ---"
ufw status | grep -v "(v6)"

echo ""
echo "--- Fail2ban ---"
systemctl is-active fail2ban
fail2ban-client status

echo ""
echo "--- Öffentliche Dienste (sollte leer sein außer SSH/Webserver) ---"
ss -tlnp | grep "0.0.0.0" | grep -vE "127\.0\.0|sshd|caddy|nginx|tailscale|systemd-resolve"

echo ""
echo "--- Kernel ---"
echo "accept_redirects: $(sysctl -n net.ipv4.conf.all.accept_redirects) (soll: 0)"
echo "syncookies: $(sysctl -n net.ipv4.tcp_syncookies) (soll: 1)"
echo "ASLR: $(sysctl -n kernel.randomize_va_space) (soll: 2)"
echo "dmesg_restrict: $(sysctl -n kernel.dmesg_restrict) (soll: 1)"
echo "swappiness: $(sysctl -n vm.swappiness) (soll: 10)"
echo "ipv6_disabled: $(sysctl -n net.ipv6.conf.all.disable_ipv6) (soll: 1)"
echo "hidepid: $(mount | grep hidepid)"

echo ""
echo "--- Swap ---"
swapon --show
free -h | grep Swap

echo ""
echo "--- Monitoring ---"
/usr/local/bin/check-memory.sh
cat /etc/cron.d/memory-monitor 2>/dev/null | head -2

echo ""
echo "--- Auto-Updates ---"
systemctl is-active unattended-upgrades

echo ""
echo "--- Festplatte ---"
df -h / | awk 'NR==2{print $3" belegt / "$2" gesamt ("$5")"}'

echo ""
echo "=============================="
echo "  AUDIT ABGESCHLOSSEN"
echo "=============================="
```

## Erwartete Ergebnisse

| Check | Erwartet |
|-------|----------|
| SSH Port | 2222 (oder dein gewählter Port, NICHT 22) |
| PermitRootLogin | prohibit-password |
| PasswordAuthentication | no |
| AllowUsers | admin root |
| Firewall | active, nur SSH + HTTP/HTTPS |
| Fail2ban | active, 2 Jails (sshd + recidive) |
| Öffentliche Dienste | Leer (außer SSH + Webserver) |
| Kernel-Werte | Alle wie angegeben |
| Swap | 4GB+ vorhanden |
| Memory-Check | OK |
| Auto-Updates | active |
| Festplatte | unter 80% |

## Was tun bei Abweichungen?

- Gehe zurück zum entsprechenden Schritt und arbeite ihn erneut durch
- Bei kritischen Abweichungen (SSH, Firewall): Sofort beheben
- Bei kleinen Abweichungen (Log-Größe, Swap): Beim nächsten Wartungsfenster

## Regelmäßig wiederholen

Empfehlung: Diesen Audit **einmal im Monat** laufen lassen, oder nach:
- Installation neuer Software
- System-Updates
- Sicherheitsvorfällen
