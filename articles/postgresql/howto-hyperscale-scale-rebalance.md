---
title: Szegmensek kiegyensúlyozása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Szegmensek egyenletes elosztása a kiszolgálók között a jobb teljesítmény érdekében
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026386"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Szegmensek újraelosztása a nagy kapacitású (Citus) kiszolgálói csoportban

Az újonnan hozzáadott csomópontok kihasználása érdekében újra kell osztania az [elosztott tábla](concepts-hyperscale-distributed-data.md#shards)szegmenseit, ami azt jelenti, hogy a meglévő csomópontokból származó szegmensek áthelyezése az újakra történik. Először ellenőrizze, hogy az új feldolgozók sikeresen befejezték-e az üzembe helyezést. Ezután indítsa el a szegmens-újraelosztást a psql-mel és a-t futtató fürt-koordinátor csomóponthoz való csatlakozással:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) függvény a (z) argumentumban található tábla együttes [elhelyezés](concepts-hyperscale-colocation.md) csoportjában lévő összes táblát újraegyenlíti. Így nem kell minden elosztott táblához meghívnia a függvényt, csak egy reprezentatív táblán kell meghívnia az egyes közös helyek csoportjából.

**Következő lépések**


- További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
- [Kiszolgáló csoport méretezése](howto-hyperscale-scale-grow.md) felfelé vagy lefelé
- Lásd a [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) referenciaanyagok
