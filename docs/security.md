# Sécurité : filtrage inter-VLAN

Filtrage par ACL étendues nommées sur R1, appliquées en entrée (`in`) sur les sous-interfaces, au plus près de la source.

## Politique

| VLAN | Source | Autorisé | Interdit |
|---|---|---|---|
| 10 | ADMIN | Tout (aucune ACL) | - |
| 20 | USERS | DHCP, DNS (UDP 53), web (TCP 80/443) vers 192.168.30.10, Internet | ADMIN, MANAGEMENT, reste des SERVERS |
| 30 | SERVERS | Réponses uniquement : DHCP relayé, DNS, TCP established, ICMP echo-reply | Toute connexion initiée par le serveur |
| 40 | GUEST | DHCP, DNS (UDP 53) vers 192.168.30.10, Internet | ADMIN, USERS, SERVERS, MANAGEMENT |
| 99 | MANAGEMENT | Aucune ACL pour l'instant | - |

## ACL appliquées

| ACL | Interface | Sens |
|---|---|---|
| USERS_IN | Gig0/0.20 | in |
| SERVERS_IN | Gig0/0.30 | in |
| GUEST_IN | Gig0/0.40 | in |

Les règles DHCP et DNS précèdent les `deny` du VLAN 30 : la première règle qui correspond s'applique.

## Validation

| Test | Attendu | Résultat |
|---|---|---|
| GUEST (PC6) vers 172.16.0.10 | Autorisé | OK |
| GUEST : nslookup web.infra.local | Autorisé | OK |
| GUEST vers ADMIN | Refusé | OK |
| GUEST vers 192.168.30.10 (ping) | Refusé | OK |
| USERS (PC5) vers 172.16.0.10 | Autorisé | OK |
| USERS : nslookup web.infra.local | Autorisé | OK |
| USERS : web http et https vers 192.168.30.10 | Autorisé | OK |
| USERS vers ADMIN | Refusé | OK |
| USERS vers 192.168.30.10 (ping) | Refusé | OK |
| ADMIN (PC3) vers 192.168.30.10 (ping) | Autorisé (echo-reply) | OK |
| SERVERS : connexions initiées | Refusées | OK (7 paquets bloqués) |
| Renouvellement DHCP hors VLAN 30 | Autorisé | OK (2 matches ligne DHCP de SERVERS_IN) |

## Compteurs relevés (`show access-lists`)

| ACL | Règle | Matches |
|---|---|---|
| GUEST_IN | deny vers 192.168.10.0 | 1 |
| GUEST_IN | deny vers 192.168.30.0 | 2 |
| GUEST_IN | permit vers any | 2 |
| USERS_IN | permit tcp www | 10 |
| USERS_IN | permit tcp 443 | 15 |
| USERS_IN | deny vers 192.168.10.0 | 3 |
| USERS_IN | deny vers 192.168.30.0 | 3 |
| SERVERS_IN | permit udp bootps | 2 |
| SERVERS_IN | permit tcp established | 3 |
| SERVERS_IN | permit icmp echo-reply | 2 |
| SERVERS_IN | deny ip | 7 |
