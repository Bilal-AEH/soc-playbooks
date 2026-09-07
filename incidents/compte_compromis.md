# Playbook — Compte utilisateur compromis

## Déclencheur

Alerte générée sur : connexion depuis un pays inhabituel, connexion depuis un nouvel appareil non reconnu, connexion réussie après plusieurs échecs, activité inhabituelle sur le compte (règles de boîte créées, emails envoyés en masse, accès à des ressources non habituelles).

Sources possibles : SIEM, console d'administration Microsoft 365 / Google Workspace, EDR, alertes IAM.

## Investigation

**1. Confirmer la compromission**
- L'utilisateur reconnaît-il la connexion signalée ?
- L'IP source est-elle cohérente avec ses habitudes (géolocalisation, FAI, appareil) ?
- Y a-t-il une connexion simultanée légitime et suspecte (impossible travel) ?

**2. Évaluer l'étendue**
- Quelles ressources le compte peut-il accéder ? (emails, partages, VPN, bases de données)
- Des actions ont-elles déjà été effectuées depuis la session suspecte ?
  - Emails envoyés ? À qui ?
  - Fichiers téléchargés ou modifiés ?
  - Règles de boîte créées ?
  - Permissions modifiées ?
- D'autres comptes ont-ils été accédés depuis ce compte ?

**3. Vérifier la latéralisation**
Un compte compromis peut avoir été utilisé pour accéder à d'autres systèmes. Vérifier les logs des accès aux ressources partagées dans les heures suivant la première connexion suspecte.

## Containment — dans l'ordre

1. **Réinitialiser le mot de passe** du compte concerné
2. **Révoquer toutes les sessions actives** (Microsoft 365 : "Sign out everywhere")
3. **Activer le MFA** si non déjà en place
4. **Supprimer les règles suspectes** dans la boîte email (transferts, suppressions automatiques)
5. **Bloquer l'IP source** si elle n'a pas de légitimité
6. **Notifier l'utilisateur** : lui expliquer ce qui s'est passé et les actions prises

## Remédiation

- Analyser l'historique complet des actions de la session compromise
- Prévenir les contacts externes ayant reçu des messages de l'attaquant
- Vérifier la réutilisation du mot de passe sur d'autres services internes
- Évaluer si une déclaration CNIL est nécessaire (données personnelles exposées)
- Déployer le MFA sur l'ensemble des comptes si ce n'est pas déjà fait

## Documentation

Consigner dans le ticket d'incident :
- Compte affecté, IP suspecte, timestamps
- Actions effectuées par l'attaquant pendant la session
- Contacts notifiés
- Durée de la compromission (première connexion suspecte → containment)
- Mesures correctives appliquées

→ `ioc/compte_compromis.md`
