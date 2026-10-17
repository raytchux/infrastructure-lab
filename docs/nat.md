# NAT/PAT

## Architecture

R1 traduit les adresses des VLAN autorisés en l'adresse de son interface Gig0/1 (10.0.0.1). Le réseau 172.16.0.0/24 derrière R2 simule Internet. R1 n'annonce plus les réseaux 192.168.x.0 en OSPF : R2 ne connaît aucun réseau privé.

## Configuration (R1)

| Élément | Valeur |
|---|---|
| Interface outside | Gig0/1 |
| Interfaces inside | Gig0/0.10, Gig0/0.20, Gig0/0.40 |
| ACL | access-list 1 (192.168.10.0, 20.0, 40.0 en /24) |
| Règle | ip nat inside source list 1 interface Gig0/1 overload |

VLAN 30 (SERVERS) et VLAN 99 (MANAGEMENT) : exclus du NAT.

## Validation

| Test | Résultat |
|---|---|
| PC0 vers PC7 avant NAT (R2 sans route retour) | Échec |
| PC5 (VLAN 20) vers PC7 | OK |
| PC3 (VLAN 10) vers PC7 | OK |
| PC6 (VLAN 40) vers PC7 | OK |
| PC2 (VLAN 30) vers PC7 | Échec (attendu) |

Exemple de traduction observée :

| Inside local | Inside global | Destination |
|---|---|---|
| 192.168.40.100 | 10.0.0.1 | 172.16.0.10 |

Compteurs de l'ACL 1 : VLAN 20 = 8, VLAN 10 = 6, VLAN 40 = 4.
