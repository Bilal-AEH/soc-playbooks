# soc-playbooks

Procédures de réponse à incident pour les scénarios courants rencontrés dans un SOC (Security Operations Center).

Chaque playbook suit la même structure : déclencheur, investigation, containment, remédiation, documentation. Les indicateurs de compromission (IOC) associés sont référencés dans `ioc/`.

## Organisation

```
incidents/   un playbook par type d'incident
ioc/         patterns de détection et indicateurs associés à chaque scénario
```

## Incidents couverts

| Fichier | Scénario |
|---|---|
| `bruteforce_ssh.md` | Tentatives de connexion SSH répétées depuis une IP externe |
| `port_scan.md` | Scan de ports entrant détecté sur le périmètre |
| `exfiltration_dns.md` | Exfiltration de données suspectée via requêtes DNS |
| `compte_compromis.md` | Compte utilisateur compromis — activité anormale détectée |
| `ransomware.md` | Comportement ransomware détecté sur un poste |
