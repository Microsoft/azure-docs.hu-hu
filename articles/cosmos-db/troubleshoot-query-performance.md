---
title: Az Azure Cosmos DB használatakor felmerülő lekérdezési hibák elhárítása
description: Ismerje meg, hogyan azonosíthatja, diagnosztizálhatja és elháríthatja a Azure Cosmos DB SQL-lekérdezéssel kapcsolatos problémákat.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/02/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: d50893fc3bf5d890efbdc1f5b59cf52f35d91a15
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475726"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Az Azure Cosmos DB használatakor felmerülő lekérdezési hibák elhárítása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk részletesen ismerteti a Azure Cosmos DB-lekérdezések hibaelhárításának általános javasolt megközelítését. Habár nem veszi figyelembe a jelen cikkben ismertetett lépéseket a lehetséges lekérdezési problémákkal kapcsolatos teljes körű védekezéssel, a leggyakoribb teljesítménybeli tippeket is itt találja. Ezt a cikket az Azure Cosmos DB Core (SQL) API lassú vagy költséges lekérdezéseinek kezdeti hibaelhárításához érdemes használni. A [diagnosztikai naplókat](cosmosdb-monitor-resource-logs.md) is használhatja a lassú vagy jelentős mennyiségű átviteli sebességet használó lekérdezések azonosítására. Ha a MongoDB Azure Cosmos DB API-ját használja, használja a [Azure Cosmos db API-ját a MongoDB-lekérdezés hibaelhárítási útmutatója](mongodb-troubleshoot-query.md)

A Azure Cosmos DB lekérdezési optimalizálásait az alábbiak szerint széles körben kategorizáljuk:

- Optimalizálások, amelyek csökkentik a lekérdezésre vonatkozó kérési egység (RU) díját
- Optimalizálások, amelyek csak csökkentik a késést

Ha csökkenti egy lekérdezés RU-díját, általában a késést is csökkenti.

Ez a cikk példákat tartalmaz, amelyeket újra létrehozhat a [táplálkozási adatkészlet](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)használatával.

## <a name="common-sdk-issues"></a>Általános SDK-problémák

Az útmutató elolvasása előtt érdemes megfontolni az olyan általános SDK-problémákat, amelyek nem kapcsolódnak a lekérdezési motorhoz.

- Kövesse az alábbi [SDK-teljesítménnyel kapcsolatos tippeket](performance-tips.md).
    - [A .NET SDK hibaelhárítási útmutatója](troubleshoot-dot-net-sdk.md)
    - [A Java SDK hibaelhárítási útmutatója](troubleshoot-java-sdk-v4-sql.md)
- Az SDK lehetővé teszi a beállítását a `MaxItemCount` lekérdezésekhez, de nem adhat meg minimális számú tételt.
    - A kódnak minden oldalméret, nulláról a értékre kell kezelnie `MaxItemCount` .
- Időnként előfordulhat, hogy a lekérdezések üres lapokkal is rendelkezhetnek, még akkor is, ha egy jövőbeli oldal eredményei vannak. Ennek oka a következő lehet:
    - Az SDK-t több hálózati hívás is végezheti.
    - Előfordulhat, hogy a lekérdezés hosszú időt vesz fel a dokumentumok beolvasására.
- Minden lekérdezés folytatási jogkivonattal rendelkezik, amely lehetővé teszi a lekérdezés folytatását. Ügyeljen arra, hogy a lekérdezés teljes mértékben le legyen ürítve. További információ [az eredmények több oldalának kezelésére](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>Lekérdezési metrikák beolvasása

Ha Azure Cosmos DB-lekérdezését optimalizálja, az első lépés mindig a lekérdezési [metrikák beolvasása](profile-sql-api-query.md) . Ezek a metrikák a Azure Portalon keresztül is elérhetők. Miután futtatta a lekérdezést a Adatkezelőban, a lekérdezési metrikák az **eredmények** lap mellett láthatók:

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Lekérdezési mérőszámok beolvasása" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

A lekérdezési metrikák lekérése után hasonlítsa össze a **lekért dokumentumok darabszámát** a lekérdezés **kimeneti dokumentumainak számával** . Ezt az összehasonlítást használva azonosíthatja a cikkben áttekinthető releváns részeket.

A **beolvasott dokumentumok** száma érték a lekérdezési motor betöltéséhez szükséges dokumentumok számát adja meg. A **kimeneti dokumentumok** száma érték a lekérdezés eredményeihez szükséges dokumentumok számát adja meg. Ha a **beolvasott dokumentumok száma** jelentősen meghaladja a **kimeneti dokumentumok számát**, akkor a lekérdezésnek legalább egy része nem tudta használni az indexet, és nem volt szükség a vizsgálatra.

A forgatókönyvhöz kapcsolódó lekérdezési optimalizálások megismeréséhez tekintse meg a következő szakaszt.

### <a name="querys-ru-charge-is-too-high"></a>A lekérdezés RU-díja túl magas

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>A lekért dokumentumok száma lényegesen nagyobb, mint a kimeneti dokumentumok száma

- [Adja meg a szükséges elérési utakat az indexelési házirendben.](#include-necessary-paths-in-the-indexing-policy)

- [Az indexet használó rendszerfunkciók ismertetése.](#understand-which-system-functions-use-the-index)

- [A karakterláncrendszer-függvény végrehajtásának javítása.](#improve-string-system-function-execution)

- [Megtudhatja, hogy mely összesített lekérdezések használják az indexet.](#understand-which-aggregate-queries-use-the-index)

- [A szűrővel és ORDER BY záradékkal rendelkező lekérdezések optimalizálása.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Összekapcsolási kifejezések optimalizálása segédlekérdezés használatával.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A lekért dokumentumok száma körülbelül egyenlő a kimeneti dokumentumok számával

- [Csökkentse a több partíciós lekérdezéseket.](#minimize-cross-partition-queries)

- [A szűrőket több tulajdonságon használó lekérdezések optimalizálása.](#optimize-queries-that-have-filters-on-multiple-properties)

- [A szűrővel és ORDER BY záradékkal rendelkező lekérdezések optimalizálása.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A lekérdezés RU-díja elfogadható, de a késés még mindig túl magas

- [Javítsa a közelséget.](#improve-proximity)

- [A kiépített átviteli sebesség növelése.](#increase-provisioned-throughput)

- [Növelje a MaxConcurrency.](#increase-maxconcurrency)

- [Növelje a MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Lekérdezések, amelyekben a lekérdezett dokumentumok száma meghaladja a kimeneti dokumentumok darabszámát

 A **beolvasott dokumentumok** száma érték a lekérdezési motor betöltéséhez szükséges dokumentumok számát adja meg. A **kimeneti dokumentum** száma a lekérdezés által visszaadott dokumentumok száma. Ha a **beolvasott dokumentumok száma** jelentősen meghaladja a **kimeneti dokumentumok számát**, akkor a lekérdezésnek legalább egy része nem tudta használni az indexet, és nem volt szükség a vizsgálatra.

Az alábbi példa olyan vizsgálati lekérdezést mutat be, amelyet az index nem teljes egészében kiszolgált:

Lekérdezés:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Lekérdezési metrikák:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

A **beolvasott dokumentumok száma** (60 951) jelentősen meghaladja a **kimeneti dokumentumok darabszámát** (7), ami azt jelenti, hogy ez a lekérdezés egy dokumentum vizsgálatát eredményezte. Ebben az esetben a [felső ()](sql-query-upper.md) rendszerfunkció nem használ indexet.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>A szükséges elérési utak belefoglalása az indexelési házirendbe

Az indexelési szabályzatnak tartalmaznia kell a `WHERE` záradékok, a `ORDER BY` záradékok `JOIN` és a legtöbb rendszerfunkció összes tulajdonságát. Az indexelési házirendben megadott kívánt elérési utaknak meg kell egyezniük a JSON-dokumentumok tulajdonságaival.

> [!NOTE]
> Azure Cosmos DB indexelési házirend tulajdonságai megkülönböztetik a kis-és nagybetűket

Ha a következő egyszerű lekérdezést futtatja a [táplálkozási](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) adatkészleten, akkor a záradékban található tulajdonság indexelése sokkal alacsonyabb költséget vesz igénybe `WHERE` :

#### <a name="original"></a>Eredeti

Lekérdezés:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

Indexelési házirend:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Ru díj:** 409,51 RUs

#### <a name="optimized"></a>Optimalizált

Frissített indexelési házirend:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Ru díj:** 2,98 RUs

Az indexelési házirendhez bármikor hozzáadhat tulajdonságokat, és nincs hatással az írási vagy olvasási rendelkezésre állásra. [Nyomon követheti az index átalakításának folyamatát](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-system-functions-use-the-index"></a>Az indexet használó rendszerfunkciók ismertetése

A legtöbb rendszerfüggvény indexeket használ. Az alábbi lista néhány olyan gyakori karakterlánc-függvényt mutat be, amely indexeket használ:

- StartsWith
- Contains
- RegexMatch
- Bal
- Alkarakterlánc – de csak akkor, ha az első num_expr 0

Az alábbiakban néhány olyan gyakori rendszerfüggvényt ismertetünk, amely nem használja az indexet, és be kell töltenie az egyes dokumentumokat:

| **System függvény**                     | **Optimalizálási ötletek**             |
| --------------------------------------- |------------------------------------------------------------ |
| FELSŐ/ALSÓ                             | Ahelyett, hogy a rendszerfüggvényt használja az összehasonlításhoz, a beillesztéskor normalizálja a burkolatot. Egy lekérdezés, például A ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` válik ```SELECT * FROM c WHERE c.name = 'BOB'``` . |
| Matematikai függvények (nem összesítések) | Ha egy értéket gyakran kell kiszámítani a lekérdezésben, érdemes az értéket a JSON-dokumentum tulajdonságának megfelelően tárolni. |

### <a name="improve-string-system-function-execution"></a>A karakterláncrendszer-függvény végrehajtásának javítása

Az indexeket használó egyes rendszerfunkcióknál javíthatja a lekérdezés végrehajtását egy záradék hozzáadásával `ORDER BY` a lekérdezéshez. 

Pontosabban, minden olyan rendszerfunkció, amelynek az RU-díja növekszik, mivel a tulajdonság megnövekedésének mértéke is előnyös lehet a `ORDER BY` lekérdezésben. Ezek a lekérdezések egy indexelési vizsgálatot végeznek, így a lekérdezési eredmények rendezése hatékonyabbá teheti a lekérdezést.

Ez az optimalizálás a következő rendszerfunkciók végrehajtásának javítását is lehetővé teheti:

- StartsWith (a kis-és nagybetűk megkülönböztetése = igaz)
- StringEquals (a kis-és nagybetűk megkülönböztetése = igaz)
- Contains
- RegexMatch
- EndsWith

Vegyük például az alábbi lekérdezést a következővel: `CONTAINS` . `CONTAINS` az indexeket fogja használni, de néha még a releváns index hozzáadása után is, az alábbi lekérdezés futtatásakor továbbra is nagyon magas RU-díjat kell megfigyelni.

Eredeti lekérdezés:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

A lekérdezés-végrehajtást a alábbiak hozzáadásával növelheti `ORDER BY` :

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

Ugyanez az optimalizálás segíthet a további szűrőkkel rendelkező lekérdezésekben. Ebben az esetben érdemes az egyenlőségi szűrőket tartalmazó tulajdonságokat is hozzáadni a `ORDER BY` záradékhoz.

Eredeti lekérdezés:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

A lekérdezés-végrehajtást a `ORDER BY` (z) (c.name, c. Town) [összetett indexének](index-policy.md#composite-indexes) hozzáadásával és a következőhöz lehet javítani:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>Az indexet használó összesített lekérdezések ismertetése

A legtöbb esetben a Azure Cosmos DB összesített rendszerfunkciói az indexet fogják használni. Az összesítő lekérdezésekben lévő szűrőktől vagy további feltételektől függően azonban előfordulhat, hogy a lekérdezési motor nagy számú dokumentum betöltésére van szükség. A lekérdezési motor általában az egyenlőség és a tartomány szűrőit fogja alkalmazni először. A szűrők alkalmazása után a lekérdezési motor kiértékelheti a további szűrőket, és igény szerint betöltheti a fennmaradó dokumentumokat az Összesítés kiszámításához.

Ha például ez a két minta lekérdezés, az egyenlőség és a `CONTAINS` rendszerfüggvény szűrővel rendelkező lekérdezés általában hatékonyabb, mint egy `CONTAINS` rendszerfunkció-szűrővel rendelkező lekérdezés. Ennek az az oka, hogy a rendszer először alkalmazza az egyenlőség szűrőt, és az indexet használja, mielőtt a drágább szűrőhöz a dokumentumokat be kell tölteni `CONTAINS` .

Lekérdezés csak `CONTAINS` szűrővel – magasabb ru-díj:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Lekérdezés az esélyegyenlőségi szűrővel és a `CONTAINS` szűrővel – alacsonyabb ru díj:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Az alábbiakban további példákat talál azon összesített lekérdezésekre, amelyek nem fogják teljes mértékben használni az indexet:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Az indexet nem használó rendszerfunkciókkal rendelkező lekérdezések

Tekintse át a megfelelő [rendszerfüggvény oldalát](sql-query-system-functions.md) , és ellenőrizze, hogy az indexet használja-e.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Felhasználó által definiált függvényekkel (UDF) rendelkező összesített lekérdezések

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Lekérdezések a GROUP BY alapján

A lekérdezésekhez tartozó, a `GROUP BY` záradékban található tulajdonságok feltételének mértéke növekszik `GROUP BY` . Az alábbi lekérdezésben például a lekérdezés RU-díja növekedni fog, ahogy az egyedi leírások száma növekszik.

Egy záradékkal rendelkező összesítő függvény VT-díja `GROUP BY` magasabb lesz, mint az összesítő függvények ru-díja. Ebben a példában a lekérdezési motornak be kell töltenie minden olyan dokumentumot, amely megfelel a `c.foodGroup = "Sausages and Luncheon Meats"` szűrőnek, így az ru-díj várhatóan magas lesz.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Ha azt tervezi, hogy gyakran ugyanazokat az összesítő lekérdezéseket futtatja, akkor hatékonyabban hozhat létre valós idejű, megtekinthető nézetet a [Azure Cosmos db változási csatornán](change-feed.md) , mint az egyes lekérdezések futtatásakor.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Szűrővel és ORDER BY záradékkal rendelkező lekérdezések optimalizálása

Habár a szűrővel és `ORDER BY` záradékkal rendelkező lekérdezések általában egy tartomány-indexet használnak, hatékonyabbak lesznek, ha összetett indexből is kiszolgálják őket. Az indexelési házirend módosításán kívül az összes tulajdonságot fel kell vennie az összetett indexbe a `ORDER BY` záradékba. A lekérdezésnek ez a változása biztosítja, hogy az összetett indexet használja.  A hatás megfigyeléséhez futtasson egy lekérdezést a [táplálkozási](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) adatkészleten:

#### <a name="original"></a>Eredeti

Lekérdezés:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

Indexelési házirend:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Ru díj:** 44,28 RUs

#### <a name="optimized"></a>Optimalizált

Frissített lekérdezés (a záradékban mindkét tulajdonságot tartalmazza `ORDER BY` ):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Frissített indexelési házirend:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Ru díj:** 8,86 RUs

### <a name="optimize-join-expressions-by-using-a-subquery"></a>ILLESZTÉSi kifejezések optimalizálása segédlekérdezés használatával

A többértékű allekérdezések úgy optimalizálják `JOIN` a kifejezéseket, hogy az egyes Select-sok kifejezés után lenyomják a predikátumokat, nem pedig a záradékban szereplő összes kereszthivatkozást `WHERE` .

Tekintse át ezt a lekérdezést:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Ru díj:** 167,62 RUs

Ebben a lekérdezésben az index minden olyan dokumentumnak megfelel, amelynek a neve `infant formula` , nullánál `nutritionValue` nagyobb, és 1-nél `amount` nagyobb. Az `JOIN` itt megjelenő kifejezés a címkék, a tápanyagok és az összes egyező dokumentum tömbjét a szűrők alkalmazása előtt is végrehajtja. `WHERE`Ezután a záradék alkalmazza a szűrő predikátumot az egyes `<c, t, n, s>` rekordokra.

Ha például egy egyező dokumentum 10 elemet tartalmaz mindhárom tömbben, akkor 1 x 10 x 10 x 10 (azaz 1 000) rekordok lesz kibontva. Az allekérdezések használata a következő kifejezéssel való csatlakozás előtt segíthet kiszűrni a csatlakoztatott tömböket.

Ez a lekérdezés megegyezik az előzővel, de allekérdezéseket használ:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Ru díj:** 22,17 RUs

Tegyük fel, hogy a címkék tömbben csak egy elem egyezik a szűrővel, és hogy a tápanyagok és a tömbök egyaránt öt elemből állnak. A `JOIN` kifejezések 1 x 1 x 5 x 5 = 25 elemre lesznek kibontva, szemben az első lekérdezés 1 000 elemével.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Lekérdezések, amelyekben a lekérdezett dokumentumok száma megegyezik a kimeneti dokumentumok számával

Ha a **beolvasott dokumentumok száma** nagyjából megegyezik a **kimeneti dokumentumok számával**, a lekérdezési motornak nem kell sok felesleges dokumentumot beolvasnia. Számos lekérdezéshez, például a kulcsszót használó alkalmazásokhoz `TOP` , a **lekéréses dokumentumok száma** meghaladhatja a **kimeneti dokumentumok számát** 1 értékkel. Erről nem kell foglalkoznia.

### <a name="minimize-cross-partition-queries"></a>Több partíciós lekérdezés csökkentése

A Azure Cosmos DB [particionálás](partitioning-overview.md) használatával méretezi az egyes tárolókat a kérelmek egysége és az adattárolási igények növekedésével. Mindegyik fizikai partíció külön és független indexszel rendelkezik. Ha a lekérdezésben olyan esélyegyenlőségi szűrő szerepel, amely megfelel a tároló partíciós kulcsának, akkor csak a megfelelő partíció indexét kell ellenőriznie. Ez az optimalizálás csökkenti a lekérdezés által igényelt RUs teljes számát.

Ha nagy számú kiépített RUs (több mint 30 000) vagy nagy mennyiségű adat van tárolva (több mint körülbelül 100 GB), valószínűleg elég nagy tárolóval rendelkezik ahhoz, hogy jelentős mértékben csökkentse a lekérdezés RU-díjait.

Ha például létrehoz egy tárolót a partíciós kulcs foodGroup, a következő lekérdezéseknek csak egyetlen fizikai partíciót kell ellenőriznie:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

`IN`A partíciós kulccsal szűrővel rendelkező lekérdezések csak a megfelelő fizikai partíció (ka) t fogják megtekinteni, és nem "ventilátor":

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Azok a lekérdezések, amelyek tartomány-szűrői vannak a partíciós kulcson, vagy amelyek nem rendelkeznek szűrővel a partíciós kulcsban

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Több tulajdonság szűrőit tartalmazó lekérdezések optimalizálása

Bár a több tulajdonság szűrőit tartalmazó lekérdezések általában egy tartomány-indexet használnak, hatékonyabbak lesznek, ha egy összetett indexből is kiszolgálják őket. Kis mennyiségű adat esetében ez az optimalizálás nem lesz jelentős hatással. Nagy mennyiségű adattal azonban hasznos lehet. Az összetett indexek esetében csak egyetlen nem egyenlőségi szűrőt lehet optimalizálni. Ha a lekérdezés több nem egyenlőségi szűrővel rendelkezik, válasszon egyet azok közül, akik az összetett indexet fogják használni. A REST továbbra is a tartomány indexeit használja. A nem egyenlőségi szűrőt az összetett indexben kell megadni. [További információ az összetett indexekről](index-policy.md#composite-indexes).

Íme néhány példa az összetett indextel optimalizált lekérdezésekre:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Itt látható a kapcsolódó összetett index:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>A lekérdezés késését csökkentő optimalizálások

Sok esetben az RU-díj akkor lehet elfogadható, ha a lekérdezés késése még mindig túl magas. A következő részekben áttekintheti a lekérdezések késésének csökkentéséhez szükséges tippeket. Ha ugyanazzal a lekérdezéssel többször is futtatja ugyanazt az adatkészletet, akkor a rendszer általában minden alkalommal ugyanazzal az RU-díjjal fog rendelkezni. A lekérdezési késések azonban eltérőek lehetnek a lekérdezés végrehajtása során.

### <a name="improve-proximity"></a>A közelség javítása

A Azure Cosmos DB fióktól eltérő régióból futtatott lekérdezések nagyobb késéssel fognak rendelkezni, mint ha ugyanazon a régión belül futnak. Ha például kódot futtat az asztali számítógépen, a késést várhatóan tíz vagy több száz ezredmásodpercnél nagyobb értékre kell számítani, mint ha a lekérdezés a Azure Cosmos DB azonos Azure-régióban található virtuális gépről származik. Az [adatAzure Cosmos DBek globális elosztása](distribute-data-globally.md) egyszerű, így biztosíthatja, hogy az adatai közelebb legyenek az alkalmazáshoz.

### <a name="increase-provisioned-throughput"></a>Kiosztott átviteli sebesség növelése

Azure Cosmos DB a kiosztott átviteli sebességet a kérelmek egységében (RUs) mérjük. Képzelje el, hogy van egy olyan lekérdezése, amely 5 RUs átviteli sebességet használ. Ha például 1 000 RUs-t hoz létre, akkor az adott lekérdezést a másodpercenként 200-szor futtathatja. Ha a lekérdezés futtatása sikertelen volt, mert nem áll rendelkezésre elegendő átviteli sebesség, Azure Cosmos DB HTTP 429-es hibát ad vissza. A jelenlegi Core (SQL) API SDK-k bármelyike automatikusan újrapróbálkozik a lekérdezéssel, miután rövid ideig várakozik. A szabályozott kérelmek hosszabb időt vehetnek igénybe, így a kiépített átviteli sebesség növelése javíthatja a lekérdezés késését. A Azure Portal **mérőszámok** paneljén megfigyelheti a [szabályozott kérelmek teljes számát](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) .

### <a name="increase-maxconcurrency"></a>MaxConcurrency javítása

A párhuzamos lekérdezések több partíció párhuzamos lekérdezésével működnek. Az egyedi particionált gyűjteményekből származó adatok azonban a lekérdezésre vonatkozó, sorosan beolvasva vannak. Így ha a MaxConcurrency a partíciók számára állítja be, akkor a lehető leghatékonyabban hajthatja végre a legeredményesebb lekérdezéseket, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, beállíthatja, hogy a MaxConcurrency (vagy a MaxDegreesOfParallelism a régebbi SDK-verziókban) magas számra állítsa be. A rendszer kijelöli a minimális (partíciók száma, felhasználó által megadott bemenet) értéket a maximális párhuzamossági fok alapján.

### <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount javítása

A lekérdezések az eredmények előzetes beolvasására lettek kialakítva, miközben az ügyfél az aktuális eredményt dolgozza fel. Az előzetes beolvasás a lekérdezés teljes késésének javítására nyújt segítséget. A MaxBufferedItemCount beállítása korlátozza az előre beolvasott eredmények számát. Ha ezt az értéket a visszaadott várt számú eredmény (vagy egy magasabb szám) értékre állítja, a lekérdezés a lehető legtöbb előnyt érheti el az előzetes beolvasáskor. Ha a-1 értéket adja meg, a rendszer automatikusan meghatározza a pufferbe kerülő elemek számát.

## <a name="next-steps"></a>Következő lépések
A következő cikkekből megtudhatja, hogyan méri a lekérdezéseket, és hogyan végezhet végrehajtási statisztikát a lekérdezések finomhangolásához, és így tovább:

* [SQL-lekérdezés végrehajtási metrikáinak lekérése a .NET SDK használatával](profile-sql-api-query.md)
* [A lekérdezési teljesítmény finomhangolása az Azure Cosmos DB-vel](./sql-api-query-metrics.md)
* [Teljesítménnyel kapcsolatos tippek .NET SDK-hoz](performance-tips.md)
* [Teljesítménnyel kapcsolatos tippek a Java v4 SDK-hoz](performance-tips-java-sdk-v4-sql.md)