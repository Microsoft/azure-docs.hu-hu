---
title: Migrálás a Change feed Processor Library-ről a Azure Cosmos DB .NET v3 SDK-ra
description: Megtudhatja, hogyan migrálhatja az alkalmazást a refeed Processor Library módosítása a Azure Cosmos DB SDK v3 verzióra
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/17/2019
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: ce2d4d3ad3ae349718f01584ec077b18e11e4f8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341263"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrálás a Change feed Processor Library-ről a Azure Cosmos DB .NET v3 SDK-ra
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk azokat a szükséges lépéseket ismerteti, amelyekkel áttelepítheti a meglévő alkalmazás kódját, amely a a .NET SDK legújabb verziójában (más néven .NET v3 SDK) módosítja a [hírcsatorna-feldolgozó függvénytárának](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) módosítása funkciót a [változási](change-feed.md) hírcsatorna szolgáltatásban.

## <a name="required-code-changes"></a>Szükséges kód módosításai

A .NET v3 SDK több feltörési változást is tartalmaz, az alábbi lépésekkel telepítheti át az alkalmazást:

1. Alakítsa át a `DocumentCollectionInfo` példányokat `Container` a figyelt és a bérleti tárolók hivatkozásaira.
1. A használatban lévő testreszabásokat a `WithProcessorOptions` használat `WithLeaseConfiguration` és `WithPollInterval` az intervallumok, a `WithStartTime` [Kezdési idő](./change-feed-processor.md#starting-time)és `WithMaxItems` a maximális elemek számának meghatározására kell frissíteni.
1. A on érték megadásával `processorName` `GetChangeFeedProcessorBuilder` egyeztetheti a `ChangeFeedProcessorOptions.LeasePrefix` vagy `string.Empty` más módon konfigurált értéket.
1. A módosítások már nem kerülnek be a rendszerbe, hanem az a `IReadOnlyList<Document>` `IReadOnlyCollection<T>` hely, ahol meg `T` kell határozni a kívánt típust, már nincs alapelem osztálya.
1. A módosítások kezeléséhez már nincs szükség megvalósításra, hanem meg kell [adnia egy delegált](change-feed-processor.md#implementing-the-change-feed-processor). A delegált lehet statikus függvény, vagy ha meg kell őriznie az állapotot a végrehajtások között, létrehozhat egy saját osztályt, és delegált néven is átadhat egy példány-metódust.

Ha például az eredeti kód a módosítási hírcsatorna processzorának összeállítására szolgál, a következőképpen néz ki:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Az áttelepített kód a következőképpen fog kinézni:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

A delegált pedig statikus metódus lehet:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Állapot-és bérlet tároló

A Restore Processor Library-hez hasonlóan a .NET v3 SDK-ban a hírcsatorna módosítása szolgáltatás egy [bérlet tárolót](change-feed-processor.md#components-of-the-change-feed-processor) használ az állapot tárolásához. A sémák azonban eltérőek.

Az SDK v3 változási hírcsatorna-feldolgozó processzora észlelni fogja a régi függvénytár-állapotot, és automatikusan áttelepíti az új sémára az áttelepített alkalmazás kódjának első végrehajtásakor. 

A régi kóddal biztonságosan leállíthatja az alkalmazást, áttelepítheti a kódot az új verzióra, elindíthatja az áttelepített alkalmazást, és az alkalmazás leállításakor bekövetkezett változásokat az új verzió fogja felvenni és feldolgozni.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat olvashat a hírcsatorna-feldolgozó szolgáltatással kapcsolatos változásokról:

* [A hírcsatorna-feldolgozó változásának áttekintése](change-feed-processor.md)
* [A változáscsatorna-becslő használata](how-to-use-change-feed-estimator.md)
* [Változáscsatorna-feldolgozó indításának időpontja](./change-feed-processor.md#starting-time)