---
title: 'Gyors útmutató: optikai karakterfelismerés REST API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban megismerheti az optikai karakterfelismerési REST API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 0e22691b025299b16df9141e48d22e0063d92c97
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284778"
---
A nyomtatott és a kézírásos szöveg olvasásához használja az optikai karakterfelismerési REST API.

> [!NOTE]
> Ez a rövid útmutató cURL-parancsokat használ a REST API meghívásához. A REST API a programozási nyelv használatával is meghívhatja. A GitHub-mintákat a [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), a [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), a [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), a [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)és a [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)című példákban tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/) 
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
  * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
  * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* telepített [curl](https://curl.haxx.se/)



## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

Az OCR szolgáltatás megjelenítheti a képen látható szöveget, és átalakíthatja a karakteres adatfolyamba. A szöveges felismeréssel kapcsolatos további információkért tekintse meg az [optikai karakterfelismerés (OCR) áttekintését](../overview-ocr.md).

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi parancsot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a parancs megfelelő területein:
    1. Cserélje le a `<subscriptionKey>` értéket az előfizetői azonosítóra.
    1. Cserélje le a kérelem URL-címének ( `westcentralus` ) első részét a saját végpont URL-címében található szövegre.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Igény szerint cserélje a kép URL-címét a kérelem törzsében (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) egy másik elemzendő kép URL-címére.
1. Nyisson meg egy parancsablakot.
1. Illessze be a szövegszerkesztőből a parancsot, majd futtassa.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/read/analyze?language={string}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

A válasz egy `Operation-Location` fejlécet is tartalmaz, amelynek értéke egy egyedi URL-cím. Ezt az URL-címet használva kérdezheti le az olvasási művelet eredményét. Az URL 48 óra múlva lejár.

1. Másolja a következő parancsot a szövegszerkesztőbe.
1. Cserélje le az URL-címet az `Operation-Location` előző lépésben másolt értékre.
1. Hajtsa végre a következő módosításokat a parancs megfelelő területein:
    1. Cserélje le a `<subscriptionKey>` értéket az előfizetői azonosítóra.
1. Nyisson meg egy parancsablakot.
1. Illessze be a szövegszerkesztőből a parancsot, majd futtassa.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A mintaalkalmazás elemzi és megjeleníti a sikeres választ a parancssorban, a következő példához hasonló módon:

```json
{
  "status": "succeeded",
  "createdDateTime": "2019-10-03T14:32:04.236Z",
  "lastUpdatedDateTime": "2019-10-03T14:38:14.852Z",
  "analyzeResult": {
    "version": "v3.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 49.59,
        "width": 600,
        "height": 400,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              202,
              618,
              2047,
              643,
              2046,
              840,
              200,
              813
            ],
            "text": "Our greatest glory is not",
            "words": [
              {
                "boundingBox": [
                  204,
                  627,
                  481,
                  628,
                  481,
                  830,
                  204,
                  829
                ],
                "text": "Our",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  519,
                  628,
                  1057,
                  630,
                  1057,
                  832,
                  518,
                  830
                ],
                "text": "greatest",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1114,
                  630,
                  1549,
                  631,
                  1548,
                  833,
                  1114,
                  832
                ],
                "text": "glory",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1586,
                  631,
                  1785,
                  632,
                  1784,
                  834,
                  1586,
                  833
                ],
                "text": "is",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1822,
                  632,
                  2115,
                  633,
                  2115,
                  835,
                  1822,
                  834
                ],
                "text": "not",
                "confidence": 0.164
              }
            ]
          },
...
        ]
      },
      {
        "page": 2,
        "language": "en",
        "angle": 1.32,
        "width": 600,
        "height": 400,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              1612,
              903,
              2744,
              935,
              2738,
              1139,
              1607,
              1107
            ],
            "text": "in never failing ,",
            "words": [
              {
                "boundingBox": [
                  1611,
                  934,
                  1707,
                  933,
                  1708,
                  1147,
                  1613,
                  1147
                ],
                "text": "in",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1753,
                  933,
                  2132,
                  930,
                  2133,
                  1144,
                  1754,
                  1146
                ],
                "text": "never",
                "confidence": 0.999
              },
              {
                "boundingBox": [
                  2162,
                  930,
                  2673,
                  927,
                  2674,
                  1140,
                  2164,
                  1144
                ],
                "text": "failing",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  2703,
                  926,
                  2788,
                  926,
                  2790,
                  1139,
                  2705,
                  1140
                ],
                "text": ",",
                "confidence": 0.164
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

Az OCR API alaposabb megismerése. Ha gyorsan kísérletezni szeretne az API-val, próbálja meg az [Open API Testing Console](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005/console)-t.

> [!div class="nextstepaction"]
> [Az OCR API megismerése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)
