# Infrastructure Lab

Infrastructure d'entreprise simulée : réseau segmenté et sécurisé (Cisco Packet Tracer) et serveur Linux (VirtualBox). Projet personnel de préparation à un stage Systèmes et Réseaux.

## Architecture

```text
INTERNET (simulé : 172.16.0.0/24)
        |
       R2 ---- 10.0.0.0/30 ---- R1 (routeur inter-VLAN, NAT, ACL)
                                |
                              trunk
                                |
                          SW0 ==== SW1
                          |         |
              VLAN 10 ADMIN   VLAN 30 SERVEUR DHCP/DNS
              VLAN 20 USERS   VLAN 40 GUEST
                              VLAN 99 MANAGEMENT
```

| VLAN | Fonction | Réseau |
|---|---|---|
| 10 | ADMIN | 192.168.10.0/24 |
| 20 | USERS | 192.168.20.0/24 |
| 30 | SERVERS | 192.168.30.0/24 |
| 40 | GUEST | 192.168.40.0/24 |
| 99 | MANAGEMENT | 192.168.99.0/24 |

## Réalisé

**Réseau (Packet Tracer)**
- VLAN, trunk 802.1Q, routage inter-VLAN (router-on-a-stick)
- DHCP avec relais, un pool par VLAN ; DNS interne (infra.local)
- OSPF entre R1 et R2 ; NAT/PAT vers un réseau externe simulé
- ACL étendues par VLAN (GUEST, USERS, SERVERS)
- Sécurité des switchs : port security, DHCP snooping, DAI
- Administration en SSH v2 sur R1 et les switchs, limitée au VLAN ADMIN

**Linux (VirtualBox)**
- Ubuntu Server, IP statique (netplan), deux cartes (NAT et host-only)
- Utilisateurs, groupes, permissions, dossiers partagés (setgid)
- sudo limité à des commandes précises
- SSH par clé, mot de passe et login root désactivés

## En cours
Nginx, pare-feu (ufw), fail2ban, scripts Bash, Wireshark, Active Directory, Ansible, supervision.

## Limites
- Le réseau est simulé dans Packet Tracer ; le serveur Linux tourne dans VirtualBox. Les deux ne sont pas reliés : la VM reprend l'adressage du VLAN 30 à titre de cohérence.
- Le réseau Internet est simulé par un second routeur.

## Sécurité du dépôt
Aucun mot de passe, hash, clé privée ou secret n'est versionné. Les comptes des équipements sont indiqués en commentaire.

## Organisation

| Dossier | Contenu |
|---|---|
| docs/ | adressage, VLAN, services, routage, NAT, sécurité, Linux |
| configs/ | configurations R1, R2, SW0, SW1 |
| linux/ | netplan, sudoers, SSH |
| incidents/ | incidents rencontrés (symptôme, cause, correction, prévention) |
| topology/ | fichier Packet Tracer |
