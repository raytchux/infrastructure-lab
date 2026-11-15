# Serveur Linux

## Machine

| Paramètre | Valeur |
|---|---|
| Hôte | srv-linux (VirtualBox) |
| Système | Ubuntu Server LTS |
| Carte 1 (enp0s3) | NAT, DHCP (accès Internet) |
| Carte 2 (enp0s8) | Host-only, 192.168.30.20/24, sans passerelle |

Le serveur reprend l'adressage du VLAN 30 (serveur secondaire). Il n'est pas relié à la topologie Packet Tracer. Configuration réseau : `linux/netplan/00-installer-config.yaml`.

## Utilisateurs et groupes

| Compte | Groupes secondaires |
|---|---|
| bob | sysadmin, reseau |
| claire | sysadmin, reseau |

| Dossier | Propriétaire:groupe | Droits |
|---|---|---|
| /srv/reseau | root:reseau | 2770 (setgid) |
| /srv/admin | root:sysadmin | 2770 (setgid) |

## sudo limité

| Groupe | Commande autorisée | Fichier |
|---|---|---|
| reseau | /usr/bin/systemctl restart nginx | linux/sudoers/reseau |
| sysadmin | /usr/bin/journalctl -u ssh | linux/sudoers/sysadmin |

## SSH

Authentification par clé (ed25519), mot de passe désactivé, connexion root interdite, accès limité à l'utilisateur `user`. Configuration : `linux/ssh/00-durcissement.conf`.

| Test | Résultat |
|---|---|
| Connexion par clé | OK |
| Connexion forcée par mot de passe | Refusée |
| Connexion en root | Refusée |
| Connexion en bob (hors AllowUsers) | Refusée |
| `sshd -T` | passwordauthentication no, permitrootlogin no, allowusers user |

## Nginx

Serveur web installé depuis les dépôts Ubuntu, actif et activé au démarrage. Page servie depuis `/var/www/html/index.html`.

| Test | Résultat |
|---|---|
| Écoute sur le port 80 (IPv4 et IPv6) | OK |
| `curl http://192.168.30.20` depuis le PC | OK |
| Service arrêté : connexion refusée | OK |
| `nginx -t` détecte une erreur de syntaxe | OK (incident 08) |
