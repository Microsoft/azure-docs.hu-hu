---
title: Monitor és hibakeresés metrikák segítségével a Azure Cosmos DB
description: A metrikák használatával Azure Cosmos DB hibák hibakeresését és az adatbázis figyelése érdekében.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/09/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: cf92d9e1a1f92c2dc3294b71e3e620166fd90680
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818698"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitor és hibakeresés metrikák segítségével a Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos DB az átviteli sebességhez, a tároláshoz, a konzisztenciához, a rendelkezésre álláshoz és a késéshez nyújt metrikákat. Az Azure Portal ezen metrikák összesített nézetét nyújtja. Az Azure Cosmos DB-metrikákat az Azure Monitor API-ból is megtekintheti. A metrikák dimenzióértékei, például a tároló neve, nem érzékenyek. Ezért ezeket a dimenzióértékeket sztring-összehasonlítások esetén a kis- és a nagy- és kis- és nagy közötti összehasonlításhoz is használnia kell. Ha meg szeretne ismerkedni az Azure Monitor metrikainak megtekintésével, olvassa el a Metrikák be Azure Monitor [cikket.](./monitor-cosmos-db.md)

Ez a cikk a gyakori használati eseteket, valamint a problémák Azure Cosmos DB-metrikákkal való elemzését és hibakeresését mutatja be. A rendszer öt percenként gyűjti a metrikákat, és hét napig van megőrizve.

## <a name="view-metrics-from-azure-portal"></a>Metrikák megtekintése a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **a Metrikák panelt.** Alapértelmezés szerint a Metrikák panel az Azure Cosmos-fiók összes adatbázisának tárolási, indexelési és kérelemegység-metrikákat jeleníti meg. Ezeket a metrikákat adatbázisonként, tárolónként vagy régiónként szűrheti. A metrikákat adott idő részletességgel is szűrheti. Az átviteli sebességre, a tárterületre, a rendelkezésre állásra, a késésre és a konzisztenciára vonatkozó metrikákról további részleteket külön lapokon talál. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Cosmos DB teljesítménymetrikák a Azure Portal":::

A Metrikák panelen a következő **metrikák érhetők** el:

* **Átvitelisebesség-metrikák** – Ez a metrika a felhasznált vagy meghiúsult kérések számát mutatja (429 válaszkód), mert a tárolóhoz kihelyelt átviteli sebesség vagy tárolási kapacitás túllépte a korlátot.

* **Tárolási metrikák** – Ez a metrika az adatok méretét és az indexhasználatot mutatja.

* **Rendelkezésre állási metrikák** – Ez a metrika a sikeres kérések százalékos arányát mutatja óránkénti összes kérelemre. A sikeresség mértékét a sikeres Azure Cosmos DB határozza meg.

* **Késési metrikák** – Ez a metrika a fiók működési régiójában Azure Cosmos DB olvasási és írási késést mutatja. Vizualizálhatja a régiók közötti késést egy georeplikált fiók esetében. Ez a metrika nem a kérések végpontok közötti késését képviseli.

* **Konzisztenciametrikák** – Ez a metrika bemutatja, hogy a kiválasztott konzisztenciamodell konzisztenciája milyen lesz a 2012-es es konzisztenciaszinten. Többrégió-fiókok esetében ez a metrika a kiválasztott régiók közötti replikációs késést is megjeleníti.

* **Rendszermetrikák** – Ez a metrika azt mutatja meg, hogy hány metaadatkérést kiszolgál az elsődleges partíció. Emellett segít azonosítani a szabályozással lekért kéréseket is.

Az alábbi szakaszok olyan gyakori forgatókönyveket ismertetnek, amelyekben a metrikák Azure Cosmos DB használhatók. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>A sikeres vagy hibákat okozó kérések száma

Első lépésként lépjen az Azure Portal [a](https://portal.azure.com) **Metrikák panelre.** A panelen keresse meg a **Kapacitást túllépő kérelmek száma 1 percenként diagramot. Ez a diagram percenként jeleníti meg az állapotkód szerint szegmentált összes kérelmet. A HTTP-állapotkódokkal kapcsolatos további információkért lásd: [HTTP-állapotkódok a Azure Cosmos DB.](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)

A leggyakoribb hibaállapot-kód a 429 (sebességkorlátozás/szabályozás). Ez a hiba azt jelenti, hogy a Azure Cosmos DB kérések nagyobbak a kiépített átviteli sebességnél. A leggyakoribb megoldás erre a problémára az adott [gyűjteményhez](./set-throughput.md) megadott ru-k horizontális felskálása.

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Kérelmek percenkénti száma":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>A partíciók közötti átviteli sebesség elosztásának meghatározása

A partíciókulcsok megfelelő számossága elengedhetetlen minden méretezhető alkalmazáshoz. A particionált tárolók partíciókra bontott átviteli sebességelosztásának meghatározásához lépjen a metrikák panelre a [Azure Portal.](https://portal.azure.com)  Az Átviteli **sebesség lapon a** tárterület lebontása az egyes fizikai partíciók által felhasznált maximális **RU/másodperc diagramon látható.** Az alábbi ábra egy példát mutat be az adatok gyenge eloszlására, ahogyan azt a bal távoli, eltolt partíció mutatja.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Egypartíciós, nagy kihasználtságú":::

Az egyenlőtlen átviteli  sebesség eloszlása forró partíciókat okozhat, ami a kérelmek szabályozását eredményezheti, és újraparticionálást követelhet meg. További információ a particionálásról a [Azure Cosmos DB: Particionálás és skálázás a Azure Cosmos DB.](./partitioning-overview.md)

## <a name="determine-the-storage-distribution-across-partitions"></a>A partíciók közötti tárterület-elosztás meghatározása

A partíció jó számossága elengedhetetlen minden méretezhető alkalmazáshoz. A particionált tárolók tárolóeloszlásának partíciókra bontott meghatározásához a panel metrikák paneljét [Azure Portal.](https://portal.azure.com) A Tárolás lapon a tárterület lebontása a felső partíciókulcsok által felhasznált adat- és indextároló diagramon látható. Az alábbi ábra az adattárolás gyenge elosztását szemlélteti, ahogyan azt a bal távoli, eltolt partíció mutatja.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Példa a gyenge adateloszlásra":::

A diagramon a partícióra kattintva kiváltó okként használhatja, hogy melyik partíciókulcs stornonja el az elosztást.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="A partíciókulcs eltorlja az elosztást":::

Miután azonosította, hogy melyik partíciókulcs okozza az elosztási lemezeloszlást, előfordulhat, hogy egy elosztottabb partíciókulccsal kell újraparticionálta a tárolót. További információ a particionálásról a [Azure Cosmos DB: Particionálás és skálázás a Azure Cosmos DB.](./partitioning-overview.md)

## <a name="compare-data-size-against-index-size"></a>Az adatméret összehasonlítása az index méretével

A Azure Cosmos DB a teljes felhasznált tárterület az Adatméret és az Indexméret kombinációja. Az index mérete általában az adatméret törtrésze. További tudnivalókért tekintse meg az [Index mérete cikket.](index-policy.md#index-size) A tárterület Metrikák [Azure Portal](https://portal.azure.com)Tárolás lapján látható a tárhelyfelhasználás lebontása az adatok és az index alapján.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Ha meg szeretné takarékosni az indexterületet, módosíthatja az [indexelési szabályzatot.](index-policy.md)

## <a name="debug-why-queries-are-running-slow"></a>A lekérdezések lassú futásának hibakeresése

Az SQL API-kban a Azure Cosmos DB biztosít lekérdezés-végrehajtási statisztikákat.

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

*A QueryMetrics* részletesen bemutatja, hogy mennyi ideig tartott a lekérdezés egyes komponensének végrehajtása. A hosszú ideig futó lekérdezések leggyakoribb kiváltó oka a vizsgálatok, ami azt jelenti, hogy a lekérdezés nem tudta használni az indexeket. Ez a probléma jobb szűrési feltétellel oldható meg.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan figyelheti a problémákat és hogyan lehet hibakeresést végezni a Azure Portal. A következő cikkekből többet is megtudhat az adatbázis teljesítményének javításáról:

* Az Azure Monitorból származó metrikák megtekintéséről a Metrikák lekérte [a](./monitor-cosmos-db.md) Azure Monitor cikkben talál. 
* [Teljesítmény- és mérettesztelés Azure Cosmos DB](performance-testing.md)
* [Teljesítménnyel kapcsolatos tippek az Azure Cosmos DB-hez](performance-tips.md)