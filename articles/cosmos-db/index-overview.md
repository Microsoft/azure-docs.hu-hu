---
title: Indexelés az Azure Cosmos DB-ben
description: Ismerje meg, hogy az indexelés hogyan működik Azure Cosmos DBban, különböző típusú indexek, például a tartomány, a térbeli és az összetett indexek támogatottak.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: b7349a08b93810dcc3befd6058302d6c4573ab8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019210"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexelés az Azure Cosmos DB-ben – Áttekintés
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB egy séma-agnosztikus adatbázis, amely lehetővé teszi az alkalmazáson belüli iterációt anélkül, hogy a sémát vagy az indexelést kellene foglalkoznia. Alapértelmezés szerint a Azure Cosmos DB automatikusan indexel minden tulajdonságot a [tároló](account-databases-containers-items.md#azure-cosmos-containers) összes eleméhez anélkül, hogy sémát kellene meghatároznia vagy másodlagos indexeket kellene konfigurálnia.

A cikk célja annak ismertetése, hogy az Azure Cosmos DB hogyan indexeli az adatokat, illetve hogy miként használja az indexeket a lekérdezési teljesítmény javításához. Azt javasoljuk, hogy az [indexelési szabályzatok](index-policy.md)testreszabásának megismerése előtt folytassa ezt a szakaszt.

## <a name="from-items-to-trees"></a>Elemektől a fákig

Minden alkalommal, amikor egy elem egy tárolóban tárolódik, a tartalma JSON-dokumentumként van kialakítva, majd egy faszerkezetbe konvertálva. Ez azt jelenti, hogy az adott elem minden tulajdonsága egy fa csomópontjaiként lesz megjelenítve. A rendszer egy pszeudo-főcsomópontot hoz létre szülőként az elem összes első szintű tulajdonságához. A levél csomópontjai tartalmazzák az elemek tényleges skaláris értékeit.

Példaként tekintse meg ezt az tételt:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

A következő fa fogja képviselni:

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="Az előző elem faszerkezetként van ábrázolva" border="false":::

Vegye figyelembe, hogy a tömbök a fában vannak kódolva: a tömb minden bejegyzése egy köztes csomópontot kap, amely a tömbben (0, 1 stb.) lévő bejegyzés indexével van megjelölve.

## <a name="from-trees-to-property-paths"></a>A fák és a tulajdonságok közötti útvonalakig

Ennek az az oka, hogy Azure Cosmos DB átalakítja az elemeket a fákba, mivel lehetővé teszi a tulajdonságok hivatkozását a fák elérési útjain belül. Ha egy tulajdonság elérési útját szeretné lekérni, a fában áthaladhat a gyökér csomópontból a tulajdonságba, és összefűzheti az egyes bejárt csomópontok feliratait.

Itt láthatók a fent ismertetett példában szereplő egyes tulajdonságok elérési útjai:

- /Locations/0/Country: "Németország"
- /Locations/0/City: "Berlin"
- /Locations/1/Country: "Franciaország"
- /Locations/1/City: "Párizs"
- /Headquarters/Country: "Belgium"
- /Headquarters/Employees: 250
- /exports/0/City: "Moszkva"
- /exports/1/City: "Athén"

Egy elem írásakor Azure Cosmos DB hatékonyan indexeli az egyes tulajdonságok elérési útját és a hozzá tartozó értéket.

## <a name="types-of-indexes"></a><a id="index-types"></a>Indexek típusai

A Azure Cosmos DB jelenleg három típusú indexet támogat. A tárgymutató-típusokat az indexelési szabályzat definiálásakor konfigurálhatja.

### <a name="range-index"></a>Tartomány indexe

A **Range** index egy megrendelt fastruktúrán alapul. A tartomány indexének típusát a következőhöz használhatja:

- Esélyegyenlőségi lekérdezések:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Egyenlőségi egyezés egy tömb elemnél
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Tartomány lekérdezései:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (a,,,, `>` `<` `>=` `<=` `!=` )

- Tulajdonság meglétének ellenőrzése:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Karakterláncrendszer-függvények:

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
   ```

- `ORDER BY` lekérdezések

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` lekérdezések

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

A tartomány-indexek a skaláris értékeken (String vagy Number) is használhatók. Az újonnan létrehozott tárolók alapértelmezett indexelési szabályzata minden sztring vagy szám esetében tartományindexeket kényszerít. A tartomány-indexek konfigurálásával kapcsolatos további információkért lásd: [tartomány-indexelési házirend – példák](how-to-manage-indexing-policy.md#range-index)

### <a name="spatial-index"></a>Térbeli index

A **térbeli** indexek hatékony lekérdezéseket tesznek lehetővé térinformatikai objektumokon, például pontokon, vonalakon, sokszögeken és több sokszögen. Ezek a lekérdezések ST_DISTANCE, ST_WITHIN, ST_INTERSECTS kulcsszavakat használnak. A következő példák a térbeli index típusát használják:

- Földrajzi távolsági lekérdezések:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Térinformatika a lekérdezéseken belül:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] })
   ```

- Térinformatikai metsző lekérdezések:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

A térbeli indexek megfelelően formázott [GeoJSON](./sql-query-geospatial-intro.md) -objektumokon is használhatók. A pontok, Linestring, sokszögek és többsokszögek jelenleg támogatottak. Ha ezt az indexet szeretné használni, állítsa be a `"kind": "Range"` tulajdonságot az indexelési házirend konfigurálásakor. A térbeli indexek konfigurálásával kapcsolatos további információkért lásd: [térbeli indexelési házirend – példák](how-to-manage-indexing-policy.md#spatial-index)

### <a name="composite-indexes"></a>Összetett indexek

Az **összetett** indexek nagyobb hatékonyságot biztosítanak, ha több mezőn végez műveleteket. Az összetett index típusát a következőhöz használhatja:

- `ORDER BY` több tulajdonság lekérdezései:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Lekérdezések szűrővel és `ORDER BY` . Ezek a lekérdezések összetett indexet használhatnak, ha a Filter tulajdonságot hozzáadja a `ORDER BY` záradékhoz.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Lekérdezés két vagy több tulajdonsággal rendelkező szűrővel, ahol legalább egy tulajdonság egy egyenlőségi szűrő.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Mindaddig, amíg az egyik szűrési predikátum az egyik indexelési típust használja, a lekérdezési motor kiértékeli, hogy először a REST ellenőrzése előtt. Ha például SQL-lekérdezéssel rendelkezik, például: `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* A fenti lekérdezés először szűrni fogja azokat a bejegyzéseket, ahol a firstName = "Andrew" kifejezést használja az index használatával. Ezután továbbítja az összes firstName = "Andrew" bejegyzést egy későbbi folyamaton keresztül, hogy kiértékelje a tartalmazza a szűrő predikátumát.

* Felgyorsíthatja a lekérdezéseket, és elkerülheti a tárolók teljes vizsgálatát, ha olyan függvényeket használ, amelyek nem használják az indexet (például tartalmazza) az indexet használó további szűrési predikátumok hozzáadásával. A Filter záradékok sorrendje nem fontos. A lekérdezési motor kideríti, hogy mely predikátumok szelektívek, és ennek megfelelően futtatják a lekérdezést.

Az összetett indexek konfigurálásának megismeréséhez tekintse meg az [összetett indexelési házirend példáit](how-to-manage-indexing-policy.md#composite-index) .

## <a name="querying-with-indexes"></a>Lekérdezés indexekkel

Az adatindexelés során kinyert elérési utak megkönnyítik az index keresését egy lekérdezés feldolgozásakor. Az `WHERE` indexelt elérési utak listáját tartalmazó lekérdezés záradékának egyeztetésével nagyon gyorsan azonosíthatja a lekérdezési predikátumnak megfelelő elemeket.

Vegyük például a következő lekérdezést: `SELECT location FROM location IN company.locations WHERE location.country = 'France'` . A lekérdezési predikátum (elemek szűrése, ahol bármely hely "Franciaország" országa/régiója) megfelel az alábbi piros színnel jelölt elérési útnak:

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="Megadott elérési út megfeleltetése egy fában belül" border="false":::

> [!NOTE]
> Egy olyan `ORDER BY` záradék, amelyet egy adott tulajdonság megrendelése *mindig* egy tartomány indexre van szüksége, és sikertelen lesz, ha az általa hivatkozott elérési út nem rendelkezik ilyennel. Hasonlóképpen, a `ORDER BY` több tulajdonság által megrendelést igénylő lekérdezésnek *mindig* összetett indexre van szüksége.

## <a name="next-steps"></a>Következő lépések

Az indexeléssel kapcsolatos további információkért olvassa el a következő cikkeket:

- [Indexelési szabályzat](index-policy.md)
- [Az indexelési szabályzat kezelése](how-to-manage-indexing-policy.md)