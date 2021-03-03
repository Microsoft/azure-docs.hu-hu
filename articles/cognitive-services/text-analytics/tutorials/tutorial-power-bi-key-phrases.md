---
title: 'Oktatóanyag: Power BI integrálása a Text Analytics Cognitive Services-szolgáltatásba'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan lehet a Text Analytics API használatával kinyerni a Power BIban tárolt szövegből származó kulcsfontosságú kifejezéseket.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 8444ae08aa2c25c20723b2f8c571422af3b24bc8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736678"
---
# <a name="tutorial-integrate-power-bi-with-the-text-analytics-cognitive-service"></a>Oktatóanyag: Power BI integrálása a Text Analytics Cognitive Services-szolgáltatásba

A Microsoft Power BI Desktop ingyenes alkalmazás segítségével csatlakozni, átalakítani és szemléltetni is lehet az adatokat. A Microsoft Azure Cognitive Services részét képező Text Analytics természetes nyelvi feldolgozást kínál. A nyers, szerkezet nélkül megadott szövegből képes kivonatolni a legfontosabb kifejezéseket, elemzi az érzéseket és azonosítani tud olyan jól ismert dolgokat, mint a márkák. Ezekkel az eszközökkel gyors bepillantást nyerhet abba, hogy az ügyfelei miről beszélnek, és milyen érzéseket váltottak ki belőlük.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * A Power BI Desktop használatával adatokat importálhat és alakíthat át
> * Egyéni függvény létrehozása a Power BI Desktopban
> * A Power BI Desktop integrálása a Text Analytics Key Phrases API felületbe
> * A Text Analytics Key Phrases API használatával az ügyfél visszajelzések közül a legfontosabb kifejezések kiemelése
> * Szófelhő létrehozása az ügyfél visszajelzésekből

## <a name="prerequisites"></a>Előfeltételek
<a name="Prerequisites"></a>

- Microsoft Power BI Desktop. [Töltse le ingyenesen](https://powerbi.microsoft.com/get-started/).
- Egy Microsoft Azure-fiók. [Hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/) , vagy [Jelentkezzen be](https://portal.azure.com/).
- Cognitive Services API fiók a Text Analytics API felülettel. Ha még nincs fiókja, [regisztráljon](../../cognitive-services-apis-create-account.md) és használhatja a havi 5000 tranzakciót tartalmazó ingyenes csomagot (lásd [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)) az oktatóanyag befejezéséhez.
- Kell még a regisztráció során létrejött [Text Analytics hozzáférési kulcs](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource).
- Ügyfelek megjegyzései. Használhatja [a példaadatokat](https://aka.ms/cogsvc/ta), de a saját adatait is. Az oktatóanyagban azt feltételezzük, hogy a példaadatainkat használja.

## <a name="load-customer-data"></a>Ügyféladatok betöltése
<a name="LoadingData"></a>

Első lépésként nyissa meg a Power BI Desktopot és töltse be az [Előfeltételeknél](#Prerequisites) letöltött vesszővel tagolt (CSV) fájlt `FabrikamComments.csv`. Ez a fájl egy fiktív kisvállalkozás támogatási fórumának egy napi feltételezett tevékenységeit tartalmazza.

> [!NOTE]
> A Power BI források széles választékából, például a Facebookról vagy egy SQL-adatbázisból származó adatokat is képes használni. További információt [a Facebook](https://powerbi.microsoft.com/integrations/facebook/) és [az SQL Server a Power BI-jal való integrációját](https://powerbi.microsoft.com/integrations/sql-server/) ismertető cikkekben olvashat.

A Power BI Desktop főablakában válassza ki a **Kezdőlap** menüszalagot. A menüszalagon a **Külső adatok** csoportban, nyissa meg az **Adatok lekérése** legördülő menüt és válassza **Szöveg/CSV** opciót.

![[Az Adatok lekérése gomb]](../media/tutorials/power-bi/get-data-button.png)

Megjelenik a Megnyitás párbeszédpanel. Keresse meg a Letöltések mappát vagy azt a mappát, ahová letöltötte a `FabrikamComments.csv` fájlt. Kattintson a `FabrikamComments.csv` elemre, majd a **Megnyitás** gombra. Megjelenik a CSV-importálási párbeszédpanel.

![[A CSV-importálási párbeszédpanel]](../media/tutorials/power-bi/csv-import.png)

A CSV-importálási párbeszédpanelen ellenőrizheti, hogy a Power BI Desktop megfelelően észlelte-e a karakterkészletet, a tagoló karaktert, a fejlécsorokat és az oszloptípusokat. Ha minden információ rendben van, kattintson a **Betöltés** parancsra.

A betöltött adatok megtekintéséhez kattintson a Power BI-munkaterület bal szélén az **Adatnézet** gombra. Megnyílik az adatokat tartalmazó táblázat, mint a Microsoft Excelben.

![[Az importált adatok kezdeti nézete]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="prepare-the-data"></a>Az adatok előkészítése
<a name="PreparingData"></a>

Előfordulhat, hogy az adatokat át kell alakítani a Power BI Desktopban ahhoz, hogy a Text Analytics szolgáltatás Key Phrases API fel tudja dolgozni.

A mintaadatok egy `subject` oszlopot és egy `comment` oszlopot tartalmaznak. A Power BI Desktopon az Oszlopok egyesítése függvénnyel mindkét oszlop adataiból gyűjtheti a kulcskifejezéseket, nem csak az `comment` oszlopból.

A Power BI Desktopon válassza ki a **Kezdőlap** menüszalagot. A **Külső adatok** csoportban kattintson a **Lekérdezések szerkesztése** elemre.

![[A Külső adatok csoport a Kezdőlap menüszalagon]](../media/tutorials/power-bi/edit-queries.png)

Ha még nincs kijelölve, jelölje ki az ablak bal oldalán a `FabrikamComments` elemet a **Lekérdezések** listában.

Most jelölje be a `subject` és a `comment` oszlopot a táblázatban. Lehetséges, hogy vízszintesen görgetnie kell, hogy mindkét oszlopot elérje. Először kattintson a `subject` oszlop fejlécére, majd a Control billentyűt lenyomva tartva kattintson a `comment` oszlop fejlécére is.

![[Az egyesíteni kívánt mezők kijelölése]](../media/tutorials/power-bi/select-columns.png)

Válassza ki az **Átalakít** menüszalagot. A menüszalagon a **Szöveges oszlopok** csoportban kattintson az **Oszlopok egyesítése** elemre. Megjelenik az Oszlopok egyesítése párbeszédpanel.

![[Oszlopok egyesítése az Oszlopok egyesítése párbeszédpanelen]](../media/tutorials/power-bi/merge-columns.png)

Az Oszlopok egyesítése párbeszédpanelen válassza ki az `Tab` elválasztó karaktert, majd kattintson az **OK** gombra.

Ha szeretné, az Üres elemek eltávolítása szűrővel ki is szűrheti az üres üzeneteket, illetve az Átalakítás tisztítással eltávolíthatja a nem nyomtatható karaktereket. Ha az adatok a mintafájl `spamscore` oszlopához hasonló oszlopot tartalmaznak, akkor a Szám szűrővel kihagyhatja a „spam” megjegyzéseket.

## <a name="understand-the-api"></a>Az API ismertetése
<a name="UnderstandingAPI"></a>

A Text Analytics szolgáltatás a [Key Phrases API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V3-0/operations/KeyPhrases) útján egy HTTP-kéréssel legfeljebb ezer szöveges dokumentumot tud feldolgozni. A Power BI azonban jobban szereti a rekordokat egyenként kezelni, ezért az oktatóanyagban az API-hívásaink mindig csak egyetlen dokumentumot fognak tartalmazni. A Key Phrases API számára minden feldolgozandó dokumentumra a következő mezőket kell megadni.

| Mező | Leírás |
| - | - |
| `id`  | A dokumentum egy egyedi azonosítóját a kérésen belül. Ezt a mezőt a válasz is tartalmazza. Így több dokumentum feldolgozásakor könnyen társítani lehet a kinyert kulcskifejezéseket a dokumentummal, amelyből származnak. Az oktatóanyagban, mivel kérésenként csak egy dokumentumot dolgozunk fel, az `id` értéket fixen rögzítheti, hogy mindegyik kérésre ugyanaz legyen.|
| `text`  | A feldolgozandó szöveg. Ennek a mezőnek az értéke az [előző részben](#PreparingData) létrehozott `Merged` oszlopból származik, amely a tárgysor és a megjegyzés kombinált szövegét tartalmazza. A legfontosabb kifejezések API-nak szüksége van arra, hogy az adathalmaz ne legyen hosszabb 5 120 karakternél.|
| `language` | A dokumentum természetes nyelvét jelölő kód. A mintaadatokban minden üzenet angolul van, így a mezőre rögzítheti a `en` értéket.|

## <a name="create-a-custom-function"></a>Egyéni függvény létrehozása
<a name="CreateCustomFunction"></a>

Most már készen áll az egyéni függvény létrehozására, amely integrálja majd a Power BI-t és a Text Analytics szolgáltatást. A függvény a feldolgozandó szöveget paraméterként kapja meg. Elvégzi az adatok átalakítását a szükséges JSON formára és vissza, és elküldi a HTTP-kérést a Key Phrases API-nak. A függvény elemzi az API válaszát és a kinyert kulcskifejezések vesszővel tagolt listáját tartalmazó sztringet ad vissza.

> [!NOTE]
> A Power BI Desktop egyéni függvényei a [Power Query M képletnyelven](/powerquery-m/power-query-m-reference) (röviden „M”) vannak megírva. Az M egy funkcionális programozási nyelv, amelynek az alapját az [F#](/dotnet/fsharp/) képezi. Az oktatóanyag elvégzéséhez azonban nem kell programozónak lennie. A szükséges kód alább megtalálható.

A Power BI Desktopban ellenőrizze, hogy még a Lekérdezésszerkesztő ablakban van-e. Ha nem, válassza ki a **Kezdőlap** menüszalagot, majd a **Külső adatok** csoportban kattintson a **lekérdezések szerkesztése** elemre.

Most a **Kezdőlap** menüszalagon az **Új lekérdezés** csoportban nyissa meg az **Új forrás** legördülő menüt, majd válassza az **Üres lekérdezést**. 

Az új lekérdezés eredetileg `Query1` névvel megjelenik a Lekérdezések listában. Kattintson duplán erre az elemre, és nevezze át a `KeyPhrases` névre.

Most a **Kezdőlap** menüszalag **Lekérdezés** csoportjának **Speciális szerkesztő** elemére kattintva nyissa meg a Speciális szerkesztő ablakot. Törölje az eredetileg az ablakban lévő kódot, és illessze be a következőt. 

> [!NOTE]
> Cserélje le az alábbi példás végpontot (amely tartalmazza `<your-custom-subdomain>` ) a Text Analytics erőforráshoz generált végponttal. Ezt a végpontot megkeresheti a [Azure Portalba](https://azure.microsoft.com/features/azure-portal/)való bejelentkezéssel, a Text Analytics-előfizetés kiválasztásával, majd a lehetőség kiválasztásával `Quick start` .


```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics" & "/v3.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

A `YOUR_API_KEY_HERE` helyett írja be saját Text Analytics hozzáférési kulcsát. A kulcsot megkeresheti úgy is, hogy bejelentkezik az [Azure portálra](https://azure.microsoft.com/features/azure-portal/) és a Text Analytics előfizetésének kijelölése után az Áttekintés lapot választja. A kulcs előtt és után mindenképp tartsa meg az idézőjeleket. Ezután kattintson a **Kész** gombra.

## <a name="use-the-custom-function"></a>Egyéni függvény használata
<a name="UseCustomFunction"></a>

Ekkor az egyéni függvény használatával kinyerheti az ügyfelek megjegyzéseiből a kulcskifejezéseket, és eltárolhatja őket a táblázat új oszlopában. 

A Power BI Desktopban, a Lekérdezésszerkesztő ablakban váltson vissza a `FabrikamComments` lekérdezésre. Válassza ki a **Oszlop hozzáadása** menüszalagot. Az **Általános** csoportban kattintson az **Egyéni függvény hívása** elemre.

![[Egyéni függvény hívása gomb]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

Megjelenik az Egyéni függvény hívása párbeszédablak. Az **Új oszlop neve** mezőbe írja be: `keyphrases`. A **Függvénylekérdezésnél** válassza ki a létrehozott, `KeyPhrases` egyéni függvényt.

A párbeszédpanelen új mező jelenik meg, **(nem kötelező) szöveg**. A mezőben azt kell megadnunk, mely oszlop adja a Key Phrases API `text` paraméterének az értékét. (Ne feledje, hogy már rögzítette a és a paraméterek értékeit `language` `id` .) Válassza ki `Merged` (a [korábban](#PreparingData) létrehozott oszlopot a tárgy és az üzenet mezőinek egyesítésével) a legördülő menüből.

![[Egyéni függvény hívása]](../media/tutorials/power-bi/invoke-custom-function.png)

Végezetül kattintson az **OK** gombra.

Ha minden kész, a Power BI a táblázat minden sorára meghívja egyszer az egyéni függvényt. A lekérdezéseket elküldi a Key Phrases API-nak és új oszlopot ad a táblázathoz az eredmények tárolására. Mielőtt azonban ez megtörténne, lehetséges, hogy meg kell adnia a hitelesítési és adatvédelmi beállításokat.

## <a name="authentication-and-privacy"></a>Hitelesítés és adatvédelem
<a name="Authentication"></a>

Miután bezárja az Egyéni függvény hívása párbeszédpanelt, egy értesítés jelenhet meg, amely megkéri, hogy adja meg, miként szeretne csatlakozni a Key Phrases API-hoz.

![[hitelesítő adatok értesítése]](../media/tutorials/power-bi/credentials-banner.png)

Kattintson a **Hitelesítő adatok szerkesztése** gombra, majd ellenőrizze, hogy a párbeszédpanelen a `Anonymous` lehetőség legyen kiválasztva, végül kattintson a **Csatlakozás** parancsra. 

> [!NOTE]
> Válassza az `Anonymous` elemet, mert a Text Analytics szolgáltatás a hozzáférési kulcs használatával hitelesíti Önt, ezért a Power BI-nak nem kell hitelesítő adatokat megadnia magához a HTTP-kéréshez.

> [!div class="mx-imgBorder"]
> ![[a névtelen hitelesítés beállítása]](../media/tutorials/power-bi/access-web-content.png)

Ha a Hitelesítő adatok szerkesztése értesítés a névtelen hozzáférés beállítása után is látható, elképzelhető, hogy elfelejtette bemásolni a Text Analytics hozzáférési kulcsot az `KeyPhrases` [egyéni függvény](#CreateCustomFunction) kódba.

Ezután egy értesítés jelenhet meg, amely arra kéri, hogy szolgáltasson információkat az adatforrás adatainak védelmével kapcsolatban. 

![[adatvédelmi értesítés]](../media/tutorials/power-bi/privacy-banner.png)

Kattintson a **Folytatás** gombra, és válassza a `Public` beállítást a párbeszédpanelen lévő minden adatforrásra. Ezután kattintson a **Mentés gombra.**

![[adatforrás adatvédelmi beállításainak megadása]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="create-the-word-cloud"></a>A szófelhő létrehozása
<a name="WordCloud"></a>

Miután a megjelenő értesítéseket elintézte, kattintson a Kezdőlap menüszalag **Bezárás és alkalmazás** elemére a Lekérdezésszerkesztő bezárásához.

A Power BI Desktop néhány pillanatig a szükséges HTTP-kérésekkel lesz elfoglalva. A táblázat mindegyik sorához az új `keyphrases` oszlopban találhatók a szövegben a Key Phrases API által talált kulcskifejezések. 

Most pedig ezzel az oszloppal létrehozunk egy szófelhőt. Első lépésként kattintson a **Jelentés** gombra a Power BI Desktop főablakában, a munkaterület bal oldalán.

> [!NOTE]
> Miért a kivonatolt kulcskifejezéseket használjuk a szófelhő létrehozásához a megjegyzések teljes szövege helyett? A kulcskifejezések az ügyfelek megjegyzéseiből a *fontos* és nem egyszerűen csak a *leggyakoribb* szavakat tartalmazzák. Emellett a szavak méretezését így nem torzítja az, ha valamely szó csak szűk számú megjegyzésben fordul elő nagyon gyakran.

Ha a Word Cloud (Szófelhő) egyéni vizualizációt még nem telepítette, tegye meg. A munkaterület jobb oldalán lévő Vizualizációk panelen kattintson a három pontra (**...**), és válassza az **Importálás az áruházból** lehetőséget. Ezután keressen rá a „cloud” (felhő) kifejezésre, és kattintson a **Hozzáadás** gombra a Word Cloud (Szófelhő) vizualizáció mellett. A Power BI telepíti a szófelhő vizualizációt, és tájékoztatja, amint ez sikeresen megtörtént.

![[egyéni vizualizáció hozzáadása]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Először kattintson a szófelhő ikonjára a Vizualizációk panelen.

![[A szófelhő ikonja a Vizualizációk panelen]](../media/tutorials/power-bi/visualizations-panel.png)

Egy új jelentés jelenik meg a munkaterületen. Húzza a `keyphrases` mezőt a Mezők panelről a Vizualizációk panel Kategória mezőjébe. A szófelhő megjelenik a jelentésben.

Most váltson a Vizualizációk panel Formátum lapjára. A Stopszavak kategóriában kapcsolja be az **Alapértelmezett stopszavak** beállítást a rövid és gyakori szavak (például a névelők) kiszűréséhez a felhőből. 

![[az alapértelmezett stopszavak aktiválása]](../media/tutorials/power-bi/default-stop-words.png)

Egy kicsit lejjebb ugyanezen a panelen kapcsolja ki a **Szöveg elforgatása** és a **Cím** beállítást.

![[fókusz mód aktiválása]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

A jelentésben a Fókusz mód eszközre kattintva közelebbről is megtekintheti a szófelhőt. Az eszköz kinagyítja a szófelhőt a teljes munkaterületre, amint az alább látható.

![[Egy szófelhő]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>A Text Analytics további szolgáltatásai
<a name="MoreServices"></a>

A Text Analytics szolgáltatás a Microsoft Azure Cognitive Services szolgáltatásainak egyike, és hangulatelemzési és nyelvfelismerési funkciókat is kínál. A nyelvfelismerés különösen hasznos, ha az ügyfelek visszajelzései nem mind angol nyelven születnek.

Mindkét további API hasonlóan működik, mint a Key Phrases API. Ez azt jelenti, hogy a Power BI Desktoppal integrálni tudja majd őket olyan egyéni függvénnyel, amely majdnem megegyezik az oktatóanyagban létrehozottal. Csak hozzon létre egy üres lekérdezést, és illessze be a megfelelő kódot az alábbiak közül a Speciális szerkesztőbe a korábbihoz hasonló módon. (Ne feledkezzen meg a hozzáférési kulcsról!) Ezután, ahogy korábban is, használja a függvényt, és vegyen fel egy új oszlopot a táblába.

Az alábbi hangulatelemzési függvény egy pontszámot ad vissza, amely a szövegben kifejezett hangulatot méri.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[confidenceScores]
in  sentiment
```

Íme két változat egy nyelvfelismerési függvényre. Az első a nyelv ISO-kódját adja vissza (például az angol esetében: `en`), a második pedig a közkeletű nevét (például: `English`). Észreveheti, hogy a két változat között csak a törzs utolsó sorában van eltérés.

```fsharp
// Returns the two-letter language code (for example, 'en' for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (for example, 'English') of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Végezetül íme a korábban bemutatott Key Phrases-függvény egy olyan változata, amely a kifejezéseket nem egyetlen, vesszővel tagolt sztringként, hanem listaobjektumként adja vissza. 

> [!NOTE]
> Azzal, hogy egyetlen sztringet kaptunk vissza, a szófelhőt egyszerűbben lehetett létrehozni. A lista azonban egy olyan formátum, amellyel a visszaadott kifejezések sokkal rugalmasabban kezelhetők a Power BI-ban. A listaobjektumok a Power BI Desktopban a Lekérdezésszerkesztő Átalakítás menüszalagjának Strukturált oszlop csoportjában szerkeszthetők.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Következő lépések
<a name="NextSteps"></a>

További információk a Text Analytics szolgáltatásról, a Power Query M képletnyelvről és a Power BI-ról.

> [!div class="nextstepaction"]
> [Text Analytics API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0)

> [!div class="nextstepaction"]
> [Power Query M-hivatkozás](/powerquery-m/power-query-m-reference)

> [!div class="nextstepaction"]
> [A Power BI dokumentációja](https://powerbi.microsoft.com/documentation/powerbi-landing-page/)