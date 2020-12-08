# SmartAI : Faire s'asseoir un PNJ [ < TC Rev.63]

### Tutoriel par @iThorgrim-Hub

![image](https://user-images.githubusercontent.com/65762554/82666680-aba36280-9c36-11ea-980a-2519d98c7f6d.png)
Bonjour et bienvenue à SOLSTICE!

Pour les biens de ce tutoriel, j'utilise cette petite zone fort sympathique.

Depuis la mise à jour de TrinityCore (rev.63 et supérieure) il est devenus compliquer de faire jouer certaines emotes au PNJ (des emotes ressemblant à celle des joueurs).
Les faire s'asseoir, dormir ne se gère plus via une émote. (Avant il suffisait de rajouter l'emote 13 dans _creature_addon_)

**ATTENTION si vous utilisez un GOBJECT de banc cliquables des emotes sont faites pour ceci!
CETTE méthode ne s'applique que pour faire s'asseoir les PNJ comme les joueurs, lorsque le joueur appuie sur "X".**

Après avoir placer vos PNJ sur des bancs, sélectionnez les et faite un petit _.NPC INFO_
Il nous faut sont _GUID._

![image](https://user-images.githubusercontent.com/65762554/82666740-cb3a8b00-9c36-11ea-9296-f658f244bbef.png)
Maintenant que nous avons sont GUID nous nous rendons dans _creature_addon_.
Nous souhaitons voir ce pnj s'asseoir, pour cela vous allez créer une ligne avec le GUID précédemment trouvés.

- Guid
- Bytes1
- Bytes2

Dans **Bytes1** vous allez tout simplement mettre "**1**" qui correspond au "Bytes" pour s'asseoir
Dans **Bytes2** vous allez remettre "**0**" ça évite de voir les PNJ avec ses armes de sortis, à utilisez par précaution.

```sql
SET 
@GUID := [TONGUID],
@Bytes1 := 1,
@Bytes2 := 0;
DELETE FROM `creature_addon` WHERE (guid = @GUID);
INSERT INTO `creature_addon` (`guid`, `bytes1`, `bytes2`) VALUES (@GUID, @Bytes1, @Bytes2);
```

![image](https://user-images.githubusercontent.com/65762554/82666799-e86f5980-9c36-11ea-8382-fbbeb388029e.png)

Voilà nos PNJ sont maintenant assis comme des joueurs.
![image](https://user-images.githubusercontent.com/65762554/82666814-edcca400-9c36-11ea-9353-d03eb7cce422.png)

Tutoriel très rapide, rien de compliqués, à bientôt j'espère.
