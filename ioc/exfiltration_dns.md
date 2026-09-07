# IOC — Exfiltration DNS

## Patterns de détection

**Volume anormal**
- Plus de 500 requêtes DNS par heure depuis un même poste (adapter selon l'environnement)
- Taux de requêtes NXDOMAIN élevé (domaines qui n'existent pas) — signe de génération algorithmique

**Structure suspecte des sous-domaines**
- Longueur de sous-domaine > 50 caractères
- Caractères encodés (base64 : a-z, A-Z, 0-9, +, /) ou hexadécimal
- Sous-domaines qui changent à chaque requête pour le même domaine de base

**Domaines de destination suspects**
- Enregistrés il y a moins de 30 jours
- Pas de présence web (pas de réponse HTTP sur le domaine apex)
- Peu ou pas de résolutions historiques
- Registrar peu connu ou domaine en .xyz, .top, .click, .info

## Exemples de requêtes d'exfiltration

```
# Base64 dans le sous-domaine
dGhpcyBpcyBhIHRlc3Q.evil-domain.com

# Données fragmentées en plusieurs requêtes
chunk1-aGVsbG8.evil-domain.com
chunk2-d29ybGQ.evil-domain.com

# DGA (Domain Generation Algorithm) — noms aléatoires
xk3mq9pv.attacker.com
r7nh2wqz.attacker.com
```

## Outils d'analyse DNS

```bash
# Capturer les requêtes DNS sur l'interface réseau
tcpdump -i eth0 port 53 -w dns_capture.pcap

# Analyser les requêtes dans les logs bind/unbound
grep "query" /var/log/named/queries.log | awk '{print $6}' | sort | uniq -c | sort -rn | head -30

# Vérifier la date d'enregistrement d'un domaine
whois suspicious-domain.com | grep "Creation Date"
```

## Sources de réputation

- https://www.virustotal.com — analyser un domaine
- https://urlhaus.abuse.ch — domaines malveillants connus
- https://www.domaintools.com — historique et profil d'un domaine
- https://dns.google — resolver Google pour vérifier une réponse DNS

## Indicateurs de compromission sur le poste source

- Processus inconnu initiant des requêtes DNS répétitives
- Connexion réseau depuis un processus sans icône ou avec nom générique (svchost, rundll32 sur Windows)
- Fichiers créés récemment dans les répertoires temporaires (`%TEMP%`, `/tmp`)
- Tâche planifiée créée récemment (cron, Task Scheduler)
