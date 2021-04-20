---
title: 'Rövid útmutató: Képelemzési REST API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a Képelemzési funkció első REST API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7a2e8613aab61beec3720cadaa20eb008386b43b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728192"
---
A Képelemzési REST API:

* Képek elemzése címkék, szövegleírások, arcok, felnőtt tartalom és egyéb adatok alapján.
* Miniatűr létrehozása intelligens kivágással

> [!NOTE]
> Ez a rövid útmutató cURL-parancsokkal hívja meg a REST API. Az alkalmazás egy REST API is hívható. A GitHub-mintákban [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST) [Java,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST) [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)és Go nyelven talál [példákat.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/) 
* Az Azure-előfizetés létrehozása után hozzon létre egy Computer Vision-erőforrást, Computer Vision erőforrást a Azure Portal a kulcs és a végpont <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás gombra.**
  * Szüksége lesz a létrehozott erőforrás kulcsára és végpontjára az alkalmazás a Computer Vision csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
  * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.
* [telepített cURL](https://curl.haxx.se/)

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A kép különböző vizuális jellemzőkre vonatkozó elemzéséhez tegye a következőket:

1. Másolja az alábbi parancsot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a parancs megfelelő területein:
    1. Cserélje le a `<subscriptionKey>` értéket az előfizetői azonosítóra.
    1. Cserélje le a kérelem URL-címének első részét ( ) a saját végponti `westcentralus` URL-címében található szövegre.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Igény szerint cserélje a kép URL-címét a kérelem törzsében (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) egy másik elemzendő kép URL-címére.
1. Nyisson meg egy parancsablakot.
1. Illessze be a szövegszerkesztőből a parancsot, majd futtassa.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

### <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A mintaalkalmazás elemzi és megjeleníti a sikeres választ a parancssorban, a következő példához hasonló módon:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```


## <a name="generate-a-thumbnail"></a>Miniatűr létrehozása

A Képelemzés használatával miniatűrt hozhat létre intelligens kivágással. Megadhatja a kívánt magasságot és szélességet, ami eltérhet a bemeneti kép méretarányában. A képelemzés intelligens kivágással azonosítja a érintett területet, és kivágási koordinátákat hoz létre az adott régió körül.
 
A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi parancsot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a parancs megfelelő területein:
    1. Cserélje le a `<subscriptionKey>` értéket az előfizetői azonosítóra.
    1. Cserélje le a értékét annak a fájlnak az elérési útjára és nevére, amelybe a visszaadott `<thumbnailFile>` miniatűrképet menteni kell.
    1. Cserélje le a kérelem URL-címének első részét ( ) a saját végponti `westcentralus` URL-címében található szövegre.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Igény szerint cserélje a kép URL-címét a kérelem törzsében (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) egy másik olyan kép URL-címére, amelyből miniatűrt szeretne létrehozni.
1. Nyisson meg egy parancsablakot.
1. Illessze be a parancsot a szövegszerkesztőből a parancssori ablakba.
1. Nyomja le az Enter billentyűt a program futtatásához.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

### <a name="examine-the-response"></a>A válasz vizsgálata

A sikeres válasz a(z) `<thumbnailFile>` helyen megszabott fájlba írja a miniatűrt. Ha a kérés meghiúsul, a válasz tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát. Ha a kérés sikeresnek tűnik, de a létrehozott miniatűr nem érvényes képfájl, lehetséges, hogy az előfizetői kulcs érvénytelen.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan telepíthet alapszintű képelemzési hívásokat a REST API. Ezután további információk az Analyze API funkcióiról.

> [!div class="nextstepaction"]
>[Az Analyze API hívása](../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Képelemzés áttekintése](../overview-image-analysis.md)