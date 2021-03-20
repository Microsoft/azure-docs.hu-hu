---
title: Frissítés a .NET SDK 11-es verziójára
titleSuffix: Azure Cognitive Search
description: Telepítse át a kódot az Azure Cognitive Search .NET SDK 11-es verziójára régebbi verzióról. Ismerje meg, hogy mi az új, és milyen kód módosítása szükséges.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: df8841cb2dcac6335b09a5e7715f42c508c69e76
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99536815"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Frissítés az Azure Cognitive Search .NET SDK 11-es verziójára

Ha a [.net SDK](/dotnet/api/overview/azure/search)10,0-es vagy régebbi verzióját használja, ez a cikk segítséget nyújt a 11-es verzióra és a **Azure.Search.Documents** -ra való frissítésre.

A 11-es verzió egy teljes mértékben újratervezett ügyféloldali kódtár, amelyet az Azure SDK Fejlesztői csapata szabadít fel (az előző verziókat az Azure Cognitive Search fejlesztői csapat hozta létre). A rendszer újratervezte a kódtárat a többi Azure-ügyfélszoftverrel való nagyobb konzisztencia érdekében, az [Azure. Core](/dotnet/api/azure.core) és [ aSystem.Text.Js](/dotnet/api/system.text.json), valamint a gyakori feladatokra vonatkozó ismerős megközelítések megvalósításával.

Az új verzióban megjelenő legfontosabb különbségek a következők:

+ Egy csomag és egy könyvtár több
+ Új csomag neve: `Azure.Search.Documents` helyett `Microsoft.Azure.Search` .
+ Három ügyfél a kettő helyett: `SearchClient` , `SearchIndexClient` , `SearchIndexerClient`
+ Különböző API-k és kisebb szerkezeti különbségek elnevezése az egyes feladatok egyszerűsítése érdekében

Ezen a cikken kívül áttekintheti a [módosítási naplót](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) a .net SDK 11-es verziójának változásainak részletezett listájához.

## <a name="package-and-library-consolidation"></a>Csomag-és könyvtár-konszolidáció

A 11-es verzió összevonja a több csomagot és tárat. Az áttelepítés után kevesebb könyvtárat fog kezelni.

+ [Azure.Search.Documents-csomag](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [API-referenciák az ügyféloldali kódtár számára](/dotnet/api/overview/azure/search.documents-readme)

## <a name="client-differences"></a>Ügyfelek közötti különbségek

Ha lehetséges, az alábbi táblázat a két verzió közötti ügyféloldali kódtárakat képezi le.

| Műveletek hatóköre | Microsoft. Azure. Search &nbsp; (v10) | Azure.Search.Documents &nbsp; (v11) |
|---------------------|------------------------------|------------------------------|
| A lekérdezésekhez és az indexek feltöltéséhez használt ügyfél. | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| Indexekhez, elemzőekhez, szinonimák megfeleltetéséhez használt ügyfél | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Az indexelő, az adatforrások és a szakértelmével által használt ügyfél | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**új**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` mindkét verzióban létezik, de különböző dolgokat támogat. A 10-es verzióban `SearchIndexClient` Indexek és egyéb objektumok hozhatók létre. A 11-es verzióban a `SearchIndexClient` meglévő indexekkel működik. A kód frissítésekor a félreértések elkerülése érdekében figyelembe kell venni, hogy milyen sorrendben frissülnek az ügyfelek hivatkozásai. A [frissítéshez szükséges lépések](#UpgradeSteps) végrehajtásával csökkentheti a karakterlánc-helyettesítési problémákat.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Elnevezési és egyéb API-eltérések

Az ügyfél-eltérések mellett (a korábban említettek szerint) több más API is átnevezve lett, és bizonyos esetekben újratervezték. Az osztályok nevének különbségeit az alábbiakban összegzi. Ez a lista nem teljes, de az API-t a feladat szerint módosítja, ami hasznos lehet az adott kódrészletek változatának módosításához. Az API-frissítések részletezett listája a GitHubon a [változási naplóban](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) található `Azure.Search.Documents` .

### <a name="authentication-and-encryption"></a>Hitelesítés és titkosítás

| 10-es verzió | 11-es verzió – egyenértékű |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (az [előzetes verzió SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) -ban általánosan elérhető szolgáltatásként létezett) | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Indexek, elemzők, szinonimák térképek

| 10-es verzió | 11-es verzió – egyenértékű |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Mező](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [Adattípus](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (más néven `AnalyzerName` `LexicalAnalyzerName` ) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (más néven `StandardTokenizerV2` `LuceneStandardTokenizerV2` ) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (más néven `TokenizerName` `LexicalTokenizerName` ) |
| [SynonymMap. Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Nincsenek. Hivatkozások eltávolítása a következőre: `Format` . |

A mező-definíciók racionalizálva vannak: a [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield), a [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield), a [ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) új API-k a mezők definícióinak létrehozásához.

### <a name="indexers-datasources-skillsets"></a>Indexelő, adatforrások, szakértelmével

| 10-es verzió | 11-es verzió – egyenértékű |
|------------|-----------------------|
| [Indexelő](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Ügyességi](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Készségkészlet](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Adatimportálás

| 10-es verzió | 11-es verzió – egyenértékű |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Lekérdezési definíciók és eredmények

| 10-es verzió | 11-es verzió – egyenértékű |
|------------|-----------------------|
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) vagy [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1), attól függően, hogy az eredmény egyetlen dokumentum vagy több. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |

### <a name="json-serialization"></a>JSON-szerializálás

Alapértelmezés szerint az Azure SDKSystem.Text.Jshasznál [ a](/dotnet/api/system.text.json) JSON-szerializáláshoz, és ezen API-k képességeire támaszkodva kezeli a korábban a natív [SerializePropertyNamesAsCamelCaseAttribute](/dotnet/api/microsoft.azure.search.models.serializepropertynamesascamelcaseattribute) osztályon keresztül megvalósított szöveges átalakításokat, amelyeknek nincs megfelelő jogosultsága az új könyvtárban.

A tulajdonságok neveinek camelCase való szerializálásához használhatja a [JsonPropertyNameAttribute](/dotnet/api/system.text.json.serialization.jsonpropertynameattribute) (ehhez hasonló [példához](https://github.com/Azure/azure-sdk-for-net/tree/d263f23aa3a28ff4fc4366b8dee144d4c0c3ab10/sdk/search/Azure.Search.Documents#use-c-types-for-search-results)hasonlóan).

Másik lehetőségként beállíthatja a [JsonSerializerOptions](/dotnet/api/system.text.json.jsonserializeroptions)-ben megadott [JsonNamingPolicy](/dotnet/api/system.text.json.jsonnamingpolicy) . A [Microsoft. Azure. Core. térbeli információi](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial/README.md#deserializing-documents) alapján a következő, a code-on alapuló System.Text.Jsa camelCase használatát mutatja be anélkül, hogy az összes tulajdonságot attribútummal kellene elvégeznie:

```csharp
// Get the Azure Cognitive Search endpoint and read-only API key.
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
AzureKeyCredential credential = new AzureKeyCredential(Environment.GetEnvironmentVariable("SEARCH_API_KEY"));

// Create serializer options with our converter to deserialize geographic points.
JsonSerializerOptions serializerOptions = new JsonSerializerOptions
{
    Converters =
    {
        new MicrosoftSpatialGeoJsonConverter()
    },
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
};

SearchClientOptions clientOptions = new SearchClientOptions
{
    Serializer = new JsonObjectSerializer(serializerOptions)
};

SearchClient client = new SearchClient(endpoint, "mountains", credential, clientOptions);
Response<SearchResults<Mountain>> results = client.Search<Mountain>("Rainier");
```

Ha a JSON-szerializáláshoz Newtonsoft.Jst használ, a globális elnevezési házirendeket a hasonló attribútumok használatával vagy a [JsonSerializerSettings](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonSerializerSettings.htm)tulajdonságok használatával adhatja át. A fentieknek megfelelő példát a további tudnivalókat lásd: [deszerializáló dokumentumok példa](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md) a Newtonsoft.Js.


<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>A 11-es verzió

Az Azure Cognitive Search ügyféloldali kódtár minden verziója a REST API megfelelő verzióját célozza meg. A REST API a szolgáltatás alapját képezi, és egyedi SDK-k a REST API egy verzióját becsomagolják. .NET-fejlesztőként hasznos lehet áttekinteni [REST API dokumentációt](/rest/api/searchservice/) , ha adott objektumokra vagy műveletekre több hátteret szeretne használni.

A 11-es verzió a [2020-06-30 keresési szolgáltatást](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)célozza meg. Mivel a 11-es verzió egy új, az alapoktól kiépített ügyféloldali kódtár is, a fejlesztési erőfeszítések többsége a 10-es verzióra koncentrál, és néhány REST API funkció támogatása továbbra is függőben van.

Az 11,0-es verzió teljes mértékben támogatja a következő objektumokat és műveleteket:

+ Index létrehozása és kezelése
+ Szinonimák leképezésének létrehozása és kezelése
+ Minden lekérdezés típusa és szintaxisa (kivéve a Geo-térbeli szűrőket)
+ Indexelő objektumok és műveletek az Azure-adatforrások indexeléséhez, beleértve az adatforrásokat és a szakértelmével

Az 11,1-es verzió a következőt adja meg:

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (hozzáadva a 11,1-es verzióban)
+ A [szerializáló tulajdonság](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (a 11,1-ben hozzáadva) az egyéni szerializálás támogatásához

### <a name="pending-features"></a>Függőben lévő funkciók

A következő 10-es verziójú funkciók még nem érhetők el a 11. verzióban. Ha szüksége van ezekre a funkciókra, tartsa lenyomva az áttelepítést, amíg azok nem támogatottak.

+ [Tudástár](knowledge-store-concept-intro.md)
+ Térinformatikai típusok – a térinformatikai típusok első osztályú támogatása még folyamatban van. Egyelőre a [Microsoft. térbeli](https://www.nuget.org/packages/Microsoft.Spatial/) csomag használatával lehet támogatni a földrajzi műveleteket. Ilyenek például a [System.Text.Js](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) és a [Newtonsoft.Js](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései

A következő lépések végrehajtásával kezdheti meg a kód áttelepítését a szükséges feladatok első készletének bejárásával, különösen az ügyfél-referenciák tekintetében.

1. Telepítse a [Azure.Search.Documents csomagot](https://www.nuget.org/packages/Azure.Search.Documents/) úgy, hogy a jobb gombbal a projekt hivatkozásaira kattint, és kiválasztja a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

1. Cserélje le a Microsoft. Azure. Search utasításait a következőre:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. A JSON-szerializálást igénylő osztályok esetében cserélje le a-t a következőre: `using Newtonsoft.Json` `using System.Text.Json.Serialization` .

1. Az ügyfél-hitelesítési kód módosítása. A korábbi verziókban az ügyfél objektumának tulajdonságaival állíthatja be az API-kulcsot (például a [SearchServiceClient. hitelesítő adatok](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) tulajdonságot). A jelenlegi verzióban a [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) osztály használatával adja át a kulcsot hitelesítő adatként, így ha szükséges, az API-kulcsot új ügyfélalkalmazások létrehozása nélkül is frissítheti.

   Az ügyfél tulajdonságainak egyszerűsítése csak `Endpoint` , `ServiceName` és `IndexName` (ahol szükséges). A következő példa a rendszer [URI](/dotnet/api/system.uri) osztályát használja, hogy a végpontot és a [környezeti](/dotnet/api/system.environment) osztályt adja meg a kulcs értékének olvasásához:

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. Új ügyfél-referenciák hozzáadása az indexelő szolgáltatással kapcsolatos objektumokhoz. Ha indexelő, adatforrásokat vagy szakértelmével használ, módosítsa az ügyfél [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)mutató hivatkozásait. Ez az ügyfél a 11-es verzióban új, és nincs előzménye.

1. A gyűjtemények és a felsorolások áttekintése. Az új SDK-ban az összes lista csak olvasható, hogy elkerülje az alsóbb rétegbeli problémákat, ha a lista null értékeket tartalmaz. A kód módosításával elemeket adhat hozzá egy listához. Ha például karakterláncokat szeretne hozzárendelni egy Select tulajdonsághoz, ezeket a következőképpen adja hozzá:

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   A Select, a Faces, a SearchFields, a SourceFields, a ScoringParameters és a OrderBy minden olyan listát, amelyet most újra kell építeni.

1. A lekérdezésekhez és az adatimportáláshoz tartozó ügyfél-referenciák frissítése. A [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) példányait [SearchClient](/dotnet/api/azure.search.documents.searchclient)értékre kell módosítani. A félreértések elkerülése érdekében győződjön meg arról, hogy a következő lépéshez való továbblépés előtt minden példányt elkapjon.

1. Az ügyfél-referenciák frissítése az index, a szinonimák leképezése és az elemző objektumok számára. A [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) példányait [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient)értékre kell módosítani. 

1. A kód hátralévő részében frissítse az osztályokat, a metódusokat és a tulajdonságokat az új könyvtár API-jai használatához. Az [elnevezési különbségek](#naming-differences) szakasz elindítható, de a [módosítási naplót](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)is áttekintheti.

   Ha nem találja az egyenértékű API-kat, javasoljuk, hogy naplózza a problémát, [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) hogy javítsa a dokumentációt, vagy vizsgálja meg a problémát.

1. Hozza létre újra a megoldást. A felépítési hibák vagy figyelmeztetések kijavítása után további módosításokat végezhet az alkalmazásban, hogy kihasználhassa az [új funkciókat](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes"></a>Kompatibilitástörő változások

A könyvtárak és API-k elsöprő változásai miatt a 11-ös verzióra való frissítés nem triviális, és olyan változást jelent, amely abban az értelemben, hogy a kód már nem lesz kompatibilis a 10-es és a korábbi verziókkal. A különbségek alapos áttekintéséhez tekintse meg a [változási naplót](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) `Azure.Search.Documents` .

A szolgáltatási verziók frissítései esetében, ahol a Code Changes in 11-es verzió a meglévő funkciókhoz kapcsolódik (és nem csak az API-k újrabontása), a következő viselkedési változásokat fogja találni:

+ A [BM25 ranking algoritmus](index-ranking-similarity.md) újabb technológiával helyettesíti az előző rangsorolási algoritmust. Az új szolgáltatások ezt az algoritmust automatikusan használják majd. Meglévő szolgáltatások esetén a paramétereket az új algoritmus használatára kell beállítani.

+ A null értékek [rendezett eredményei](search-query-odata-orderby.md) módosultak ebben a verzióban, és először Null érték jelenik meg, ha a rendezés `asc` és az utolsó, ha a rendezés `desc` . Ha kódot írt a null értékek rendezésének kezeléséhez, tekintse át és távolítsa el ezt a kódot, ha már nincs rá szükség.

## <a name="next-steps"></a>Következő lépések

+ [Azure.Search.Documents-csomag](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Minták a GitHubon](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API-referenciája](/dotnet/api/overview/azure/search.documents-readme)