---
title: C#-oktatóanyag az első alkalmazás létrehozásához
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan hozhatja létre az első C# Search-alkalmazást lépésről lépésre. Az oktatóanyag egy, a GitHubon működő alkalmazásra mutató letöltési hivatkozást, valamint az alkalmazás teljes körű felépítésének folyamatát ismerteti.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a57e45b264badffd0305eb6ac5b3c8f7c42adf3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695124"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Oktatóanyag: az első keresőalkalmazás létrehozása a .NET SDK használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre olyan webalkalmazást, amely az Azure Cognitive Search és a Visual Studio használatával kérdezi le és adja vissza az eredményeket egy keresési indexből.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Fejlesztési környezet beállítása
> * Adatstruktúrák modellezése
> * Weblap létrehozása lekérdezési bemenetek gyűjtéséhez és eredmények megjelenítéséhez
> * Keresési módszer definiálása
> * Az alkalmazás tesztelése

Azt is megtudhatja, hogy milyen egyszerű keresési hívást végez. A kifejleszteni kívánt kódban szereplő kulcsfontosságú utasítások a következő néhány sorban vannak beágyazva.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

Csak egy hívás kérdezi le az indexet, és visszaadja az eredményeket.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="A * Pool * keresése" border="true":::

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag a Hotels-Sample-indexet használja, amelyet gyorsan létrehozhat saját keresési szolgáltatásán az [adatimportálási](search-get-started-portal.md)útmutató lépéseit követve. Az index az összes keresési szolgáltatás beépített adatforrásaként elérhető, fiktív szállodai adatforrást tartalmaz.

Ebben az oktatóanyagban az első lecke egy alapszintű lekérdezési struktúrát és egy keresési oldalt hoz létre, amelyet a következő leckében fog növelni, hogy tartalmazza a lapozást, a dimenziókat és az előre megadott élményt.

A kód befejezett verziója a következő projektben található:

* [1 – alapszintű keresés – oldal (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

Ez az oktatóanyag a Azure.Search.Documents (11-es verzió) csomag használatára lett frissítve. A .NET SDK egy korábbi verziójával kapcsolatban lásd: [Microsoft. Azure. Search (10-es verzió)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)mintakód.

## <a name="prerequisites"></a>Előfeltételek

* [Meglévő keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése.

* Hozza létre a Hotels-Sample-indexet a gyors útmutató [: keresési index létrehozása](search-get-started-portal.md)című témakörben leírtak alapján.

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Azure Cognitive Search ügyféloldali kódtár (11-es verzió)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>A projekt telepítése és futtatása a GitHubról

Ha egy működő alkalmazásra szeretne ugrani, kövesse az alábbi lépéseket a kész kód letöltéséhez és futtatásához.

1. Keresse meg a mintát a GitHubon: [első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11).

1. A [gyökérkönyvtárban](https://github.com/Azure-Samples/azure-search-dotnet-samples)válassza a **kód**, majd a **klón** vagy a **zip letöltése** lehetőséget a projekt privát helyi másolatának létrehozásához.

1. A Visual Studióban navigáljon a (z) elemre, és nyissa meg az alapszintű keresési oldal megoldását ("1-Basic-Search-Page"), majd válassza a **Start hibakeresés nélkül** (vagy nyomja le az F5 billentyűt) a program létrehozásához és futtatásához

1. Ez egy szállodák indexe, ezért gépelje be a következőt: a szállodák keresésére használható (például "WiFi", "megtekintés", "sáv", "parkoló"), és vizsgálja meg az eredményeket.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="* WiFi * keresése" border="true":::

Remélhetőleg ez a projekt zökkenőmentesen fog futni, és a webalkalmazás fut. Ebben az alkalmazásban sok olyan alapvető összetevő található, amely kifinomultabb kereséseket tartalmaz, ezért érdemes átmenni rajta, és újra létrehozni lépésről lépésre. A következő fejezetek ezeket a lépéseket ismertetik.

## <a name="set-up-a-development-environment"></a>Fejlesztési környezet beállítása

Hozzon létre egy projektet a semmiből, és erősítse meg az Azure-Cognitive Search fogalmait az Ön szemében, és kezdjen el egy Visual Studio-projekttel.

1. A Visual Studióban válassza az **új**  >  **projekt**, majd **ASP.net Core webalkalmazás** lehetőséget.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Felhőbeli projekt létrehozása" border="true":::

1. Adjon nevet a projektnek, például: "FirstSearchApp", és állítsa be a helyet. Válassza a **Létrehozás** lehetőséget.

1. Válassza ki a **webalkalmazás (Model-View-Controller)** projekt sablonját.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="MVC-projekt létrehozása" border="true":::

1. Telepítse az ügyféloldali kódtárat. Az **eszközök**  >  **NuGet csomagkezelő**  >  **NuGet-csomagok kezelése a megoldáshoz...** területen válassza a **Tallózás** lehetőséget, majd keressen rá a "azure.search.documents" kifejezésre. Telepítse **Azure.Search.Documents** (11-es vagy újabb verzió), fogadja el a licencszerződéseket és a függőségeket.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Azure-kódtárak hozzáadása a NuGet használatával" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Azure-Cognitive Search inicializálása

Ehhez a mintához nyilvánosan elérhető szállodai adatszolgáltatásokat használunk. Ezek az adat a 50 kitalált nevek és leírások tetszőleges gyűjteménye, amely kizárólag a bemutató adatának biztosítása céljából hozható létre. Az ilyen típusú adateléréshez adjon meg egy nevet és egy API-kulcsot.

1. Nyissa meg a **appsettings.jst** , és cserélje le az alapértelmezett sorokat a keresési szolgáltatás URL-címére (formátum `https://<service-name>.search.windows.net` ) és a keresési szolgáltatás [rendszergazdai vagy lekérdezési API-kulcsára](search-security-api-keys.md) . Mivel nem kell indexet létrehoznia vagy frissítenie, használhatja az oktatóanyaghoz tartozó lekérdezési kulcsot.

    ```csharp
    {
        "SearchServiceName": "<YOUR-SEARCH-SERVICE-URI>",
        "SearchServiceQueryApiKey": "<YOUR-SEARCH-SERVICE-API-KEY>"
    }
    ```

1. A Megoldáskezelő területen válassza ki a fájlt, és a tulajdonságok területen módosítsa a **Másolás a kimeneti könyvtárba** beállítást, **Ha újabb** értéket szeretne másolni.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Az alkalmazás beállításainak másolása a kimenetre" border="true":::

## <a name="model-data-structures"></a>Adatstruktúrák modellezése

A modellek (C# osztályok) az ügyfél (a nézet), a kiszolgáló (a vezérlő) és az Azure-felhő közötti adatkommunikációra szolgálnak az MVC (modell, nézet, vezérlő) architektúra használatával. Ezek a modellek általában tükrözik az éppen elért adat szerkezetét.

Ebben a lépésben modellezi a keresési index adatstruktúráját, valamint a nézet/vezérlő kommunikációban használt keresési karakterláncot. A Hotels-indexben minden egyes Hotel több szobával rendelkezik, és minden egyes szállodában több rész is található. Összességében a Hotel teljes ábrázolása hierarchikus és beágyazott adatstruktúra. Az egyes összetevők létrehozásához három osztályra lesz szüksége.

A **Hotel**, a **címe** és a **szoba** osztályok készlete [*összetett típusként*](search-howto-complex-data-types.md)ismert, az Azure Cognitive Search fontos funkciója. Az összetett típusok számos szinten lehetnek osztályok és alosztályok, és lehetővé teszik a sokkal összetettebb adatstruktúrák megjelenítését, mint az *egyszerű típusok* (csak primitív tagokat tartalmazó osztály) használatát.

1. A Megoldáskezelőban kattintson a jobb gombbal a **modellek**  >    >  **új elem** hozzáadása lehetőségre.

1. Válassza ki az **osztályt** , és nevezze el a (z) Hotel. cs elemet. Cserélje le a Hotel. cs összes tartalmát a következő kódra. Figyelje meg, hogy az osztály **címe** és a **helyiség** tagja, ezek a mezők maguk az osztályok, ezért a modellekhez is szüksége lesz.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [SimpleField(IsFilterable = true, IsKey = true)]
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

            public Address Address { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true)]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

1. Ismételje meg ugyanezt a modellt a következő **címen** , és nevezze el a fájl címe. cs. Cserélje le a tartalmát az alábbira.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
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

1. És újra, kövesse ugyanezt a folyamatot a **Room** osztály létrehozásához, a file Room. cs nevet.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. Az oktatóanyagban létrehozandó utolsó modell egy **SearchData** nevű osztály, amely a felhasználó bemenetét (**keresettszöveg**) és a keresés kimenetét (**resultList**) jelöli. A kimenet típusa kritikus, **SearchResults &lt; &gt;**, mivel ez a típus pontosan megfelel a keresés eredményeinek, és át kell adnia ezt a hivatkozást a nézetnek. Cserélje le az alapértelmezett sablont a következő kódra.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Weblap létrehozása

A Project-sablonok a **views (nézetek** ) mappában található összes ügyfél-nézettel rendelkeznek. A pontos nézetek az Ön által használt alapvető .NET-verziótól függenek (ez a példa a 3,1-as verziót használja). Ebben az oktatóanyagban módosítani fogja az **index. cshtml** elemet a keresési oldal elemeinek belefoglalásához.

Törölje az index. cshtml tartalmát a teljes egészében, majd hozza létre újra a fájlt a következő lépésekben.

1. Az oktatóanyag két kis képet használ a nézetben: egy Azure-emblémát és egy keresési nagyító ikont (azure-logo.png és search.png). Másolja át a képeket a GitHub-projektből a projekt **wwwroot/images** mappájába.

1. Az index első sora. a cshtml hivatkozni kell arra a modellre, amelyet az ügyfél (a nézet) és a kiszolgáló (a vezérlő) közötti kommunikációhoz használtak, amely a korábban létrehozott **SearchData** -modell. Adja hozzá ezt a sort az index. cshtml fájlhoz.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. Általános gyakorlat a nézet címének megadásához, így a következő soroknak kell lenniük:

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. A címet követve adjon meg egy HTML-stíluslapra mutató hivatkozást, amelyet hamarosan létre fog hozni.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. A nézet törzse két használati esetet kezel. Először is meg kell adnia egy üres oldalt az első használatnál, mielőtt a rendszer megadta a keresett szöveget. Másodszor, a keresés szövegmezőn kívül az ismétlődő lekérdezésekhez is az eredményeket kell kezelnie.

   Mindkét eset kezeléséhez ellenőriznie kell, hogy a nézethez megadott modell null értékű-e. A Null modell az első használati esetet jelzi (az alkalmazás kezdeti futtatása). Adja hozzá a következőt az index. cshtml fájlhoz, és olvassa végig a megjegyzéseket.

    ```csharp
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. Adja hozzá a stíluslapot. A Visual Studióban, a **fájl** >  **új**  >  **fájl** területén válassza ki a **stíluslapot** ( **általános** kiemelve).

   Cserélje le az alapértelmezett kódot az alábbira. Ezt a fájlt nem fogjuk részletesebben bevezetni, a stílusok a szabványos HTML-fájlok.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

1. Mentse a stíluslapot Hotels. css néven a **wwwroot/CSS** mappába az alapértelmezett site. css fájl mellett.

Ezzel befejezte a nézetet. Ezen a ponton a modellek és a nézetek is befejeződtek. Egyszerre csak a vezérlőt kell összekötni.

## <a name="define-methods"></a>Metódusok definiálása

Ebben a lépésben módosítsa a **Kezdőlap vezérlő** tartalmát.

1. Nyissa meg a HomeController. cs fájlt, és cserélje le a **using** utasításokat a következőre.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Index metódusok hozzáadása

Egy MVC-alkalmazásban az **index ()** metódus egy alapértelmezett műveleti módszer bármely vezérlőhöz. Ekkor megnyílik az index HTML-lapja. Ebben az oktatóanyagban az alapértelmezett metódus, amely nem a paramétereket használja, az alkalmazás indítási használati esete: üres keresési oldal megjelenítése.

Ebben a szakaszban kiterjesztjük a módszert a második felhasználási eset támogatására: a lap megjelenítése, amikor egy felhasználó beírt egy keresési szöveget. Az ilyen esetek támogatásához az index metódus ki van bővítve a modell paraméterként való elvégzéséhez.

1. Adja hozzá a következő metódust az alapértelmezett **index ()** metódus után.

    ```csharp
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Figyelje meg a metódus **aszinkron** deklarációját, és **várja** meg a **RunQueryAsync**. Ezek a kulcsszavak gondoskodnak az aszinkron hívások végrehajtásáról, így elkerülhetők a kiszolgálókon lévő szálak blokkolása.

    A **Catch** blokk az alapértelmezés szerint létrehozott hibakódot használja.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Figyelje meg, hogy a hibakezelés és egyéb alapértelmezett nézetek és metódusok

Attól függően, hogy a .NET Core melyik verzióját használja, a rendszer alapértelmezés szerint az alapértelmezett nézetek némileg eltérő készletét hozza létre. A .NET Core 3,1 esetében az alapértelmezett nézetek az index, az adatvédelem és a hiba. Ezeket az alapértelmezett oldalakat megtekintheti az alkalmazás futtatásakor, és megvizsgálhatja, hogyan történjen a kezelés a vezérlőben.

Ebben az oktatóanyagban később a hiba nézetet fogja tesztelni.

A GitHub-mintában a használaton kívüli nézetek és a hozzájuk tartozó műveletek törlődnek.

### <a name="add-the-runqueryasync-method"></a>A RunQueryAsync metódus hozzáadása

Az Azure Cognitive Search hívást a **RunQueryAsync** metódusban ágyazjuk be.

1. Először adjon hozzá néhány statikus változót az Azure-szolgáltatás beállításához, valamint egy hívást a kezdeményezéshez.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. Most adja hozzá a **RunQueryAsync** metódust.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    Ebben a módszerben először ellenőrizze, hogy az Azure-konfiguráció elindult-e, majd állítson be néhány keresési lehetőséget. A **Select (kiválasztás** ) beállítással adható meg, hogy mely mezők térjenek vissza az eredményekbe, és így egyezzenek meg a **szállodai** osztály tulajdonságlapjának nevével. Ha kihagyja a **kijelölést**, a rendszer az összes nem rejtett mezőt visszaadja, ami nem hatékony, ha csak az összes lehetséges mező egy részhalmazát érdekli.

    A keresés aszinkron hívása a kérést ( **keresettszöveg**) és választ ( **searchResult**-ként modellezve) alakítja ki. Ha a kód hibakeresését végzi, a **SearchResult** osztály jó választás a Töréspont beállítására, ha meg kell vizsgálnia a **Model. resultList** tartalmát. Érdemes megkeresni, hogy a rendszer intuitív módon adja meg a kért adatmennyiséget, és nem sok más.

### <a name="test-the-app"></a>Az alkalmazás tesztelése

Most ellenőrizzük, hogy az alkalmazás megfelelően fut-e.

1. Válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget, vagy nyomja le az **F5** billentyűt. Ha az alkalmazás a várt módon fut, szerezze be a kezdeti index nézetet.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Az alkalmazás megnyitása" border="true":::

1. Adjon meg egy lekérdezési karakterláncot (például "Beach") (vagy bármilyen szöveget, amely szóba jöhet), majd kattintson a keresés ikonra a kérelem elküldéséhez.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="A * Beach * keresése" border="true":::

1. Próbálja meg beírni a "Five Star" kifejezést. Figyelje meg, hogy ez a lekérdezés nem ad vissza eredményt. A kifinomultabb keresés a "Five Star" kifejezést fogja kezelni a "luxus" szinonimája, és visszaküldi az eredményeket. A [szinonimák](search-synonyms.md) támogatása az Azure Cognitive Searchban érhető el, de ez az oktatóanyag-Sorozat nem vonatkozik rá.

1. Próbálja meg beírni a "gyors" kifejezést a keresett szövegként. _Nem_ ad vissza bejegyzéseket a (z) "Hotel" szónak. A keresés csak a teljes szavakat keresi, bár a rendszer néhány eredményt ad vissza.

1. Próbálja ki a következő szavakat: "pool", "Sunshine", "View", és bármit. Az Azure Cognitive Search a legegyszerűbb, de még mindig meggyőző szinten fog megjelenni.

## <a name="test-edge-conditions-and-errors"></a>Edge-feltételek és hibák tesztelése

Fontos ellenőrizni, hogy a hibakezelés funkciói működnek-e, még akkor is, ha a dolgok tökéletesen működnek. 

1. Az **index** metódusban, a **Try {** hívás után írja be a line **Throw New Exception ()** sort. Ez a kivétel hibát jelez a szöveg keresésekor.

2. Futtassa az alkalmazást, írja be a "sáv" kifejezést keresési szövegként, majd kattintson a keresés ikonra. A kivételnek a hiba nézetet kell eredményeznie.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Hiba kényszerítése" border="true":::

    > [!Important]
    > Biztonsági kockázatnak számít a belső hibák számának visszaküldése a hibás lapokon. Ha az alkalmazást általános használatra szánják, végezzen vizsgálatot a biztonságos és ajánlott eljárások között, amelyekkel a hiba bekövetkezésekor vissza lehet térni.

3. Távolítsa el az **új kivételt ()** , ha meggyőződött arról, hogy a hibakezelés működik.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe az alábbi elvihetőket a projektből:

* Az Azure Cognitive Search hívása tömör, és könnyen értelmezhető az eredmények.
* Az aszinkron hívások kis bonyolultságot adhatnak a vezérlőhöz, de az ajánlott eljárás, ha minőségi alkalmazásokat szeretne fejleszteni.
* Az alkalmazás egyszerű szöveges keresést hajtott végre, amelyet a **searchOptions**-ben beállított beállítások határoznak meg. Ez az osztály azonban számos olyan taggal feltölthető, amely kifinomultságot ad a kereséshez. Nem sok további munka szükséges ahhoz, hogy ez az alkalmazás lényegesen nagyobb teljesítményű legyen.

## <a name="next-steps"></a>Következő lépések

A felhasználói élmény javítása érdekében vegyen fel további funkciókat, például a lapozást (oldalszámok vagy végtelen görgetés használatával), valamint az automatikus kiegészítés/javaslatok lehetőséget. Összetettebb keresési lehetőségeket is megvizsgálhat (például a földrajzi keresést egy adott pont egy adott sugarán belül, a keresési eredmények sorrendje alapján).

A következő lépések a hátralévő oktatóanyagokban találhatók. Kezdjük a lapozással.

> [!div class="nextstepaction"]
> [C# oktatóanyag: keresési eredmények tördelése – Azure Cognitive Search](tutorial-csharp-paging.md)