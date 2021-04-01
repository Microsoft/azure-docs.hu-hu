---
title: Modell lekérése REST-hívással a Go-ban
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.openlocfilehash: 0db01cbd281b2060c1e08e6c44202624faefc5d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534653"
---
[Dokumentáció](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)  |  [Minta](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/LUIS/go-rest-model/model.go)

## <a name="prerequisites"></a>Előfeltételek

* [Go](https://golang.org/) programozási nyelv
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-pizza-app"></a>Pizza-alkalmazás létrehozása

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]

## <a name="change-model-programmatically"></a>Modell programozott módosítása

1. Hozzon létre egy új fájlt `predict.go` néven. Adja hozzá a következő kódot:

    [!code-go[Code snippet](~/cognitive-services-quickstart-code/go/LUIS/go-rest-model/model.go)]

1. Cserélje le az értékeket a `YOUR-` saját értékeivel kezdődő értékekre.

    |Tájékoztatás|Cél|
    |--|--|
    |`YOUR-APP-ID`| A LUIS-alkalmazás azonosítója. |
    |`YOUR-AUTHORING-KEY`|Az 32 karakteres szerzői kulcs.|
    |`YOUR-AUTHORING-ENDPOINT`| Az authoring URL-végpontja. Például: `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Az erőforrás neve az erőforrás létrehozásakor állítható be.|

    A hozzárendelt kulcsok és erőforrások a LUIS portálon láthatók a kezelés szakasz Azure- **erőforrások** lapján. Az alkalmazás-azonosító az **Alkalmazásbeállítások** lapon, ugyanazon kezelés szakaszban érhető el.

1. Ha a parancssor ugyanabban a címtárban található, ahol a fájlt létrehozta, adja meg a következő parancsot a go-fájl fordításához:

    ```console
    go build model.go
    ```

1. Futtassa a Go-alkalmazást a parancssorból a következő szöveg beírásával:

    ```console
    go run model.go
    ```

1. Tekintse át a szerzői válaszokat:

    ```console
    add example utterances requested
    body
    [{'text': 'order a pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 12}]}, {'text': 'order a large pepperoni pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'PizzaType', 'startCharIndex': 14, 'endCharIndex': 28}, {'entityName': 'Size', 'startCharIndex': 8, 'endCharIndex': 12}]}, {'text': 'I want two large pepperoni pizzas on thin crust', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'PizzaType', 'startCharIndex': 17, 'endCharIndex': 32}, {'entityName': 'Size', 'startCharIndex': 11, 'endCharIndex': 15}, {'entityName': 'Quantity', 'startCharIndex': 7, 'endCharIndex': 9}, {'entityName': 'Crust', 'startCharIndex': 37, 'endCharIndex': 46}]}]
        201
    [{"value":{"ExampleId":1137150691,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1137150692,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1137150693,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    training selected
    body

        202
    {"statusId":9,"status":"Queued"}
    training status selected
    body

        200
    [{"modelId":"edb46abf-0000-41ab-beb2-a41a0fe1630f","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"a5030be2-616c-4648-bf2f-380fa9417d37","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"e4b6704b-1636-474c-9459-fe9ccbeba51c","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"031d3777-2a00-4a7a-9323-9a3280a30000","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"9250e7a1-06eb-4413-9432-ae132ed32583","details":{"statusId":9,"status":"Queued","exampleCount":0}}]
    ```

    Itt látható az olvashatóságra formázott kimenet:

    ```json
    add example utterances requested
    body
    [
      {
        'text': 'order a pizza',
        'intentName': 'ModifyOrder',
        'entityLabels': [
          {
            'entityName': 'Order',
            'startCharIndex': 6,
            'endCharIndex': 12
          }
        ]
      },
      {
        'text': 'order a large pepperoni pizza',
        'intentName': 'ModifyOrder',
        'entityLabels': [
          {
            'entityName': 'Order',
            'startCharIndex': 6,
            'endCharIndex': 28
          },
          {
            'entityName': 'FullPizzaWithModifiers',
            'startCharIndex': 6,
            'endCharIndex': 28
          },
          {
            'entityName': 'PizzaType',
            'startCharIndex': 14,
            'endCharIndex': 28
          },
          {
            'entityName': 'Size',
            'startCharIndex': 8,
            'endCharIndex': 12
          }
        ]
      },
      {
        'text': 'I want two large pepperoni pizzas on thin crust',
        'intentName': 'ModifyOrder',
        'entityLabels': [
          {
            'entityName': 'Order',
            'startCharIndex': 7,
            'endCharIndex': 46
          },
          {
            'entityName': 'FullPizzaWithModifiers',
            'startCharIndex': 7,
            'endCharIndex': 46
          },
          {
            'entityName': 'PizzaType',
            'startCharIndex': 17,
            'endCharIndex': 32
          },
          {
            'entityName': 'Size',
            'startCharIndex': 11,
            'endCharIndex': 15
          },
          {
            'entityName': 'Quantity',
            'startCharIndex': 7,
            'endCharIndex': 9
          },
          {
            'entityName': 'Crust',
            'startCharIndex': 37,
            'endCharIndex': 46
          }
        ]
      }
    ]

        201
    [
      {
        "value": {
          "ExampleId": 1137150691,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150692,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150693,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    training selected
    body

        202
    {
      "statusId": 9,
      "status": "Queued"
    }
    training status selected
    body

        200
    [
      {
        "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ajánlott eljárások az alkalmazásokhoz](../luis-concept-best-practices.md)