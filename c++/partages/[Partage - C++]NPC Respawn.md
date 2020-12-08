# NPC Respawn

## Partage par @Shard-MW



Bonjour/bonsoir !  

Je tenais à vous partager un petit Script C++ que j'ai fait et qui pourrait en satisfaire plus d'un ! Il s'agit d'un Script Creature qui Respawn la zone dans laquelle il se trouve et meurt (pour éviter que les joueurs en abusent). 

De ce fait, vous pouvez y mettre un temps de recharge (ou plutôt un temps de respawn)

<u>/!\ Attention /!\</u> Ce Script est optimisé pour fonctionner avec la **rev.63** de **TrinityCore**.



```cpp
#include "ScriptPCH.h"

class NPC_Respawn : public CreatureScript
{
public:

	NPC_Respawn()
		: CreatureScript("NPC_Respawn") {}

	static bool OnGossipHello(Player* player, Creature* creature)
	{

		AddGossipItemFor(player, 5, "Respawn la zone", GOSSIP_SENDER_MAIN, 333);
		AddGossipItemFor(player, 7, "Ce cristal a un temps de recharge de 5 minutes, utilisez le soigneusement.", GOSSIP_SENDER_MAIN, 334);

		SendGossipMenuFor(player, DEFAULT_GOSSIP_MESSAGE, creature->GetGUID());

		return true;
	}

	static void SendDefaultMenu(Player* player, Creature* creature, uint32 action)
	{
		// Interdit en combat
		if (player->IsInCombat())
		{
			CloseGossipMenuFor(player);
			creature->Whisper("Vous etes en combat !", LANG_UNIVERSAL, player, true);
			return;
		}

		switch (action)
		{
			case 333:  //Respawn
			{
				CellCoord p(Trinity::ComputeCellCoord(player->GetPositionX(), player->GetPositionY()));
				Cell cell(p);
				cell.SetNoCreate();

				Trinity::RespawnDo u_do;
				Trinity::WorldObjectWorker<Trinity::RespawnDo> worker(player, u_do);

				TypeContainerVisitor<Trinity::WorldObjectWorker<Trinity::RespawnDo>, GridTypeMapContainer > obj_worker(worker);
				cell.Visit(p, obj_worker, *player->GetMap(), *player, player->GetGridActivationRange());

				// Le npc meurt, retirez cette ligne si vous voulez qu'un joueur puisse Respawn à l'infini
				player->Kill(creature);

				player->PlayerTalkClass->SendCloseGossip();
			}
			break;

			case 334: //msg
			{
				player->PlayerTalkClass->SendCloseGossip();
				break;
			}
		}
	}

	static bool OnGossipSelect(Player* player, Creature* creature, uint32 sender, uint32 action)
	{
		// Main menu
		if (sender == GOSSIP_SENDER_MAIN)
		{
			SendDefaultMenu(player, creature, action);
		}

		return true;
	}


	struct npcRespawnAI : public ScriptedAI
	{
		npcRespawnAI(Creature* creature)
			: ScriptedAI(creature) { }
		
		bool GossipHello(Player* player) override
		{
			return OnGossipHello(player, me);
		}
		
		bool GossipSelect(Player* player, uint32 menuId, uint32 gossipListId) override
		{
			uint32 sender = player->PlayerTalkClass->GetGossipOptionSender(gossipListId);
			uint32 action = player->PlayerTalkClass->GetGossipOptionAction(gossipListId);
			return OnGossipSelect(player, me, sender, action);
		}
	};

	CreatureAI* GetAI(Creature* creature) const override
	{
		return new npcRespawnAI(creature);
	}

};

void AddSC_NPC_Respawn()
{
	new NPC_Respawn;
}
```

Pour le temps de recharge, une fois que vous aviez créé la créature dans la table **'creature_template'** de votre BD avec la valeur **"NPC_Respawn"** dans la colonne **'ScriptName'** et que vous l'aviez spawn en jeu, vous avez juste à exécuter ce script SQL dans votre world.

```sql
UPDATE creature SET spawntimesecs = 300 WHERE id = ID_DE_VOTRE_NPC_RESPAWN;
```

(300 équivaut à 5 minutes, vous pouvez mettre ce que vous voulez ici).  


Si vous voulez qu'un ou plusieurs NPC_Respawn aient un temps de recharge différent en fonction de leur zone, récupérez leur GUID via un .npc info en jeu et exécuter ce script SQL :

```sql
UPDATE creature SET spawntimesecs = 300 WHERE guid = GUID_DE_VOTRE_NPC_RESPAWN;
```



Voilà tout, c'est pas plus compliqué que ca !
J'espère que vous lui trouverez une utilité.



Cordialement,
Shard.
