# Créer un GossipMenu et liés un Texte

### Tutoriel par @iThorgrim-Hub

Bonjour,

Aujourd'hui je vais vous apprendre à créer un Gossip Menu et à liés un texte autre que le célébre 'Greetings'.

### 1 - Outils

- Une base de donnée.
- Un SGBD (Navicat, SQLyog).

### 2 - GOSSIP_MENU_OPTIONS

Pour commencer on va créer un Gossip_Menu_Option.
[Direction le Wiki](https://trinitycore.atlassian.net/wiki/spaces/tc/pages/2130188/gossip+menu+option335) pour voir ce qu'il nous dis.

Ont peu voir pas mal d'informations, je vais donc tout décomposer..

- **MenuID** : *Le MenuID est une colonne qui permet d'attribuer un ID ou Entry à notre GossipMenu ce qui fait de lui qu'il est unique.*
- **OptionID** : *L'option ID permet d'avoir plusieurs options avec le même MenuID*
- **OptionIcon** : *Vous permet de changer l'icone (la Bulle), il suffis juste de mettre l'ID de l'icône que vous souhaitais(tout est sur le wiki)*
- **OptionText** : *Très très simple, cette colonne vous permet de mettre en place un texte pour votre gossip (conseil : mettez le en Anglais et mettez en place vos traduction dans GOSSIP_MENU_OPTION_LOCALE).*
- **OptionBroadcastTextID** : *Si vous souhaitez créer des textes un peu plus avancés (avec Emote etc.) vous pouvez entrer l'ID d'un BroadCast_Text (pour ce tutoriel nous ne l'utiliserons pas).*
- **OptionType** : *Vous permet d'entrer le type de Gossip, par exemple si c'est un GossipMenu pour mettre en place un vendeur(3) si c'est juste un dialogue(1)*
- **OptionNpcFlag** : *Il doit être égale au NPCFlag de votre NPC*
- **ActionMenuID** : *Vous permet de créer des Gossip à plusieurs options, en gros il fait référence à un autre MenuID, pour faire des suites de dialogues.*

Si vous ne faite pas de gros Gossip et que votre option doit juste "tourner en rond" vous mettez simplement le MenuID de votre menu actuel (complexe ?)
Exemple :

```sql
MenuID = 123456;

OptionID = 0,
OptionText = Tourner en Rond,
ActionMenuID = 123456;
```

Ici le code feras en sorte que si vous cliquez sur l'Option "Tourner en Rond" il reviendras sur lui même et ainsi il tourneras en rond ..

- **ActionPoiID** : *Permet de mettre en place des POI (Point Of Interest), ce sont les petits drapeau que vous pouvez voir sur la minimap (Comme pour les gardes par exemple).*
- **BoxCoded** : *Très simple, si vous entrer quelques choses, (Genre 123) le joueur devras connaitre le code (123) pour accéder à l'action derrière cette ligne*
- **BoxMoney** : *Vous permet d'entrer un montant en pièces de cuivre pour accéder à l'action derrière cette ligne.*
- **BoxText** : *Vous permet de mettre en place un texte, avertissement pour accèder à l'action derrière cette ligne.*
- **BoxBroadcastTextID** : *Exactement la même chose que OptionBroadcastTextID mais pour la BoxText.*

Code Exemple de gossip :

```sql
INSERT INTO `gossip_menu_option` (MenuID, OptionID, OptionIcon, OptionText, OptionBroadcastTextID, OptionType, OptionNpcFlag, ActionMenuID, ActionPoiID, BoxCoded, BoxMoney, BoxText, BoxBroadcastTextID) VALUES
(62000, 0, 0, 'Menu 62000 - 0 (Boucle sur lui même)', 0, 1, 1, 62000, 0, 0, 0, '', 0),
(62000, 1, 0, 'Menu 62000 - 1 (Boucle sur lui même + BoxCoded(1))', 0, 1, 1, 62000, 0, 1, 0, '', 0),
(62000, 2, 0, 'Menu 62000 - 2 (Boucle sur lui même + BoxMoney)', 0, 1, 1, 62000, 0, 0, 100000, 'Paye donc..', 0),
(62000, 3, 0, 'Menu 62000 - 3 (Boucle sur lui même + BoxText)', 0, 1, 1, 62000, 0, 0, 0, 'Super Texte de super Tutoriel sur super Gossip', 0);
```

### 3 - GOSSIP_MENU & NPC_TEXT

Afin d'avoir un peu de personnalités et pas faire un serveur avec des Gossip fade sans saveurs ont vas ajouter un texte.
Et pour ce faire ont vas passer par Gossip_Menu & Npc_Text.

Comme toujours direction le [Wiki de NPC_Text](https://trinitycore.atlassian.net/wiki/spaces/tc/pages/2130250/npc+text)
Bon j'ai pas vraiment d'expliquer plus que le Wiki de TC il est déjà très explicite.
(Je conseil toujours d'écrire en Anglais ici et de faire votre Traduction dans NPC_TEXT_LOCALE)

Pour l'exemple nous ne mettrons en place qu'un texte simple :

```sql
INSERT INTO `npc_text` (id, text0_0, text0_1) VALUES
(62000, 'Je suis un Homme et je vois le super Menu', 'Je suis une Femme et je vois le super Menu');
```

Et là du coup ont va lier notre Texte à notre Gossip_Menu_Option via [Gossip_Menu](https://trinitycore.atlassian.net/wiki/spaces/tc/pages/2130187/gossip+menu335).

Pareil je pense pas avoir besoin de vous expliquer les deux colonnes qu'il y a, TrinityCore est quand même pas mal explicite.
Donc très simple :

```sql
INSERT INTO `gossip_menu` (menuid, textid) VALUES
(62000, 62000);
```

Maintenant direction le jeu ou je vais spawn mon NPC pour tester mon Gossip.

![image](https://user-images.githubusercontent.com/65762554/82667684-a2b39080-9c38-11ea-9df5-08e5a43521a6.png)
![image](https://user-images.githubusercontent.com/65762554/82667686-a47d5400-9c38-11ea-9f12-e4e5d7623a23.png)
![image](https://user-images.githubusercontent.com/65762554/82667694-a7784480-9c38-11ea-8965-10ec2b79454c.png)
![image](https://user-images.githubusercontent.com/65762554/82667701-a9420800-9c38-11ea-9db7-526bed91a97b.png)

Voilà je vous dis à la prochaine pour un prochain tuto !
