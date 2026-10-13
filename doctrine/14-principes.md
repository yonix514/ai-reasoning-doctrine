<!--
  Document publie a partir d'un systeme de travail prive.
  Les etudes de cas portant sur des projets prives, leurs chiffres et
  les chemins internes ont ete retirees ou rendues generiques. Les cas
  de montage video, de scripting VEGAS Pro et d'ffmpeg sont conserves :
  ce sont les travaux de l'auteur.
-->

# the harness-doctrine - la doctrine de raisonnement du harness

> Document de reference. A lire **avant** toute analyse, toute mesure, tout audit,
> et **avant d'annoncer un chiffre**.
> Chaque principe est ne d'une **erreur reelle**, avec les chiffres mesures dans la session.
> Les sources sont citees : `the private harness`,
> `verif/une verification interne`, `un projet prive/md/une synthese interne`, `un projet prive/md/un rapport interne`,
> `un projet prive/md/un document interne`, `vegas/scripts/format_sortie.cs`.

## La regle en une phrase

**Aucune affirmation sans mesure. Aucune mesure sans contradicteur. Aucun calcul sur le poste.**

## Les 14 principes

### 1. MESURER AVANT D'AFFIRMER

**L'erreur reelle.** Un systeme de un projet prive a annonce « valeur +4,5 % » pendant des semaines.
Recalcul complet, sans reseau, sur les 99 evenements cotes du cache (`un projet prive/un audit interne` §1.3) :

| Mesure recalculee | Valeur |
|---|---|
<!-- ligne retirée : projet privé et chiffre privé -->
<!-- ligne retirée : projet privé et chiffre privé -->
| Ecart (= le « gain de marge ») | **4,5455 %** |
<!-- ligne retirée : projet privé et chiffre privé -->

**Ce que la session a d'abord conclu :** « le meme nombre » - le detecteur ne trouvait rien,
il mesurait sa propre construction (un **maximum** compare a une **moyenne** des memes
~20 cotes).

**Ce que le contradicteur a corrige ensuite** (`verif/une verification interne`, Affirmation 1,
verdict **PARTIELLEMENT VRAI**) - et c'est la lecon la plus importante de cette doctrine :

- « positivite **par construction** » est **FAUX** : sur les **256 issues** du meme cache,
<!-- ligne retirée : projet privé et chiffre privé -->
  mediane **-0,7532 %**.
- Le vrai mecanisme est un **biais pro-outsider**, mesure par tranche de cote :
<!-- ligne retirée : projet privé et chiffre privé -->
  `3,0-6,0` : 45,8 % (~+0,163 %) · `6,0-10,0` : **100 %** (~+6,437 %) · `10,0-et plus` :
  **100 %** (~+20,183 %).
- Contre-exemple algebrique : avec **un seul** bookmaker, `EV = 1/O - 1 = -marge < 0`
<!-- ligne retirée : projet privé et chiffre privé -->
- « 4,5455 % ~ 4,5100 % : c'est le meme nombre » est **FAUX, coincidence de troncature** :
<!-- ligne retirée : projet privé et chiffre privé -->
  signaux non suspects ont `min = 2,0100 %`, mediane 4,5100 %, max 14,8800 %.
  La mediane d'une distribution **tronquee** comparee a un ecart calcule sur
  99 evenements x toutes les issues : deux populations differentes.
<!-- ligne retirée : projet privé et chiffre privé -->
  **fausse en soi** : la formule reelle utilise le **consensus des probabilites
  devigorees**, qui n'est **pas** `1/cote`.

**LA REGLE.** Un chiffre qui revient « trop bien » doit etre recalcule **de zero**, par un
calcul ecrit par soi-meme, sur les **memes donnees brutes**. Puis :
1. calculer la distribution **complete** (pas seulement les lignes retenues) ;
2. chercher le **mecanisme** (ici : le biais depend de la tranche de cote) ;
3. chercher le **contre-exemple algebrique** (ici : un seul bookmaker) ;
4. verifier **sur quelle population** porte chaque nombre (ici : fichier tronque vs cache entier).
Une conclusion juste peut reposer sur une demonstration fausse. Les deux se verifient separement.

### 2. UNE ABSENCE DE RESULTAT N'EST PAS UNE PREUVE D'ABSENCE

**Les erreurs reelles.**
- Un verificateur a annonce « `pipeline.log` **absent** » sur la base d'un `find` qui n'a
  rien retourne. **Le fichier existait (7 086 o).** Son erreur venait de **sa** commande.
- Autre cas : « le serveur est eteint », conclu sur un `ping` sans reponse.
  **Il etait allume**, a une **autre adresse IP**.

**LA REGLE.** Avant de conclure « absent » :
- refaire la recherche **autrement** (`ls` du dossier parent, `find` sans filtre, chemin absolu) ;
- verifier que **sa propre commande** aurait trouve l'objet s'il existait ;
- ne jamais transformer « ma commande n'a rien renvoye » en « la chose n'existe pas ».
Le mot juste est **« je n'ai pas trouve avec cette methode »**.

### 3. VERIFIER CE QU'ON A AVANT DE CONSTRUIRE DESSUS

**L'erreur reelle.** Trois analyses de « style de montage » ont ete construites sur un
fichier suppose etre un **recap monte**. C'etait un **enregistrement d'ecran de lecture** :
aucune coupe, **un seul plan de 133 secondes**. Les trois analyses mesuraient le montage
d'une video qui n'en etait pas un.

**LA REGLE.** Avant d'analyser un fichier, prouver sa **nature** :
duree, nombre reel de plans/coupes, presence de transitions, codec, source.
Un plan unique de 133 s n'est pas un montage - c'est une capture.

### 4. TESTER SON PROPRE OUTIL AVANT DE LUI FAIRE CONFIANCE

**L'erreur reelle.** Un scanner reseau a conclu « **aucun serveur nulle part** ».
**Il ne detectait meme pas la passerelle** - c'est-a-dire l'objet dont la presence est
certaine. Un second scanner, **valide par auto-test**, a trouve le serveur immediatement.

**LA REGLE.** Tout outil de mesure passe d'abord un **auto-test** sur une cible dont on
connait deja la reponse. Un scanner qui rate la passerelle n'a aucune autorite pour dire
« aucun serveur ». Un outil non valide ne produit pas un resultat, il produit du bruit.

### 5. DISTINGUER « FAUX » DE « NON VERIFIABLE »

**LA REGLE.** Ce ne sont **pas** la meme chose, et ce ne sont pas les memes mots.

| Situation | Reponse autorisee |
|---|---|
| J'ai mesure le contraire, avec la preuve | « **c'est faux**, voici la mesure » |
| Je n'ai pas pu mesurer | « **je n'ai pas pu verifier** » |
| J'ai mesure, resultat ambigu | « **non concluant**, IC = [...] » |

« Je n'ai pas pu verifier » est une **reponse acceptable** et souvent la bonne.
« C'est faux » **sans preuve** ne l'est pas. `une verification interne` utilise exactement ces
verdicts : *CONFIRME*, *PARTIELLEMENT VRAI*, *REFUTE*, *NON CONCLUANT*.

### 6. LA CORRECTION POUR TESTS MULTIPLES

**L'erreur reelle.** Le projet de un projet prive a teste **314 strategies** (`un projet prive/md/une synthese interne` §2).
Parmi elles, **219 executables** (deux prix contemporains, reellement disponibles au moment
du pari) et **95 non executables**.

| Jeu de tests | m | IC 95 % entierement positif | survivants Benjamini-Hochberg (FDR 5 %) | survivants Bonferroni |
|---|---|---|---|---|
| **TOUS** les tests realises | 314 | 36 | 29 | 22 |
| Tests **EXECUTABLES** seulement | **219** | **2** | **0** | **0** |

Seuil de Bonferroni : `0,05 / 314 = 0,000159` ; sur les seuls executables `0,05 / 219 = 0,000228`.
Sur les **95 strategies NON executables** (fuite de donnees) : **34** ont un IC positif et
**29** survivent a Benjamini-Hochberg.

**Le « succes » venait de la methode de mesure, pas du marche.** Detail des 34 artefacts :
<!-- ligne retirée : projet privé et chiffre privé -->
Pinnacle de cloture), **3** du prix d'ouverture pris **apres** que le marche a bouge,
**1** d'un **prix de combinaison que personne ne publie**.

**LA REGLE.** Des qu'on teste **plus d'une** hypothese, il faut corriger.
- declarer **le nombre total** de tests realises, y compris ceux qu'on n'a pas publies ;
- distinguer **executable** de **non executable** : un resultat non executable ne compte pas,
  meme s'il survit a la correction ;
- Bonferroni = critere le plus severe (FWER) ; Benjamini-Hochberg = taux de fausses
  decouvertes (FDR 5 %) ;
- un resultat qui survit **seulement** sans correction n'est pas un resultat.

### 7. CHERCHER LA FUITE DE DONNEES

**L'erreur reelle.** Une strategie « rentable » comparait une cote d'**ouverture** a une cote
de **cloture** : elle utilisait une information qui n'existait **pas encore** au moment du
pari. Sur **90** comparaisons, **30** ressortaient « rentables ».
**Alignees correctement dans le temps : 1 sur 89, et 0 survivante** a Benjamini-Hochberg
(`une synthese interne` §5.2 ; `pinnacle.md` : « Sur 89 comparaisons alignees : 1 ont un IC 95 %
entierement positif ; apres Benjamini-Hochberg, il en reste 0 »).
Toujours sur la piste Pinnacle : sans correction, **1/179** strategies paraissent rentables ;
avec Benjamini-Hochberg, **0** survivent.

**LA REGLE.** La fuite de donnees est **le piege principal** de toute etude temporelle.
- ecrire noir sur blanc **ce qui etait connu a l'instant de la decision** ;
- n'utiliser que des prix **contemporains** (ouverture/ouverture, cloture/cloture) ;
- se mefier des prix « synthetiques » (combinaison, consensus, meilleure cote multi-books) :
  ils n'existent chez aucun operateur ;
- tester la version **alignee** ; si l'avantage disparait, c'etait la fuite qui parlait.
### 8. NE JAMAIS PROMETTRE AU-DELA DU MESURE

**Les chiffres reels.** Le systeme de un projet prive a ete evalue sur un jeu de donnees de
**85 834 matchs** (`un projet prive/md/un document interne` : `donnees3.csv`, 29 140 510 o,
sha256 `4c3045c3a9dbbe42`), **22 divisions** x **12 saisons**
(`une synthese interne` §3a : 276 fichiers demandes = 22 x 12 ; 264 en HTTP 200, 12 en HTTP 300).
Resultat (`une synthese interne` §1) : **aucune des 6 pistes n'est robustement rentable.**

| Piste | n un projet prive | ROI | IC 95 % | Verdict |
|---|---|---|---|---|
| 1. Divisions inferieures (agregat) | 31 431 | -6,90 % | voir DIVISIONS.md | PERDANT |
| 2. Pinnacle (ALIGNE) | 147 | +24,72 % | [-23,78 % ; +82,24 %] | NON CONCLUANT (0 survit a BH) |
| 3. Arbitrage (ouverture) | 956 | +1,15 % | deterministe | GARANTI SUR LE PAPIER - NON EXECUTE |
| 4. Marches (Asian Handicap ext.) | 16 067 | +3,75 % | [+0,64 % ; +7,65 %] | effet du biais du modele |
| 4. Marches (combine, prix SYNTHETIQUE) | 16 621 | +8,33 % | [+4,94 % ; +11,73 %] | NON EXPLOITABLE : ce prix n'existe pas |
| 5. Promotions (matched betting) | sans objet | sans objet | sans objet | SEUL GAIN GARANTI - montants NON verifiables |
| 6. Mouvement (steam a la cloture) | 41 068 | -0,96 % | [-2,38 % ; +0,49 %] | PERDANT |
| 6. Mouvement (reference ouverture) | 8 522 | +14,35 % | [+10,01 % ; +18,96 %] | NON EXECUTABLE |

Le modele Dixon-Coles ne bat le marche **nulle part** : ecart de log-loss positif et
significatif sur **21 des 22 divisions** (la 22e, SC3, non concluante : IC
[-0,0084 ; +0,0262]).

**LA REGLE.** L'utilisateur a prefere cette verite a une promesse - **et c'est ce qui lui a
evite de perdre de l'argent**. On annonce le resultat **avec son incertitude**, jamais un
ROI sans son intervalle de confiance, jamais un « rentable » sans « survivant a la correction ».
Une conclusion negative, mesuree, est un **livrable**. Ce n'est pas un echec.

### 9. VERIFIER LE FICHIER QUE PRODUIT UNE TACHE, PAS SA DECLARATION

**L'erreur reelle.** Deux crons declares « actifs » n'avaient **jamais produit un seul
fichier**. Un cron qui n'ecrit pas de journal est **indiscernable d'un cron mort**.

**LA REGLE.** Pour toute tache planifiee (cron, timer, service) :
- exiger un **battement de coeur horodate a chaque execution** (une ligne, meme vide) ;
- verifier l'**existence et la date** du fichier produit, pas la presence de la ligne de cron ;
- verifier que le fichier **grossit** entre deux executions ;
- « la ligne est dans la crontab » ne prouve **rien** sur ce qui s'execute.
Corollaire : le journal doit contenir l'**horodatage** et, si possible, le **code de retour**.

### 10. CHOISIR LA BONNE METHODE SELON LA SOURCE

**Ce qui a reellement ete fait** (`verif/une verification interne`, en-tete) : le contradicteur a
reecrit **23 scripts** (`v00.py` ... `v23.py`), tous ecrits par lui, en repartant des
**sources brutes** (`donnees.csv`, `preds_test.csv`, `fixtures.csv`, `journal.csv`,
`le cache prive`, `le journal prive`, `un projet prive/etat/dataset.csv`, APIs en direct).
Le code de l'agent n'a ete **lu** que pour auditer les protocoles, **jamais reutilise pour
mesurer**. Resultat : 3 affirmations sur 8 sont tombees ou se sont affaiblies, dont deux que
personne n'avait vues (bug de CLV sur 675 des 1250 un projet prive ; filtre horaire absent).

**LA REGLE.**
- **Ne jamais reutiliser le code de l'auteur** quand on verifie : ecrire le sien.
- Croiser **au moins 2 sources independantes** (ici : ESPN + TheSportsDB + un telechargement
  live de un projet prive.co.uk, identique **au md5 pres**).
- Chercher le **contre-exemple** et l'**angle mort** : le contradicteur a liste
  **8 angles morts** que l'agent n'avait pas vus.
- Verifier aussi les **sources** elles-memes : codes HTTP reels (401, 403, 404, 000, 302),
  pas « l'API marche » (`une synthese interne` §3b : **18 sources sur 32** repondent 200).

### 11. AJOUTER D'ABORD, REGLER ENSUITE (APIs)

**L'erreur reelle (Vegas Pro).** Regler `VideoMotionKeyframe.Bounds` **avant**
`Keyframes.Add(k)` est **ignore** : Vegas calcule la fenetre par defaut **au moment de
l'ajout** et **ecrase** la valeur.
Resultat mesure : **72 images sur 288 reussies, 216 ratees**.
Corrige en **ajoutant d'abord, en reglant ensuite** -> **288/288**.
Source du mecanisme : `vegas/scripts/format_sortie.cs` lignes 162-176
(« ON AJOUTE D'ABORD, ON REGLE ENSUITE »).

**Meme famille de bug, hors Vegas.** `ffmpeg acrossfade` avec son defaut `overlap=1`
**raccourcit chaque jonction de 30 ms**. Sur **943 jonctions** :
`943 x 30 ms = 28,29 s` perdus **silencieusement** (sortie 1770,51 s au lieu de 1798,8 s).
Corrige par `overlap=0`. Sources : `vegas/paquet/LIVRAISON/04-DOCUMENTATION/EXPLICATION.md`
lignes 132-144 ; `vegas/atelier2/GUIDE-MODIFICATION.md` lignes 96-97.

**LA REGLE.** Un objet doit **exister dans son conteneur** avant qu'on le configure.
Et : **verifier la duree totale en sortie** - une perte de 30 ms par jonction est invisible
unite par unite et enorme en agregat. Toujours comparer `duree_attendue` et `duree_obtenue`.

### 12. LE CONTRADICTEUR OBLIGATOIRE

**Toute affirmation doit etre verifiee par un agent distinct dont la mission EST de la
contrefaire.**

Le modele de reference est `verif/une verification interne` : **8 affirmations** examinees une par une,
chacune avec **VERDICT**, **METHODE**, **PREUVE**, et une section **« les attaques qui
echouent »** (une attaque qui echoue doit etre listee aussi : elle prouve que l'attaque a eu lieu).

**Regle d'obligation.** Le contradicteur ne peut pas conclure « tout va bien » sans avoir :
- **liste au moins 5 attaques tentees** ;
- **produit au moins 1 defaut reel** - ou demontre pourquoi aucun n'est trouvable,
  **en montrant ses tentatives**.
**Un rapport qui dit « conforme » sans lister ses tentatives est REJETE.**

**Et le contradicteur doit se verifier lui-meme.** Dans la session il s'est corrige
**4 fois**, dont une fois ou il a evite un **faux positif**.

Structure minimale d'un rapport de contradiction :
```
1. VERDICT par affirmation (CONFIRME / PARTIELLEMENT VRAI / REFUTE / NON CONCLUANT)
2. METHODE : ce que j'ai recalcule, avec quel code, sur quelles donnees brutes
3. PREUVE : mon chiffre vs le chiffre affirme, cote a cote
4. LES ATTAQUES QUI ECHOUENT (ce que j'ai essaye de casser et qui a tenu)
5. LES ANGLES MORTS (ce que personne n'avait vu)
6. CE QUE JE N'AI PAS PU VERIFIER (et pourquoi)
7. PREUVE QUE LE PC N'A PAS ETE TOUCHE
```
Le contradicteur travaille en **lecture seule** sur les donnees d'autrui et **ecrit son
propre code**. Il ne « confirme » pas : il **attaque**.

### 13. CE QUI COMPTE N'EST PAS D'AVOIR RAISON, C'EST QUE LE TRAVAIL SOIT JUSTE

**L'erreur reelle.** L'utilisateur a corrige **6 fois** sur un meme chantier.
**Chaque fois a raison.**
- « ca doit suivre l'audio »
- « il n'y a pas d'animation »
- « tu as choisi les images ? »
- « ne touche plus mon PC »
- « utilise la methode que je t'ai montree »

**LA REGLE.** **Ecouter la correction vaut mieux que defendre la solution.**
Une correction de l'utilisateur est une **mesure**, pas une opinion : elle vient de quelqu'un
qui regarde le resultat. Quand il dit « il n'y a pas d'animation », il faut ouvrir le fichier
et compter les images cles - pas expliquer pourquoi le script devait en mettre.
Ne jamais repondre « mais le script a bien tourne » : c'est une declaration (principe 9),
pas une preuve.

### 14. LE SERVEUR CALCULE, LE POSTE CONTROLE

**L'erreur reelle.** Le processeur de l'utilisateur a **chauffe a 97 %**, parce que des agents
calculaient **en local**.

**LA REGLE.**
- Le poste ecrit des **scripts de quelques kilo-octets** et **controle** ; le **serveur execute**.
- Le poste ne doit pas depasser **10 %**.
- Sur le poste, **uniquement** : `plink`, `pscp`, lire, petits fichiers texte.
- **Jamais** sur le poste : `python` lourd, `ffmpeg`, `ffprobe`, whisper, OpenCV,
  toute **boucle de calcul**.
- Tout travail long : `tmux` + `nice -n 10`.
- Locale FR casse les outils -> **`LC_ALL=C`**. Fins de ligne CRLF -> `sed -i 's/\r$//'`.
- Ecriture distante : **base64 dans une seule commande SSH**, ce qui evite tout fichier
  temporaire sur le poste et tout probleme CRLF.

Le detail du patron complet est dans la competence **`the harness-serveur-uniquement`**.

---

## COMMENT ABORDER UNE MISSION

La sequence, dans cet ordre, sans en sauter une :

```
1. LIRE ce qui existe avant de creer
      ls, cat, INDEX, SKILLS.md, PROJETS.md. Ne pas reinventer ce qui est la.

2. VERIFIER ce qu'on a (le fichier est-il ce qu'on croit ?)
      nature, taille, date, duree, nombre de plans, empreinte. Principe 3.

3. MESURER l'etat initial
      un chiffre avant, un chiffre apres. Sans etat initial, aucun progres n'est prouvable.

4. SAUVEGARDER avant de modifier
      .bak_<date> horodate, empreinte SHA256 avant/apres.

5. FAIRE le plus petit changement qui teste l'hypothese
      pas de refonte. Une variable, une ligne, une comparaison.

6. MESURER le resultat
      meme methode, memes donnees, meme outil qu'a l'etape 3.

7. FAIRE VERIFIER par un contradicteur
      agent distinct, mission = contredire, 5 attaques listees minimum. Principe 12.
      Puisqu'un outil non teste ne vaut rien : auto-tester l'outil (principe 4).

8. DIRE ce qui a echoue
      la partie ratee fait partie du livrable. Principe 8.
```

**Ordre non negociable :** on ne code pas avant d'avoir lu, on ne modifie pas avant d'avoir
sauvegarde, on n'annonce pas avant d'avoir fait verifier.

## Les 3 questions a se poser avant d'annoncer un chiffre

1. **D'ou vient ce nombre ?** Puis-je le recalculer de zero, moi-meme, sur les donnees brutes ?
2. **Sur quelle population ?** Est-elle complete, ou tronquee par un filtre que j'ai oublie ?
3. **Qui a essaye de me contredire ?** Combien d'attaques, lesquelles, et qu'ont-elles donne ?

Si les trois reponses ne sont pas solides : le mot juste est **« je n'ai pas pu verifier »**.