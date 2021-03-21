---
title: A MongoDB Azure Cosmos DB API-ját használó lekérdezési problémák elhárítása
description: Ismerje meg, hogyan azonosíthatja, diagnosztizálhatja és elháríthatja Azure Cosmos DB API-ját a MongoDB-lekérdezési problémák esetén.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.subservice: cosmosdb-mongo
ms.date: 03/02/2021
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5302cb7bb3f4683d200f6f9ea106991bb934fc17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659902"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>A MongoDB Azure Cosmos DB API-ját használó lekérdezési problémák elhárítása
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a cikk részletesen ismerteti a Azure Cosmos DB-lekérdezések hibaelhárításának általános javasolt megközelítését. Habár nem veszi figyelembe a jelen cikkben ismertetett lépéseket a lehetséges lekérdezési problémákkal kapcsolatos teljes körű védekezéssel, a leggyakoribb teljesítménybeli tippeket is itt találja. Ez a cikk kiindulópontként használható a Azure Cosmos DB API-MongoDB való lassú vagy költséges lekérdezések hibaelhárításához. Ha a Azure Cosmos DB Core (SQL) API-t használja, tekintse meg az [SQL API-lekérdezés hibaelhárítási útmutatóját](troubleshoot-query-performance.md) ismertető cikket.

A Azure Cosmos DB lekérdezési optimalizálásait az alábbiak szerint széles körben kategorizáljuk:

- Optimalizálások, amelyek csökkentik a lekérdezésre vonatkozó kérési egység (RU) díját
- Optimalizálások, amelyek csak csökkentik a késést

Ha csökkenti egy lekérdezés RU-díját, általában a késést is csökkenti.

Ez a cikk példákat tartalmaz, amelyeket újra létrehozhat a [táplálkozási adatkészlet](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)használatával.

> [!NOTE] 
> Ez a cikk azt feltételezi, hogy Azure Cosmos DB API-ját használja a 3,6-es és újabb verziójú MongoDB-fiókokhoz. Néhány, a 3,2-es verzióban rosszul elvégezhető lekérdezés jelentős újdonságokkal rendelkezik a 3.6 + verzióban. [Támogatási kérelem](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)bejelentésével frissítsen a 3,6-es verzióra.

## <a name="use-explain-command-to-get-metrics"></a>Metrikák beolvasása $explain parancs használatával

Ha Azure Cosmos DB-lekérdezéseket optimalizálja, az első lépés a lekérdezéshez tartozó [ru-díj beszerzése](find-request-unit-charge-mongodb.md) . Durva útmutatóként érdemes megvizsgálni, hogyan csökkentheti a kérelmeket a 50 RUs-nál nagyobb költségekkel rendelkező lekérdezéseknél. 

Az RU-díj beszerzése mellett a parancsot kell használnia a `$explain` lekérdezés és az indexelés használati metrikáinak beszerzéséhez. Az alábbi példa egy lekérdezést futtat, és a parancs használatával `$explain` jeleníti meg a lekérdezési és indexelési használati metrikákat:

**$explain parancs:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**Kimeneti**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

A `$explain` parancs kimenete hosszadalmas, és részletes információkkal szolgál a lekérdezés végrehajtásáról. Általánosságban elmondható, hogy néhány szakaszt érdemes figyelembe vennie a lekérdezési teljesítmény optimalizálásakor:

| Metric | Leírás | 
| ------ | ----------- |
| `timeInclusiveMS` | Háttérbeli lekérdezés késése |
| `pathsIndexed` | Megjeleníti a lekérdezés által használt indexeket | 
| `pathsNotIndexed` | Megjeleníti a lekérdezés által használható indexeket, ha vannak ilyenek. | 
| `shardInformation` | Egy adott [fizikai partíció](./partitioning-overview.md#physical-partitions) lekérdezési teljesítményének összefoglalása | 
| `retrievedDocumentCount` | A lekérdezési motor által betöltött dokumentumok száma | 
| `outputDocumentCount` | A lekérdezés eredményeiben visszaadott dokumentumok száma | 
| `estimatedDelayFromRateLimitingInMilliseconds` | Becsült további lekérdezési késés a ráta korlátozása miatt | 

A lekérdezési metrikák beolvasása után hasonlítsa össze a- `retrievedDocumentCount` t a `outputDocumentCount` lekérdezéssel. Ezt az összehasonlítást használva azonosíthatja a cikkben áttekinthető releváns részeket. A azon `retrievedDocumentCount`  dokumentumok száma, amelyeket a lekérdezési motornak be kell töltenie. A a `outputDocumentCount` lekérdezés eredményeihez szükséges dokumentumok száma. Ha a `retrievedDocumentCount`  értéke jelentősen meghaladja a `outputDocumentCount` -t, a lekérdezésnek legalább egy része nem tudott indexet használni, és nem volt szükség a vizsgálatra.

A forgatókönyvhöz kapcsolódó lekérdezési optimalizálások megismeréséhez tekintse meg a következő szakaszt.

### <a name="querys-ru-charge-is-too-high"></a>A lekérdezés RU-díja túl magas

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>A lekért dokumentumok száma lényegesen nagyobb, mint a kimeneti dokumentumok száma

- [A szükséges indexek belefoglalása.](#include-necessary-indexes)

- [Ismerje meg, hogy mely aggregációs műveletek használják az indexet.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A lekért dokumentumok száma körülbelül egyenlő a kimeneti dokumentumok számával

- [Csökkentse a több partíciós lekérdezéseket.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A lekérdezés RU-díja elfogadható, de a késés még mindig túl magas

- [Javítsa a közelséget.](#improve-proximity)

- [A kiépített átviteli sebesség növelése.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Lekérdezések, amelyekben a lekérdezett dokumentumok száma meghaladja a kimeneti dokumentumok darabszámát

 A a `retrievedDocumentCount` lekérdezési motor betöltéséhez szükséges dokumentumok száma. A a `outputDocumentCount` lekérdezés által visszaadott dokumentumok száma. Ha a `retrievedDocumentCount` értéke jelentősen meghaladja a `outputDocumentCount` -t, a lekérdezésnek legalább egy része nem tudott indexet használni, és nem volt szükség a vizsgálatra.

Az alábbi példa olyan vizsgálati lekérdezést mutat be, amelyet az index nem teljes egészében kiszolgált:

**$explain parancs:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**Kimeneti**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

A `retrievedDocumentCount` (8618) nagyobb, mint a `outputDocumentCount` (1), ami azt jelenti, hogy ez a lekérdezés szükséges a dokumentumok vizsgálatához. 

### <a name="include-necessary-indexes"></a>A szükséges indexek belefoglalása

Tekintse át a `pathsNotIndexed` tömböt, és adja hozzá ezeket az indexeket. Ebben a példában az elérési utakat `foodGroup` és `description` indexelést kell megadni.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

A Azure Cosmos DB API-MongoDB tartozó ajánlott eljárások indexelése eltér a MongoDB. Azure Cosmos DB API-MongoDB az összetett indexek csak olyan lekérdezésekben használatosak, amelyeknek több tulajdonság alapján kell hatékonyan rendezniük. Ha több tulajdonságra vonatkozó szűrőkkel rendelkező lekérdezésekkel rendelkezik, akkor mindegyik tulajdonsághoz létre kell hoznia egy mező-indexet. A lekérdezési predikátumok több egymezős indexet is használhatnak.

A [helyettesítő karakterek](mongodb-indexing.md#wildcard-indexes) indexelése egyszerűbbé teheti az indexelést. A MongoDB eltérően a helyettesítő karakterek több mezőt is támogatnak a lekérdezési predikátumokban. Ha egyetlen helyettesítő karaktert használ, nem lehet különbséget adni a lekérdezési teljesítményben, és nem kell külön indexet létrehoznia az egyes tulajdonságokhoz. Az összes tulajdonsághoz helyettesítő karakteres index hozzáadása az összes lekérdezés optimalizálásának legegyszerűbb módja.

Bármikor hozzáadhat új indexeket, és nem befolyásolhatja az írási és olvasási rendelkezésre állást. [Nyomon követheti az index átalakításának folyamatát](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-aggregation-operations-use-the-index"></a>Az indexet használó aggregációs műveletek ismertetése

A legtöbb esetben a Azure Cosmos DB API-MongoDB található összesítési műveletek részben az indexeket fogják használni. A lekérdezési motor általában először alkalmazza az egyenlőség és a tartomány szűrőit, és használja az indexeket. A szűrők alkalmazása után a lekérdezési motor kiértékelheti a további szűrőket, és igény szerint betöltheti a fennmaradó dokumentumokat az Összesítés kiszámításához. 

Bemutatunk egy példát:

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

Ebben az esetben az indexek optimalizálni tudják a `$match` szakaszt. Az index hozzáadása `foodGroup` jelentősen javítja a lekérdezési teljesítményt. A MongoDB hasonlóan `$match` az összesítési folyamat elején kell elhelyezni az indexek használatának maximalizálása érdekében.

Azure Cosmos DB API-MongoDB az indexek nem használatosak a tényleges összesítéshez, ami ebben az esetben a következő: `$max` . Az index hozzáadása `version` nem javítja a lekérdezési teljesítményt.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Lekérdezések, amelyekben a lekérdezett dokumentumok száma megegyezik a kimeneti dokumentumok számával

Ha a `retrievedDocumentCount` nagyjából megegyezik a-val `outputDocumentCount` , a lekérdezési motornak nem kell sok felesleges dokumentumot beolvasnia.

### <a name="minimize-cross-partition-queries"></a>Több partíciós lekérdezés csökkentése

A Azure Cosmos DB [particionálás](partitioning-overview.md) használatával méretezi az egyes tárolókat a kérelmek egysége és az adattárolási igények növekedésével. Mindegyik fizikai partíció külön és független indexszel rendelkezik. Ha a lekérdezésben olyan esélyegyenlőségi szűrő szerepel, amely megfelel a tároló partíciós kulcsának, akkor csak a megfelelő partíció indexét kell ellenőriznie. Ez az optimalizálás csökkenti a lekérdezés által igényelt RUs teljes számát. [További információ a partíciós lekérdezések és a több partíciós lekérdezések közötti különbségekről](how-to-query-container.md).

Ha nagy számú kiépített RUs (több mint 30 000) vagy nagy mennyiségű adat van tárolva (több mint körülbelül 100 GB), valószínűleg elég nagy tárolóval rendelkezik ahhoz, hogy jelentős mértékben csökkentse a lekérdezés RU-díjait. 

Az `shardInformation` egyes fizikai partíciók lekérdezési metrikáinak megismeréséhez tekintse meg a tömböt. Az egyedi értékek száma `shardKeyRangeId` azon fizikai partíciók száma, amelyeken a lekérdezés végrehajtásához szükséges. Ebben a példában a lekérdezés négy fizikai partíción lett végrehajtva. Fontos megérteni, hogy a végrehajtás teljesen független az index kihasználtságával. Más szóval a több partíciós lekérdezések továbbra is használhatnak indexeket.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>A lekérdezés késését csökkentő optimalizálások

Sok esetben az RU-díj akkor lehet elfogadható, ha a lekérdezés késése még mindig túl magas. A következő részekben áttekintheti a lekérdezések késésének csökkentéséhez szükséges tippeket. Ha ugyanazzal a lekérdezéssel többször is futtatja ugyanazt az adatkészletet, akkor a rendszer általában minden alkalommal ugyanazzal az RU-díjjal fog rendelkezni. A lekérdezési késések azonban eltérőek lehetnek a lekérdezés végrehajtása során.

### <a name="improve-proximity"></a>A közelség javítása

A Azure Cosmos DB fióktól eltérő régióból futtatott lekérdezések nagyobb késéssel fognak rendelkezni, mint ha ugyanazon a régión belül futnak. Ha például kódot futtat az asztali számítógépen, a késést várhatóan tíz vagy több száz ezredmásodpercnél nagyobb értékre kell számítani, mint ha a lekérdezés a Azure Cosmos DB azonos Azure-régióban található virtuális gépről származik. Az [adatAzure Cosmos DBek globális elosztása](tutorial-global-distribution-mongodb.md) egyszerű, így biztosíthatja, hogy az adatai közelebb legyenek az alkalmazáshoz.

### <a name="increase-provisioned-throughput"></a>Kiosztott átviteli sebesség növelése

Azure Cosmos DB a kiosztott átviteli sebességet a kérelmek egységében (RUs) mérjük. Képzelje el, hogy van egy olyan lekérdezése, amely 5 RUs átviteli sebességet használ. Ha például 1 000 RUs-t hoz létre, akkor az adott lekérdezést a másodpercenként 200-szor futtathatja. Ha a lekérdezés futtatása sikertelen volt, mert nincs elég elérhető átviteli sebesség, a Azure Cosmos DB korlátozza a kérelmeket. A MongoDB Azure Cosmos DB API-je automatikusan újra próbálkozik a lekérdezéssel, miután rövid ideig várakozik. A szabályozott kérelmek hosszabb időt vehetnek igénybe, így a kiépített átviteli sebesség növelése javíthatja a lekérdezés késését.

Az érték az `estimatedDelayFromRateLimitingInMilliseconds` átviteli sebesség növelésének lehetséges késleltetési előnyeit adja meg.

## <a name="next-steps"></a>Következő lépések

* [Lekérdezési teljesítmény – hibakeresés (SQL API)](troubleshoot-query-performance.md)
* [Indexelés kezelése a MongoDB-hez készült Azure Cosmos DB API-ban](mongodb-indexing.md)
