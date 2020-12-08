# SmartAI : Téléporteur

### Tutoriel par @iThorgrim-Hub

### 1 - Intro

Aujourd'hui dans ce tutoriel je vais vous apprendre à créer un téléporteur en SQL donc on va voir :

- creature_template
  - gossip_menu_option
  - gossip_menu
  - npc_text
  - smart_scripts

### 2 - CREATURE_TEMPLATE

**(Exécutez bien le code dans l'ordre des chapitres, c'est très important, surtout pour le Chapitre 5).**

Pour commencer va nous falloir une créature, pas la peine de remplir toutes les colonnes certains sont par défaut à 0 et ça tombe bien on a pas besoin de tout.

```sql
SET
    @Entry := (SELECT MAX(entry)+1 FROM creature_template),
    @Model := , -- A toi de mettre le model de ta créature
    @Npcflag := 1,
    @Faction := 35,
    @UnitClass := 1,
    @Level := 80, -- A toi de mettre le niveau de ta créature
    @GossipMenuId := (SELECT MAX(menuid)+1 FROM gossip_menu_options),
    @Name := ''; -- A toi de mettre ce que tu souhaite comme nom

INSERT INTO `creature_template` (entry, modelid1, npcflag, faction, unit_class, minlevel, maxlevel, gossip_menu_id, name) VALUES
    (@Entry, @Model, @Npcflag, @Faction, @UnitClass, @Level, @Level, @GossipMenuId, @Name);
```

Comme tu peux le voir pour **@Entry** et **@GossipMenuId** ont fait une requête SQL pour aller sélectionner le dernier et rajouter +1, plus rapide et moins chiant que de chercher le dernier ID.

### 3 - GOSSIP_MENU_OPTION

C'est partit pour le gossip_menu_option, en gros le petit menu quand tu parles a ton PNJ.

```sql
SET
    @MenuID = (Select MAX(menuid) FROM gossip_menu_option);

-- EXEMPLE DE MENU AVEC AUCUNE SOUS CATEGORIES
INSERT INTO `gossip_menu_option` (menuid, optionid, optionicon, optiontext, optiontype, optionnpcflag, actionmenuid) VALUES
    (@MenuID, 0, 0, 'Emplacement I', 1, 1, @MenuID),
    (@MenuID, 1, 0, 'Emplacement II', 1, 1, @MenuID),
    (@MenuID, 2, 0, 'Emplacement III', 1, 1, @MenuID);

-- EXEMPLE DE MENU AVEC SOUS CATEGORIES
INSERT INTO `gossip_menu_option` (menuid, optionid, optionicon, optiontext, optiontype, optionnpcflag, actionmenuid) VALUES
    (@MenuID, 0, 0, 'Emplacement I', 1, 1, @MenuID),
    (@MenuID, 1, 0, 'Emplacement II', 1, 1, @MenuID+1),
        (@MenuID+1, 0, 0, 'Emplacement I', 1, 1, @MenuID+1),
        (@MenuID+1, 1, 0, 'Emplacement II', 1, 1, @MenuID+1),
        (@MenuID+1, 2, 0, 'Emplacement III', 1, 1, @MenuID+1),
        (@MenuID+1, 50, 0, '{bouton retour}', 1, 1, @MenuID),
    (@MenuID, 2, 0, 'Emplacement III', 1, 1, @MenuID);
```

Alors un peu d'explication, toujours la même chose que pour l'entry de creature_template, le MenuId va allez cherche le dernier tout seul.
Pour ce qui est des sous-catégorie, je pense pas a avoir besoin de vous expliquer juste à lire le code et on comprend directement, c'est presque logique.

Pour les lignes sans sous-catégories je met deux fois @MenuID :
![image](https://user-images.githubusercontent.com/65762554/82665800-d5f42080-9c34-11ea-83a2-48efacfc6aef.png)

En gros ce que je fais c'est que si tu clique sur '_Emplacement_ I' il va te renvoyer vers le menu principal: 

> Menu principal > affiche Emplacement I envoie > Menu principal

### 4 - GOSSIP_MENU & NPC_TEXT

Bon là on va faire un texte custom à afficher dans le gossip_menu parce que 'Greetings $n' c'est un peu de la m*rde.
Donc la on va lui donner un texte custom à afficher et on va lier ce texte à la page en gros.

```sql
SET
    @ID := (SELECT MAX(id)+1 FROM creature_template);

INSERT INTO `npc_text` (id, text0_0) VALUES
    (@ID, 'Ton Texte');
    -- Si tu souhaite rajouter plusieurs texte pour plusieurs pages tu copie colle la ligne et tu met (@ID+1) oublie pas de remplacer le ";" par un "," faut surtout pas oublier que le ";" signifie "FIN DE CODE"

SET
    @MenuID := (SELECT MAX(menuid) FROM gossip_menu_option);


INSERT INTO `gossip_menu` (menuid, textid) VALUES
    (@ID, @MenuID);
    -- Si tu as créé plusieurs textes et donc plusieurs "pages/catégorie" alors en gros ça dit "Affiche Texte(@ID) sur la Page(@MenuID)"
```

Donc là tu as ton texte, tu as mis ton texte sur les/la page(s), c'est partit pour la fin.

### 5 - SMART_SCRIPT

C'est partit pour le plus chiant, alors là je compte sur toi pour augmenter ta concentration à 687%, j'ai besoin de toutes tes facultés mentale.

```sql
  SET 
    @Entry := (SELECT MAX(entry) FROM creature_template),
    @GossipMenuId := (SELECT MAX(menuid) FROM gossip_menu_option);

  UPDATE `creature_template` SET ainame = 'SmartAI' WHERE entry = @ENTRY;

  INSERT INTO `smart_scripts` (entryorguid, id, event_type, event_chance, event_param1, event_param2, action_type, action_param1, target_type, target_x, target_y, target_z, target_o, COMMENT)VALUES
    (@Entry, 0, 62, 100, @GossipMenuId, 0, 62, M, 7, X, Y, Z, O, 'TP ');
```

Go go go pour les explications bien relous :

- **@Entry** c'est l'entry de ton PNJ
- **0** c'est l'id faut ajouter +1 à chaque fois que rajoute une ligne
- **62** c'est l'ID de l'événement pas besoin de le changer
- **100** c'est le pourcentage de chance de "recevoir" l'event, en gros.
- **@GossipMenuID** c'est le numéro de ta page
- **0** c'est l'id de ton option (OptionID en gros dans gossip_menu_option)
- **62** c'est l'action donc là Teleport
- **M** c'est le numéro de ta map ou tu souhaites TP ton joueur (Faut que tu le change quoi)
- **7** c'est la target donc la c'est ACTION_INVOKER donc le joueur quoi c'est lui qui clique donc il invoque l'action
- **X, Y, Z, O** faut le changer en donnant les positions pour ton TP

Et le dernier c'est un commentaire je te conseil d'en mettre un bien complet pour comprendre ce que fais ta ligne.

Et voilà Monsieur/Madame ton téléporteur fonctionne enfin à 100%, c'est vrais qu'un tutoriel écris est quand pas mal difficile à comprendre et même à écrire, en tout cas si vous avez des questions, je suis disponible sur Discord et sur ce forum.

Allez salut..
