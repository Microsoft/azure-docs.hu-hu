---
title: 'Modell lekérése REST-hívással C-ben #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.openlocfilehash: 4e4a5115ee83030b5cf3bd8c047f7ccadf4c9e2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91534628"
---
[Dokumentáció](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)  |  [Minta](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-model-with-rest/model.py)

## <a name="prerequisites"></a>Előfeltételek

* [Python 3,6](https://www.python.org/downloads/) vagy újabb verzió.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-pizza-app"></a>Pizza-alkalmazás létrehozása

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]

## <a name="change-model-programmatically"></a>Modell programozott módosítása

1. Hozzon létre egy új fájlt `model.py` néven. Adja hozzá a következő kódot:

    [!code-python[Add example utterances to Language Understanding in python](~/cognitive-services-quickstart-code/python/LUIS/python-model-with-rest/model.py)]

1. Cserélje le az értékeket a `YOUR-` saját értékeivel kezdődő értékekre.

    |Tájékoztatás|Cél|
    |--|--|
    |`YOUR-APP-ID`| A LUIS-alkalmazás azonosítója. |
    |`YOUR-AUTHORING-KEY`|Az 32 karakteres szerzői kulcs.|
    |`YOUR-AUTHORING-ENDPOINT`| Az authoring URL-végpontja. Például: `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Az erőforrás neve az erőforrás létrehozásakor állítható be.|

    A hozzárendelt kulcsok és erőforrások a LUIS portálon láthatók a kezelés szakasz Azure- **erőforrások** lapján. Az alkalmazás-azonosító az **Alkalmazásbeállítások** lapon, ugyanazon kezelés szakaszban érhető el.

1. Ha a parancssor ugyanabban a címtárban található, ahol a fájlt létrehozta, a következő parancs futtatásával futtassa a fájlt:

    ```console
    python model.py
    ```

1. Tekintse át a szerzői válaszokat:

    ```console
    Add the list of utterances:
    [{'value': {'ExampleId': 1137150691, 'UtteranceText': 'order a pizza'}, 'hasError': False}, {'value': {'ExampleId': 1137150692, 'UtteranceText': 'order a large pepperoni pizza'}, 'hasError': False}, {'value': {'ExampleId': 1137150693, 'UtteranceText': 'i want two large pepperoni pizzas on thin crust'}, 'hasError': False}]
    Request training:
    {'statusId': 9, 'status': 'Queued'}
    Request training status:
    [{'modelId': 'edb46abf-0000-41ab-beb2-a41a0fe1630f', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': 'a5030be2-616c-4648-bf2f-380fa9417d37', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': '3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': 'e4b6704b-1636-474c-9459-fe9ccbeba51c', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': '031d3777-2a00-4a7a-9323-9a3280a30000', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': '9250e7a1-06eb-4413-9432-ae132ed32583', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}]
    ```

    Itt látható az olvashatóságra formázott kimenet:

    ```json
    Add the list of utterances:
    [
      {
        'value': {
          'ExampleId': 1137150691,
          'UtteranceText': 'order a pizza'
        },
        'hasError': False
      },
      {
        'value': {
          'ExampleId': 1137150692,
          'UtteranceText': 'order a large pepperoni pizza'
        },
        'hasError': False
      },
      {
        'value': {
          'ExampleId': 1137150693,
          'UtteranceText': 'i want two large pepperoni pizzas on thin crust'
        },
        'hasError': False
      }
    ]

    Request training:
    {
      'statusId': 9,
      'status': 'Queued'
    }

    Request training status:
    [
      {
        'modelId': 'edb46abf-0000-41ab-beb2-a41a0fe1630f',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': 'a5030be2-616c-4648-bf2f-380fa9417d37',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': '3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': 'e4b6704b-1636-474c-9459-fe9ccbeba51c',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': '031d3777-2a00-4a7a-9323-9a3280a30000',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': '9250e7a1-06eb-4413-9432-ae132ed32583',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ajánlott eljárások az alkalmazásokhoz](../luis-concept-best-practices.md)
