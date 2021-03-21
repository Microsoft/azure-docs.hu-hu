---
title: A Azure Cosmos DB Gremlin API-ban található lekérdezések kiértékeléséhez használja a végrehajtási profilt
description: Ismerje meg, hogyan oldhatja meg és javíthatja a Gremlin-lekérdezéseket a végrehajtási profil lépésével.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 03/27/2019
ms.author: chrande
ms.openlocfilehash: 18cefb1dd80368a8ccdad9f6f3ffc30881a8a889
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087485"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Gremlin-lekérdezések kiértékelése a végrehajtási profil lépés segítségével
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Ez a cikk áttekinti, hogyan használható a végrehajtási profil lépés az Azure Cosmos DB Gremlin API gráfadatbázisokkal. Ez a lépés a hibaelhárításhoz és a lekérdezés optimalizálásához nyújt releváns információkat, és kompatibilis minden olyan Gremlin-lekérdezéssel, amelyet egy Cosmos DB Gremlin API-fiókon lehet végrehajtani.

Ennek a lépésnek a használatához egyszerűen fűzze hozzá a `executionProfile()` függvény hívását a Gremlin-lekérdezés végén. **A rendszer végrehajtja a Gremlin-lekérdezést** , és a művelet eredménye egy JSON-válasz objektumot ad vissza a lekérdezés végrehajtási profiljával.

Például:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

A lépés meghívása után `executionProfile()` a válasz egy JSON-objektum lesz, amely magában foglalja a végrehajtott Gremlin lépést, a teljes időt, valamint a Cosmos db Runtime operátorok tömbjét, amelyet az utasítás eredményezett.

> [!NOTE]
> A végrehajtási profilhoz tartozó implementáció nincs definiálva az Apache Tinkerpop-specifikációban. Azure Cosmos DB Gremlin API implementációja.


## <a name="response-example"></a>Példa válaszra

Az alábbi példa a visszaadott kimenetre mutat be egy megjegyzést:

> [!NOTE]
> Ez a példa megjegyzésekkel van ellátva, amelyek ismertetik a válasz általános szerkezetét. A tényleges executionProfile-válasz nem tartalmaz megjegyzéseket.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> A executionProfile lépés végrehajtja a Gremlin-lekérdezést. Ez magában foglalja a `addV` vagy a `addE` lépéseket, amelyek a létrehozást eredményezik, és véglegesítik a lekérdezésben megadott módosításokat. Ennek eredményeképpen a Gremlin-lekérdezés által generált kérések egységei is felszámítva lesznek.

## <a name="execution-profile-response-objects"></a>Végrehajtási profil válaszának objektumai

A executionProfile () függvény válasza a JSON-objektumok hierarchiáját fogja eredményezni a következő szerkezettel:
  - **Gremlin műveleti objektum**: a teljes Gremlin műveletet jelöli. A következő tulajdonságokat tartalmazza.
    - `gremlin`: A végrehajtott explicit Gremlin utasítás.
    - `totalTime`: Az az idő (ezredmásodpercben), amelyet a lépés végrehajtása során felmerült. 
    - `metrics`: Egy tömb, amely a lekérdezés teljesítéséhez végrehajtott Cosmos DB futtatókörnyezeti operátorokat tartalmazza. Ezt a listát a rendszer a végrehajtás sorrendjében rendezi.
    
  - **Cosmos db futásidejű operátorok**: a teljes Gremlin művelet összes összetevőjét képviseli. Ezt a listát a rendszer a végrehajtás sorrendjében rendezi. Minden objektum a következő tulajdonságokat tartalmazza:
    - `name`: Az operátor neve. A kiértékelt és végrehajtott lépés típusa. További tudnivalókat az alábbi táblázatban talál.
    - `time`: Az adott operátor által tartott időtartam ezredmásodpercben.
    - `annotations`: További információkat tartalmaz, amelyek a végrehajtás alatt álló operátorra vonatkoznak.
    - `annotations.percentTime`: Az adott operátor végrehajtásához szükséges teljes idő százalékos aránya.
    - `counts`: Az operátor által a tárolási rétegből visszaadott objektumok száma. Ezt a `counts.resultCount` skaláris érték tárolja.
    - `storeOps`: Olyan tárolási műveletet jelöl, amely egy vagy több partícióra terjedhet ki.
    - `storeOps.fanoutFactor`: Az adott tárolási művelet által elért partíciók számát jelöli.
    - `storeOps.count`: A tárolási művelet által visszaadott eredmények számát jelöli.
    - `storeOps.size`: Egy adott tárolási művelet eredményének mérete bájtban kifejezve.

Cosmos DB Gremlin Runtime operátor|Description
---|---
`GetVertices`| Ez a lépés egy predikátum-készletet szerez be az adatmegőrzési rétegből. 
`GetEdges`| Ez a lépés a csúcspontok egy halmazához szomszédos éleket szerzi be. Ez a lépés egy vagy több tárolási művelethez vezethet.
`GetNeighborVertices`| Ez a lépés a széleihez csatlakozó csúcspontokat szerzi be. Az élek tartalmazzák a forrás-és a cél csúcspontok partíciós kulcsait és AZONOSÍTÓját.
`Coalesce`| Ez a lépés a `coalesce()` Gremlin lépés végrehajtásakor két művelet kiértékelését végzi.
`CartesianProductOperator`| Ez a lépés egy Descartes-féle terméket számít ki két adatkészlet között. Általában a predikátumok vagy a `to()` `from()` használatakor hajtható végre.
`ConstantSourceOperator`| Ez a lépés kiszámít egy kifejezést, hogy eredményként konstans értéket hozzon létre.
`ProjectOperator`| Ez a lépés a korábbi műveletek eredményének használatával készít elő és szerializál egy választ.
`ProjectAggregation`| Ez a lépés egy összesítési műveletre vonatkozó választ készít elő és szerializál.

> [!NOTE]
> A lista továbbra is frissülni fog, mivel új operátorok lettek hozzáadva.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Példák a végrehajtási profilok válaszának elemzésére

A következő példák olyan gyakori optimalizációkat mutatnak be, amelyeket a végrehajtási profil válasza alapján lehet kitalálni:
  - Vak kipróbálható lekérdezés.
  - Szűretlen lekérdezés.

### <a name="blind-fan-out-query-patterns"></a>Blind fan-out lekérdezési minták

Tegyük fel, hogy a következő végrehajtási profilt választ egy **particionált gráfból**:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

A következő következtetések hozhatók létre belőle:
- A lekérdezés egyetlen AZONOSÍTÓval való keresés, mivel a Gremlin utasítás a mintát követi `g.V('id')` .
- A metrika megítélése `time` szerint a lekérdezés késése úgy tűnik, hogy magas, mert [több mint 10ms egyetlen pont – olvasási művelethez](./introduction.md#guaranteed-speed-at-any-scale).
- Ha megnézzük az `storeOps` objektumot, láthatjuk, hogy az `fanoutFactor` az `5` , ami azt jelenti, hogy ez a művelet [5 partíciót](./partitioning-overview.md) használt.

Az elemzés befejezésekor meghatározhatjuk, hogy az első lekérdezés a szükségesnél több partíciót is elér. Ez a következő lehet: a lekérdezésben szereplő particionáló kulcs megadásával predikátumként. Ez kevesebb késést és lekéréses költségeket eredményez. További információ a [Graph particionálásról](graph-partitioning.md). Az optimális lekérdezés lenne `g.V('tt0093640').has('partitionKey', 't1001')` .

### <a name="unfiltered-query-patterns"></a>Szűretlen lekérdezési minták

Hasonlítsa össze a következő két végrehajtási profilra adott válaszokat. Az egyszerűség kedvéért ezek a példák egyetlen particionált gráfot használnak.

Ez az első lekérdezés lekéri az összes csúcspontot a címkével, `tweet` majd beolvassa a szomszédos csúcspontokat:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Figyelje meg ugyanazt a lekérdezést, de most egy további szűrővel, `has('lang', 'en')` mielőtt megkezdené a szomszédos csúcspontokat:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Ez a két lekérdezés ugyanahhoz az eredményhez vezetett, azonban az elsőnek több kérési egységre van szüksége, mivel ez szükséges egy nagyobb kezdeti adatkészlet megismétléséhez a szomszédos elemek lekérdezése előtt. A viselkedés mutatói láthatók a következő paraméterek mindkét válaszból való összevetése esetén:
- Az `metrics[0].time` érték nagyobb az első válaszban, ami azt jelzi, hogy ez az egyetlen lépés már nem volt feloldva.
- Az `metrics[0].counts.resultsCount` érték nagyobb, mint az első válasznál is, amely azt jelzi, hogy a kezdeti munkaadatkészlet nagyobb volt.

## <a name="next-steps"></a>Következő lépések
* A Azure Cosmos DB [támogatott Gremlin szolgáltatásainak](gremlin-support.md) megismerése. 
* További információ a [Azure Cosmos db GREMLIN API-](graph-introduction.md)ról.