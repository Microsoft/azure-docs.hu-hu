---
title: A Azure Cosmos DB metrikáinak monitorozása és hibakeresése
description: A Azure Cosmos DB mérőszámai az általános problémák hibakereséséhez és az adatbázis figyeléséhez használhatók.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0711d764514e45d3c28e26cf99b45dc711ef201c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868256"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>A Azure Cosmos DB metrikáinak monitorozása és hibakeresése
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos DB az átviteli sebességhez, a tároláshoz, a konzisztenciához, a rendelkezésre álláshoz és a késéshez nyújt metrikákat. Az Azure Portal ezen metrikák összesített nézetét nyújtja. Az Azure Cosmos DB-metrikákat az Azure Monitor API-ból is megtekintheti. A metrikák, például a tároló neve nem megkülönbözteti a kis-és nagybetűket. Ezért a kis-és nagybetűket megkülönböztető összehasonlítást kell használnia, amikor karakterlánc-összehasonlítást végez ezen dimenzió értékeken. Az Azure monitor metrikáinak megtekintéséről a [metrikák Beolvasása Azure monitor](./monitor-cosmos-db.md) cikkből tájékozódhat.

Ez a cikk a gyakori használati eseteket, valamint a problémák Azure Cosmos DB-metrikákkal való elemzését és hibakeresését mutatja be. A metrikák gyűjtése öt percenként történik, és hét napig tart.

## <a name="view-metrics-from-azure-portal"></a>Metrikák megtekintése Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg a **metrikák** ablaktáblát. Alapértelmezés szerint a metrikák ablaktábla megjeleníti az Azure Cosmos-fiókban lévő összes adatbázis tárolóját, indexét, a kérelmek egységének mérőszámait. Ezeket a metrikákat adatbázis, tároló vagy régió szerint szűrheti. A mérőszámokat egy adott időrészletességgel is szűrheti. Az átviteli sebességre, a tárterületre, a rendelkezésre állásra, a késésre és a konzisztencia-mérőszámokra vonatkozó további részletek külön lapokon találhatók. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Cosmos DB teljesítmény-metrikák a Azure Portal":::

A **metrikák** ablaktáblán a következő metrikák érhetők el: 

* **Átviteli sebesség mérőszámai** – ez a metrika a felhasznált vagy sikertelen kérelmek számát mutatja (429 válasz kódja), mert túllépte a tárolóhoz kiépített átviteli sebességet vagy tárolókapacitást.

* **Tárolási mérőszámok** – ez a metrika az adatok méretét és az indexelési használatot mutatja.

* **Rendelkezésre állási mérőszámok** – ez a metrika a sikeres kérelmek százalékos arányát jeleníti meg az óránkénti kérelmek száma alapján. A sikerességi arányt a Azure Cosmos DB SLA-kat határozzák meg.

* **Késési metrikák** – ez a metrika az Azure Cosmos db által megfigyelt olvasási és írási késést mutatja abban a régióban, ahol a fiókja működik. Egy földrajzilag replikált fiók esetében megjelenítheti a késést a régiók között. Ez a metrika nem a végpontok közötti kérelem késését jelöli.

* **Konzisztencia-mérőszámok** – ez a metrika azt mutatja be, hogy az esetleges konzisztencia a kiválasztott konzisztencia-modellnél. A többrégiós fiókok esetében ez a metrika a kiválasztott régiók közötti replikációs késést is megjeleníti.

* **Rendszermetrikák** – ez a metrika azt mutatja, hogy az elsődleges partíció hány metaadat-kérelmet szolgáltat. Emellett segít azonosítani a szabályozott kérelmeket.

A következő szakaszokban ismertetjük azokat a gyakori forgatókönyveket, amelyekben Azure Cosmos DB mérőszámok használhatók. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Megtudhatja, hány kérelem sikeres vagy hibát okoz

Első lépésként lépjen a [Azure Portalra](https://portal.azure.com) , és navigáljon a **metrikák** panelre. A panelen keresse meg a * * kérelmek száma meghaladja a kapacitást egy 1 perces diagramon. Ez a diagram az állapotkód által szegmentált percenkénti kérelmek számát mutatja. További információ a HTTP-állapotkódok használatáról: [Azure Cosmos db HTTP-állapotkódok](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

A leggyakoribb hiba az állapotkód 429 (ráta korlátozása/szabályozása). Ez a hiba azt jelenti, hogy a Azure Cosmos DBre irányuló kérelmek többek, mint a kiosztott átviteli sebesség. A probléma leggyakoribb megoldása az, hogy az adott gyűjteményhez tartozó [RUs vertikális felskálázását](./set-throughput.md) .

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Kérelmek száma percenként":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>Az átviteli sebesség eloszlásának meghatározása a partíciók között

A partíciós kulcsok jó kihasználása elengedhetetlen a skálázható alkalmazások számára. A particionált tárolók partíciók szerinti elosztásának meghatározásához navigáljon a [Azure Portal](https://portal.azure.com) **metrikák** paneljére. Az **átviteli sebesség** lapon a tárolási bontás az **egyes fizikai partíciós diagramok maximális felhasznált ru/másodpercben** jelenik meg. Az alábbi ábrán egy példa látható az olyan gyenge adateloszlásra, amelyet a bal szélen lévő elferdített partíció is mutat.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Egyetlen partíció, amely nagy kihasználtságot lát":::

Az egyenetlen átviteli sebesség a *gyakori partíciókat* okozhatja, ami szabályozható kérelmeket eredményezhet, és újraparticionálást igényelhet. A Azure Cosmos DB particionálásával kapcsolatos további információkért lásd: [particionálás és skálázás Azure Cosmos DBban](./partitioning-overview.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>A tárolási eloszlás meghatározása a partíciók között

A partíciók jó kihasználása elengedhetetlen a skálázható alkalmazások számára. Ha meg szeretné határozni, hogy a particionált tárolók tárolási eloszlása partíciók szerinti bontásban történjen-e, a [Azure Portal](https://portal.azure.com)metrikák paneljén. A Storage (tárolás) lapon a tárolók lebontása a felső partíciós kulcsok diagram által felhasznált adattábla és index tárolóban jelenik meg. Az alábbi ábra az adattárolás gyenge eloszlását mutatja be, ahogy azt a bal szélen található ferde partíció mutatja.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Gyenge adateloszlás – példa":::

A diagramon a partícióra kattintva megadhatja, hogy melyik partíciós kulcs döntse el a terjesztést.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="A partíciós kulcs elferdíti az eloszlást":::

Miután meghatározta, hogy melyik partíciós kulcs okozza az elferdítés eloszlását, lehet, hogy újra kell particionálnia a tárolót egy elosztott partíciós kulccsal. A Azure Cosmos DB particionálásával kapcsolatos további információkért lásd: [particionálás és skálázás Azure Cosmos DBban](./partitioning-overview.md).

## <a name="compare-data-size-against-index-size"></a>Az adatméret összehasonlítása az index méretével

Azure Cosmos DB a teljes felhasznált tárterület az adatméret és az index méretének kombinációja. Az index mérete általában az adatméret töredéke. További információt az [index mérete](index-policy.md#index-size) című cikkben talál. A [Azure Portal](https://portal.azure.com)metrika paneljén a Storage (tárolás) lapon az adatok és az index alapján megjelenő tárterület-felhasználás bontása látható.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Ha meg szeretné őrizni az indexelési területet, módosíthatja az [indexelési házirendet](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>A lekérdezések lassú futásának hibakeresése

Az SQL API SDK-k Azure Cosmos DB lekérdezés-végrehajtási statisztikát biztosítanak.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

A *QueryMetrics* részletesen ismerteti, hogy mennyi ideig tartott a lekérdezés egyes összetevőinek végrehajtása. A hosszú ideig futó lekérdezések esetében a leggyakoribb kiváltó ok a vizsgálatok, ami azt jelenti, hogy a lekérdezés nem tudta kihasználni az indexeket. Ez a probléma jobb szűrőfeltétel használatával oldható meg.

## <a name="next-steps"></a>Következő lépések

Most megtanulta, hogyan figyelheti és hibakeresési hibákat a Azure Portalban megadott mérőszámok használatával. Ha többet szeretne megtudni az adatbázis teljesítményének növeléséről, olvassa el a következő cikkeket:

* Az Azure monitor metrikáinak megtekintéséről a [metrikák Beolvasása Azure monitor](./monitor-cosmos-db.md) cikkből tájékozódhat. 
* [Teljesítmény-és méretezési tesztelés a Azure Cosmos DB](performance-testing.md)
* [Teljesítménnyel kapcsolatos tippek az Azure Cosmos DB-hez](performance-tips.md)