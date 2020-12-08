# Changer la faction d'une créature

### Tutoriel par Thanatos

Bonjour,

A la suite d'une demande en MP je me permet de vous partager cette simple ligne de code qui va vous permettre de passer votre créature en faction neutre :

```sql
SET @Entry := 55000;
SET @Faction = 35;
UPDATE creature_template SET faction = @Faction WHERE entry = @Entry;
```

Il vous suffis de modifier l'entry 55000 pour mettre le votre et de changer la faction par celle que vous souhaitez.

Bonne journée à vous et à la revoyure.

**Horde & Alliance**

- Amical = **35**
- Ennemie = **168**
- Non ami = **14**
- Neutre = **7**

**Faction Horde**

- Orgrimmar = **85**
- Les Pitons-du-Tonnerre = **105**
- Fossoyeuse = **68**
- Lune-d'Argent = **1602**

**Faction Alliance**

- Hurlevent = **11**
- Darnassus = **79**
- Exodar = **1638**
- Forgefer = **55**
