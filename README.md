# ai-reasoning-doctrine

**Comment penser avant d'affirmer, quand on fait travailler des agents IA.**
14 principes, 14 « lunettes » d'expert, 8 types d'erreurs, et un protocole de
contradiction obligatoire. Chaque principe est né d'une **erreur réelle et
mesurée**, pas d'une bonne intention.

[English version →](README.en.md)

---

## Le problème

Un agent IA qui analyse produit des conclusions **très convaincantes**, et
souvent fausses. Pas par malveillance : par construction.

Il mesure son propre artefact et croit mesurer le monde. Il transforme une
absence de résultat en preuve d'absence. Il annonce un chiffre sans dire sur
quelle population il porte. Il déclare qu'une tâche a réussi parce qu'elle a
tourné, sans jamais relire ce qu'elle a produit.

Ces erreurs ne se corrigent pas en demandant à l'agent « d'être plus
rigoureux ». Elles se corrigent par des **règles** et par un **contradicteur
dont le travail est de vous contredire**.

Ce dépôt est la codification de ces règles, écrite après les avoir vues
échouer.

---

## Ce qu'il y a dedans

| Document | Contenu |
| --- | --- |
| **[14-principes.md](doctrine/14-principes.md)** | Les 14 principes. Chacun : l'erreur réelle, la mesure, ce que le contradicteur a corrigé, la règle. |
| **[les-14-lunettes.md](doctrine/les-14-lunettes.md)** | 14 perspectives d'expert (investisseur, dev senior, avocat, sécurité, actuaire…) + la méthode du **Conseil des 14** : choisir 5 lunettes, provoquer leur désaccord, arbitrer, décider. |
| **[8-types-erreurs.md](doctrine/8-types-erreurs.md)** | La taxonomie des 8 familles d'erreurs et le protocole du contradicteur obligatoire. |
| **[explorer-une-api.md](doctrine/explorer-une-api.md)** | Explorer une API non documentée par réflexion, au lieu de deviner les noms et les unités. |
| **[examples/](examples/)** | Un exemple appliqué au montage vidéo. |

### Les 14 principes

1. **Mesurer avant d'affirmer.**
2. Une absence de résultat n'est pas une preuve d'absence.
3. Vérifier ce qu'on a avant de construire dessus.
4. Tester son propre outil avant de lui faire confiance.
5. Distinguer « faux » de « non vérifiable ».
6. La correction pour tests multiples.
7. Chercher la fuite de données.
8. Ne jamais promettre au-delà du mesuré.
9. Vérifier le **fichier produit** par une tâche, pas sa **déclaration**.
10. Choisir la bonne méthode selon la source.
11. **Ajouter d'abord, régler ensuite** (APIs).
12. Le contradicteur obligatoire.
13. Ce qui compte n'est pas d'avoir raison, c'est que le travail soit juste.
14. Le serveur calcule, le poste contrôle.

---

## Les trois idées qui portent le reste

**1. Un script qui écrit doit être accompagné d'un script qui relit.**
« Le script a tourné » est une déclaration, pas une preuve. Le cas mesuré
derrière le principe 9 : deux tâches planifiées étaient déclarées « actives »
alors qu'elles **n'avaient jamais produit un seul fichier**. Un travail sans
journal de sortie est **indiscernable d'un travail mort**.

**2. Le contradicteur ne peut pas conclure « tout va bien » sans avoir essayé.**
Liste au moins 5 attaques tentées, et produis au moins 1 défaut réel — ou
démontre pourquoi aucun n'est trouvable **en montrant tes tentatives**. Un
rapport qui dit « conforme » sans lister ses attaques est rejeté.

**3. Les APIs ont des valeurs par défaut silencieuses.**
Régler une propriété **avant** d'insérer l'objet dans son conteneur est ignoré :
le conteneur écrase la valeur à l'insertion. Le code est valide, aucune
exception n'est levée, et le résultat est faux. Voir le principe 11.

---

## Ce que j'ai mesuré

Les cas ci-dessous viennent de mon propre travail de montage vidéo et de
scripting. Ce sont les seuls cas que je publie, parce que ce sont les seuls qui
sont entièrement à moi.

| Mesure | Méthode | Résultat |
| --- | --- | --- |
| Images clés posées par une API, ordre incorrect | comptage des événements animés après exécution | **72 / 288** |
| Mêmes images, ordre correct | idem | **288 / 288** |
| `Timecode` : unité réelle du constructeur | aller-retour construire/relire | **millisecondes** (`new Timecode(4000.0).ToMilliseconds() == 4000`) |
| Assemblage de 4 segments de 2,000 s, `acrossfade` défaut | `ffprobe` | **6,500000 s** au lieu de 8,000000 s |
| Même assemblage avec `overlap=0` | `ffprobe` | **8,000000 s** — aucune perte |
| Assemblage avec des taux d'échantillonnage mélangés | `ffprobe` | **2,544218 s** au lieu de 2,500000 s |
| Chaîne de 30 `acrossfade` à 30 ms sur 31 sources de 1 s | `ffprobe`, 3 exécutions | **1,36 s / 1,83 s / 0,82 s** au lieu de 30,100 s attendues |

### Ce que j'ai retiré avant de publier

Ce document vient d'un système de travail **privé**, et il citait ce système :
des projets, leurs chiffres, des chemins de fichiers internes. **Tout cela a
été retiré ou rendu générique.** Quand une section entière reposait sur un
projet privé, la section est marquée comme retirée et **aucun chiffre de
remplacement n'a été inventé** — un chiffre inventé serait exactement l'erreur
que ce document dénonce.

### Une correction que je dois publier

Une version antérieure de mes notes affirmait que le défaut `overlap=1` de
`acrossfade` (ffmpeg) **« raccourcit chaque jonction de 30 ms »** comme un
défaut caché, et qu'`overlap=0` et `overlap=1` donnaient la **même** durée.

**Les deux affirmations étaient fausses.** La mesure donne :

- le raccourcissement vaut **exactement `d`**, la durée du fondu, par jonction —
  ce n'est pas un 30 ms mystérieux ;
- `overlap=0` et `overlap=1` ne sont **pas** équivalents : `overlap=0` conserve
  la durée totale, `overlap=1` retire `d` par jonction.

C'est le principe 8 appliqué à moi-même : **ne jamais promettre au-delà du
mesuré**, y compris quand on s'est déjà engagé publiquement.

---

## À qui ça sert

- À ceux qui **construisent des agents** et veulent qu'ils arrêtent d'affirmer
  sans mesurer.
- À ceux qui **consomment les rapports d'un agent** et doivent décider s'ils
  peuvent y croire.
- À ceux qui doivent **trancher sans être spécialiste de tout** : la partie
  « lunettes » est faite pour ça.

Ce n'est ni un cadre théorique ni une méthodologie de consulting. C'est une
liste de contrôle, née d'erreurs, avec les chiffres qui les ont attrapées.

---

## Installer / utiliser

Il n'y a rien à installer : ce sont des documents.

```bash
git clone <ce depot>
cd ai-reasoning-doctrine
```

**Pour s'en servir comme instruction d'agent**, collez `doctrine/14-principes.md`
dans le contexte de l'agent comme document de référence, avant la tâche.

**Pour l'utiliser en revue**, `doctrine/8-types-erreurs.md` contient un gabarit
de rapport de vérification à recopier.

## Contribuer

Voir [CONTRIBUTING.md](CONTRIBUTING.md). Le critère est strict : une
contribution n'est acceptée que si elle apporte **une erreur réelle et sa
mesure**. Une affirmation sans mesure est refusée.

## Licence

[MIT](LICENSE).
