# Plan d'adressage

| VLAN | Fonction   | Réseau           | Passerelle    |
|------|------------|------------------|---------------|
| 10   | ADMIN      | 192.168.10.0/24  | 192.168.10.1  |
| 20   | USERS      | 192.168.20.0/24  | 192.168.20.1  |
| 30   | SERVERS    | 192.168.30.0/24  | 192.168.30.1  |
| 40   | GUEST      | 192.168.40.0/24  | 192.168.40.1  |
| 99   | MANAGEMENT | 192.168.99.0/24  | 192.168.99.1  |

## Adresses fixes

| IP            | Équipement                    |
|---------------|-------------------------------|
| 192.168.30.10 | Serveur DHCP/DNS (web)        |
| 192.168.30.20 | Serveur secondaire (réservé)  |

Les adresses .1 à .99 de chaque réseau sont réservées aux équipements à IP fixe.
Les pools DHCP démarrent à .100.
