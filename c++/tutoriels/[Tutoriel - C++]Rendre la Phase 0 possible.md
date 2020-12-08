# Rendre la Phase 0 possible

### Tutoriel par @iThorgrim-Hub

Il y a longtemps quand je travaillés pour @Oldarorn (Hyakkimaru) sur le projet Shadow-Storm j'avais besoin d'avoir un nombre de phase presque illimités pour ranger mes pnjs.

J'ai donc cherchés très longtemps comment ranger mes PNJ dans une phase qui ne serais **JAMAIS** utilisés ?
C'est là que je me suis posés la question *"Pourquoi la phase 0 n'est pas utilisés correctement ?"*.

Si vous voulez, vous avec un total de 32 phases actives sur lesquels vous pouvez vous rendre, la phase 0 n'en fait pas partit MAIS vous pouvez phaser un PNJ en phase 0.
Alors ou vas t'il ? Que ce passe t'il ?

Pour faire simple la phase 0 est un "bug ?" de l’Emulateur.
En gros quand vous envoyés une entité dans la phase 0, la phase 0 est liés à cette entité, personne ne peu venir dans votre phase 0.

J'avais donc trouvés la solution, sauf que biim coup de réalités dans la bouche quand tu restart ton serveur hop le pnj retourne en phase 1.
Je me suis donc dit, "Faut que je modifie le CORE" et comme je suis une merde en CPP bah ça fait deux coups de réalités dans la bouche!

J'ai donc demandés de l'aide à @Belitharian que je remercie encore, car grâce à lui, j'ai une phase poubelle qui me permet d'utiliser encore les 32 phases correctement.

### Sommaire :

- Outils
- Le Core
- La Base de donnée

### 1. Outils :

- Un core fonctionnel
- Un Editeur de texte (Atom, Visual Code, SublimeText)
- Un SGDB (Navicat, SQLYog etc.)
- Une machine pour compiler ..

### 2. Le Core :

Vous allez vous rendre dans : `src/server/game/Globals/ObjectMgr.cpp`
Vous cherchez la ligne : `if (data.phaseMask == 0)`

Et vous commentez tout le bloc :
![image](https://user-images.githubusercontent.com/65762554/82656559-40e92b80-9c24-11ea-9c33-ae2d85d254a2.png)

Et pareil pour les GameObject’s qui se trouve dans le même fichier :
![image](https://user-images.githubusercontent.com/65762554/82656585-4ba3c080-9c24-11ea-9b80-0f2425defc80.png)

Et hop tu peux recompiler et d'ailleurs sous Linux et Windows, ne refaite pas un coup de Cmake, pas besoin, tu build et c'est finis :D

### 3. La base de donnée :

Très simple quand vous voulez qu'un PNJ soit en phase 0 il vous faudra exécuter ce code dans votre DB :

```SQL
SET @GUID := ;
UPDATE creature SET spawnMask = 0, phaseMask = 0 WHERE guid = @GUID;
```

Et si vous souhaitez que ça soit un GameObject en phase 0 :

```SQL
SET @GUID := ;
UPDATE gameobject SET spawnMask = 0, phaseMask = 0 WHERE guid = @GUID;
```

Voilà, voilà, je vous dit à la prochaine et je vous fait la bise ;)
