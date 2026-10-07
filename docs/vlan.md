# VLAN

## Objectif
Segmenter le réseau en trois VLAN sur un switch Cisco.

| VLAN | Nom     | Ports  | Machine |
|------|---------|--------|---------|
| 10   | ADMIN   | Fa0/1  | PC1     |
| 10   | ADMIN   | Fa0/4  | PC4     |
| 20   | USERS   | Fa0/2  | PC2     |
| 30   | SERVERS | Fa0/3  | PC3     |

Tous les ports sont en mode access. Les autres ports restent dans le VLAN 1.

## Tests

| Test | Résultat attendu | Résultat observé |
|------|------------------|------------------|
| PC1 -> PC4 (même VLAN 10) | répond | OK |
| PC1 -> PC2 (VLAN 10 -> 20) | échoue | OK (échec) |

PC1 -> PC4 sert de témoin positif : même switch, même câblage, seul le VLAN
change entre les deux tests. L'échec vers PC2 est donc bien dû à l'isolation
des VLAN.
