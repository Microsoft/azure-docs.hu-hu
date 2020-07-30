---
title: Az SQL-lekérdezések első lépései a Azure Cosmos DBban
description: Ismerje meg, hogyan lehet SQL-lekérdezéseket lekérdezni a Azure Cosmos DBról. A mintaadatok feltölthetők egy Azure Cosmos DB tárolóba, és lekérdezheti azt.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: tisande
ms.openlocfilehash: d292b7cfcda73cb4cd6ac2535c7e27fc675e1030
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87308185"
---
# <a name="getting-started-with-sql-queries"></a>SQL-lekérdezések – első lépések

Azure Cosmos DB SQL API-fiókokban kétféleképpen olvashatók be az információk:

**Pont beolvasása** – a kulcs/érték megkeresése egyetlen *elem-azonosítón* és partíciós kulcson végezhető el. Az *Item azonosító* és a partíciós kulcs kombinációja a kulcs, és maga az az érték. 1 KB-os dokumentum esetén a pont általában az 1., 10 MS alatti késéssel rendelkező [kérési egységet](request-units.md) olvassa. A pont olvasás egyetlen elemmel tér vissza.

**SQL-lekérdezések** – az adatlekérdezéshez lekérdezéseket írhat a STRUCTURED Query Language (SQL) használatával JSON-lekérdezési nyelvként. A lekérdezések mindig legalább 2,3-es kérési egységbe kerülnek, és általánosságban a pont olvasásakor nagyobb és nagyobb a változó késése. A lekérdezések számos elemet adhatnak vissza.

A legtöbb nagy mennyiségű olvasási terhelés Azure Cosmos DB a pontok és az SQL-lekérdezések kombinációját használja. Ha csak egyetlen elem olvasását kell elolvasnia, az olvasások olcsóbbak és gyorsabbak a lekérdezéseknél. A pont olvasásának nem kell a lekérdezési motort használnia az adateléréshez, és közvetlenül is elolvashatja az adatokat. Természetesen nem lehetséges, hogy az összes számítási feladat kizárólag olvasási pontok használatával olvassa az adatokat, így az SQL lekérdezési nyelvként és a [séma-agnosztikus indexelés](index-overview.md) révén rugalmasabban férhet hozzá az adatokhoz.

Íme néhány példa arra, hogyan végezheti el a pontok olvasását az egyes SDK-kal:

- [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet)
- [Java SDK](https://docs.microsoft.com/java/api/com.azure.cosmos.cosmoscontainer.readitem?view=azure-java-stable#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](https://docs.microsoft.com/javascript/api/@azure/cosmos/item?view=azure-node-latest#read-requestoptions-)
- [Python SDK](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.containerproxy?view=azure-python#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

A dokumentum hátralévő része azt mutatja be, hogyan kezdheti el az SQL-lekérdezések írását a Azure Cosmos DBban. Az SQL-lekérdezések az SDK-n vagy a Azure Portalon is futtathatók.

## <a name="upload-sample-data"></a>Mintaadatok feltöltése

Az SQL API Cosmos DB fiókjában hozzon létre egy nevű tárolót `Families` . Hozzon létre két egyszerű JSON-elemet a tárolóban. Az adathalmaz használatával a Azure Cosmos DB lekérdezési dokumentációjában a legtöbb lekérdezési lekérdezést futtathatja.

### <a name="create-json-items"></a>JSON-elemek létrehozása

A következő kód két egyszerű JSON-elemet hoz létre a családokról. Az Andersen és a Wakefield család egyszerű JSON-elemei közé tartoznak a szülők, a gyermekek és a hozzájuk tartozó háziállatok, a címek és a regisztrációs adatok. Az első elem sztringeket, számokat, logikai elemeket, tömböket és beágyazott tulajdonságokat tartalmaz.

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

A második tétel a `givenName` és `familyName` a helyett a és a értéket használja `firstName` `lastName` .

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>A JSON-elemek lekérdezése

Próbáljon ki néhány lekérdezést a JSON-adaton, hogy megértse a Azure Cosmos DB SQL-lekérdezési nyelvének főbb szempontjait.

A következő lekérdezés azokat az elemeket adja vissza, amelyekben a `id` mező megfelel `AndersenFamily` . Mivel ez egy `SELECT *` lekérdezés, a lekérdezés kimenete a teljes JSON-elem. A SELECT szintaxissal kapcsolatos további információkért lásd: [SELECT utasítás](sql-query-select.md).

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

A lekérdezés eredményei a következők:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

A következő lekérdezés újraformázza a JSON-kimenetet egy másik alakzatba. A lekérdezés egy új JSON- `Family` objektumot választ ki két kiválasztott mezővel, `Name` és `City` Ha a városi város megegyezik az állapottal. "NY, NY" megfelel ebben az esetben.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

A lekérdezés eredményei a következők:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

A következő lekérdezés az összes olyan gyermek nevét adja vissza a családban `id` , amelyek egyezéseit `WakefieldFamily` város szerint rendezi.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Az eredmény a következő:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Megjegyzések

Az előző példák a Cosmos DB lekérdezési nyelvének számos aspektusát mutatják be:  

* Mivel az SQL API JSON-értékeken működik, sorok és oszlopok helyett faszerkezetű entitásokkal foglalkozik. A facsomópontok tetszőleges részletességgel, például az `Node1.Node2.Node3…..Nodem` ANSI SQL-ben lévő kétrészes hivatkozáshoz hasonlóan is megtekinthetők `<table>.<column>` .

* Mivel a lekérdezési nyelv a séma nélküli adatmennyiséggel működik, a típusrendszer számára dinamikusan kell kötni. Ugyanez a kifejezés különböző típusokat eredményezhet különböző elemeken. A lekérdezés eredménye egy érvényes JSON-érték, de nem garantált, hogy rögzített séma legyen.  

* A Azure Cosmos DB csak a szigorú JSON-elemeket támogatja. A Type rendszer és a kifejezések kizárólag JSON-típusokkal való kezelésre korlátozódnak. További információ: [JSON-specifikáció](https://www.json.org/).  

* A Cosmos-tároló a JSON-elemek séma nélküli gyűjteménye. A tároló elemein belüli és azok közötti kapcsolatok implicit módon vannak rögzítve az adattárolással, nem az elsődleges kulcs és a idegenkulcs-kapcsolatok esetében. Ez a funkció fontos a cikk későbbi részében tárgyalt elemekhez tartozó illesztések esetében.

## <a name="next-steps"></a>Következő lépések

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT záradék](sql-query-select.md)
