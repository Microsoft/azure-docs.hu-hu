---
title: Elrendezések – űrlap-felismerő
titleSuffix: Azure Cognitive Services
description: Az elrendezés elemzésével kapcsolatos fogalmak megismerése az űrlap-felismerő API használatának és korlátainak használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: d529e74614a4fd5ee49d7e6161c65f68e8e76fce
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585058"
---
# <a name="form-recognizer-layout-service"></a>Űrlap-felismerő elrendezési szolgáltatás

Az Azure-űrlap felismerője szöveget, táblákat, kiválasztási jeleket és strukturált adatokat tud kinyerni a dokumentumokból az elrendezési szolgáltatás használatával. Az elrendezési API lehetővé teszi, hogy az ügyfelek különböző formátumokban fogadják a dokumentumokat, és a dokumentum strukturált adatait és ábrázolását adják vissza. A hatékony optikai karakterfelismerési [(OCR)](../computer-vision/concept-recognizing-text.md) képességeket kombinálja a részletes tanulási modellekkel a szöveg, a táblák, a kiválasztási jelek és a dokumentumok szerkezetének kinyeréséhez. 

## <a name="what-does-the-layout-service-do"></a>Mit tesz az elrendezési szolgáltatás?

A layout API Kinyeri a szöveg, a táblák, a kiválasztási jelek és a strukturált adatokat a dokumentumokból kivételes pontossággal, és egy strukturált, JSON-válaszban adja vissza őket. A dokumentumok különböző formátumokból és minőségből származnak, beleértve a telefonnal rögzített képeket, a szkennelt dokumentumokat és a digitális PDF-fájlokat is. Az elrendezési API Kinyeri a strukturált kimenetet az összes dokumentumból.

![Példa elrendezésre](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Próbálja ki

Az űrlap-felismerő elrendezési szolgáltatás kipróbálásához nyissa meg az online minta felhasználói felület eszközét:

> [!div class="nextstepaction"]
> [Mintául szolgáló felhasználói felület](https://fott-preview.azurewebsites.net/)

Az űrlap-felismerő elrendezési API kipróbálásához szüksége lesz egy Azure-előfizetésre ([hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)) és egy [űrlap-felismerő erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) -végpontot és kulcsot. 

![Minta felhasználói felület képernyőképe; a dokumentumok szövegét, táblázatait és kiválasztási jelét elemezzük](./media/analyze-layout.png)

### <a name="input-requirements"></a>Bemeneti követelmények 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Az elemzés elrendezésének művelete

Az [elemzés elrendezés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) művelete egy dokumentumot (képet, TIFF vagy PDF fájlt) fogad el bemenetként, és Kinyeri a dokumentum szövegét, táblázatait, kiválasztási jelzéseit és szerkezetét. A hívás egy válasz fejléc nevű mezőt ad vissza `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó eredmény-azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>Az elemzési elrendezés eredményének lekérése művelet

A második lépés az elemzési [elrendezés eredményének beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) művelet hívása. Ez a művelet az elemzés elrendezése művelet által létrehozott eredmény-azonosítót adja meg. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. 

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | `notStarted`: Az elemzési művelet nem indult el.<br /><br />`running`: Az elemzési művelet folyamatban van.<br /><br />`failed`: Az elemzési művelet meghiúsult.<br /><br />`succeeded`: Az elemzési művelet sikeresen befejeződött.|

Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér az `succeeded` értékkel. A kérések másodpercenkénti (RPS) arányának meghaladása érdekében használjon 3 – 5 másodperces intervallumot.

Ha az **állapot** mező `succeeded` értéke, a JSON-válasz tartalmazza a kinyert elrendezés kinyerésének eredményét, a szöveget, a táblákat és a kiválasztási jeleket. A kinyert adatok a kibontott szövegeket és szavakat, a határolókeret, a szöveges megjelenést jelző, a táblákat és a kiválasztási jeleket tartalmazzák, és a kijelölt/nem kijelölt jelöléseket jelölik. 

### <a name="sample-json-output"></a>Példa JSON-kimenetre

Az elemzési elrendezés eredményének beolvasása műveletre adott válasz a dokumentum strukturált ábrázolása lesz a kinyert információkkal együtt. Tekintse meg a [minta dokumentum fájlját](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) és a strukturált kimeneti [minta elrendezési kimenetét](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json).

A JSON-kimenet két részből áll: 
* `"readResults"` a csomópont tartalmazza az összes felismert szöveget és kiválasztási jelet. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. 
* `"pageResults"` a csomópont tartalmazza a határoló mezőkkel, a megbízhatósággal és a "readResults" sorokra és szavakra mutató hivatkozásokat tartalmazó táblákat és cellákat.

## <a name="example-output"></a>Példa kimenetre

### <a name="text"></a>Szöveg

Az elrendezés Kinyeri a szöveget a dokumentumokból (PDF, TIFF) és képekből (jpg, PNG, BMP) különböző szöveggel, színekkel, szögekkel, a dokumentumok, a faxok, a nyomtatott, a kézírásos (csak angol) és a kevert üzemmódokkal együtt. A szöveg a sorokra, a szavakra, a határolókeretokra, a megbízhatósági pontszámokra és a stílusra (kézzel írott vagy egyéb) vonatkozó információkkal van kibontva. Az összes szöveges információt a `"readResults"` JSON-kimenet szakasza tartalmazza. 

### <a name="tables"></a>Táblák

Az elrendezés kibontja a táblákat a dokumentumokból (PDF, TIFF) és képekből (jpg, PNG, BMP). A dokumentumok beolvasható, lefényképezhető vagy digitalizált dokumentumokat is használhatnak. A táblázatok lehetnek összetett táblák, amelyek egyesített cellákkal vagy oszlopokkal, szegélyekkel vagy anélkül, illetve páratlan nézőpontokkal rendelkeznek. A kinyert táblák tartalmazzák az oszlopok és sorok, a sorok és az oszlopok tartományának számát. Az egyes cellákat a rendszer kinyeri a határoló mezővel, és a szakaszban kinyert szövegre hivatkozik `"readResults"` . A tábla adatai a JSON- `"pageResults"` kimenet szakaszában találhatók. 

![Táblák – példa](./media/tables-example.jpg)

### <a name="selection-marks"></a>Kiválasztási jelek

Az elrendezés kibontja a dokumentumok kiválasztási jeleit is. Kinyert kijelölési jelek közé tartozik a határolókeret, a megbízhatóság és az állapot (kiválasztott/nem kijelölt). A kijelölési megjelölés adatai a JSON-kimenet szakaszában vannak kibontva `"readResults"` . 

## <a name="next-steps"></a>Következő lépések

- Saját elrendezés kinyerésének kipróbálása az [űrlap-felismerő minta felhasználói felületének](https://fott-preview.azurewebsites.net/) használatával
- Az [űrlap-felismerő](quickstarts/client-library.md) gyors üzembe helyezésével megkezdheti az elrendezés kinyerését az Ön által választott fejlesztői nyelven.

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](./overview.md)
* [REST API dokumentációs dokumentumok](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)