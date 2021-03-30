---
title: Személy modell testreszabása Video Indexer API-val
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan szabhatja testre a személy modelljét a Video Indexer API-val.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 09366dea1a0d77052b6f99e9f5ab52c270e341b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87047008"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Személy modell testreszabása a Video Indexer API-val

A Video Indexer támogatja az arcfelismerés és a Celebrity-felismerés használatát a videók tartalmához. A híresség-felismerési funkció a gyakran igényelt adatforrások (például a IMDB, a wikipedia és a legnépszerűbb LinkedIn-befolyásoló) alapján 1 000 000 Faces-t ölel fel. A híresség-felismerési funkció által nem felismerhető arcok észlelhetők, de a név nem a neve. Miután feltöltötte a videót Video Indexerre, és visszakeresi az eredményeket, visszatérhet, és megadhatja azokat az arcokat, amelyeket nem ismert fel. Miután felcímkéz egy nevet a névvel, az arc és a név bekerül a fiókja személy modelljébe. Ezt követően a Video Indexer felismeri ezt az arcot a későbbi videókban és a korábbi videókban is.

A jelen témakörben ismertetett módon szerkesztheti a videóban észlelt arcokat a Video Indexer API használatával. A Video Indexer webhelyét a [személy modell testreszabása a video Indexer webhelyről](customize-person-model-with-api.md)című témakörben leírtak szerint is használhatja.

## <a name="managing-multiple-person-models"></a>Több személyből származó modellek kezelése

A Video Indexer fiókkal több person modellt is támogat. Ez a funkció jelenleg csak a Video Indexer API-kon keresztül érhető el.

Ha a fiókja más használati esetekre is kiterjed, érdemes lehet több személyből származó modellt létrehozni. Ha például a tartalma a sporthoz kapcsolódik, létrehozhat egy külön személy modellt minden sportághoz (labdarúgás, kosárlabda, futball stb.).

A modell létrehozása után használhatja azt egy adott személy modell AZONOSÍTÓjának megadásával, amikor feltölti/indexeli vagy újraindexeli a videót. A videó új arca betanítása frissíti azt a konkrét egyéni modellt, amelyhez a videó társítva lett.

Minden fiókhoz legfeljebb 50 személy-modell tartozik. Ha nincs szüksége a több személyre vonatkozó modell támogatására, ne rendeljen hozzá egy személy modell-azonosítót a videóhoz feltöltés/indexelés vagy újraindexelés során. Ebben az esetben a Video Indexer az alapértelmezett egyéni személy modellt használja a fiókjában.

## <a name="create-a-new-person-model"></a>Új személy modell létrehozása

Ha új személy modellt szeretne létrehozni a megadott fiókban, használja a [create a person Model](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) API-t.

A válasz megadja az imént létrehozott személy modell nevét és a generált modell AZONOSÍTÓját, amelyet az alábbi példa formátuma követ.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Ezután használja a **personModelId** paraméter **azonosító** értékét, amikor [feltölt egy videót egy videó indexeléséhez](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) vagy [újraindexeléséhez](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Személy modell törlése

Ha törölni szeretne egy egyéni személy modellt a megadott fiókból, használja a [személyes modell törlése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) API-t.

Miután sikeresen törölte a személy modelljét, a törölt modellt használó aktuális videók indexe változatlan marad, amíg újra nem indexeli őket. Az újraindexelés során a törölt modellben megnevezett arcok nem ismerhetők fel Video Indexer az adott modellel indexelt aktuális videókon, de az arcok továbbra is észlelhetők lesznek. A törölt modell használatával indexelt aktuális videók mostantól a fiók alapértelmezett személyének modelljét fogják használni. Ha a törölt modellből származó arcok a fiók alapértelmezett modelljében is megtalálhatók, akkor a rendszer továbbra is felismeri ezeket az arcokat a videókban.

A személy modelljének törlése után nincs visszaadott tartalom.

## <a name="get-all-person-models"></a>Összes modell lekérése

A megadott fiókban található összes modell beszerzéséhez használja a [személyre szabott modell](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) API-t.

A válasz felsorolja a fiókban lévő összes személy modelljét (beleértve a megadott fiókban található alapértelmezett személy modellt is) és az alábbi példa formátumát követő neveket és azonosítókat.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Kiválaszthatja, hogy melyik modellt kívánja használni a videóhoz, `id` `personModelId` Ha egy videót tölt fel az [indexbe](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) , vagy [újraindexel egy](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)videót.

## <a name="update-a-face"></a>Arc frissítése

Ez a parancs lehetővé teszi a videóban lévő arc frissítését a videó és az arc AZONOSÍTÓját használó névvel. Ez a művelet ezután frissíti azt a személy-modellt, amelyet a videó a feltöltéshez, az indexeléshez vagy az újraindexeléshez társított. Ha nincs személy modell rendelve, akkor a fiók alapértelmezett személy modelljét frissíti.

A rendszer ezután felismeri, hogy ugyanaz az arc fordul elő a többi aktuális videóban, amelyek ugyanazt a személyt használják. A többi aktuális videóban lévő arc felismerése hosszabb időt is igénybe vehet, mivel ez egy batch-folyamat.

Frissíthet egy olyan arcot, amely új néven hírességként Video Indexer ismert. Az Ön által megadott új név elsőbbséget élvez a beépített híresség felismerésével szemben.

Az arc frissítéséhez használja a [video Face API frissítése lapot](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) .

A nevek egyediek a személy modellek esetében, így ha két különböző arcot ad meg ugyanabban a `name` paraméter-értékben, video Indexer megtekinti az arcokat ugyanazzal a személlyel, és a videó újraindexelése után átszervezi őket.

## <a name="next-steps"></a>Következő lépések

[Személy modell testreszabása a Video Indexer webhely használatával](customize-person-model-with-website.md)
