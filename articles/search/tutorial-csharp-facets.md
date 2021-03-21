---
title: C# oktatóanyag a dimenziók használatának támogatásához a navigáláshoz
titleSuffix: Azure Cognitive Search
description: A "lapozási eredmények" lehetőséggel bővítheti a sokoldalú navigálást. Ebből a témakörből megtudhatja, hogyan használhatók a keresések egyszerű szűkítéséhez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 44a05ed5b4386f2787a1c84dfeb61a5d539c7cb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789803"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Oktatóanyag: sokoldalú Navigálás hozzáadása a .NET SDK használatával

A dimenziók lehetővé teszik a saját irányítású navigálást az eredmények szűrésére szolgáló hivatkozások megadásával. Ebben az oktatóanyagban egy sokoldalú navigációs struktúra kerül a lap bal oldalára, és a feliratokkal és a szöveggel az eredmények kivágására is lehetőség van.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Modell tulajdonságainak beállítása _IsFacetable_
> * Face-Navigálás hozzáadása az alkalmazáshoz

## <a name="overview"></a>Áttekintés

A dimenziók a keresési index mezőin alapulnak. A facet = [string] karakterláncot tartalmazó lekérdezési kérelem a következőt adja meg a dimenziónak:. Gyakori, hogy több aspektust is magában foglal, például az `&facet=category&facet=amenities` egyeseket, amelyeket egy jellel (&) kell elválasztani. A sokoldalú navigációs struktúra megvalósításához meg kell adnia mindkét aspektust és szűrőt. A szűrőt egy Click eseményen kell használni, hogy Szűkítse az eredményeket. A "költségvetés" lehetőségre kattintva például a feltételek alapján szűri az eredményeket.

Ez az oktatóanyag kiterjeszti a lapozófájl hozzáadása a [keresési eredményekhez](tutorial-csharp-paging.md) című oktatóanyagban létrehozott lapozófájl-projektet.

Az oktatóanyagban szereplő kód befejezett verziója a következő projektben található:

* [4 – Hozzáadás – dimenzió – navigáció (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>Előfeltételek

* [2a – Add-személyhívó (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) megoldás. A projekt az előző oktatóanyagból vagy a GitHubról származó másolatból létrehozott saját verzió lehet.

Ez az oktatóanyag a [Azure.Search.Documents (11-es verzió)](https://www.nuget.org/packages/Azure.Search.Documents/) csomag használatára lett frissítve. A .NET SDK egy korábbi verziójával kapcsolatban lásd: [Microsoft. Azure. Search (10-es verzió)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)mintakód.

## <a name="set-model-properties-as-isfacetable"></a>Modell tulajdonságainak beállítása IsFacetable

Ahhoz, hogy egy Model tulajdonságot a faced keresési szolgáltatásban lehessen elhelyezni, a címkének **IsFacetable** kell lennie.

1. Vizsgálja meg a **szállodai** osztályt. A **Kategóriák** és **címkék** például **IsFacetable** vannak megjelölve, de a **pezsgő** és a **Leírás** nem. 

    ```cs
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
    ```

1. Az oktatóanyag részeként nem módosítunk címkéket, ezért a rendszer nem módosítja a (z). cs fájlt.

    > [!Note]
    > A dimenziós keresés hibát jelez, ha a keresésben kért mező nincs megfelelően címkézve.

## <a name="add-facet-navigation-to-your-app"></a>Face-Navigálás hozzáadása az alkalmazáshoz

Ebben a példában engedélyezzük a felhasználó számára, hogy kiválassza az eredmények közül az egyik kategóriát, vagy egy rekreációs elemet. A felhasználó egy adott keresési szöveg beírásával indul el, majd fokozatosan leszűkíti a keresés eredményét egy kategória vagy egy élvezet kiválasztásával.

A vezérlő feladata, hogy átadja az aspektusok listáját a nézetnek. Ha a keresési folyamat során meg szeretné őrizni a felhasználói beállításokat, a rendszer ideiglenes tárterületet használ az állapot megőrzése érdekében.

![A "pool" keresésének szűkítése a facet navigációs használatával](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Szűrő sztringek hozzáadása a SearchData-modellhez

1. Nyissa meg a SearchData. cs fájlt, és adja hozzá a **SearchData** osztályhoz a karakterlánc-tulajdonságokat, hogy az a Dimenzióérték-szűrő sztringeket tartsa.

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>A dimenzió műveleti módszer hozzáadása

A Kezdőlap vezérlőnek egy új műveletre, **aspektusra** és frissítésre van szüksége a meglévő **index** és **Page** műveletekhez, valamint a **RunQueryAsync** metódushoz.

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. Cserélje le az **index (SearchData Model)** műveleti metódusát.

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. Cserélje le a **PageAsync (SearchData Model)** műveleti metódust.

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            // Calculate the page that should be displayed.
            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the filters.
            string catFilter = TempData["categoryFilter"].ToString();
            string ameFilter = TempData["amenityFilter"].ToString();

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Search for the new page.
            await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

1. Vegyen fel egy **FacetAsync (SearchData Model)** műveleti módszert, amely akkor aktiválódik, amikor a felhasználó rákattint egy aspektusra mutató hivatkozásra. A modell a kategória-vagy a rekreációs keresési szűrőt fogja tartalmazni. Adja hozzá a **PageAsync** művelet után.

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
    {
        try
        {
            // Filters set by the model override those stored in temporary data.
            string catFilter;
            string ameFilter;
            if (model.categoryFilter != null)
            {
                catFilter = model.categoryFilter;
            } else
            {
                catFilter = TempData["categoryFilter"].ToString();
            }

            if (model.amenityFilter != null)
            {
                ameFilter = model.amenityFilter;
            } else
            {
                ameFilter = TempData["amenityFilter"].ToString();
            }

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Initiate a new search.
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }

        return View("Index", model);
    }
    ```

### <a name="set-up-the-search-filter"></a>A keresési szűrő beállítása

Ha a felhasználó egy adott dimenziót választ ki, például az **üdülő és a fürdő** kategóriára kattint, akkor csak az ebben a kategóriában megadott szállodák lesznek visszaadva az eredmények között. Ha így szeretne szűkíteni egy keresést, be kell állítania egy _szűrőt_.

1. Cserélje le a **RunQueryAsync** metódust a következő kódra. Elsődlegesen egy kategória-szűrő sztringet és egy rekreációs szűrő sztringet vesz igénybe, és beállítja a **SearchOptions** **Filter** paraméterét.

    ```cs
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
    {
        InitSearch();

        string facetFilter = "";

        if (catFilter.Length > 0 && ameFilter.Length > 0)
        {
            // Both facets apply.
            facetFilter = $"{catFilter} and {ameFilter}"; 
        } else
        {
            // One, or zero, facets apply.
            facetFilter = $"{catFilter}{ameFilter}";
        }

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        // Ensure Temp data is stored for the next call.
        TempData["page"] = page;
        TempData["leftMostPage"] = model.leftMostPage;
        TempData["searchfor"] = model.searchText;
        TempData["categoryFilter"] = catFilter;
        TempData["amenityFilter"] = ameFilter;

        // Return the new view.
        return View("Index", model);
    }
    ```

    Figyelje meg, hogy a **Kategória** és a **címkék** tulajdonságok hozzáadódnak a **kijelölni** kívánt elemek listájához. Ehhez a feltételhez nem szükséges, hogy a Face navigáció működjön, de ezeket az információkat a szűrők helyes működésének ellenőrzéséhez használjuk.

### <a name="add-lists-of-facet-links-to-the-view"></a>A nézethez tartozó aspektusi hivatkozások listáját adja hozzá

A nézet jelentős változásokat fog igényelni. 

1. Először nyissa meg a Hotels. css fájlt (a WWWroot/CSS mappában), és adja hozzá a következő osztályokat.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

1. A nézet megjelenítéséhez rendezze a kimenetet egy táblázatba, hogy a bal oldali dimenziók listáit szépen igazítsa, a jobb oldalon pedig az eredményeket. Nyissa meg az index. cshtml fájlt. Cserélje le a HTML törzs címkék teljes tartalmát a &lt; &gt; következő kódra.

    ```html
    <body>
        @using (Html.BeginForm("Index", "Home", FormMethod.Post))
        {
            <table>
                <tr>
                    <td></td>
                    <td>
                        <h1 class="sampleTitle">
                            <img src="~/images/azure-logo.png" width="80" />
                            Hotels Search - Facet Navigation
                        </h1>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td>
                        <!-- Display the search text box, with the search icon to the right of it.-->
                        <div class="searchBoxForm">
                            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                        </div>
                    </td>
                </tr>

                <tr>
                    <td valign="top">
                        <div id="facetplace" class="facetchecks">

                            @if (Model != null && Model.resultList != null)
                            {
                                List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                                if (categories.Count > 0)
                                {
                                    <h5 class="facetheader">Category:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < categories.Count; c++)
                                        {
                                            var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                            </li>
                                        }
                                    </ul>
                                }

                                List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                                if (tags.Count > 0)
                                {
                                    <h5 class="facetheader">Amenities:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < tags.Count; c++)
                                        {
                                            var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                            </li>
                                        }
                                    </ul>
                                }
                            }
                        </div>
                    </td>
                    <td valign="top">
                        <div id="resultsplace">
                            @if (Model != null && Model.resultList != null)
                            {
                                // Show the result count.
                                <p class="sampleText">
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                                    @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                                }
                            }
                        </div>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td valign="top">
                        @if (Model != null && Model.pageCount > 1)
                        {
                            // If there is more than one page of results, show the paging buttons.
                            <table>
                                <tr>
                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">|&lt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&lt;</p>
                                        }
                                    </td>

                                    @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                    {
                                        <td class="tdPage">
                                            @if (Model.currentPage == pn)
                                            {
                                                // Convert displayed page numbers to 1-based and not 0-based.
                                                <p class="pageSelected">@(pn + 1)</p>
                                            }
                                            else
                                            {
                                                <p class="pageButton">
                                                    @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                                                </p>
                                            }
                                        </td>
                                    }

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;|</p>
                                        }
                                    </td>
                                </tr>
                            </table>
                        }
                    </td>
                </tr>
            </table>
        }
    </body>
    ```

    Figyelje meg a **HTML. ActionLink** hívás használatát. Ez a hívás érvényes szűrési karakterláncokat kommunikál a vezérlővel, amikor a felhasználó egy aspektus hivatkozásra kattint. 

### <a name="run-and-test-the-app"></a>Az alkalmazás futtatása és tesztelése

A felhasználó aspektusának az az előnye, hogy egyetlen kattintással szűkítheti a keresést, amelyet a következő sorozatban megjeleníthet.

1. Futtassa az alkalmazást, és írja be a "repülőtér" kifejezést a keresett szövegként. Győződjön meg arról, hogy a dimenziók listája a bal oldalon látható. Ezek az aspektusok mind azokra a szállodákra vonatkoznak, amelyekben szerepelnek a "Airport" szövegük a szöveges adattárban.

    ![A "repülőtér" keresésének szűkítése a Face navigációs használatával](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

1. Kattintson az **üdülő és a fürdő** kategóriára. Ellenőrizze, hogy az összes eredmény ebben a kategóriában van-e.

    ![A keresés szűkítése a "Resort and Spa" kifejezésre](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. Kattintson a **kontinentális reggeli** élvezete lehetőségre. Ellenőrizze, hogy az összes eredmény továbbra is az "üdülőhely és Spa" kategóriában van-e, a kiválasztott rekreációval együtt.

    ![A keresés szűkítése a "kontinentális reggeli" kifejezésre](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. Próbáljon meg más kategóriát kijelölni, majd egy élvezetet, és tekintse meg a szűkített eredményeket. Ezután próbálkozzon a másik megoldással, egy kivezetővel, majd egy kategóriával. Üres keresés küldése az oldal alaphelyzetbe állításához.

    >[!Note]
    > Ha egy dimenziós listában (például kategória) végez egy kijelölést, akkor a rendszer felülírja az összes korábbi kijelölést a Kategória listában.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe az alábbi elvihetőket a projektből:

* Fontos, hogy az egyes dimenziós mezőket a **IsFacetable** tulajdonsággal kell megjelölni a facet navigationbe való felvételhez.
* Az aspektusokat szűrőkkel kombinálva csökkentheti az eredményeket.
* A dimenziók összegző jellegűek, és az előzőekhez tartozó egyes kiválasztások tovább szűkítik az eredményeket.

## <a name="next-steps"></a>Következő lépések

A következő oktatóanyagban megtekintjük az eredmények sorrendjét. Ezen a ponton az eredmények egyszerűen sorrendbe rendezhetők az adatbázisban található sorrendben.

> [!div class="nextstepaction"]
> [C# oktatóanyag: az eredmények rendezése – Azure Cognitive Search](tutorial-csharp-orders.md)
