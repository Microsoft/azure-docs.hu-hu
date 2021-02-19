---
title: 'Gyors útmutató: űrlap-felismerő REST API'
description: Az űrlap-felismerő REST API használatával létrehozhat egy űrlap-feldolgozási alkalmazást, amely Kinyeri a kulcs/érték párokat és a tábla adatait az egyéni dokumentumokból.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/15/2020
ms.author: lajanuar
ms.openlocfilehash: 08026ea04e7f997ae75f6bdb2cea09e1f41e26a3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101102852"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> Ez az útmutató a cURL használatával REST API hívásokat hajt végre. A [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/FormRecognizer/rest) is szerepel egy mintakód, amely bemutatja, hogyan hívhatja meg a REST API-kat a Python használatával.

## <a name="prerequisites"></a>Előfeltételek

* a [fürt](https://curl.haxx.se/windows/) telepítve van.
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört. Ebben a rövid útmutatóban használhatja a [minta adathalmaz](https://go.microsoft.com/fwlink/?linkid=2090451) (letöltés és kibontás *sample_data.zip*) **alatt található fájlokat** .
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hozzon létre egy űrlap-felismerő erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Az Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
  * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
  * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* Egy nyugtát ábrázoló rendszerkép URL-címe. Ehhez a rövid útmutatóhoz [minta képet](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) is használhat.
* Egy névjegykártya-rendszerkép URL-címe. Ehhez a rövid útmutatóhoz [minta képet](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) is használhat.
* Egy számla rendszerképének URL-címe. Ehhez a rövid útmutatóhoz [minta dokumentumot](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) is használhat.

## <a name="analyze-layout"></a>Elrendezés elemzése

Az űrlap-felismerő használatával táblázatokat, kiválasztási jeleket, szöveget és struktúrát elemezheti és kinyerheti a dokumentumokban anélkül, hogy egy modellt kellene betanítania. További információ az elrendezés kinyeréséről: az [elrendezés fogalmi útmutatója](../../concept-layout.md). A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le az `{subscription key}` elemet az előző lépésből másolt előfizetési kulcsra.
1. Cserélje le az értékét `\"{your-document-url}` a példa URL-címek egyikére.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

---

Olyan választ fog kapni `202 (Success)` , amely tartalmazza a am **Operation-Location** fejlécet. A fejléc értéke olyan műveleti azonosítót tartalmaz, amelynek segítségével lekérdezheti az aszinkron művelet állapotát, és lekérheti az eredményeket. A következő példában az azt követő karakterlánc a `analyzeResults/` művelet azonosítója.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Elrendezés eredményeinek beolvasása

Az elemzési **[elrendezési](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)** API meghívása után hívja meg az elemzési **[elrendezés eredményének beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult)** API-t a művelet és a kinyert adatmennyiség állapotának lekéréséhez. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le az `{subscription key}` elemet az előző lépésből másolt előfizetési kulcsra.
1. Cserélje le az `{resultId}` elemet az előző lépésben szereplő műveleti azonosítóra.
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-results"></a>Az eredmények vizsgálata

A rendszer a `200 (success)` JSON-tartalommal kapcsolatos választ küld.

Tekintse meg a következő számla képét és a hozzá tartozó JSON-kimenetet.
* A `"readResults"` csomópont a szöveg minden sorát tartalmazza a megfelelő határolókeret elhelyezésével az oldalon. 
* A `"selectionMarks"` csomópont (v 2.1 előzetes verzió) megjeleníti az összes kijelölési jelet (jelölőnégyzet, választógomb), valamint azt, hogy az állapota "kijelölt" vagy "nem kijelölt". 
* A `"pageResults"` szakasz tartalmazza a kinyert táblákat. Minden táblához a szöveg, a sor és az oszlop indexe, a sor és az oszlop átfedése, a határolókeret és egyebek is ki lesznek csomagolva.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Contoso Project-utasítás dokumentum táblával.":::

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

Ezt a kimenetet az egyszerűség kedvéért lerövidítjük. Tekintse [meg a teljes minta kimenetet a githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

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

Ezt a kimenetet az egyszerűség kedvéért lerövidítjük. Tekintse [meg a teljes minta kimenetet a githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

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

## <a name="analyze-invoices"></a>Számlák elemzése

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

A számla elemzésének megkezdéséhez használja az alábbi cURL-parancsot. A számlázási elemzéssel kapcsolatos további információkért tekintse meg a [számla fogalmi útmutatóját](../../concept-invoices.md). A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le a `{your invoice URL}` címet egy számlázási dokumentum URL-címére.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

Olyan választ fog kapni `202 (Success)` , amely tartalmazza a am **Operation-Location** fejlécet. A fejléc értéke olyan műveleti azonosítót tartalmaz, amelynek segítségével lekérdezheti az aszinkron művelet állapotát, és lekérheti az eredményeket.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Számla eredményeinek beolvasása

Miután megismerte az elemzési **[számla](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** API-t, hívja meg a **[Get elemzése számla eredménye](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** API-t a művelet és a kinyert adatmennyiség állapotának lekéréséhez. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` az helyére az űrlapot felismerő előfizetési kulccsal beszerzett végpontot. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le az `{resultId}` elemet az előző lépésben szereplő műveleti azonosítóra.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer a `200 (Success)` JSON-kimenettel kapcsolatos választ küld. 
* A `"readResults"` mező a számlából kinyert szöveg minden sorát tartalmazza.
* A `"pageResults"` tartalmazza a számlából kinyert táblákat és választási jeleket.
* A `"documentResults"` mező a számla legfontosabb részeire vonatkozó kulcs/érték információkat tartalmazza.

Tekintse meg az alábbi számlázási dokumentumot és a hozzá tartozó JSON-kimenetet. 

* [Minta számla](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

Ez a JSON-tartalom le lett rövidítve az olvashatóság érdekében. Tekintse [meg a teljes minta kimenetet a githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json).

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

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Egyéni modell betanításához egy Azure Storage-blobban be kell állítania a betanítási adatkészletet. Ugyanahhoz a típushoz vagy struktúrához legalább öt kitöltött űrlapra (PDF-dokumentumra és/vagy lemezképre) van szükség. A betanítási adataival kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört.

A címkézett adatbevitel nélküli képzés az alapértelmezett művelet, és egyszerűbb. Azt is megteheti, hogy manuálisan is megcímkézi a betanítási adatait. Ez egy összetettebb folyamat, de jobban betanított modellt eredményez.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például az [űrlap-felismerő minta feliratozási eszközét](../../quickstarts/label-tool.md).


### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Ha az Azure Blob-tárolóban található dokumentumokkal szeretne betanítani egy űrlap-felismerő modellt, hívja meg az **[Egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API-ját a következő curl-parancs futtatásával. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le az `{subscription key}` elemet az előző lépésből másolt előfizetési kulcsra.
1. Cserélje le `{SAS URL}` az-t az Azure Blob Storage-tároló megosztott hozzáférési aláírása (SAS) URL-címére. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-cím lekérése":::

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---

A `201 (Success)` Válasz egy **Location** fejlécet kap. Ennek a fejlécnek az értéke a betanított új modell azonosítója.

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

Ha címkéket szeretne betanítani, `\<filename\>.pdf.labels.json` a blob Storage-tárolóban speciális címke-információs fájlokkal () kell rendelkeznie a betanítási dokumentumok mellett. Az [űrlap-felismerő minta címkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkék létrehozásához. Ha megkapta őket, meghívhatja az **[Egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** -API-t a `"useLabelFile"` `true` JSON-törzsben beállított paraméterrel.

A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le az `{subscription key}` elemet az előző lépésből másolt előfizetési kulcsra.
1. Cserélje le `{SAS URL}` az-t az Azure Blob Storage-tároló megosztott hozzáférési aláírása (SAS) URL-címére. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-cím lekérése":::

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

---

A `201 (Success)` Válasz egy **Location** fejlécet kap. Ennek a fejlécnek az értéke a betanított új modell azonosítója.

### <a name="get-training-results"></a>Képzési eredmények beolvasása

Miután elindította a vonatok műveletét, új műveletet fog használni, **[Egyéni modell beszerzésével](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** ellenőrizhető a betanítási állapot. Adja át a modell AZONOSÍTÓját ebbe az API-hívásba a betanítási állapot megtekintéséhez:

1. Cserélje le `{Endpoint}` az helyére az űrlapot felismerő előfizetési kulccsal beszerzett végpontot.
1. Lecserélés az `{subscription key}` előfizetési kulccsal
1. Cserélje le az `{model ID}` t az előző lépésben kapott modell-azonosítóra.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

`200 (Success)`A következő formátumban kap választ egy JSON-törzsből. Figyelje meg a `"status"` mezőt. Ez az érték a `"ready"` betanítás befejezése után lesz. Ha a modell nem fejeződött be, a parancs újbóli futtatásával újra le kell kérdezni a szolgáltatást. Javasoljuk, hogy a hívások között egy másodperc vagy több intervallum legyen.

A `"modelId"` mező tartalmazza a betanított modell azonosítóját. Ezt a következő lépéshez kell megadnia.

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

Ezután az újonnan betanított modellt fogja használni a dokumentumok elemzéséhez és a kulcs-érték párok és táblák kinyeréséhez. Hívja meg az **[elemzés űrlap](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API-t a következő curl-parancs futtatásával. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` az elemet az űrlap-felismerő előfizetési kulcsból beszerzett végpontra. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le az értékét az `{model ID}` előző szakaszban kapott modell-azonosítóra.
1. Cserélje le `{SAS URL}` egy sas URL-címet a fájlra az Azure Storage-ban. Kövesse a betanítás szakasz lépéseit, de ahelyett, hogy a teljes blob-tároló SAS URL-címét beolvassa, szerezze be egyet az elemezni kívánt fájlhoz.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

---

Választ fog kapni egy `202 (Success)` **műveleti hely** fejlécével. Ennek a fejlécnek az értéke tartalmazza az elemzési művelet eredményeinek nyomon követésére használt eredmény-azonosítót. Mentse a következő lépés eredmény-AZONOSÍTÓját.

### <a name="get-the-analyze-results"></a>Az elemzés eredményeinek beolvasása

Az elemzési művelet eredményeinek lekérdezéséhez hívja meg az elemzési űrlap beolvasása **[eredmény](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeFormResult)** API-t.

1. Cserélje le `{Endpoint}` az elemet az űrlap-felismerő előfizetési kulcsból beszerzett végpontra. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le az `{result ID}` azonosítót az előző szakaszban kapott azonosítóra.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

`200 (Success)`A következő formátumban kap választ egy JSON-törzsből. A kimenet lerövidítve az egyszerűség kedvéért. Figyelje meg a `"status"` mező alján található mezőt. Ez az érték lesz az elemzési `"succeeded"` művelet befejezésekor. Ha az elemzési művelet nem fejeződött be, újra le kell kérdezni a szolgáltatást a parancs újbóli futtatásával. Javasoljuk, hogy a hívások között egy másodperc vagy több intervallum legyen.

A címkék nélkül betanított egyéni modellek esetében a kulcs/érték párok társításai és táblái a `"pageResults"` JSON-kimenet csomópontjában találhatók. A címkékkel rendelkező egyéni modellekben a kulcs/érték párok társítása a `"documentResults"` csomópontban található. Ha az egyszerű szöveg kinyerését is a *includeTextDetails* URL-cím paraméterrel adta meg, akkor a `"readResults"` csomópont megjeleníti a dokumentumban lévő összes szöveg tartalmát és pozícióit.

Ez a JSON-kimenet lerövidítve az egyszerűség kedvéért. Tekintse [meg a teljes minta kimenetet a githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json).

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

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

### <a name="improve-results"></a>Az eredmények javítása

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="analyze-receipts"></a>Visszaigazolások elemzése

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az Egyesült államokbeli nyugták közös mezőit egy előre képzett beérkezési modell használatával. További információ a bevételezési elemzésről: a [nyugták fogalmi útmutatója](../../concept-receipts.md). A nyugták elemzésének megkezdéséhez hívja meg az **[elemzés visszaigazolása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync)** API-t az alábbi curl-parancs használatával. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le a értékét `{your receipt URL}` egy nyugtát ábrázoló rendszerkép URL-címére.
1. Cserélje le az `{subscription key>` elemet az előző lépésből másolt előfizetési kulcsra.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

---

Olyan választ fog kapni `202 (Success)` , amely tartalmazza a am **Operation-Location** fejlécet. A fejléc értéke olyan műveleti azonosítót tartalmaz, amelynek segítségével lekérdezheti az aszinkron művelet állapotát, és lekérheti az eredményeket. A következő példában az azt követő karakterlánc a `operations/` művelet azonosítója.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>A Bevételezés eredményeinek beolvasása

Az **elemzés visszaigazolási** API meghívása után hívja meg az **[elemzés visszaigazolásának eredményét](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult)** API-t a művelet és a kinyert adatmennyiség állapotának lekéréséhez. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` az helyére az űrlapot felismerő előfizetési kulccsal beszerzett végpontot. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le az `{operationId}` elemet az előző lépésben szereplő műveleti azonosítóra.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer a `200 (Success)` JSON-kimenettel kapcsolatos választ küld. Az első mező `"status"` jelzi a művelet állapotát. Ha a művelet nem fejeződött be, a vagy a érték lesz, `"status"` `"running"` `"notStarted"` és az API-t manuálisan vagy parancsfájlon keresztül kell meghívni. Javasoljuk, hogy a hívások között egy másodperc vagy több intervallum legyen.

A `"readResults"` csomópont tartalmazza az összes felismert szöveget (ha a választható *includeTextDetails* paramétert a értékre állítja `true` ). A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. A `"documentResults"` csomópont tartalmazza a modell által felderített bevételezés-specifikus értékeket. Itt talál hasznos kulcs/érték párokat, mint például az adó, a teljes, a kereskedelmi címek és így tovább.

Tekintse meg az alábbi beérkezési képet és a hozzá tartozó JSON-kimenetet.

![A contoso áruházból érkezett visszaigazolás](../../media/contoso-allinone.jpg)

Ezt a kimenetet lerövidíti az olvashatóság érdekében. Tekintse [meg a teljes minta kimenetet a githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

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

## <a name="analyze-business-cards"></a>Üzleti kártyák elemzése

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)  

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az angol üzleti kártyákból származó általános mezőket egy előre betanított modell használatával. További információ a névjegykártya-elemzésről: a [Business Cards fogalmi útmutatója](../../concept-business-cards.md). A névjegykártya elemzésének megkezdéséhez hívja meg a **[Business Card API elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** az alábbi curl-parancs használatával. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le a értékét `{your business card URL}` egy nyugtát ábrázoló rendszerkép URL-címére.
1. Cserélje le az `{subscription key}` elemet az előző lépésből másolt előfizetési kulcsra.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your business card URL}'}"
```

Olyan választ fog kapni `202 (Success)` , amely tartalmazza a am **Operation-Location** fejlécet. A fejléc értéke olyan műveleti azonosítót tartalmaz, amelynek segítségével lekérdezheti az aszinkron művelet állapotát, és lekérheti az eredményeket.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Névjegykártya eredményeinek beolvasása

Miután megismerte a **Business Card elemzése** API-t, hívja meg a **[Get elemezze Business Card result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** API-t, hogy lekérje a művelet állapotát és a kinyert adatmennyiséget. A parancs futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `{Endpoint}` az helyére az űrlapot felismerő előfizetési kulccsal beszerzett végpontot. Az űrlap-felismerő erőforrás- **Áttekintés** lapon találhatja meg.
1. Cserélje le az `{resultId}` elemet az előző lépésben szereplő műveleti azonosítóra.
1. A `{subscription key}` helyére írja be az előfizetési kulcsot.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer a `200 (Success)` JSON-kimenettel kapcsolatos választ küld. 

A `"readResults"` csomópont tartalmazza az összes felismert szöveget. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. A `"documentResults"` csomópont tartalmazza a modell által felderített névjegykártya-specifikus értékeket. Itt találhat hasznos kapcsolattartási adatokat, például a vállalat nevét, utónevét, vezetéknevét, telefonszámát stb.

![A contoso vállalat üzleti kártyája](../../media/business-card-english.jpg)

Ez a JSON-kimenet lerövidítve az olvashatóság érdekében. Tekintse [meg a teljes minta kimenetet a githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

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

A parancsfájl addig kinyomtatja a válaszokat a konzolra, amíg be nem fejeződik a **névjegykártya elemzése** művelet.

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)  

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="manage-custom-models"></a>Egyéni modellek kezelése

### <a name="get-a-list-of-custom-models"></a>Egyéni modellek listájának beolvasása

Az alábbi parancs **[Custom models (egyéni modellek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetCustomModels)** ) API-ját használva visszaállíthatja az előfizetéshez tartozó egyéni modellek listáját.

1. Cserélje le `{Endpoint}` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le az `{subscription key}` elemet az előző lépésből másolt előfizetési kulcsra.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

A `200` következőhöz hasonló JSON-adatkérések sikeresek lesznek. A `"modelList"` elem az összes létrehozott modellt és azok információit tartalmazza.

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

### <a name="get-a-specific-model"></a>Adott modell beszerzése

Egy adott egyéni modell részletes adatainak beolvasásához használja az **[Egyéni modell beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetCustomModel)** API-t a következő parancsban.

1. Cserélje le `{Endpoint}` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le az `{subscription key}` elemet az előző lépésből másolt előfizetési kulcsra.
1. Cserélje le a helyére a `{modelId}` megkeresni kívánt egyéni modell azonosítóját.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

A `200` következőhöz hasonló JSON-adatkérések sikeresek lesznek.

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

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrás-fiókból

Az AZONOSÍTÓra hivatkozva egy modellt is törölhet a fiókjából. Ez a parancs meghívja az **[Egyéni modell törlése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/DeleteCustomModel)** API-t az előző szakaszban használt modell törléséhez.

1. Cserélje le `{Endpoint}` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le az `{subscription key}` elemet az előző lépésből másolt előfizetési kulcsra.
1. Cserélje le a helyére a `{modelId}` megkeresni kívánt egyéni modell azonosítóját.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

`204`Sikeres választ kaphat, amely azt jelzi, hogy a modell törlésre van megjelölve. A modell összetevői 48 órán belül el lesznek távolítva.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az űrlap-felismerő REST API használatával különböző módokon taníthatja ki a modelleket és elemezheti az űrlapokat. Következő lépésként tekintse meg a dokumentációt az űrlap-felismerő API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)

* [Mi a Form Recognizer?](../../overview.md)
