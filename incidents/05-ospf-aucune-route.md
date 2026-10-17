# Incident 05 : voisin OSPF FULL mais aucune route apprise

## Symptôme
`show ip ospf neighbor` affiche R2 en FULL/BDR, mais `show ip route` sur R1 ne contient aucune route `O`.

## Impact
Le réseau 172.16.0.0/24 derrière R2 est injoignable depuis les VLAN.

## Hypothèses
Instruction `network` erronée, interface de R2 éteinte ou sans IP, interface sans lien.

## Tests réalisés
`show ip protocols` sur R2 : réseaux correctement déclarés. `show ip interface brief` : Gig0/1 unassigned, administratively down.

## Cause
Aucune interface up de R2 ne portait une IP de 172.16.0.0/24 : le réseau n'était pas annoncé. Gig0/1 était sans IP, éteinte et sans équipement branché.

## Correction
IP 172.16.0.1/24 et `no shutdown` sur Gig0/1, PC raccordé pour que le lien soit up.

## Vérification
Route `O 172.16.0.0/24` présente sur R1 ; ping PC0 vers PC7 réussi.

## Prévention
Contrôler `show ip interface brief` (up/up) avant de déclarer les `network`.
