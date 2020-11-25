---
title: 'Gyors útmutató: névjegykártya-adatok kinyerése a Python-Form felismerővel'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő REST API a Python használatával kinyerheti az adatokból az angol nyelvű képeket az üzleti kártyákról.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 67a21dd86059f6cf1f017ce3eada285d2faab1e6
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012424"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Gyors útmutató: névjegykártya-adatok kinyerése az űrlap-felismerő REST API és a Python használatával

Ebben a rövid útmutatóban az Azure űrlap-felismerő REST API a Python használatával kinyerheti és azonosíthatja az üzleti kártyákkal kapcsolatos releváns információkat.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőket kell tennie:
- [Python](https://www.python.org/downloads/) telepítve (ha helyileg szeretné futtatni a mintát).
- Egy névjegykártya képe. Ehhez a rövid útmutatóhoz [minta képet](../media/business-card-english.jpg) is használhat.

> [!NOTE]
> Ez a rövid útmutató egy helyi fájlt használ. Az URL-cím alapján elérhető távoli névjegykártya-rendszerkép használatához tekintse meg a [dokumentációt](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync).

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Névjegykártya elemzése

A névjegykártya elemzésének megkezdéséhez hívja meg a **[Business Card API elemzése](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** az alábbi Python-szkripttel. A szkript futtatása előtt végezze el a következő módosításokat:

1. Cserélje le `<endpoint>` a helyére az űrlap-felismerő előfizetéshez kapott végpontot.
1. Cserélje le a helyére `<path to your business card>` a névjegykártyájának vagy PDF-fájljának helyi elérési útját.
1. Cserélje le az `<subscription key>` elemet az előző lépésből másolt előfizetési kulcsra.
1. Cserélje le `<file type>` a "rendszerkép/JPEG", a "képfájl/png", az "Application/PDF" vagy a "képek/TIFF" kifejezésre.

    ```python
    ########### Python Form Recognizer Async Business Cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    content_type = "<file type>"
    
    headers = {
        # Request headers
        'Content-Type': content_type,
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True  # True to output all recognized text
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Mentse a kódot egy. file kiterjesztésű fájlba. Például: *Form-Recognizer-BusinessCards.py*.
1. Nyisson meg egy parancsablakot.
1. A parancssoron használja a `python` parancsot a minta futtatására. Például: `python form-recognizer-businesscards.py`.

Egy olyan választ fog kapni `202 (Success)` , amely egy **művelet – hely** fejlécet tartalmaz, amelyet a szkript a konzolra fog nyomtatni. Ez a fejléc egy eredmény-azonosítót tartalmaz, amelynek segítségével lekérdezheti a hosszú ideig futó művelet állapotát, és lekérheti az eredményeket. A következő példában a karakterlánc az eredmény-azonosító után következik be `operations/` .

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>A névjegykártya eredményeinek beolvasása

Miután megismerte a **Business Card elemzése** API-t, hívja meg a **[Get elemezze Business Card result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** API-t, hogy lekérje a művelet állapotát és a kinyert adatmennyiséget. Adja hozzá a következő kódot a Python-szkript aljához. Ez az eredmény-azonosító értékét használja egy új API-hívásban. Ez a parancsfájl rendszeres időközönként meghívja az API-t, amíg az eredmények elérhetővé nem válnak. Javasoljuk, hogy egy vagy több másodperces intervallumot válasszon.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Mentse a parancsfájlt.
1. Ismét a `python` paranccsal futtassa a mintát. Például: `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>A válasz vizsgálata
![A contoso vállalat üzleti kártyája](../media/business-card-english.jpg)

Ez a példa az űrlap-felismerő által visszaadott JSON-kimenetet mutatja be. A rendszer csonkolja az olvashatóságot.

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

A parancsfájl addig kinyomtatja a válaszokat a konzolra, amíg be nem fejeződik a **névjegykártya elemzése** művelet. A `"readResults"` csomópont tartalmazza az összes felismert szöveget. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. A `"documentResults"` csomópont tartalmazza a modell által felderített névjegykártya-specifikus értékeket. Itt találhat hasznos kapcsolattartási adatokat, például a vállalat nevét, utónevét, vezetéknevét, telefonszámát stb.


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az űrlap-felismerő REST APIt használta a Python használatával a névjegykártya tartalmának kinyeréséhez. Következő lépésként tekintse meg a dokumentációt az űrlap-felismerő API részletesebb megismeréséhez.

> [!div class="nextstepaction"]
> [REST API dokumentáció](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
