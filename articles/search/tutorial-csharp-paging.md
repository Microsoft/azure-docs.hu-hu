---
title: C#-oktatóanyag a keresési eredmények tördeléséhez
titleSuffix: Azure Cognitive Search
description: A keresési eredményekhez oldalszámozási és navigációs gombokat adhat hozzá, egy meglévő Hotels-projekt alapján pedig megadhatja az első, következő, előző, utolsó és számozott gombokat. A második lapozási rendszer végtelen görgetést használ, amelyet egy függőleges görgetősávnak az alsó határértékre való áthelyezésével indít el.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: bad4bc4d0016b2898b315bfb9799dc8972be7b12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785870"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Oktatóanyag: lapozás hozzáadása a keresési eredményekhez a .NET SDK használatával

Megtudhatja, hogyan valósítható meg két különböző lapozófájl-rendszer, az első a oldalszámok alapján, a második pedig a végtelen görgetésen. A lapozás mindkét rendszerét széles körben használják, és a jobb gombbal kiválaszthatja az eredményekkel kapcsolatos felhasználói élményt. 

Az oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Az alkalmazás kiterjesztése számozott lapozással
> * Az alkalmazás kiterjesztése végtelen görgetéssel

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag egy lapozófájlt helyez át egy korábban létrehozott projektbe, amelyet az [első keresési alkalmazás létrehozása](tutorial-csharp-create-first-app.md) oktatóanyagban ismertetett.

Az oktatóanyagban fejleszteni kívánt kód befejezett verziói a következő projektekben találhatók:

* [2a – Add-lapozás (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging)

* [2b – Hozzáadás-végtelen-Scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll)

## <a name="prerequisites"></a>Előfeltételek

* [1 – Basic-Search-Page (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) projekt. A projekt az előző oktatóanyagból vagy a GitHubról származó másolatból létrehozott saját verzió lehet.

Ez az oktatóanyag a [Azure.Search.Documents (11-es verzió)](https://www.nuget.org/packages/Azure.Search.Documents/) csomag használatára lett frissítve. A .NET SDK egy korábbi verziójával kapcsolatban lásd: [Microsoft. Azure. Search (10-es verzió)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)mintakód.

## <a name="extend-your-app-with-numbered-paging"></a>Az alkalmazás kiterjesztése számozott lapozással

A számozott Lapozás a fő kereskedelmi webkeresőmotorok és számos más keresési webhely számára választható lapozófájl. A számozott lapozás általában tartalmaz egy "Next" és "Previous" beállítást is a tényleges oldalszámok tartományán kívül. Emellett az "első oldal" és az "utolsó oldal" lehetőség is elérhető lehet. Ezek a beállítások természetesen lehetővé tennék a felhasználók számára, hogy átirányítsák a navigálást az oldal alapú eredmények között.

Ebben az oktatóanyagban egy olyan rendszert fog hozzáadni, amely tartalmazza az első, az előző, a következő és az utolsó lehetőséget, valamint az oldalszámokat, amelyek nem 1-től kezdődnek, hanem a felhasználó aktuális oldalának a megkeresése (például ha a felhasználó a 10. oldalon található, a 8, 9, 10, 11 és 12 oldalszámot is megjeleníti).

A rendszer elég rugalmas lesz ahhoz, hogy a megjelenített oldalszámok számát egy globális változóban lehessen megadni.

A rendszer a bal szélső és a jobb oldalon lévő oldalszám gombokat speciálisként fogja kezelni, ami azt jelenti, hogy a megjelenített oldalszámok tartományának módosítását fogja eredményezni. Ha például a 8, 9, 10, 11 és 12 oldalszám jelenik meg, és a felhasználó a 8 értékre kattint, akkor az oldalszámok a 6, 7, 8, 9 és 10 értékre változnak. És a jobb oldali váltás hasonló, ha a 12 lehetőséget választotta.

### <a name="add-paging-fields-to-the-model"></a>Lapozási mezők hozzáadása a modellhez

Nyissa meg az alapszintű keresési oldal megoldást.

1. Nyissa meg a SearchData. cs Model fájlt.

1. Adja hozzá a globális változókat a tördelés támogatásához. Az MVC-ben a globális változók a saját statikus osztályában vannak deklarálva. A **ResultsPerPage** az eredmények számát állítja be oldalanként. A **MaxPageRange** meghatározza a nézeten látható oldalszámok számát. A **PageRangeDelta** határozza meg, hogy a bal vagy a jobb oldalon hány oldalt kell átirányítani balra vagy jobbra. Ez utóbbi szám általában a **MaxPageRange** körülbelül fele. Adja hozzá a következő kódot a névtérhez.

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Ha a projektet egy kisebb képernyővel rendelkező eszközön futtatja, például egy laptopon, akkor érdemes lehet a **ResultsPerPage** 2-ra módosítani.

1. Adja hozzá a lapozási tulajdonságokat a **SearchData** osztályhoz a **keresettszöveg** tulajdonság után.

    ```csharp
    // The current page being displayed.
    public int currentPage { get; set; }

    // The total number of pages of results.
    public int pageCount { get; set; }

    // The left-most page number to display.
    public int leftMostPage { get; set; }

    // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
    public int pageRange { get; set; }

    // Used when page numbers, or next or prev buttons, have been selected.
    public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Lapozási beállítások táblázatának hozzáadása a nézethez

1. Nyissa meg az index. cshtml fájlt, és közvetlenül a záró/Body címke előtt adja hozzá a következő kódot &lt; &gt; . Ez az új kód a lapozási lehetőségek táblázatát jeleníti meg: első, előző, 1, 2, 3, 4, 5, tovább, utolsó.

    ```html
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">|&lt;</p>
                }
            </td>

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    Egy HTML-táblázatot használunk, hogy szépen illeszkedjenek a dolgokhoz. Azonban az összes művelet az @Html.ActionLink utasításokból származik, és mindegyik a vezérlőt egy olyan **új** modellel hívja, amelyet a korábban hozzáadott **lapozófájl** -tulajdonsághoz különböző bejegyzésekkel hoztak létre.

    Az első és az utolsó oldal beállításai nem küldenek karakterláncokat (például "First" és "Last"), hanem a megfelelő oldalszámokat küldik el.

1. Adja hozzá a lapozási osztályokat a HTML-stílusok listájához a Hotels. css fájlban. Az **pageSelected** osztály az oldalszámok listájában az aktuális oldal azonosítására szolgál (félkövér formátum az oldalszámra való alkalmazásával).

    ```html
    .pageButton {
        border: none;
        color: darkblue;
        font-weight: normal;
        width: 50px;
    }

    .pageSelected {
        border: none;
        color: black;
        font-weight: bold;
        width: 50px;
    }

    .pageButtonDisabled {
        border: none;
        color: lightgray;
        font-weight: bold;
        width: 50px;
    }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Oldal művelet hozzáadása a vezérlőhöz

1. Nyissa meg a HomeController. cs fájlt, és adja hozzá a **PageAsync** műveletet. Ez a művelet a kiválasztott oldal bármelyik beállítására válaszol.

    ```csharp
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

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

            // Recover the search text and search for the data for the new page.
            model.searchText = TempData["searchfor"].ToString();

            await RunQueryAsync(model, page, leftMostPage);

            // Ensure Temp data is stored for next call, as TempData only stores for one call.
            TempData["page"] = (object)page;
            TempData["searchfor"] = model.searchText;
            TempData["leftMostPage"] = model.leftMostPage;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

    A **RunQueryAsync** metódus ekkor egy szintaktikai hibát jelenít meg a harmadik paraméter miatt, amelyet egy kicsit fogunk megtekinteni.

    > [!Note]
    > A **TempData** hívása egy érték (egy **objektum**) ideiglenes tárolóban való tárolására, bár ez a tárterület _csak_ egy hívásra marad. Ha egy ideiglenes adatmennyiséget tárolunk, akkor a rendszer a vezérlőhöz tartozó következő híváshoz lesz elérhető, de az ezt követő hívás esetén a lehető legpontosabban el fogja végezni a műveletet. Ennek a rövid élettartamnak a következtében a keresési szöveg és a lapozási tulajdonságok visszaállnak az ideiglenes tárolóba, és minden hívást **PageAsync**.

1. Frissítse az **index (modell)** műveletet az ideiglenes változók tárolására, és adja hozzá a bal szélső oldal paramétert a **RunQueryAsync** híváshoz.

    ```csharp
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
            await RunQueryAsync(model, 0, 0);

            // Ensure temporary data is stored for the next call.
            TempData["page"] = 0;
            TempData["leftMostPage"] = 0;
            TempData["searchfor"] = model.searchText;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }
        return View(model);
    }
    ```

1. Az előző leckében bemutatott **RunQueryAsync** metódus módosítást igényel a szintaktikai hiba feloldásához. A [**SearchOptions**](/dotnet/api/azure.search.documents.searchoptions) osztály **kihagyás**, **méret** és **IncludeTotalCount** mezőinek használatával csak egy oldalt kérhet le, amely a **kihagyás** beállítástól kezdődően csak egyetlen lapot kér. A nézethez a lapozási változókat is ki kell számítani. Cserélje le a teljes metódust a következő kódra.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
    {
        InitSearch();

        var options = new SearchOptions
        {
            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true
        };

        // Add fields to include in the search results.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

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

        return View("Index", model);
    }
    ```

1. Végül végezze el a nézet kis módosítását. A **resultList. Results. TotalCount** változó mostantól tartalmazza az egyik lapon visszaadott eredmények számát (a példában 3), nem az összes számot. Mivel a **IncludeTotalCount** értéke TRUE (igaz), a **resultList. TotalCount** változó már tartalmazza az eredmények teljes számát. Ezért keresse meg az eredmények számát a nézetben, és módosítsa a következő kódra.

    ```csharp
    // Show the result count.
    <p class="sampleText">
        @Model.resultList.TotalCount Results
    </p>

    var results = Model.resultList.GetResults().ToList();

    @for (var i = 0; i < results.Count; i++)
    {
        // Display the hotel name and description.
        @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
        @Html.TextArea($"desc{1}", results[i].Document.Description, new { @class = "box2" })
    }
    ```

    > [!Note]
    > A **IncludeTotalCount** True értékre állításakor kisebb teljesítmény érhető el, mivel ezt az összeget az Azure Cognitive Search alapján kell kiszámítani. Összetett adatkészletek esetén figyelmeztetést kap, hogy a visszaadott érték egy _közelítés_. Mivel a Hotel Search Corpus kicsi, pontos lesz.

### <a name="compile-and-run-the-app"></a>Az alkalmazás fordítása és futtatása

Most válassza a **Start hibakeresés nélkül** lehetőséget (vagy nyomja le az F5 billentyűt).

1. Keressen olyan karakterláncot, amely rengeteg eredményt ad vissza (például "WiFi"). Az eredményekkel könnyedén áttekintheti a lapjait?

    ![Számozott Lapozás a "pool" eredményein keresztül](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

1. Kattintson a jobb szélső, majd a bal szélső oldalszámokra. A oldalszámok megfelelően vannak módosítva a lap középpontba állításához?

1. Hasznosak az "első" és a "Last" lehetőség? Egyes kereskedelmi keresőmotorok ezeket a beállításokat használják, mások pedig nem.

1. Ugrás az eredmények utolsó oldalára. Az utolsó lap az egyetlen oldal, amely kevesebb, mint **ResultsPerPage** eredményt tartalmazhat.

    ![A "WiFi" utolsó oldalának vizsgálata](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

1. Írja be a "város" kifejezést, majd kattintson a Keresés gombra. Nem jelennek meg lapozási beállítások, ha az eredmények kevesebb, mint egy oldal.

    ![A "város" kifejezés keresése](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Mentse a projektet, és folytassa a következő szakasszal a lapozás alternatív formájaként.

## <a name="extend-your-app-with-infinite-scrolling"></a>Az alkalmazás kiterjesztése végtelen görgetéssel

A végtelen görgetés akkor aktiválódik, ha a felhasználó függőleges görgetősávot görget az utolsó megjelenített eredmények között. Ebben az esetben a keresési szolgáltatás hívása történik az eredmények következő oldalára. Ha nincs több eredmény, a rendszer semmit sem ad vissza, és a függőleges görgetősáv nem változik. Ha több találat is van, akkor a rendszer hozzáfűzi az aktuális oldalhoz, és a görgetősáv úgy módosul, hogy több találat is elérhető lesz.

Fontos megjegyezni, hogy az aktuális oldal nem lesz lecserélve, hanem a további találatok megjelenítésére is kiterjeszthető. A felhasználó bármikor görgetheti a keresést a keresés első eredményeire.

A végtelen görgetés megvalósításához kezdjük a projekttel, mielőtt az oldalszámot görgető elemek bármelyike hozzá lett adva. A GitHubon ez a [FirstAzureSearchApp](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) megoldás.

### <a name="add-paging-fields-to-the-model"></a>Lapozási mezők hozzáadása a modellhez

1. Először adjon hozzá egy **lapozási** tulajdonságot a **SearchData** osztályhoz (a SearchData. cs Model fájlban).

    ```csharp
    // Record if the next page is requested.
    public string paging { get; set; }
    ```

    Ez a változó egy karakterlánc, amely a "Next" értéket tartalmazza, ha az eredmények következő oldalát kell elküldeni, vagy null értékűnek kell lennie a keresés első oldalán.

1. Ugyanebben a fájlban és a névtéren belül adjon hozzá egy globális változó osztályt egy tulajdonsággal. Az MVC-ben a globális változók a saját statikus osztályában vannak deklarálva. A **ResultsPerPage** az eredmények számát állítja be oldalanként. 

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Függőleges görgetősáv hozzáadása a nézethez

1. Keresse meg az index. cshtml fájlnak azt a szakaszát, amely megjeleníti az eredményeket (ez a **@if következővel kezdődik: (Model! = null)**).

1. Cserélje le a szakaszt az alábbi kódra. Az új **&lt; div &gt;** szakasz az a terület körül van, amelynek görgethető kell lennie, és hozzá kell adni egy **túlfolyó-y** attribútumot, valamint egy "görgetve ()" nevű **onscroll** -függvény hívását, például:.

    ```csharp
    @if (Model != null)
    {
        // Show the result count.
        <p class="sampleText">
            @Model.resultList.TotalCount Results
        </p>

        var results = Model.resultList.GetResults().ToList();

        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

            <!-- Show the hotel data. -->
            @for (var i = 0; i < results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", results[i].Document.Description, new { @class = "box2" })
            }
    ```

1. Közvetlenül a hurok alatt, a &lt; /div &gt; címke után adja hozzá a **görgetett** függvényt.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/NextAsync", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 2) {
                        div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

    A fenti szkriptben lévő **IF** utasítás ellenőrzi, hogy a felhasználó a függőleges görgetősáv aljára görgetve van-e. Ha rendelkezik a szolgáltatással, a rendszer egy **NextAsync** nevű művelethez kezdeményezi a **Kezdőlap** vezérlőt. A vezérlő nem igényel további információkat, az adatok következő oldalát fogja visszaadni. Ezeket az adatsorokat a rendszer az eredeti lapon megegyező HTML-stílusokkal formázza. Ha a rendszer nem ad vissza eredményt, semmi sem kerül hozzáfűzésre, és a dolgok maradnak.

### <a name="handle-the-next-action"></a>A következő művelet kezelése

Csak három műveletet kell elküldeni a vezérlőnek: az alkalmazás első futtatása, amely meghívja az **indexet ()**, a felhasználó első keresését, amely meghívja az **indexet (Model)**, majd a további találatokat a **következő (modell)** használatával.

1. Nyissa meg a Kezdőlap vezérlő fájlját, és törölje a **RunQueryAsync** metódust az eredeti oktatóanyagból.

1. Cserélje le az **index (modell)** műveletet a következő kódra. Ekkor a **lapozási** mezőt a NULL értékre állítja, vagy a "Next" (tovább) értékre van állítva, és kezeli a hívást az Azure Cognitive Search.

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First call. Check for valid text input.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true
            };

            // Specify which fields to include in results.
            options.Select.Add("HotelName");
            options.Select.Add("Description");

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);               

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    A számozott lapozási módszerhez hasonlóan a **kihagyás** és a **méret** keresési beállításokat használjuk, hogy csak a szükséges adatmennyiséget adja vissza.

1. Adja hozzá a **NextAsync** műveletet a Kezdőlap vezérlőhöz. Figyelje meg, hogyan adja vissza a listát, és minden szálloda két elemet ad hozzá a listához: egy szállodai nevet és egy szállodai leírást. Ez a formátum úgy van beállítva, hogy az megfeleljen a nézet visszaadott adatának **görgetett** függvényének.

    ```csharp
    public async Task<ActionResult> NextAsync(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model).ConfigureAwait(false);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel name, then description, to the list.
        await foreach (var searchResult in model.resultList.GetResultsAsync())
        {
            nextHotels.Add(searchResult.Document.HotelName);
            nextHotels.Add(searchResult.Document.Description);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Ha szintaktikai hiba jelenik meg a **lista &lt; karakterláncában &gt;**, adja hozzá a következő **using** direktívát a vezérlő fájljának fejlécéhez.

    ```csharp
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>A projekt fordítása és futtatása

Most válassza a **Start hibakeresés nélkül** lehetőséget (vagy nyomja le az F5 billentyűt).

1. Adjon meg egy kifejezést, amely sok eredményt ad (például "pool"), majd tesztelje a függőleges görgetősávot. Elindítja az eredmények új oldalát?

    ![Végtelen görgetés a "pool" eredményei között](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Annak érdekében, hogy az első oldalon megjelenjen egy görgetősáv, az eredmények első oldalának valamivel nagyobbnak kell lennie a megjelenített terület magasságán. A példánkban a **Box1** 30 képpont magasságú **. a rajzolása 2** magassága 100 képpont, az alsó _margó pedig 24_ képpont. Így minden bejegyzés 154 képpont-t használ. Három bejegyzés 3 x 154 = 462 képpont-t vesz igénybe. Annak érdekében, hogy megjelenjen egy függőleges görgetősáv, a megjelenített területen lévő magasságot 462 képpontnál kisebb értékre kell állítani, még a 461 is. Ez a probléma csak az első oldalon fordul elő, miután a görgetősáv biztosan megjelenik. A frissítendő vonal a következő: **&lt; div id = "myDiv" style = "width: 800px; height: 450px; túlcsordulás-y: Scroll;" onscroll = "görgetett ()" &gt;**.

1. Görgessen le egészen az eredmények aljáig. Figyelje meg, hogy az összes információ most már az egyetlen nézet oldalon található. Az összes kiszolgáló hívásának elindítása nélkül görgetheti az összes utat a tetejére.

A kifinomultabb végtelen görgetési rendszerek az egér görgőjét vagy hasonló más mechanizmust használhatnak az eredmények új oldalának betöltésének elindításához. Ezekben az oktatóanyagokban nem kerül sor a végtelen görgetésre, de egy kis varázsa van rá, mivel elkerüli az egér további kattintásait, és további lehetőségeket is szeretne kivizsgálni.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe az alábbi elvihetőket a projektből:

* A számozott lapozás olyan keresésekhez hasznos, ahol az eredmények sorrendje némileg tetszőleges, ami azt jelenti, hogy a későbbi lapokon a felhasználók számára érdekes lehet.
* A végtelen görgetés akkor hasznos, ha az eredmények sorrendje különösen fontos. Például, ha az eredmények a rendeltetési város középpontjának a távolságára vannak rendezve.
* A számozott lapozás nagyobb navigálást tesz lehetővé. Egy felhasználó például megjegyezheti, hogy egy érdekes eredmény a 6. oldalon található, míg a végtelen görgetés nem tartalmaz ilyen egyszerű referenciát.
* A végtelen görgetés egyszerűen megfellebbezhető, és nem lapozható fel, és nem használható fel oldalszámok.
* A végtelen görgetés egyik kulcsfontosságú funkciója, hogy az eredmények egy meglévő lapra kerülnek, nem helyettesíti ezt az oldalt, amely hatékony.
* Az ideiglenes tároló csak egy hívást tart fenn, és vissza kell állítania a további hívások túléléséhez.

## <a name="next-steps"></a>Következő lépések

A lapozás alapvető fontosságú a keresési élményhez. A lapozással jól érintett, a következő lépés a felhasználói élmény további javítása a Type-Ahead típusú keresések hozzáadásával.

> [!div class="nextstepaction"]
> [C# oktatóanyag: az autocomplete és a javaslatok hozzáadása – Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)