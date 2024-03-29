# Autres conseils ou astuces

## Utilisation de set 

| | Description |
|:--:|:--:|
| set -e | si erreur = exit |
| set -u | si une variable n'est pas utilisée = exit |
| set -f | désactive le globbing (à enlever si utilisation) |
| shopt -s failglob | si erreur de correspondance avec un regex alors = exit |
| set -o pipefail | si certaines commandes n'ont pas fonctionnées dans le pipe alors = exit  |

## Conseil écriture de scripts

* Écrire les scripts comme si dans deux ans quelqu'un d'autre allait les faire évoluer.
* Commenter.
* Espacer.
* Indenter.
* Créer des variables explicites. 
* Utiliser $(toto) au lieux de ``` `toto` ```.
* Avant de quitter un script par une erreur toujours décrire cette erreur.
* Utiliser le plus possible les enchaînements de commandes avec &&||| ;...

## Conseils en ligne de commande 

Préférer les flag au arg
ex:

dpkg -i nom
dpkg --install nom

## Pourquoi documenter mes scripts

Il est important de toujours documenter son code pour que nous puissions l'améliorer ou le réparer même dans 2 ans et aussi pour que d'autres personnes puissent le poursuivre plus facilement ou si jamais nous avons besoin d'un bout de code pour un autre script alors on comprendras mieux le code si c'est bien commenté.

## Quelle extention pour mon script
Au lieu de mettre une extension en .sh pour des scripts qui tournent avec bash il vaudrait mieux utiliser .bash pour ne pas confondre entre un script ecrit en sh et un autre ecrit en bash. 
