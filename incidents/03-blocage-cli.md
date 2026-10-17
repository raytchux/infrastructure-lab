# Incident 03 : blocage de la CLI sur une commande inconnue

## Symptôme
`Translating "end"...domain server (255.255.255.255)` puis CLI figée.

## Impact
Console inutilisable pendant 1 à 2 minutes.

## Hypothèses
Commande mal tapée, mauvais mode.

## Tests réalisés
Lecture du prompt : `Switch#` au lieu de `Switch(config)#`.

## Cause
`end` tapé en mode privilégié ; le switch traite la commande inconnue comme un nom d'hôte et interroge un DNS inexistant.

## Correction
Interruption avec Ctrl+Maj+6 puis x. Ajout de `no ip domain-lookup`.

## Vérification
Une commande inconnue renvoie immédiatement `Invalid input`.

## Prévention
`no ip domain-lookup` sur tous les équipements ; lire le prompt avant de taper.
