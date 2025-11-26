# Capture moi ça

## Description

Un employé a perdu le mot de passe de sa base KeePass (Database.kdbx).
Il se souvient avoir envoyé un screenshot de ses mots de passe à un collègue, mais celui-ci a été recadré, et le mot de passe maître n’apparaît plus.

Votre mission : retrouver le mot de passe KeePass malgré le recadrage.

Ce challenge exploite Acropalypse, une faille permettant de récupérer une image non recadrée depuis un screenshot PNG vulnérable.

## Fichiers fournis

Capture.png — Screenshot recadré du bureau Windows

Database.kdbx — Base KeePass protégée par mot de passe

## Objectif

Retrouver le mot de passe complet permettant d’ouvrir la base KeePass.

## Méthodologie
### 1. Analyse du screenshot

L’image fournie montre :

- un bureau Windows

- un fichier To Do List.txt

- un fichier Excel ouvert avec plusieurs mots de passe (Google, Amazon, Root-Me, etc.)

Un caractère “k” isolé à droite, indice d’un mot de passe partiellement visible. Cependant, aucun mot de passe affiché dans le tableau n’ouvre la base KeePass. Il faut donc retrouver la partie cachée de l’image, supprimée lors du recadrage.

### 2. Détection de la vulnérabilité Acropalypse

Les versions non patchées de Snipping Tool / Snip & Sketch ne supprimaient pas les pixels supprimés lors d’un recadrage.

Un screenshot recadré peut donc toujours contenir :

- le PNG normal

- un “trailer” avec les données de l’image originale complète

Pour vérifier la vulnérabilité :

```def detect_png(self, input_source):
    ...
    if len(data) > iend_index + iend_full_chunk_length:
        return True```


Si des données existent après le chunk IEND, l’image est vulnérable.

Dans le fichier du challenge original :
la détection est positive 

### 3. Reconstruction de l’image originale

Le script Acropalypse cherche dans les données résiduelles :

- les anciens chunks IDAT,

- les bitstreams tronqués,

- les décalages de bits possibles (8 positions),

- une séquence valide décompressable en zlib.

Code principal :

`decompressed = d.decompress(prefix+truncated)`


Une fois le bon offset trouvé :

Found viable parse at bit offset X!


Le script génère alors une nouvelle image :

`restored.png`


Cette image contient :

- en haut → une zone magenta (pixels impossibles à récupérer)

- en bas → la vraie image non recadrée

### 4. Analyse de l’image restaurée

Dans restored.png, au-delà des colonnes visibles dans la version recadrée, se trouve :

le mot de passe complet KeePass, caché dans une colonne très éloignée (ex : X, Y ou Z)


<img width="1384" height="842" alt="image" src="https://github.com/user-attachments/assets/6f7e3987-ac9d-4ce1-8187-2b605e68bea7" />



Le mot de passe retrouvé est :

`-=b9w9h^+j%\x-rMPUqv9Vv`@X%*=a`



### 5. Ouverture de la base KeePass

En ouvrant Database.kdbx avec ce mot de passe :

-=b9w9h^+j%\x-rMPUqv9Vv`@X%*=a


on accède à l’unique entrée de la base contenant le flag du challenge :

Flag

