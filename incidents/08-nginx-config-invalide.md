# Incident 08 : configuration Nginx invalide

## Symptôme
`nginx -t` renvoie `unexpected "}" in /etc/nginx/sites-enabled/default:52` et `test failed`.

## Impact
Un `restart` aurait arrêté le site ; un `reload` est refusé et l'ancienne configuration reste active.

## Hypothèses
Accolade en trop, directive mal terminée.

## Tests réalisés
`sudo nginx -t` : indique le fichier et la ligne où l'analyse échoue.

## Cause
Un `;` manquait en fin de directive. L'erreur est signalée à l'accolade suivante, pas à la ligne fautive.

## Correction
Ajout du `;` manquant. Sauvegarde préalable dans /root/default.bak.

## Vérification
`nginx -t` : syntax is ok, test is successful. `systemctl reload nginx` sans erreur.

## Prévention
Toujours lancer `nginx -t` avant `reload` ou `restart` ; sauvegarder le fichier avant modification.
