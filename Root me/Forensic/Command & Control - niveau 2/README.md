# Command & Control - niveau 2

## Description

Un analyste a réalisé un dump mémoire d’un poste Windows afin de mener une investigation.
Cependant, il a oublié de noter le nom de la machine, indispensable pour valider le challenge.

Vous disposez uniquement du dump compressé et devez retrouver le hostname Windows à partir de l’image mémoire.

Ce challenge introduit à la forensic mémoire et à l’analyse avec Volatility 3.

## Fichiers fournis

ch2.tbz2 — archive contenant le dump mémoire compressé

## Objectif

Extraire le nom complet de la machine Windows depuis la RAM, via Volatility 3.

## Méthodologie
### 1. Décompression de l’archive

Le dump mémoire se trouve dans une archive .tbz2 (tar + bzip2).
Il faut d’abord l’extraire.

Commande :
`tar -xvjf ch2.tbz2`

Explication :

- x → extract (extraire)

- v → verbose (affiche les fichiers, facultatif)

- j → décompresse un fichier bz2

- f → indique le nom du fichier à traiter

Après extraction, on obtient :

ch2.dmp


→ Le fichier mémoire à analyser.

### 2. Vérification initiale du dump

On identifie les caractéristiques de la machine :

Commande :
`vol -f ch2.dmp windows.info`

Explication :

- confirme que l’image provient d’un système Windows

- affiche la version, le build, l’architecture

- valide les symboles pour les futurs plugins

À cette étape, on sait que le dump est exploitable.

<img width="1103" height="563" alt="image" src="https://github.com/user-attachments/assets/e0de7342-453b-46eb-82a1-8458d33f18f9" />

### 3. Extraction des variables d’environnement

Sous Windows, chaque processus maintient une série de variables d’environnement, stockées dans la mémoire du processus.

Parmi elles :

- USERNAME

- USERPROFILE

- HOMEDRIVE

- COMPUTERNAME (ce que l’on cherche)

Commande :
`vol -f ch2.dmp windows.envars`

Explication :

Ce plugin parcourt la structure EPROCESS de chaque tâche et extrait la liste complète des variables d’environnement actives au moment de la capture mémoire.

<img width="1090" height="571" alt="image" src="https://github.com/user-attachments/assets/d32a54e9-92d1-4511-b406-67de28fedf02" />


### 4. Filtrage pour obtenir uniquement le COMPUTERNAME

Pour isoler la ligne contenant le nom de la machine, on filtre avec grep.

Commande clé du challenge :
`vol -f ch2.dmp windows.envars | grep -i COMPUTERNAME`

Explication :

- | → envoie la sortie vers une autre commande

- grep → recherche textuelle

- -i → ignore la casse

- COMPUTERNAME → variable contenant le hostname Windows

<img width="666" height="212" alt="image" src="https://github.com/user-attachments/assets/f9c0f642-ff90-4651-a857-0a4c95ef8f44" />


### Flag
