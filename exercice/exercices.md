# TP Guidé – Answer Set Programming (ASP)

## Objectif

Dans ce TP, vous allez construire progressivement un modèle ASP complet en suivant ces étapes :

1. Définir des faits
2. Utiliser des variables
3. Générer des solutions
4. Ajouter des contraintes
5. Résoudre un problème réel



## Environnement

Utiliser le solveur en ligne :

👉 https://potassco.org/clingo/run/

### Modes de raisonnement (à connaître)

Dans le solveur en ligne, vous pouvez changer le mode de raisonnement.

Exemple minimal :

```prolog
a :- not b.
b :- not a.

#show a/0.
#show b/0.
```

Ce programme a deux answer sets possibles : `{a}` et `{b}`.

* `default` : retourne un answer set (souvent le premier trouvé).
* `enumerate all` : retourne tous les answer sets (`{a}` et `{b}`).
* `cautious` : garde seulement ce qui est vrai dans tous les answer sets (ici : rien).
* `brave` : garde ce qui est vrai dans au moins un answer set (ici : `a` et `b`).

Lecture rapide :

* `cautious` = intersection des solutions
* `brave` = union des solutions

### Directives Clingo utiles

Certaines lignes commencent par `#` : ce sont des directives du solveur.

Syntaxes générales :

```prolog
#const nom = valeur.
#show predicat/arite.
#show terme : condition.
```

Exemple :

```prolog
#const n = 3.
person(1..n).
{selected(X)} :- person(X).

#show selected/1.
```

À retenir :

* `#const` sert à paramétrer un programme sans réécrire toutes les règles.
* `#show` sert à contrôler ce qui est affiché dans la sortie.




# Étape 1 – Premiers faits

## Objectif

Définir un domaine simple.

### À faire

Définir les faits suivants :

```prolog
person(alice).
person(bob).
person(charlie).
```

### Puis ajouter une règle :

```text
active(X) si X est une personne
```



### À observer

* Combien de résultats sont produits ?
* Comment ASP applique-t-il la règle ?


# Étape 2 – Introduction des variables

## Objectif

Comprendre le rôle des variables et du grounding.

### À faire

Ajouter la règle suivante :

```text
likes(X) si X est une personne
```


### Questions

* Que se passe-t-il si vous enlevez `person(X)` ?
* Pourquoi ASP a besoin d’un domaine ?


# Étape 3 – Génération de possibilités

## Objectif

Apprendre à générer des solutions.

### À faire

Écrire une règle qui permet de choisir une couleur parmi :

```text
red, blue, green
```

Indice :

* utiliser `{}`



### Étape suivante

Modifier votre règle pour imposer :

```text
exactement une couleur
```



# Étape 4 – Génération libre

## Objectif

Explorer toutes les combinaisons possibles.

### À faire

Générer librement les faits :

```text
a, b, c
```


### Questions

* Combien de solutions obtenez-vous ?
* Que représente chaque solution ?


# Étape 5 – Ajout de contraintes

## Objectif

Filtrer les solutions.

### À faire

Ajouter une contrainte :

```text
interdire a et b en même temps
```



### Questions

* Quelles solutions disparaissent ?
* Pourquoi ?


# Étape 6 – Modélisation d’un problème simple

## Objectif

Associer des objets à des valeurs.

### À faire

Définir :

```prolog
object(a; b; c).
color(red; green; blue).
```



### Étape suivante

Attribuer exactement une couleur à chaque objet.



### Puis ajouter une contrainte

Deux objets différents ne peuvent pas avoir la même couleur.


### Questions

* Combien de solutions obtenez-vous ?
* Que représente une solution ?



# Étape 7 – Passage à un graphe

## Objectif

Appliquer le modèle à un problème réel.

### À faire

Définir un graphe :

```text
a --- b --- c
 \         /
   -------
```



### Étape suivante

* associer une couleur à chaque nœud
* interdire que deux nœuds connectés aient la même couleur



### Questions

* Le nombre de solutions change-t-il ?
* Pourquoi ?


# Étape 8 – Problème combinatoire (N-Queens)

## Objectif

Modéliser un problème plus complexe.

### À faire

1. Définir un échiquier NxN
2. Placer une reine par ligne
3. Interdire :

* deux reines sur la même colonne
* deux reines sur la même diagonale


### Indices

* utiliser des variables `(R, C)`
* utiliser `|R1 - R2| == |C1 - C2|`



# Étape 9 – Extension (optionnelle)

## Objectif

Approcher un problème réel complexe.

### À faire

Modéliser un Sudoku :

* chaque cellule a une valeur
* contraintes sur lignes, colonnes, blocs



# Étape 10 – Compréhension globale

## Questions finales

1. Quelle est la différence entre :

```prolog
not p(X)
```

et

```prolog
p(X)
```



2. Quelle est la différence entre :

```text
générer
```

et

```text
contraindre
```



3. Pourquoi ASP est-il adapté aux problèmes combinatoires ?



4. Que représente un "Answer Set" ?



# Rappel essentiel

ASP suit toujours ce schéma :

```text
Generate → Constrain → Solve
```


# Conseils

* toujours définir un domaine
* penser en termes de contraintes
* ne pas raisonner comme en programmation impérative
* tester étape par étape
* observer toutes les solutions



Essayez de modifier vos programmes :

* ajouter des contraintes
* enlever des contraintes
* observer l’impact sur les solutions

C’est la meilleure manière de comprendre ASP.


