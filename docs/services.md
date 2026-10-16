# Services

## Serveur

| Paramètre | Valeur |
|---|---|
| Hôte | Serveur DHCP/DNS (Packet Tracer) |
| Adresse | 192.168.30.10/24 |
| VLAN | 30 (SERVERS) |
| Port | Switch1 Fa0/4 (access) |
| Passerelle | 192.168.30.1 |

## DHCP

| Pool | Réseau | Passerelle | DNS | Plage de départ | Max |
|---|---|---|---|---|---|
| VLAN10 | 192.168.10.0/24 | 192.168.10.1 | 192.168.30.10 | 192.168.10.100 | 100 |
| VLAN20 | 192.168.20.0/24 | 192.168.20.1 | 192.168.30.10 | 192.168.20.100 | 100 |

Les adresses .1 à .99 de chaque réseau sont réservées aux équipements à IP fixe.

Relais DHCP (R1) : `ip helper-address 192.168.30.10` sur `Gig0/0.10` et `Gig0/0.20`.

## DNS

Domaine interne : `infra.local`

| Nom | Type | Adresse |
|---|---|---|
| web.infra.local | A | 192.168.30.10 |
| dns.infra.local | A | 192.168.30.10 |

## Validation

| Test | Résultat |
|---|---|
| Attribution DHCP VLAN 10 (PC3) | OK |
| Attribution DHCP VLAN 20 (PC5) | OK |
| `ping web.infra.local` | OK |
| `nslookup web.infra.local` | OK |
