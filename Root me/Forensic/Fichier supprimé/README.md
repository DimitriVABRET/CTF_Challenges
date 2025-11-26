# USB Forensics Challenge
## Description

Ce challenge consiste à analyser l’image d’une clé USB retrouvée à la bibliothèque.
Le but est d’identifier le propriétaire de la clé.

Le flag demandé est au format :

`prénom_nom`

### Fichiers fournis

usb.image — Image brute de la clé USB.

## Objectif

Retrouver l’identité du propriétaire à partir des informations présentes dans l’image disque.

## Méthodologie
### 1. Extraction des chaînes lisibles

PowerShell :

(strings .\usb.image) -match "[\x20-\x7E]{4,}"

### 2. Analyse du résultat

En parcourant les chaînes extraites, on trouve des métadonnées XMP contenant un nom d’auteur.

Aperçu :

<img width="603" height="456" alt="image" src="https://github.com/user-attachments/assets/336c2261-599a-4f05-922d-25b710b4b675" />


## Ce nom correspond au propriétaire de la clé USB.
