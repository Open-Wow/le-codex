# Faire un système de file d'attente

### Tutoriel par Kazuma

Salut Open-Wow ! Aujourd'hui je vous montre comment faire un système de file d'attente. Eh oui, c'est quand même plus simple et plus classe de faire bien les choses surtout quand il s'agit d'éviter les laborieux "Ceux qui veulent participer à l'event faites "+1" !" de vos maitre de jeu dans les chat...On est plus en 2008
(ou je sais pas quand ! 😄 )

L'objectif ça va être de pouvoir permettre aux joueurs de pouvoir s'inscrire/se désincrire auprès d'un PNJ proprement.

### 1 - Pré-requis

Avant toute chose vous vous en serez surement douter, il vous faut un gossip pour que le joueur puissent interagir avec le PNJ.

Concernant les gossip si vous ne savez pas ou ne comprenez pas comment ça marche je vous recommande de consulter [mon tutoriel sur les Gossip](https://github.com/Open-Wow/forum/issues/5) car je ne vais pas ré-expliquer le fonctionnement dans ce tutoriel.

### 2 - Comment s'y prendre ?

En réalité c'est très simple, après avoir mis en place un gossip à minimum 2 choix sur un PNJ.
![image](https://user-images.githubusercontent.com/65762554/82659954-c91dff80-9c29-11ea-8706-2b91c2737377.png)

Ce qui nous importe c'est le mécanisme qui se cache derrière ces deux choix, donc direction votre fonction `OnGossipSelect`

Vous devez avoir votre bloc d'instruction IF (Si) au moins comme ceci :
![image](https://user-images.githubusercontent.com/65762554/82659996-d3d89480-9c29-11ea-8f03-c1651212f83d.png)

Pour que vous preniez l'habitude de faire du code propre, on ne va pas directement écrire du code pour chaque situation, on va se servir de deux fonctions qu'on va créer nous-même :

- **inscription** : _On lui donnera comme paramètre le nom du joueur, elle gérera l'inscription, elle devra vérifier si le joueur n'est pas déjà inscrit et si ce n'est pas le cas : l'inscrire._
- **desinscription** : _On lui donnera comme paramètre le nom du joueur, elle gérera la désinscription, elle devra vérifier si le joueur est déjà inscrit avant d'essayer de désinscrire le joueur._

#### A - Mais comment on inscrit un joueur ?

Bonne question, c'est ici que repose la clé du script !

Il y a deux façon d'enregistrer une valeur, de sauvegarder une donnée :

- **La base de donnée** : _C'est la première chose à laquelle on peut penser j'imagine quand on est novice dans le domaine, en effet c'est une bonne façon de faire et c'est réalisable en Lua grâce à une requête SQL , et si c'est bien effectuer vous êtes sûr de conserver vos données._

Mais pourquoi s'embêter à stocker des informations qui ont déjà peu de valeur et qui ne vous seront absolument plus d'aucune utilité une fois passer un délai ?
Pourquoi faire compliqué quand on peut faire simple ? 
(Question à prendre au second degré , parfois il est mieux de faire compliqué... 😄)

- **Les table LUA** : _Les tables sont comme des tableaux que vous pouvez mettre en place dans votre code, c'est une fonctionnalité vraiment puissante, car vous pouvez stocker énormément d'informations dans ces tableaux sans pour autant embêter la DB. 
  [Le tutoriel d'iThorgrim](https://github.com/Open-Wow/forum/issues/8) saura vous expliquer parfaitement ce que vous devez savoir à ce sujet_

**Nous allons donc nous servir d'un table LUA afin de s'en servir de "Liste d'attente".**

### 3 - Le Code

Rendez-vous au début du script ou vous créerez un table LUA intitulé "ListeAttente" : dedans nous y stockeront les noms des joueurs inscrits.
Créez également une variable contenant le nombre de joueur nécessaire au déclenchement de l'événement , ça sera le même principe que les champs de batailles où un nombre de joueur est requis. 
_(Mettez minimum 2 pour un test plus concret)_

![image](https://user-images.githubusercontent.com/65762554/82660246-4a759200-9c2a-11ea-82eb-67fb2f7077c8.png)

Désormais, nous avons notre liste d'attente vide, et un nombre de joueur minimum, maintenant il nous manque nos deux fonctions dont j'ai parlé tout à l'heure.

**Tout d'abord, placez-vous avant vos fonctions par rapport aux gossip !**
**C'est important car vous ferez appel à ces fonctions (inscription & desinscription) dans votre fonction OnGossipSelect, c'est pourquoi quand l'interpréteur va lire votre script de haut en bas, il doit connaitre la fonction inscription pour pouvoir l'utiliser, sinon il y aura erreur !**

![image](https://user-images.githubusercontent.com/65762554/82660272-5a8d7180-9c2a-11ea-86ca-446e87c4f0d4.png)

- **Paramètre de la fonction** :
  - **pName** : _Quand vous ferez appel à la fonction, vous devrez donner le nom du joueur entre les parenthèses, je trouve ça plus propre de procéder ainsi plutôt qu'en allant chercher son nom avec sa fonction alors qu'on aurait pu directement le donner entre les parenthèses de la fonction en tant que paramètre._

```lua
  local trouve = true

  for numero, nom in pairs(ListeAttente) do -- Parcours liste d'attente
    if (nom == pName) then -- Si il existe déjà ce nom sur la liste
      trouve = false
      break
    end
  end

  if (trouve == true) then 
    table.insert(ListeAttente, pName) -- Inscription dans la liste
  end

  return trouve
```

![image](https://user-images.githubusercontent.com/65762554/82660355-801a7b00-9c2a-11ea-937a-ea6172e3cff2.png)

Voici notre fonction, c'est beaucoup de ligne qui apparaissent d'un coup, alors qu'est-ce qu'il se passe exactement ?

- **boucle for** : _Ici grâce à une boucle for, on parcours le table ListeAttente, ainsi on va pouvoir regarder tout les noms contenus dans la liste. Je rappelle que les noms dans la liste représente les joueurs inscrit. la variable "numero" correspond à l'index, la clé ou l'indice (plusieurs façons de l'appeler), disons qu'il représente le numéro de ligne comme dans un tableau excel (1, 2 , 3, 4, 5 ...) chaque ligne à sa clé. la variable "nom" est tout simplement la valeur de la clé dans le table, ainsi, chaque ligne contient un nom de joueur._

Imaginez le table visuellement comme ceci :

```
1 - Kazuma
2 - Thanatos
3 - iThorgrim
```

- **trouve = true** : *C'est une variable de type booléen, sa valeur est soit "true" (vrai), soit "false" (faux). On va se servir de cette variable pour nous indiquer si le joueur existe dans la liste ou pas.*
- **if (nom == pName)** : *Ici c'est tout bête on compare le nom trouvé à telle ligne et le nom du joueur qui souhaite s'inscrire.*
- **trouve = false** : *On met la variable à false, ce qui indique que le joueur ne peut pas être inscrit. (car il est déjà inscrit)*
- **break** : *C'est un mot-clé en lua qui va vous permettre d'interrompre et sortir d'un bloc d'instruction , nous sortons donc de la boucle car nous avons pu tester le joueur, si nous continuons , le script va machinalement continuer et traiter les autres joueurs alors que ce n'est pas ce que nous voulons.*
- **if (trouve == true) then** : *Si la valeur de la variable est vrai (donc n'a pas changé)*
- **table.insert(ListeAttente, pName)** : *On ajoute le nom du joueur dans le table*
- **return trouve** : *On renvoie la valeur de la variable, ainsi, vous verrez que plus tard à l'utilisation de la fonction , la fonction saura répondre si OUI ou NON le joueur à pu être inscrit.*

La fonction `desinscription `est exactement l'inverse, il vous est facile de la comprendre, on vérifiera si le joueur est bel et bien inscrit, si oui : on le supprime de la table avec table.remove.Sinon, il ne se passera rien :

```lua
local function desinscription(pName)

  local trouve = false

  for numero, nom in pairs(ListeAttente) do -- Parcours liste d'attente
    if (nom == pName) then -- Si il est bien dans la liste
      table.remove(ListeAttente, numero) -- Suppression dans la liste
      trouve = true
      break
    end
  end

  return trouve
end
```

![image](https://user-images.githubusercontent.com/65762554/82660468-b657fa80-9c2a-11ea-99b6-d5f844478594.png)

Maintenant que nos deux fonctions sont prêtes on retourne à notre fonction `OnGossipSelect `où l'ont va les appeler selon les choix :
![image](https://user-images.githubusercontent.com/65762554/82660477-bc4ddb80-9c2a-11ea-98a3-9051be654a65.png)

```lua
if (inscription(pName) == true) then
```

Vous remarquerez que nous mettons à profit l'utilisation du "`return trouve`" dans les fonctions. Les fonctions retournent une valeur, elles retournent soit "`true`" ("vrai") soit "`false`" ("faux")

Ce qui nous permet de savoir si le joueur a bien été inscrit ou non. Pratique non ?
Et donc on peut informer le joueur du résultat via la fonction `SendNotification` 😃

### 4 - Déclenchement de l'événement

Bon c'est bien beau mais il reste une dernière étape, il faut provoquer le déclenchement de votre événement si suffisamment de joueur sont inscrit.

Pour l'exemple on va mettre une fonction qui téléporte tout les joueurs inscrit.
![image](https://user-images.githubusercontent.com/65762554/82660602-f323f180-9c2a-11ea-948f-1e1b5eb9ef46.png)

- **if (#ListeAttente == NombreJoueur) then** : *On vérifie si le nombre de joueur inscrit est égal au nombre de joueur requis pour l'événement.*
- **for numero, nom in pairs(ListeAttente) do** : *On parcours la liste d'attente contenant les noms.*
- **for _, player in ipairs(GetPlayersInWorld()) do** : *On parcours un table contenant tout les joueurs en ligne.*
- **if (player:GetName() == nom) then** : *Si le nom du joueur en ligne correspont au nom figurant dans la liste d'attente*
- **player:Teleport(571, 5804.149902, 624.70996, 647.767029, 1.640000)** : *On téléporte le joueur à Dalaran (par exemple).*
- **ListeAttente = {};** : *A la fin de la fonction on vide la liste d'attente pour pouvoir s'en resservir correctement, les noms n'ont plus lieu d'y figurer.*

Vous n'avez plus qu'à ajouter l'appel de la fonction à l'endroit convenu et vous avez désormais en votre possession un système de file d'attente sympathique en Lua (façon Kazuma).
![image](https://user-images.githubusercontent.com/65762554/82660684-19499180-9c2b-11ea-872b-d14e51517494.png)

### 5 - Aperçu en jeu

"Se désinscrire sans être inscrit"
![image](https://user-images.githubusercontent.com/65762554/82660710-26ff1700-9c2b-11ea-9f61-2692dd499604.png)

"S'inscrire et téléportation dû au déclenchement de l'événement"
![image](https://user-images.githubusercontent.com/65762554/82660722-2c5c6180-9c2b-11ea-8efa-50b9f225497a.png)
