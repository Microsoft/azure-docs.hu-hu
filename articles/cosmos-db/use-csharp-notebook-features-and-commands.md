---
title: Beépített notebook-parancsok és-szolgáltatások használata Azure Cosmos DB C# jegyzetfüzetekben (előzetes verzió)
description: Ismerje meg, hogyan használhatók a beépített parancsok és szolgáltatások a Azure Cosmos DB beépített C# jegyzetfüzetekkel való gyakori működéséhez.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 67fe71d8e2d6ab239989cb30e9bf5a1b4d731037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340478"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Beépített notebook-parancsok és-szolgáltatások használata Azure Cosmos DB C# jegyzetfüzetekben (előzetes verzió)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB beépített Jupyter notebookok lehetővé teszik az adatok elemzését és megjelenítését a Azure Portal. Ez a cikk bemutatja, hogyan használhatók a beépített notebook-parancsok és-funkciók a gyakori műveletek végrehajtásához C#-jegyzetfüzetekben.

## <a name="install-a-new-nuget-package"></a>Új NuGet-csomag telepítése
Miután engedélyezte az Azure Cosmos-fiókok notebook-támogatását, megnyithat egy új jegyzetfüzetet, és telepítheti a csomagot.

Az új kód cellában szúrja be és futtassa a következő kódot, cserélje le a parancsot ``PackageToBeInstalled`` a kívánt NuGet-csomagra, és ``optionalVersion`` Ha szükséges, a csomag adott verziójára. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

Több NuGet-csomagot is telepíthet ugyanabba a cellába. A csomagok minden jegyzetfüzetből elérhetők lesznek az Azure Cosmos-fiók munkaterületen. 

A C# jegyzetfüzetek munkaterülete jelenleg nem támogatja a NuGet-csomagok rekurzív feloldását. Ha egy NuGet-csomag más, jelenleg nem telepített NuGet-csomagok függőségeivel rendelkezik, explicit módon hivatkoznia kell rájuk a szülő csomaggal együtt.

> [!TIP]
> Ha a notebookjának egyéni csomagra van szüksége, javasoljuk, hogy adjon hozzá egy cellát a jegyzetfüzethez a csomag telepítéséhez és az első cella létrehozásához. Ez csökkenti annak az esélyét, hogy a rendszer a többi olyan csomaggal ütközik, amely alapértelmezés szerint Azure Cosmos DB betöltődik. A csomagok újratelepítése is egyszerű, ha [alaphelyzetbe állítja a munkaterületet](#reset-notebooks-workspace), amely eltávolítja az összes csomagot. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>A beépített Azure Cosmos DB .NET SDK használata
A [Azure Cosmos db .net SDK for SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3) 3. verziója telepítve van, és tartalmazza az Azure Cosmos-fiók notebook-környezetében.

Hozzon létre egy példányt egy ``CosmosClient`` SDK-művelet futtatásához. 

Például:

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
További információ: [.net v3 SDK-minták](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage). 

> [!IMPORTANT]
> A beépített Azure Cosmos DB .NET SDK csak az SQL (Core) API-fiókok esetében támogatott. Más API-k esetén [telepítenie kell az](#install-a-new-nuget-package) API-nak megfelelő .net-illesztőprogramot. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>Egyéni beállítások megadása a használatával ``CosmosClientOptions``
A nagyobb rugalmasság érdekében beállíthatja az egyéni tulajdonságot, és átadhatja ``CosmosClientOptions`` azt a ``CosmosClient`` példányban. Ezt a tulajdonságot használhatja a következőhöz:

- A felhasználói ügynök utótagjában adja meg az alkalmazás nevét, hogy minden kérelemben szerepeljen.
- Állítsa be az előnyben részesített régiót, amelyet a rendszer a szolgáltatással kapcsolatos műveletek futtatásakor használ.
- Állítsa be az egyéni újrapróbálkozási szabályzatot a ráta-korlátozott kérelmek kezeléséhez.

Az összes támogatott beállításhoz tekintse meg a [COSMOSCLIENTOPTIONS API-referenciát](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) ismertető cikket. Az alábbi példa bemutatja, hogyan kell beállítani a `cosmosClientOptions` tulajdonságot:

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>Hozzáférés a fiók végpontja és az elsődleges kulcs változói
Elérheti az Azure Cosmos-fiók beépített végpontját és kulcsát, ahol a jegyzetfüzet található.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> A ``Cosmos.Key`` és a ``Cosmos.Endpoint`` változók csak az SQL API-ra alkalmazhatók. Más API-k esetén keresse meg a végpontot és a kulcsot az Azure Cosmos-fiókjának **kapcsolatok karakterláncok** vagy **kulcsok** paneljén.  

## <a name="print-console-output-in-c-code"></a>A konzol kimenetének nyomtatása C#-kódban
A C#-kódban használhatja a Display. AsMarkdown () szintaxist [karakterlánc-interpolációval](/dotnet/csharp/language-reference/tokens/interpolated) a konzol kimenetének nyomtatásához, amely a cella futtatásakor fog megjelenni. 

Például: 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> A Console. WriteLine () szintaxis jelenleg nem támogatott C# jegyzetfüzetekben. A Display. AsMarkdown használatával nyomtassa ki a konzol kimenetét a programból. 

## <a name="use-built-in-nteract-data-explorer"></a>Beépített nteract adatkezelő használata
A beépített [nteract adatkezelő](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) használatával szűrheti és megjelenítheti az elemek gyűjteményét. Egy cellában helyezze el az utolsó sorban megjeleníteni kívánt változót, amely automatikusan megjelenik a nteract a cella futtatásakor.

Például a *GetingStarted_Csharp. ipynb* példában kinyomtathatjuk a változót az eredményünk segítségével ``telemetryEvents`` . Tekintse meg a [GettingStarted_Csharp. ipynb jegyzetfüzetet](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb) a teljes mintában. 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Csharp-lekérdezési cella":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="nteract adatkezelő":::

## <a name="use-built-in-dictionary-viewer"></a>Beépített szótár-megjelenítő használata
A változó megjelenítéséhez használhatja a beépített szótár megjelenítőjét. Egy cellában helyezze el az utolsó sorban megjeleníteni kívánt változót, amely automatikusan megjelenik a cella futtatásakor.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Beépített szótár megjelenítője":::

## <a name="upload-json-items-to-a-container"></a>JSON-elemek feltöltése tárolóba
A ``%%upload`` Magic paranccsal adatok tölthetők fel egy JSON-fájlból egy megadott Azure Cosmos-tárolóba. Az elemek feltöltéséhez használja az alábbi parancsot:

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Cserélje le az ``{database_id}`` és az ``{container_id}`` nevet az Azure Cosmos-fiók adatbázisának és tárolójának nevére. 
- Cserélje le a helyére a ``{url_location_of_file}`` JSON-fájl helyét. A fájlnak érvényes JSON-objektumokból álló tömbnek kell lennie, és a nyilvános interneten keresztül elérhetőnek kell lennie.

Például:

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

A kimeneti statisztikával kiszámíthatja az elemek feltöltéséhez használt hatályos RU/s értékeit. Ha például az 25 000 RUs-t 38 másodpercen keresztül használták, a hatályos RU/s értéke 25 000 RUs/38 másodperc = 658 RU/s.

## <a name="run-another-notebook-in-current-notebook"></a>Másik jegyzetfüzet futtatása az aktuális jegyzetfüzetben 
A ``%%run`` Magic paranccsal egy másik jegyzetfüzetet futtathat a munkaterületen az aktuális jegyzetfüzetből. Használja a szintaxist:

```csharp
%%run ./path/to/{notebookName}.ipynb
```
Cserélje le a helyére a ``{notebookName}`` futtatni kívánt jegyzetfüzet nevét. A notebooknak a jelenlegi "saját jegyzetfüzetek" munkaterületen kell lennie. 

## <a name="reset-notebooks-workspace"></a>Jegyzetfüzetek alaphelyzetbe állítása munkaterület
Ha a jegyzetfüzetek munkaterületet az alapértelmezett beállításokra szeretné visszaállítani, válassza a parancssáv **munkaterület alaphelyzetbe** állítása lehetőséget. Ezzel eltávolítja az összes egyéni telepített csomagot, majd újraindítja a Jupyter-kiszolgálót. A jegyzetfüzeteket, a fájlokat és az Azure Cosmos-erőforrásokat nem érinti a rendszer.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Jegyzetfüzetek alaphelyzetbe állítása munkaterület":::

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [Azure Cosmos db Jupyter notebookok](cosmosdb-jupyter-notebooks.md) előnyeit
- További információ a [Azure Cosmos db .net SDK for SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3) -ról
