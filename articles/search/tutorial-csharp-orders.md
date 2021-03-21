---
title: C#-oktatóanyag az eredmények megrendeléséhez
titleSuffix: Azure Cognitive Search
description: Ez a C#-oktatóanyag azt mutatja be, hogyan lehet a keresési eredményeket megrendelni. A szolgáltatás egy korábbi, az elsődleges tulajdonság, a másodlagos tulajdonság rendezése, és egy pontozási profilt tartalmaz, amely növeli a feltételt.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1f8100dd6340383eadec5d10b7f23db59ba0ebdf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786385"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Oktatóanyag: keresési eredmények sorrendbe helyezése a .NET SDK használatával

Ebben az oktatóanyag-sorozatban az eredmények visszaadva lettek, és [alapértelmezett sorrendben](index-add-scoring-profiles.md#what-is-default-scoring)jelennek meg. Ebben az oktatóanyagban elsődleges és másodlagos rendezési feltételeket fog felvenni. A numerikus értékek alapján történő rendezés alternatívájaként az utolsó példa azt szemlélteti, hogyan rangsorolhatja az eredményeket egy egyéni pontozási profil alapján. Az _összetett típusok_ megjelenítését is mélyebben fogjuk bemutatni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Eredmények megrendelése egy tulajdonság alapján
> * Eredmények megrendelése több tulajdonság alapján
> * Eredmények megrendelése pontozási profil alapján

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag kiterjeszti a [lapozás hozzáadása a keresési eredményekhez](tutorial-csharp-paging.md) című oktatóanyagban létrehozott végtelen görgetési projektet.

Az oktatóanyagban szereplő kód befejezett verziója a következő projektben található:

* [5 – Order-Results (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Előfeltételek

* [2b – Add-végtelen-Scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll) megoldás. A projekt az előző oktatóanyagból vagy a GitHubról származó másolatból létrehozott saját verzió lehet.

Ez az oktatóanyag a [Azure.Search.Documents (11-es verzió)](https://www.nuget.org/packages/Azure.Search.Documents/) csomag használatára lett frissítve. A .NET SDK egy korábbi verziójával kapcsolatban lásd: [Microsoft. Azure. Search (10-es verzió)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)mintakód.

## <a name="order-results-based-on-one-property"></a>Eredmények megrendelése egy tulajdonság alapján

Ha egy tulajdonság alapján rendezi az eredményeket, például a szállodai minősítést, nem csupán a megrendelt eredményeket akarjuk megerősíteni, akkor azt is szeretnénk, hogy a megrendelés helyes legyen. A minősítési mező az eredményekhez való hozzáadása lehetővé teszi, hogy az eredmények megfelelően rendezve legyenek.

Ebben a gyakorlatban még egy kicsit többet is megadunk az eredmények megjelenítéséhez: a legolcsóbb szobaár, valamint a legdrágább szobaár az egyes hotelekhez.

Nem szükséges módosítani a modelleket a rendezés engedélyezéséhez. Csak a nézet és a vezérlő frissítései szükségesek. Először nyissa meg a Kezdőlap vezérlőt.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Adja hozzá a OrderBy tulajdonságot a keresési paraméterekhez

1. A HomeController. cs-ban adja hozzá a **OrderBy** lehetőséget, és foglalja bele a minősítés tulajdonságot csökkenő rendezési sorrendtel. Az **index (SearchData Model)** metódusban adja hozzá a következő sort a keresési paraméterekhez.

    ```cs
    options.OrderBy.Add("Rating desc");
    ```

    >[!Note]
    > Az alapértelmezett sorrend növekvő, de `asc` a tulajdonsághoz hozzáadhatja ezt a beállítást. A csökkenő sorrendet a hozzáadásával határozhatja meg `desc` .

1. Most futtassa az alkalmazást, és adja meg a gyakori keresési kifejezéseket. Előfordulhat, hogy az eredmények nem a megfelelő sorrendben jelennek meg, mert nem a fejlesztő, hanem nem a felhasználó, egyszerűen ellenőrizheti az eredményeket!

1. Tegyük fel, hogy egyértelművé teszi, hogy az eredmények a minősítés alapján vannak rendezve. Először cserélje le a **Box1** és a **rajzolása 2** osztályt a Hotels. CSS fájlra a következő osztályokkal (ezek az osztályok az oktatóanyaghoz szükséges összes újdonság).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > A böngészők általában a CSS-fájlokat gyorsítótárazzák, és ez egy régi CSS-fájl használatát eredményezi, és a módosítások figyelmen kívül lesznek hagyva. Ez egy jó módja annak, hogy egy lekérdezési karakterláncot adjon hozzá egy Version paraméterrel a hivatkozáshoz. Például:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Frissítse a verziószámot, ha úgy gondolja, hogy a böngésző egy régi CSS-fájlt használ.

1. Adja hozzá a **minősítés** tulajdonságot a **Select** paraméterhez az **index (SearchData Model)** metódusban, hogy az eredmények a következő három mezőt tartalmazzák:

    ```cs
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Rating");
    ```

1. Nyissa meg a nézetet (index. cshtml), és cserélje le a renderelési ciklust (**&lt; !--jelenítse meg a szállodai adatkészletet.-- &gt;**) a következő kóddal.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var ratingText = $"Rating: {result[i].Document.Rating}";

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. A minősítésnek mind az első megjelenített oldalon, mind a további, a végtelen görgetéssel meghívott lapokon elérhetőnek kell lennie. Az utóbbi két helyzetben frissítenie kell a vezérlő **következő** műveletét és a nézet **görgetett** függvényét. A vezérlőtől kezdődően módosítsa a **következő** metódust a következő kódra. Ez a kód létrehozza és közli a minősítési szöveget.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
    
            string fullDescription = result.Document.Description;
    
            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Most frissítse a nézet **görgetett** függvényét a minősítés szövegének megjelenítéséhez.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 3) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Most futtassa újra az alkalmazást. Keressen az általános kifejezésre, például a "WiFi" kifejezésre, és győződjön meg róla, hogy az eredmények sorrendje a Hotel minősítésének csökkenő sorrendje.

    ![Megrendelés minősítés alapján](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Megfigyelheti, hogy több szálláshely azonos minősítéssel rendelkezik, és így a megjelenésük a képernyőn megjelenő sorrendben is megkezdődik, ami tetszőleges.

    Mielőtt megnézzük a második szintű megrendelés hozzáadását, vegyünk fel egy kódot a szobaárak megjelenítéséhez. Ezt a kódot úgy vesszük fel, hogy az adatok kinyerését _összetett típusból_ is megmutassa, és így a díjszabás alapján is megbeszéljük a sorrendet (az első legolcsóbbat).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Adja hozzá a szobaárak tartományát a nézethez

1. Adja hozzá a legolcsóbb és legdrágább szobaárat tartalmazó tulajdonságokat a Hotel. cs modellhez.

    ```cs
    // Room rate range
    public double cheapest { get; set; }
    public double expensive { get; set; }
    ```

1. Az **index (SearchData Model)** művelet végén lévő szobaárak kiszámításához a Kezdőlap vezérlőben. Az ideiglenes adattárolást követően adja hozzá a számításokat.

    ```cs
    // Ensure TempData is stored for the next call.
    TempData["page"] = page;
    TempData["searchfor"] = model.searchText;

    // Calculate the room rate ranges.
    await foreach (var result in model.resultList.GetResultsAsync())
    {
        var cheapest = 0d;
        var expensive = 0d;

        foreach (var room in result.Document.Rooms)
        {
            var rate = room.BaseRate;
            if (rate < cheapest || cheapest == 0)
            {
                cheapest = (double)rate;
            }
            if (rate > expensive)
            {
                expensive = (double)rate;
            }
        }
        model.resultList.Results[n].Document.cheapest = cheapest;
        model.resultList.Results[n].Document.expensive = expensive;
    }
    ```

1. Adja hozzá a **Rooms** tulajdonságot a vezérlő **index (SearchData modell)** műveleti metódusának **Select** paraméteréhez.

    ```cs
    options.Select.Add("Rooms");
    ```

1. Módosítsa a renderelési ciklust a nézetben az eredmények első oldalának díjszabási tartományának megjelenítéséhez.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Változtassa meg a **következő** metódust a Kezdőlap vezérlőn, hogy az eredmény további oldalain kommunikáljon a ráta tartományával.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";

            string fullDescription = result.Document.Description;

            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(rateText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Frissítse a **görgethető** függvényt a nézetben a szobaárak szövegének kezeléséhez.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 4) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Futtassa az alkalmazást, és ellenőrizze, hogy megjelenik-e a szobaárak tartománya.

    ![A szobák díjszabási tartományának megjelenítése](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

A keresési paraméterek **OrderBy** tulajdonsága nem fogad el olyan bejegyzést, mint például a **Rooms. BaseRate** , hogy a legolcsóbb szobaárat adja meg, még akkor is, ha a szobák már a díjszabás szerint vannak rendezve. Ebben az esetben a szobák nem a díjszabás szerint vannak rendezve. Ahhoz, hogy megjelenjenek a megadott adatkészletben található szállodák a szobaár alapján rendezve, rendeznie kell az eredményeket a saját vezérlőben, majd az eredményeket a kívánt sorrendben kell elküldeni a nézetnek.

## <a name="order-results-based-on-multiple-values"></a>Eredmények megrendelése több érték alapján

A kérdés most az, hogy hogyan lehet különbséget tenni a szállodák között ugyanazzal a minősítéssel. Az egyik megközelítés lehet egy másodlagos megrendelés, amely a szálloda legutóbbi felújításának időpontját képezi, így a közelmúltban felújított szállodák magasabban jelennek meg az eredményekben.

1. Második szintű rendezés hozzáadásához vegyen fel **"lastrenovationdate** a keresési eredményekbe, és a **OrderBy** az **index (SearchData Model)** metódusba.

    ```cs
    options.Select.Add("LastRenovationDate");

    options.OrderBy.Add("LastRenovationDate desc");
    ```

    >[!Tip]
    > A **OrderBy** listájában tetszőleges számú tulajdonság adható meg. Ha a hotelek azonos minősítési és felújítási dátummal rendelkeztek, egy harmadik tulajdonságot is meg lehet adni a közöttük való különbségtételhez.

1. Újra meg kell látni a felújítási dátumot a nézetben, csak azért, hogy az adott megrendelés helyes legyen. Olyan dolgok esetében, mint a felújítás, valószínűleg csak az év elegendő. Módosítsa a megjelenítési hurkot a nézetben a következő kódra.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var lastRenovatedText = $"Last renovated: { result[i].Document.LastRenovationDate.Value.Year}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Változtassa meg a **következő** metódust a fővezérlőn a legutóbbi felújítás év összetevőjének továbbításához.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var ratingText = $"Rating: {result.Document.Rating}";
                var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
                var lastRenovatedText = $"Last renovated: {result.Document.LastRenovationDate.Value.Year}";

                string fullDescription = result.Document.Description;

                // Add strings to the list.
                nextHotels.Add(result.Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(fullDescription);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. A felújítási szöveg megjelenítéséhez módosítsa a nézet **görgetett** függvényét.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 5) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Futtassa az alkalmazást. Keressen egy általános kifejezésre, például a "pool" vagy a "View" kifejezésre, és ellenőrizze, hogy az azonos minősítéssel rendelkező szállodák mostantól csökkenő sorrendben jelennek-e meg a felújítás dátuma alapján.

    ![Megrendelés a felújítás napján](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="order-results-based-on-a-scoring-profile"></a>Eredmények megrendelése pontozási profil alapján

Az oktatóanyagban szereplő példák azt mutatják be, hogyan kell a numerikus értékeket (a minősítési és a felújítási dátumot) sorrendbe rendezni, ami _pontos_ sorrendet biztosít a rendezéshez. Egyes keresések és egyes adatértékek azonban nem alkalmasak arra, hogy egy ilyen egyszerű összehasonlítást végezzenek két adatelem között. A teljes szöveges keresési lekérdezések esetében a Cognitive Search a _rangsorolás_ fogalmát is tartalmazza. A _pontozási profilok_ megadhatók az eredmények rangsorolásának befolyásolásához, összetettebb és minőségi összehasonlítások biztosításához.

[Pontozási profilok](index-add-scoring-profiles.md) definiálva vannak az index sémában. Több pontozási profil is be lett állítva a szállodákban tárolt értékekre. Nézzük meg, hogyan határozzák meg a pontozási profilt, majd próbáljon meg kódot írni a kereséshez.

### <a name="how-scoring-profiles-are-defined"></a>Pontozási profilok meghatározása

A pontozási profilok a tervezési időszakban a keresési indexben vannak meghatározva. A Microsoft által üzemeltetett írásvédett szállodák indexe három pontozási profilt tartalmaz. Ez a szakasz a pontozási profilokat vizsgálja, és bemutatja, hogyan használhatja a kódot.

1. Az alábbiakban látható a Hotels-adatkészlethez tartozó alapértelmezett pontozási profil, amelyet akkor kell használni, ha nem ad meg **OrderBy** vagy **ScoringProfile** paramétert. Ez a profil növeli a _pontszámot_ egy adott szállodában, ha a keresett szöveg szerepel a Hotel neve, leírása vagy címkék listájában (kényelmi szolgáltatás). Figyelje meg, hogy a pontozás súlyozása bizonyos mezőknél. Ha a keresett szöveg egy másik mezőben jelenik meg, amely nem szerepel az alábbi listában, akkor az 1. súlyozással fog rendelkezni. Nyilvánvaló, hogy minél magasabb a pontszám, annál nagyobb eredmény jelenik meg a nézetben.

     ```cs
    {
        "name": "boostByField",
        "text": {
            "weights": {
                "Tags": 3,
                "HotelName": 2,
                "Description": 1.5,
                "Description_fr": 1.5,
            }
        }
    }
    ```

1. A következő alternatív pontozási profil jelentős mértékben növeli a pontszámot, ha egy megadott paraméter egy vagy több címkét tartalmaz (amelyek "kényelmi"). A profil legfontosabb pontja, hogy _egy paramétert kell megadni_ , amely szöveget tartalmaz. Ha a paraméter üres vagy nincs megadva, a rendszer hibát jelez.

    ```cs
    {
        "name":"boostAmenities",
        "functions":[
            {
            "fieldName":"Tags",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":null,
            "distance":null,
            "tag":{
                "tagsParameter":"amenities"
            },
            "type":"tag",
            "boost":5
            }
        ],
        "functionAggregation":0
    },
    ```

1. Ebben a harmadik profilban a szállodai minősítés jelentős lökést ad a pontszámnak. A legutóbb felújított dátum is fellendíti a pontszámot, de csak akkor, ha az adatok az aktuális dátum 730 napon (2 év) belül esik.

    ```cs
    {
        "name":"renovatedAndHighlyRated",
        "functions":[
            {
            "fieldName":"Rating",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":{
                "boostingRangeStart":0,
                "boostingRangeEnd":5,
                "constantBoostBeyondRange":false
            },
            "distance":null,
            "tag":null,
            "type":"magnitude",
            "boost":20
            },
            {
            "fieldName":"LastRenovationDate",
            "freshness":{
                "boostingDuration":"P730D"
            },
            "interpolation":"quadratic",
            "magnitude":null,
            "distance":null,
            "tag":null,
            "type":"freshness",
            "boost":10
            }
        ],
        "functionAggregation":0
    }
    ```

    Most nézzük meg, hogy ezek a profilok működnek-e.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Kód hozzáadása a nézethez a profilok összehasonlításához

1. Nyissa meg az index. cshtml fájlt, és cserélje le a &lt; Body (törzs) &gt; szakaszt a következő kódra.

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
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. Nyissa meg a SearchData. cs fájlt, és cserélje le a **SearchData** osztályt a következő kódra.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. Nyissa meg a Hotels. css fájlt, és adja hozzá a következő HTML-osztályokat.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Kód hozzáadása a vezérlőhöz pontozási Profil megadásához

1. Nyissa meg a Kezdőlap vezérlő fájlját. Adja hozzá a következő **using** utasítást (a listák létrehozásához nyújtandó támogatáshoz).

    ```cs
    using System.Linq;
    ```

1. Ebben a példában az **indexhez** a kezdeti hívást egy kicsit nagyobbra kell tenni, mint a kezdeti nézet visszaadása. A metódus most legfeljebb 20 kényelmi lehetőséget keres a nézetben való megjelenítéshez.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchOptions options = new SearchOptions();
            // Search for up to 20 amenities.
            options.Facets.Add("Tags,count:20");

            SearchResults<Hotel> searchResult = await _searchClient.SearchAsync<Hotel>("*", options);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. Két privát módszerre van szükségünk az aspektusok ideiglenes tárhelyre való mentéséhez, valamint az ideiglenes tárolóból történő helyreállításához és a modell feltöltéséhez.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. Szükség szerint be kell állítania a **OrderBy** és a **ScoringProfile** paramétereket. Cserélje le a meglévő **index (SearchData Model)** metódust a következőre.

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Recover the facet text, and the facet check box settings.
                RecoverFacets(model, true);

                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First search with text. 
                // Recover the facet text, but ignore the check box settings, and use the current model settings.
                RecoverFacets(model, false);

                // First call. Check for valid text input, and valid scoring profile.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                if (model.scoring == null)
                {
                    model.scoring = "Default";
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
                IncludeTotalCount = true,
            };
            // Select the data properties to be returned.
            options.Select.Add("HotelName");
            options.Select.Add("Description");
            options.Select.Add("Tags");
            options.Select.Add("Rooms");
            options.Select.Add("Rating");
            options.Select.Add("LastRenovationDate");

            List<string> parameters = new List<string>();
            // Set the ordering based on the user's radio button selection.
            switch (model.scoring)
            {
                case "RatingRenovation":
                    // Set the ordering/scoring parameters.
                    options.OrderBy.Add("Rating desc");
                    options.OrderBy.Add("LastRenovationDate desc");
                    break;

                case "boostAmenities":
                    {
                        options.ScoringProfile = model.scoring;

                        // Create a string list of amenities that have been clicked.
                        for (int a = 0; a < model.facetOn.Length; a++)
                        {
                            if (model.facetOn[a])
                            {
                                parameters.Add(model.facetText[a]);
                            }
                        }

                        if (parameters.Count > 0)
                        {
                            options.ScoringParameters.Add($"amenities-{ string.Join(',', parameters)}");
                        }
                        else
                        {
                            // No amenities selected, so set profile back to default.
                            options.ScoringProfile = "";
                        }
                    }
                    break;

                case "renovatedAndHighlyRated":
                    options.ScoringProfile = model.scoring;
                    break;

                default:
                    break;
            }

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options);

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
            TempData["scoring"] = model.scoring;
            SaveFacets(model, true);

            // Calculate the room rate ranges.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var cheapest = 0d;
                var expensive = 0d;

                foreach (var room in result.Document.Rooms)
                {
                    var rate = room.BaseRate;
                    if (rate < cheapest || cheapest == 0)
                    {
                        cheapest = (double)rate;
                    }
                    if (rate > expensive)
                    {
                        expensive = (double)rate;
                    }
                }

                result.Document.cheapest = cheapest;
                result.Document.expensive = expensive;
            }
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Olvassa végig az egyes **kapcsolók** kiválasztásának megjegyzéseit.

1. A **következő** művelet módosítása nem szükséges, ha az előző szakaszhoz tartozó további kódokat több tulajdonság alapján hajtotta végre.

### <a name="run-and-test-the-app"></a>Az alkalmazás futtatása és tesztelése

1. Futtassa az alkalmazást. A nézetben a szolgáltatások teljes készletét kell megtekinteni.

1. A "numerikus minősítéssel" lehetőség kiválasztásával megadhatja az ebben az oktatóanyagban már megvalósított numerikus sorrendet, a felújítási dátumot pedig az egyenlő minősítésű szállodák között kell eldöntenie.

   ![A "Beach" rendezése minősítés alapján](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Most próbálja ki a "by kényelmi" profilt. Tegye elérhetővé a különböző kényelmi szolgáltatásokat, és ellenőrizze, hogy az adott szolgáltatásokkal rendelkező szállodák támogatják-e az eredmények listáját.

   ![A "Beach" rendezése profil alapján](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Próbálja ki a "megújított dátum/minősítési profil" lehetőséget, hogy megtekintse, mire számíthat. Csak a közelmúltban felújított szállodáknak kell megszerezniük a _frissesség_ növelését.

### <a name="resources"></a>Erőforrások

További információt az alábbi [pontozási profilok hozzáadása Azure Cognitive Search indexhez](./index-add-scoring-profiles.md)című témakörben talál.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe az alábbi elvihetőket a projektből:

* A felhasználók a keresési eredmények sorrendjét fogják várni, ami a legfontosabb.
* Az adatigényt strukturálni kell, hogy a rendezés egyszerűen megtörténjen. Először nem tudtuk rendezni a "legolcsóbb" kifejezést, mert az adathalmaz nem strukturált, hogy a megrendelés további kód nélkül legyen végrehajtva.
* A rendezés számos szinten elvégezhető, így megkülönböztethető az olyan eredmények között, amelyek értéke magasabb a megrendelésnél.
* Természetes, hogy egyes eredmények növekvő sorrendben legyenek rendezve (azaz távolság egy ponttól), és néhány csökkenő sorrendben (azaz a vendég minősítése).
* A pontozási profilok meghatározhatók, ha a numerikus összehasonlítások nem érhetők el, vagy nem elég intelligensek egy adatkészlethez. Az egyes eredmények pontozásával intelligens módon rendezheti és jelenítheti meg az eredményeket.

## <a name="next-steps"></a>Következő lépések

Elvégezte ezt a C#-oktatóanyagokat – értékes ismereteket szerzett az Azure Cognitive Search API-król.

További tudnivalókat és oktatóanyagokat az [Azure Cognitive Search dokumentációjában találhat](./index.yml) [Microsoft Learn](/learn/browse/?products=azure)vagy más oktatóanyagokat.