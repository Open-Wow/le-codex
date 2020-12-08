# Function Eluna (guild:SetCoinage(amount))

### Partage par @iThorgrim-Hub

Bonjour,

Aujourd'hui je partage avec vous une petite modification d'Eluna.
Cette modification vous permettras d'ajouter une fonction pour ajouter de l'or dans la banque de guilde.

```LUa
guild:SetCoinage(amount)
```

Vous aurez besoin de modifier 4 fichiers:

> src/server/game/LuaEngine/LuaFunctions.cpp
> Ajouter à la ligne 1105 ceci :

```cpp
{ "SetCoinage", &LuaGuild::SetCoinage },
```

> src/server/game/LuaEngine/GuildMethods.h
> Ajouter à la ligne 306 ceci :

```cpp
int SetCoinage(lua_State* L, Guild* guild)
    {
        uint64 money = Eluna::CHECKVAL<uint64>(L, 2);
        SQLTransaction trans = CharacterDatabase.BeginTransaction();
        guild->ModifyBankMoney(trans, money, true);
        CharacterDatabase.CommitTransaction(trans);
        return 0;
    }
```

> src/server/game/Guilds/Guild.h
> Ajouter à la ligne 708 ceci :

```cpp
bool ModifyBankMoney(SQLTransaction& trans, uint64 amount, bool add);
```

> src/server/game/Guilds/Guild.cpp
> Ajouter à la ligne 2617 ceci : 

```cpp
bool Guild::ModifyBankMoney(SQLTransaction& trans, uint64 amount, bool add)
{
    if (add)
        m_bankMoney += amount;
    else
    {
        // Check if there is enough money in bank.
        if (m_bankMoney < amount)
            return false;
        m_bankMoney -= amount;
    }

PreparedStatement* stmt = CharacterDatabase.GetPreparedStatement(CHAR_UPD_GUILD_BANK_MONEY);
stmt->setUInt64(0, m_bankMoney);
stmt->setUInt32(1, m_id);
trans->Append(stmt);
return true;
}
```

Une fois ceci fait un simple reBuild et une reCompilation de votre Core est nécessaire.
Le numéro des lignes n'est pas obligatoire, mais je ne saurais vous aider si vous ne respectez pas ceci.
Allez la bise ![image](https://user-images.githubusercontent.com/65762554/82668974-dabbd300-9c3a-11ea-8eb4-9106002ca77f.png)
