---
title: Tömeges végrehajtó .NET-kódtár használata Azure Cosmos DB tömeges importálási és frissítési műveletekhez
description: A Azure Cosmos DB dokumentumok tömeges importálása és frissítése a tömeges végrehajtó .NET-kódtár használatával.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 34aef5bd880e3ef080676fb9e90e62796d499e7b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429816"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Tömeges műveletek végrehajtása a tömeges végrehajtó .NET-kódtár használatával Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> A jelen cikkben ismertetett tömeges végrehajtó függvénytár a .NET SDK 2. x verzióját használó alkalmazások számára van fenntartva. Az új alkalmazásokhoz használhatja a [.net SDK 3. x verziójában](tutorial-sql-api-dotnet-bulk-import.md) közvetlenül elérhető **tömeges támogatást** , és nincs szükség külső könyvtárra. 

> Ha jelenleg a tömeges végrehajtó függvénytárat használja, és azt tervezi, hogy az újabb SDK-ban csoportos támogatásra kíván áttérni, az [áttelepítési útmutató](how-to-migrate-from-bulk-executor-library.md) lépéseit követve telepítse át az alkalmazást.

Ez az oktatóanyag útmutatást nyújt ahhoz, hogyan importálhat és frissíthet dokumentumokat egy Azure Cosmos-tárolóban a tömeges végrehajtó .NET-kódtárának használatával. Ha többet szeretne megtudni a tömeges végrehajtó függvénytárról, valamint arról, hogy miként segíti a nagy átviteli sebesség és a tárterület kihasználását, tekintse meg a [tömeges végrehajtó függvénytár áttekintését](bulk-executor-overview.md) ismertető cikket. Ebben az oktatóanyagban egy minta .NET-alkalmazást fog látni, amely a véletlenszerűen generált dokumentumokat egy Azure Cosmos-tárolóba importálja. Az importálást követően bemutatjuk, hogyan frissítheti az importált adatok tömeges frissítését úgy, hogy az adott dokumentum mezőin végrehajtandó műveletekként megadhatja a javításokat.

Jelenleg a tömeges végrehajtó függvénytárat csak a Azure Cosmos DB SQL API és a Gremlin API-fiókok támogatják. Ez a cikk a tömeges végrehajtó .NET-függvénytár SQL API-fiókokkal való használatát ismerteti. A Gremlin API-fiókokkal rendelkező tömeges végrehajtó .NET-függvénytár használatáról további információt a következő témakörben talál: [tömeges műveletek végrehajtása a Azure Cosmos db GREMLIN API-ban](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Előfeltételek

* Ha még nincs telepítve a Visual Studio 2019, letöltheti és használhatja a [Visual studio 2019 Community Editiont](https://www.visualstudio.com/downloads/). Győződjön meg arról, hogy engedélyezi az "Azure-fejlesztést" a Visual Studio telepítése során.

* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Az Azure-előfizetések nélkül, díjmentesen és kötelezettségvállalásokon keresztül [Azure Cosmos db ingyen kipróbálhatja](https://azure.microsoft.com/try/cosmosdb/) . Vagy használhatja a [Azure Cosmos db emulátort](./local-emulator.md) a `https://localhost:8081` végponttal. Az elsődleges kulcs a [Kérelmek hitelesítése](local-emulator.md#authenticate-requests) című részben található.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot a .NET gyors üzembe helyezési cikk [adatbázis-fiók létrehozása](create-sql-api-dotnet.md#create-account) című szakaszában ismertetett lépések segítségével.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most váltson a kóddal való használatra egy minta .NET-alkalmazás letöltésével a GitHubról. Ez az alkalmazás tömeges műveleteket hajt végre az Azure Cosmos-fiókjában tárolt adatokon. Az alkalmazás klónozásához nyisson meg egy parancssort, Navigáljon arra a könyvtárra, ahová másolni szeretné, majd futtassa a következő parancsot:

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

A klónozott tárház két mintát tartalmaz: "BulkImportSample" és "BulkUpdateSample". Megnyithatja a minta alkalmazások egyikét, frissítheti App.config fájlban lévő kapcsolatok karakterláncait a Azure Cosmos DB-fiókja kapcsolatainak karakterláncait, felépítheti a megoldást, és futtathatja.

A "BulkImportSample" alkalmazás véletlenszerű dokumentumokat hoz létre, és tömegesen importálja őket az Azure Cosmos-fiókjába. A "BulkUpdateSample" alkalmazás az importált dokumentumokat úgy frissíti, hogy a javításokat az adott dokumentum mezőin végrehajtandó műveletekként határozza meg. A következő részekben ezeket a példákat fogja áttekinteni a kódban.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Az Azure Cosmos-fiókba való tömeges importálás

1. Navigáljon a "BulkImportSample" mappára, és nyissa meg a "BulkImportSample. SLN" fájlt.  

2. A Azure Cosmos DB kapcsolatok karakterláncait a rendszer a App.config fájlból olvassa be, ahogy az a következő kódban látható:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   A tömeges importáló egy új adatbázist és egy tárolót hoz létre az adatbázis nevével, a tároló nevével és a App.config fájlban megadott átviteli sebesség értékével.

3. A DocumentClient objektum következő közvetlen TCP-kapcsolati móddal van inicializálva:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. A BulkExecutor objektum a várakozási idő és a szabályozott kérelmek esetében magas újrapróbálkozási értékkel van inicializálva. Ezután a 0 értékre vannak állítva, hogy átadja a zsúfoltság-vezérlést a BulkExecutor élettartama érdekében.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Az alkalmazás meghívja a BulkImportAsync API-t. A .NET-függvénytár két túlterhelést biztosít a tömeges importálási API-nak, amely elfogadja a szerializált JSON-dokumentumok listáját, valamint a másikat, amely elfogadja a deszerializált POCO-dokumentumok listáját. Ha többet szeretne megtudni ezekről a túlterhelt módszerekről, tekintse meg az [API dokumentációját](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **A BulkImportAsync metódus a következő paramétereket fogadja el:**
   
   |**Paraméter**  |**Leírás** |
   |---------|---------|
   |enableUpsert    |   Egy jelző, amely engedélyezi a upsert műveleteket a dokumentumokon. Ha a megadott AZONOSÍTÓJÚ dokumentum már létezik, frissül. Alapértelmezés szerint hamis értékre van állítva.      |
   |disableAutomaticIdGeneration    |    Az azonosító automatikus generálásának letiltására szolgáló jelző. Alapértelmezés szerint igaz értékre van állítva.     |
   |maxConcurrencyPerPartitionKeyRange    | A partíciós kulcs tartományának maximális párhuzamossági foka, a NULL értékre állítás a függvénytár alapértelmezett értékét fogja használni. |
   |maxInMemorySortingBatchSize     |  A dokumentum-enumerálásból lekért dokumentumok maximális száma, amely minden fázisban az API-hívásnak lesz átadva. A tömeges importálás előtt megjelenő memóriabeli rendezési fázisban a paraméter null értékre állítása esetén a könyvtár az alapértelmezett minimális értéket fogja használni (Documents. Count, 1000000).       |
   |cancellationToken    |    A lemondási token, amely szabályosan kilép a tömeges importálási műveletből.     |

   **Tömeges importálási válasz objektumának definíciója** A tömeges importálási API-hívás eredménye a következő attribútumokat tartalmazza:

   |**Paraméter**  |**Leírás**  |
   |---------|---------|
   |NumberOfDocumentsImported (hosszú)   |  Azon dokumentumok teljes száma, amelyek importálása sikeresen megtörtént a tömeges importálási API-híváshoz megadott összes dokumentumból.       |
   |TotalRequestUnitsConsumed (dupla)   |   A tömeges importálási API-hívás által felhasznált összes kérelmek egysége (RU).      |
   |TotalTimeTaken (TimeSpan)    |   A tömeges importálási API hívásának teljes ideje a végrehajtás befejezéséhez.      |
   |BadInputDocuments (lista \<object> )   |     Azon helytelen formátumú dokumentumok listája, amelyeket nem sikerült importálni a tömeges importálási API-hívásban. Javítsa ki a visszaadott dokumentumokat, és próbálkozzon újra az importálással. A helytelen formátumú dokumentumok közé tartoznak azok a dokumentumok, amelyek azonosító értéke nem sztring (null vagy bármely más adattípus érvénytelennek tekintendő).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Az Azure Cosmos-fiókban tárolt adatmennyiségek tömeges frissítése

A meglévő dokumentumokat a BulkUpdateAsync API használatával frissítheti. Ebben a példában a `Name` mezőt egy új értékre állítja be, és eltávolítja a `Description` mezőt a meglévő dokumentumokból. A támogatott frissítési műveletek teljes készletét az [API dokumentációjában](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate)találja.

1. Navigáljon a "BulkUpdateSample" mappára, és nyissa meg a "BulkUpdateSample. SLN" fájlt.  

2. A frissítési elemek meghatározása a megfelelő mező-frissítési műveletekkel együtt. Ebben a példában a használatával `SetUpdateOperation` frissíti a `Name` mezőt, és `UnsetUpdateOperation` eltávolítja a `Description` mezőt az összes dokumentumból. Más műveleteket is végrehajthat, például egy adott értékkel növelheti a dokumentum mező értékét, leküldheti a konkrét értékeket egy tömb mezőjébe, vagy eltávolíthat egy adott értéket egy tömb mezőből. Ha többet szeretne megtudni a tömeges frissítési API által nyújtott különböző módszerekről, tekintse meg az [API dokumentációját](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Az alkalmazás meghívja a BulkUpdateAsync API-t. Az BulkUpdateAsync metódus definíciójának megismeréséhez tekintse meg az [API dokumentációját](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **A BulkUpdateAsync metódus a következő paramétereket fogadja el:**

   |**Paraméter**  |**Leírás** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   A partíciós kulcs tartományának maximális párhuzamossági foka, ha ezt a paramétert NULL értékre állítja, a függvénytár az alapértelmezett értéket fogja használni (20).   |
   |maxInMemorySortingBatchSize    |    Az egyes szakaszok API-hívása által a frissítési elemek enumerálása által lekért frissítési elemek maximális száma. A tömeges frissítés előtti, memóriabeli rendezési fázisban a paraméter null értékre állításával a könyvtár az alapértelmezett minimális értéket fogja használni (updateItems. Count, 1000000).     |
   | cancellationToken|A lemondási token, amely szabályosan kilép a tömeges frissítési műveletből. |

   **Tömeges frissítés válasz objektumának definíciója** A tömeges frissítési API-hívás eredménye a következő attribútumokat tartalmazza:

   |**Paraméter**  |**Leírás** |
   |---------|---------|
   |NumberOfDocumentsUpdated (hosszú)    |   Azon dokumentumok száma, amelyek sikeresen frissítve lettek a tömeges frissítési API-híváshoz megadott összes dokumentumból.      |
   |TotalRequestUnitsConsumed (dupla)   |    A tömeges frissítési API-hívás által felhasznált összes kérési egység (RUs).    |
   |TotalTimeTaken (TimeSpan)   | A tömeges frissítési API-hívás által a végrehajtás befejezéséhez szükséges teljes idő. |
    
## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek 

A tömeges végrehajtó függvénytár használata esetén vegye figyelembe a következő szempontokat a jobb teljesítmény érdekében:

* A legjobb teljesítmény érdekében az alkalmazást egy olyan Azure-beli virtuális gépről futtassa, amely ugyanabban a régióban található, mint az Azure Cosmos-fiók írási régiója.  

* Azt javasoljuk, hogy egyetlen, egy `BulkExecutor` adott Azure Cosmos-tárolónak megfelelő virtuális gépen belül egyetlen objektumot hozza létre a teljes alkalmazáshoz.  

* Mivel egyetlen tömeges művelet API-végrehajtása nagy mennyiségű adatrészletet használ az ügyfélszámítógép processzor-és hálózati IO-jával (ez a több feladat belső elindításával történik). Ne indítson el egyszerre több egyidejű feladatot az alkalmazási folyamaton belül, amely tömeges műveleti API-hívásokat hajt végre. Ha egyetlen virtuális gépen futó egyetlen tömeges működésű API-hívás nem tudja felhasználni a teljes tároló átviteli sebességét (ha a tároló átviteli sebessége > 1 000 000 RU/s), akkor érdemes külön virtuális gépeket létrehozni a tömeges művelet API-hívások egyidejű végrehajtásához.  

* Győződjön `InitializeAsync()` meg arról, hogy a metódus meghívása egy BulkExecutor-objektum példányának beolvasását követően a cél Cosmos-tároló partíciós térképének beolvasása céljából  

* Az alkalmazás App.Config ellenőrizze, hogy a **gcServer** engedélyezve van-e a jobb teljesítmény érdekében
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A könyvtár olyan nyomkövetéseket bocsát ki, amelyek egy naplófájlba vagy a konzolon gyűjthetők össze. Mindkét lehetőség engedélyezéséhez adja hozzá a következő kódot az alkalmazás App.Config-fájljához.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Következő lépések

* A NuGet-csomag részleteivel és a kibocsátási megjegyzésekkel kapcsolatos további információkért tekintse meg a [tömeges végrehajtó SDK részleteit](sql-api-sdk-bulk-executor-dot-net.md).
