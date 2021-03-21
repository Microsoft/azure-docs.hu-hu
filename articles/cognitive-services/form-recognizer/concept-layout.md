---
title: Elrendezések – űrlap-felismerő
titleSuffix: Azure Cognitive Services
description: Az elrendezés elemzésével kapcsolatos fogalmak megismerése az űrlap-felismerő API használatának és korlátainak használatával.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 73bef21f430bde1c6c2c95d7c3f685cccbbd9179
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467017"
---
# <a name="form-recognizer-layout-service"></a>Űrlap-felismerő elrendezési szolgáltatás

Az Azure-űrlap felismerője szöveget, táblákat, kiválasztási jeleket és strukturált adatokat tud kinyerni a dokumentumokból az elrendezési szolgáltatás használatával. Az elrendezési API lehetővé teszi, hogy az ügyfelek különböző formátumokban fogadják a dokumentumokat, és a dokumentumok strukturált adatábrázolását adják vissza. A hatékony optikai karakterfelismerési [(OCR)](../computer-vision/concept-recognizing-text.md) képességeket kombinálja a mély tanulási modellel a szöveg, a táblák, a kiválasztási jelek és a dokumentum-struktúra kinyeréséhez. 

## <a name="what-does-the-layout-service-do"></a>Mit tesz az elrendezési szolgáltatás?

A layout API Kinyeri a szöveg, a táblák, a kiválasztási jelek és a strukturált adatokat a dokumentumokból kivételes pontossággal, és egy rendezett, strukturált, JSON-választ ad vissza. A dokumentumok különböző formátumokból és minőségből állnak, beleértve a telefonról rögzített képeket, a szkennelt dokumentumokat és a digitális PDF-fájlokat is. Az elrendezési API pontosan kibontja az összes dokumentum strukturált kimenetét.

![Példa elrendezésre](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Próbálja ki

Az űrlap-felismerő elrendezési szolgáltatás kipróbálásához nyissa meg az online minta felhasználói felület eszközét:

> [!div class="nextstepaction"]
> [Űrlap OCR test Tool eszköz (FOTT)](https://fott-preview.azurewebsites.net)

Az űrlap-felismerő elrendezési API kipróbálásához szüksége lesz egy Azure-előfizetésre ([hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)) és egy [űrlap-felismerő erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) -végpontot és kulcsot. 

![Minta felhasználói felület képernyőképe; a dokumentumok szövegét, táblázatait és kiválasztási jelét elemezzük](./media/analyze-layout.png)

### <a name="input-requirements"></a>Bemeneti követelmények 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Az elemzés elrendezésének művelete

Először hívja meg az [elemzés elrendezése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) műveletet. Az elemzés során a dokumentum (rendszerkép, TIFF vagy PDF-fájl) bemenetként jelenik meg, és Kinyeri a dokumentum szövegét, táblázatait, kiválasztási jelzéseit és szerkezetét. A hívás egy válasz fejléc nevű mezőt ad vissza `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó eredmény-azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Természetes olvasási sorrend kimenete (csak latin betűs)

Megadhatja, hogy a szövegsorok milyen sorrendben legyenek kimenetben a `readingOrder` lekérdezési paraméterrel. Az `natural` az alábbi példában látható módon egy emberi felhasználóbarát olvasási sorrendi kimenetre használható. Ez a funkció csak latin nyelveken támogatott.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Elrendezési olvasási sorrend – példa" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Oldalszámok vagy tartományok kiválasztása szöveg kinyeréséhez

Nagyméretű többoldalas dokumentumok esetén a `pages` lekérdezési paraméterrel adhatja meg a szöveg kinyerésének adott oldalszámait vagy a különböző lapok tartományait. Az alábbi példa egy 10 oldalas dokumentumot mutat be, amely mindkét esetben kinyert szöveggel rendelkezik – az összes oldalt (1-10) és a kijelölt lapokat (3-6).

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Kijelölt oldalak kimenetének elrendezése":::

## <a name="the-get-analyze-layout-result-operation"></a>Az elemzési elrendezés eredményének lekérése művelet

A második lépés az elemzési [elrendezés eredményének beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) művelet hívása. Ez a művelet az elemzés elrendezése művelet által létrehozott eredmény-azonosítót adja meg. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. 

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | `notStarted`: Az elemzési művelet nem indult el.<br /><br />`running`: Az elemzési művelet folyamatban van.<br /><br />`failed`: Az elemzési művelet meghiúsult.<br /><br />`succeeded`: Az elemzési művelet sikeresen befejeződött.|

Hívja meg ezt a műveletet a iteratív, amíg vissza nem adja az `succeeded` értéket. A kérések másodpercenkénti (RPS) arányának meghaladása érdekében használjon 3 – 5 másodperces intervallumot.

Ha az **állapot** mező `succeeded` értéke, a JSON-válasz tartalmazni fogja a kinyert elrendezést, szöveget, táblázatot és kijelölési jeleket. A kinyert adatok között szerepelnek a kibontott szövegsorok és szavak, a határolókeretok, a szöveges megjelenések a kézzel írt jelekkel, táblákkal és kiválasztási jelekkel a kijelölt/nem kiválasztott jelöléssel. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Szövegsorok kézírásos besorolása (csak latin betűs)

A válasz magában foglalja annak besorolását, hogy az egyes szövegfájlok kézírásos stílusúak-e, valamint a megbízhatósági pontszám mellett. Ez a funkció csak latin nyelveken támogatott. Az alábbi példa a képen látható szöveg kézírásos besorolását mutatja be.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="Példa kézírás-besorolásra":::

### <a name="sample-json-output"></a>Példa JSON-kimenetre

Az elemzési *elrendezés eredményének beolvasása* műveletre adott válasz a dokumentum strukturált ábrázolása, a kinyert információkkal együtt. Tekintse meg a [minta dokumentum fájlját](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) és a strukturált kimeneti [minta elrendezési kimenetét](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json).

A JSON-kimenet két részből áll:

* `readResults` a csomópont tartalmazza az összes felismert szöveget és kiválasztási jelet. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. 
* `pageResults` a csomópont tartalmazza a határoló mezőkkel, a megbízhatósággal és a "readResults" sorokra és szavakra mutató hivatkozásokat tartalmazó táblákat és cellákat.

## <a name="example-output"></a>Példa kimenetre

### <a name="text"></a>Szöveg

Az elrendezési API Kinyeri a szöveget a dokumentumokból (PDF, TIFF) és képekből (JPG, PNG, BMP) több szöveges szöggel és színnel. A dokumentumok, faxok, nyomtatott és/vagy kézzel írott (csak angol nyelvű) és vegyes üzemmódok képeit fogadja el. A szöveg kinyerve a sorokra, szavakra, határoló mezőkre, megbízhatósági pontszámokra és stílusra (kézírásos vagy egyéb) vonatkozó információkkal. Az összes szöveges információt a `readResults` JSON-kimenet szakasza tartalmazza. 

### <a name="tables"></a>Táblázatok

A layout API kibontja a táblákat a dokumentumokból (PDF, TIFF) és képekből (JPG, PNG, BMP). A dokumentumok beolvasható, lefényképezhető vagy digitalizált dokumentumokat is használhatnak. A táblázatok összetett cellákkal vagy oszlopokkal, szegélyekkel vagy anélkül is összetettek, és páratlan szögek. A kinyert tábla adatai tartalmazzák az oszlopok és a sorok, a sorok és az oszlopok tartományának mennyiségét. Az egyes cellákat a rendszer kinyeri a határoló mezővel, és a szakaszban kinyert szövegre hivatkozik `readResults` . A tábla adatai a JSON- `pageResults` kimenet szakaszában találhatók. 

![Táblák – példa](./media/tables-example.jpg)

### <a name="selection-marks"></a>Kiválasztási jelek

Az elrendezési API a dokumentumokból származó kiválasztási jeleket is kibontja. A kinyert kiválasztási jelek közé tartozik a határolókeret, a megbízhatóság és az állapot (kiválasztott/nem kijelölt). A kijelölési megjelölés adatai a JSON-kimenet szakaszában vannak kibontva `readResults` . 

## <a name="next-steps"></a>Következő lépések

* Saját elrendezés kinyerésének kipróbálása az [űrlap-felismerő minta felhasználói felület eszköz](https://fott-preview.azurewebsites.net/) használatával
* Az [űrlap-felismerő](quickstarts/client-library.md) gyors üzembe helyezésével megkezdheti az elrendezés kinyerését az Ön által választott fejlesztői nyelven.

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](./overview.md)
* [REST API dokumentációs dokumentumok](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
