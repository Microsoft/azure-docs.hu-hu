---
title: Változáscsatorna lekérési modellje
description: Megtudhatja, hogyan használhatja a Azure Cosmos DB Change feed lekérési modellt a változási csatorna és a lekéréses modell és a hírcsatorna-feldolgozó közötti különbségek olvasásához
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/09/2021
ms.reviewer: sngun
ms.openlocfilehash: ee05cbdfb2634ed7c299f736b3343ce2dfbd3520
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548403"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>A hírcsatorna lekérési modelljének módosítása Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ha a Change feed pull modellt használja, a saját tempójában használhatja a Azure Cosmos DB változási csatornát. Ahogy az [adatváltozási folyamattal](change-feed-processor.md)már megteheti a változást, a hírcsatorna-lekérési modell módosításával integrálással a változások feldolgozását több módosítási csatornán keresztül is.

> [!NOTE]
> A hírcsatorna-váltási lekérési modell jelenleg csak előzetes verzióban érhető [el a Azure Cosmos db .net SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.17.0-preview) -ban. Az előzetes verzió még nem érhető el más SDK-verziókhoz.

## <a name="comparing-with-change-feed-processor"></a>Összehasonlítás a csatorna változása folyamattal

Számos forgatókönyv képes feldolgozni a változási csatornát a [változási csatorna](change-feed-processor.md) vagy a lekéréses modell használatával. A lekéréses modell folytatási jogkivonatai és a változási hírcsatorna-feldolgozó címbérleti tárolója "Könyvjelzők" is a legutóbbi feldolgozott elemhez (vagy köteghez) a változási hírcsatornában.

A folytatási tokeneket azonban nem lehet címbérleti tárolóra konvertálni (vagy fordítva).

> [!NOTE]
> A legtöbb esetben, ha a változási hírcsatornából kell olvasnia, a legegyszerűbb lehetőség az [adatcsatorna módosítása](change-feed-processor.md).

Az alábbi helyzetekben érdemes megfontolni a lekéréses modell használatát:

- Egy adott partíciós kulcs változásainak olvasása
- Szabályozza, hogy az ügyfél milyen tempóban kapja meg a feldolgozás változásait
- A változási hírcsatornában meglévő adatok egyszeri beolvasása (például adatáttelepítés elvégzéséhez)

Íme néhány lényeges különbség a változási hírcsatorna-feldolgozó és a lekérési modell között:

|Szolgáltatás  | Változáscsatorna feldolgozója| Lekéréses modell |
| --- | --- | --- |
| A változási hírcsatorna aktuális pontjának nyomon követése | Bérlet (Azure Cosmos DB tárolóban tárolva) | Folytatási token (memóriában tárolt vagy manuálisan megőrzött) |
| A korábbi módosítások visszajátszásának lehetősége | Igen, leküldéses modellel | Igen, lekéréses modellel|
| A jövőbeli változások lekérdezése | Automatikusan ellenőrzi a módosításokat a felhasználó által megadott érték alapján `WithPollInterval` | Kézi |
| Olyan viselkedés, ahol nincsenek új módosítások | Automatikus várakozás `WithPollInterval` és ismételt vizsgálat | Meg kell fognia a kivételt, és manuálisan újra kell ellenőriznie |
| A teljes tároló változásainak feldolgozása | Igen, és automatikusan párhuzamosan, több szálon/gépen, ugyanarról a tárolóból| Igen, és manuálisan párhuzamosan a FeedTokens használatával |
| Csak egyetlen partíciós kulcs változásainak feldolgozása | Nem támogatott | Igen|
| Támogatási szint | Általánosan elérhető | Előnézet |

> [!NOTE]
> A Change feed processzor használatával történő olvasástól eltérően explicit módon kell kezelnie azokat az eseteket, amelyekben nincsenek új módosítások. 

## <a name="consuming-an-entire-containers-changes"></a>Teljes tároló módosításainak felhasználása

Létrehozhat egy t a `FeedIterator` módosítási csatorna feldolgozásához a lekéréses modell használatával. A első létrehozásakor meg `FeedIterator` kell adnia egy szükséges értéket, `ChangeFeedStartFrom` amely a módosítások olvasásának kezdő pozícióját és a kívánt értékeket is tartalmazza `FeedRange` . A a `FeedRange` partíciós kulcs értékeinek egy tartománya, és meghatározza azokat az elemeket, amelyek a változási csatornából lesznek beolvasva az adott konkrét használatával `FeedIterator` .

Opcionálisan megadhatja `ChangeFeedRequestOptions` az a beállítást is `PageSizeHint` . Az `PageSizeHint` egy lapon visszaadott elemek maximális száma.

A `FeedIterator` két ízeket tartalmaz. Az entitás-objektumokat visszaadó lenti példákon kívül a támogatással is kérheti a választ `Stream` . A streamek lehetővé teszik az adatolvasást anélkül, hogy először deszerializált, az ügyfél erőforrásain mentve.

Az alábbi példa egy olyan objektum beszerzését szemlélteti `FeedIterator` , amely entitás-objektumokat ad vissza, ebben az esetben egy `User` objektumot:

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Beginning());
```

Az alábbi példa egy `FeedIterator` , a értéket visszaadó beszerzését szemlélteti `Stream` :

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Beginning());
```

Ha nem ad meg a `FeedRange` -t `FeedIterator` , a teljes tároló változási csatornáját saját tempójában is feldolgozhatja. Íme egy példa, amely az aktuális időponttól kezdődően elkezdi az összes módosítás olvasását:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Now());

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Mivel a változási csatorna gyakorlatilag az összes jövőbeli írást és frissítést magában foglaló elemek végtelen listája, a értéke `HasMoreResults` mindig igaz. Amikor megpróbálja beolvasni a változási csatornát, és nincsenek elérhető új módosítások, a rendszer kivételt fog kapni. A fenti példában a kivételt 5 másodperccel a módosítások ismételt ellenőrzésének megkezdése előtt kell megvárni.

## <a name="consuming-a-partition-keys-changes"></a>Partíciós kulcs módosításainak felhasználása

Bizonyos esetekben előfordulhat, hogy csak egy adott partíciós kulcs módosításait szeretné feldolgozni. Beszerezhet egy `FeedIterator` adott partíciós kulcsot, és feldolgozhatja a módosításokat ugyanúgy, mint egy teljes tárolóban.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(
    ChangeFeedMode.Incremental, 
    ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue"))));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>A FeedRange használata a párhuzamos

A [change feed processzorban](change-feed-processor.md)a munka automatikusan több felhasználó között oszlik meg. A Change feed lekérési modellben a használatával `FeedRange` integrálással a módosítási csatorna feldolgozását. A a `FeedRange` partíciós kulcs értékeinek egy tartományát jelöli.

Az alábbi példa bemutatja, hogyan kérhető le a tárolóhoz tartozó tartományok listája:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

A tárolóhoz tartozó FeedRanges listájának beszerzése után a `FeedRange` [fizikai partíciók](partitioning-overview.md#physical-partitions)közül egyet kell kapnia.

A használatával `FeedRange` létrehozhatja a `FeedIterator` integrálással a változási csatorna több gépen vagy szálon történő feldolgozását. Az előző példától eltérően, amely megmutatta, hogyan szerezhet be egy-t `FeedIterator` a teljes tárolóhoz vagy egy partíciós kulcshoz, a FeedRanges használatával több FeedIterators is beszerezhető, amely párhuzamosan képes feldolgozni a változási csatornát.

Abban az esetben, ha a FeedRanges-t szeretné használni, rendelkeznie kell egy Orchestrator-folyamattal, amely beolvassa a FeedRanges, és elosztja azokat a gépeken. A terjesztés a következő lehet:

* `FeedRange.ToJsonString`A karakterlánc értékének használata és terjesztése. A felhasználók használhatják ezt az értéket a következővel `FeedRange.FromJsonString`
* Ha a terjesztés folyamatban van, a rendszer átadja az `FeedRange` objektum hivatkozását.

Íme egy példa, amely bemutatja, hogyan olvashatja el a tároló változási csatornájának elejét két feltételezett különálló, párhuzamosan olvasott gép használatával:

1. gép:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Beginning(ranges[0]));
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

2. gép:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Beginning(ranges[1]));
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="saving-continuation-tokens"></a>Folytatási tokenek mentése

`FeedIterator`Egy folytatási token létrehozásával mentheti a pozícióját. A folytatási token olyan karakterlánc-érték, amely nyomon követi a FeedIterator legutóbbi feldolgozott módosításait. Ez lehetővé teszi `FeedIterator` , hogy később folytassa a folytatást. A következő kód beolvassa a változási csatornát a tároló létrehozása óta. Ha nem áll rendelkezésre több módosítás, a rendszer továbbra is megőrzi a folytatási jogkivonatot, így később folytathatja a módosítást.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.Beginning());

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedMode.Incremental, ChangeFeedStartFrom.ContinuationToken(continuation));
```

Amíg a Cosmos-tároló továbbra is létezik, a FeedIterator folytatási tokenje soha nem jár le.

## <a name="next-steps"></a>Következő lépések

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [Az adatcsatorna módosításának használata](change-feed-processor.md)
* [Az Azure Functions aktiválása](change-feed-functions.md)