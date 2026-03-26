
# TP Guidé – Answer Set Programming (ASP)
## Corrigé complet

## Objectif

Dans ce TP, nous construisons progressivement un modèle ASP complet en suivant ces étapes :

1. Définir des faits
2. Utiliser des variables
3. Générer des solutions
4. Ajouter des contraintes
5. Résoudre un problème réel


## Environnement

Utiliser le solveur en ligne :

👉 https://potassco.org/clingo/run/



# Étape 1 – Premiers faits

## Énoncé

Définir les faits suivants :

```prolog
person(alice).
person(bob).
person(charlie).
```

Ajouter ensuite une règle disant que toute personne est active.

## Solution

```prolog
person(alice).
person(bob).
person(charlie).

active(X) :- person(X).
```

## Explication

* `person/1` définit trois faits
* la règle `active(X) :- person(X).` s’applique à chaque personne
* après instanciation, on obtient :

```prolog
active(alice).
active(bob).
active(charlie).
```



# Étape 2 – Introduction des variables

## Énoncé

Ajouter une règle :

```text
likes(X) si X est une personne
```

## Solution

```prolog
person(alice).
person(bob).
person(charlie).

likes(X) :- person(X).
```

## Explication

La variable `X` doit être bornée par un domaine connu. Ici, le domaine est donné par `person(X)`.

Le solveur instancie la règle en :

```prolog
likes(alice) :- person(alice).
likes(bob) :- person(bob).
likes(charlie) :- person(charlie).
```

Puisque les faits `person(alice)`, `person(bob)` et `person(charlie)` sont vrais, on déduit :

```prolog
likes(alice).
likes(bob).
likes(charlie).
```

## Remarque importante

Si on écrivait seulement :

```prolog
likes(X).
```

cela ne serait pas accepté comme il faut dans une modélisation ASP correcte, car `X` ne serait pas correctement bornée.



# Étape 3 – Génération de possibilités

## Énoncé

Choisir une couleur parmi :

* red
* blue
* green

Puis modifier pour imposer exactement une couleur.

## Solution 1 ; choix libre

```prolog
{color(red); color(blue); color(green)}.
```

## Explication

Cette règle autorise toutes les combinaisons possibles :

* aucune couleur
* une seule couleur
* deux couleurs
* les trois couleurs

## Solution 2 ; exactement une couleur

```prolog
1 {color(red); color(blue); color(green)} 1.
```

## Explication

Cette cardinalité signifie :

* au moins 1 couleur
* au plus 1 couleur

Donc exactement une seule couleur est choisie.

## Réponses possibles

```prolog
color(red).
```

ou

```prolog
color(blue).
```

ou

```prolog
color(green).
```



# Étape 4 – Génération libre

## Énoncé

Générer librement les faits :

* a
* b
* c

## Solution

```prolog
{a; b; c}.
```

## Explication

Cette règle génère toutes les sous-combinaisons possibles de `{a,b,c}`.

## Solutions possibles

```text
{}
{a}
{b}
{c}
{a,b}
{a,c}
{b,c}
{a,b,c}
```

Chaque answer set représente un sous-ensemble possible de `{a,b,c}`.

# Étape 5 – Ajout de contraintes

## Énoncé

Ajouter une contrainte qui interdit `a` et `b` en même temps.

## Solution

```prolog
{a; b; c}.

:- a, b.
```

## Explication

La contrainte :

```prolog
:- a, b.
```

signifie :

> il est interdit d’avoir `a` et `b` ensemble dans un même modèle

## Solutions restantes

```text
{}
{a}
{b}
{c}
{a,c}
{b,c}
```

## Solutions éliminées

```text
{a,b}
{a,b,c}
```



# Étape 6 – Modélisation d’un problème simple

## Énoncé

Définir :

* trois objets `a`, `b`, `c`
* trois couleurs `red`, `green`, `blue`

Attribuer exactement une couleur à chaque objet, puis interdire que deux objets différents aient la même couleur.

## Solution

```prolog
object(a; b; c).
color(red; green; blue).

1 { assign(X,C) : color(C) } 1 :- object(X).

:- assign(X,C), assign(Y,C), X != Y.
```

## Explication détaillée

### Domaine

```prolog
object(a; b; c).
color(red; green; blue).
```

On définit les objets et les couleurs possibles.

### Génération

```prolog
1 { assign(X,C) : color(C) } 1 :- object(X).
```

Pour chaque objet `X`, on choisit exactement une couleur `C`.

### Contrainte

```prolog
:- assign(X,C), assign(Y,C), X != Y.
```

Deux objets différents ne peuvent pas partager la même couleur.

## Interprétation

Chaque answer set représente une affectation valide des couleurs aux objets.

Exemple de solution :

```prolog
assign(a,red).
assign(b,green).
assign(c,blue).
```



# Étape 7 – Passage à un graphe

## Énoncé

Définir un graphe :

* `a --- b`
* `b --- c`
* `a --- c`

Attribuer une couleur à chaque nœud, avec la contrainte que deux nœuds reliés ne peuvent pas avoir la même couleur.

## Solution

```prolog
node(a; b; c).

edge(a,b).
edge(b,c).
edge(a,c).

color(red; green; blue).

1 { assign(X,C) : color(C) } 1 :- node(X).

:- assign(X,C), assign(Y,C), edge(X,Y).
```

## Explication

* `node/1` définit les sommets
* `edge/2` définit les arêtes
* chaque nœud reçoit exactement une couleur
* si deux nœuds sont reliés, ils ne doivent pas avoir la même couleur

## Remarque

Ici, le graphe forme un triangle. Il faut donc trois couleurs différentes.


Exemple de solution :

```prolog
assign(a,red).
assign(b,green).
assign(c,blue).
```


# Étape 8 – Problème combinatoire : N-Queens

## Énoncé

Sur un échiquier `N x N`, placer `N` reines de façon à ce que :

* chaque ligne contienne exactement une reine
* aucune colonne ne contienne deux reines
* aucune diagonale ne contienne deux reines

## Solution

```prolog
n(8).

row(1..N) :- n(N).
col(1..N) :- n(N).

1 { queen(R,C) : col(C) } 1 :- row(R).

:- queen(R1,C), queen(R2,C), R1 != R2.

:- queen(R1,C1), queen(R2,C2),
   R1 != R2,
   |R1 - R2| == |C1 - C2|.
```

## Explication détaillée

### Taille de l’échiquier

```prolog
n(8).
```

Ici on fixe `N = 8` à titre d’exemple (8 reines). Remplacez 8 par `N` si vous voulez un échiquier `N x N`.

### Définition des lignes et colonnes

```prolog
row(1..N) :- n(N).
col(1..N) :- n(N).
```

Si `N = 8`, alors on génère :

```prolog
row(1). row(2). ... row(8).
col(1). col(2). ... col(8).
```

### Une reine par ligne

```prolog
1 { queen(R,C) : col(C) } 1 :- row(R).
```

Pour chaque ligne `R`, on choisit exactement une colonne `C`.

### Pas deux reines sur la même colonne

```prolog
:- queen(R1,C), queen(R2,C), R1 != R2.
```

Si deux lignes différentes ont une reine dans la même colonne, la solution est interdite.

### Pas deux reines sur la même diagonale

```prolog
:- queen(R1,C1), queen(R2,C2),
   R1 != R2,
   |R1 - R2| == |C1 - C2|.
```

Deux reines sont sur la même diagonale si la différence de leurs lignes est égale à la différence de leurs colonnes.

## Exemple de solution possible

```prolog
queen(1,1) queen(2,5) queen(3,8) queen(4,6)
queen(5,3) queen(6,7) queen(7,2) queen(8,4)
```


# Étape 9 – Sudoku (optionnelle)

## Énoncé

Modéliser un Sudoku 9x9 avec les contraintes suivantes :

* chaque cellule contient une valeur de 1 à 9
* chaque ligne contient des valeurs différentes
* chaque colonne contient des valeurs différentes
* chaque bloc 3x3 contient des valeurs différentes

## Solution

```prolog
row(1..9).
col(1..9).
val(1..9).

1 { cell(R,C,V) : val(V) } 1 :- row(R), col(C).

:- cell(R,C,V1), cell(R,C,V2), V1 != V2.

:- cell(R,C1,V), cell(R,C2,V), C1 != C2.

:- cell(R1,C,V), cell(R2,C,V), R1 != R2.

block(R,C,B) :-
    B = ((R-1)/3)*3 + (C-1)/3.

:- cell(R1,C1,V), cell(R2,C2,V),
   block(R1,C1,B),
   block(R2,C2,B),
   (R1 != R2 ; C1 != C2).
```

## Explication détaillée

### Domaines

```prolog
row(1..9).
col(1..9).
val(1..9).
```

On définit lignes, colonnes et valeurs.

### Une valeur par cellule

```prolog
1 { cell(R,C,V) : val(V) } 1 :- row(R), col(C).
```

Chaque cellule `(R,C)` reçoit exactement une valeur `V`.

### Unicité dans une cellule

```prolog
:- cell(R,C,V1), cell(R,C,V2), V1 != V2.
```

Une même cellule ne peut pas contenir deux valeurs différentes.

### Unicité sur une ligne

```prolog
:- cell(R,C1,V), cell(R,C2,V), C1 != C2.
```

Une même valeur ne peut pas apparaître deux fois sur une même ligne.

### Unicité sur une colonne

```prolog
:- cell(R1,C,V), cell(R2,C,V), R1 != R2.
```

Une même valeur ne peut pas apparaître deux fois sur une même colonne.

### Unicité dans un bloc 3x3

```prolog
block(R,C,B) :-
    B = ((R-1)/3)*3 + (C-1)/3.
```

Cette formule associe à chaque cellule un numéro de bloc.

Puis :

```prolog
:- cell(R1,C1,V), cell(R2,C2,V),
   block(R1,C1,B),
   block(R2,C2,B),
   (R1 != R2 ; C1 != C2).
```

Une même valeur ne peut pas apparaître deux fois dans un même bloc.

## Remarque

Pour un vrai Sudoku, on ajoute aussi des valeurs initiales, par exemple :

```prolog
cell(1,1,5).
cell(1,2,3).
cell(1,5,7).
```

Il faut alors adapter la génération pour respecter ces cases déjà remplies.



# Étape 10 – Compréhension globale

## 1. Différence entre `not p(X)` et `p(X)`

* `p(X)` signifie que `p(X)` doit être vrai
* `not p(X)` signifie que `p(X)` n’est pas prouvé

En ASP, `not` est une négation par défaut.



## 2. Différence entre générer et contraindre

### Générer

Créer des solutions possibles.

Exemple :

```prolog
{a; b; c}.
```

### Contraindre

Éliminer les solutions interdites.

Exemple :

```prolog
:- a, b.
```



## 3. Pourquoi ASP est-il adapté aux problèmes combinatoires ?

Parce qu’on peut :

* décrire toutes les solutions possibles
* exprimer facilement les contraintes
* laisser le solveur explorer l’espace de recherche



## 4. Que représente un Answer Set ?

Un answer set est une solution complète et cohérente du programme ; c’est un ensemble de faits qui respecte toutes les règles et toutes les contraintes.



# Rappel essentiel

ASP suit le schéma :

```text
Generate → Constrain → Solve
```

1. Générer des mondes possibles
2. Éliminer ceux qui violent les contraintes
3. Garder uniquement les solutions valides



# Conseils pédagogiques

* toujours introduire un domaine explicite
* construire le programme étape par étape
* commencer par générer, puis ajouter les contraintes
* tester régulièrement
* observer plusieurs answer sets pour comprendre le comportement du modèle
