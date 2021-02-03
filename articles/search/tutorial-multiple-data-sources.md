---
title: C#-oktatóanyag több Azure-adatforrás indexeléséhez
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan importálhat több adatforrásból származó adatait egyetlen Azure Cognitive Search indexbe az indexelő használatával. Ez az oktatóanyag és mintakód a C# nyelven van.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: df7dcf9859b6942662ae447af836f59985e2d11a
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509503"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Oktatóanyag: több adatforrásból származó index a .NET SDK használatával

Az Azure Cognitive Search több adatforrás adatait is importálhatja, elemezheti és indexelheti egyetlen konszolidált keresési indexbe. 

Ez az oktatóanyag a C# nyelvet és a .NET-hez készült Azure SDK-ban lévő [Azure.Search.Documents](/dotnet/api/overview/azure/search) -t használja a Sample Hotel-adatok egy Azure Cosmos DBból való indexeléséhez, és az azure-blob Storage dokumentumokból kirajzolt szállodai szobák adatainak egyesítéséhez. Az eredmény egy szállodai dokumentumokat tartalmazó kombinált szállodai keresési index lesz, amely összetett adattípusú szobákkal rendelkezik.

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Mintaadatok feltöltése és adatforrások létrehozása
> * A dokumentum kulcsának azonosítása
> * Az index meghatározása és létrehozása
> * Szállodai adatok indexelése Azure Cosmos DBból
> * Szállodai helyiség adatainak egyesítése a blob Storage-ból

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag az új ügyféloldali kódtárat, [Azure.Search.Documents](/dotnet/api/overview/azure/search), 11. x verziót használja több indexelő létrehozásához és futtatásához. Ebben az oktatóanyagban két Azure-adatforrást állít be, így konfigurálhat egy indexelő, amely egyszerre egy keresési index feltöltésére szolgál. A két adathalmaznak közös értékkel kell rendelkeznie az egyesítés támogatásához. Ebben a példában ez a mező egy azonosító. Ha a leképezés támogatásához közösen van egy mező, az indexelő egyesítheti a különböző erőforrások adatait: az Azure SQL-ből származó strukturált adatok, a blob Storage-ból strukturálatlan adatok vagy az Azure [által támogatott adatforrások](search-indexer-overview.md#supported-data-sources) bármely kombinációja.

Az oktatóanyagban szereplő kód befejezett verziója a következő projektben található:

* [több adatforrások/v11 (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v11)

Ez az oktatóanyag a Azure.Search.Documents (11-es verzió) csomag használatára lett frissítve. A .NET SDK egy korábbi verziójával kapcsolatban lásd: [Microsoft. Azure. Search (10-es verzió)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v10) mintakód a githubon.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Visual Studio](https://visualstudio.microsoft.com/)
+ [Azure Cognitive Search (11. x verzió) NuGet csomag](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Meglévő keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást. Az ingyenes keresési szolgáltatás három indexre, három indexelő elemre és három adatforrásra korlátozza a szolgáltatást. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a szolgáltatásban az új erőforrások elfogadására szolgáló helyiséggel.

## <a name="1---create-services"></a>1 – szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Searcht használja az indexeléshez és lekérdezésekhez, Azure Cosmos DB egy adatkészlethez, valamint az Azure Blob Storage-hoz a második adatkészlethez. 

Ha lehetséges, a közelség és kezelhetőség érdekében hozzon létre minden szolgáltatást ugyanabban a régióban és erőforráscsoporthoz. A gyakorlatban a szolgáltatásai bármely régióban lehetnek.

Ez a példa két kisebb adathalmazt használ, amelyek a hét kitalált szállodát írják le. Egy készlet maga írja le a szállodákat, és betöltődik egy Azure Cosmos DB adatbázisba. A másik készlet tartalmazza a szállodai szobák részleteit, és hét különálló JSON-fájlt biztosít az Azure Blob Storageba való feltöltéshez.

### <a name="start-with-cosmos-db"></a>Kezdés Cosmos DB

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd navigáljon a Azure Cosmos db-fiók áttekintő oldalára.

1. Válassza a **adatkezelő** lehetőséget, majd válassza az **új adatbázis** lehetőséget.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Új adatbázis létrehozása" border="false":::

1. Adja meg a **Hotel-Rooms-db** nevet. Fogadja el az alapértelmezett értékeket a többi beállításhoz.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Adatbázis konfigurálása" border="false":::

1. Hozzon létre egy új tárolót. Használja az imént létrehozott meglévő adatbázist. Adja **meg a helyet a tároló** neveként, és használja a **/HotelId** a partíciós kulcshoz.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Tároló hozzáadása" border="false":::

1. Válassza a **hotelek** területen található **elemek** elemet, majd kattintson az **elem feltöltése** gombra a parancssorban. Keresse meg, majd válassza ki a **cosmosdb/HotelsDataSubset_CosmosDb.js** fájlt a Project mappában.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Feltöltés Azure Cosmos DB gyűjteménybe" border="false":::

1. A refresh (frissítés) gombbal frissítheti a Hotels gyűjteményben lévő elemek nézetét. A felsorolt hét új adatbázis-dokumentumnak kell megjelennie.

1. A **kulcsok** lapról másolja a Jegyzettömbbe a kapcsolatok karakterláncát. Egy későbbi lépésben **appsettings.jsra** lesz szüksége. Ha nem használta a "Hotel-Rooms-db" nevű javasolt adatbázisnevet, másolja az adatbázis nevét is.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), navigáljon az Azure Storage-fiókjához, kattintson a **Blobok** elemre, majd a **+ tároló** elemre.

1. [Hozzon létre egy](../storage/blobs/storage-quickstart-blobs-portal.md) " **Hotel-Rooms** " nevű BLOB-tárolót a minta szállodai szoba JSON-fájljainak tárolásához. Megadhatja a nyilvános hozzáférési szintet bármelyik érvényes értékéhez.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Blobtároló létrehozása" border="false":::

1. A tároló létrehozása után nyissa meg, majd válassza a parancssáv **feltöltés** elemét. Navigáljon a minta fájlokat tartalmazó mappához. Jelölje ki az összeset, majd kattintson a **feltöltés** elemre.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Fájlok feltöltése" border="false":::

1. Másolja a Storage-fiók nevét és egy kapcsolati karakterláncot a **hozzáférési kulcsok** lapról a Jegyzettömbbe. Egy későbbi lépésben aappsettings.jsértékeit is **meg** kell adni.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A harmadik összetevő az Azure Cognitive Search, amelyet [a portálon lehet létrehozni](search-create-service-portal.md). 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>A felügyeleti API-kulcs és az Azure-beli URL-cím másolása Cognitive Search

A keresési szolgáltatás hitelesítéséhez szüksége lesz a szolgáltatás URL-címére és egy hozzáférési kulcsra.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése" border="false":::

Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="2---set-up-your-environment"></a>2 – a környezet beállítása

1. Indítsa el a Visual studiót, és az **eszközök** menüben válassza a **NuGet csomagkezelő** elemet, majd a **megoldáshoz tartozó NuGet-csomagokat..**. lehetőséget. 

1. A **Tallózás** lapon keresse meg és telepítse **Azure.Search.Documents** (11,0-es vagy újabb verzió). A telepítés befejezéséhez kattintson a további párbeszédablakok lehetőségre.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Azure-kódtárak hozzáadása a NuGet használatával" border="false":::

1. Keressen rá a **Microsoft.Extensions.Configszülő** , és **Microsoft.Extensions.Configuration.Jsa** NuGet-csomagokra, és telepítse azokat is.

1. Nyissa meg a megoldás fájljának **/v11/AzureSearchMultipleDataSources.SLN**.

1. A Megoldáskezelőban szerkessze a fájl **appsettings.jsját** a kapcsolódási adatok hozzáadásához.  

    ```json
    {
      "SearchServiceUri": "<YourSearchServiceURL>",
      "SearchServiceAdminApiKey": "<YourSearchServiceAdminApiKey>",
      "BlobStorageAccountName": "<YourBlobStorageAccountName>",
      "BlobStorageConnectionString": "<YourBlobStorageConnectionString>",
      "CosmosDBConnectionString": "<YourCosmosDBConnectionString>",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

Az első két bejegyzés a keresési szolgáltatás URL-címe és rendszergazdai kulcsa. Használja a teljes végpontot, például: `https://mydemo.search.windows.net` .

A következő bejegyzések megadják az Azure Blob Storage és Azure Cosmos DB adatforrások fiókjának nevét és a kapcsolatok karakterláncának adatait.

## <a name="3---map-key-fields"></a>3 – a kulcs mezőinek megjelenítése

A tartalom egyesítéséhez az szükséges, hogy mindkét adatfolyam ugyanazt a dokumentumot célozza meg a keresési indexben. 

Az Azure Cognitive Searchban a Key mező egyedileg azonosítja az egyes dokumentumokat. Minden keresési indexnek pontosan egy típusú Key mezővel kell rendelkeznie `Edm.String` . A kulcs mezőnek jelen kell lennie az indexhez hozzáadott adatforrásban lévő minden dokumentumhoz. (Valójában ez az egyetlen kötelező mező.)

Ha több adatforrásból indexeli az adatait, győződjön meg arról, hogy az egyes bejövő sorok vagy dokumentumok közös dokumentum-kulcsot tartalmaznak, hogy a két fizikailag különböző forrásból származó adatok egyesítése egy új keresési dokumentumba történjen a kombinált indexben. 

Gyakran igényel némi kezdeti megtervezést az index értelmes dokumentum-kulcsainak azonosításához, és győződjön meg arról, hogy mindkét adatforrásban létezik. Ebben a bemutatóban a `HotelId` Cosmos db minden egyes szállodájának kulcsa a blob Storage-ban található szobák JSON-blobjában is megtalálható.

Az Azure Cognitive Search indexelő mezőivel átnevezheti és akár újraformázhatja az adatmezőket az indexelési folyamat során, így a forrásadatok a megfelelő index mezőre irányíthatók. Cosmos DB például a rendszer a Hotel azonosítóját hívja meg **`HotelId`** . A szállodai szobákhoz tartozó JSON blob-fájlokban azonban a szállodai azonosító neve **`Id`** . A program ezt a mezőt úgy kezeli, **`Id`** hogy a Blobok mezőjét az **`HotelId`** Indexelő kulcs mezőjébe rendeli.

> [!NOTE]
> A legtöbb esetben az automatikusan generált dokumentum-kulcsok, például az egyes indexelő által alapértelmezés szerint létrehozott, nem végeznek jó dokumentum-kulcsokat a kombinált indexekhez. Általánosságban olyan értelmes, egyedi kulcsot szeretne használni, amely már létezik a-ben, vagy egyszerűen hozzáadható az adatforrásokhoz.

## <a name="4---explore-the-code"></a>4 – a kód megismerése

Az adatés konfigurációs beállítások megadását követően a **/v11/AzureSearchMultipleDataSources.SLN** lévő minta programnak készen kell állnia a létrehozásra és a futtatásra.

Ez az egyszerű C# kódon-konzol alkalmazás a következő feladatokat hajtja végre:

* Létrehoz egy új indexet a C# Hotel osztály adatstruktúrája alapján (amely a címekre és a Room osztályokra is hivatkozik).
* Létrehoz egy új adatforrást és egy indexelő, amely leképezi az Azure Cosmos db az adat index mezőibe. Ezek mind az Azure Cognitive Searchban található objektumok.
* Futtatja az indexelő a szállodai adatok Cosmos DBból való betöltéséhez.
* Létrehoz egy második adatforrást és egy indexelő, amely a JSON-Blobok adatait indexelő mezőkbe képezi.
* A második indexelő futtatásával tölti be a helyiségek adatait a blob Storage-ból.

 A program futtatása előtt szánjon egy percet a minta kódjának és indexének és indexelő definícióinak tanulmányozására. A megfelelő kód a következő két fájlban található meg:

  + A **Hotel.cs** tartalmazza az indexet meghatározó sémát.
  + A **program.cs** olyan függvényeket tartalmaz, amelyek létrehozzák az Azure Cognitive Search indexet, az adatforrásokat és az indexelő, és betöltik az összesített eredményeket az indexbe.

### <a name="create-an-index"></a>Index létrehozása

Ez a [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) egy Azure Cognitive Search index definiálásához és létrehozásához használja a következőt:. Kihasználja a [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) osztályt, hogy a C# adatmodell osztályból létrehozzon egy index-struktúrát.

Az adatmodellt a Hotel osztály határozza meg, amely a címekre és a helyiségekre vonatkozó osztályokra mutató hivatkozásokat is tartalmaz. A FieldBuilder részletesen részletezi az indexek összetett adatstruktúrájának létrehozásához. A metaadatok címkéi az egyes mezők attribútumainak meghatározására szolgálnak, például hogy kereshető vagy rendezhető legyen.

A program az új létrehozása előtt törli az azonos nevű meglévő indexet, abban az esetben, ha egynél többször szeretné futtatni ezt a példát.

A **Hotel.cs** fájl következő kódrészletei egyetlen mezőt jelenítenek meg, majd egy másik adatmodell-osztályra mutató hivatkozást, a Room [] elemet, amely viszont a **Room.cs** fájlban van definiálva (nem látható).

```csharp
. . .
[SimpleField(IsFilterable = true, IsKey = true)]
public string HotelId { get; set; }

[SearchableField(IsFilterable = true, IsSortable = true)]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

A **program.cs** fájlban a [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) egy névvel és egy, a metódus által generált mező-gyűjteményrel van definiálva, `FieldBuilder.Build` majd a következőképpen jön létre:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Azure Cosmos DB adatforrás és indexelő létrehozása

A főprogram következő része a Azure Cosmos DB adatforrás létrehozásához szükséges logikát tartalmazza.

Először összefűzi a Azure Cosmos DB adatbázis nevét a kapcsolódási karakterlánchoz. Ezután definiál egy [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) objektumot.

```csharp
private static async Task CreateAndRunCosmosDbIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    // Append the database name to the connection string
    string cosmosConnectString =
        configuration["CosmosDBConnectionString"]
        + ";Database="
        + configuration["CosmosDBDatabaseName"];

    SearchIndexerDataSourceConnection cosmosDbDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["CosmosDBDatabaseName"],
        type: SearchIndexerDataSourceType.CosmosDb,
        connectionString: cosmosConnectString,
        container: new SearchIndexerDataContainer("hotels"));

    // The Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(cosmosDbDataSource);
```

Az adatforrás létrehozása után a program beállítja a **Hotel-Rooms-Cosmos-indexelő** nevű Azure Cosmos db indexelő.

A program frissíti az azonos nevű meglévő indexelő adatokat, és felülírja a meglévő indexelő a fenti kód tartalmával. Az alaphelyzetbe állítási és futtatási műveleteket is magában foglalja abban az esetben, ha egynél többször szeretné futtatni ezt a példát.

Az alábbi példa az indexelő ütemtervét határozza meg, hogy naponta egyszer fusson. Ha nem szeretné, hogy az indexelő automatikusan újrafusson a jövőben, távolítsa el az ütemezett tulajdonságot ebből a hívásból.

```csharp
SearchIndexer cosmosDbIndexer = new SearchIndexer(
    name: "hotel-rooms-cosmos-indexer",
    dataSourceName: cosmosDbDataSource.Name,
    targetIndexName: indexName)
{
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

// Indexers keep metadata about how much they have already indexed.
// If we already ran the indexer, it "remembers" and does not run again.
// To avoid this, reset the indexer if it exists.
try
{
    await indexerClient.GetIndexerAsync(cosmosDbIndexer.Name);
    // Reset the indexer if it exists.
    await indexerClient.ResetIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404)
{
    // If the indexer does not exist, 404 will be thrown.
}

await indexerClient.CreateOrUpdateIndexerAsync(cosmosDbIndexer);

Console.WriteLine("Running Cosmos DB indexer...\n");

try
{
    // Run the indexer.
    await indexerClient.RunIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 429)
{
    Console.WriteLine("Failed to run indexer: {0}", ex.Message);
}
```

Ez a példa egy egyszerű try-catch blokkot tartalmaz a végrehajtás során esetlegesen előforduló hibák jelentéséhez.

A Azure Cosmos DB indexelő futtatását követően a keresési index tartalmazni fogja a Sample Hotel-dokumentumok teljes készletét. Az egyes szállodákhoz tartozó szobák mező azonban üres tömb lesz, mivel a Azure Cosmos DB adatforrás kihagyja a helyiség részleteit. Ezután a program lekéri a blob Storage-ból a helyiség adatainak betöltését és egyesítését.

### <a name="create-blob-storage-data-source-and-indexer"></a>BLOB Storage adatforrás és indexelő létrehozása

A helyiség részleteinek beszerzéséhez a program először egy blob Storage-adatforrást állít be, amely az egyes JSON blob-fájlok készletére hivatkozik.

```csharp
private static async Task CreateAndRunBlobIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    SearchIndexerDataSourceConnection blobDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["BlobStorageAccountName"],
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["BlobStorageConnectionString"],
        container: new SearchIndexerDataContainer("hotel-rooms"));

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(blobDataSource);
```

Az adatforrás létrehozása után a program egy **Hotel-Rooms-blob-indexelő** nevű blob-indexet állít be az alább látható módon.

A JSON-Blobok egy nevű kulcs mezőt tartalmaznak a **`Id`** helyett **`HotelId`** . A kód a osztály használatával közli `FieldMapping` az indexelő, hogy a **`Id`** mező értékét az **`HotelId`** indexben lévő dokumentum kulcsára irányítsa.

A blob Storage-indexelő a [IndexingParameters](/dotnet/api/azure.search.documents.indexes.models.indexingparameters) használatával határozzák meg az elemzési módot. Különböző elemzési módokat kell beállítania attól függően, hogy a Blobok egyetlen dokumentumot vagy több, ugyanazon a blobon belüli dokumentumot képviselnek-e. Ebben a példában minden blob egyetlen JSON-dokumentumot képvisel, ezért a kód az `json` elemzési módot használja. További információ a JSON-Blobok indexelő paramétereinek elemzéséről: [JSON-Blobok indexelése](search-howto-index-json-blobs.md).

Ez a példa az indexelő ütemtervét határozza meg, hogy naponta egyszer fusson. Ha nem szeretné, hogy az indexelő automatikusan újrafusson a jövőben, távolítsa el az ütemezett tulajdonságot ebből a hívásból.

```csharp
// Map the Id field in the Room documents to the HotelId key field in the index
List<FieldMapping> map = new List<FieldMapping> {
    new FieldMapping("Id")
    {
        TargetFieldName =  "HotelId"
    }
};

IndexingParameters parameters = new IndexingParameters();
parameters.Configuration.Add("parsingMode", "json");

SearchIndexer blobIndexer = new SearchIndexer(
    name: "hotel-rooms-blob-indexer",
    dataSourceName: blobDataSource.Name,
    targetIndexName: indexName)
{
    Parameters = parameters,
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

blobIndexer.FieldMappings.Add(new FieldMapping("Id") { TargetFieldName = "HotelId" });

// Reset the indexer if it already exists
try
{
    await indexerClient.GetIndexerAsync(blobIndexer.Name);
    await indexerClient.ResetIndexerAsync(blobIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404) { }

await indexerClient.CreateOrUpdateIndexerAsync(blobIndexer);

try
{
    // Run the indexer.
    await searchService.Indexers.RunAsync(blobIndexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
    Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

Mivel az index már fel van töltve a Azure Cosmos DB adatbázisból származó adatokkal, a blob indexelő frissíti a meglévő dokumentumokat az indexben, és hozzáadja a helyiség részleteit.

> [!NOTE]
> Ha ugyanazokat a nem kulccsal rendelkező mezőket tartalmazza mindkét adatforrásban, és az ezekben a mezőkben lévő adatok nem egyeznek, akkor az index a legutóbb futtatott indexelő értékeit fogja tartalmazni. A példánkban mindkét adatforrás tartalmaz egy **pezsgő** mezőt. Ha valamilyen okból kifolyólag az ebben a mezőben lévő adatok eltérnek, az azonos kulccsal rendelkező dokumentumok esetében a **pezsgő** az indexben tárolt érték lesz az aktuálisan indexelt adatforrás.

## <a name="5---search"></a>5 – keresés

A program futtatása után megtekintheti a feltöltött keresési indexet a portálon található [**keresési ablak**](search-explorer.md) használatával.

A Azure Portalban nyissa meg a keresési szolgáltatás **áttekintése** lapot, és keresse meg a **Hotel-Rooms-Sample** indexet az **indexek** listájában.

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Az Azure Cognitive Search indexek listája" border="false":::

Kattintson a Hotel-Rooms-Sample index elemre a listában. Ekkor megjelenik az indexhez tartozó keresési Explorer felülete. Adjon meg egy lekérdezést egy olyan kifejezéshez, mint a "Luxury". Meg kell jelennie legalább egy dokumentumnak az eredményekben, és a dokumentumnak tartalmaznia kell a Room Objects-objektumok listáját a szobák tömbben.

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszaiban a tervezési iteráció legalkalmasabb megközelítése az objektumok törlése az Azure Cognitive Search és a kód újraépítésének engedélyezése. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

A mintakód ellenőrzi a meglévő objektumokat, és törli vagy frissíti őket, hogy újra tudja futtatni a programot.

A portál segítségével indexeket, indexelő fájlokat és adatforrásokat is törölhet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, a projekt végén érdemes lehet eltávolítani a már nem szükséges erőforrásokat. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a minden erőforrás vagy erőforráscsoport hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a különböző forrásokból származó adatok betöltésének koncepcióját, ismerkedjen meg közelebbről az indexelő konfigurálásával, amely a Cosmos DBtól kezdődik.

> [!div class="nextstepaction"]
> [Azure Cosmos DB indexelő konfigurálása](search-howto-index-cosmosdb.md)
