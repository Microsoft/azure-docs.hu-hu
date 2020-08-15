---
title: 'Gyors útmutató: lekérdezés az előrejelzéshez a böngészővel – LUIS'
description: Ebben a rövid útmutatóban egy elérhető nyilvános LUIS-alkalmazás használatával határozhatja meg a felhasználó szándékát a böngészőben a társalgási szövegben.
ms.topic: quickstart
ms.date: 04/21/2020
ms.openlocfilehash: 24c7a8aca410ad10b8a9299c4bca2f13d0feb780
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245995"
---
# <a name="quickstart-query-prediction-runtime-with-user-text"></a>Gyors útmutató: lekérdezési előrejelzési futtatókörnyezet felhasználói szöveggel

Ha szeretné látni, mit ad vissza egy LUIS előrejelzési végpont, tekintsen meg egy előrejelzési eredményt egy webböngészőben.

## <a name="prerequisites"></a>Előfeltételek

Egy nyilvános alkalmazás lekérdezéséhez a következőkre lesz szüksége:

* A Language Understanding (LUIS) erőforrás-információi:
    * **Előrejelzési kulcs** – amely a Luis- [portálról](https://www.luis.ai/)szerezhető be. Ha még nem rendelkezik előfizetéssel a kulcs létrehozásához, regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services).
    * **Előrejelzési végpont altartománya** – az altartomány egyben a Luis-erőforrás **neve** is.
* A LUIS-alkalmazás azonosítója – használja a nyilvános IoT-alkalmazás AZONOSÍTÓját `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . A rövid útmutató kódjában használt felhasználói lekérdezés az adott alkalmazásra jellemző.

## <a name="use-the-browser-to-see-predictions"></a>A böngésző használata az előrejelzések megjelenítéséhez

1. Nyisson meg egy webböngészőt.
1. Használja az alábbi teljes URL-címeket, `YOUR-KEY` és cserélje le a saját Luis-előrejelzési kulcsára. A kérések MEGKAPják a kérelmeket, és tartalmazzák az engedélyt a LUIS-előrejelzési kulccsal, lekérdezési karakterlánc paraméterként.

    #### <a name="v3-prediction-request"></a>[V3 előrejelzési kérelem](#tab/V3-1-1)


    A **Get** -végpont (bővítőhely) kérelmének v3 URL-címének formátuma a következő:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2 előrejelzési kérelem](#tab/V2-1-2)

    A **Get** Endpoint kérelem v2 URL-címének formátuma a következő:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. Illessze be az URL-címet egy böngészőablakba, és nyomja le az Enter billentyűt. A böngészőben megjelenik egy JSON-eredmény, amely jelzi, hogy a LUIS a `HomeAutomation.TurnOn` szándékot észleli első szándékként és a `on` értékű `HomeAutomation.Operation` entitást.

    #### <a name="v3-prediction-response"></a>[V3-előrejelzési válasz](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[V2 előrejelzési válasz](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Az összes cél megjelenítéséhez adja hozzá a megfelelő lekérdezési karakterlánc paramétert.

    #### <a name="v3-prediction-endpoint"></a>[V3 előrejelzési végpont](#tab/V3-3-1)

    Adja hozzá a `show-all-intents=true` querystring végéhez az **összes leképezés megjelenítéséhez**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[V2 előrejelzési végpont](#tab/V2)

    Adja hozzá a `verbose=true` querystring végéhez az **összes leképezés megjelenítéséhez**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

## <a name="next-steps"></a>További lépések

További információk:
* [V3 előrejelzési végpont](luis-migration-api-v3.md)
* [Egyéni altartományok](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Alkalmazás létrehozása a LUIS-portálon](get-started-portal-build-app.md)
