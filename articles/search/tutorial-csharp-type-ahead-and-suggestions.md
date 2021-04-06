---
title: C#-oktatóanyag az automatikus kiegészítésről és a javaslatokról
titleSuffix: Azure Cognitive Search
description: Automatikus kiegészítés és javaslatok hozzáadása a keresési kifejezéseknek a felhasználóktól a legördülő listából való összegyűjtéséhez. Ez az oktatóanyag egy meglévő Hotels-projektre épül.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/22/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 06c0b25bcf64cfce01b4144550ef69da8c96ee0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785854"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Oktatóanyag: automatikus kiegészítés és javaslatok hozzáadása a .NET SDK használatával

Megtudhatja, hogyan valósítható meg az automatikus kiegészítés (typeahead-lekérdezések és a javasolt eredmények), amikor a felhasználó elkezd begépelni egy keresőmezőt. Ebben az oktatóanyagban az automatikusan befejezett lekérdezéseket és a javasolt eredményeket külön, majd együtt fogjuk megjeleníteni. Egy felhasználónak csak két vagy három karaktert kell beírnia az összes elérhető eredmény megkereséséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Javaslatok hozzáadása
> * Kiemelés hozzáadása a javaslatokhoz
> * Automatikus kiegészítés hozzáadása
> * Az autocomplete és a javaslatok egyesítése

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag hozzáadja az automatikusan befejeződött és javasolt eredményeket az előző [hozzáadási lapozáshoz a keresési eredményekhez](tutorial-csharp-paging.md) tartozó oktatóanyagban.

Az oktatóanyagban szereplő kód befejezett verziója a következő projektben található:

* [3 – Add-typeahead (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/3-add-typeahead)

## <a name="prerequisites"></a>Előfeltételek

* [2a – Add-személyhívó (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) megoldás. A projekt az előző oktatóanyagból vagy a GitHubról származó másolatból létrehozott saját verzió lehet.

Ez az oktatóanyag a [Azure.Search.Documents (11-es verzió)](https://www.nuget.org/packages/Azure.Search.Documents/) csomag használatára lett frissítve. A .NET SDK egy korábbi verziójával kapcsolatban lásd: [Microsoft. Azure. Search (10-es verzió)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)mintakód.

## <a name="add-suggestions"></a>Javaslatok hozzáadása

Kezdjük a legegyszerűbb esettel, ha alternatívákat ajánlunk fel a felhasználó számára: a javasolt eredmények legördülő listája.

1. Az index. cshtml fájlban módosítsa `@id` a **TextBoxFor** utasítást a **azureautosuggest** értékre.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Ezt az utasítást követve a záró **&lt; /div &gt;** után adja meg ezt a parancsfájlt. Ez a szkript kihasználja az [automatikus kiegészítési widgetet](https://api.jqueryui.com/autocomplete/) a nyílt forráskódú jQuery UI könyvtárból, hogy bemutassa a javasolt eredmények legördülő listáját.

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/SuggestAsync?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    Az azonosító a `"azureautosuggest"` fenti szkriptet csatlakoztatja a keresőmezőbe. A widget forrás opciója egy olyan javaslati metódusra van beállítva, amely meghívja az ajánlott API-t két lekérdezési paraméterrel: **kiemelések** és **fuzzy**, mindkettő hamis értékre van állítva ebben a példányban. Emellett a keresés indításához legalább két karakter szükséges.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Hivatkozások hozzáadása a jQuery-parancsfájlokhoz a nézethez

1. A jQuery-könyvtár eléréséhez módosítsa a &lt; &gt; nézet fájljának Head szakaszát a következő kódra:

    ```html
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Mivel egy új jQuery-referenciát vezetünk be, az alapértelmezett jQuery-hivatkozást is el kell távolítani a _Layout. cshtml fájlban (a **nézetek/megosztott** mappában). Keresse meg a következő sorokat, és tegye megjegyzésbe az első parancsfájlt az ábrán látható módon. Ez a változás megakadályozza a jQuery-re mutató hivatkozások összeütközését.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Most már használhatjuk az előre definiált autocomplete jQuery-függvényeket.

### <a name="add-the-suggest-action-to-the-controller"></a>A javaslat művelet hozzáadása a vezérlőhöz

1. A Kezdőlap vezérlőben adja hozzá a **SuggestAsync** műveletet (a **PageAsync** művelet után).

    ```cs
    public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
    {
        InitSearch();

        // Setup the suggest parameters.
        var options = new SuggestOptions()
        {
            UseFuzzyMatching = fuzzy,
            Size = 8,
        };

        if (highlights)
        {
            options.HighlightPreTag = "<b>";
            options.HighlightPostTag = "</b>";
        }

        // Only one suggester can be specified per index. It is defined in the index schema.
        // The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg", and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

        // Convert the suggested query results to a list that can be displayed in the client.
        List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

        // Return the list of suggestions.
        return new JsonResult(suggestions);
    }
    ```

    A **size** paraméter határozza meg, hogy a rendszer hány eredményt ad vissza (ha nincs megadva, az alapértelmezett érték 5). Az index létrehozásakor a rendszer a keresési indexben megadja a _javaslatot_ . A Microsoft által üzemeltetett Sample Hotels-indexben a javaslat neve "SG", amely kizárólag a **pezsgő** mezőben keresi a javasolt egyezéseket.

    A zavaros megfeleltetés lehetővé teszi a "közelről lemaradt" beállítás használatát a kimenetben, legfeljebb egy szerkesztési távolságot. Ha a **Highlights** paraméter értéke TRUE (igaz), akkor a félkövér HTML-címkék hozzáadódnak a kimenethez. A következő szakaszban mindkét paraméter igaz értékre lesz állítva.

2. Előfordulhat, hogy szintaktikai hibákat talál. Ha igen, adja hozzá a következő két **using** utasítást a fájl elejéhez.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Futtassa az alkalmazást. A "Po" beírásakor számos lehetőséget kap, például? Most próbálkozzon a "PA" kifejezéssel.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-po.png" alt-text="A * Po * beírása két javaslatot mutat be" border="false":::

    Figyelje meg, hogy a beírt betűknek egy szót _kell_ elkezdeniük, és nem egyszerűen bele kell foglalni a szót.

4. A View szkriptben állítsa a **&fuzzy** igaz értékre, majd futtassa újra az alkalmazást. Most írja be a "Po" kifejezést. Figyelje meg, hogy a keresés azt feltételezi, hogy van egy levél hibás.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png" alt-text="A * PA * beírása az igaz értékre" border="false":::

    Ha érdekli, a [Lucene lekérdezési szintaxisa az Azure Cognitive Search](./query-lucene-syntax.md) ismerteti a fuzzy keresésekben használt logikát.

## <a name="add-highlighting-to-the-suggestions"></a>Kiemelés hozzáadása a javaslatokhoz

Javíthatjuk a javaslatok megjelenését a felhasználó számára úgy, hogy a **Highlights** paramétert igaz értékre állítja. Először azonban hozzá kell adnia egy kódot a nézethez a félkövér szöveg megjelenítéséhez.

1. A View (index. cshtml) nézetben adja hozzá a következő parancsfájlt a `"azureautosuggest"` korábban ismertetett parancsfájl után.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

1. Most módosítsa a szövegmező AZONOSÍTÓját, hogy az a következőképpen legyen beolvasva.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Futtassa újra az alkalmazást, és a javaslatok között a beírt szöveg félkövérrel jelenik meg. Próbálja meg beírni a "PA" kifejezést.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png" alt-text="* PA * beírása kiemeléssel" border="false":::

   A fenti kiemelési parancsfájlban használt logika nem üzembiztos. Ha egy olyan kifejezést ad meg, amely ugyanabban a névben kétszer jelenik meg, a félkövérrel szedett eredmények nem eléggé, amit szeretne. Próbálja beírni a "mo" kifejezést.

   Ha a fejlesztőnek választ kell adni az egyik kérdésre, ha egy parancsfájl "elég jól" működik, és mikor kell megoldania a hirtelen fordulatot. Ebben az oktatóanyagban nem vesszük figyelembe a további lépéseket, de a pontos algoritmus megtalálásával azt érdemes figyelembe venni, hogy a kiemelés nem hatékony az adataihoz. További információ: [találatok kiemelése](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Automatikus kiegészítés hozzáadása

Egy másik variáció, amely némileg eltér a javaslatoktól, az autocomplete (más néven "type-Ahead"), amely befejezi a lekérdezési időszakot. A felhasználói élmény fokozása előtt a legegyszerűbb implementációval fogunk kezdeni.

1. Adja meg a következő parancsfájlt a nézetbe az előző parancsfájlok követésével.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

1. Módosítsa a szövegmező AZONOSÍTÓját, hogy az a következőképpen legyen beolvasva.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. A Kezdőlap vezérlőben adja meg a **AutocompleteAsync** műveletet a **SuggestAsync** művelet után.

    ```cs
    public async Task<ActionResult> AutoCompleteAsync(string term)
    {
        InitSearch();

        // Setup the autocomplete parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 6
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

        // Convert the autocompleteResult results to a list that can be displayed in the client.
        List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

        return new JsonResult(autocomplete);
    }
    ```

    Figyelje meg, hogy ugyanazt a *javaslati* funkciót használjuk, amelyet a "SG" néven használunk az automatikus kiegészítési keresésben, ahogy a javaslatok esetében tettük (ezért csak a szállodai nevek automatikus kiegészítését próbáljuk ki).

    Számos **AutocompleteMode** -beállítás létezik, és **OneTermWithContext** használunk. További beállítások leírását az [automatikus kiegészítési API](/rest/api/searchservice/autocomplete) -ban találja.

1. Futtassa az alkalmazást. Figyelje meg, hogy a legördülő listában megjelenő beállítások köre egyetlen szó. Próbálja meg beírni a szavakat az "újra" kifejezéssel kezdődően. Figyelje meg, hogy a lehetőségek száma több karakter beírásával csökken.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png" alt-text="Alapszintű autocomplete beírása" border="false":::

    Ebben az esetben a korábban futtatott javaslatok szkriptje valószínűleg hasznosabb ennél az autocomplete parancsfájlnál. Annak érdekében, hogy az autocomplete felhasználóbarát legyen, érdemes a javasolt eredményekkel használni.

## <a name="combine-autocompletion-and-suggestions"></a>Az autocomplete és a javaslatok egyesítése

Az autocomplete és a javaslatok együttes használata a lehető legbonyolultabb lehetőség, és valószínűleg a legjobb felhasználói élményt nyújtja. Azt szeretnénk, hogy megjelenjen a begépelt szöveg, amely az első választás az Azure Cognitive Search számára a szöveg kitöltéséhez. Emellett számos javaslatot szeretnénk legördülő listaként használni.

Vannak olyan kódtárak, amelyek ezt a funkciót használják – gyakran "beágyazott autocomplete" néven vagy hasonló névvel. Ezzel a funkcióval azonban natív módon Implementáljuk ezt a funkciót, így megismerheti az API-kat. Ebben a példában először a vezérlőn fogunk dolgozni.

1. Adjon hozzá egy műveletet a vezérlőhöz, amely csak egy autocomplete eredményt ad vissza, valamint megadott számú javaslatot. Ezt a műveletet **AutoCompleteAndSuggestAsync** fogjuk hívni. A Kezdőlap vezérlőben adja hozzá a következő műveletet a többi új művelet után.

    ```cs
    public async Task<ActionResult> AutoCompleteAndSuggestAsync(string term)
    {
        InitSearch();

        // Setup the type-ahead search parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 1,
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap);

        // Setup the suggest search parameters.
        var sp = new SuggestOptions()
        {
            Size = 8,
        };

        // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg" and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", sp).ConfigureAwait(false);

        // Create an empty list.
        var results = new List<string>();

        if (autocompleteResult.Value.Results.Count > 0)
        {
            // Add the top result for type-ahead.
            results.Add(autocompleteResult.Value.Results[0].Text);
        }
        else
        {
            // There were no type-ahead suggestions, so add an empty string.
            results.Add("");
        }

        for (int n = 0; n < suggestResult.Value.Results.Count; n++)
        {
            // Now add the suggestions.
            results.Add(suggestResult.Value.Results[n].Text);
        }

        // Return the list.
        return new JsonResult(results);
    }
    ```

    Az **eredmények** listájának tetején egy autocomplete beállítást ad vissza, amelyet a javaslatok követnek.

1. A nézetben először egy olyan trükköt teszünk elérhetővé, hogy egy világosszürke autocomplete szó közvetlenül a felhasználó által megadott merészebb-szöveg alatt legyen megjelenítve. A HTML relatív elhelyezést is tartalmaz erre a célra. Módosítsa a **TextBoxFor** utasítást (és a körülötte &lt; &gt; lévő div-utasításokat) a következőre, és ha úgy gondolja, hogy az **alatta** lévő második keresőmező közvetlenül a normál keresőmező alatt található, akkor a keresőmező 39 képpont kihúzásával állítsa le az alapértelmezett helyükről.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Figyelje meg, hogy a rendszer ismét megváltoztatja az azonosítót, hogy **azureautocomplete** ebben az esetben.

1. A nézetben is adja meg a következő parancsfájlt, miután az eddig megadott összes parancsfájlt elvégezte. A szkript hosszas és összetett, mivel az általa kezelt bemeneti viselkedések különbözőek.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggestAsync?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Figyelje meg, hogyan használja az **intervallum** függvényt a mögöttes szöveg törlésére, ha az már nem egyezik meg a felhasználó által beírt értékkel, és a felhasználó beírásakor ugyanezt a kis-és nagybetűket is be kell állítani (a "PA" a "PA", a "PA", a "PA

    Tekintse át a szkript megjegyzéseit, és ismerkedjen meg a teljes körű ismeretekkel.

1. Végezetül egy kisebb módosítást kell végezni két HTML-osztályon, hogy azok transzparensek legyenek. Adja hozzá a következő sort a **searchBoxForm** és a **searchBox** osztályhoz a Hotels. css fájlban.

    ```html
    background: rgba(0,0,0,0);
    ```

1. Most futtassa az alkalmazást. Írja be a "PA" kifejezést a keresőmezőbe. Az automatikus kiegészítési javaslatként "Palace"-t kap, valamint két, "PA"-t tartalmazó szállodát?

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png" alt-text="Bevitel beágyazott automatikus kiegészítéssel és javaslatok" border="false":::

1. Próbálja megismételni az automatikus kiegészítési javaslat elfogadását, majd próbálja meg kiválasztani a javaslatokat a nyílbillentyűkkel és a TAB billentyűvel, és próbálkozzon újra az egérrel és egyetlen kattintással. Győződjön meg arról, hogy a parancsfájl szépen kezeli az összes helyzetet.

    Dönthet úgy, hogy egyszerűbb betölteni egy olyan könyvtárat, amely ezt a szolgáltatást kínálja Önnek, de most már tudja, hogy van-e legalább egy módszer a beágyazott automatikus kiegészítés működéséhez.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe az alábbi elvihetőket a projektből:

* Az autocomplete (más néven "type-Ahead") és a javaslatok lehetővé tehetik, hogy a felhasználó csak néhány kulcsot írjon be, hogy pontosan mit szeretne.
* Az autocomplete és a közös használatú javaslatok gazdag felhasználói élményt nyújtanak.
* Mindig tesztelje az autocomplete függvényeket a bemenet összes formájával.
* A **setInterval** függvény használata hasznos lehet a felhasználói felületi elemek ellenőrzése és javítása érdekében.

## <a name="next-steps"></a>Következő lépések

A következő oktatóanyagban megvizsgáljuk a felhasználói élmény javításának egy másik módját, a dimenziók használatával szűkítheti a keresést egyetlen kattintással.

> [!div class="nextstepaction"]
> [C# oktatóanyag: dimenziók használata a navigáláshoz – Azure Cognitive Search](tutorial-csharp-facets.md)
