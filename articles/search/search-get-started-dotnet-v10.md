---
title: Örökölt C# gyors útmutató
titleSuffix: Azure Cognitive Search
description: Ez a C#-útmutató a 10-es verziójú ügyféloldali kódtárat (Microsoft. Azure. Search) használja a keresési indexek létrehozásához, betöltéséhez és lekérdezéséhez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8dc2eb898c12e374bc503c5a05f00eb20667443b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94701840"
---
# <a name="quickstart-create-a-search-index-using-the-legacy-microsoftazuresearch-v10-client-library"></a>Gyors útmutató: keresési index létrehozása a régi Microsoft. Azure. Search v10 ügyféloldali kódtár használatával

Ez a cikk az örökölt [**Microsoft. Azure. Search**](/dotnet/api/overview/azure/search/client10) (10-es verzió) ügyféloldali függvénytárának C#-es rövid útmutatója, amelyet mostantól a [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) (11-es verzió) ügyféloldali kódtár váltott ki.

> [!NOTE]
> Ha már rendelkezik meglévő vagy fedélzeti fejlesztési projektekkel, továbbra is használhatja a 10-es verziót. De az új projektek esetében, vagy új funkciók használatához érdemes áttérni az [új könyvtárra](/dotnet/api/overview/azure/search.documents-readme).

## <a name="about-this-quickstart"></a>Tudnivalók a rövid útmutatóról

Hozzon létre egy .NET Core Console-alkalmazást a C#-ban, amely létrehoz, betölt és lekérdez egy Azure Cognitive Search indexet a Visual Studióval és a [Microsoft. Azure. Search ügyféloldali kódtárak](/dotnet/api/overview/azure/search/client10)használatával. 

Ez a cikk azt ismerteti, hogyan hozhatja létre az alkalmazást. [Letöltheti és futtathatja a teljes alkalmazást](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v10)is.

> [!NOTE]
> A jelen cikkben szereplő bemutató kód az Azure Cognitive Search 10-es verzióhoz készült .NET SDK szinkron metódusait használja az egyszerűség kedvéért. Éles környezetekben azonban azt javasoljuk, hogy a saját alkalmazásaiban lévő aszinkron metódusok használatával méretezhető és rugalmas legyen. Használhatja például `CreateAsync` `DeleteAsync` a és a és a helyett a és a értéket `Create` `Delete` .

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

+ Egy Azure Cognitive Search szolgáltatás. [Hozzon létre egy szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

+ A [Visual Studio](https://visualstudio.microsoft.com/downloads/)bármely kiadása. A mintakód és az utasítások tesztelése az ingyenes közösségi kiadásban történt.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A szolgáltatás felé irányuló hívások URL-végpontot és hozzáférési kulcsot igényelnek minden kérelemben. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-rest/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-your-environment"></a>A környezet kialakítása

Először nyissa meg a Visual studiót, és hozzon létre egy új, a .NET Core-on futtatható Console app-projektet.

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

A [Microsoft. Azure. Search csomag](https://www.nuget.org/packages/Microsoft.Azure.Search/) néhány, NuGet-csomagként terjesztett ügyféloldali kódtárat tartalmaz.

Ehhez a projekthez használja a `Microsoft.Azure.Search` NuGet csomag 10-es verzióját és a legújabb `Microsoft.Extensions.Configuration.Json` NuGet-csomagot.

1. Az **eszközök**  >  **NuGet csomagkezelő** területén válassza a **NuGet-csomagok kezelése megoldást..**. lehetőséget. 

1. Kattintson a **Browse** (Tallózás) gombra.

1. Keresse meg `Microsoft.Azure.Search` és válassza ki a 10-es verziót.

1. A jobb oldalon kattintson a **telepítés** gombra a szerelvény projekthez és megoldáshoz való hozzáadásához.

1. Ismételje meg a `Microsoft.Extensions.Configuration.Json` beállítást, és válassza a 2.2.0 vagy újabb verziót.


### <a name="add-azure-cognitive-search-service-information"></a>Azure Cognitive Search szolgáltatás adatainak hozzáadása

1. Megoldáskezelő kattintson a jobb gombbal a projektre, és válassza az  >  **új elem hozzáadása..** . lehetőséget. 

1. Az új elem hozzáadása lapon keressen rá a "JSON" kifejezésre, és adja vissza a JSON-hez kapcsolódó elemek listáját.

1. Válassza a **JSON-fájl** elemet, nevezze el a "appsettings.json" fájlt, majd kattintson a **Hozzáadás** gombra. 

1. Adja hozzá a fájlt a kimeneti könyvtárhoz. Kattintson a jobb gombbal a appsettings.jselemre, majd válassza a **Tulajdonságok** lehetőséget. A **Másolás kimeneti könyvtárba** lapon válassza a **Másolás, ha újabb** lehetőséget.

1. Másolja a következő JSON-t az új JSON-fájlba. 

    ```json
    {
      "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
      "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
      "SearchIndexName": "hotels-quickstart"
    }
    ```

1. Cserélje le a keresési szolgáltatás nevét (a-SEARCH-SERVICE-NAME) és a felügyeleti API-kulcsot (a-ADMIN-API-KEY) érvényes értékekkel. Ha a szolgáltatás végpontja `https://mydemo.search.windows.net` , a szolgáltatás neve "" lesz `mydemo` .

### <a name="add-class-method-files-to-your-project"></a>Osztály hozzáadása ". Metódus "fájlok a projekthez

Ez a lépés szükséges az értelmes kimenet létrehozásához a-konzolon. Amikor kinyomtatja az eredményeket a konzol ablakába, a Hotel objektum egyedi mezőinek karakterláncként kell szerepelnie. Ez a lépés a [ToString ()](/dotnet/api/system.object.tostring) megvalósítását valósítja meg a feladat végrehajtásához, amelyet a szükséges kód két új fájlba másolásával hajt végre.

1. Adja hozzá a következő két üres osztály-definíciót a projekthez: címe. Methods. cs, Hotel. Methods. cs

1. A címben. Methods. cs fájlban írja felül az alapértelmezett tartalmakat a következő kóddal, a 1-25-es [sorokkal](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Address.Methods.cs#L1-L25).

1. A Hotel. Methods. cs, [sorok másolása 1-68](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Hotel.Methods.cs#L1-L68).

## <a name="1---create-index"></a>1 – index létrehozása

A Hotels index egyszerű és összetett mezőket tartalmaz, ahol egy egyszerű mező a "pezsgő" vagy a "Description", az összetett mezők pedig almezőket tartalmazó címek vagy szobák gyűjteménye. Ha egy index összetett típusokat tartalmaz, különítse el az összetett mezők definícióit külön osztályokban.

1. Adjon hozzá két üres osztály-definíciót a projekthez: címe. cs, Hotel. cs

1. A címben. cs fájlból írja felül az alapértelmezett tartalmakat a következő kóddal:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

1. A Hotel. cs esetében az osztály az index általános szerkezetét határozza meg, beleértve a címek osztályra mutató hivatkozásokat is.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    A mező attribútumai határozzák meg, hogyan használják az alkalmazásokban. Az `IsSearchable` attribútumot például minden olyan mezőhöz hozzá kell rendelni, amelynek szerepelnie kell egy teljes szöveges keresésben. 
    
    > [!NOTE]
    > A .net SDK-ban a mezőket explicit módon kell megadni,, [`IsSearchable`](/dotnet/api/microsoft.azure.search.models.field.issearchable) [`IsFilterable`](/dotnet/api/microsoft.azure.search.models.field.isfilterable) [`IsSortable`](/dotnet/api/microsoft.azure.search.models.field.issortable) és [`IsFacetable`](/dotnet/api/microsoft.azure.search.models.field.isfacetable) . Ez a viselkedés ellentétben áll azzal a REST APItel, amely implicit módon engedélyezi a kiosztást az adattípusok alapján (például az egyszerű karakterlánc mezők automatikusan kereshetők).

    Az indexben pontosan egy mezőnek `string` kell lennie az  egyes dokumentumok egyedi azonosítására szolgáló kulcsmező. Ebben a sémában a kulcs a következő: `HotelId` .

    Ebben az indexben a Description (Leírás) mezők a választható [`analyzer`](/dotnet/api/microsoft.azure.search.models.field.analyzer) tulajdonságot használják, ha felül szeretné írni az alapértelmezett standard Lucene-elemzőt. A `description_fr` mező a francia Lucene Analyzert ([FrLucene](/dotnet/api/microsoft.azure.search.models.analyzername.frlucene)) használja, mert francia nyelvű szöveget tárol. A a `description` választható Microsoft nyelvi analizátort ([EnMicrosoft](/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft)) használja.

1. A program. cs fájlban hozza létre az osztály egy példányát a [`SearchServiceClient`](/dotnet/api/microsoft.azure.search.searchserviceclient) szolgáltatáshoz való kapcsolódáshoz az alkalmazás konfigurációs fájljában (appsettings.js) tárolt értékek alapján. 

   `SearchServiceClient` rendelkezik egy [`Indexes`](/dotnet/api/microsoft.azure.search.searchserviceclient.indexes) tulajdonsággal, amely az Azure Cognitive Search indexek létrehozásához, listázásához, frissítéséhez vagy törléséhez szükséges összes módszert biztosítja. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Microsoft.Azure.Search.Models.Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Ha lehetséges, ossza meg az alkalmazás egy példányát, `SearchServiceClient` hogy elkerülje a túl sok kapcsolat megnyitását. Az osztály-metódusok olyan szálon biztonságosak, amely lehetővé teszi az ilyen megosztást.

   Az osztálynak több konstruktora van. Az, amelyiket Ön szeretne, a keresőszolgáltatása nevét és egy `SearchCredentials` objektumot használ paraméterként. A `SearchCredentials` becsomagolja az API-kulcsot.

    Az index definíciójában az objektumok létrehozásának legegyszerűbb módja a `Field` metódus meghívása `FieldBuilder.BuildForType` , a Model osztály átadása a Type paraméterhez. A modellosztály olyan tulajdonságokkal rendelkezik, amelyek az index mezőire mutatnak. Ez a leképezés lehetővé teszi dokumentumok kötését a keresési indexből a modell osztály példányaira.

    > [!NOTE]
    > Ha nem tervez modellosztályt használni, közvetlenül `Field` objektumok létrehozásával is meghatározhatja az indexet. A konstruktornak megadhatja a mező nevét és az adattípust (vagy sztringmezők esetében az elemzőnek). Más tulajdonságokat is beállíthat, például: `IsSearchable` ,, `IsFilterable` hogy csak néhányat említsünk.
    >

1. Nyomja le az F5 billentyűt az alkalmazás felépítéséhez és az index létrehozásához. 

    Ha a projekt sikeresen létrejön, megnyílik egy konzolablak, amely megjeleníti az állapotjelző üzeneteket a képernyőre az index törléséhez és létrehozásához. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Az Azure Cognitive Searchban a dokumentumok olyan adatstruktúrák, amelyek mind a lekérdezések indexeléséhez, mind pedig a kimenetekhez tartoznak. Egy külső adatforrásból beszerzett módon a dokumentumok bemenetei egy adatbázisban, a blob Storage-ban vagy a lemezen található JSON-dokumentumok soraiban lehetnek. Ebben a példában a JSON-dokumentumokat a kód négy szállodájának ajánljuk. 

Dokumentumok feltöltésekor egy objektumot kell használnia [`IndexBatch`](/dotnet/api/microsoft.azure.search.models.indexbatch) . Az egy `IndexBatch` objektumok gyűjteményét tartalmazza [`IndexAction`](/dotnet/api/microsoft.azure.search.models.indexaction) , amelyek mindegyike tartalmaz egy dokumentumot és egy olyan tulajdonságot, amely azt jelzi, hogy az Azure Cognitive Search milyen műveletet kell végrehajtani ([feltöltés, Egyesítés, törlés és mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. A program. cs programban hozzon létre egy tömböt a dokumentumok és az indexelési műveletek közül, majd adja át a tömböt a következőre: `IndexBatch` . Az alábbi dokumentumok megfelelnek a Hotel – rövid útmutató indexnek, amelyet a szálloda és a címe osztályok határoznak meg.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
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
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Miután elvégezte az `IndexBatch` objektum inicializálását, elküldheti az indexnek az [`Documents.Index`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index) objektum hívásával [`SearchIndexClient`](/dotnet/api/microsoft.azure.search.searchindexclient) . `Documents` a egy tulajdonsága, `SearchIndexClient` amely az indexben lévő dokumentumok hozzáadására, módosítására, törlésére vagy lekérdezésére szolgáló metódusokat biztosít.

    A `try` / `catch` metódus meghívása során az `Index` indexelési hibák megmaradnak, ami akkor fordulhat elő, ha a szolgáltatás nagy terhelés alatt áll. Az éles kódban késleltetheti, majd újra megpróbálkozhat a meghiúsult dokumentumok indexelésével, vagy a napló és a folytatás hasonló módon történő megismétlésével, vagy az alkalmazás adatkonzisztenciáji követelményeinek megfelelő más módon történő kezelésével.

    A 2 másodperces késleltetés kompenzálja az indexelést, amely aszinkron módon történik, így minden dokumentum indexelhető a lekérdezések végrehajtása előtt. A késések kódolása jellemzően csak demók, tesztek és példák esetében szükséges.

1. A program. cs, a Mainban, a "2-Load Documents" sorok megjegyzéseit. 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Az alkalmazás újraépítéséhez nyomja le az F5 billentyűt. 

    Ha a projekt sikeresen felépítve, megnyílik egy konzolablak, amely a dokumentumok feltöltésével kapcsolatos üzenettel jelenik meg. A Azure Portal keresési szolgáltatás **áttekintése** lapján a Hotels-rövid útmutatónak már 4 dokumentummal kell rendelkeznie.

További információ a dokumentumok feldolgozásáról: ["a .net SDK kezeli a dokumentumokat"](search-howto-dotnet-sdk-v10.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

A lekérdezési eredményeket az első dokumentum indexelése után azonnal lekérheti, de az index tényleges tesztelésének meg kell várnia, amíg az összes dokumentum indexelve van. 

Ez a szakasz két funkciót tartalmaz: a lekérdezési logikát és az eredményeket. Lekérdezésekhez használja a [`Search`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search) metódust. Ezzel a módszerrel a keresési szöveg és más [Paraméterek](/dotnet/api/microsoft.azure.search.models.searchparameters)is bekerülnek. 

Az [`DocumentsSearchResult`](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) osztály az eredményeket jelöli.


1. A program. cs eszközben hozzon létre egy WriteDocuments metódust, amely a keresési eredményeket a konzolra írja.

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Hozzon létre egy RunQueries metódust a lekérdezések végrehajtásához és az eredmények visszaadásához. Az eredmények a Hotel objektumai. A Select paraméterrel felveheti a felületet az egyes mezőkre. Ha egy mező nem szerepel a Select paraméterben, annak megfelelő szállodai tulajdonsága null értékű lesz.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    A [kifejezéseket kétféleképpen lehet egyeztetni a lekérdezésekben](search-query-overview.md#types-of-queries): teljes szöveges keresés és szűrők. A teljes szöveges keresési lekérdezés egy vagy több kifejezést keres az `IsSearchable` index mezőiben. A szűrő egy logikai kifejezés, amely egy index mezőin van kiértékelve `IsFilterable` . A teljes szöveges keresést és a szűrőket együtt vagy külön is használhatja.

    A keresések és a szűrések egyaránt a `Documents.Search` módszer használatával vannak végrehajtva. Keresési lekérdezések a `searchText` paraméterben, szűrőkifejezések pedig a `SearchParameters` osztály `Filter` tulajdonságában adhatóak át. A keresés nélküli szűrés végrehajtásához a `searchText` paraméter számára a `"*"` kifejezést adja át. A szűrés nélküli keresés végrehajtásához ne állítsa be a `Filter` tulajdonságot, vagy egyáltalán ne adja át azt egy `SearchParameters`-példányban.

1. A program. cs nevű programban a Mainban a "3-Search" sorok megjegyzéseit. 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. A megoldás már befejeződött. Nyomja le az F5 billentyűt az alkalmazás újraépítéséhez és a program teljes körű futtatásához. 

    A kimenet ugyanazokat az üzeneteket tartalmazza, mint a korábban, a lekérdezési adatok és eredmények hozzáadásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Ebben a C# rövid útmutatóban egy sor feladatot dolgozott ki egy index létrehozásához, a dokumentumok betöltéséhez és a lekérdezések futtatásához. Az olvashatóság és a megértés érdekében a kód leegyszerűsítése különböző fázisokban volt. Ha Ön az alapvető fogalmakkal is rendelkezik, javasoljuk, hogy a következő cikkből megtudhatja, milyen alternatív megközelítésekre és fogalmakra van szükség, amelyek elmélyítik az Ön ismereteit. 

A mintakód és az index ezen egy kibontott verziója. A következő minta egy Rooms-gyűjteményt hoz létre, különböző osztályokat és műveleteket használ, és közelebbről vizsgálja a feldolgozás működését.

> [!div class="nextstepaction"]
> [Fejlesztés a .NET-ben](search-howto-dotnet-sdk.md)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

> [!div class="nextstepaction"]
> [A költségek elemzésének megkezdése a Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)