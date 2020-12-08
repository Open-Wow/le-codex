# Les Conditions

### Tutoriel par @iThorgrim-Hub

Bonjour à toi jeune développeur !
Je pense que si tu es ici, soit tu ne connais rien aux Conditions soit tu en connais un petit peu mais en tout les cas tu veux en savoir plus!

Pour ce tutoriel il te faudras un serveur fonctionne sous TrinityCore ou AzerothCore ainsi qu'un accès à la base de donnée.
Personnellement j'utilise un AzerothCore et l'outil (SGDB) SqlYog Community.

### Kessecé ?

D'après TrinityCore :
![image](https://user-images.githubusercontent.com/65762554/82664721-9b898400-9c32-11ea-9170-eaec1293273d.png)

D'après iThorgrim _(c'est moi)_:
La condition est un système permettant de mettre en place l'ajout de pré-requis (de conditions) pour accéder à un contenu (Loot, Quête, Gossip etc.)
Cela permet, par exemple, de créer des Menu dédiés à une classe ou aux joueurs à conditions d'avoir réussis une quête donnés etc.

Les conditions fonctionne aussi à l'inverse vous pouvez cacher des choses une fois une quête effectués.

**Exemple de Condition 'normal' :**
_Si le joueur termine la quête 283, il pourras voir le contenu du vendeur à l'entry 85._

**Exemple de Condition 'inverse' :**
_Si le joueur termine la quête 283, il ne pourras plus voir le contenu du vendeur à l'entry 85._

Je pense que vous avez compris le concept, l'idée de l'utilité d'une telle table, surtout en SQL.
Le SQL étant un langage qui nous limites de base à quelques modifications, mais via ses nombreux alliés comme la table Conditions le SQL deviens extremement interessant!

#### Sommaire :

- La source de tout .. le Wiki.
- Exemple : Disparition de Gossip

### La source de tout .. le Wiki

Il y a pas de secret, tout s'y trouve et pourtant peu de gens y font attention .. je parle bien sûr des Wiki.
Ils doivent devenir plus proche que vos parents et plus important que respirer(sisi) les Wiki sont votre nouvel/le {Insérer revue religieuse}.

Bon .. par contre ils sont sûrement comme votre première copine .. moche et des fois tu comprends rien.
Donc le Wiki qui nous interesse c'est celui de [TrinityCore sur les Conditions](https://trinitycore.atlassian.net/wiki/spaces/tc/pages/2130002/conditions).

Le Wiki se divise en plusieurs partie.

- Structure

- SourceTypeOrReferenceId

- SourceGroup

- SourceEntry

- ElseGroup

- ConditionTypeOrReference

- ConditionTarget

- ConditionValue1 & Value2 & Value3

- NegativeCondition

- ErrorType

- ErrorTextId

- ScriptName

- Comment

Cela fait beaucoup de choses à expliquer, mais en même temps .. après ça seras plus simple à comprendre.

**Structure** : Structure de la table, présente la façon dont elle est ordonnée ainsi que le type de donnée à entrer dans les cellules correspond à chaque colonne.

**SourceTypeOrReferenceId** : Un peu plus hardu à comprendre .. C'est un tableau qui vous présente les données possible prise en compte par l'Emulateur, chaque Emulateur peu avoir ajoutés, modifier, supprimés des SourceType. Mais grossièrement c'est toujours les mêmes.

Les ID sont ce qui  nous interesse, ce sont eux qui vont nous dire qu'elle source, qu'est ce que l'ont cherche à faire apparaitre ou disparaitre.
Et pour mieux comprendre ce que l'ont doit mettre, sur la droite d'ID, vous avez quelques informations qui nous permette de savoir ce que l'ont s'apprette à faire.
Il est aussi dit que si nous mettons une valeur négative donc avec un - avant votre ID cela fait appelle à une reference .. ont verras ça un peu plus tard.

**SourceGroup** : Permet d'entrer un ID, l'id correspondras à l'Entry du Template de ce nous souhaitons modifier, sur lequel nous souhaitons appliquer une condition _(Entry du NPC_Vendor, Loot_Template, Gossip_Menu_Option etc.)_.

**SourceEntry** : Toujours pareil on entre un ID mais cette fois ci il correspondras lui .. à l'OBJET concernés, son ID.
Par exemple : si nous souhaitons modifier un GossipMenuOption et lui appliquer une condition.
Je mettrais en SourceGroup l'entry du Gossip_Menu_Option MenuID(63) et je mettrais l'ID de la ligne qui m'interesse vraiment OptionID(1).

**ElseGroup** : Très simple cela vous permet de créer des groupes de Conditions, il vous faudras donc le même

**SourceTypeOrReferenceId & SourceGroup  & SourceEntry** : Cela permet de créer des groupes du type _OR ou AND_.

- **Exemple AND**: 
  
  - Je souhaite voir le Gossip_Menu_Option MenuID(63) et l'OptionID(1) si je tue le Mob 53. _AND (ET)_ si je tue le Mob 45. Il partagerons donc tout les deux le ElseGroup 1.

- **Exemple OR**:
  
  - Je souhaite voir le Gossip_Menu_Option MenuID(63) et l'OptionID(1) si je tue le Mob 53. _OR(OU)_ si je tue le Mob 45.

**_Il ne partagerons donc pas tout les deux le même ElseGroup_**

La première condition (Mob 53) devras avoir le ElseGroup 1.
La deuxième condition (Mob 45) devras avoir le ElseGroup 2.

**ConditionTypeOrReference** : Le vif du sujet .. Tout simplement notre condition en elle même, sous qu'elle condition je vais voir mon Gossip_Menu_Option MenuID(63) ?
Le tableau fonctionne exactement pareil que **SourceTypeOrReferenceId** vous avez une colonne ID pour voir l'ID à entrer, et vous avez à gauche le nom pour comprendre ce que vous vous apprêtez à faire.

**ConditionTarget** : Il sagit de la cible, elle n'est pas toujours demandés mais peut être requise, retenez simplement que _1 = le Joueurs; 2 = L'objet_ du **SourceTypeOrReferenceId**. 
D'ailleurs sur le Wiki à la ligne **SourceTypeOrReferenceId** vous pouvez voir les différentes **ConditionTarget**.

**ConditionValue1 & Value2 & Value3** : C'est tout simplement la valeur à entrer. Si vous souhaitez que le joueur tue 58 fois le mob 45, déjà c'est pas très sympas, c'est ici que cela se passe.

**NegativeCondition** : Colonne très utile, qui permet de faire des choses plutôt "dynamique". C'est un **Booléan (0 ou 1)**. 

Le 1 peu se traduire par un *TANT QUE*
Si vous souhaitez dire que *TANT QUE* le joueurs n'as pas tués 58 fois le mob 45 il peu voir le voir le Gossip_Menu_Option MenuID(63) et l'OptionID (1) et bien vous écrivez votre condition normalement puis à la fin vous mettez tout simple un petit 1 ici.

**ErrorType & ErrorTextId** : Aucune documentation dessus et je ne m'en suis jamais servis.

TrinityCore dis : 
![image](https://user-images.githubusercontent.com/65762554/82665181-92e57d80-9c33-11ea-85e2-388cfe667871.png)

**ScriptName** : Très simple si vous avez un Script CPP à lier à votre condition vous venez le dire ici.

**Comment** : La partie surement la plus importante.. Le commentaire. S'il vous plaît merci, de commenter correctement.
Exemple à ne pas faire : Gossip 12.
Exemple à suivre (à la limite) : Si le joueur tue 58 fois la creature 45 alors il peu voir le gossip 12.

La description du Wiki et des colonnes se termine ici, normalement tu as tout pour créer des Conditions digne de ce nom.

### Disparition de Gossip.

```sql
SET
@SourceTypeOrReferenceId := 15,
@SourceGroup := 435,
@SourceEntry := 1,
@ConditionTypeOrReference := 2,
@ConditionTarget := 0,
@ConditionValue1 := 6948,
@ConditionValue2 := 1,
@ConditionValue3 := 1,
@NegativeCondition := 1,
@Comment := 'Tant que le joueur a sa pierre de foyer il ne peu voir la ligne 1 du gossip 6948';

INSERT INTO `conditions` VALUES(@SourceTypeOrReferenceId,@SourceGroup,@SourceEntry,'0','0',@ConditionTypeOrReference,@ConditionTarget,@ConditionValue1,@ConditionValue2,@ConditionValue3,@NegativeCondition,'0','0','',@Comment);
```

Bon du coup dans cet exemple, j'ai mis en place une condition negative ou inverse qui dit que tant que j'ai ma [Pierre de Foyer] dans mon Inventaire ou dans ma banque je ne peu pas voir la ligne 1 du Gossip 435.

![image](https://user-images.githubusercontent.com/65762554/82665257-b6a8c380-9c33-11ea-9120-d668ddd692af.png)

**Du coup si je détruit ma [Pierre de Foyer].**
![image](https://user-images.githubusercontent.com/65762554/82665278-be686800-9c33-11ea-982b-5be2c77bb907.png)
