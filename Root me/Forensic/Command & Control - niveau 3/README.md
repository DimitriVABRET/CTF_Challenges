# Command & Control - niveau 3

## Description

Ce challenge consiste à analyser un dump de mémoire vive afin d’identifier un programme malveillant en cours d’exécution au moment de la capture.

Le flag demandé est :
→ le hash MD5 (en minuscules) du chemin absolu de l’exécutable malveillant.

Fichier fourni

ch2.dmp — Dump de la RAM d’une machine Windows 7.

## Objectif

Identifier le programme malveillant présent en mémoire, récupérer son chemin absolu, puis calculer son empreinte MD5.

## Méthodologie
### 1. Identification du système

Utilisation de Volatility 2 pour analyser le dump :

vol.py -f ch2.dmp imageinfo


Profil détecté :

Win7SP0x86

### 2. Analyse des processus actifs
vol.py -f ch2.dmp --profile=Win7SP0x86 pslist


Parmi les processus légitimes, un Internet Explorer attire l’attention :

- PID 2772  iexplore.exe


Son emplacement n’est pas celui habituel (Program Files), ce qui en fait un candidat suspect.

### 3. Récupération de la ligne de commande
vol.py -f ch2.dmp --profile=Win7SP0x86 cmdline -p 2772


Résultat :

<img width="839" height="67" alt="image" src="https://github.com/user-attachments/assets/f938a8ab-2041-49d8-b8b4-5e32190177d1" />


### 4. Génération du flag

Le flag correspond au MD5 du chemin absolu, sans guillemets :

<img width="832" height="59" alt="image" src="https://github.com/user-attachments/assets/d8f65ff0-5e21-4371-b60c-9b42d3393295" />

