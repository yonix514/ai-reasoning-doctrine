<!--
  Document publie a partir d'un systeme de travail prive.
  Les etudes de cas portant sur des projets prives, leurs chiffres et
  les chemins internes ont ete retirees ou rendues generiques. Les cas
  de montage video, de scripting VEGAS Pro et d'ffmpeg sont conserves :
  ce sont les travaux de l'auteur.
-->

# the harness-api-inconnue - explorer une API par reflexion

## Le principe

**Ne jamais deviner une signature d API. Ne jamais croire la documentation avant de l avoir
verifiee sur l assembly reellement installe.** La documentation en ligne decrit une autre
version ; les noms de plugins varient d une machine a l autre.

Trois methodes, dans cet ordre.

## Methode 1 - reflexion sur la DLL (la plus rapide)

Sur le poste, la DLL locale est la **source de verite** :

```
C:\Program Files\VEGAS\VEGAS Pro 20.0\ScriptPortal.Vegas.dll
```

Sonde reelle utilisee dans l atelier (`default-workspace\tools\inspect_api.ps1`) :

```powershell
$dll = "C:\Program Files\VEGAS\VEGAS Pro 20.0\ScriptPortal.Vegas.dll"
$asm = [System.Reflection.Assembly]::LoadFrom($dll)

function Show-T($name, $pat) {
    $t = $asm.GetType($name)
    if (-not $t) { Write-Host "--- $name ABSENT"; return }
    Write-Host "--- $name"
    Write-Host "    interfaces : $((($t.GetInterfaces() | ForEach-Object { $_.Name }) -join ', '))"
    $t.GetMethods([System.Reflection.BindingFlags]'Public,Instance,DeclaredOnly') |
        Where-Object { -not $_.IsSpecialName -and $_.Name -match $pat } |
        ForEach-Object {
            $ps = ($_.GetParameters() | ForEach-Object { "$($_.ParameterType.Name) $($_.Name)" }) -join ', '
            Write-Host "    $($_.ReturnType.Name) $($_.Name)($ps)"
        }
}

Show-T 'ScriptPortal.Vegas.Project' 'Track|Marker|Region|Media'
Show-T 'ScriptPortal.Vegas.Timecode' '.'
$p = $asm.GetType('ScriptPortal.Vegas.Project')
$p.GetProperties([System.Reflection.BindingFlags]'Public,Instance') |
    Where-Object { $_.Name -match 'Track|Media|Marker|Region' } |
    ForEach-Object { Write-Host "    $($_.PropertyType.Name) $($_.Name)" }
```

**Points qui comptent :**
- `LoadFrom` sur le **chemin absolu** de la DLL reellement installee ;
- `DeclaredOnly` pour **ne pas** heriter des methodes de `Object` et noyer la sortie ;
- filtrer par expression reguliere sur le nom, sinon la sortie est illisible ;
- `GetProperties` **et** `GetMethods` : les deux, une propriete n est pas une methode.

**Pourquoi une sonde sur le poste ici ?** Parce que la DLL n existe **que** sur le poste.
C est une **lecture** de quelques centaines de millisecondes, pas un calcul : l exception
est bornée et assumee (voir `the harness-serveur-uniquement`). Aucune boucle longue, aucun
traitement de donnees.

## Format de vidage a produire (et a conserver)

Le resultat se conserve en clair dans l atelier : `vegas\_api_dump.txt`, `vegas\_api_motion.txt`.
Format **stable**, un type par bloc :

```
### ScriptPortal.Vegas.VideoMotion  (base=Object) enum=False
   Property Boolean ScaleToFill  {get;set;}
   Property ScriptPortal.Vegas.VideoMotionKeyframes Keyframes  {get;}
### ScriptPortal.Vegas.VideoMotionKeyframe  (base=Object) enum=False
   Constructor Void .ctor(ScriptPortal.Vegas.Project, ScriptPortal.Vegas.Timecode)
   Constructor Void .ctor(ScriptPortal.Vegas.Timecode)
   Property ScriptPortal.Vegas.Timecode Position  {get;set;}
   Property ScriptPortal.Vegas.VideoMotionBounds Bounds  {get;set;}
   Property ScriptPortal.Vegas.VideoKeyframeType Type  {get;set;}
### ScriptPortal.Vegas.VideoMotionBounds  (base=Object) enum=False
   Constructor Void .ctor(VideoMotionVertex, VideoMotionVertex, VideoMotionVertex, VideoMotionVertex)
   Property VideoMotionVertex TopLeft  {get;set;}
   ... BottomRight, BottomLeft
```

Trois enseignements tires de ce seul vidage :
- `Keyframes` a un **getter seul** : on ne remplace pas la collection, on y **ajoute** ;
- `VideoMotionKeyframe` a un constructeur qui exige **(Project, Timecode)** ;
- `VideoMotionBounds` se construit avec **4 sommets** dans l ordre
  **haut-gauche, haut-droit, bas-droit, bas-gauche** - l ordre n est pas devinable.

## Methode 2 - enumerer l arbre au runtime

Les **noms exacts** des transitions et effets dependent de la machine et des plugins
installes. On ne les devine pas : on les **liste** depuis Vegas lui-meme.

Sonde reelle (`vegas\ProbeVegasAPI.cs`), parcours recursif d un `PlugInNode` :

```csharp
void Walk(StringBuilder sb, PlugInNode node, int depth) {
    if (depth > MAX_DEPTH) return;
    string name = "?";
    try { name = node.Name; } catch { name = "<nom illisible>"; }
    if (node.IsContainer) sb.AppendLine(indent + "[dossier] " + name);
    else                 sb.AppendLine(indent + "- " + name + "   (id: " + node.UniqueID + ")");
    int count = node.Count;
    for (int i = 0; i < count; i++) {
        PlugInNode child = node.GetChild(i);
        if (child != null) Walk(sb, child, depth + 1);
    }
}
// racines : vegas.Transitions, vegas.VideoFX, vegas.AudioFX, vegas.Generators, vegas.VideoCompositeFX
```

**Chaque lecture de propriete est entouree d un `try/catch`** : un noeud peut lever une
exception (plugin casse). Une enumeration qui s arrete au premier plugin fautif ne liste rien.

Rendu : une `MessageBox` (script lance depuis l interface) ou une ecriture dans un `.txt`
a cote du script. **Le resultat doit etre lisible en dehors de Vegas.**

## Methode 3 - la sonde minimale

Avant d ecrire le script complet, on ecrit le **plus petit script qui teste l hypothese**
sur **un seul** objet : un evenement, une image cle, un parametre. Puis on **lit le
resultat en retour**. C est le principe 5 de `the harness-doctrine` applique aux API.

Script de **relecture** reel (`vegas\scripts\lire_cadrage.cs`) - il n ecrit rien, il
**relit ce que le projet contient vraiment** :

```
num | image | src W x H | Larg | Haut | Cx | Cy | cles | ScaleToFill
```

Un script qui ecrit **doit** etre accompagne d un script qui **relit**. Sans relecture,
« le script a tourne » n est qu une declaration (principe 9).

## Les pieges verifies

### 1. Ajouter d abord, regler ensuite

```
FAUX  :  k = new VideoMotionKeyframe(project, ve.Start);
         k.Bounds = new VideoMotionBounds(...);   // regle AVANT
         motion.Keyframes.Add(k);                 // Vegas ECRASE la valeur
         -> 72 images sur 288 reussies

JUSTE :  k = new VideoMotionKeyframe(project, ve.Start);
         motion.Keyframes.Add(k);                 // l objet EXISTE dans le conteneur
         k.Bounds = new VideoMotionBounds(...);   // ensuite on regle
         -> 288 sur 288
```

**Regle generale.** Vegas calcule une valeur par defaut **au moment de l insertion** et
ecrase ce qu on avait mis avant. Un objet doit **exister dans son conteneur** avant d etre
configure. Meme famille que `ffmpeg acrossfade` avec `overlap=1` par defaut (28,29 s perdues
sur 943 jonctions) : **le defaut silencieux**.

### 2. Les unites reelles ne sont pas les unites supposees

```
Timecode.Nanos n est PAS des nanosecondes :     ms = Nanos / 10000.0
new Timecode(4000.0) -> ToMilliseconds() = 4000   (le constructeur prend des MILLISECONDES)
```

Un nom de propriete ne dit pas l unite. **Toujours verifier par un aller-retour** :
construire un objet avec une valeur connue, relire la valeur convertie, comparer.

### 3. Les coordonnees sont en pixels SOURCE

`VideoMotionBounds` s exprime en **pixels source de l image PRE-REDIMENSIONNEE a 1920 de
large** : facteur `f = W_src / 1920`. Ce ne sont **ni** des pixels de sortie, **ni** des
coordonnees normalisees 0-1.

Formule de remplissage du cadre (source `W x H`, ratio de sortie `R = W_sortie / H_sortie`) :

```
si W/H > R  ->  fenetre = (H x R) x H        # source plus large
si W/H < R  ->  fenetre = W x (W/R)          # source plus haute
centre = (W/2, H/2)
```

### 4. Un nom trompeur : `ScaleToFill`

`VideoMotion.ScaleToFill` n est **pas** l option « Faire correspondre le format de sortie ».
C est un **booleen local** : « cette partie rognee est-elle AGRANDIE pour remplir le cadre ? »

```
Bounds       = QUELLE PARTIE de l image on voit
ScaleToFill  = si cette partie est AGRANDIE pour remplir le cadre
LES DEUX SONT NECESSAIRES. Sans ScaleToFill = true, la partie rognee reste a sa taille
-> BANDES NOIRES (symptome observe : l apercu montrait toujours des bandes noires).
```

Methode directe de verification : `[System.Reflection.BindingFlags]'Public,Instance'` sur
`VideoMotion` -> `Property Boolean ScaleToFill {get;set;}`. La reflexion tranche un debat
de vocabulaire en une ligne.

### 5. Autres pieges verifies

```
TrackEvent.Split(t) prend un DECALAGE depuis le debut de l evenement, PAS une position absolue.
Un objet Effect ne peut appartenir qu a UN SEUL evenement.
StringFormatFlags::NoWrap ROGNE le haut des glyphes si la boite de dessin est plus petite
    que la police (un « O » devient un « U »).
Un .cs SANS son .dll ne se lance pas : il faut compiler.
```

## Procedure complete

```
1. LOCALISER l assembly reellement installe (chemin absolu).
2. VIDER les signatures par reflexion (Methode 1) -> fichier .txt conserve dans l atelier.
3. LISTER les noms d objets au runtime (Methode 2) si l API expose un arbre de plugins.
4. ECRIRE une sonde minimale sur UN objet, puis RELIRE le resultat (Methode 3).
5. COMPILER avant d executer (csc.exe) : une erreur de compilation ne coute rien,
   une erreur dans Vegas coute un projet.
6. SAUVEGARDER LE PROJET avant tout script qui ecrit.
7. APPLIQUER a l echelle.
8. RELIRE pour verifier : le script qui ecrit et le script qui relit sont deux scripts.
9. ECRIRE les pieges trouves dans la competence the harness-vegas-scripting.
```

Voir `the harness-vegas-scripting` pour la compilation, les dossiers de scripts reellement lus
et le dialogue d installation administrateur.