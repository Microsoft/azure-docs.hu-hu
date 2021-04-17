---
title: Video Indexer, felvételek és kulcsképek
titleSuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt a Video Indexer, a felvételekről és a kulcskockákról.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 5a738152296aacbb5914e859a65976bd0f6dbf0a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532423"
---
# <a name="scenes-shots-and-keyframes"></a>Jelenetek, felvételek és kulcsképkockák

Video Indexer támogatja a videók időbeli egységekre szegmentálását szerkezeti és szemantikai tulajdonságok alapján. Ez a funkció lehetővé teszi, hogy az ügyfelek könnyedén, különböző részletességgel böngésszen és szerkessze a videós tartalmakat. Például a jelen témakörben ismertetett jelenetek, felvételek és kulcsképek alapján.   

![Jelenetek, felvételek és kulcsképkockák](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Jelenetészlelés  
 
Video Indexer vizuális jel alapján határozza meg, hogy egy jelenet mikor változik a videóban. A jelenet egyetlen eseményt ábrázol, és egymás utáni felvételek sorozatát alkotja, amelyek szemantikailag kapcsolódnak egymáshoz. A jelenet miniatűrje az alapul szolgáló kép első kulcsképe. A Video Indexer színkomerencia alapján, egymást követő felvételekre szegmentál egy videót, és lekéri az egyes jelenetek kezdő és záró idejét. A jelenetészlelés kihívást jelentő feladat, mivel a videók szemantikai aspektusainak számszerűsítését jelenti.

> [!NOTE]
> Olyan videókra vonatkozik, amelyek legalább 3 jeleneteket tartalmaznak.

## <a name="shot-detection"></a>Felvételészlelés

Video Indexer vizuális jel alapján meghatározza, hogy mikor változik egy felvétel a videóban, a szomszédos képkockák színsémában való hirtelen és fokozatos átmenetek nyomon követésével. A felvétel metaadatai tartalmazzák a kezdési és a záró időt, valamint a felvételben szereplő kulcskeretek listáját. A felvételek egymást követő képkockák, amelyek ugyanattól a kamerától egyidejűleg készültek.

## <a name="keyframe-detection"></a>Kulcsképkockák észlelése

Video Indexer az egyes felvételeket legjobban ábrázoló képkocká(k)t választja ki. A kulcskeretek a teljes videóból az esztétikai tulajdonságok (például kontraszt és stabilitás) alapján kiválasztott reprezentatív képkockák. Video Indexer lekéri a kulcskeret-adatokat a felvétel metaadatainak részeként, amelyek alapján az ügyfelek nagy felbontású képként kinyerik a kulcsképkockát.  

### <a name="extracting-keyframes"></a>Kulcskeretek kinyerés

Ha nagy felbontású kulcsképkockákat kell kinyernie a videóhoz, először fel kell töltenie és indexelnie kell a videót.

![Kulcsképek](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>A Video Indexer webhelyén

Ha kulcsképkockákat a Video Indexer, töltse fel és indexelja a videót. Az indexelési feladat befejezése után kattintson a Letöltés **gombra,** és válassza az **Artifacts (ZIP) (Összetevők (ZIP) lehetőséget.** Ezzel letölti az artifacts mappát a számítógépre. 

![Képernyőkép a "Letöltés" legördülő menüről, az "Összetevők" elem kijelölve.](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Csomagolja ki és nyissa meg a mappát. A *_KeyframeThumbnail* mappában találja a videóból kinyert összes kulcsképkockát. 

#### <a name="with-the-video-indexer-api"></a>A Video Indexer API-val

Ha kulcsképkockákat a Video Indexer API-val, töltse fel és indexelja a videót a [Videó feltöltése hívás](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) használatával. Az indexelési feladat befejezése után hívja meg a [Get Video Index (Videóindex lehívása) hívást.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index) Ez minden olyan információt tartalmaz, Video Indexer JSON-fájlban található tartalomból nyerhet ki.  

Az egyes felvétel metaadatainak részeként le fogja kapni a kulcsképkockákhoz való kulcskép-adatokat. 

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

Most le kell futtatnia ezeket a kulcsképkockákat a [Get Thumbnails híváson.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) Ezzel letölti a keyframe-rendszerképeket a számítógépre. 

## <a name="editorial-shot-type-detection"></a>Szerkesztői felvétel típusának észlelése

A kulcskeretek a kimeneti JSON-fájlban a felvételekhez vannak társítva. 

Az elemzés JSON-fájlban egy adott felvételhez társított felvételtípus a szerkesztői típust jelöli. Ezek a felvételtípus-jellemzők akkor lehetnek hasznosak, ha videókat szerkeszt videóklipekbe vagy előzetesekbe, vagy ha egy adott típusú kulcsképkockát keres esztétes célokra. A különböző típusokat az egyes felvételek első kulcskeretének elemzése határozza meg. A felvételeket az első kulcskeretben megjelenő arcok mérete, mérete és helye azonosítja. 

A felvétel méretét és méretét a kamera és a keretben megjelenő arcok távolsága határozza meg. Ezekkel a tulajdonságokkal a Video Indexer a következő felvételtípusokat észleli:

* Széles: egy teljes személy testét jeleníti meg.
* Közepes: egy személy felső törzsét és arcát jeleníti meg.
* Közelről: főleg egy személy arcát mutatja.
* Szélsőséges közelről: a képernyő kitöltését jeleníti meg egy személy arcával. 

A felvételtípusokat a tárgy karakterének helye is meg lehet határozni a képkocka közepétől függően. Ez a tulajdonság a következő felvételtípusokat határozza meg a Video Indexer:

* Bal arc: egy személy jelenik meg a képkocka bal oldalán.
* Középső arc: egy személy jelenik meg a képkocka középső régiójában.
* Jobb arc: egy személy jelenik meg a képkocka jobb oldalán.
* Kültér: egy személy külső környezetben jelenik meg.
* Beltéri: egy személy beltéri környezetben jelenik meg.

További jellemzők:

* Két felvétel: két személy közepes méretű arcát mutatja.
* Több arc: több mint két személy.


## <a name="next-steps"></a>Következő lépések

[Az API Video Indexer által előállított kimeneti adatok vizsgálata](video-indexer-output-json-v2.md#scenes)
