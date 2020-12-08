# Phaser des maps en 6x

## Tutoriel par @Stitch

Vous avez surement tous apprécié sur un serveur les passages particuliers que sont les changement de la carte apres une quete comme l'inondation d'un village chez les Worgens ou ville en boite chez les Goblins , un tas de petards qui ce transforme en village 

Vous vous êtes toujours demandé comment c’était possible et rêviez de l'adapter a votre serveur privé ... rassurez vous apres ce petit tuto ce sera possible :p

Pour ceux qui n'auraient pas encore compris voici pour l'exemple la zone chez les Panda ou une epave de navire et dans un premiers temps expulsé laissant une faille a sa place pour ensuite etre comblé .



**La map d'origine avec l'epave :**

![[Image: 776001WoWScrnShot042016195503.jpg]](https://zupimages.net/up/20/50/jsn8.jpg)

**La map apres la premiere phase , sans l’épave et une crevasse a sa place :**

![[Image: 409240WoWScrnShot042016195621.jpg]](https://zupimages.net/up/20/50/w4hl.jpg)

**Toujours la meme map apres la seconde phase , sans l’épave et une crevasse rebouchée :**

![[Image: 953677WoWScrnShot042016195857.jpg]](https://zupimages.net/up/20/50/qcs6.jpg)



### Partie 1 : Le principe & mise en place

Le principe est simple . Ici nous avons la map d'origine des pandarens ID 860 , ainsi que deux morceaux de map ID 975 & 976 . 
C'est deux dernieres sont faciles a reconnaitre lorsque l'on ce tp dessus : elles sont assez petites et l'on bute vite contre un mur invisible .



![[Image: 680456771.jpg]](https://zupimages.net/up/20/50/xc0b.jpg)

Pour les phaser il faut renseigner dans un premier temps la table terrain_worldmap , avec dans TerrainSwapMap la carte générale (860) et dans WorldMapArea le morceau de carte qui viendra ce greffer sur la première (975).



![[Image: 428215562.jpg]](https://zupimages.net/up/20/50/jkl2.jpg)

Ensuite dans la Table **terrain_phase_info** avec dans ID un numero de phase comme pour le phasage des pnj et dans TerrainSwapMap le morceau de map qui va ce greffer sur la map principale . 
Dans mon exemple j'ai choisi la phase 171 parce que j'avais le sort de la zone DK mais libre a vous de changer la phase du joueur comme vous voulez , par exemple en C+ dans le core.

Voila c'est fait ... enfin presque . Si vous allez dans le jeu vous ne verez pas de différence , et pour cause : il manque l'action de déclenchement .

L'action de déclenchement est assez simple : il faut faire changer le joueur de phase comme dans toutes ces quetes de depart chez les DK , Goblin , Worgen ou autres.
Par exemple par une quete qui va sur le meme principe que le phasage des mobs lui coller une aura qui le change de phase suivant certaines conditions de zone , ou changer la phase du joueur directement dans votre script C++ .



Pour avoir un aperçu du changement vous pouvez changer votre joueur de phase avec la commande .modify phase 171 . Cette commande bascule on/off la phase .
Il ne vous restes plus désormais qu'a peupler une nouvelle phase avec des mobs qui doit etre différente de celle choisi pour le morceau de terrain sous peine de crash ( n'oubliez pas que vous n'etes plus dans la phase par defaut 0 mais la 171 et que donc que les mobs sont resté en phase 0 . 

Vous devrez donc activer deux phases : 171 pour afficher le terrain et 175 par exemple pour spawm les mobs .

**Quelques commandes qui peuvent vous etre utiles :**

- .gobject set phase  

- .modify phase dephase123  

- .npc set phase N°dephase123  

- .debug phase : vous retourne les phase active pour la sélection (joueur ou pnj)

Il est aussi possible de phaser d'origine sans la possibilité de basculer grâce a la table **terrain_swap_defaults** .



### Partie 2 : Activation des phases sur le joueur

**<mark>EDIT Important :</mark>** Il faut impérativement retirer du dossier mmaps les mmaps principales phasées , Dans le cas contraire le serveur crash a la reconnexion du joueur ayant ces auras dans la table caracter_aura . 

Exemple la zone de départ des Panda (L'île Vagabonde 860) est phasé au niveau de l’épave par 2 morceaux de maps 975 ou 976 , il faudra donc retirer la principale , c'est a dire la mmap 860 .



**Grâce a Sai-editor créez un script SmartAI comme ci dessous :**

**Pour activer les phases :**

- **Event** : Ce que vous voulez

- **Action** : ACTION_CROSS_CAST (86) = spell phase -> action/Target type caster =7

- **Target** : TARGET_SELF (1)

**Pour de activer les phases :**

- **Event** : Ce que vous voulez

- **Action** : ACTION_REMOVEAURASFROMSPELL(28) = spell phase

- **Target** : TARGET_ACTION_INVOKER (7)



**Une liste de spell activant les phases :**

Ces sorts agissent sur le lanceur d'ou l'importance de le lancer par **ACTION_CROSS_CAST** et non avec **ACTION_CAST** .



**Spell          Phase**

- 70889 170  

- 59074 171  

- 67789 179  

- 68480 180  

- 70105 180  

- 68481 181  

- 68482 182  

- 68483 183  

- 69077 184  

- 75243 184 
  
  - Emerald Dream + voile vert - reste apres dephasage & deco : remove aura  

- 69078 185  

- 69484 186  

- 69484 187  

- 69486 188  

- 70695 189  

- 70696 190  

- 74093 191  

- 74094 192  

- 74095 193  

- 74096 194  

- 74097 195



Si vous avez apprécié ce tuto faite de meme : partagez vos découvertes avant qu'il n'y ai plus de communauté :p

[Exemple de phasing de map](https://www.youtube.com/watch?v=VkK8pNF0uAk)
