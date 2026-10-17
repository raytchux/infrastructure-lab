# Incident 04 : lien rouge entre Switch0 et R1

## Symptôme
Lien rouge ; Fa0/6 absent de `show interfaces trunk`.

## Impact
Aucun VLAN transporté vers le routeur.

## Hypothèses
Port routeur éteint, mauvais port, mauvais câble.

## Tests réalisés
Survol du câble, `show ip interface brief`.

## Cause
Ports de routeur éteints par défaut ; `no shutdown` appliqué à Gig0/1 au lieu de Gig0/0.

## Correction
`no shutdown` sur Gig0/0 ; `shutdown` sur Gig0/1.

## Vérification
Gig0/0 `up/up` ; Fa0/6 en trunking.

## Prévention
Vérifier le port réellement câblé avant `no shutdown`.
