# IOC — Ransomware

## Indicateurs comportementaux (pré-chiffrement)

Ces actions précèdent souvent le déclenchement du chiffrement — les détecter tôt peut permettre de stopper l'attaque avant le chiffrement.

```
Désactivation ou modification du service VSS (Volume Shadow Copy)
  → vssadmin delete shadows /all /quiet
  → wmic shadowcopy delete

Désactivation du pare-feu Windows
  → netsh advfirewall set allprofiles state off

Arrêt de processus de sauvegarde ou antivirus
  → taskkill /f /im backup*.exe

Téléchargement d'outils de reconnaissance (mimikatz, cobalt strike, BloodHound)

Mouvement latéral sur le réseau (connexions SMB vers d'autres postes)

Exfiltration de données avant chiffrement (double extorsion)
```

## Indicateurs au moment du chiffrement

```
Modification d'extensions de fichiers en masse (.locked, .encrypted, .WNCRY, variable selon le groupe)
Création de fichiers README / HOW_TO_DECRYPT / RECOVER_FILES dans les répertoires
Consommation CPU et disque très élevée par un processus inconnu
Modification des wallpapers du bureau
Suppression des sauvegardes locales et points de restauration
```

## Familles connues et signatures

| Famille | Extension typique | Note |
|---|---|---|
| LockBit | `.lockbit` | Très actif, double extorsion |
| BlackCat (ALPHV) | variable | Écrit en Rust, multi-plateforme |
| Cl0p | `.clop` | Cible souvent les transferts de fichiers (MOVEit) |
| Akira | `.akira` | Active depuis 2023 |
| Ryuk | `.ryk` | Ciblé, souvent via BazarLoader |

## Ressources de déchiffrement

Avant tout paiement ou abandon — vérifier :
- https://www.nomoreransom.org — déchiffreurs gratuits disponibles pour certaines familles
- https://id-ransomware.malwarehunterteam.com — identifier la famille à partir d'un fichier chiffré ou de la note

## Commandes de collecte forensique (avant isolation ou redémarrage)

```powershell
# Windows — processus en cours
Get-Process | Select-Object Name, Id, Path, StartTime | Export-Csv processes.csv

# Windows — connexions réseau actives
Get-NetTCPConnection | Where-Object State -eq "Established" | Export-Csv connections.csv

# Windows — tâches planifiées récentes
Get-ScheduledTask | Where-Object {$_.Date -gt (Get-Date).AddDays(-7)} | Export-Csv tasks.csv
```

```bash
# Linux — processus actifs
ps auxf > processes.txt

# Linux — connexions réseau
ss -antp > connections.txt

# Linux — fichiers modifiés récemment
find / -newer /tmp/reference_file -type f 2>/dev/null | head -100 > recent_files.txt
```

## Signalement

- **ANSSI** : https://www.cybermalveillance.gouv.fr (PME et particuliers) / anssi.fr (OIV/OSE)
- **Dépôt de plainte** : recommandé avant tout contact avec l'attaquant
- **CNIL** : notification obligatoire sous 72h si données personnelles concernées
