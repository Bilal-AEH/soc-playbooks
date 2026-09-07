# Playbook — Bruteforce SSH

## Déclencheur

Alerte générée sur : plus de 10 tentatives de connexion SSH échouées depuis la même IP source en moins de 60 secondes.

Sources possibles : SIEM, fail2ban, logs `/var/log/auth.log`.

## Investigation

**1. Qualifier l'alerte**
- L'IP source est-elle connue ? (infrastructure interne, partenaire, services légitimes)
- Les tentatives ciblent-elles un ou plusieurs comptes ?
- Y a-t-il eu une connexion réussie après les échecs ?

**2. Collecter les indicateurs**
```
IP source
Timestamp des premières tentatives
Comptes ciblés (root, admin, noms génériques ?)
Protocole et port (SSH/22 ou port non standard ?)
User-Agent si disponible
```

**3. Vérifier si l'IP est connue**
- Consulter les listes de réputation (AbuseIPDB, VirusTotal)
- Vérifier si l'IP est dans les logs historiques
- Vérifier la géolocalisation

**4. Chercher une connexion réussie**
```bash
grep "Accepted" /var/log/auth.log | grep <IP_source>
```
Si une connexion a réussi → élever la priorité, déclencher le playbook `compte_compromis.md`.

## Containment

**Si aucune connexion réussie :**
- Bloquer l'IP source au niveau firewall
- Ajouter l'IP à la liste de blocage fail2ban si en place
- Vérifier les autres équipements exposés sur la même IP source

**Si connexion réussie :**
→ Voir `compte_compromis.md`

## Remédiation

- Vérifier que SSH n'est pas exposé inutilement (utilité d'un bastion ?)
- S'assurer que l'authentification par mot de passe est désactivée (clé SSH uniquement)
- Vérifier la configuration du compte root : désactiver la connexion directe root si active
- Confirmer que fail2ban ou équivalent est actif et configuré

## Documentation

Consigner dans le ticket d'incident :
- IP source, timestamps, comptes ciblés
- Connexion réussie : oui / non
- Actions prises (blocage IP, escalade)
- IOC à ajouter à la liste de surveillance

→ `ioc/bruteforce_ssh.md`
