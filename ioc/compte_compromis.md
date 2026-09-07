# IOC — Compte utilisateur compromis

## Indicateurs dans les logs de connexion

**Géographie suspecte**
- Connexion depuis un pays où l'utilisateur n'a jamais été connecté
- Impossible travel : deux connexions simultanées depuis des pays distants

**Appareil/session suspect**
- Nouvel appareil jamais vu sur le compte
- User-Agent inhabituel (navigateur non standard, outil automatisé)
- Connexion depuis une IP connue comme proxy, VPN ou Tor

**Comportement de connexion**
- Plusieurs échecs suivis d'un succès (credential stuffing)
- Connexion à des heures inhabituelles pour ce compte
- Volume d'actions élevé immédiatement après connexion

## Indicateurs d'activité post-connexion

**Email (Microsoft 365 / Google Workspace)**
```
Nouvelle règle de transfert vers un domaine externe
Emails envoyés depuis la boîte sans action utilisateur
Téléchargements massifs de pièces jointes
Accès à des boîtes partagées inhabituelles
Modification des contacts de récupération du compte
```

**Ressources internes**
```
Accès à des partages réseau non habituels
Téléchargements volumineux depuis SharePoint / OneDrive
Connexions VPN depuis une IP non habituelle
Élévation de privilèges ou tentative de modification de groupe AD
```

## Requêtes de détection (exemples KQL pour Microsoft Sentinel)

```kql
// Connexions depuis des pays non vus dans les 30 derniers jours
SigninLogs
| where TimeGenerated > ago(1h)
| extend Country = tostring(LocationDetails.countryOrRegion)
| where Country !in (
    SigninLogs
    | where TimeGenerated between (ago(30d) .. ago(1h))
    | where UserId == <UserId>
    | extend Country = tostring(LocationDetails.countryOrRegion)
    | distinct Country
)
| project TimeGenerated, UserPrincipalName, IPAddress, Country, ResultType
```

```kql
// Règles de boîte créées récemment
OfficeActivity
| where Operation == "New-InboxRule"
| where TimeGenerated > ago(24h)
| project TimeGenerated, UserId, Parameters
```

## Commandes utiles (Microsoft 365 via PowerShell)

```powershell
# Sessions actives sur un compte
Get-AzureADAuditSignInLogs -Filter "userPrincipalName eq 'sarah@entreprise.com'" | Select-Object -First 20

# Règles de boîte d'un utilisateur
Get-InboxRule -Mailbox "sarah@entreprise.com" | Select-Object Name, Enabled, ForwardTo, RedirectTo

# Révoquer toutes les sessions
Revoke-AzureADUserAllRefreshToken -ObjectId <UserId>
```
