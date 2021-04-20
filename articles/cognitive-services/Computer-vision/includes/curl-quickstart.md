---
title: 'Rövid útmutató: Optikai karakterfelismerési REST API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az optikai karakterfelismerési és -felismerési REST API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2f01b1d222470c49505638be64180948b6f7e046
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728254"
---
Az optikai karakterfelismerési REST API nyomtatott és kézzel írt szövegek olvashatók.

> [!NOTE]
> Ez a rövid útmutató cURL-parancsokat használ a REST API. Az alkalmazás egy REST API is hívható. A GitHub-mintákban [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST) [Java,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST) [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)és Go nyelven talál [példákat.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/) 
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Computer Vision-erőforrást, Computer Vision erőforrást a Azure Portal a kulcs és a <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás gombra.**
  * Szüksége lesz a létrehozott erőforrás kulcsára és végpontjára az alkalmazás a Computer Vision csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
  * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.
* [telepített cURL](https://curl.haxx.se/)



## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

Az OCR szolgáltatás képes olvasni a képeken látható szövegeket, és konvertálni karakterfolyamokká. A szövegfelismeréssel kapcsolatos további információkért lásd az optikai karakterfelismerés [(OCR) áttekintését.](../overview-ocr.md)

### <a name="call-the-read-api"></a>A Read API hívása

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

A válasz tartalmazni fog egy `Operation-Location` fejlécet, amelynek értéke egy egyedi URL-cím. Ezt az URL-címet az Olvasási művelet eredményeinek lekérdezésére használhatja. Az URL-cím 48 óra múlva lejár.

### <a name="get-read-results"></a>Olvasási eredmények lekérte

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

Ebben a rövid útmutatóban megtanulta, hogyan hívhatja meg a Read REST API. A következő lépés az API-funkciók olvasása.

> [!div class="nextstepaction"]
>[A Read API hívása](../Vision-API-How-to-Topics/call-read-api.md)

* [Az OCR áttekintése](../overview-ocr.md)
