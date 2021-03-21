---
title: Video Indexer fogalmak – Azure
titleSuffix: Azure Media Services Video Indexer
description: Ez a cikk rövid áttekintést nyújt a Azure Media Services Video Indexer terminológiáról és fogalmakról.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633981"
---
# <a name="video-indexer-concepts"></a>Video Indexer fogalmak

Ez a cikk rövid áttekintést nyújt a Azure Media Services Video Indexer terminológiáról és fogalmakról.

## <a name="audiovideocombined-insights"></a>Hang/videó/összetett elemzések

A videók Video Indexer való feltöltésekor a vizualizációkat és a hanganyagokat különböző AI-modellek futtatásával elemezze. Ahogy Video Indexer elemzi a videót, az AI-modellek által kinyert elemzéseket. További információ: [Áttekintés](video-indexer-overview.md).

## <a name="confidence-scores"></a>Megbízhatósági pontszámok

A megbízhatósági pontszám a betekintés megbízhatóságát jelzi. 0,0 és 1,0 közötti szám. Minél magasabb a pontszám – annál nagyobb a válaszban megjelenő bizalom. Példa: 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>Tartalom moderálása

A szöveges és a vizuális tartalom moderálási modelljei segítségével a felhasználók biztonságban maradhatnak a nem megfelelő tartalomtól, és ellenőrizheti, hogy a közzétett tartalom megfelel-e a szervezet értékeinek. Automatikusan blokkolhat bizonyos videókat, vagy riasztást kaphat a felhasználóknak a tartalomról. További információkért lásd: a [vizualizációk és a szöveges tartalom moderálása](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Blokkok   

A blokkok célja, hogy megkönnyítse az adattovábbítást. A blokkokat például aszerint lehet bontani, hogy mikor váltanak a beszélők, vagy hogy mikor tartanak hosszú szünetet.  

## <a name="project-and-editor"></a>Projekt és szerkesztő

A [video Indexer](https://www.videoindexer.ai/) webhely lehetővé teszi, hogy a videó mélyreható megállapításait használja: keresse meg a megfelelő médiatartalom tartalmát, keresse meg a kívánt részeket, és használja az eredményeket egy teljesen új projekt létrehozásához. A létrehozást követően a projekt megjeleníthető és letölthető Video Indexer, és használható a saját szerkesztési alkalmazásaiban vagy az alárendelt munkafolyamatokban.

Bizonyos esetekben hasznos lehet a funkció: 

* Movie Highlights for Trailers létrehozása
* A videók régi videoklipek használata a hírekben.
* Rövidebb tartalom létrehozása a közösségi médiához.

További információ: [projektek létrehozása a szerkesztő használatával](use-editor-create-project.md).

## <a name="keyframes"></a>Kulcsképek

Video Indexer kiválasztja az egyes lövésekhez legjobban illő keret (eke) t. A kulcsképek a teljes videóban az esztétikai tulajdonságok (például a kontraszt és a stabilitás) alapján kiválasztott reprezentatív keretek. További információ: [jelenetek, felvételek és kulcsképek](scenes-shots-keyframes.md).

## <a name="time-range-vs-adjusted-time-range"></a>időtartomány és a beállított időtartomány   

A TimeRange az eredeti videó időtartománya. A AdjustedTimeRange az aktuális lejátszási listához viszonyított időtartomány. Mivel a különböző videók különböző soraiból is létrehozhat egy lejátszási listát, igénybe vehet egy 1 órás videót, és akár 1 sort is használhat, például 10:00-10:15. Ebben az esetben egy olyan lejátszási listával fog rendelkezni, amelyben az időtartomány 10:00-10:15, a adjustedTimeRange pedig a 00:00-00:15. 

## <a name="widgets"></a>Vezérlők

A Video Indexer támogatja az alkalmazásokban lévő widgetek beágyazását. További információkért lásd: [video Indexer widgetek beágyazása az alkalmazásokba](video-indexer-embed-widgets.md).

## <a name="summarized-insights"></a>Összesített bepillantást  

Az összesített elemzések az alábbi összesített nézetet tartalmazzák: arcok, témakörök, érzelmek. Például ahelyett, hogy a több ezer időtartományra, illetve annak ellenőrzésére, hogy mely arcok találhatók, az összesített megállapítások tartalmazzák az összes arcot és az egyes, a megjelenő időtartományokat és a megjelenített idő%-át.  

## <a name="next-steps"></a>Következő lépések

- [áttekintés](video-indexer-overview.md)
- [Insights](video-indexer-output-json-v2.md)
