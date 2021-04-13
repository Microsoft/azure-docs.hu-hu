---
title: Szegmensek kiegyensúlyozása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Szegmensek egyenletes elosztása a kiszolgálók között a jobb teljesítmény érdekében
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 63322fac4c6ad5b705deedcd8a80466ddd803814
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305701"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Szegmensek újraelosztása a nagy kapacitású (Citus) kiszolgálói csoportban

Az újonnan hozzáadott csomópontok kihasználása érdekében újra kell osztania az [elosztott tábla](concepts-hyperscale-distributed-data.md#shards)szegmenseit, ami azt jelenti, hogy a meglévő csomópontokból származó szegmensek áthelyezése az újakra történik.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>Annak megállapítása, hogy a kiszolgálócsoport igényel-e újraelosztást

A Azure Portal megmutathatja, hogy az Adatelosztás egyenlő-e a kiszolgálócsoport munkavégző csomópontjai között. Ha szeretné megtekinteni, ugorjon a szegmensek **átszervezése lapra a kiszolgáló csoport kezelése** menüben.  Ha az adat a feldolgozók között elferdítve van, akkor az üzenet **újraelosztása ajánlott**, valamint az egyes csomópontok méretének listája.

Ha az adathalmaz már egyensúlyban van, akkor az üzenet **újrakiegyensúlyozása jelenleg nem ajánlott**.

## <a name="run-the-shard-rebalancer"></a>A szegmens-újraegyensúly futtatása

A szegmens-újraegyensúly elindításához kapcsolódnia kell a kiszolgálócsoport koordinátori csomópontjára, és futtatnia kell a [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) SQL-függvényt az elosztott táblákon. A függvény a (z) argumentumban megnevezett tábla együttes [elhelyezés](concepts-hyperscale-colocation.md) csoportjában lévő összes táblát újraegyenlíti. Így nem kell minden elosztott táblához meghívnia a függvényt, csak egy reprezentatív táblán kell meghívnia az egyes közös helyek csoportjából.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>A figyelő egyensúlyának figyelése

Ha a megkezdése után szeretné megtekinteni a kiegyensúlyozó, térjen vissza a Azure Portal. Nyissa  meg a szegmensek **átszervezése lapot a kiszolgálói csoportok kezelése** lapon. Ekkor megjelenik az üzenet **újrakiegyensúlyozása** két táblázattal együtt.

Az első táblázat a csomópontokon áthelyezett vagy onnan kifelé irányuló szegmensek számát jeleníti meg, például: "6 – 24 áthelyezve". A második táblázat az adatbázis-tábla előrehaladását mutatja: név, szegmensek száma érintett, az adatméret érintett, és az állapot kiegyenlítő állapota.

A lap frissítéséhez kattintson a **frissítés** gombra. Ha a kiegyensúlyozás befejeződött, az újbóli elmondás után a **kiegyenlítő nem ajánlott**.

## <a name="next-steps"></a>Következő lépések

- További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
- [Kiszolgáló csoport méretezése](howto-hyperscale-scale-grow.md) felfelé vagy lefelé
- Lásd a [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) referenciaanyagok
