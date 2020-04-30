---
title: Migrálás a tömeges végrehajtó könyvtárából a Azure Cosmos DB .NET v3 SDK tömeges támogatásához
description: Ismerje meg, hogyan migrálhatja az alkalmazást a tömeges végrehajtó kódtár használatával a Azure Cosmos DB SDK v3-es verziójának tömeges támogatásához
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: maquaran
ms.openlocfilehash: d63b34c118cd719f73abbd6711dcb3ef02a6fb28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146295"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrálás a tömeges végrehajtó könyvtárából a Azure Cosmos DB .NET v3 SDK tömeges támogatásához

Ez a cikk azokat a szükséges lépéseket ismerteti, amelyekkel áttelepíthet egy meglévő alkalmazás kódját, amely a .net [bulk végrehajtó függvénytárat](bulk-executor-dot-net.md) használja a .net SDK legújabb verziójának [tömeges támogatás](tutorial-sql-api-dotnet-bulk-import.md) szolgáltatásához.

## <a name="enable-bulk-support"></a>Tömeges támogatás engedélyezése

Tömeges támogatás engedélyezése a `CosmosClient` példányon a [AllowBulkExecution](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) -konfiguráción keresztül:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Feladatok létrehozása az egyes műveletekhez

A .NET SDK tömeges támogatása úgy működik, hogy kihasználja a [párhuzamos függvénytárat](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) és csoportosítási műveleteket, amelyek párhuzamosan történnek. 

Az SDK-ban nincs olyan egyetlen metódus, amely a dokumentumok vagy műveletek bemeneti paraméterként való megadását végzi, ehelyett létre kell hoznia egy feladatot minden olyan művelethez, amelyet tömegesen kíván végrehajtani, majd egyszerűen várjon, amíg befejeződik.

Ha például a kezdeti bemenet azon elemek listája, amelyekben az egyes elemek a következő sémával rendelkeznek:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Ha tömeges importálást szeretne végezni (hasonlóan a BulkExecutor. BulkImportAsync-hoz), akkor egyidejű hívásokkal kell rendelkeznie `CreateItemAsync`. Például:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Ha a tömeges *frissítést* szeretné elvégezni (a [BulkExecutor. BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)-hez hasonlóan), az elem értékének frissítése után egyidejű `ReplaceItemAsync` hívásokkal kell rendelkeznie a metódushoz. Például:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

Ha pedig tömeges *törlést* szeretne végezni (hasonlóan a [BulkExecutor. BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)-hoz), az `DeleteItemAsync` `id` egyes elemek és partíciós kulcsával párhuzamos hívásokat kell megadnia a szolgáltatáshoz. Például:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Rögzítési feladat eredményének állapota

A korábbi példákban a feladatok egyidejű listáját hoztuk létre, és minden egyes feladathoz meghívjuk a `CaptureOperationResponse` metódust. Ez a módszer egy olyan bővítmény, amely lehetővé teszi, hogy a BulkExecutor *hasonló választ* lehessen fenntartani a hibák rögzítésével és a [kérési egységek használatának](request-units.md)nyomon követésével.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Ahol a `OperationResponse` a következőképpen van deklarálva:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Műveletek párhuzamos végrehajtása

A feladatok teljes listájának hatókörének nyomon követéséhez ezt a segítő osztályt használjuk:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

A `ExecuteAsync` metódus megvárja, amíg az összes művelet be nem fejeződik, és az alábbihoz hasonló módon használható:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Statisztikák rögzítése

Az előző kód vár, amíg az összes művelet be nem fejeződik, és kiszámítja a szükséges statisztikát. Ezek a statisztikák hasonlóak a tömeges végrehajtó könyvtár [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

A `BulkOperationResponse` tartalmazza a következőket:

1. A műveletek listájának tömeges támogatással végzett feldolgozásának teljes ideje.
1. A sikeres műveletek száma.
1. Az összes felhasznált kérelem egysége.
1. Ha hibák történtek, megjelenik a kivételt és a hozzá tartozó, a naplózási és azonosítási célú elemeket tartalmazó rekordok listája.

## <a name="retry-configuration"></a>Újrapróbálkozási konfiguráció

A tömeges végrehajtó függvénytár [útmutatása](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) szerint megemlítette, `MaxRetryWaitTimeInSeconds` hogy `MaxRetryAttemptsOnThrottledRequests` az és a `0` [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) a vezérlés delegálására a könyvtárba.

A .NET SDK tömeges támogatásához nincs rejtett viselkedés. Az újrapróbálkozási beállításokat közvetlenül a [CosmosClientOptions. MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) és a [CosmosClientOptions. MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests)használatával konfigurálhatja.

> [!NOTE]
> Azokban az esetekben, amikor a kiépített kérelmek mennyisége sokkal alacsonyabb, mint a várt mennyiségű adatmennyiség, érdemes lehet ezeket magas értékekre beállítani. A tömeges művelet hosszabb időt vesz igénybe, de nagyobb a valószínűsége annak, hogy a nagyobb próbálkozások miatt teljes sikerrel jár.

## <a name="performance-improvements"></a>Teljesítménnyel kapcsolatos fejlesztések

A .NET SDK-val végzett egyéb műveletekhez hasonlóan a stream API-k segítségével jobb teljesítményt és a szükségtelen szerializálást is elkerülheti. 

A stream API-k használata csak akkor lehetséges, ha a használt adat természete megegyezik a bájtos adatfolyamok (például a fájlok streamek) természetétől. Ilyen `CreateItemStreamAsync`esetekben a, `ReplaceItemStreamAsync`vagy `DeleteItemStreamAsync` a metódusok és a `ResponseMessage` (helyett `ItemResponse`) használata növeli az elérhető átviteli sebességet.

## <a name="next-steps"></a>További lépések

* A .NET SDK kiadásával kapcsolatos további tudnivalókért tekintse meg a [Azure Cosmos db SDK](sql-api-sdk-dotnet.md) -cikket.
* Szerezze be a teljes [áttelepítési forráskódot](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) a githubról.
* [További tömeges minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
