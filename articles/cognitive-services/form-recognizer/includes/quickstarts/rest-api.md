---
title: 'Rövid útmutató: Form Recognizer REST API'
description: A Form Recognizer REST API létrehozhat egy űrlapfeldolgozó alkalmazást, amely kulcs/érték párokat és táblaadatokat von ki az egyéni dokumentumokból.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: cd9cf0e4a2abec1ad29d5b34f3d6f473ca5bf71d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564492"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> Ez az útmutató cURL-t használ a REST API végrehajtására.

|[Form Recognizer REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) | [Azure REST API-referencia](/rest/api/azure/)|

## <a name="prerequisites"></a>Előfeltételek

* [a cURL telepítve](https://curl.haxx.se/windows/) van.
* [PowerShell 6.0+](/powershell/scripting/install/installing-powershell-core-on-windows)vagy hasonló parancssori alkalmazás.
* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* Egy Azure Storage-blob, amely betanító adatok egy halmazát tartalmazza. A [betanítás adatkészletének összeállítására](../../build-training-data-set.md) vonatkozó tippekért és lehetőségekért tekintse meg a betanítás adatkészletének összeállítása egyéni modellhez való összeállítását. Ebben a rövid útmutatóban a mintaadatkészlet **Betanítás** mappájában [található](https://go.microsoft.com/fwlink/?linkid=2090451) fájlokat használhatja (töltse le és bontsa ki a *sample_data.zip).*
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Form Recognizer-erőforrást, Form Recognizer erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás gombra.**
  * A létrehozott erőforrás kulcsának és végpontjának szüksége lesz az alkalmazás a Form Recognizer API-hoz való csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
  * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.
* Egy nyugtát mutató kép URL-címe. Ehhez a rövid [útmutatóhoz használhat](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) egy mintaképet.
* Egy névjegykártyát képhez tartozó URL-cím. Ehhez a rövid [útmutatóhoz használhat](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) egy mintaképet.
* Egy számla képének URL-címe. Ehhez a rövid [útmutatóhoz használhat](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) egy mintadokumentumot.

## <a name="analyze-layout"></a>Elrendezés elemzése

A dokumentumokkal Form Recognizer és kinyerheti a táblákat, a kijelölési jeleket, a szöveget és a struktúrát a dokumentumokban anélkül, hogy modellt kellene betanítanunk. Az elrendezéskinyerésről az Elrendezés fogalmi [útmutatóban található további információ.](../../concept-layout.md) A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a `{Endpoint}` saját előfizetésével Form Recognizer végpontot.
1. Cserélje `{subscription key}` le a helyére az előző lépésben kimáselt előfizetői kulcsot.
1. Cserélje `\"{your-document-url}` le a helyére a példa URL-címek valamelyikét.

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

---

Egy olyan választ fog kapni, amely tartalmazza az `202 (Success)` **Operation-Location fejlécet.** A fejléc értéke tartalmaz egy műveletazonosítót, amely az aszinkron művelet állapotának lekérdezésére és az eredmények lekérdezhető. A következő példában az utána következő sztring `analyzeResults/` a művelet azonosítója.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Elrendezési eredmények lekérte

Az Analyze **[Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)** API hívása után hívja meg a **[Get Analyze Layout Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult)** API-t a művelet és a kinyert adatok állapotának lehívásához. A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a `{Endpoint}` saját előfizetésével Form Recognizer végpontot.
1. Cserélje `{subscription key}` le a helyére az előző lépésben kimáselt előfizetői kulcsot.
1. Cserélje `{resultId}` le a helyére az előző lépésben lekért műveletazonosítót.
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-results"></a>Az eredmények vizsgálata

`200 (success)`JSON-tartalommal fog választ kapni.

Tekintse meg az alábbi számlaképet és a hozzá tartozó JSON-kimenetet.
* A csomópont minden szövegsort tartalmaz, a megfelelő határolókeret elhelyezésével `"readResults"` együtt az oldalon.
* A csomópont (a v2.1 előzetes verzióban) minden kijelölési jelet (jelölőnégyzetet, választójelet) és azt megjeleníti, hogy az állapota "selected" (kiválasztva) vagy `"selectionMarks"` "unselected" (nincs kiválasztva).
* A `"pageResults"` szakasz a kinyert táblákat tartalmazza. A rendszer minden táblához kinyeri a szöveg, a sor és az oszlop indexét, a sor- és oszlopdiagramot, a határolókeretet stb.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="A Contoso projekt utasításának dokumentuma egy táblával.":::

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

Ez a kimenet az egyszerűség kedvéért le lett rövidülve. Tekintse meg [a teljes mintakimenetet a GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

Ez a kimenet az egyszerűség kedvéért le lett rövidülve. Tekintse meg [a teljes mintakimenetet a GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

---

## <a name="analyze-receipts"></a>Nyugták elemzése

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki közös mezőket az USA-nyugtákból egy előre betanított nyugtamodell használatával. A nyugták elemzésével kapcsolatos további információkért lásd a [nyugta fogalmi útmutatóját.](../../concept-receipts.md) A nyugták elemzésének elkezdéséhez hívja meg az **[Analyze Receipt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)** API-t az alábbi cURL-paranccsal. A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a `{Endpoint}` saját előfizetéséhez Form Recognizer végpontot.
1. Cserélje `{your receipt URL}` le a helyére a nyugtakép URL-címét.
1. Cserélje le a helyére az előző lépésben `{subscription key>` kimáselt előfizetői kulcsot.

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

---

Egy olyan választ fog kapni, amely tartalmazza az `202 (Success)` **Operation-Location fejlécet.** A fejléc értéke tartalmaz egy műveletazonosítót, amely az aszinkron művelet állapotának lekérdezésére és az eredmények lekérdezhető. A következő példában az utána következő sztring `operations/` a művelet azonosítója.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>A nyugta eredményeinek le kérése

Az **Analyze Receipt** API hívása után hívja meg a Get Analyze **[Receipt Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult)** API-t a művelet és a kinyert adatok állapotának lehívásához. A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a saját előfizetési kulcsával `{Endpoint}` Form Recognizer végpontot. Ezt a saját erőforrásának Áttekintés Form Recognizer **találhatja** meg.
1. Cserélje `{operationId}` le a helyére az előző lépésben lekért műveletazonosítót.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-response"></a>A válasz vizsgálata

Egy JSON-kimenettel kapott `200 (Success)` választ fog kapni. Az első mező `"status"` () a művelet állapotát jelzi. Ha a művelet nem fejeződött be, a értéke vagy lesz, és újra meg kell hívnia az `"status"` `"running"` API-t `"notStarted"` manuálisan vagy egy szkripttel. Javasoljuk, hogy a hívások között legalább egy másodperces időközt javasoljon.

A csomópont tartalmazza az összes felismert szöveget (ha az opcionális `"readResults"` *includeTextDetails* paramétert a következőre adja meg: `true` ). A szöveg lap, sor, majd egyéni szavak szerint van rendezve. A csomópont tartalmazza a modell által felderített `"documentResults"` nyugtaspecifikus értékeket. Itt találhat olyan hasznos kulcs/érték párokat, mint az adó, az összeg, a kereskedő címe stb.

Tekintse meg az alábbi nyugtaképet és a hozzá tartozó JSON-kimenetet.

![Nyugtát a Contoso áruházból](../../media/contoso-allinone.jpg)

Ez a kimenet az olvashatóság érdekében le lett rövidülve. Tekintse meg [a teljes mintakimenetet a GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="analyze-business-cards"></a>Névjegykártyák elemzése

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki általános mezőket angol névjegykártyákból egy előre betanított modell használatával. A névjegykártya-elemzéssel kapcsolatos további információkért lásd a [névjegykártyák fogalmi útmutatóját.](../../concept-business-cards.md) A névjegykártya elemzésének elkezdéséhez hívja meg az **[Analyze Business Card](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)** API-t az alábbi cURL-paranccsal. A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a `{Endpoint}` saját előfizetéséhez Form Recognizer végpontot.
1. Cserélje `{your business card URL}` le a helyére a nyugtakép URL-címét.
1. Cserélje le a helyére az előző lépésben `{subscription key}` kimáselt előfizetői kulcsot.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

Egy olyan választ fog kapni, amely tartalmazza `202 (Success)` az **Operation-Location fejlécet.** A fejléc értéke tartalmaz egy műveletazonosítót, amely segítségével lekérdezheti az aszinkron művelet állapotát, és lekérdezheti az eredményeket.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Névjegykártya-eredmények leése

Az Analyze Business **Card** API hívása után hívja meg a **[Get Analyze Business Card Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult)** API-t a művelet és a kinyert adatok állapotának lehívásához. A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a saját előfizetési kulcsával `{Endpoint}` Form Recognizer végpontot. Ezt a saját erőforrásáttekintés Form Recognizer **lapon** találja.
1. Cserélje `{resultId}` le a helyére az előző lépésben lekért műveletazonosítót.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

Egy `200 (Success)` JSON-kimenettel kapott választ fog kapni.

A `"readResults"` csomópont tartalmazza az összes felismert szöveget. A szöveg lap, sor, majd egyéni szavak szerint van rendezve. A csomópont a modell által felderített `"documentResults"` névjegykártya-specifikus értékeket tartalmazza. Itt találhat hasznos kapcsolattartási adatokat, például a vállalat nevét, vezetéknevét, vezetéknevét, telefonszámát stb.

![A Contoso vállalat névjegykártyája](../../media/business-card-english.jpg)

Ez a JSON-mintakimenet az olvashatóság érdekében lerövidítve lett. Tekintse meg [a teljes mintakimenetet a GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

A szkript a konzolra ír ki válaszokat, amíg a **Névjegykártya elemzése** művelet be nem fejeződik.

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="analyze-invoices"></a>Számlák elemzése

### <a name="v21-preview"></a>[2.1-es előzetes verzió](#tab/v2-1)

Egy számla elemzésének elkezdését az alábbi cURL-paranccsal kezdheti meg. A számlaelemzéssel kapcsolatos további információkért lásd a számla [fogalmi útmutatóját.](../../concept-invoices.md) A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a `{Endpoint}` saját előfizetésével Form Recognizer végpontot.
1. Cserélje `{your invoice URL}` le a helyére egy számladokumentum URL-címét.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

Egy olyan választ fog kapni, amely tartalmazza az `202 (Success)` **Operation-Location fejlécet.** A fejléc értéke tartalmaz egy műveletazonosítót, amely az aszinkron művelet állapotának lekérdezésére és az eredmények lekérdezhető.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Számlaeredmények lekérte

Az Analyze **[Invoice](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)** API hívása után hívja meg a **[Számla](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83)** eredményének lehívása API-t a művelet és a kinyert adatok állapotának lehívásához. A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a saját előfizetési kulcsával `{Endpoint}` Form Recognizer végpontot. Ezt a saját erőforrásáttekintés Form Recognizer **lapon** találja.
1. Cserélje `{resultId}` le a helyére az előző lépésben lekért műveletazonosítót.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

Egy `200 (Success)` JSON-kimenettel kapott választ fog kapni.

* A mező a számlából kinyert összes szöveges `"readResults"` sort tartalmazza.
* A tartalmazza a számlából kinyert táblákat és `"pageResults"` kijelölési jeleket.
* A `"documentResults"` mező a számla legfontosabb részeinek kulcs-érték információit tartalmazza.

Tekintse meg az alábbi számladokumentumot és a hozzá tartozó JSON-kimenetet.

* [Mintaszámla](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

Ez a JSON-tartalom az olvashatóság érdekében lerövidítve lett. Tekintse meg [a teljes mintakimenetet a GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...
            }
        }]
    }
}
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="analyze-id-document"></a>Azonosító elemzése dokumentum

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

Egy azonosítási dokumentum elemzésének elkezdését az alábbi cURL-paranccsal kezdheti meg. Az identitásdokumentumok elemzésével kapcsolatos további információkért lásd az [identitásdokumentumok fogalmi útmutatóját.](../../concept-identification-cards.md) Egy identitásdokumentum elemzésének elkezdéséhez hívja meg az **[Analyze ID https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7738978e467c5fb8707) Document]** API-t az alábbi cURL-paranccsal. A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a `{endpoint}` saját előfizetéséhez Form Recognizer végpontot.
1. Cserélje `{your ID document URL}` le a helyére a nyugtakép URL-címét.
1. Cserélje `{subscription key}` le a helyére az előző lépésben kimáselt előfizetői kulcsot.

### <a name="v21-previewtabv2-1"></a>[v2.1 előzetes verzió] (#tab/v2-1

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your  ID document URL}'}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

Egy olyan választ fog `202 (Success)` kapni, amely tartalmaz egy **Operation-Location fejlécet.** A fejléc értéke tartalmaz egy eredményazonosítót, amely segítségével lekérdezheti az aszinkron művelet állapotát, és lekérdezheti az eredményeket. A következő példában az utána következő sztring `analyzeResults/` az eredmény azonosítója.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/0c6cb19e-538f-4b8d-98b7-e105c9995ba6
```

### <a name="get-the-analyze-id-document-result"></a>Az Azonosító elemzése dokumentum eredményének lekérte

Az Analyze ID **Document** API hívása után hívja meg a **[Get Analyze Id Document Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822)** API-t a művelet és a kinyert adatok állapotának lehívásához.  A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a saját előfizetési kulcsával `{endpoint}` Form Recognizer végpontot. Ezt a saját erőforrásának Áttekintés Form Recognizer **találhatja** meg.
1. Cserélje `{resultId}` le a helyére az előző lépésben lekért műveletazonosítót.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

Egy JSON-kimenettel kapott `200 (Success)` választ fog kapni. Az első mező `"status"` () a művelet állapotát jelzi. Ha a művelet nem fejeződött be, a értéke vagy lesz, és újra meg kell hívnia az API-t manuálisan vagy egy szkripttel, amíg meg nem `"status"` `"running"` kapja az `"notStarted"` `succeeded` értéket. Javasoljuk, hogy a hívások között legalább egy másodperces időközt javasoljon.

* A mező az identitásdokumentumból kinyert `"readResults"` összes szövegsort tartalmazza.
* A mező objektumok tömböt tartalmaz, amelyek a bemeneti dokumentumban észlelt `"documentResults"` azonosítódokumentumot képviselik.

Az alábbiakban egy mintaidentitási dokumentum és a hozzá tartozó JSON-kimenet látható

* :::image type="content" source="https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-license.jpg" alt-text="minta illesztői licenc":::

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-04-13T17:24:52Z",
    "lastUpdatedDateTime": "2021-04-13T17:24:55Z",
    "analyzeResult": {
      "version": "2.1.0",
      "readResults": [
        {
          "page": 1,
          "angle": -0.2823,
          "width": 450,
          "height": 294,
          "unit": "pixel"
        }
      ],
      "documentResults": [
        {
          "docType": "prebuilt:idDocument:driverLicense",
          "docTypeConfidence": 0.995,
          "pageRange": [
            1,
            1
          ],
          "fields": {
            "Address": {
              "type": "string",
              "valueString": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "text": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "boundingBox": [
                158,
                151,
                326,
                151,
                326,
                177,
                158,
                177
              ],
              "page": 1,
              "confidence": 0.965
            },
            "Country": {
              "type": "country",
              "valueCountry": "USA",
              "confidence": 0.99
            },
            "DateOfBirth": {
              "type": "date",
              "valueDate": "1958-01-06",
              "text": "01/06/1958",
              "boundingBox": [
                187,
                133,
                272,
                132,
                272,
                148,
                187,
                149
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DateOfExpiration": {
              "type": "date",
              "valueDate": "2020-08-12",
              "text": "08/12/2020",
              "boundingBox": [
                332,
                230,
                414,
                228,
                414,
                244,
                332,
                245
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DocumentNumber": {
              "type": "string",
              "valueString": "LICWDLACD5DG",
              "text": "LIC#WDLABCD456DG",
              "boundingBox": [
                162,
                70,
                307,
                68,
                307,
                84,
                163,
                85
              ],
              "page": 1,
              "confidence": 0.99
            },
            "FirstName": {
              "type": "string",
              "valueString": "LIAM R.",
              "text": "LIAM R.",
              "boundingBox": [
                158,
                102,
                216,
                102,
                216,
                116,
                158,
                116
              ],
              "page": 1,
              "confidence": 0.985
            },
            "LastName": {
              "type": "string",
              "valueString": "TALBOT",
              "text": "TALBOT",
              "boundingBox": [
                160,
                86,
                213,
                85,
                213,
                99,
                160,
                100
              ],
              "page": 1,
              "confidence": 0.987
            },
            "Region": {
              "type": "string",
              "valueString": "Washington",
              "confidence": 0.99
            },
            "Sex": {
              "type": "gender",
              "valueGender": "M",
              "text": "M",
              "boundingBox": [
                226,
                190,
                232,
                190,
                233,
                201,
                226,
                201
              ],
              "page": 1,
              "confidence": 0.99
            }
          }
        }
      ]
    }
  }
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Egy egyéni modell betanítása egy Azure Storage-blobban lévő betanításadat-készletre lesz szüksége. Legalább öt kitöltött űrlapra (PDF-dokumentumokra és/vagy képekre) van szükség, amelyek azonos típusúak/szerkezetűek. A [betanítás adatainak összeállítására](../../build-training-data-set.md) vonatkozó tippekért és lehetőségekért tekintse meg a betanítás adatkészletének összeállítása egyéni modellhez való összeállítását.

A címkével jelölt adatok nélküli betanítás az alapértelmezett művelet, és egyszerűbb. Másik lehetőségként manuálisan is megcímkézheti a betanítás egyes adatait, vagy akár az összeset is. Ez egy összetettebb folyamat, de jobb betanított modellt ad vissza.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel, például a mintacímkéző eszközzel [Form Recognizer betaníthatja.](../../quickstarts/label-tool.md)


### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Ha betanít egy Form Recognizer az Azure Blob-tárolóban lévő **[](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** dokumentumokkal, hívja meg az Egyéni modell betanítás API-ját a következő cURL-parancs futtatásával. A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a `{Endpoint}` saját előfizetéséhez Form Recognizer végpontot.
1. Cserélje le a helyére az előző lépésben `{subscription key}` kimáselt előfizetői kulcsot.
1. Cserélje le a helyére az Azure Blob Storage-tároló közös hozzáférésű `{SAS URL}` jogosultság jogosultságának (SAS) URL-címét. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-lekérés":::

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---

Egy Location fejléccel kapott `201 (Success)` **választ** fog kapni. Ennek a fejlécnek az értéke a betanított új modell azonosítója.

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

A címkékkel való betanításhoz speciális címkeinformációs fájlokra () van szükség a Blob Storage-tárolóban `\<filename\>.pdf.labels.json` a betanítás dokumentumai mellett. A [Form Recognizer mintacímkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkefájlok létrehozásához. Ha már megvan, hívja **[](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** meg az Egyéni modell betanítása API-t, a paraméter pedig legyen a `"useLabelFile"` `true` JSON-törzsben.

A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a saját előfizetésével Form Recognizer `{Endpoint}` végpontot.
1. Cserélje `{subscription key}` le a helyére az előző lépésben kimáselt előfizetői kulcsot.
1. Cserélje `{SAS URL}` le a helyére az Azure Blob Storage-tároló közös hozzáférésű jogosultsága (SAS) URL-címét. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-lekérés":::

### <a name="v21-preview"></a>[2.1-es előzetes verzió](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

---

Egy Location fejléccel kapott `201 (Success)` **választ** fog kapni. A fejléc értéke a betanított új modell azonosítója.

### <a name="get-training-results"></a>Betanítás eredményeinek lekérte

Miután elindította a betanítás műveletet, egy új, **[Egyéni](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** modell lekért művelettel ellenőrizheti a betanítás állapotát. A betanítás állapotának ellenőrzéshez adja át a modellazonosítót az API-hívásnak:

1. Cserélje le a helyére a saját előfizetési kulcsával `{Endpoint}` Form Recognizer végpontot.
1. Cserélje le `{subscription key}` a helyére az előfizetői kulcsot
1. Cserélje `{model ID}` le a helyére az előző lépésben kapott modellazonosítót

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

A JSON-törzset a következő formátumban `200 (Success)` kapja meg. Figyelje meg a `"status"` mezőt. Ez lesz az érték a `"ready"` betanítás befejezése után. Ha a modell betanítása nem fejeződött be, újra le kellkérdezni a szolgáltatást a parancs újrafuttatása által. Javasoljuk, hogy a hívások között legalább egy másodperces időközt javasoljon.

A `"modelId"` mező a betanított modell azonosítóját tartalmazza. Erre szüksége lesz a következő lépéshez.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ezután az újonnan betanított modellel fog elemezni egy dokumentumot, és kinyerni belőle kulcs-érték párokat és táblákat. Hívja meg **[az Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)** API-t a következő cURL-parancs futtatásával. A parancs futtatása előtt tegye a következő módosításokat:

1. Cserélje le a helyére a saját előfizetési Form Recognizer `{Endpoint}` lekért végpontot. Ezt a saját erőforrásáttekintés Form Recognizer **lapon** találja.
1. Cserélje le a helyére az előző szakaszban `{model ID}` kapott modellazonosítót.
1. Cserélje `{SAS URL}` le a helyére az Azure Storage-ban található fájl SAS URL-címét. Kövesse a Betanítás szakasz lépéseit, de ahelyett, hogy a teljes blobtárolóhoz sas URL-címet kap, az elemezni kívánt fájlhoz is be kell szereznie egyet.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

---

Egy `202 (Success)` **Operation-Location fejléccel kapcsolatos választ fog** kapni. A fejléc értéke tartalmaz egy eredményazonosítót, amely az Elemzés művelet eredményeinek nyomon követésére használható. Mentse ezt az eredményazonosítót a következő lépéshez.

### <a name="get-the-analyze-results"></a>Az eredmények elemzése

Hívja meg a Get **[Analyze Form Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult)** API-t az Elemzés művelet eredményeinek lekérdezéséhez.

1. Cserélje le a helyére az előfizetési kulcsból Form Recognizer `{Endpoint}` végpontot. Ezt a saját erőforrásának Áttekintés Form Recognizer **találhatja** meg.
1. Cserélje `{result ID}` le a helyére az előző szakaszban kapott azonosítót.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

### <a name="v21-preview"></a>[2.1-es előzetes verzió](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

A JSON-törzset a következő formátumban `200 (Success)` kapja meg. A kimenet az egyszerűség kedvéért le lett rövidülve. Figyelje `"status"` meg a lenti mezőt. Ez lesz az érték, `"succeeded"` amikor az Elemzés művelet befejeződött. Ha az Elemzés művelet nem fejeződött be, újra le kellkérdezni a szolgáltatást a parancs újrafuttatása által. Javasoljuk, hogy a hívások között legalább egy másodperces időközt javasoljon.

A címkék nélkül betanított egyéni modellekben a kulcs/érték pár társításai és táblái a `"pageResults"` JSON-kimenet csomópontján vannak. A címkékkel betanított egyéni modellekben a kulcs/érték pár társításai a csomóponton `"documentResults"` vannak. Ha egyszerű szövegkinyerést is megadott az *includeTextDetails* URL-paraméteren keresztül, akkor a csomópont a dokumentumban található összes szöveg tartalmát és pozícióit `"readResults"` is meg fogja mutatni.

Ez a JSON-mintakimenet az egyszerűség kedvéért lett lerövidítve. Tekintse meg [a teljes mintakimenetet a GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json)

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          },
          ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ],
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          },
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

---

### <a name="improve-results"></a>Eredmények javítása

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>Egyéni modellek kezelése

### <a name="get-a-list-of-custom-models"></a>Egyéni modellek listájának lekért listája

Az alábbi **[parancsban](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModels)** az Egyéni modellek listása API használatával lekért lista az előfizetéshez tartozó összes egyéni modellről.

1. Cserélje le a helyére a `{Endpoint}` saját előfizetéséhez Form Recognizer végpontot.
1. Cserélje le a helyére az előző lépésben `{subscription key}` kimáselt előfizetői kulcsot.

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Sikeres választ fog kapni, amely az alábbihoz hasonló `200` JSON-adatokat tartalmaz. A `"modelList"` elem tartalmazza az összes létrehozott modellt és azok adatait.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Adott modell lekérte

Egy adott egyéni modell részletes információinak **[](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** lekérésére használja az egyéni modell beolvasása API-t a következő parancsban.

1. Cserélje le a helyére a `{Endpoint}` saját előfizetéséhez Form Recognizer végpontot.
1. Cserélje le a helyére az előző lépésben `{subscription key}` kimáselt előfizetői kulcsot.
1. Cserélje le a helyére a keresni kívánt egyéni modell `{modelId}` azonosítóját.

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Sikeres választ fog kapni, amely az alábbihoz hasonló `200` JSON-adatokat tartalmaz.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrásfiókból

Az azonosítójára való hivatkozásokkal törölheti is a modellt a fiókjából. Ez a parancs az **[egyéni modell törlése API-t](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/DeleteCustomModel)** hívja meg az előző szakaszban használt modell törléséhez.
code
1. Cserélje le a helyére a `{Endpoint}` saját előfizetéséhez Form Recognizer végpontot.
1. Cserélje le a helyére az előző lépésben `{subscription key}` kimáselt előfizetői kulcsot.
1. Cserélje le a helyére a keresni kívánt egyéni modell `{modelId}` azonosítóját.

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Sikeres választ fog kapni, amely jelzi, hogy a `204` modell törlésre van megjelölve. A modell-összetevők 48 órán belül el lesznek távolítva.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a modellt Form Recognizer REST API és különböző módokon elemezte az űrlapokat. Ezután tekintse meg a referenciadokumentációt a Form Recognizer API részletes vizsgálatához.

> [!div class="nextstepaction"]
> [REST API referenciadokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)

* [Mi a Form Recognizer?](../../overview.md)
