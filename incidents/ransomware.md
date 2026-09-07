# Playbook — Comportement ransomware détecté

## Déclencheur

Alerte générée sur : chiffrement massif de fichiers, modification d'extensions en masse, création de fichiers README / DECRYPT / HOW_TO_RECOVER sur plusieurs répertoires, désactivation des sauvegardes (VSS), processus inconnu consommant des ressources élevées avec accès disque intense.

Sources possibles : EDR, antivirus, SIEM, remontée utilisateur ("mes fichiers sont illisibles").

⚠️ **Priorité maximale. Réponse immédiate.**

## Investigation

**1. Confirmer et qualifier**
- Le processus responsable est-il identifié ?
- Quels répertoires sont affectés ? L'étendue est-elle locale ou réseau ?
- Y a-t-il une note de rançon lisible ? Elle peut contenir des informations sur le groupe attaquant.
- Les sauvegardes sont-elles intactes ?

**2. Évaluer la propagation**
- Le ransomware s'est-il propagé à d'autres postes ou partages réseau ?
- Y a-t-il des signes de latéralisation (connexions SMB depuis le poste infecté) ?
- Le compte utilisateur du poste a-t-il des droits d'accès réseau étendus ?

## Containment — immédiat

1. **Isoler le poste du réseau immédiatement** (déconnecter le câble réseau ou désactiver le Wi-Fi physiquement)
   - Ne pas éteindre le poste : certains ransomwares chiffrent au reboot, et la mémoire RAM peut contenir la clé de chiffrement
2. **Identifier et isoler les partages réseau affectés**
3. **Couper l'accès du compte utilisateur compromis**
4. **Vérifier les sauvegardes** — sont-elles en ligne ou hors ligne ? Ont-elles été atteintes ?

## Investigation approfondie (après containment)

- Identifier le vecteur d'entrée : phishing, RDP exposé, vulnérabilité exploitée ?
- Analyser les logs du poste pour reconstruire la chronologie
- Rechercher des indicateurs de compromission antérieurs au chiffrement (l'attaquant est souvent dans le réseau plusieurs jours avant de déclencher le ransomware)
- Identifier si des données ont été exfiltrées avant le chiffrement (double extorsion)

## Remédiation

- **Ne pas payer la rançon** sans consultation juridique et évaluation des alternatives
- Restaurer depuis les sauvegardes non affectées
- Reconstruire le poste depuis zéro si les sauvegardes sont compromises
- Corriger le vecteur d'entrée avant de reconnecter quoi que ce soit au réseau
- Déposer plainte (ANSSI recommande de ne pas payer et de signaler)

## Documentation

Consigner dans le ticket d'incident :
- Poste(s) affecté(s), étendue du chiffrement, timestamps
- Sauvegardes intactes : oui / non
- Vecteur d'entrée identifié
- Données exfiltrées suspectées : oui / non
- Chronologie complète

Notifier si applicable :
- ANSSI (anssi.fr) pour les opérateurs d'importance vitale
- CNIL si des données personnelles sont affectées
- Assureur cyber si couvert

→ `ioc/ransomware.md`
