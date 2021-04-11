---
title: A rendszerkép-elemzési API meghívása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hívhatja meg a rendszerkép-elemzési API-t, és hogyan konfigurálhatja annak működését.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3f9a6afe3202df40e26332c3a8c91b8c3eca8a32
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012268"
---
# <a name="call-the-image-analysis-api"></a>A rendszerkép-elemzési API meghívása

Ez a cikk bemutatja, hogyan hívhatja meg a rendszerkép-elemzési API-t egy kép vizuális funkcióival kapcsolatos információk visszaküldéséhez.

Ez az útmutató feltételezi, hogy már <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" létrehozott egy Computer Vision erőforrást "  target="_blank"> Computer Vision erőforrás létrehozása </a> és az előfizetési kulcs és a végpont URL-címének beszerzése. Ha még nem tette meg, [kövesse az első](../quickstarts-sdk/image-analysis-client-library.md) lépéseket.
  
## <a name="submit-data-to-the-service"></a>Adatküldés a szolgáltatásba

Helyi vagy távoli rendszerképet küld az elemzési API-nak. Helyi esetén a bináris képadatokat a HTTP-kérelem törzsében helyezi el. Távoli használatra a rendszerképek URL-címét a kérelem törzsének formázásával kell megadnia, a következőhöz hasonló módon: `{"url":"http://example.com/images/test.jpg"}` .

## <a name="determine-how-to-process-the-data"></a>Az adatfeldolgozás módjának meghatározása

###  <a name="select-visual-features"></a>Vizuális funkciók kiválasztása

Az Analysis [API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) hozzáférést biztosít a szolgáltatás összes rendszerkép-elemzési funkciójának eléréséhez. Meg kell adnia, hogy mely szolgáltatásokat szeretné használni az URL-lekérdezés paramétereinek beállításával. Egy paraméter több értékkel is rendelkezhet, vesszővel elválasztva. Minden egyes megadott szolgáltatáshoz további számítási idő szükséges, ezért csak a szükséges igényeket kell megadnia.

|URL-paraméter | Érték | Leírás|
|---|---|--|
|`visualFeatures`|`Adult` | észleli, ha a rendszerkép pornográf jellegű (meztelenség vagy szexuális cselekmény ábrázolása), vagy véres (a szélsőséges erőszakot vagy vért ábrázolja). A rendszer a szexuálisan szuggesztív tartalmat (más néven zamatos tartalmat) is észleli.|
||`Brands` | különböző márkákat észlel egy képen belül, beleértve a hozzávetőleges helyet is. A Brands argumentum csak angol nyelven érhető el.|
||`Categories` | a képtartalom kategorizálása a dokumentációban meghatározott besorolásnak megfelelően. Ez az alapértelmezett érték `visualFeatures` .|
||`Color` | meghatározza az ékezetes színeket, a domináns színeket, valamint azt, hogy a képek fekete&fehérek-e.|
||`Description` | a képtartalmat a támogatott nyelveken teljes mondattal ismerteti.|
||`Faces` | észleli, ha az arcok jelen vannak. Ha van ilyen, a koordinátákat, a nemeket és az életkort is létrehozza.|
||`ImageType` | észleli, ha a kép ClipArt vagy vonalas rajz.|
||`Objects` | észleli a képeken belüli különböző objektumokat, beleértve a hozzávetőleges helyet is. Az Objects argumentum csak angol nyelven érhető el.|
||`Tags` | a képet a képtartalommal kapcsolatos szavak részletes listájával címkézi.|
|`details`| `Celebrities` | azonosítja a hírességeket, ha a rendszerképben észlelt.|
||`Landmarks` |a rendszerképeken észlelt tereptárgyak azonosítására szolgál.|

A feltöltött URL-cím a következőhöz hasonló lehet:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>Nyelvek meghatározása

Megadhatja a visszaadott érték nyelvét is. A következő URL-lekérdezési paraméter határozza meg a nyelvet. Az alapértelmezett érték `en`.

|URL-paraméter | Érték | Leírás|
|---|---|--|
|`language`|`en` | Angol|
||`es` | Spanyol|
||`ja` | Japán|
||`pt` | Portugál|
||`zh` | Egyszerűsített kínai|

A feltöltött URL-cím a következőhöz hasonló lehet:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **Hatókörön belüli API-hívások**
>
> A képelemzés egyes funkciói meghívhatók közvetlenül, valamint az Analysis API-hívással is. Például a képcímkék hatókörön belüli elemzését elvégezheti egy kérelem elküldésével `https://{endpoint}/vision/v3.2-preview.3/tag` . Tekintse meg az egyéb, külön hívható szolgáltatások [dokumentációját](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) .

## <a name="get-results-from-the-service"></a>Eredmények beolvasása a szolgáltatásból

A szolgáltatás egy http-választ ad vissza `200` , és a törzs JSON-karakterlánc formájában tartalmazza a visszaadott adatok mennyiségét. A következő példa egy JSON-választ mutat be.

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

A példában szereplő mezők magyarázatait az alábbi táblázat tartalmazza:

Mező | Típus | Tartalom
------|------|------|
Címkék  | `object` | A címkék egy tömb legfelső szintű objektuma.
tags[].Name | `string`    | A címkét a címkék besorolása alapján.
tags[].Score    | `number`    | A megbízhatósági pontszám 0 és 1 között van.
leírás     | `object`    | A rendszerkép leírásának legfelső szintű objektuma.
description.tags[] |    `string`    | A címkék listája. Ha nem áll rendelkezésre elegendő megbízhatóság a képaláírás létrehozásához, akkor előfordulhat, hogy a címkék az egyetlen elérhető információ a hívó számára.
description.captions[].text    | `string`    | A képet leíró kifejezés.
description.captions[].confidence    | `number`    | A kifejezés megbízhatósági pontszáma.

### <a name="error-codes"></a>Hibakódok

Tekintse meg az alábbi listát a lehetséges hibákról és azok okairól:

* 400
    * InvalidImageUrl – a képurl-cím helytelen formátumú vagy nem érhető el.
    * InvalidImageFormat – a bemeneti adatok nem érvényes rendszerkép.
    * InvalidImageSize – a bemeneti rendszerkép túl nagy.
    * NotSupportedVisualFeature – a megadott szolgáltatástípus érvénytelen.
    * NotSupportedImage – nem támogatott rendszerkép, például gyermekpornográfia.
    * InvalidDetails – a `detail` paraméter értéke nem támogatott.
    * NotSupportedLanguage – a kért művelet nem támogatott a megadott nyelven.
    * BadArgument – további részletek a hibaüzenetben olvashatók.
* 415 – nem támogatott adathordozó-típusi hiba. A Content-Type típus nem az engedélyezett típusok:
    * Rendszerkép URL-címe: a Content-Type alkalmazásnak/JSON-nek kell lennie
    * Bináris képadatok esetén: a Content-Type alkalmazásnak/oktettnek kell lennie – stream vagy multipart/form – adatoknak kell lennie.
* 500
    * FailedToProcess
    * Időtúllépés – a rendszerkép feldolgozása időtúllépés miatt megtörtént.
    * InternalServerError

## <a name="next-steps"></a>Következő lépések

A REST API kipróbálásához nyissa meg a [rendszerkép-elemzési API-referenciát](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b).
