---
title: 'Gyors útmutató: kiszolgálócsoport létrehozása – nagy kapacitású (Citus) – Azure Database for PostgreSQL'
description: Gyors útmutató elosztott táblák létrehozásához és lekérdezéséhez Azure Database for PostgreSQL nagy kapacitású (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/17/2020
ms.openlocfilehash: 03a6e927a074067e85f1a3adca38cae386d1af38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026216"
---
# <a name="quickstart-create-a-hyperscale-citus-server-group-in-the-azure-portal"></a>Gyors útmutató: nagy kapacitású-(Citus-) kiszolgálócsoport létrehozása a Azure Portalban

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálói csoportot a Azure Portal használatával. Tekintse át az elosztott adatmennyiségeket: a csomópontok közötti horizontális skálázást, a mintaadatok betöltését és a több csomóponton futtatott lekérdezések futtatását.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Táblák létrehozása és terjesztése

Miután csatlakozott a nagy kapacitású koordinátori csomóponthoz a psql használatával, elvégezhet néhány alapvető feladatot.

A nagy kapacitású-(Citus-) kiszolgálókon három típusú tábla található:

- Elosztott vagy felosztott táblák (a teljesítmény és a párhuzamos skálázásának elősegítése érdekében)
- Hivatkozási táblák (több másolat is karbantartva)
- Helyi táblák (gyakran használt belső felügyeleti táblákhoz)

Ebben a rövid útmutatóban elsősorban az elosztott táblákra koncentrálunk, és ismerkedjen meg velük.

A következő adatmodell egyszerű: felhasználó-és esemény-adatok a GitHubról. Az események közé tartozik az elágazás létrehozása, a git-véglegesítés egy szervezettel kapcsolatban, és így tovább.

Miután kapcsolódott a psql-on keresztül, hozzuk létre a táblázatokat. A psql-konzolon futtassa a következőket:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

A `payload` mezőhöz `github_events` JSONB adattípus tartozik. A JSONB a postgres bináris formátumú JSON-adattípusa. Az adattípussal könnyedén tárolhat egy rugalmas sémát egyetlen oszlopban.

Az postgres létrehozhat egy `GIN` indexet ezen a típuson, amely a benne található összes kulcsot és értéket indexeli. Az indextel gyorsan és egyszerűen lekérdezheti a hasznos adatokat különböző feltételekkel. Nézzük meg, és hozzon létre néhány indexet az adatbetöltése előtt. A psql-ben:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Ezután ezeket a postgres táblázatokat a koordinátor csomóponton kell megadnia, és meg kell mondanom a nagy kapacitású (Citus). Ehhez le kell futtatni egy lekérdezést minden olyan táblára vonatkozóan, amely megadja, hogy a kulcs a szegmensbe kerüljön. Az aktuális példában az eseményeket és a felhasználók táblát is a következőre fogjuk bemutatni `user_id` :

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

Készen áll az betöltésre. A psql továbbra is kipróbálhatja a fájlok letöltését:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Ezután töltse be az adatok a fájlokból az elosztott táblákba:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Lekérdezések futtatása

Itt az ideje, hogy a szórakoztató rész ténylegesen futtasson néhány lekérdezést. Kezdjük egy egyszerű `count (*)` megtekinteni a betöltött adatmennyiséget:

```sql
SELECT count(*) from github_events;
```

Ez szépen működött. Egy kicsit vissza fogunk térni erre a fajta összesítésre, de most nézzük meg néhány más lekérdezést. A JSONB `payload` oszlopon belül van egy jó kis mennyiség, de az esemény típusa alapján változhat. `PushEvent` az események olyan méretet tartalmaznak, amely tartalmazza a leküldés különböző véglegesítő feltételeit. A felhasználható, hogy megkeresse az óránkénti véglegesítés teljes számát:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Eddig a lekérdezések kizárólag a GitHub \_ -eseményeket érintették, de ezeket az információkat a GitHub- \_ felhasználókkal kombináljuk. Mivel a felhasználókat és az eseményeket ugyanazon az azonosítón () osztottuk fel `user_id` , a megfelelő felhasználói azonosítókkal rendelkező táblák sorai ugyanazon adatbázis-csomópontokon [helyezkednek](concepts-hyperscale-colocation.md) el, és könnyedén csatlakoztathatók.

Ha bekapcsoljuk a szolgáltatást `user_id` , a nagy kapacitású (Citus) a munkavégző csomópontokon párhuzamosan hajthatja végre a csatlakozás végrehajtását a szegmensekben. Például keresse meg a legtöbb tárházat létrehozó felhasználókat:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy kiszolgálócsoport számára. Ha nem várható, hogy a jövőben szüksége lesz ezekre az erőforrásokra, törölje a kiszolgálót. A kiszolgálócsoport **Áttekintés** lapján kattintson a **Törlés** gombra. Amikor a rendszer rákérdez egy előugró oldalra, erősítse meg a kiszolgálócsoport nevét, és kattintson a végleges **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan építhet ki egy nagy kapacitású-(Citus-) kiszolgáló csoportot. Csatlakoztatta azt a psql-hoz, létrehozott egy sémát és egy elosztott adatkészletet.

- A [skálázható több-bérlős alkalmazások létrehozásához](./tutorial-design-database-hyperscale-multi-tenant.md) kövesse az oktatóanyagot
- A kiszolgálócsoport legjobb [kezdeti méretének](howto-hyperscale-scale-initial.md) meghatározása
