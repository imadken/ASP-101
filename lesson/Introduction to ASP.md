# Introduction à ASP – Answer Set Programming 101

# 1. Introduction

ASP (Answer Set Programming) est un paradigme de programmation basé sur la **logique déclarative**, proche de Prolog mais avec une sémantique différente.

Comme en Prolog, on ne décrit pas **comment calculer**, mais **ce qui est vrai**.

Cependant, ASP va plus loin :

- Prolog cherche à **prouver un objectif**
- ASP cherche à **générer des modèles cohérents** appelés *answer sets*

Un programme ASP décrit un **problème**, et le solveur retourne toutes les **solutions possibles** satisfaisant les contraintes.



# 2. Différence avec les langages classiques

Dans les langages impératifs :

- on écrit une suite d’instructions
- le programme suit un flux d’exécution

En ASP :

- on décrit des **contraintes logiques**
- le système génère toutes les solutions possibles

Ce changement implique une nouvelle manière de penser :

> Décrire le problème = définir l’espace des solutions + les contraintes



# 3. Différence avec Prolog

Bien que similaires en apparence :

| Prolog | ASP |
|------|-----|
| basé sur la preuve (SLD resolution) | basé sur des modèles (answer sets) |
| exécution dirigée par une requête | exécution globale du programme |
| backtracking | génération de modèles |
| logique procédurale | logique déclarative pure |

ASP est plus adapté aux **problèmes combinatoires et de recherche**.



# 3.1 Visualisation d’arbre de recherche : Prolog vs ASP

**Idée clé :** Prolog explore un **arbre de preuves** (goal-directed), alors qu’ASP explore l’**espace global des modèles** (solution-directed).

| Prolog (preuve / backtracking) | ASP (modèles / contraintes) |
|---|---|
| ```text
Requête Q
├─ règle 1 → sous-buts
│  ├─ règle 1.1 → ...
│  └─ règle 1.2 → ...
└─ règle 2 → sous-buts
   └─ ...
↺ backtracking si échec
``` | ```text
Programme ASP
├─ Candidat M1 = {…}
├─ Candidat M2 = {…}
├─ Candidat M3 = {…}
└─ Candidat M4 = {…}
↓ filtrage par contraintes
Answer Sets (solutions)
``` |

**Lecture rapide :**
* Prolog : on **cherche une preuve** pour une requête donnée.
* ASP : on **génère des modèles** puis on **élimine** ceux qui violent les contraintes.



# 4. Structure d’un programme ASP

Un programme ASP est composé de **règles logiques**.

Structure générale :

```prolog
head :- body.
```

Mais contrairement à Prolog, ASP est **non directionnel** :

* on ne “lance pas une requête”
* on calcule des **modèles (solutions)**



# 5. Faits

Les faits sont des informations toujours vraies.

```prolog
parent(john, mary).
parent(mary, luc).
```



# 6. Règles

Syntaxe :

```prolog
head :- body.
```

Exemple :

```prolog
grandparent(X,Z) :-
    parent(X,Y),
    parent(Y,Z).
```


# 7. Négation en ASP

ASP distingue deux types de négation :

## 1. Négation classique

```prolog
- p(X)
```

## 2. Négation par défaut (très importante)

```prolog
not p(X)
```

Interprétation :

```text
not p(X) signifie "p(X) n’est pas prouvé"
```

C’est la base du raisonnement non monotone.



# 8. Exemple simple

```prolog
bird(tweety).
penguin(tweety).

flies(X) :-
    bird(X),
    not penguin(X).
```

Ici :

* Tweety est un oiseau
* Tweety est un pingouin
* donc `flies(tweety)` est faux



# 9. Génération et contraintes

ASP fonctionne souvent en deux phases :

#### - Générer des solutions possibles

#### - Éliminer celles qui ne respectent pas les contraintes


# 9.1 Slide — Generate / Constrain / Solve (ultra clair)

```text
Generate → Constrain → Solve
```

| Generate | Constrain | Solve |
|---|---|---|
| Définir l’espace des possibilités (faits + choix). | Enlever les candidats qui violent les règles. | Garder uniquement les answer sets. |

**Mnémo :** on *produit*, on *filtre*, on *garde*.



# 10. Choix (Choice Rules)

ASP permet de générer des possibilités :

```prolog
{ p(X) }.
```

Cela signifie :

> p(X) peut être vrai ou faux

Exemple :

```prolog
{color(red); color(blue); color(green)}.
```



# 11. Contraintes

Une contrainte élimine des modèles.

Syntaxe :

```prolog
:- condition.
```

Exemple :

```prolog
:- color(red), color(blue).
```

Cela signifie :

> on ne peut pas avoir red et blue en même temps



# 12. Cardinalité

ASP permet d’exprimer des contraintes numériques.

Exemple :

```prolog
1 {color(red); color(blue); color(green)} 1.
```

Signifie :

> exactement une couleur doit être choisie



# 13. Variables

Les variables commencent par une majuscule.

```prolog
X, Y, Z
```

Comme en Prolog.



# 14. Domaines

Les variables doivent être **bornées** par des faits.

Exemple :

```prolog
person(alice).
person(bob).

likes(X) :- person(X).
```



# 15. Sémantique et Résolution en ASP

## 15.1 Intuition générale

Contrairement à Prolog qui tente de **prouver un objectif**, ASP fonctionne en :

1. Générant des **ensembles candidats de faits**
2. Vérifiant si ces ensembles respectent les règles
3. Gardant uniquement ceux qui sont **cohérents**

Ces ensembles sont appelés :

```text
Answer Sets (ou modèles stables)
```



## 15.2 Vue globale du processus

Le solveur ASP suit globalement ce pipeline :

```text
Programme ASP
    ↓
Grounding (instanciation)
    ↓
Génération de candidats
    ↓
Vérification des contraintes
    ↓
Answer Sets (solutions)
```



## 15.3 Étape 1 : Grounding (instanciation)

ASP ne travaille pas directement avec des variables.

Il commence par transformer le programme en version **sans variables**.

Exemple :

```prolog
person(alice).
person(bob).

likes(X) :- person(X).
```

Après grounding :

```prolog
likes(alice) :- person(alice).
likes(bob) :- person(bob).
```

Puis comme `person(alice)` est vrai :

```prolog
likes(alice).
likes(bob).
```

Le grounding produit un programme **propositionnel**.



## 15.4 Étape 2 : Génération de modèles candidats

Le solveur génère des **ensembles possibles de faits**.

Exemple :

```prolog
{p; q}.
```

Modèles possibles :

```text
{}
{p}
{q}
{p,q}
```

Tous les ensembles possibles sont considérés comme candidats.



## 15.5 Étape 3 : Vérification des règles

Une règle :

```prolog
a :- b, not c.
```

Signifie :

> si b est vrai et c n’est pas prouvé, alors a doit être vrai

Le solveur vérifie que chaque modèle candidat respecte :

* toutes les règles
* toutes les contraintes



## 15.6 Étape 4 : Réduction de Gelfond-Lifschitz

C’est le cœur de la sémantique ASP.

Pour un modèle candidat M :

1. On supprime toutes les règles contenant `not p` avec `p ∈ M`
2. On enlève les `not` restants

On obtient un programme **sans négation**.

Puis :

> M est valide si c’est un modèle minimal de ce programme réduit



## 15.7 Exemple simple de réduction

Programme :

```prolog
a :- not b.
b :- not a.
```

### Candidats :

```text
{}
{a}
{b}
{a,b}
```

### Test du candidat {a}

* `not b` est vrai → règle devient `a :-`
* `not a` est faux → règle supprimée

Programme réduit :

```prolog
a.
```

Modèle minimal :

```text
{a}
```

Donc {a} est un answer set.



### Test du candidat {b}

Même logique :

```text
{b} est aussi un answer set
```



### Résultat final

```text
Answer sets :
{a}
{b}
```



## 15.8 Contraintes

Une contrainte :

```prolog
:- a, b.
```

Signifie :

> les modèles contenant a et b sont interdits

Donc si un candidat contient `a` et `b`, il est rejeté.



## 15.9 Minimalité

Un answer set est :

```text
minimal
```

Cela signifie :

> il ne contient que ce qui est nécessaire

Exemple :

```prolog
a :- b.
b.
```

Le modèle :

```text
{b, a}
```

est valide.

Mais :

```text
{a}
```

ne l’est pas car `b` est requis.



## 15.10 Rôle de "not"

Le `not` en ASP est crucial :

```prolog
not p
```

ne signifie pas :

```text
p est faux
```

mais :

```text
p n’est pas prouvé
```

C’est ce qui permet le **raisonnement non monotone**.



## 15.11 Différence fondamentale avec Prolog

### Prolog

* cherche une preuve
* explore avec backtracking
* dépend de l’ordre

### ASP

* génère des modèles
* évalue globalement
* indépendant de l’ordre des règles (en théorie)



## 15.12 Exemple complet de résolution

Programme :

```prolog
person(alice).
person(bob).

{likes(alice, pizza); likes(alice, pasta)}.

:- likes(alice, pizza), likes(alice, pasta).
```

### Étape 1 ; grounding

Déjà instancié.



### Étape 2 ; génération

```text
{}
{pizza}
{pasta}
{pizza, pasta}
```



### Étape 3 ; contrainte

On élimine :

```text
{pizza, pasta}
```



### Étape 4 ; answer sets

```text
{likes(alice, pizza)}
{likes(alice, pasta)}
```



## 15.13 Résumé du mécanisme

ASP fonctionne comme :

```text
1. Generate
2. Constrain
3. Solve
```

ou encore :

```text
Generate → Constrain → Solve
```


## 15.14 Intuition finale

ASP ne répond pas à une question.

Il répond à :

```text
"Quelles sont toutes les solutions possibles à ce problème ?"
```

Et chaque réponse est un **monde cohérent complet**




# 16. Résolution en ASP

Contrairement à Prolog :

* pas de requête
* pas de backtracking explicite

ASP :

```text
programme → génération de modèles → filtrage → answer sets
```



# 17. Exemple complet

```prolog
person(alice).
person(bob).

{likes(alice, pizza); likes(alice, pasta)}.

:- likes(alice, pizza), likes(alice, pasta).
```

Résultats possibles :

```text
likes(alice, pizza)
```

ou

```text
likes(alice, pasta)
```



# 18. ASP et recherche combinatoire

ASP est particulièrement adapté pour :

* Sudoku
* N-Queens
* planning
* allocation de ressources
* graphes

Car il permet de décrire :

* l’espace des solutions
* les contraintes


# 19. Outils ASP

Le solveur le plus utilisé :

```text
Clingo
```

Commande typique :

```bash
clingo program.lp
```


# 20. Différences clés avec Prolog

### Prolog

* exécute une requête
* utilise unification + backtracking
* logique orientée exécution

### ASP

* génère des modèles
* utilise contraintes globales
* logique orientée solution



# 21. Conclusion

ASP repose sur :

* la définition de règles logiques
* la génération de solutions possibles
* la filtration via contraintes
* la sémantique des modèles stables

C’est un paradigme puissant pour résoudre des problèmes complexes de manière déclarative.

