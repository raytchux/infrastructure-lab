# Incident 06 : port coupé par port security

## Symptôme
Lien rouge sur SW0 Fa0/1 après le remplacement de PC0 par un autre PC.

## Impact
Aucune communication sur le port.

## Hypothèses
Câble défectueux, port éteint, violation de port security.

## Tests réalisés
`show interfaces status` : port err-disabled. `show port-security interface fastEthernet 0/1` : Secure-shutdown.

## Cause
La MAC du nouveau PC différait de la MAC apprise (sticky) ; maximum fixé à 1, violation en mode shutdown.

## Correction
Remise de PC0 sur le port, puis `shutdown` et `no shutdown` sur l'interface.

## Vérification
Port Secure-up ; ping depuis PC0 réussi.

## Prévention
`clear port-security sticky interface` avant de changer le poste d'un port ; documenter les MAC autorisées.
