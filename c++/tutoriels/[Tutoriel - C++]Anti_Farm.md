# Anti Farm

### Tutoriel par @Killit5

Voici un petit tips pour lutter contre le multi boxing/farming.

Ouvrer votre fichier **Player.cpp**.
Chercher la ligne suivante, vers lignes 6750:
`bool Player::RewardHonor(Unit* victim, uint32 groupsize, int32 honor, bool pvptoken)`

Ajouter ceci :

```cpp
if(victim)
{
    if(victim->GetTypeId() == TYPEID_PLAYER)
    {
        if(GetSession()->GetRemoteAddress() == victim->ToPlayer()->GetSession()->GetRemoteAddress())
        {
            ChatHandler(GetSession()).PSendSysMessage("Vous ne pouvez pas recevoir de récompense en tuant quelqu'un avec la même adresse ip");
            return false;
        }
    }
}
```

Compiler, enjoy !
