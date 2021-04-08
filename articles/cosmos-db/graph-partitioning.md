---
title: Adatparticionálás Azure Cosmos DB Gremlin API-ban
description: Megtudhatja, hogyan használható particionált gráf a Azure Cosmos DBban. Ez a cikk a particionált gráfokra vonatkozó követelményeket és ajánlott eljárásokat is ismerteti.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 4d76bdcb385ed2fe4b8a697f24187b8e3d5addbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988748"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Particionált gráfok használata az Azure Cosmos DB-ben
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

A Azure Cosmos DB Gremlin API egyik fő funkciója a horizontális skálázási képességgel rendelkező nagyméretű diagramok kezelése. A tárolók egymástól függetlenül méretezhetők a tárolás és az átviteli sebesség tekintetében. Létrehozhat olyan Azure Cosmos DB tárolókat, amelyek automatikusan méretezhetők a Graph-adatmennyiség tárolására. Az adatértékek a megadott **partíciós kulcs** alapján automatikusan egyensúlyban vannak.

A particionálás belsőleg történik, ha a tároló várhatóan 20 GB-nál több méretet tárol, vagy ha több mint 10 000 adategységet szeretne lefoglalni másodpercenként (RUs). A rendszer a megadott partíciós kulcs alapján automatikusan particionálja az adattárolást. A partíciós kulcs megadása kötelező, ha gráf-tárolókat hoz létre a Azure Portal vagy a Gremlin-illesztőprogramok 3. x vagy újabb verziójából. A partíciós kulcs nem szükséges, ha 2. x vagy alacsonyabb verziójú Gremlin-illesztőprogramot használ.

A [Azure Cosmos db particionálási mechanizmussal](partitioning-overview.md) azonos általános elvek vonatkoznak néhány, az alábbiakban ismertetett gráf-specifikus optimalizálásra.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Gráf particionálás." border="false":::

## <a name="graph-partitioning-mechanism"></a>Gráf particionálási mechanizmusa

Az alábbi irányelvek azt írják le, hogyan működik a Azure Cosmos DB particionálási stratégiája:

- **A csúcspontokat és az éleket JSON-dokumentumként tárolja a rendszer**.

- **A csúcspontok partíciós kulcsot igényelnek**. Ez a kulcs határozza meg, hogy a rendszer melyik partíción tárolja a csúcspontot egy kivonatoló algoritmus használatával. A Partition Key tulajdonság neve az új tároló létrehozásakor van meghatározva, és a formátuma a (z `/partitioning-key-name` ):.

- Az **élek a forrás csúcspontjának megfelelően lesznek tárolva**. Más szóval a partíciós kulcs minden csúcspontján meghatározza, hogy a rendszer hol tárolja azokat a kimenő élekkel együtt. Ez az optimalizálás elkerüli a több partíciós lekérdezések használatát a `out()` diagramos lekérdezésekben felhasznált kardinális használatakor.

- **Az élek a csúcspontokra mutató hivatkozásokat tartalmaznak**. Az összes élek tárolása a csúcspontok partíciós kulcsaival és azonosítói történik. Ez a számítás lehetővé teszi, hogy az összes `out()` irány lekérdezése mindig hatókörrel rendelkező particionált lekérdezés legyen, és ne egy vak, több partíciós lekérdezést.

- **A Graph-lekérdezéseknek partíciós kulcsot kell megadniuk**. A Azure Cosmos DB vízszintes particionálásának teljes kihasználása érdekében a partíciós kulcsot meg kell adni, ha egyetlen csúcspont van kiválasztva, amikor lehetséges. A következő lekérdezések egy vagy több csúcspont kiválasztására szolgálnak egy particionált gráfban:

    - `/id` a és a `/label` nem támogatottak a GREMLIN API-hoz tartozó tárolók partíciós kulcsaként.


    - Jelölje ki az azonosító alapján egy csúcspontot, majd **a `.has()` lépés használatával adja meg a Partition Key tulajdonságot**:

        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```

    - Csúcspont kiválasztása **egy olyan rekord megadásával, amely tartalmazza a partíciós kulcs értékét és azonosítóját**:

        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```

    - **Rekordok-értékek és-azonosítók tömbjét** határozza meg:

        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```

    - A csúcspontok és az azonosítóik kiválasztásával **megadhatja a partíciós kulcs értékeinek listáját**:

        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - A **partíciós stratégia** használata a lekérdezés elején, valamint egy partíció megadása a Gremlin-lekérdezés további részének hatóköréhez:

        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Ajánlott eljárások particionált gráfok használatakor

A következő irányelvek segítségével biztosíthatja a teljesítményt és a méretezhetőséget, ha korlátlan számú tárolóval rendelkező particionált gráfokat használ:

- **A csúcspont lekérdezésekor mindig meg kell adni a partíciós kulcs értékét**. A teljesítmény eléréséhez a csúcspont egy ismert partícióból való beolvasása. Az összes további érintkezési művelet mindig a partícióra lesz kiterjedően, mivel a szegélyek hivatkozási azonosítót és partíciós kulcsot tartalmaznak a cél csúcspontokra.

- **Ha lehetséges, használja a kimenő irányt az élek lekérdezéséhez**. A fentiekben leírtaknak megfelelően a rendszer a széleket a kimenő irányú forrás csúcspontokkal tárolja. Így a több partíciós lekérdezések kiszolgálása a lehető legkisebbre csökkenthető, ha az adatforrásokat és a lekérdezéseket szem előtt tartva tervezték meg. Éppen ellenkezőleg, a `in()` lekérdezés mindig drága kiugró lekérdezés lesz.

- **Olyan partíciós kulcsot válasszon, amely egyenletesen osztja el az adatelosztást a partíciók között**. Ez a döntés nagy mértékben függ a megoldás adatmodelljétől. További információ a megfelelő partíciós kulcs létrehozásáról a [particionálás és méretezés Azure Cosmos DBban című részében található](partitioning-overview.md).

- **Optimalizálja a lekérdezéseket a partíció határain belüli** adatgyűjtéshez. Az optimális particionálási stratégia a lekérdezési mintákhoz igazodik. Az adatok egyetlen partícióból való beszerzését biztosító lekérdezések biztosítják a lehető legjobb teljesítményt.

## <a name="next-steps"></a>Következő lépések

Ezután folytassa a következő cikkek elolvasásával:

* Tudnivalók a [particionálásról és a méretezésről Azure Cosmos db](partitioning-overview.md).
* Ismerje meg a [GREMLIN API Gremlin-támogatását](gremlin-support.md).
* Ismerje meg a [GREMLIN API bevezetését](graph-introduction.md).
