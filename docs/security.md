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

## Port security

Appliqué sur les ports d'accès des PC, jamais sur les trunks ni sur le port du serveur.

| Switch | Ports | Maximum | Apprentissage | Violation |
|---|---|---|---|---|
| SW0 | Fa0/1 à Fa0/4 | 1 | sticky | shutdown |
| SW1 | Fa0/2, Fa0/3, Fa0/5 | 1 | sticky | shutdown |

| Test | Attendu | Résultat |
|---|---|---|
| Apprentissage de la MAC de chaque PC | 1 MAC par port (SecureSticky) | OK |
| Branchement d'un second PC sur SW0 Fa0/1 | Port en err-disabled (Secure-shutdown) | OK |
| Réactivation (shutdown puis no shutdown) après remise de PC0 | Port Secure-up | OK |


## Administration à distance (SSH)

Telnet est désactivé : seules les connexions SSH version 2 sont acceptées sur les lignes vty, avec authentification par compte local. L'accès n'est possible que depuis le VLAN ADMIN : les ACL USERS_IN et GUEST_IN bloquent le VLAN 99.

| Équipement | IP de management | Interface |
|---|---|---|
| R1 | 192.168.99.1 | Gig0/0.99 |
| SW0 | 192.168.99.2 | Vlan99 |
| SW1 | 192.168.99.3 | Vlan99 |

Passerelle des switchs : 192.168.99.1. Clés RSA générées sur chaque équipement (domaine infra.local). Les comptes et mots de passe ne sont pas versionnés.

| Test | Attendu | Résultat |
|---|---|---|
| `show ip ssh` sur R1 et SW1 | SSH Enabled, version 2.0 | OK |
| SSH depuis PC0 (ADMIN) vers R1 | Connexion | OK |
| SSH depuis PC0 (ADMIN) vers SW0 | Connexion | OK |
| SSH depuis PC0 (ADMIN) vers SW1 | Connexion | OK |
| SSH depuis PC5 (USERS) vers R1 | Refusé (USERS_IN) | OK |
