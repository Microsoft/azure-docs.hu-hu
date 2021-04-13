---
title: 'Rövid útmutató: Optikai karakterfelismerési REST API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az optikai karakterfelismerési funkcióval REST API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 01149eed5cc4195ca501507e7fe1d66fffecb84d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327100"
---
Az optikai karakterfelismerési REST API nyomtatott és kézzel írt szövegek olvasása.

> [!NOTE]
> Ez a rövid útmutató cURL-parancsokkal hívja meg a REST API. Az alkalmazás egy REST API is hívható. A GitHub-mintákban [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST) [Java,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST) [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)és Go nyelven talál [példákat.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/) 
* Az Azure-előfizetés létrehozása után hozzon létre egy Computer Vision-erőforrást, Computer Vision erőforrást a Azure Portal a kulcs és a végpont <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás elemre.**
  * Szüksége lesz a létrehozott erőforrás kulcsára és végpontjára az alkalmazás a Computer Vision csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
  * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.
* [telepített cURL](https://curl.haxx.se/)



## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

Az OCR szolgáltatás képes olvasni a képeken látható szövegeket, és konvertálni karakterfolyamokká. A szövegfelismeréssel kapcsolatos további információkért lásd az optikai karakterfelismerés [(OCR) áttekintését.](../overview-ocr.md)

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi parancsot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a parancs megfelelő területein:
    1. Cserélje le a `<subscriptionKey>` értéket az előfizetői azonosítóra.
    1. Cserélje le a kérelem URL-címének első részét ( ) a saját végponti `westcentralus` URL-címében található szövegre.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Igény szerint cserélje a kép URL-címét a kérelem törzsében (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) egy másik elemzendő kép URL-címére.
1. Nyisson meg egy parancsablakot.
1. Illessze be a szövegszerkesztőből a parancsot, majd futtassa.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

A válasz tartalmazni fog egy `Operation-Location` fejlécet, amelynek értéke egy egyedi URL-cím. Az URL-cím használatával lekérdezheti az Olvasási művelet eredményeit. Az URL-cím 48 óra múlva lejár.

1. Másolja a következő parancsot a szövegszerkesztőbe.
1. Cserélje le az URL-címet az előző lépésben `Operation-Location` kimásott értékre.
1. Hajtsa végre a következő módosításokat a parancs megfelelő területein:
    1. Cserélje le a `<subscriptionKey>` értéket az előfizetői azonosítóra.
1. Nyisson meg egy parancsablakot.
1. Illessze be a szövegszerkesztőből a parancsot, majd futtassa.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A mintaalkalmazás elemzi és megjeleníti a sikeres választ a parancssorban, a következő példához hasonló módon:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>Következő lépések

Az OCR API mélyebb megismerése. Az API-val való gyors kísérletezéshez próbálja ki az [Open API tesztelési konzolját.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005/console)

> [!div class="nextstepaction"]
> [Az OCR API megismerése](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)
