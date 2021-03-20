---
title: Gyakori kérdések a Gremlin API-ról Azure Cosmos DB
description: Válaszok a Gremlin API-val kapcsolatos gyakori kérdésekre Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 0374886482b43eefe6df5e107fd4da371633099d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359778"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Gyakori kérdések a Gremlin API-ról Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Ez a cikk a Azure Cosmos DB Gremlin API-val kapcsolatos gyakori kérdésekre adott válaszokat ismerteti.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>A Gremlin-lekérdezések hatékonyságának kiértékelése

A **executionProfile ()** előzetes verziójának használatával megadhatja a lekérdezés végrehajtási tervének elemzését. Ezt a lépést fel kell venni a Gremlin-lekérdezések végére, ahogy az a következő példában látható:

**Példa lekérdezésre**

```
g.V('mary').out('knows').executionProfile()
```

**Példa kimenetre**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

A fenti profil kimenete azt mutatja, hogy mennyi idő telt el a csúcspont-objektumok, a peremhálózat-objektumok és a munkahalmaz méretének beszerzése során. Ez a Azure Cosmos DB lekérdezések standard díjszabásával kapcsolatos.

## <a name="other-frequently-asked-questions"></a>Egyéb gyakori kérdések

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hogyan történik az RU/s díja a lekérdezéseknek a Graph-adatbázison való futtatásakor?

Az összes gráf-objektum, csúcs és szegély a háttér JSON-dokumentumaiként jelenik meg. Mivel egy Gremlin-lekérdezés egyszerre egy vagy több gráf-objektumot is módosíthat, a hozzá társított díj közvetlenül kapcsolódik az objektumokhoz, a lekérdezés által feldolgozott szegélyekhez. Ez ugyanaz a folyamat, amelyet a Azure Cosmos DB az összes többi API-hoz használ. További információ: [Az Azure Cosmos DB kérelemegységei](request-units.md).

Az RU-díj a bejárás munkahalmazán alapul, nem az eredményhalmazban. Ha például egy lekérdezés egyetlen csúcspont beszerzését célozza meg, de több mint egy másik objektumra van szüksége, akkor a díj az összes gráf-objektumon alapul, amelyet az egyetlen eredmény csúcspontjának kiszámításához fog végezni.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Mi a Graph-adatbázisok maximális mérete Azure Cosmos DB Gremlin API-ban?

A Azure Cosmos DB a [horizontális particionálások](partitioning-overview.md) használatával automatikusan megjavítja a tárolási és az átviteli sebességre vonatkozó követelményeket. A számítási feladatok maximális átviteli sebességét és tárolókapacitását az adott tárolóhoz társított partíciók száma határozza meg. A Gremlin API-tárolók azonban az irányelvek egy adott készletével biztosítják a megfelelő teljesítménybeli teljesítményt. A particionálással és az ajánlott eljárásokkal kapcsolatos további információkért lásd: [particionálás Azure Cosmos db](partitioning-overview.md) cikkben.

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>A C# kódon-fejlesztéshez a Microsoft. Azure. graphs csomagot vagy a Gremlin.NET kell használnia?

Azure Cosmos db Gremlin API kihasználja a nyílt forráskódú illesztőprogramokat a szolgáltatás fő összekötői. Ezért az ajánlott lehetőség az [Apache Tinkerpop által támogatott illesztőprogramok](https://tinkerpop.apache.org/)használata.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hogyan biztosíthatok védelmet a Gremlin-illesztőprogramokat használó injekciós támadásokkal szemben?

A legtöbb natív Apache Tinkerpop Gremlin-illesztőprogram lehetővé teszi, hogy a lekérdezés végrehajtásához paraméterek szótárát adja meg. Ez egy példa arra, hogyan végezheti el a [Gremlin.net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) -ben és a [Gremlin-JavaScriptben](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/main/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Miért kapok a "Gremlin-lekérdezés fordítási hibája: nem található metódus" hibaüzenetet?

Azure Cosmos DB Gremlin API a Gremlin felületén definiált funkciók egy részhalmazát valósítja meg. A támogatott lépések és további információk a [Gremlin-támogatással](gremlin-support.md) foglalkozó cikkben találhatók.

A legjobb megoldás a szükséges Gremlin lépések újraírása a támogatott funkciókkal, mivel a Azure Cosmos DB az összes alapvető Gremlin-lépést támogatja.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Miért kapok "WebSocketException: a kiszolgáló a (z) 200-as állapotkódot adta vissza, amikor a rendszer a következő állapotkódot várta:" 101 ".

Ez a hiba valószínűleg akkor fordul elő, ha rossz végpontot használ. A hibát generáló végpont a következő mintával rendelkezik:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Ez a Graph-adatbázis dokumentumok végpontja.  A használni kívánt végpont a Gremlin végpont, amelynek formátuma a következő:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Miért kapok "RequestRateIsTooLarge" hibaüzenetet?

Ez a hiba azt jelenti, hogy a lefoglalt kérelmek másodpercenkénti száma nem elegendő a lekérdezés kiszolgálásához. Ez a hiba általában akkor látható, ha olyan lekérdezést futtat, amely az összes csúcspontot beszerzi:

```
// Query example:
g.V()
```

Ez a lekérdezés megkísérli a gráf összes csúcspontjának lekérését. Így a lekérdezés díja legalább a csúcspontok számának felel meg az RUs szempontjából. Az RU/s beállítást úgy kell beállítani, hogy foglalkozzon a lekérdezéssel.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Miért nem sikerül a Gremlin-illesztőprogram kapcsolatainak eldobása?

A Gremlin-kapcsolat egy WebSocket-kapcsolaton keresztül történik. Bár a WebSocket-kapcsolatok nem rendelkeznek adott időponttal, Azure Cosmos DB Gremlin API 30 perc inaktivitás után leáll az üresjárati kapcsolatokon.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Miért nem használhatom fluent API hívásokat a natív Gremlin-illesztőprogramokban?

A Azure Cosmos DB Gremlin API még nem támogatja a Fluent API-hívásokat. A Fluent API-hívások olyan belső formázási funkciót igényelnek, amely a Azure Cosmos DB Gremlin API által jelenleg nem támogatott bytecode-támogatás. Ugyanezen ok miatt a legújabb Gremlin-JavaScript illesztőprogram is jelenleg nem támogatott.

## <a name="next-steps"></a>Következő lépések

* [Azure Cosmos DB Gremlin-támogatás](gremlin-support.md)
* Azure Cosmos DB Graph-adatbázis létrehozása, lekérdezése és bejárása a [Gremlin-konzol](create-graph-gremlin-console.md) használatával
