# Playbook — Scan de ports entrant

## Déclencheur

Alerte générée sur : plus de 15 ports différents contactés depuis la même IP source en moins de 30 secondes, ou détection d'une signature de scan connue (nmap, masscan).

Sources possibles : IDS/IPS (Snort, Suricata), firewall logs, SIEM.

## Investigation

**1. Qualifier le scan**
- Scan horizontal (une IP, beaucoup de ports) ou vertical (beaucoup d'IPs, un port) ?
- Les ports ciblés sont-ils sensibles ? (22/SSH, 3389/RDP, 445/SMB, 1433/SQL, 80/443/HTTP)
- L'IP source est-elle connue ? (scanner de vulnérabilités interne, partenaire de sécurité, prestataire)

**2. Évaluer l'exposition**
- Quels services répondent réellement aux ports scannés ?
- Y a-t-il des services exposés qui ne devraient pas l'être ?
- Des banners ou réponses d'erreur ont-ils été retournés, révélant des versions logicielles ?

**3. Chercher une activité suspecte après le scan**
Un scan est souvent une phase de reconnaissance précédant une attaque. Vérifier dans les logs des 24 heures suivantes :
- Tentatives de connexion sur les ports qui ont répondu
- Exploits connus pour les services détectés
- Connexions réussies depuis la même IP ou un sous-réseau proche

## Containment

**Scan de reconnaissance simple (aucune connexion suspecte après) :**
- Bloquer l'IP source au firewall
- Logger l'IP pour surveillance dans les 48h suivantes

**Scan suivi d'une tentative d'exploitation :**
- Blocage immédiat IP source
- Isoler les systèmes ciblés si une exploitation a pu réussir
- Escalader selon la politique interne

## Remédiation

- Revoir les règles de firewall : aucun port non nécessaire ne doit être exposé
- S'assurer que les services exposés sont à jour (pas de versions vulnérables)
- Désactiver les banners qui révèlent les versions (Apache, OpenSSH, etc.)
- Envisager un IPS en mode bloquant pour les signatures de scan connues

## Documentation

Consigner dans le ticket d'incident :
- IP source, timestamps, type de scan identifié
- Ports ciblés et services qui ont répondu
- Activité suspecte après le scan : oui / non
- Actions prises

→ `ioc/port_scan.md`
