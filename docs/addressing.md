# Plan d'adressage

| VLAN | Fonction   | Réseau           | Gateway       | Hôtes utilisables |
|------|------------|------------------|---------------|-------------------|
| 10   | ADMIN      | 192.168.10.0/24  | 192.168.10.1  | 254               |
| 20   | USERS      | 192.168.20.0/24  | 192.168.20.1  | 254               |
| 30   | SERVERS    | 192.168.30.0/24  | 192.168.30.1  | 254               |
| 40   | GUEST      | 192.168.40.0/24  | 192.168.40.1  | 254               |
| 99   | MANAGEMENT | 192.168.99.0/24  | 192.168.99.1  | 254               |

## Adresses réservées

| IP             | Équipement      |
|----------------|-----------------|
| 192.168.30.10  | Serveur Linux   |
| 192.168.30.20  | Serveur secondaire |

## Choix du /24

Un /24 par VLAN donne 254 hôtes ce qui est suffisants pour une petite entreprise.
