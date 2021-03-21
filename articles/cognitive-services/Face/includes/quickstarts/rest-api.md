---
title: Face REST API rövid útmutató
description: Használja a Face REST APIt a cURL használatával az arcok észleléséhez, hasonló kereséséhez (képkeresés a képhez), az arcok azonosítása (Arcfelismerés) és az arc-adatok migrálása.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: 5523d89548ba08dfc5a43f5c21b5a12a5e5955c3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510662"
---
Ismerkedjen meg az arc-felismeréssel a Face REST API használatával. A Face szolgáltatás hozzáférést biztosít a speciális algoritmusokhoz a képeken található emberi arcok észleléséhez és felismeréséhez.

Használja a Face REST APIt a következőre:

* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)

> [!NOTE]
> Ez a rövid útmutató cURL-parancsokat használ a REST API meghívásához. A REST API a programozási nyelv használatával is meghívhatja. A GitHub-mintákat a [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), a [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest), a [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest), a [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest)és a [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest)című példákban tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Az alkalmazásnak a Face APIhoz való összekapcsolásához a létrehozott erőforrás kulcsát és végpontját kell használnia. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* A [PowerShell 6.0](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)-s vagy újabb verziója, vagy egy hasonló parancssori alkalmazás.


## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

A következőhöz hasonló parancsot kell használnia a Face API meghívásához és a Face attribútum adatainak a rendszerképből való lekéréséhez. Először másolja a kódot egy szövegszerkesztőbe, hogy &mdash; a Futtatás előtt módosítania kell a parancs egyes részeit.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

Hajtsa végre a következő módosításokat:
1. Rendelje hozzá `Ocp-Apim-Subscription-Key` az érvényes Face előfizetési kulcshoz.
1. Módosítsa a lekérdezés URL-címének első részét úgy, hogy az megfeleljen az előfizetési kulcsnak megfelelő végpontnak.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Igény szerint módosíthatja a kérelem törzsében lévő URL-címet, hogy egy másik képre mutasson.

Miután elvégezte a módosításokat, nyisson meg egy parancssort, és írja be az új parancsot. 

### <a name="examine-the-results"></a>Az eredmények vizsgálata

A konzol ablakában a JSON-adatokként megjelenő arc információit kell látnia. Például:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>Arc attribútumainak beolvasása
 
A Face attribútumok kinyeréséhez hívja meg újra az észlelési API-t, de állítsa a következőre: `detectionModel` `detection_01` . Adja hozzá a `returnFaceAttributes` lekérdezési paramétert is. A parancsnak ekkor a következőhöz hasonlóan kell kinéznie. Ahogy korábban is, szúrja be az arc-előfizetési kulcsot és a végpontot.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>Az eredmények vizsgálata

A visszaadott arc adatai mostantól a Face attribútumokat is tartalmazzák. Például:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>Hasonló arcok keresése

Ezzel a művelettel egyetlen észlelt arc (forrás) található, és megkeresi a találatok között megjelenő más arcokat (TARGET). Ha egyezést talál, kinyomtatja az egyeztetett arc AZONOSÍTÓját a konzolon.

### <a name="detect-faces-for-comparison"></a>Arcok észlelése összehasonlításhoz

Először is fel kell ismernie a képekben lévő arcokat, mielőtt összehasonlítja őket. Futtassa ezt a parancsot az [arcok észlelése](#detect-faces-in-an-image) szakaszban leírtak szerint. Ez az észlelési módszer összehasonlítási műveletekre van optimalizálva. Nem kibontja a részletes Arcfelismerés-attribútumokat, például a fenti részben, és egy másik észlelési modellt használ.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

Keresse meg az `"faceId"` értéket a JSON-válaszban, és mentse egy ideiglenes helyre. Ezután hívja meg újra a fenti parancsot a többi rendszerkép URL-címéhez, és mentse a Face ID-ket is. Ezeket az azonosítókat fogja használni azon arcok célcsoportja, amelyekről hasonló arcot talál.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

Végezetül azonosítsa az egyforrású arcot, amelyet az egyeztetéshez fog használni, és mentse az AZONOSÍTÓját. Ezt az azonosítót külön kell megtartania a többitől.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>Egyezések keresése

Másolja a következő parancsot egy szövegszerkesztőbe.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

Ezután végezze el a következő módosításokat:
1. Rendelje hozzá `Ocp-Apim-Subscription-Key` az érvényes Face előfizetési kulcshoz.
1. Módosítsa a lekérdezés URL-címének első részét úgy, hogy az megfeleljen az előfizetési kulcsnak megfelelő végpontnak.

Az értékhez használja a következő JSON-tartalmat `body` :

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. Használja a forrás Arcfelismerés azonosítót a következőhöz: `"faceId"` .
1. Illessze be a többi arc-azonosítót a tömbben szereplő kifejezésként `"faceIds"` .

### <a name="examine-the-results"></a>Az eredmények vizsgálata

A rendszer egy JSON-választ fog kapni, amely felsorolja a lekérdezési oldallal egyező arcok azonosítóit.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Face REST APIt az alapszintű arc-felismerési feladatok elvégzésére. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
> [Face API referenciája](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [Mi az a Face szolgáltatás?](../../overview.md)