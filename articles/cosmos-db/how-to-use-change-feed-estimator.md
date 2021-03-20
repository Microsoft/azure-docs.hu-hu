---
title: A Change feed kalkulátor használata – Azure Cosmos DB
description: Ismerje meg, hogy miként elemezheti a változási hírcsatorna-feldolgozók állapotát a Change feed kalkulátor használatával
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/15/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: a44557d15f437317c2b5fa659ab8d4ca3c208edf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339836"
---
# <a name="use-the-change-feed-estimator"></a>A módosítási hírcsatorna kalkulátor használata
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk azt ismerteti, hogyan figyelheti meg a [változási hírcsatorna processzor](./change-feed-processor.md) példányainak állapotát, ahogy beolvasták a változási csatornát.

## <a name="why-is-monitoring-progress-important"></a>Miért fontos a figyelési folyamat?

A Change feed processzor olyan mutatóként működik, amely a [változási csatornán](./change-feed.md) halad előre, és a delegált implementációban végez módosításokat. 

A módosítási hírcsatorna-feldolgozó üzembe helyezése az elérhető erőforrások, például a processzor, a memória, a hálózat stb. alapján feldolgozhatja a módosításokat egy adott sebességgel.

Ha ez a sebesség lassabb, mint a változásoknak az Azure Cosmos-tárolóban történt változásai, akkor a processzor elmarad.

Ennek a forgatókönyvnek az azonosítása segít megérteni, hogy szükség van-e a Change feed processzor üzembe helyezésének skálázására.

## <a name="implement-the-change-feed-estimator"></a>A Change feed kalkulátor implementálása

A [change feed processzorhoz](./change-feed-processor.md)hasonlóan a Change feed kalkulátor leküldéses modellként működik. A kalkulátor méri a legutóbb feldolgozott elem (a címbérletek állapota által meghatározott) és a tároló legutóbbi változása közötti különbséget, és leküldi ezt az értéket egy delegált értékre. A mérés elvégzésének időköze az alapértelmezett 5 másodperces értékkel is testreszabható.

Ha például a változási csatorna processzora a következőképpen van definiálva:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

A becslések inicializálásának helyes módja, ha azt méri, hogy a processzor a következőképpen fog `GetChangeFeedEstimatorBuilder` kinézni:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Ahol a processzor és a kalkulátor is ugyanazt a `leaseContainer` nevet használja.

A másik két paraméter a delegált, amely egy számot fog kapni, amely azt jelzi, hogy hány **módosítást kell olvasni** a processzor, valamint azt az időintervallumot, amelyen a mérést el szeretné végezni.

A becslést fogadó delegált példa:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Ezt a becslést elküldheti a figyelési megoldásnak, és azt is megtudhatja, hogyan viselkedik az előrehaladás az idő múlásával.

> [!NOTE]
> A módosítási hírcsatorna-kalkulátor nem szükséges a módosítási csatorna processzorának részeként telepíteni, és nem lehet ugyanannak a projektnek a része. Független lehet, és egy teljesen más példányban futtatható. Csak ugyanazt a nevet és címbérleti konfigurációt kell használnia.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat olvashat a hírcsatorna-feldolgozó szolgáltatással kapcsolatos változásokról:

* [A hírcsatorna-feldolgozó változásának áttekintése](change-feed-processor.md)
* [Változáscsatorna-feldolgozó indításának időpontja](./change-feed-processor.md#starting-time)