---
title: Csomópontok – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ismerje meg, hogy milyen típusú csomópontok és táblák szerepelnek a Azure Database for PostgreSQLban lévő kiszolgálócsoportban.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: b3eda2c8de8319552f32938f20ff98af0e0a49fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91314827"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>Csomópontok és táblák a Azure Database for PostgreSQLban – nagy kapacitású (Citus)

## <a name="nodes"></a>Csomópontok

A nagy kapacitású (Citus) üzemeltetési típusa lehetővé teszi, hogy Azure Database for PostgreSQL-kiszolgálók (más néven csomópontok) összehangolják egymással a "shared nothing" architektúrában. A kiszolgálócsoport csomópontjai közösen több adattal rendelkeznek, és több CPU-magot használnak, mint amennyit egyetlen kiszolgálón lehet használni. Az architektúra azt is lehetővé teszi, hogy az adatbázis méretezhető legyen, ha további csomópontokat ad hozzá a kiszolgálói csoporthoz.

### <a name="coordinator-and-workers"></a>Koordinátor és feldolgozók

Minden kiszolgálócsoport rendelkezik egy koordinátori csomóponttal és több feldolgozóval. Az alkalmazások a koordinátori csomópontba küldik a lekérdezéseket, amelyek továbbítják az érintett munkavállalóknak, és felhalmozzák eredményeiket. Az alkalmazások nem tudnak közvetlenül csatlakozni a feldolgozókhoz.

A nagy kapacitású (Citus) lehetővé teszi, hogy az adatbázis rendszergazdája a különböző munkavégző csomópontokon lévő különböző sorok tárolására szolgáló táblákat *terjesszen* . Az elosztott táblák a nagy kapacitású (Citus) teljesítményének kulcsa. A táblázatok nem terjeszthetők ki teljes mértékben a koordinátor csomóponton, és nem tudják kihasználni a több gépen történő párhuzamosságot.

Az elosztott táblákon lévő összes lekérdezés esetében a koordinátor vagy egyetlen munkavégző csomópontra irányítja, vagy parallelizes azt attól függően, hogy a szükséges adat egyetlen vagy több csomóponton van-e. A koordinátor eldönti, hogy mi a teendő a metaadatokkal folytatott konzultációk során. Ezek a táblák nyomon követik a munkavégző csomópontok DNS-neveit és állapotát, valamint az adateloszlást a csomópontok között.

## <a name="table-types"></a>Táblázattípusok

A nagy kapacitású-(Citus-) kiszolgálócsoport háromféle táblával rendelkezik, amelyek mindegyike eltérően van tárolva a csomópontokon, és különböző célokra szolgál.

### <a name="type-1-distributed-tables"></a>1. típus: elosztott táblák

Az első típus és a leggyakoribb érték az elosztott táblák. Úgy tűnik, hogy az SQL-utasításoknak normális táblák, de horizontálisan particionálva vannak a munkavégző csomópontok között. Ez azt jelenti, hogy a tábla sorait különböző csomópontok tárolják, a szilánkok nevű töredék táblákban.

A nagy kapacitású (Citus) nem csak az SQL-, hanem a DDL-utasításokat futtatja a fürtben.
Az elosztott táblák sémájának módosítása a tábla összes munkaterületének a feldolgozók közötti frissítéséhez.

#### <a name="distribution-column"></a>Terjesztési oszlop

A nagy kapacitású (Citus) algoritmusos horizontális skálázást használ a sorok szegmensekhez rendeléséhez. A hozzárendelés determinisztikus módon a Distribution oszlop nevű Table oszlop értéke alapján történik. A fürt rendszergazdájának meg kell jelölnie ezt az oszlopot egy tábla terjesztésekor.
A megfelelő választás a teljesítmény és a funkcionalitás szempontjából fontos.

### <a name="type-2-reference-tables"></a>2. típus: hivatkozási táblák

A hivatkozási tábla olyan elosztott tábla, amelynek teljes tartalma egyetlen szegmensbe van koncentrálva. A szegmens minden feldolgozón replikálódik. A munkavégzők lekérdezései helyileg is hozzáférhetnek a hivatkozási információkhoz anélkül, hogy egy másik csomóponttól származó sorokat kellene megkérniük. A hivatkozási táblák nem rendelkeznek terjesztési oszlopokkal, mert nem kell Megkülönböztetniük a különálló szegmenseket egymás után.

A hivatkozási táblák általában kicsik, és a rendszer a munkavégző csomóponton futó lekérdezésekhez kapcsolódó adatokat tárolja. Ilyen például az enumerált értékek, például a megrendelés állapota vagy a termékkategóriák.

### <a name="type-3-local-tables"></a>3. típus: helyi táblák

Ha a nagy kapacitású (Citus) használja, a csatlakozáshoz használt koordinátori csomópont egy normál PostgreSQL-adatbázis. Létrehozhat hagyományos táblákat a koordinátoron, és megadhatja, hogy ne legyenek szétválasztva.

A helyi táblák jó jelöltje olyan kis adminisztrációs táblák, amelyek nem vesznek részt a csatlakoztatási lekérdezésekben. Ilyen például az alkalmazások bejelentkezésének és hitelesítésének felhasználói táblázata.

## <a name="shards"></a>Szilánkok

Az előző szakasz azt ismerteti, hogy az elosztott táblák hogyan tárolódnak a munkavégző csomópontokon. Ez a szakasz a technikai részleteket ismerteti.

A `pg_dist_shard` koordinátor metaadat-táblázata a rendszeren lévő összes elosztott tábla minden szegmensének sorát tartalmazza. A sor egy egész számmal rendelkező szegmens-AZONOSÍTÓra illeszkedik egy kivonatoló térben (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Ha a koordinátor-csomópont meg szeretné állapítani, hogy melyik szegmens tartalmaz egy sort `github_events` , akkor a sorban lévő eloszlás oszlop értékét kivonata. Ezután a csomópont ellenőrzi, hogy a \' szegmens k tartománya tartalmazza-e a kivonatos értéket. A tartományok úgy vannak meghatározva, hogy a kivonatoló függvény képe a különálló Unió legyen.

### <a name="shard-placements"></a>Szilánkok

Tegyük fel, hogy a szegmens 102027 a kérdéses sorhoz van társítva. A sor olvasása vagy írása egy `github_events_102027` feldolgozónak nevezett táblában történik. Melyik feldolgozót? Ezt teljes mértékben a metaadat-táblázatok határozzák meg. A szegmensek feldolgozóra való hozzárendelését nevezzük a szilánkok elhelyezésének.

A koordinátor csomópontja a lekérdezéseket olyan töredékekre írja, amelyek az adott táblákra hivatkoznak, például a `github_events_102027` megfelelő munkatársain futtatják ezeket a töredékeket. Íme egy példa arra, hogy egy lekérdezés a háttérben fusson, hogy megtalálja a csomópontot, amely a 102027-es szegmens AZONOSÍTÓját tárolja.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>Következő lépések

- [Az alkalmazás típusának meghatározása](concepts-hyperscale-app-type.md) az adatmodellezés előkészítéséhez
