---
title: Video Indexer jelenetek, felvételek és kulcsképek
titleSuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt a Video Indexer jelenetekről, a képekről és a kulcsképekről.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 248799d70e0741efcaea1714c12f4d92a42cef25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041910"
---
# <a name="scenes-shots-and-keyframes"></a>Jelenetek, felvételek és kulcsképkockák

Video Indexer támogatja a videók szegmentálását a strukturális és szemantikai tulajdonságok alapján. Ez a funkció lehetővé teszi az ügyfeleknek, hogy különböző részletességek alapján könnyedén böngészhetik, kezelhetik és szerkeszthetik a videó tartalmát. Például a jelen témakörben ismertetett jelenetek, felvételek és kulcsképek alapján.   

![Jelenetek, felvételek és kulcsképkockák](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Jelenet észlelése  
 
Video Indexer határozza meg, hogy a jelenet Mikor változik a videóban a vizualizációs célzások alapján. Egy jelenet egyetlen eseményt ábrázol, és egymást követő felvételekből áll, amelyek szemantikai kapcsolatban állnak egymással. A jelenet miniatűrje a mögöttes felvétel első képkockája. A video Indexer egy videót helyez el a jeleneteken az egymást követő felvételek közötti színkoherencia alapján, és lekéri az egyes jelenetek kezdő és záró időpontját. A jelenet észlelése kihívást jelentő feladat, mivel a videók szemantikai szempontjait is magában foglalja.

> [!NOTE]
> Olyan videókra vonatkozik, amelyek legalább 3 jelenetet tartalmaznak.

## <a name="shot-detection"></a>Lövés észlelése

Video Indexer meghatározza, hogy mikor változik a videó a vizualizációs dákó alapján, hogy a rendszer hirtelen és fokozatos átmeneteket is nyomon követ a szomszédos keretek színsémájában. A shot metaadatai a kezdő és a záró időpontot is tartalmazzák, valamint a lövéshez tartozó kulcsképek listáját. A felvételek egymást követő képkockák, amelyek ugyanabból a kamerából származnak.

## <a name="keyframe-detection"></a>Kulcsképek észlelése

Video Indexer kiválasztja az egyes lövésekhez legjobban illő keret (eke) t. A kulcsképek a teljes videóban az esztétikai tulajdonságok (például a kontraszt és a stabilitás) alapján kiválasztott reprezentatív keretek. Video Indexer lekéri a képkockák azonosítóinak listáját a shot metaadatainak részeként, amely alapján az ügyfelek nagy felbontású képként kinyerhetik a képkockát.  

### <a name="extracting-keyframes"></a>Kulcsképek kibontása

A videóhoz tartozó nagy felbontású kulcsképek kinyeréséhez először fel kell töltenie és indexelni kell a videót.

![Kulcsképek](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>A Video Indexer webhelyén

A kulcsképek kinyeréséhez használja a Video Indexer webhelyet, töltse fel és indexelje a videóját. Az indexelési feladatok befejezését követően kattintson a **Letöltés** gombra, és válassza az összetevők **(zip)** lehetőséget. Ezzel letölti az összetevők mappát a számítógépére. 

![Képernyőfelvétel: a "Letöltés" legördülő lista "összetevőkkel" van kiválasztva.](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Bontsa ki és nyissa meg a mappát. A *_KeyframeThumbnail* mappában a videóból kinyert összes kulcsképek megtalálhatók. 

#### <a name="with-the-video-indexer-api"></a>A Video Indexer API-val

Ha a Video Indexer API-val szeretné lekérni a kulcsképek használatát, töltse fel és indexelje a videót a [videó feltöltése](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) hívás használatával. Az indexelési feladatok befejezése után hívja meg a [Get video indexet](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). Ez megadja az összes olyan bepillantást, amelyet a rendszer egy JSON-fájlban lévő tartalomból Video Indexer kinyerni.  

Ekkor megjelenik a kulcsképek azonosítóinak listája az egyes shot metaadatok részeként. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Ekkor futtatnia kell ezeket a kulcsképek-azonosítókat a [miniatűrök beolvasása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) hívásban. Ezzel letölti a kulcsképek lemezképeit a számítógépre. 

## <a name="editorial-shot-type-detection"></a>Szerkesztői shot típusú észlelés

A kulcsképek a kimenet JSON-ban található felvételekhez vannak társítva. 

Az adatforrások JSON-beli felvételéhez tartozó shot típus a szerkesztői típust jelöli. Ezek a shot Type-jellemzők hasznosak lehetnek a videók klipek, pótkocsik számára történő szerkesztésekor, illetve a képkockák adott stílusának művészi célokra való keresésekor. A különböző típusok meghatározása az egyes lövések első képkockájának elemzése alapján történik. A képeket az első képkockában megjelenő arcok méretezése, mérete és helye azonosítja. 

A shot méretének és méretezésének meghatározása a kamera és a keretben megjelenő arcok távolsága alapján történik. Ezeknek a tulajdonságoknak a használatával a Video Indexer észleli a következő shot típusokat:

* Széles: egy teljes személy törzsét jeleníti meg.
* Közepes: egy személy felső törzsét és arcát mutatja.
* Bezárás: elsősorban egy személy arcát mutatja.
* Extreme Közelkép: egy személy arcát jeleníti meg a képernyő kitöltésével. 

A lövési típusok a tulajdonos karakterek helye alapján is meghatározhatók a keret közepéhez képest. Ez a tulajdonság határozza meg a következő shot típusokat a Video Indexerban:

* Bal oldali: a keret bal oldalán egy személy jelenik meg.
* Középső arc: egy személy jelenik meg a keret középső régiójában.
* Jobb oldali: egy személy jelenik meg a keret jobb oldalán.
* Kültéri: egy személy jelenik meg egy külső beállításban.
* Beltéri: egy személy megjelenik egy beltéri környezetben.

További jellemzők:

* Két kép: két személy közepes méretű arca látható.
* Több arc: több mint két személy.


## <a name="next-steps"></a>Következő lépések

[Vizsgálja meg az API által létrehozott Video Indexer kimenetet](video-indexer-output-json-v2.md#scenes)
