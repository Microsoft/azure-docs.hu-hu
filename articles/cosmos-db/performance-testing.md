---
title: Teljesítmény-és méretezési tesztelés a Azure Cosmos DB
description: Ismerje meg, hogyan végezheti el a méretezést és a teljesítmény tesztelését Azure Cosmos DB használatával. Ezután kiértékelheti Azure Cosmos DB funkcióit a nagy teljesítményű alkalmazási forgatókönyvek esetében.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: cb0dd8eb879c5511da3c79a3dee6a715fb1275e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358643"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Teljesítmény-és méretezési tesztelés a Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A teljesítmény-és méretezési teszt az alkalmazások fejlesztésének egyik kulcsfontosságú lépése. Számos alkalmazás esetében az adatbázis szintje jelentős hatással van a teljes teljesítményre és méretezhetőségre. Ezért a teljesítmény-tesztelés kritikus összetevője. [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) a rugalmas skálázás és kiszámítható teljesítmény érdekében készült. Ezek a képességek nagyszerű illeszkedést biztosítanak az olyan alkalmazások számára, amelyeknek nagy teljesítményű adatbázis-rétegre van szükségük. 

Ez a cikk a teljesítmény-tesztelési csomagokat végrehajtó fejlesztőknek készült Azure Cosmos DB munkaterhelések esetében. Emellett a nagy teljesítményű alkalmazás-forgatókönyvek Azure Cosmos DB kiértékeléséhez is használható. Elsődlegesen az adatbázis elszigetelt teljesítmény-tesztelésére összpontosít, de az éles alkalmazások esetében ajánlott eljárásokat is tartalmaz.

A cikk elolvasása után a következő kérdésekre tud válaszolni: 

* Hol találhatok egy minta .NET-ügyfélalkalmazás Azure Cosmos DB teljesítményének teszteléséhez? 
* Hogyan magas átviteli sebességet érhet el az ügyfélalkalmazás Azure Cosmos DBával?

A kód megkezdéséhez töltse le a projektet [Azure Cosmos db teljesítmény-tesztelési mintából](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Az alkalmazás célja, hogy bemutassa, hogyan érheti el a lehető legjobb teljesítményt Azure Cosmos DB kis számú ügyfélszámítógépről. A minta célja nem a Azure Cosmos DB maximális átviteli kapacitásának (amely korlátok nélkül méretezhető) elérésére szolgál.

Ha az Azure Cosmos DB teljesítmény javítása érdekében ügyféloldali konfigurációs beállításokat keres, tekintse meg a [Azure Cosmos db teljesítményével kapcsolatos tippeket](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>A teljesítmény-tesztelési alkalmazás futtatása
Első lépésként a leggyorsabb módszer a .NET-minta fordítása és futtatása az alábbi lépések szerint. Emellett áttekintheti a forráskódot, és hasonló konfigurációkat is alkalmazhat a saját ügyfélalkalmazások számára.

**1. lépés:** Töltse le a projektet [Azure Cosmos db teljesítmény-tesztelési mintából](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark), vagy a GitHub-tárházat.

**2. lépés:** Módosítsa a EndpointUrl, a AuthorizationKey, a CollectionThroughput és a DocumentTemplate beállításait (opcionális) a App.config.

> [!NOTE]
> Mielőtt nagy adatátviteli sebességű gyűjteményeket kiépíteni, tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/) , és becsülje meg a begyűjtési költségeket. Azure Cosmos DB a számlák tárolását és átviteli sebességét óránként, egymástól függetlenül. A tesztelés után az Azure Cosmos-tárolók teljesítményének törlésével vagy csökkentésével költségeket takaríthat meg.
> 
> 

**3. lépés:** Fordítsa le és futtassa a Console alkalmazást a parancssorból. Az alábbihoz hasonló kimenetnek kell megjelennie:

```bash
C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
Summary:
---------------------------------------------------------------------
Endpoint: https://arramacquerymetrics.documents.azure.com:443/
Collection : db.data at 100000 request units per second
Document Template*: Player.json
Degree of parallelism*: -1
---------------------------------------------------------------------
DocumentDBBenchmark starting...
Found collection data with 100000 RU/s
Starting Inserts with 100 tasks
Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

Summary:
---------------------------------------------------------------------
Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
---------------------------------------------------------------------
DocumentDBBenchmark completed successfully.
Press any key to exit...
```

**4. lépés (ha szükséges):** Az eszközről jelentett átviteli sebesség (RU/s) nem lehet nagyobb, mint a gyűjtemény vagy gyűjtemény kiépített átviteli sebessége. Ha nem, akkor a kis léptékű Analyticsunits növelése segíthet a korlát elérésében. Ha az ügyfél-alkalmazás-fennsíkon lévő átviteli sebesség, további ügyfélgépeken indítsa el az alkalmazás több példányát. Ha segítségre van szüksége ennek a lépésnek a fájljához, a [Azure Portal](https://portal.azure.com)támogatási jegyét.

Miután futtatta az alkalmazást, különböző [indexelési házirendeket](index-policy.md) és konzisztencia- [szinteket](consistency-levels.md) kipróbálva megtudhatja, milyen hatással vannak a teljesítményre és a késésre. Emellett áttekintheti a forráskódot, és hasonló konfigurációkat hozhat létre saját tesztelési lakosztállyal vagy éles alkalmazásokhoz.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben azt vizsgáltuk, hogyan végezheti el a teljesítmény és a méretezés tesztelését Azure Cosmos DB használatával egy .NET-konzol alkalmazás használatával. További információért tekintse át a következő cikkeket:

* [Azure Cosmos DB teljesítmény-tesztelési minta](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark)
* [Ügyfél-konfigurációs beállítások a Azure Cosmos DB teljesítményének növeléséhez](performance-tips.md)
* [Kiszolgálóoldali particionálás Azure Cosmos DB](partitioning-overview.md)


