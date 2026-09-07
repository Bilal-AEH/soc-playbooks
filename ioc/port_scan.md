# IOC — Scan de ports

## Patterns de détection

**Seuil d'alerte recommandé**
- 15 ports distincts contactés depuis la même IP source en 30 secondes
- Signature nmap détectée (TTL, TCP flags, probe sequence)
- Connexions SYN sans suite (half-open scan / SYN scan)

**Signatures Suricata/Snort associées**
```
ET SCAN Nmap Scripting Engine User-Agent Detected
ET SCAN Potential SSH Scan
ET SCAN Masscan User-Agent Detected
ET SCAN NMAP OS Detection Probe
```

## Ports sensibles à surveiller

| Port | Service | Risque si exposé |
|---|---|---|
| 22 | SSH | Bruteforce, accès non autorisé |
| 23 | Telnet | Protocole non chiffré, ne devrait plus être exposé |
| 80/443 | HTTP/HTTPS | Exploitation web |
| 445 | SMB | EternalBlue, ransomware |
| 3389 | RDP | Bruteforce, BlueKeep |
| 1433 | MSSQL | Exfiltration, injection SQL |
| 3306 | MySQL | Accès direct à la base |
| 5900 | VNC | Accès bureau à distance non sécurisé |

## Distinction scan automatisé / ciblé

**Scan automatisé (masse)** — IP souvent référencée sur Shodan/Censys, scan de toute la plage 0-65535 ou seulement les ports les plus communs, aucune exploitation après.

**Scan ciblé** — ports spécifiques à l'infrastructure visible, suivi d'une tentative d'exploitation, IP nouvelle sans historique de scan de masse.

## Commandes utiles

```bash
# Voir les connexions actives et ports en écoute
ss -tlnp

# Vérifier ce qui répond sur les ports exposés
nmap -sV localhost

# Logs firewall (exemple iptables)
grep "DPT=22\|DPT=3389\|DPT=445" /var/log/syslog | tail -50
```
