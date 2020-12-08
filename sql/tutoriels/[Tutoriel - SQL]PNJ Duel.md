# SmartAI : PNJ Duel

### Tutoriel par @Stitch11

![image](https://user-images.githubusercontent.com/65762554/82665434-12734c80-9c34-11ea-90f5-1fab1a174a3a.png)

Etant tombé sur une zone ou je devais provoquer en duel une quinzaine de pnj , j'ai ressorti mon vieux script SmartAI que je partage avec vous.

Le principe est simple , a l'origine le pnj est de faction neutre et passe en faction agressive après un gossip_menu. Arrivé a un minimun de vie il repasse faction neutre et vous crédite la quête .

- Diverses valeurs optionnelles a modifier sur votre creature_template . seul l'id du pnj , le gossip_menu et le npcflag = 1 sont indispensable .
  
  
  
  ```sql
  SET @ENTRY := 60540;
  SET @GOSSIP := 65576;
  UPDATE `creature_template` SET `HealthModifier` = 3.5, `ArmorModifier` = 2.5, `DamageModifier` = 2.5,`resistance1` = 25, `resistance2` = 25, `resistance3` = 25, `resistance4` = 25, `resistance5` = 25, `resistance6` = 25 WHERE (entry = @ENTRY);
  ```

UPDATE `creature_template` SET `npcflag` = 1, `unit_class` = 1, `unit_flags2` = 2048, `gossip_menu_id` = @GOSSIP, `type_flags` = 0 WHERE (entry = @ENTRY);

- Le gossip_menu pour lancer le duel , a vous de changer le message et l'id du gossip

```sql
SET NAMES utf8;
REPLACE INTO `gossip_menu_option` (`menu_id`, `id`, `option_icon`, `option_text`, `OptionBroadcastTextID`, `option_id`, `npc_option_npcflag`, `action_menu_id`, `action_poi_id`, `box_coded`, `box_money`, `box_text`, `BoxBroadcastTextID`) VALUES
(65576, 0, 0, "Voulez-vous vous mesurer à moi ?", 0, 1, 1, 0, 0, 0, 0, '', 0);
```

- L'ai générique pour gérer le duel :
  - **Ligne  10** ferme le gossip_menu
  - **Ligne  15** lance un script (Actionlist) qui a comme fonction de temporiser le debut du duel et changer la faction agressive du pnj)
  - **Ligne 20** un message de debut du duel
  - **Ligne 25** repasse en faction neutre pour arrêter le duel si les pv du pnj passe a 20% ou moins , le duel est gagné
  - **Ligne 30** donne le crédit après avoir gagné le duel
  - **Ligne  35** message pour congratuler le joueur pour sa victoire
  - **Ligne  40** un message du pnj si vous perdez le duel
  - **Ligne  45** repasse en faction neutre pour arrêter le duel après votre duel perdu
  - **Ligne  55** l'ai de combat du pnj , ici un unique sort

```sql
UPDATE `creature_template` SET `HealthModifier` = 3.5, `ArmorModifier` = 2.5, `DamageModifier` = 2.5,`resistance1` = 25, `resistance2` = 25, `resistance3` = 25, `resistance4` = 25, `resistance5` = 25, `resistance6` = 25 WHERE (entry = @ENTRY);
UPDATE `creature_template` SET `npcflag` = 1, `unit_class` = 1, `unit_flags2` = 2048, `gossip_menu_id` = @GOSSIP, `type_flags` = 0 WHERE (entry = @ENTRY);
UPDATE `creature_template` SET `AIName`="SmartAI" WHERE `entry`=@ENTRY;
DELETE FROM `smart_scripts` WHERE `entryorguid`=@ENTRY AND `source_type`=0;
INSERT INTO `smart_scripts` (`entryorguid`,`source_type`,`id`,`link`,`event_type`,`event_phase_mask`,`event_chance`,`event_flags`,`event_param1`,`event_param2`,`event_param3`,`event_param4`,`action_type`,`action_param1`,`action_param2`,`action_param3`,`action_param4`,`action_param5`,`action_param6`,`target_type`,`target_param1`,`target_param2`,`target_param3`,`target_x`,`target_y`,`target_z`,`target_o`,`comment`) VALUES
(@ENTRY,0,10,15,62,0,100,0,@GOSSIP,0,0,0,72,0,0,0,0,0,0,7,0,0,0,0,0,0,0,"GOSSIP_MENU : fermer gossip"),
(@ENTRY,0,15,20,61,0,100,0,0,0,0,0,80,5494400,2,0,0,0,0,1,0,0,0,0,0,0,0," - ACTIONLIST"),
(@ENTRY,0,20,0,61,0,100,0,0,0,0,0,1,0,0,0,0,0,0,1,0,0,0,0,0,0,0," - blabla 0"),
(@ENTRY,0,25,30,2,0,100,0,0,20,3000,3000,2,35,0,0,0,0,0,1,0,0,0,0,0,0,0,"DUEL GAGNANT : 1-20% pv : faction neutre"),
(@ENTRY,0,30,35,61,0,100,0,0,0,0,0,33,@ENTRY,0,0,0,0,0,7,0,0,0,0,0,0,0," - credit"),
(@ENTRY,0,35,0,61,0,100,0,0,0,0,0,1,1,0,0,0,0,0,1,0,0,0,0,0,0,0," - blabla 1"),
(@ENTRY,0,40,45,5,0,100,0,3000,3000,0,0,1,2,0,0,0,0,0,1,0,0,0,0,0,0,0,"DUEL PERDU : blabla 2"),
(@ENTRY,0,45,0,61,0,100,0,0,0,0,0,2,35,0,0,0,0,0,1,0,0,0,0,0,0,0," - faction neutre"),
(@ENTRY,0,55,0,9,0,100,0,0,5,2500,3000,11,119526,0,0,0,0,0,2,0,0,0,0,0,0,0,"Poings roulants");
```

- Les divers messages du pnj : groupid=0 pour le debut du duel , groupid=1 pour un duel gagné , groupid=2 pour un duel perdu
  
  ```sql
  REPLACE INTO `creature_text` (`entry`, `groupid`, `id`, `text`, `type`, `language`, `probability`, `emote`, `duration`, `sound`, `BroadcastTextId`, `TextRange`, `comment`) VALUES
  (@ENTRY, 0, 0, "Trés bien , montrez moi", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 0, 1, "J'essaierais de ne vous faire pas trop mal", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 0, 2, "Sa va etre votre fete !", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 0, 3, "On évite l’estomac, d’accord ?", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 0, 4, "Ouais ! D’la baston !", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 0, 5, "Trés bien , montrez moi vos compétences", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 0, 6, "J'essaierais de ne pas vous tuer", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 0, 7, "Ouais ! Ça va saigner !", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 1, 0, "Merci de m’avoir donné l’occasion d’être vaincu par vous.", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 1, 1, "Joli combat.", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 1, 2, "Beau combat. Mais je vous aurai, un jour.", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 1, 3, "Je ne crois pas avoir eu une seule chance. À un de ces quatre.", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 1, 4, "Waouh… Moi qui croyais y arriver…", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 1, 5, "Joli combat.", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 1, 6, "Sa ne compte pas , je n'étais pas en forme aujourdhui", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 1, 7, "Mouai , je croyais que ce serait facile …", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 2, 0, "Vous prenez ça vraiment au sérieux, pas vrai ?", 12, 0, 0, 11, 500, 0, 0, 0, "Commentaire"),
  (@ENTRY, 2, 1, "Et voila ... c'etait prévisible", 12, 0, 0, 1, 500, 0, 0, 0, "Commentaire");
  ```

- L'actionlist perso qui temporise le changement de faction et l'agro lancé par la ligne 15
  
  ```sql
  -- Actionlist SAI
  SET @ENTRY := 5494400;
  DELETE FROM `smart_scripts` WHERE `entryorguid`=@ENTRY AND `source_type`=9;
  INSERT INTO `smart_scripts` (`entryorguid`,`source_type`,`id`,`link`,`event_type`,`event_phase_mask`,`event_chance`,`event_flags`,`event_param1`,`event_param2`,`event_param3`,`event_param4`,`action_type`,`action_param1`,`action_param2`,`action_param3`,`action_param4`,`action_param5`,`action_param6`,`target_type`,`target_param1`,`target_param2`,`target_param3`,`target_x`,`target_y`,`target_z`,`target_o`,`comment`) VALUES
  (@ENTRY,9,10,15,60,0,100,1,5000,5000,5000,5000,2,7,0,0,0,0,0,1,0,0,0,0,0,0,0,"Tempo avant faction agressive 5s"),
  (@ENTRY,9,15,0,61,0,100,0,0,0,0,0,49,0,0,0,0,0,0,7,0,0,0,0,0,0,0," - attaque joueur");
  ```

Voila ce n'est qu'un modeste script mais qui peut vous rendre service , notez quand même que pour le test des 20% de pv du mob ne doit pas être inférieur aux dégâts que vous pourriez lui infliger , ne descendez donc pas trop cette valeur , préférez ses résistances ou ces pv .

A noter que ce script peut être amélioré par exemple en créditant la quete si je pnj est tué , tout dépend de ce que vous désirez . Ici pour ces quetes les pnj étaient amicaux et ne devaient donc pas mourir .

Bon jeu a vous 
