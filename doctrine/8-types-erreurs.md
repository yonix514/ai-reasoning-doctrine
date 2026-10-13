<!--
  Document publie a partir d'un systeme de travail prive.
  Les etudes de cas portant sur des projets prives, leurs chiffres et
  les chemins internes ont ete retirees ou rendues generiques. Les cas
  de montage video, de scripting VEGAS Pro et d'ffmpeg sont conserves :
  ce sont les travaux de l'auteur.
-->

# the harness-verification - le protocole de contradiction obligatoire

## Pourquoi cette competence existe

Dans la session de reference (`the private harness`),
**8 affirmations** ont ete soumises a un verificateur adversarial independant :

- **3 sont tombees ou se sont affaiblies** ;
- **2 defauts que personne n'avait vus** ont ete trouves
  (bug de CLV sur **675 des 1250** un projet prive officiels ; filtre horaire absent dans `pipeline.py`) ;
- **8 angles morts** ont ete listes.

Sans ce passage, des chiffres faux auraient ete publies. **C'est l'etape la plus rentable
de tout le travail.**

## La regle d'obligation

**Toute affirmation doit etre verifiee par un agent distinct dont la mission EST de la
contrefaire.**

Le contradicteur ne peut **pas** conclure « tout va bien » sans avoir :

1. **liste au moins 5 attaques tentees** (nommees, avec leur resultat) ;
2. **produit au moins 1 defaut reel** - ou demontre pourquoi aucun n'est trouvable,
   **en montrant ses tentatives**.

> **Un rapport qui dit « conforme » sans lister ses tentatives est REJETE.**

Et le contradicteur **doit se verifier lui-meme** : dans la session il s'est corrige
**4 fois**, dont une fois ou il a evite un **faux positif**.

## Les 8 types d'erreurs a chercher

| # | Type | Signature | Cas reel mesure |
|---|---|---|---|
<!-- ligne retirée : projet privé et chiffre privé -->
| **E2** | **Absence** | Une non-detection est convertie en non-existence | « `pipeline.log` absent » sur un `find` vide -> le fichier existait (**7 086 o**). « Le serveur est eteint » sur un `ping` muet -> il etait **allume, a une autre adresse IP** |
| **E3** | **Nature** | L'objet analyse n'est pas ce qu'on croit | 3 analyses de « style de montage » sur un **enregistrement d'ecran de lecture** : aucune coupe, **un seul plan de 133 s** |
| **E4** | **Outil** | L'instrument n'a pas ete valide | Un scanner conclut « **aucun serveur nulle part** » mais **ne detecte meme pas la passerelle**. Un second scanner, auto-teste, trouve le serveur aussitot |
| **E5** | **Multiplicite** | Plusieurs hypotheses testees, aucune correction | **314** strategies testees -> **36** IC positifs, **29** survivent a Benjamini-Hochberg, **22** a Bonferroni. Sur les **219 executables** : **2** positifs, **0** survivant a BH, **0** a Bonferroni |
| **E6** | **Temporalite** | Fuite de donnees : information pas encore connue | Ouverture vs cloture : **30 « rentables » sur 90**. Alignees dans le temps : **1 sur 89**, **0** survivante |
| **E7** | **Ordre** | API : configurer avant d'inserer | `Bounds` regle **avant** `Keyframes.Add(k)` est **ecrase** -> **72/288**. Ajouter puis regler -> **288/288**. Meme famille : `ffmpeg acrossfade` defaut `overlap=1` -> **28,29 s** perdues sur **943** jonctions |
| **E8** | **Declaration** | L'etat declare remplace l'etat produit | **2 crons** declares « actifs » n'avaient **jamais produit un seul fichier**. Un cron sans journal est **indiscernable d'un cron mort** |

**Sous-type important de E1 : la coincidence numerique.** Deux nombres egaux ne prouvent pas
qu'ils decrivent la meme chose. « 4,5455 % ~ 4,5100 % » a ete declare « le meme nombre » :
<!-- ligne retirée : projet privé et chiffre privé -->
(min 2,0100 %, max 14,8800 %), l'ecart de 4,5455 % sur **99 evenements x toutes les issues**.
**Deux populations differentes.** Toujours verifier **sur quelle population** porte un nombre.

## La methode du contradicteur

```
1. ECRIRE SON PROPRE CODE
   Ne JAMAIS reutiliser le code de l'auteur pour mesurer. Dans la session : 23 scripts
   v00.py ... v23.py, tous ecrits par le verificateur, en nice -n 10, LC_ALL=C.
   Le code de l'auteur n'est LU que pour auditer les protocoles.

2. REPARTIR DES SOURCES BRUTES
   donnees.csv, preds_test.csv, fixtures.csv, journal.csv, le cache prive,
   le journal prive, un projet prive/etat/dataset.csv, APIs en direct.
   Jamais d'un resultat intermediaire deja calcule par l'auteur.

3. EVALUER LA POPULATION COMPLETE
   Pas seulement les lignes retenues par le filtre de l'auteur. Le cache entier :
<!-- ligne retirée : projet privé et chiffre privé -->

4. QUANTIFIER L'INCERTITUDE
   IC 95 % bootstrap (4000 reechantillonnages), graine fixee, methode documentee.
   Un ROI sans IC n'est pas un resultat.

5. CORRIGER POUR TESTS MULTIPLES
   Declarer m = nombre TOTAL de tests, y compris non publies.
   Benjamini-Hochberg (FDR 5 %) et Bonferroni (FWER).
   Distinguer EXECUTABLE de NON EXECUTABLE.

6. CHERCHER LE CONTRE-EXEMPLE
   Algebrique : 1 seul bookmaker -> EV = 1/O - 1 = -marge < 0 TOUJOURS (1,90/1,90, marge
<!-- ligne retirée : projet privé et chiffre privé -->
<!-- ligne retirée : projet privé et chiffre privé -->

7. CROISER AU MOINS 2 SOURCES INDEPENDANTES
   Fixtures confirmees par ESPN + TheSportsDB + un telechargement live de
   un projet prive.co.uk, identique AU MD5 PRES (9 affiches sur 9).
   Verifier aussi les sources elles-memes : codes HTTP REELS (18 sources sur 32 en 200).

8. AUDITER LE PROTOCOLE, PAS SEULEMENT LES CHIFFRES
   Fuite ? walk-forward strict (train = date < bloc courant). Hyperparametres choisis
   avant les saisons de test. Cotes exclues de l'estimation. Placebo. Permutation.
   Attention a la selection de seuil SUR le jeu de test (argmax(ROI) sur le test set).

9. LISTER LES ANGLES MORTS ET CE QU'ON N'A PAS PU VERIFIER
   8 angles morts listes dans la session. Et « pas de cotes de cloture Bet365 pour
   l'Over/Under, seulement le Max » : reserve honnete, ecrite noir sur blanc.

10. PROUVER QUE LE PC N'A PAS ETE TOUCHE
    Seules des lectures et l'ecriture du rapport .md. Les scripts crees et executes
    SUR LE SERVEUR, pousses en base64 (aucun fichier temporaire sur le poste).
```

## Les verdicts autorises

| Verdict | Sens | Exemple reel |
|---|---|---|
| **CONFIRME** | Recalcule, identique au chiffre annonce | Modele **0,9927** vs marche **0,9634**, ecart **+0,0269** IC [+0,0224 ; +0,0316]. Le bootstrap de l'agent est **correct** |
| **CONFIRME, et PIRE** | Recalcule, l'ecart reel est plus grand | Marche encore meilleur : **+0,0293** vs cloture, **+0,0364** si on note les cotes brutes non devigorees (0,956720) |
| **PARTIELLEMENT VRAI** | Chiffres exacts, demonstration fausse | Affirmation 1 : « chiffres CONFIRMES a la decimale, demonstration REFUTEE, these de fond CONFIRMEE et renforcee » |
| **REFUTE** | Mesure contraire, preuve produite | « Les fixtures sont fausses » -> **9/9 confirmees** par ESPN. « CLV non mesurable » -> colonnes de cloture sur **12 709 matchs** |
| **NON CONCLUANT** | Mesure faite, ambiguë | ROI decroissant avec le seuil : tendance globale massive (Spearman **-0,983**) mais la plage 0-5 % est **plate et non monotone** (bruit) |
<!-- ligne retirée : projet privé et chiffre privé -->

**« C'est faux » sans preuve n'est pas un verdict.** « Je n'ai pas pu verifier » en est un.

## Gabarit de rapport (a recopier)

```markdown
# une verification interne - Verification ADVERSARIALE des N affirmations

> Verificateur adversarial independant. Objectif : CASSER les affirmations, pas les confirmer.
> Tous les calculs executes sur le serveur <hote>, dans <dossier>
> (N scripts v00.py ... vNN.py, ecrits par moi, nice -n 10, LC_ALL=C).
> Aucun code de l'agent reutilise pour mesurer : je repars des sources brutes.
> Date serveur au moment des mesures : <...>
> Aucun pari reel. MODE_REEL jamais touche.

## AFFIRMATION k - <enonce>
**VERDICT :** <CONFIRME | PARTIELLEMENT VRAI | REFUTE | NON CONCLUANT | NON VERIFIABLE>
**METHODE :** ce que j'ai recalcule, avec quel code, sur quelles donnees brutes
**PREUVE :**
| Mesure | Affirme | Mon recalcul |
|---|---|---|
**Les attaques qui echouent :** <ce que j'ai essaye de casser et qui a tenu>
**SI REFUTE - la VRAIE valeur :** <...>

## SYNTHESE
### Les affirmations qui TOMBENT
### Les affirmations qui TIENNENT
### Ce que l'agent n'avait pas vu (angles morts)
### Ce que je n'ai pas pu verifier
### PREUVE QUE LE PC N'A PAS ETE TOUCHE
```

## Criteres de REJET d'un rapport de verification

Un rapport est **rejete** (et le travail recommence) si :

1. il contient le mot « conforme », « OK » ou « tout va bien » **sans liste d'attaques** ;
2. il liste **moins de 5 attaques tentees** ;
3. il ne produit **aucun defaut reel** et ne montre pas ses tentatives ;
4. il reutilise le code de l'auteur pour mesurer ;
5. il mesure sur les **donnees filtrees** de l'auteur au lieu des sources brutes ;
6. il annonce un ROI **sans intervalle de confiance** ;
7. il ne declare pas le **nombre total de tests** ;
8. il ne dit **pas** ce qu'il n'a **pas** pu verifier ;
9. il ne precise pas **ou** le calcul a tourne (et si le PC a ete touche) ;
10. il **confirme tout** : un verificateur qui ne trouve jamais rien n'a pas cherche.

## Ce que la contradiction a rapporte dans la session

- **3 affirmations sur 8** tombees ou affaiblies ;
- un **bug de CLV** (`04_value.py` l.45/l.49) qui mettait la CLV a 0 sur **675 des 1250**
  un projet prive et fabriquait le chiffre de tete « 0,66 % » (vraie CLV 1X2 : **+1,42 %**) ;
- un **filtre horaire manquant** : 1 ticket sur 9 enregistre a 16:15 sur un match
  commence a 14:30 ;
- une **selection de seuil sur le jeu de test** (`04_value.py` l.163-164) ;
- **l'affirmation de l'agent selon laquelle le marche etait mieux note que la realite**
  s'est revelee **fausse dans le bon sens** : le marche est **encore meilleur**.

Le contradicteur coute une passe de travail. Il a evite de publier des chiffres faux.