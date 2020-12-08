# Créer un PNJ qui MORPH

### Tutoriel par @iThorgrim-Hub

Bonjour, arrête de relire, tu as bien lu !
Aujourd’hui nous allons voir comment créer un PNJ qui va simplement nous morph !

### 1 - Outils

- Un Éditeur de Base de donnée (Mysql Workbench, SQLyog, Navicat etc.)
- Une Base de donnée World
- Du coca ou une boisson parce que là on en a pour longtemps!

Bon pour commencer il nous faut créer un PNJ de type POULE (ou autre t'es grand tu fais ce que tu veux)!
**VARIABLE LOCALE !**

```sql
SET
@ENTRY:= 50001, 
-- Bah .. l\'entry (Genre son ID quoi pour le faire spawn),

@ModelID:= 304, 
-- Son Model aka son Skin,

@Type:= 7, 
-- Son Type (Genre Humanoïde, Géant, Bête etc.),

@NPCFlag:= 1, 
-- Imagine ça comme des options, mais genre des options bien sympas,

@Unit_Class:= 1, 
-- La classe de ton PNJ il en existe pas beaucoup (1= Guerrier (Barre de vie seulement et beaucoup de Vie) 2= Paladin (Beaucoup de Vie et peu de Mana), 4= Voleur (Barre de Vie seulement et peu de Vie), 8= Mage (Peu de Vie et beaucoup de Mana))

@Unit_Flags:= 2048, 
-- Encore plus d\'option la normalement c\'est le moment ou tu peu "activer" la regen de vie etc.

@Faction:= 35, 
-- Sa faction nous on va utiliser la faction 35 parce qu\'on a la flemme de cherche une autre faction ;)

@Name:= 'Poulet de Fou!', 
-- Déconne pas .. c\'est son nom

@SubName:= 'Il te Morph!', 
-- Bah son sous nom ..

@MinLevel:= 80, 
-- Son niveau minimum, minimal, son niveau mini!

@MaxLevel:= 80, 
-- T\'as compris le truc ..

@AIName:= 'SmartAI', 
-- Alors l\'AIName c\'est compliqués, tu peux en avoir plusieurs mais nous on s\'en fou on juste utiliser le SmartAI

@Gossip_menu_id:= 60005; 
-- L\'ID du Gossip_Menu_Option, on s\'en sert juste après ^^

DELETE FROM `creature_template` WHERE (entry = @ENTRY);
INSERT INTO `creature_template` (`entry`, `name`, `subname`, `modelid1`, `npcflag`, `unit_class`, `unit_flags2`, `AIName`, `faction`, `type`, `minlevel`, `maxlevel`, `gossip_menu_id`)
VALUES (@ENTRY, @Name, @SubName, @ModelID, @NPCFlag, @Unit_Class, @Unit_Flags, @AIName, @Faction, @Type, @MinLevel, @MaxLevel, @Gossip_menu_id);
```

Vas y copie, de toute façon je sais que tu lis rien..

### 3 - GOSSIP_MENU_ACTION ?

On est partit pour le **_GOSSIP_MENU_OPTION_**!

```sql
SET @MenuID:= 60005, 
-- L\'ID du gossip_menu_option là c\'est = 60005

@OptionID:= 0, 
-- C\'est l\'ordre du gossip, enfin des options

@OptionIcon:= 0,
-- Tu peu tout simplement mettre une petite icône sympas

@OptionText:= 'Morph moi si tu peux!', 
-- Bah c\'est le texte ex : "Montrez moi ce que vous avez à vendre". Tu comprend là, dans ta tête le PNJ commence à prendre forme

@OptionType= 1, 
-- Simple tu met la même chose que dans OptionNPCFlag (Sauf dans certains cas, moi je te conseil de mettre 1), sinon c\'est des "Flags" des options si tu préfères

@OptionNPCFlag:= 1, 
-- Simple tu met ce que tu as mis dans NPCFLAG de ton creature_template, tu met toujours ce qu\'il y a dans creature_template colonne NPCFLAG ! TOUJOURS!

@ActionMenuID:= 60005; 
-- Bon tu peu en gros liée un Gossip à un autre genre tu clique sur Menu 1 tu arrive sur Page 2

DELETE FROM `gossip_menu_option` WHERE (MenuID = @MenuID);
INSERT INTO `gossip_menu_option` (`MenuID`, `OptionID`, `OptionIcon`, `OptionText`, `OptionType`, `OptionNPCFlag`, `ActionMenuID`)VALUES (@MenuID, @OptionID, @OptionIcon, @OptionText, @OptionType, @OptionNPCFlag, @ActionMenuID);[/sql]


[align=center][size=120][color=#FF4000]4/- GOSSIP_MENU & NPC_TEXT ?[/color][/size][/align]
[code=sql]SET 
@MenuID := 60005, 
-- Alors c\'est l\'ID du MenuID de ton Gossip_Menu_Option Aka (60005)

@TextID := 60005; 
-- C\'est l\'ID du NPC_Text

DELETE FROM `gossip_menu` WHERE (MenuID = @MenuID);
INSERT INTO `gossip_menu` (`MenuID`, `TextID`)
VALUES(@MenuID,@TextID);
```

Là on passe au **NPC_TEXT** pour faire quelques chose de propre et pas laisser "**Greetings**".

```sql
SET 
@ID:= 60005, 
-- C\'est son ID le même que tu as dans Text_ID de Gossip_Menu

@Text0_0:= '|Accueil > ? |\r\n\r\n Cot ? Cot cot .. ! COT!'; 
-- C\'est son Texte que tu vas voir sur la jolie page aka Gossip_Menu_Option

DELETE FROM `npc_text` WHERE (ID = @MenuID);
INSERT INTO `npc_text` (`ID`, `text0_0`)
VALUES(@ID,@Text0_0);
```

### 5 - SPELL_DBC ?

Alors je suis désolé mais je vais pas t'expliquer chaque colonne, juste 3 colonnes.

- Pourquoi ?
  - Parce que cette table c'est SPELL.DBC version SQL et j'en parle dans un prochain tutoriel.
  - Donc pour le moment tu recopie comme un gogole et tu arrêtes DE POSER DES QUESTIONS !

```sql
SET 
@ID:= , -- Spell.DBC de ton DBC de ton serveur qui viens compléter le tout, donc met un ID abuseyy
@EFFECTMISCVALUE1:= , -- Long à écrire celui là! Alors la en gros ton joueurs vas se transformer en ce que tu auras mis ici, donc tu met l\'ENTRY du PNJ auquel tu veux que ton joueurs ressemble
@Comment:= 'Poulet - Morph joueur > Poulet'; -- Met un commentaire, met le nom de ton sort et son effet
DELETE FROM `spell_dbc` WHERE (Id = @ID);
INSERT INTO `spell_dbc` (`Id`, `Dispel`, `Mechanic`, `Attributes`, `AttributesEx`, `AttributesEx2`, `AttributesEx3`, `AttributesEx4`, `AttributesEx5`, `AttributesEx6`, `AttributesEx7`, `Stances`, `StancesNot`, `Targets`, `CastingTimeIndex`, `AuraInterruptFlags`, `ProcFlags`, `ProcChance`, `ProcCharges`, `MaxLevel`, `BaseLevel`, `SpellLevel`, `DurationIndex`, `RangeIndex`, `StackAmount`, `EquippedItemClass`,`EquippedItemSubClassMask`, `EquippedItemInventoryTypeMask`, `Effect1`, `Effect2`, `Effect3`, `EffectDieSides1`, `EffectDieSides2`, `EffectDieSides3`, `EffectRealPointsPerLevel1`, `EffectRealPointsPerLevel2`, `EffectRealPointsPerLevel3`, `EffectBasePoints1`, `EffectBasePoints2`, `EffectBasePoints3`, `EffectMechanic1`, `EffectMechanic2`, `EffectMechanic3`, `EffectImplicitTargetA1`, `EffectImplicitTargetA2`, `EffectImplicitTargetA3`, `EffectImplicitTargetB1`, `EffectImplicitTargetB2`, `EffectImplicitTargetB3`, `EffectRadiusIndex1`, `EffectRadiusIndex2`, `EffectRadiusIndex3`, `EffectApplyAuraName1`, `EffectApplyAuraName2`, `EffectApplyAuraName3`, `EffectAmplitude1`, `EffectAmplitude2`, `EffectAmplitude3`, `EffectMultipleValue1`, `EffectMultipleValue2`, `EffectMultipleValue3`, `EffectItemType1`, `EffectItemType2`, `EffectItemType3`, `EffectMiscValue1`, `EffectMiscValue2`, `EffectMiscValue3`, `EffectMiscValueB1`, `EffectMiscValueB2`, `EffectMiscValueB3`, `EffectTriggerSpell1`, `EffectTriggerSpell2`, `EffectTriggerSpell3`,
`EffectSpellClassMaskA1`, `EffectSpellClassMaskA2`, `EffectSpellClassMaskA3`, `EffectSpellClassMaskB1`, `EffectSpellClassMaskB2`, `EffectSpellClassMaskB3`, `EffectSpellClassMaskC1`, `EffectSpellClassMaskC2`, `EffectSpellClassMaskC3`,
`MaxTargetLevel`, `SpellFamilyName`, `SpellFamilyFlags1`, `SpellFamilyFlags2`, `SpellFamilyFlags3`, `MaxAffectedTargets`, `DmgClass`, `PreventionType`, `DmgMultiplier1`, `DmgMultiplier2`, `DmgMultiplier3`, `AreaGroupId`, `SchoolMask`, `Comment`)
VALUES(@ID,'0','0','384','0','0','0','0','0','0','0','0','0','0','1','0','0','101','0','0','0','0','21','1','0','-
1','0','0','6','0','0','0','0','0','0','0','0','0','0','0','0','0','0','1','0','0','0','0','0','0','0','0','56','0','0','0','0','0','0','0','0','0','0','0',@EFFECTMISCVALUE1,'0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0',@Comment)
```

### 6 - SMART_SCRIPTS ?

Bon plus que ça et ... c'est finis ! T'imagine ?! Déjà !
Alors là j'ai fait les _VARIABLES LOCALE_ à ma manière tu peux faire comme tu veux, mais c'est mieux ranger pour ma lecture.

```sql
SET 
@ENTRY:= 50001, 
-- L\'ID du PNJ (Pour lier ça à ta créature!)

@ID:= 0, 
-- Bon la t\'es pas con, t\'as compris

@LINK:= 1,
 -- Sympas ce machin en gros tu peu liéer 1 EVENT_TYPE à 2 ACTION_TYPE (Genre la nous on veux on gossip select > remove item et morph)

@ID_1:= 1, 
-- Pareil que le ID normal quoi ..

@EventType_1:= 62, 
-- Simple c\'est event_type tu as plusieurs type, la nous on utilise l\'event GOSSIP_SELECT

@EventType_2:= 61, 
-- Et ici on utilise le LINK

@EVENT_PARAM1:= 60005, 
-- Donc la on met tout simplement le gossip_menu_option

@ACTION_TYPE_1:= 57, 
-- Comme pour event_type tu as plusieurs Action_type nous on utilise le REMOVE_ITEM

@ACTION_TYPE_2:= 75, 
-- Et la le ADD_AURA

@ACTION_PARAM1_1:= 6948, 
-- Ici l\'ID de l\'ITEMS qu\'il faut pour se faire morph (En gros toi tu as une pierre de foyer, le PNJ te le retire et te morph, si tu as pas pierre de 
foyer, pas de morph)

@ACTION_PARAM2_1:= 1, 
-- Le nombre d\'items qu\'il faut pour se faire morph (1 Pierre de foyer dans notre exemple)

@ACTION_PARAM1_2:= 190000, 
-- L\'ID du Sort aka de l\'Aura car oui tu as créer un sort mais le PNJ ne va pas lancer le sort il va appliquer le sort au joueurs aka appliquer une AURA

@TARGET_TYPE:= 7, 
-- Le target Type ! Super ce truc, donc la on met le 7 aka Action_Invoker

@COMMENT_1:= 'Poulet > 6948 x1 > Link 1', 
-- Les commentaires, je te conseil là de bien mettre ce qui se passe au plus simple, évite les longue phrases c\'est chiant à lire.

@COMMENT_2:= 'Poulet > Link 1 > Morph 50001'; 
-- Mais oublie pas faut que ça soit compréhensible de tous ou au moins des gars de ton équipe.

DELETE FROM `smart_scripts` WHERE (entryorguid = @ENTRY);
INSERT INTO `smart_scripts` (`entryorguid`, `source_type`, `id`, `link`, `event_type`, `event_phase_mask`, `event_chance`, `event_flags`, `event_param1`, `event_param2`, `event_param3`, `event_param4`, `action_type`, `action_param1`, `action_param2`, `action_param3`, `action_param4`, `action_param5`, `action_param6`, `target_type`, `target_param1`, `target_param2`, `target_param3`, `target_x`, `target_y`, `target_z`, `target_o`, `comment`) VALUES (@ENTRY, 0, @ID, @LINK, @EventType_1, 0, 100, 0, @EVENT_PARAM1, 0, 0, 0, @ACTION_TYPE_1, @ACTION_PARAM1_1, @ACTION_PARAM2_1, 0, 0, 0, 0, @TARGET_TYPE, 0, 0, 0, 0, 0, 0, 0, @COMMENT_1),
(@ENTRY, 0, @ID_1, 0, @EventType_2, 0, 100, 0, 0, 0, 0, 0, @ACTION_TYPE_2, @ACTION_PARAM1_2, 0, 0, 0, 0, 0, @TARGET_TYPE, 0, 0, 0, 0, 0, 0, 0, @COMMENT_2);
```

### EXPLICATION :

J'aurais plus faire un smart_script avec un morph direct, sans passer par un sort, mais en passant par un sort si le mec se fait buter il devras repayer pour être morph, (ce qui peu être bien si tu fais fonctionner ça avec des points de vote/dons :D )

BAAAAM! T'as finis.
Sérieux, là tu as appris à créer un sort qui morph directement en SQL, tu as aussi appris à créer un gossip lui liés un petit texte sympas et tout.

Bon allez Salut ;)
