---
title: 'Gyors útmutató: keresési index létrehozása a .NET-ben'
titleSuffix: Azure Cognitive Search
description: Ebből a C# rövid útmutatóból megtudhatja, hogyan hozhat létre indexet, tölthet be és hogyan futtathat lekérdezéseket a Azure.Search.Documents ügyféloldali kódtár használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f0d912d5b14932c43d109f8f955d5f16381cf773
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98180098"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Gyors útmutató: keresési index létrehozása a Azure.Search.Documents ügyféloldali kódtár használatával

Az új [Azure.Search.Documents (11-es verzió) ügyféloldali kódtár](/dotnet/api/overview/azure/search.documents-readme) használatával létrehozhat egy .net Core Console-alkalmazást a C#-ban, amely egy keresési indexet hoz létre, tölt be és kérdez le.

[Töltse le a forráskódot](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) , hogy a kész projekttel kezdődjön, vagy kövesse a cikk lépéseit saját létrehozásához.

> [!NOTE]
> Korábbi verziót keres? Lásd: [keresési index létrehozása a Microsoft. Azure. Search v10 használatával](search-get-started-dotnet-v10.md) .

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következő eszközökkel és szolgáltatásokkal rendelkezhet:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

+ Egy Azure Cognitive Search szolgáltatás. [Hozzon létre egy szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

+ A [Visual Studio](https://visualstudio.microsoft.com/downloads/)bármely kiadása. A mintakód a Visual Studio 2019 ingyenes közösségi kiadásában lett tesztelve.

A projekt beállításakor le fogja tölteni a [Azure.Search.DocUments NuGet csomagot](https://www.nuget.org/packages/Azure.Search.Documents/).

A .NET-hez készült Azure SDK megfelel a .net [Standard 2,0](/dotnet/standard/net-standard#net-implementation-support)-nek, ami a .NET-keretrendszer 4.6.1-es és a .net Core 2,0-as minimális követelmény.

## <a name="set-up-your-project"></a>A projekt beállítása

Állítsa össze a szolgáltatás kapcsolódási adatait, majd indítsa el a Visual studiót, és hozzon létre egy új, a .NET Core-on futtatható konzolablak-projektet.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Kulcs és végpont másolása

A szolgáltatás felé irányuló hívások URL-végpontot és hozzáférési kulcsot igényelnek minden kérelemben. Első lépésként keresse meg a projekthez hozzáadandó API-kulcsot és URL-címet. Az ügyfél egy későbbi lépésben való létrehozásakor mindkét értéket meg kell adnia.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **Beállítások**  >  **kulcsainál** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó teljes jogosultságokról, ha objektumokat hoz létre vagy töröl. Két felcserélhető elsődleges és másodlagos kulcs van. Bármelyiket használhatja.

   ![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-rest/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

### <a name="install-the-nuget-package"></a>A NuGet-csomag telepítése

A projekt létrehozása után adja hozzá az ügyféloldali kódtárat. A [Azure.Search.Documents csomag](https://www.nuget.org/packages/Azure.Search.Documents/) egy olyan ügyféloldali függvénytárból áll, amely biztosítja a .net-beli keresési szolgáltatással való együttműködéshez használt összes API-t.

1. Indítsa el a Visual studiót, és hozzon létre egy .NET Core Console-alkalmazást.

1. Az **eszközök**  >  **NuGet csomagkezelő** területén válassza a **NuGet-csomagok kezelése megoldást..**. lehetőséget. 

1. Kattintson a **Browse** (Tallózás) gombra.

1. Keresse meg `Azure.Search.Documents` és válassza ki a 11,0-es vagy újabb verziót.

1. A jobb oldalon kattintson a **telepítés** gombra a szerelvény projekthez és megoldáshoz való hozzáadásához.

### <a name="create-a-search-client"></a>Keresési ügyfél létrehozása

1. A **program. cs programban** módosítsa a névteret, `AzureSearch.SDK.Quickstart.v11` majd adja hozzá a következő `using` irányelveket.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Hozzon létre két ügyfelet: a [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) létrehozza az indexet, és betölti a [SearchClient](/dotnet/api/azure.search.documents.searchclient) , és lekérdezi egy meglévő indexet. A létrehozási/törlési jogokkal való hitelesítéshez mindkettőnek szüksége van a szolgáltatási végpontra és egy rendszergazdai API-kulcsra.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 – Index létrehozása

Ez a rövid útmutató egy olyan szállodai indexet hoz létre, amelyet a Hotel és a lekérdezése között fog betölteni. Ebben a lépésben definiálja a mezőket az indexben. Minden mező definíciója tartalmaz egy nevet, egy adattípust és egy attribútumot, amely meghatározza, hogy a rendszer hogyan használja a mezőt.

Ebben a példában a Azure.Search.Documents könyvtár szinkron metódusait használjuk az egyszerűség és az olvashatóság érdekében. Éles környezetekben azonban aszinkron metódusokat kell használnia, hogy az alkalmazás méretezhető és rugalmas legyen. A [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex)helyett például a [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) -et kellene használnia.

1. Adjon hozzá egy üres osztály-definíciót a projekthez: **Hotel. cs**

1. Másolja a következő kódot a **Hotel. cs** fájlba egy szállodai dokumentum struktúrájának definiálásához. A mező attribútumai határozzák meg, hogyan használják az alkalmazásokban. Az `IsFilterable` attribútumot például minden olyan mezőhöz hozzá kell rendelni, amely támogatja a szűrési kifejezést.

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   A [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) és a [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) használatával egyszerűsítheti a uments a Azure.Search.Doc-ügyfél függvénytárában. Mindkettő egy [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) származtatása, és lehetséges, hogy leegyszerűsíti a kódot:

   + `SimpleField` bármilyen adattípus lehet, mindig nem kereshető (figyelmen kívül hagyva a teljes szöveges keresési lekérdezéseknél), és lekérhető (nem rejtett). A többi attribútum alapértelmezés szerint ki van kapcsolva, de engedélyezve lehet. Használhat olyan `SimpleField` dokumentum-azonosítókat vagy mezőket, amelyek csak szűrők, dimenziók vagy pontozási profilok esetében használhatók. Ha igen, ügyeljen arra, hogy a forgatókönyvhöz szükséges attribútumokat (például `IsKey = true` egy dokumentum azonosítóját) alkalmazza. További információ: [SimpleFieldAttribute. cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) a forráskódban.

   + `SearchableField` karakterláncnak kell lennie, és mindig kereshető és lekérhető. A többi attribútum alapértelmezés szerint ki van kapcsolva, de engedélyezve lehet. Mivel ez a mezőtípus kereshető, támogatja a szinonimákat és az analizátor összes tulajdonságának teljes kiegészítését. További információ: [SearchableFieldAttribute. cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) a forráskódban.

   Függetlenül attól, hogy az alapszintű `SearchField` API-t vagy a segítő modellek valamelyikét használja-e, explicit módon engedélyeznie kell a szűrő, a dimenzió és a rendezési attribútumot. Például a [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), a [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)és a [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) explicit módon kell szerepelnie a fenti mintában látható módon. 

1. Adjon hozzá egy második üres osztály-definíciót a projekthez: **címe. cs**.  Másolja a következő kódot a osztályba.

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. Hozzon létre két további osztályt: a **Hotel. Methods. cs** és a **címek. Methods. cs** a ToString () felülbírálások. Ezek az osztályok a keresési eredmények megjelenítésére szolgálnak a konzol kimenetében.  Ezen osztályok tartalma nem szerepel ebben a cikkben, de a [githubon lévő fájlokból](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11)is másolhatja a kódot.

1. A **program. cs programban** hozzon létre egy [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) objektumot, majd hívja meg a [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) metódust, hogy kifejezze az indexet a keresési szolgáltatásban. Az index tartalmaz egy [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester) is, amely lehetővé teszi az automatikus kiegészítést a megadott mezőkön.

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Az Azure Cognitive Search megkeresi a szolgáltatásban tárolt tartalmakat. Ebben a lépésben az imént létrehozott szállodai indexnek megfelelő JSON-dokumentumokat fogja betölteni.

Az Azure Cognitive Searchban a keresési dokumentumok olyan adatstruktúrák, amelyek mind a lekérdezések indexeléséhez, mind pedig a kimenetekhez tartoznak. Egy külső adatforrásból beszerzett módon a dokumentumok bemenetei egy adatbázisban, a blob Storage-ban vagy a lemezen található JSON-dokumentumok soraiban lehetnek. Ebben a példában a JSON-dokumentumokat a kód négy szállodájának ajánljuk. 

Dokumentumok feltöltésekor [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) objektumot kell használnia. Egy `IndexDocumentsBatch` objektum olyan [műveleteket](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions)tartalmaz, amelyek mindegyike tartalmaz egy dokumentumot és egy olyan tulajdonságot, amely azt jelzi, hogy az Azure Cognitive Search milyen műveletet kell végrehajtani ([feltöltés, Egyesítés, törlés és mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. A **program. cs programban** hozzon létre egy tömböt a dokumentumok és az indexelési műveletek közül, majd adja át a tömböt a következőre: `IndexDocumentsBatch` . Az alábbi dokumentumok megfelelnek a Hotel osztály által definiált Hotels-rövid útmutató-indexnek.

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    Miután elvégezte a [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) objektum inicializálását, a [SearchClient](/dotnet/api/azure.search.documents.searchclient) objektum [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) meghívásával elküldheti az indexbe.

1. Adja hozzá a következő sorokat a Main () elemhez. A dokumentumok betöltése a SearchClient használatával történik, de a művelethez rendszergazdai jogosultságok is szükségesek a szolgáltatáshoz, amely általában a SearchIndexClient-hez van társítva. A művelet beállításának egyik módja a SearchClient beolvasása az SearchIndexClient-n keresztül (ebben a példában a adminClient).

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. Mivel ez egy olyan konzolos alkalmazás, amely egymás után futtatja az összes parancsot, adjon meg egy 2 másodperces várakozási időt az indexelés és a lekérdezések között.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    A 2 másodperces késleltetés kompenzálja az indexelést, amely aszinkron módon történik, így minden dokumentum indexelhető a lekérdezések végrehajtása előtt. A késések kódolása jellemzően csak demók, tesztek és példák esetében szükséges.

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

A lekérdezési eredményeket az első dokumentum indexelése után azonnal lekérheti, de az index tényleges tesztelésének meg kell várnia, amíg az összes dokumentum indexelve van.

Ez a szakasz két funkciót tartalmaz: a lekérdezési logikát és az eredményeket. Lekérdezésekhez használja a [keresési](/dotnet/api/azure.search.documents.searchclient.search) módszert. Ez a metódus a keresési szöveget (a lekérdezési karakterláncot) és egyéb [beállításokat](/dotnet/api/azure.search.documents.searchoptions)is végrehajtja.

A [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) osztály az eredményeket jelöli.

1. A **program. cs** eszközben hozzon létre egy **WriteDocuments** metódust, amely a keresési eredményeket a konzolra írja.

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Hozzon létre egy **RunQueries** metódust a lekérdezések végrehajtásához és az eredmények visszaadásához. Az eredmények a Hotel objektumai. Ez a példa a metódus-aláírást és az első lekérdezést mutatja be. Ez a lekérdezés bemutatja a Select paramétert, amely lehetővé teszi az eredmény összeállítását a dokumentum kiválasztott mezőinek használatával.

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    ```

1. A második lekérdezésben keressen egy kifejezésre, adjon hozzá egy szűrőt, amely kiválasztja azokat a dokumentumokat, amelyekben a minősítés meghaladja a 4-et, majd az értékelés szerint csökkenő sorrendbe rendezi őket. A Filter egy logikai kifejezés, amely egy index [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) mezőin keresztül lesz kiértékelve. A lekérdezéseket belefoglalhatja vagy kizárhatja az értékek közül. Ennek megfelelően a szűrő lekérdezéséhez nincs releváns pontszám társítva. 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. A harmadik lekérdezés bemutatja a searchFields, amely egy teljes szöveges keresési művelet hatókörét mutatja meghatározott mezőkre.

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. A negyedik lekérdezés az aspektusokat mutatja be, amelyek felhasználhatók egy sokoldalú navigációs struktúra struktúrájára. 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. Az ötödik lekérdezésben egy adott dokumentumot ad vissza. A dokumentumok keresése jellemző válasz egy eredményhalmaz OnClick eseményére.

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. Az utolsó lekérdezés az automatikus kiegészítés szintaxisát jeleníti meg, amely szimulálja a "SA" részleges felhasználói bevitelét, amely az indexben definiált sourceFields kapcsolódóan két lehetséges egyezésre van feloldva.

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. Adja hozzá a **RunQueries** a Main () elemhez.

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

Az előző lekérdezésekben a [kifejezések egyezésének több módja látható a lekérdezésekben](search-query-overview.md#types-of-queries): teljes szöveges keresés, szűrők és automatikus kiegészítés.

A teljes szöveges keresés és a szűrők a [SearchClient. Search](/dotnet/api/azure.search.documents.searchclient.search) metódus használatával hajthatók végre. Keresési lekérdezés adható át a `searchText` karakterláncban, míg a [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) osztály [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) tulajdonságában egy szűrési kifejezés adható át. A keresés nélküli szűréshez csak adja `"*"` meg a `searchText` [keresési](/dotnet/api/azure.search.documents.searchclient.search) metódus paraméterét. Szűrés nélküli kereséshez hagyja üresen a `Filter` tulajdonságot, vagy ne adjon meg egyetlen `SearchOptions` példányt sem.

## <a name="run-the-program"></a>A program futtatása

Nyomja le az F5 billentyűt az alkalmazás újraépítéséhez és a program teljes körű futtatásához. 

A kimenet magában foglalja a [Console. WriteLine](/dotnet/api/system.console.writeline)és a lekérdezési adatok és eredmények hozzáadásával kapcsolatos üzeneteket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Ebben a C# gyors útmutatóban egy sor feladaton keresztül dolgozott egy index létrehozásához, a dokumentumok betöltéséhez és a lekérdezések futtatásához. Az olvashatóság és a megértés érdekében a kód leegyszerűsítése különböző fázisokban volt. Ha Ön az alapvető fogalmakkal is rendelkezik, javasoljuk, hogy a következő cikkből megtudhatja, milyen alternatív megközelítésekre és fogalmakra van szükség, amelyek elmélyítik az Ön ismereteit. 

> [!div class="nextstepaction"]
> [Fejlesztés a .NET-ben](search-howto-dotnet-sdk.md)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

> [!div class="nextstepaction"]
> [A költségek elemzésének megkezdése a Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
