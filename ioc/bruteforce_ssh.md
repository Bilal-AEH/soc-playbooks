# IOC — Bruteforce SSH

## Patterns de détection

**Seuil d'alerte recommandé**
- 10 échecs d'authentification SSH depuis la même IP en 60 secondes
- 50 échecs depuis la même IP en 10 minutes

**Entrées de log à surveiller**
```
Failed password for <user> from <IP> port <port> ssh2
Invalid user <user> from <IP>
Connection closed by authenticating user <user> <IP> [preauth]
```

**Comptes généralement ciblés en premier**
```
root, admin, administrator, user, ubuntu, pi, oracle, postgres, test, guest
```
La présence de ces noms dans les logs d'échec SSH est un indicateur de scan automatisé.

## Indicateurs de compromission (si connexion réussie)

- Connexion depuis une IP non vue précédemment sur ce compte
- Connexion à une heure inhabituelle
- Processus lancés depuis la session SSH : téléchargement de fichiers, création de tâches cron, modification de `~/.ssh/authorized_keys`
- Ajout d'une clé SSH inconnue dans `authorized_keys`

## Sources de réputation

- https://www.abuseipdb.com — vérifier une IP source
- https://viz.greynoise.io — classifier le scan (mass scanner connu ou ciblé)
- https://otx.alienvault.com — IOC communautaires

## Commandes utiles

```bash
# Voir les tentatives échouées récentes
grep "Failed password" /var/log/auth.log | tail -50

# Voir les connexions réussies
grep "Accepted" /var/log/auth.log | tail -20

# Top IPs qui tentent de se connecter
grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn | head -20
```
