# VLAN et trunk

## Objectif
Segmenter le réseau en trois VLAN sur deux switchs Cisco,
reliés par un trunk 802.1Q.

## Topologie


## Affectation des ports

| Switch  | Port  | Mode   | VLAN | Machine |
|---------|-------|--------|------|---------|
| Switch0 | Fa0/1 | access | 10   | PC1     |
| Switch0 | Fa0/2 | access | 20   | PC2     |
| Switch0 | Fa0/3 | access | 30   | PC3     |
| Switch0 | Fa0/4 | access | 10   | PC4     |
| Switch0 | Fa0/5 | trunk  | tous | vers Switch1 |
| Switch1 | Fa0/1 | trunk  | tous | vers Switch0 |
| Switch1 | Fa0/2 | access | 10   | PC5     |
| Switch1 | Fa0/3 | access | 20   | PC6     |

Les VLAN 10, 20 et 30 sont créés sur chaque switch. Les autres ports restent
dans le VLAN 1.

## Tests d'isolation (un seul switch)

| Test | Attendu | Observé |
|------|---------|---------|
| PC1 -> PC4 (même VLAN 10) | répond | OK |
| PC1 -> PC2 (VLAN 10 -> 20) | échoue | OK (échec) |

PC1 -> PC4 sert de témoin positif : même switch, même câblage, seul le VLAN
change. L'échec vers PC2 est donc bien dû à l'isolation.

## Trunk

`show interfaces trunk` : Fa0/5 (Switch0) et Fa0/1 (Switch1) en mode `on`,
encapsulation `802.1q`, statut `trunking`, VLAN natif 1, VLAN 1-1005 autorisés
(1, 10, 20, 30 actifs).

| Test | Attendu | Observé |
|------|---------|---------|
| PC1 -> PC5 (VLAN 10, autre switch) | répond |
| PC1 -> PC6 (VLAN 10 -> 20) | échoue |

## Incident rencontré
Premier `show interfaces trunk` vide : le câble n'était pas sur les ports
prévus (Gig0/1 au lieu de Fa0/5 et Fa0/1). Un port non configuré en trunk
n'apparaît pas dans cette commande.
