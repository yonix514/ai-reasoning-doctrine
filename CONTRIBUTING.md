# Contribuer

Ce document est une doctrine de raisonnement. Le critère d'acceptation est donc
particulier : **une contribution n'est acceptée que si elle apporte une
preuve.**

## Ce qui est recherché

- Un **nouveau principe**, ou la correction d'un principe existant, avec
  l'**erreur réelle** qui l'a motivé et la **mesure** qui la démontre.
- Un **type d'erreur** manquant dans la taxonomie des 8.
- Un **cas d'usage** dans lequel la doctrine donne un mauvais conseil. Ce sont
  les contributions les plus utiles.

## Ce qui est refusé

- Une affirmation sans mesure : « il vaut mieux toujours… », « en général… ».
- Un chiffre sans méthode de mesure.
- Un exemple qui copie le travail de quelqu'un d'autre.
- Tout secret : jeton, mot de passe, clé d'API, adresse IP interne, nom de
  machine, nom de projet privé. **Un dépôt public ne contient aucun secret.**

## Format d'un principe

```markdown
### N. LE TITRE DU PRINCIPE

**L'erreur réelle.** Ce qui a été fait, et pourquoi c'était faux.

**La mesure.** Le chiffre, et comment il a été obtenu.

**Ce que le contradicteur a corrigé.** Si l'analyse initiale était fausse,
le dire — c'est la partie la plus instructive.

**LA RÈGLE.** Une phrase actionnable.
```

Une *pull request* = un principe. Dites d'où vient l'erreur : une erreur réelle
est ce qui donne sa valeur au principe.
