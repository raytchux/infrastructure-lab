# Incident 07 : configuration SSH durcie non appliquée

## Symptôme
`sshd -T` affiche `passwordauthentication yes` alors que le fichier de durcissement contient `PasswordAuthentication no`.

## Impact
L'authentification par mot de passe reste active : le durcissement n'a aucun effet.

## Hypothèses
Syntaxe erronée, service non rechargé, valeur écrasée par un autre fichier.

## Tests réalisés
`sudo grep -ri passwordauthentication /etc/ssh/sshd_config.d/` : un second fichier (`50-cloud-init.conf`) définit la directive. `sshd -T` : valeur réellement appliquée.

## Cause
OpenSSH conserve la première valeur lue, dans l'ordre alphabétique de `/etc/ssh/sshd_config.d/`. `50-cloud-init.conf` précédait `99-durcissement.conf`.

## Correction
Renommage en `00-durcissement.conf`, puis `sshd -t` et `systemctl reload ssh`.

## Vérification
`sshd -T` : `passwordauthentication no`. Connexion forcée par mot de passe refusée (`Permission denied (publickey)`).

## Prévention
Contrôler la valeur appliquée avec `sshd -T` plutôt que la lecture du fichier.
