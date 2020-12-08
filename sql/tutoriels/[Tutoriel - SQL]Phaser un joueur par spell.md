# Phaser un joueur avec un spell

## Tutoriel par @Stitch11

Ce petit tutoriel est générique , le principe peut s'adapter pour phaser les joueurs dans un lieu précis sous certaines conditions comme celle d'avoir une quête en cour ou terminé ou bien appliquer un bonus ou aura comme l'interdiction dans une zone comme au joug ou Dalaran . 

Nul besoin de C++ , juste avec la table [spell_area](https://trinitycore.atlassian.net/wiki/display/tc/spell_area) .

Comme exemple je prendrais la quete Dans le royaume des ombres (quete 12687) faisant partie de la zone de depart du DK .

### Il ce passe quoi au juste ?

- Le joueur en prenant cette quete quite l'espace normal et ce retrouve dans la phase Phase 174 grace a mon spell 52693 , pour imager c'est comme si le joueur avait été téléporté sur une autre map lui ressemblant avec des mob et objet différents ... sauf que c'est la même map avec plusieurs couches dont seule l'active pour les joueurs phasés est visible .
   Il poura voir d'autres mob ou gameobject que ceux présent dans la map normale .

- Lorsque le joueur rend la quete il quite la phase 174 pour revenir dans le monde normal qu'il avait quité .

### Savoir ou va ce passer cette phase :

Pour cela un simple .gps vous donnera sa zone : 4298

### Les conditions pour qu'elle s'applique automatiquement :

(suivant des conditions de zone ou de quetes)

Sous phpmyadmin j'ouvre la table [spell_area](https://trinitycore.atlassian.net/wiki/display/tc/spell_area) et je filtre par zone soit ici 4298 (j entre cette valeur dans la case  area , sa peut prêter a confusion) et j obtient ceci :

![](https://zupimages.net/up/20/50/0df2.jpg)

Connaissant l'id de la quete par wowhead , soit 12687 et que la phase dure du moment ou je la prend au moment ou je la rend , je peux donc en deviner la condition pour qu'elle s'applique :

Je vois de suite sur la derniere ligne que la condition et donc mon joueur sera phasé par le spell **52693** de la quete (**quest_start**) jusqu'a la quete (**quest_end**) dans la zone **4298** , **quest_start** &**** quest_end**** peuvent comme ici etre une seule quete ou différente pour indiquer la 1ere et la dernière d'une suite .

Les colonnes **quest_start_status** & **quest_end_status** sont des flags indiquant l'etat dans lesquelles doivent être les quêtes défini en **quest_start** & **quest_end** . 
A lire comme des " ou " c'est a dire pour 74 = 64 ou 8 ou 2.

- **QUEST_STATUS_NONE** = 0 
  
  - flag 1 Le joueur n'a pas encore la quete  

- **QUEST_STATUS_COMPLETE** = 1 
  
  - flag 2 Le joueur a remplit les objectifs mais n'a pas  rendu la quete  

- **QUEST_STATUS_UNAVAILABLE** = 2 
  
  - flag 4 (NOT USED) (Not used)

- **QUEST_STATUS_INCOMPLETE** = 3 
  
  - flag 8 Le joueur n'a pas remplit les objectifs , quete en cour  

- **QUEST_STATUS_AVAILABLE** = 4 
  
  - flag 16  (NOT USED) (Not used)  

- **QUEST_STATUS_FAILED** = 5 
  
  - flag 32 Le joueur a faillit dans les objectifs pour une raison quelconque, par exemple limite de temps  

- **QUEST_STATUS_REWARDED** = 6  
  
  - flag 64  Le joueur est mort ?!

- racemask : 
  
  - toute les races = 44042239 ou 0
  
  - horde = 33555378
  
  - alliance = 18875469

- gender : 
  
  - Male = 0 
  
  - Female = 1
  
  - les deux = 2

La colonne autocast permet d activer par 1 ou désactiver par 0 cette condition .

### Déclencher cette phase manuellement

(par un pnj grace a smartai)

- **L'event** : importe peut , vous pouvez par exemple choisir quand le joueur prend une quete : EVENT_ACCEPTED_QUEST (19)  

- **Action** : ACTION_CROSS_CAST (86) avec comme Paramètre Target type caster =7 et Spell ID le spell qui va faire phasé  

- **Target** : TARGET_SELF (1)

### Pour retirer cette phase déclanché manuellement

(par un pnj grace a smartai)

- **Event** : Ce que vous voulez

- **Action** : ACTION_REMOVEAURASFROMSPELL(28) avec comme Paramètre Spell ID le spell qui avait phasé le joueur

- **Target** : TARGET_ACTION_INVOKER (7)



**Spell Phase**

- 70889    170  

- 59074    171  

- 67789    179  

- 68480    180  

- 70105    180  

- 68481    181   

- 68482    182  

- 68483    183  

- 69077    184  

- 69078    185  

- 69484    186  

- 69484    187  

- 69486    188  

- 70695    189  

- 70696    190  

- 74093    191  

- 74094    192  

- 74095    193  

- 74096    194  

- 74097    195
