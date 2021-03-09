---
title: A Computer Vision API meghívása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hívhatja meg a Computer Vision API az Azure Cognitive Services REST API használatával.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: abb367b64da0811a1ff46efe60b60485375f809f
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486063"
---
# <a name="call-the-computer-vision-api"></a>A Computer Vision API meghívása

Ez a cikk bemutatja, hogyan hívhatja meg a Computer Vision APIt a REST API használatával. A minták a C# nyelven íródnak a Computer Vision API ügyféloldali kódtár és HTTP POST vagy GET hívások használatával. A cikk témája:

- Címkék, leírások és kategóriák beolvasása
- Domain-specifikus információk vagy "hírességek" beszerzése

A cikkben szereplő példák a következő funkciókat mutatják be:

* Egy olyan rendszerkép elemzése, amely címkék és leírások tömbjét állítja vissza
* Egy olyan rendszerkép elemzése, amely egy tartományszintű modellel rendelkezik (pontosabban a "hírességek" modellt), hogy a megfelelő eredményt a JSON-ban küldje vissza.

A funkciók a következő lehetőségeket kínálnak:

- **1. lehetőség**: hatókörön belüli elemzés – csak a megadott modell elemzése
- **2. lehetőség**: bővített elemzés – további részletek biztosítása a [86-kategóriájú taxonómia](../Category-Taxonomy.md) használatával

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* A rendszerkép URL-címe vagy egy helyileg tárolt rendszerkép elérési útja
* Támogatott bemeneti metódusok: egy nyers bináris képfájl egy alkalmazás/oktett-stream vagy egy képurl-cím formájában
* Támogatott képfájlformátumok: JPEG, PNG, GIF és BMP
* Képfájl mérete: 4 MB vagy kevesebb
* Képdimenziók: 50 &times; 50 képpont vagy nagyobb
  
## <a name="authorize-the-api-call"></a>Az API-hívás engedélyezése

A Computer Vision API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot egy lekérdezési karakterlánc paraméterének kell átadnia, vagy meg kell adni a kérelem fejlécében.

Az előfizetési kulcsot az alábbi módokon adhatja át:

* Adja át egy lekérdezési karakterláncon, ahogy az alábbi példában látható Computer Vision API:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* A HTTP-kérelem fejlécében adhatja meg:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Ha az ügyféloldali kódtárat használja, adja át a kulcsot a ComputerVisionClient konstruktorán keresztül, és adja meg a régiót az ügyfél egy tulajdonságában:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Rendszerkép feltöltése a Computer Vision API szolgáltatásba

Az Computer Vision API hívás végrehajtásának alapszintű módja, ha közvetlenül feltölt egy képet a címkék, a leírás és a hírességek visszaküldéséhez. Ezt úgy teheti meg, hogy "POST" kérést küld a HTTP-szövegtörzsben található bináris képpel együtt a rendszerképből beolvasott adatokkal. A feltöltési módszer az összes Computer Vision API-hívás esetében azonos. Az egyetlen különbség a megadott lekérdezési paraméterek. 

Egy adott rendszerkép esetében a címkék és a leírások beszerzése a következő lehetőségek egyikével történhet:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>1. lehetőség: a címkék és a leírások listájának beolvasása

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>2. lehetőség: csak címkék vagy csak leírások listájának beolvasása

Csak címkék esetén futtassa a következőt:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Csak leírás esetén futtassa a következőt:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Tartomány-specifikus elemzés (hírességek) beolvasása

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>1. lehetőség: hatókörön belüli elemzés – csak a megadott modell elemzése
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

E beállítás használata esetén semmi más lekérdezési paraméter {visualFeatures, részletek} nem érvényes. Ha meg szeretné tekinteni valamennyi támogatott modellt, használja a következőt:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>2. lehetőség: bővített elemzés – további részletek biztosítása a 86-kategóriájú taxonómia használatával

Azokon az alkalmazásokban, amelyekben egy vagy több tartományalapú modell részletei mellett általános képelemzést szeretne kapni, a models Query paraméterrel bővítse ki a v1 API-t.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

A metódus meghívásakor először hívja meg a [86 kategóriás](../Category-Taxonomy.md) besorolást. Ha a kategóriák bármelyike megfelel egy ismert vagy egyező modellnek, az osztályozó meghívások egy második fázisa jelenik meg. Ha például a "details = all" vagy a "details" kifejezés a "hírességek" kifejezést tartalmazza, akkor az 86 kategóriás osztályozó hívása után hívja meg a hírességek modelljét. Az eredmény tartalmazza a kategória személyét. Az 1. lehetőséggel ellentétben ez a módszer növeli a hírességeket érdeklő felhasználók késését.

Ebben az esetben az összes v1 lekérdezési paraméter ugyanúgy viselkedik. Ha nem ad meg visualFeatures = kategóriákat, az implicit módon engedélyezve van.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Az elemzéshez használt JSON-kimenet beolvasása és megismerése

Bemutatunk egy példát:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Mező | Típus | Tartalom
------|------|------|
Címkék  | `object` | A címkék egy tömb legfelső szintű objektuma.
tags[].Name | `string`    | A címkét a címkék besorolása alapján.
tags[].Score    | `number`    | A megbízhatósági pontszám 0 és 1 között van.
leírás     | `object`    | A Leírás legfelső szintű objektuma.
description.tags[] |    `string`    | A címkék listája.  Ha nem áll rendelkezésre elegendő megbízhatóság a képaláírás létrehozásához, akkor előfordulhat, hogy a címkék az egyetlen elérhető információ a hívó számára.
description.captions[].text    | `string`    | A képet leíró kifejezés.
description.captions[].confidence    | `number`    | A kifejezés megbízhatósági pontszáma.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>A tartományi specifikus modellek JSON-kimenetének beolvasása és megértése

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>1. lehetőség: hatókörön belüli elemzés – csak a megadott modell elemzése

A kimenet a címkék egy tömbje, ahogy az az alábbi példában is látható:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>2. lehetőség: továbbfejlesztett elemzés – további részletek biztosítása a "86-Categories" rendszertan használatával

A 2. lehetőséget (bővített elemzés) használó, tartományhoz tartozó modellek esetében a kategóriák visszatérési típusa kibővült, ahogy az alábbi példában is látható:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

A Categories (kategóriák) mező az eredeti besorolásban szereplő [86-kategóriák](../Category-Taxonomy.md) közül egy vagy több listáját tartalmazza. Azok a kategóriák, amelyek aláhúzással egyeznek meg a kategóriával és a gyermekeivel (például "people_" vagy "people_group", a hírességek modelljéhez).

Mező    | Típus    | Tartalom
------|------|------|
kategóriák | `object`    | A legfelső szintű objektum.
categories[].name     | `string`    | A név a 86 kategóriás besorolási listáról.
categories[].score    | `number`    | A megbízhatósági pontszám 0 és 1 között van.
categories[].detail     | `object?`      | Választható A részletező objektum.

Ha több kategória is egyezik (például az 86 kategóriás osztályozó egy pontszámot ad vissza a "people_" és a "people_young" kifejezéshez, ha a Model = hírességek), akkor a részletek a legáltalánosabb szintű egyezéshez ("people_", ebben a példában) vannak csatolva.

## <a name="error-responses"></a>Hibaüzenetek

Ezek a hibák ugyanazok, mint a vízióban. elemezze a további NotSupportedModel hibával (HTTP 400), amelyet az 1. és 2. lehetőség esetében is vissza lehet adni. A 2. lehetőség (bővített elemzés) esetén, ha a részletekben megadott bármelyik modell nem ismerhető fel, az API egy NotSupportedModel ad vissza, még akkor is, ha egy vagy több érvényes. A listModels meghívásával megállapíthatja, hogy milyen modellek támogatottak.

## <a name="next-steps"></a>Következő lépések

A REST API használatához lásd: [Computer Vision API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b).
