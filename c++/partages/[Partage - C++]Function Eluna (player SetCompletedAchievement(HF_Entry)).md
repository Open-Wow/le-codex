# Function Eluna (player:SetCompletedAchievement(HF_Entry))

### Partage par @iThorgrim-Hub

Bonjour,

Aujourd'hui je partage avec vous une petite modification d'Eluna.
Cette modification vous permettras d'ajouter une fonction pour "apprendre" un HF à votre joueur.

```lua
 player:SetCompletedAchievement(HF_Entry)
```

Vous aurez besoin de modifier 2 fichiers:

> src/server/game/LuaEngine/LuaFunctions.cpp
> Ajouter à la ligne 603 ceci : 

```cpp
{ "SetCompletedAchievement", &LuaPlayer::SetCompletedAchievement },
```

> src/server/game/LuaEngine/PlayerMethods.h
> Ajouter à la ligne 2056 ceci : 

```cpp
 int SetCompletedAchievement(lua_State* L, Player* player)
    {
        uint32 id = Eluna::CHECKVAL<uint32>(L, 2);
        AchievementEntry const* t = sAchievementStore.LookupEntry(id);
        if (t)
            player->CompletedAchievement(t, false);
        return 0;
    }
```

Une fois ceci fait un simple reBuild et une reCompilation de votre Core est nécessaire.
Le numéro des lignes n'est pas obligatoire, mais je ne saurais vous aider si vous ne respectez pas ceci.
Allez la bise ![image](https://user-images.githubusercontent.com/65762554/82669148-2ff7e480-9c3b-11ea-8aba-63f9f366650e.png)![image](https://user-images.githubusercontent.com/65762554/82668974-dabbd300-9c3a-11ea-8eb4-9106002ca77f.png)
