---
title: Számlák – űrlap felismerő
titleSuffix: Azure Cognitive Services
description: A számlázási elemzéssel kapcsolatos fogalmak megismerése az űrlap-felismerő API használatának és korlátainak használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 7acfa9c2ffdd4cdd62e965041cdc42dc44d469c5
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845578"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Űrlap-felismerő előre összeépített számlázási modellje

Az Azure-űrlap felismerője az előre elkészített számla modelljeivel elemezheti és kinyerheti az értékesítési számlákból származó információkat. A számla API lehetővé teszi, hogy az ügyfelek különböző formátumokban fogadják a számlákat, és a feldolgozott adatmennyiséget a számla feldolgozásának automatizálására. A nagy teljesítményű [optikai karakterfelismerési (OCR)](../computer-vision/concept-recognizing-text.md) képességeket ötvözi a részletes tanulási modellekkel, hogy kinyerje a legfontosabb információkat a számlákból angol nyelven. Kibontja a szöveget, a táblákat és az információkat, például az ügyfelet, a szállítót, a számla AZONOSÍTÓját, a számla esedékes dátumát, a teljes összeget, a számla összegét, az adó mennyiségét, a szállítást és a számlázást, Az előre elkészített számla API nyilvánosan elérhető az űrlap felismerő v 2.1 előzetes verziójában.

## <a name="what-does-the-invoice-service-do"></a>Mire szolgál a számlázási szolgáltatás?

A számla API kibontja a számlák legfontosabb mezőit, és egy strukturált, a JSON-válaszban visszaadja azokat. A számlák különböző formátumokból és minőségből származnak, beleértve a telefonnal rögzített képeket, a beolvasott dokumentumokat és a digitális PDF-fájlokat is. A számla API kibontja az összes ilyen számla strukturált kimenetét. 

![Contoso-számla – példa](./media/invoice-example.jpg)

## <a name="try-it-out"></a>Próbálja ki

Az űrlap-felismerő számlázási szolgáltatás kipróbálásához nyissa meg az online minta felhasználói felület eszközét:

> [!div class="nextstepaction"]
> [Előre elkészített modellek kipróbálása](https://fott-preview.azurewebsites.net/)

Szüksége lesz egy Azure-előfizetésre ([hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)) és egy [űrlap-felismerő erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) -végpontot és kulcsot az űrlap-felismerő számlázási szolgáltatás kipróbálásához. 

![Elemzett számlázási példa](./media/analyze-invoice.png)


### <a name="input-requirements"></a>Bemeneti követelmények 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>A számla elemzése művelet

A [számla elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291) művelet a számla egy képét vagy PDF-fájlját veszi fel, és Kinyeri a kamat értékét. A hívás egy válasz fejléc nevű mezőt ad vissza `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó eredmény-azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>Az elemzés számla eredményének lekérése művelet

A második lépés az elemzési [számla eredményének beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83) művelet hívása. Ez a művelet a számla elemzése művelettel létrehozott eredmény-azonosítót veszi figyelembe. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A kérések másodpercenkénti (RPS) arányának meghaladása érdekében használjon 3 – 5 másodperces intervallumot.

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | notStarted: az elemzési művelet nem indult el.<br /><br />fut: az elemzési művelet folyamatban van.<br /><br />sikertelen: az elemzési művelet meghiúsult.<br /><br />sikeres: az elemzési művelet sikeresen befejeződött.|

Ha az **állapot** mező értéke **sikeres** , a JSON-válasz tartalmazni fogja a számla megértésének eredményét, a kinyert táblákat és az opcionális szöveges felismerési eredményeket, ha szükséges. A számla megértésének eredménye a megnevezett mezőértékek szótára, ahol minden érték tartalmazza a kinyert szöveget, normalizált értéket, határolókerett, megbízhatóságot és a hozzájuk tartozó szó elemeit. A szöveg-felismerés eredményét vonalak és szavak hierarchiája rendezi, szöveg, határolókeret és bizalmas adatok formájában.

### <a name="sample-json-output"></a>Példa JSON-kimenetre

Az elemzési számla beolvasása műveletre adott válasz a számla strukturált ábrázolása lesz a kinyert információkkal együtt. Itt megtekintheti a [minta számlázási fájl](./media/sample-invoice.jpg) és a strukturált kimeneti [minta számlázási kimenetét](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json).

A JSON-kimenet 3 részből áll: 
* `"readResults"` a csomópont tartalmazza az összes felismert szöveget és kiválasztási jelet. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. 
* `"pageResults"` a csomópont tartalmazza a határoló mezőkkel, a megbízhatósággal és a "readResults" sorokra és szavakra mutató hivatkozásokat tartalmazó táblákat és cellákat.
* `"documentResults"` a csomópont tartalmazza a modell által felderített számla-specifikus értékeket. Itt megtalálhatja a számla összes mezőjét, például a számla AZONOSÍTÓját, a szállítást, a számlázást, az ügyfelet, az összesítést és sok mást.

## <a name="example-output"></a>Példa kimenetre

A számlázási szolgáltatás kibontja a szöveget, a táblákat és a 26 számla mezőt. Az alábbiakban láthatók a JSON-kimenetre adott számlán kinyert mezők (az alábbi kimenet ezt a [mintát](./media/sample-invoice.jpg)használja)  

|Név| Típus | Leírás | Szöveg | Érték (szabványosított kimenet) |
|:-----|:----|:----|:----| :----|
| CustomerName | sztring | Az ügyfél számlázása folyamatban van | Microsoft Corp |  |
| CustomerId | sztring | Az ügyfél hivatkozási azonosítója | CID-12345 |  |
| PurchaseOrder | sztring | Beszerzési rendelés hivatkozási száma | PO-3333 | |  |
| InvoiceId | sztring | Az adott számla azonosítója (gyakran "számla száma") | INV-100 | |  |
| InvoiceDate | dátum | A számla kiadásának dátuma | 11/15/2019 | 
| DueDate | dátum | A számla fizetési határideje esedékes | 12/15/2019 | 2019-12-15 | 2019-11-15 |
| Szállítónév | sztring | A számlát létrehozó szállító | CONTOSO LTD. | |
| VendorAddress | sztring | A szállító levelezési címe | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | sztring | A VendorAddress társított név | Contoso-központ | |
| CustomerAddress | sztring | Az ügyfél levelezési címe | 123 other St, Redmond WA, 98052 | |
| CustomerAddressRecipient | sztring | A ügyfélcímhez társított név | Microsoft Corp | |
| BillingAddress | sztring | Az ügyfél explicit számlázási címe | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | sztring | A BillingAddress társított név | Microsoft-szolgáltatások | |
| ShippingAddress | sztring | Az ügyfél explicit szállítási címe | 123 Ship St, Redmond WA, 98052 | |
| ShippingAddressRecipient | sztring | A ShippingAddress társított név | Microsoft-kézbesítés | |
| Részösszeg | szám | A számlán azonosított Részösszeg mező | $100,00 | 100 | 
| TotalTax | szám | A számlán azonosított összes adózási mező | $10,00 | 10 |
| InvoiceTotal | szám | A számlához tartozó új díjak összesen | $110,00 | 110 |
| AmountDue |  szám | A szállító által okozott teljes összeg | $610,00 | 610 |
| ServiceAddress | sztring | Az ügyfél explicit szolgáltatási címe vagy a tulajdonság címe | 123 Service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | sztring | A ServiceAddress társított név | Microsoft-szolgáltatások | |
| RemittanceAddress | sztring | Az ügyfél kifejezett átutalási vagy fizetési címe | 123 feladat St New York, NY, 10001 |  |
| RemittanceAddressRecipient | sztring | A RemittanceAddress társított név | Contoso-számlázás |  |
| ServiceStartDate | dátum | A szolgáltatási időszak első dátuma (például egy segédprogram számlázási szolgáltatásának időtartama) | 2019. 10. 14. | 2019-10-14 |
| ServiceEndDate | dátum | A szolgáltatási időszak záró dátuma (például egy segédprogram számlázási szolgáltatásának időtartama) | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | szám | Explicit, korábban nem fizetett egyenleg | $500,00 | 500 |


## <a name="next-steps"></a>További lépések

- Próbálja ki saját számláit és mintáit az [űrlap-felismerő minta felhasználói felületén](https://fott-preview.azurewebsites.net/).
- Az [űrlap](quickstarts/client-library.md) -felismerő gyors üzembe helyezésével megkezdheti a számla-feldolgozó alkalmazás írását a választott nyelven.

## <a name="see-also"></a>További információ

* [Mi a Form Recognizer?](./overview.md)
* [REST API dokumentációs dokumentumok](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)
