---
title: Azure.Search.Documents (v11) használata a .NET-ben
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan hozhat létre és kezelhet keresési objektumokat egy .NET-alkalmazásban a C# és a Azure.Search.Documents (v11) ügyféloldali kódtár használatával. A kódrészletek a szolgáltatáshoz való csatlakozást, indexek és lekérdezések létrehozását mutatják be.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 15a878eb863b71a4519e75def2598f013152dfb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881633"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>A Azure.Search.Documents használata C# .NET-alkalmazásokban

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet keresési objektumokat a C# és a [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) (11-es verzió) ügyféloldali kódtár használatával.

## <a name="about-version-11"></a>A 11-es verzió

A .NET-hez készült Azure SDK egy új [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) az Azure SDK-csapattól, amely a [Microsoft. Azure. Search](/dotnet/api/overview/azure/search/client10) ügyfél-kódtárakkal egyenértékű, de szükség esetén közös megközelítéseket és konvenciókat használ. Néhány példa [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) a kulcsos hitelesítésre, és [ aSystem.Text.Js. ](/dotnet/api/system.text.json.serialization) A JSON-szerializálás szerializálása.

A korábbi verziókhoz hasonlóan a következő függvénytárat használhatja:

+ Keresési indexek, adatforrások, indexelő, szakértelmével és szinonima térképek létrehozása és kezelése
+ Keresési dokumentumok betöltése és kezelése egy indexben
+ Lekérdezések végrehajtása anélkül, hogy a HTTP-és JSON-adatokkal kellene foglalkoznia

A kódtárat egyetlen [Azure.Search.DocUments NuGet-csomagként](https://www.nuget.org/packages/Azure.Search.Documents/)terjesztik, amely magában foglalja a keresési szolgáltatásokhoz való programozott hozzáféréshez használt összes API-t.

Az ügyféloldali függvénytár olyan osztályokat határoz meg, mint `SearchIndex` `SearchField` a, és, valamint `SearchDocument` a és a (z `SearchIndexClient.CreateIndex` ) és `SearchClient.Search` `SearchIndexClient` `SearchClient` osztályokhoz hasonló műveletek. Ezek az osztályok a következő névterekben vannak rendszerezve:

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (11-es verzió) [ `2020-06-30` az Azure Cognitive Search REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30)verzióját célozza meg. 

Az ügyféloldali kódtár nem biztosít a [szolgáltatás-felügyeleti műveleteket](/rest/api/searchmanagement/), például a keresési szolgáltatások létrehozását és méretezését, valamint az API-kulcsok kezelését. Ha .NET-alkalmazásból kell kezelnie a keresési erőforrásokat, használja a [Microsoft. Azure. Management. Search](/dotnet/api/overview/azure/search/management) függvénytárat a .net-hez készült Azure SDK-ban.

## <a name="upgrade-to-v11"></a>Frissítés a v11

Ha a .NET SDK korábbi verzióját használta, és a jelenlegi általánosan elérhető verzióra szeretné frissíteni, tekintse meg az [Azure Cognitive Search .net SDK 11-es verziójának frissítése](search-dotnet-sdk-migration-version-11.md) című témakört.

## <a name="sdk-requirements"></a>Az SDK követelményei

+ Visual Studio 2019 vagy újabb verzió.

+ Saját Azure Cognitive Search szolgáltatás. Az SDK használatához szüksége lesz a szolgáltatás nevére és egy vagy több API-kulcsra. Ha nem rendelkezik ilyennel [, hozzon létre egy szolgáltatást a portálon](search-create-service-portal.md) .

+ Töltse le a [Azure.Search.Documents csomagot](https://www.nuget.org/packages/Azure.Search.Documents) a **Tools**  >  **NuGet csomagkezelő** használatával a  >  Visual Studióban található **megoldás NuGet-csomagjainak kezeléséhez** . Keresse meg a csomag nevét `Azure.Search.Documents` .

A .NET-hez készült Azure SDK megfelel a .net [Standard 2,0](/dotnet/standard/net-standard#net-implementation-support)-nek, ami a .NET-keretrendszer 4.6.1-es és a .net Core 2,0-as minimális követelmény.

## <a name="example-application"></a>Példaalkalmazás

Ez a cikk "tanítja példaként", amely a GitHubon található [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) -programkódra támaszkodik, amely az Azure Cognitive Search alapvető fogalmait mutatja be – konkrétan a keresési indexek létrehozását, betöltését és lekérdezését.

A cikk további részében feltételezzük, hogy a "Hotels" nevű új index néhány dokumentummal van feltöltve, és több lekérdezés is megfelel az eredményeknek.

Alább látható a fő program, amely a teljes folyamatot mutatja:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

A következő egy részleges képernyőkép a kimenetről, feltéve, hogy az alkalmazást egy érvényes szolgáltatásnév és API-kulcs használatával futtatja:

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="A minta program Console. WriteLine kimenete":::

### <a name="client-types"></a>Ügyfelek típusai

Az ügyféloldali kódtár három különböző típusú ügyfél-típust használ: [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) indexek létrehozásához, frissítéséhez vagy törléséhez, indexek [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) betöltéséhez vagy lekérdezéséhez, valamint az [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) indexelő és a szakértelmével való együttműködéshez. Ez a cikk az első kettőre összpontosít. 

Az összes ügyfél számára legalább a szolgáltatás nevét vagy végpontját, valamint egy API-kulcsot kell megadni. Ezt az információt egy konfigurációs fájlban kell megadnia, hasonlóan ahhoz, amit a `appsettings.json` [DotNetHowTo minta alkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)fájljában talál. A konfigurációs fájlból való olvasáshoz adja hozzá a `using Microsoft.Extensions.Configuration;` programot a programhoz.

Az alábbi utasítás létrehozza az indexek létrehozásához, frissítéséhez vagy törléséhez használt index-ügyfelet. Keresési végpontot és rendszergazdai API-kulcsot vesz igénybe.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

A következő utasítás létrehozza a dokumentumok betöltéséhez vagy lekérdezések futtatásához használt keresési ügyfelet. `SearchClient` indexet igényel. A dokumentumok betöltéséhez rendszergazdai API-kulcsra van szükség, de lekérdezési API-kulccsal is elvégezheti a lekérdezések futtatását. 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> Ha érvénytelen kulcsot ad meg az importálási művelethez (például egy lekérdezési kulcs, amelynél rendszergazdai kulcs szükséges), akkor a `SearchClient` `CloudException` "tiltott" hibaüzenet jelenik meg, amikor először hívja meg a műveleti módszert. Ha ez történik, tekintse meg az API-kulcsot.
>

### <a name="deleting-the-index"></a>Az index törlése

A fejlesztés korai szakaszaiban érdemes lehet egy olyan utasítást is tartalmazni, amellyel [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) törölheti a folyamatban lévő indexeket, hogy újra létre lehessen hozni egy frissített definícióval. Az Azure-Cognitive Search mintakód gyakran tartalmaz egy törlési lépést, hogy újra futtatni tudja a mintát.

A következő sorokban megjelenő hívások `DeleteIndexIfExists` :

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

Ez a metódus a megadott `SearchIndexClient` értékkel vizsgálja meg, hogy az index létezik-e, és ha igen, törölje a következőt:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> A cikkben szereplő példa az egyszerűség kedvéért használja a szinkron metódusokat, de a saját alkalmazásaiban található aszinkron metódusok használatával méretezhető és rugalmasan tarthatók. Például a fenti metódusban használhatja a helyett a következőt: [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) .
>

## <a name="create-an-index"></a>Index létrehozása

[`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient)A használatával indexet hozhat létre. 

Az alábbi metódus egy új objektumot hoz létre az [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) új index sémáját meghatározó objektumok listájával. Minden mező rendelkezik egy névvel, adattípussal és számos olyan attribútummal, amely meghatározza a keresési viselkedését. 

A mezők meghatározhatók a modell osztályból a használatával [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) . Az `FieldBuilder` osztály reflexió használatával hozza létre `SearchField` az index objektumainak listáját az adott Model osztály nyilvános tulajdonságainak és attribútumainak vizsgálatával `Hotel` . Részletesebben is szemügyre vesszük az `Hotel` osztályt később.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

A mezőkön kívül pontozási profilokat, mutatókat vagy CORS lehetőségeket is hozzáadhat az indexhez (ezek a paraméterek kimaradnak a mintából a rövidség kedvéért). További információt a SearchIndex objektumról és annak összetevőiről a [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) Tulajdonságok listán, valamint a [REST API hivatkozásában](/rest/api/searchservice/)talál.

> [!NOTE]
> Az objektumok listáját mindig közvetlenül a használata helyett is létrehozhatja `Field` , `FieldBuilder` Ha szükséges. Előfordulhat például, hogy nem szeretné használni a modell osztályt, vagy egy meglévő modell osztályt kell használnia, amelyet attribútumok hozzáadásával nem kíván módosítani.
>

### <a name="call-createindex-in-main"></a>CreateIndex hívása a Mainban ()

`Main` létrehoz egy új "Hotels" indexet a fenti módszer meghívásával:

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>Modell osztály használata adatábrázoláshoz

A DotNetHowTo minta a [Hotel](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs), a [címe](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)és a [helyiség](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) adatstruktúrája modell osztályait használja. `Hotel` referenciák `Address` , egyetlen szintű összetett típus (többrészes mező) és `Room` (több részből álló mezők gyűjteménye).

Ezeket a típusokat használhatja az index létrehozásához és betöltéséhez, valamint a lekérdezés válaszának felépítéséhez:

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Egy másik módszer, hogy mezőket adjon hozzá egy indexhez közvetlenül. Az alábbi példa csupán néhány mezőt mutat be.

   ```csharp
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };
   ```

### <a name="field-definitions"></a>Mezők definíciói

A .NET-ben lévő adatmodellnek és a hozzá tartozó index sémának támogatnia kell a végfelhasználónak adni kívánt keresési élményt. A .NET-ben minden legfelső szintű objektum, például egy keresési indexben található keresési dokumentum egy keresési eredménynek felel meg, amelyet a felhasználói felületen fog megjelenni. Egy szállodai keresési alkalmazásban például előfordulhat, hogy a végfelhasználók a szállodai név, a szálloda funkciói vagy egy adott helyiség jellemzői alapján kívánják keresni a keresést. 

Az egyes osztályokba tartozó mezők egy adattípussal és attribútumokkal vannak definiálva, amelyek meghatározzák a használatuk módját. Az egyes osztályok minden egyes nyilvános tulajdonságának neve az index definíciójában azonos nevű mezőre van leképezve. 

Tekintse meg a következő kódrészletet, amely a szállodai osztály számos mező-definícióját lekéri. Figyelje meg, hogy a címek és a szobák a saját osztályok definíciója szerint C#-típusok (ha meg szeretné tekinteni őket), tekintse meg a minta kódját. Mindkettő összetett típus. További információkért lásd: [komplex típusok modellezése](search-howto-complex-data-types.md).

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>Field osztály kiválasztása

A mezők meghatározásakor használhatja az [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) alaposztályt, vagy használhat olyan származtató segítő modelleket, amelyek "sablonként" szolgálnak, előre konfigurált tulajdonságokkal.

Az index pontosan egy mezőjének a dokumentum kulcsának () kell lennie `IsKey = true` . Karakterláncnak kell lennie, és egyedi módon kell azonosítania az egyes dokumentumokat. Emellett az is szükséges `IsHidden = true` , ami azt jelenti, hogy a keresési eredmények között nem látható.

| Mező típusa | Leírás és használat |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | Alaposztály, a legtöbb tulajdonság NULL értékre van állítva, kivéve `Name` , ami kötelező, és `AnalyzerName` amely alapértelmezés szerint a standard Lucene. |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Segítő modell. Bármilyen adattípus lehet, mindig nem kereshető (figyelmen kívül hagyva a teljes szöveges keresési lekérdezéseknél), és lekérhető (nem rejtett). A többi attribútum alapértelmezés szerint ki van kapcsolva, de engedélyezve lehet. Használhat olyan `SimpleField` dokumentum-azonosítókat vagy mezőket, amelyek csak szűrők, dimenziók vagy pontozási profilok esetében használhatók. Ha igen, ügyeljen arra, hogy a forgatókönyvhöz szükséges attribútumokat (például `IsKey = true` egy dokumentum azonosítóját) alkalmazza. További információ: [SimpleFieldAttribute. cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) a forráskódban. |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Segítő modell. Karakterláncnak kell lennie, és mindig kereshető és lekérhető. A többi attribútum alapértelmezés szerint ki van kapcsolva, de engedélyezve lehet. Mivel ez a mezőtípus kereshető, támogatja a szinonimákat és az analizátor összes tulajdonságának teljes kiegészítését. További információ: [SearchableFieldAttribute. cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) a forráskódban. |

Függetlenül attól, hogy az alapszintű `SearchField` API-t vagy a segítő modellek valamelyikét használja-e, explicit módon engedélyeznie kell a szűrő, a dimenzió és a rendezési attribútumot. Például a [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), a [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)és a [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) explicit módon kell szerepelnie a fenti mintában látható módon.

#### <a name="adding-field-attributes"></a>Mező attribútumainak hozzáadása

Figyelje meg, hogy az egyes mezők az attribútumokkal (például `IsFilterable` ,, `IsSortable` `IsKey` és) vannak díszítve `AnalyzerName` . Ezek az attribútumok közvetlenül [egy Azure Cognitive Search index megfelelő mező attribútumaira](/rest/api/searchservice/create-index)mutatnak. Az `FieldBuilder` osztály ezeket a tulajdonságokat használja az indexhez tartozó mező-definíciók összeállításához.

#### <a name="field-type-mapping"></a>Mezőtípus-leképezés

A tulajdonságok .NET-típusai az index definíciójában a megfelelő mezőtípus értékre vannak leképezve. Például a rendszer a `Edm.String` típusú `Category` szöveges tulajdonságot a `category` mezőbe képezi le. Hasonló típusú leképezések találhatók a, `bool?` , `Edm.Boolean` `DateTimeOffset?` és `Edm.DateTimeOffset` stb. között. 

Észrevette a `SmokingAllowed` tulajdonságot?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

A `JsonIgnore` tulajdonság attribútuma azt jelzi `FieldBuilder` , hogy a nem szerializálja az indexet mezőként.  Ez nagyszerű módja annak, hogy olyan ügyféloldali számított tulajdonságokat hozzon létre, amelyeket segítőként használhat az alkalmazásban.  Ebben az esetben a `SmokingAllowed` tulajdonság azt jelzi, hogy `Room` a gyűjteményben lévő bármelyik a `Rooms` dohányzást teszi lehetővé. Ha az összes hamis, az azt jelzi, hogy a teljes szálloda nem teszi lehetővé a dohányzást.

## <a name="load-an-index"></a>Index betöltése

A következő lépésben `Main` feltölti az újonnan létrehozott "Hotels" indexet. Az index populációja a következő módon történik: (néhány kód helyébe "..." illusztrációs célokra. Tekintse meg a teljes adatpopulációs kód teljes minta megoldását.)

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                HotelName = "Secret Point Motel",
                ...
                Address = new Address()
                {
                    StreetAddress = "677 5th Ave",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Budget Room, 1 Queen Bed (Cityside)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (City View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                HotelName = "Twin Dome Motel",
                ...
                {
                    StreetAddress = "140 University Town Center Dr",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Suite, 2 Double Beds (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Standard Room, 1 Queen Bed (City View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Waterfront View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "3",
                HotelName = "Triple Landscape Hotel",
                ...
                Address = new Address()
                {
                    StreetAddress = "3393 Peachtree Rd",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Standard Room, 2 Queen Beds (Amenities)",
                        ...
                    },
                    new Room ()
                    {
                        Description = "Standard Room, 2 Double Beds (Waterfront View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (Cityside)",
                        ...
                    }
                }
            }
        };

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

Ez a metódus négy részből áll. Az első három objektumból álló tömböt hoz létre, `Hotel` `Room` amely az indexbe feltöltendő bemeneti adatokként szolgál majd. Ezek az adathalmazok az egyszerűség kedvéért nehezen kódoltak. Egy tényleges alkalmazásban az adatok valószínűleg egy külső adatforrásból származnak, például egy SQL-adatbázisból.

A második rész létrehozza [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) a dokumentumokat tartalmazó dokumentumot. Megadhatja a kötegre alkalmazni kívánt műveletet a létrehozáskor, ebben az esetben a hívásával [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) . Ezután a rendszer feltölti a köteget az Azure Cognitive Search indexbe a [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) metódussal.

> [!NOTE]
> Ebben a példában csak dokumentumokat töltünk fel. Ha szeretné egyesíteni a változtatásokat a meglévő dokumentumokban, vagy törölhet dokumentumokat, létrehozhat kötegeket a meghívásával, `IndexDocumentsAction.Merge` `IndexDocumentsAction.MergeOrUpload` vagy `IndexDocumentsAction.Delete` ehelyett. Egy kötegben különböző műveleteket is összekeverheti úgy, hogy meghívja a (z `IndexBatch.New` ) függvényt, amely az objektumok egy gyűjteményét kéri, `IndexDocumentsAction` amelyek mindegyike megadja az Azure Cognitive Search egy adott művelet végrehajtására a dokumentumon. Létrehozhatja a `IndexDocumentsAction` saját műveleteit úgy, hogy meghívja a megfelelő metódust, például `IndexDocumentsAction.Merge` , `IndexAction.Upload` stb.
> 

A metódus harmadik része egy olyan Catch blokk, amely egy fontos, az indexeléshez szükséges hibát kezel. Ha a keresési szolgáltatás nem tud indexelni néhány dokumentumot a kötegben, az a következő `IndexBatchException` lesz: `IndexDocuments` . Ez a kivétel akkor fordulhat elő, ha a dokumentumok indexelése során a szolgáltatás nagy terhelés alatt áll. **Javasoljuk ennek az esetnek az explicit módon való kezelését a kódban.** Azon dokumentumok esetében, ahol az indexelés meghiúsult, elhalaszthatja azt, majd később újra megpróbálkozhat az indexeléssel, vagy a mintának megfelelően naplózhatja azt, és folytathatja a munkáját, esetleg – az alkalmazás adatkonzisztencia-követelményeitől függően – más műveletbe kezdhet.

Végül a `UploadDocuments` metódus két másodpercig késlelteti. Az indexelés aszinkron módon történik a keresési szolgáltatásban, így a minta alkalmazásnak egy rövid időre várnia kell, hogy a dokumentumok kereshetőek legyenek. Ilyen mértékű késleltetésre kizárólag demók, tesztek és mintaalkalmazások esetében van szükség.

### <a name="call-uploaddocuments-in-main"></a>UploadDocuments hívása a Mainban ()

A következő kódrészlet a [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) indexClient metódusának használatával állít be egy példányt [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) . A indexClient egy rendszergazdai API-kulcsot használ a kérelmén, amely a dokumentumok betöltéséhez vagy frissítéséhez szükséges.

Egy másik módszer a közvetlen hívása `SearchClient` , amely a rendszergazdai API-kulcson keresztül történik `AzureKeyCredential` .

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>Lekérdezések futtatása

Először állítson be egy olyant, `SearchClient` amely beolvassa a keresési végpontot és a lekérdezési API-kulcsot **appsettings.json**:

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

Másodszor, adjon meg egy metódust, amely egy lekérdezési kérelmet küld. 

Minden alkalommal, amikor a metódus végrehajt egy lekérdezést, egy új objektumot hoz létre [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) . Ez az objektum a lekérdezés további beállításainak megadására szolgál, például rendezés, szűrés, lapozás és aspektusok. Ebben a metódusban a, a `Filter` és a `Select` `OrderBy` tulajdonságot a különböző lekérdezésekhez állítja be. A keresési lekérdezés kifejezésének szintaxisáról az [egyszerű lekérdezés szintaxisa](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)című témakörben olvashat bővebben.

A következő lépés a keresési lekérdezés tényleges végrehajtása. A keresés futtatása a metódus használatával történik `SearchClient.Search` . Minden lekérdezés esetében adja át a keresett szöveget karakterláncként (vagy `"*"` Ha nincs keresési szöveg), valamint a korábban létrehozott keresési beállításokat. `Hotel`A (z) Type paraméterként is megadható `SearchClient.Search` , amely azt jelzi, hogy az SDK a keresési eredményekben lévő dokumentumokat deszerializálja a típusú objektumokra `Hotel` .

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

Harmadszor, Definiáljon egy metódust, amely beírja a választ, és kinyomtatja az egyes dokumentumokat a konzolra:

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>RunQueries hívása a Mainban ()

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>A lekérdezés-összeállítások megismerése

Ismerkedjen meg közelebbről a lekérdezésekkel. Az első lekérdezés végrehajtásához az alábbi kód szükséges:

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

Ebben az esetben a "Motel" szó teljes indexét keresjük bármilyen kereshető mezőben, és a beállításban meghatározottak szerint csak a szállodai neveket szeretnénk beolvasni `Select` . Az eredmények a következők:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

A második lekérdezésben egy szűrő használatával válassza ki a termet, amelynek a hossza kevesebb, mint $100. Csak a szállodai azonosító és a Leírás visszaadása az eredményekben:

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

A fenti lekérdezés egy OData `$filter` kifejezést használ az `Rooms/any(r: r/BaseRate lt 100)` indexben lévő dokumentumok szűréséhez. Ez a [bármely operátort](./search-query-odata-collection-operators.md) használja a "BaseRate lt 100" a szobák gyűjtemény minden elemébe való alkalmazásához. További információ: [OData szűrő szintaxisa](./query-odata-filter-orderby-syntax.md).

A harmadik lekérdezésben keresse meg a legutóbb felújított legfelső szintű szállodát, és jelenítse meg a szálloda nevét és utolsó felújítási dátumát. A kód itt látható: 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

Az utolsó lekérdezésben keresse meg az összes olyan nevet, amely megfelel a "Hotel" szónak:

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

Ez a szakasz a .NET SDK bevezetését vonja le, de itt nem áll le. A következő szakasz további forrásokat javasol az Azure Cognitive Search programozásával kapcsolatos további információk megismeréséhez.

## <a name="next-steps"></a>Következő lépések

+ Az [Azure.Search.Documents](/dotnet/api/azure.search.documents) és [REST API](/rest/api/searchservice/) API-referenciájának dokumentációja

+ További példák a Azure.Search.Documents az [Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) és a [Search-első lépések-DotNet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ Tekintse át az [elnevezési konvenciókat](/rest/api/searchservice/Naming-rules) a különböző objektumok elnevezési szabályainak megismeréséhez

+ [Támogatott adattípusok](/rest/api/searchservice/Supported-data-types) áttekintése
