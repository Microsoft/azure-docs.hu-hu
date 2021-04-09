---
title: Többnyelvű tartalom automatikus azonosítása és átírása Video Indexer
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan lehet automatikusan azonosítani és átírni a többnyelvű tartalmakat Video Indexer használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 657ccafa0e7b7f640122fd6b397b3fa2a7c5f0fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015555"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Többnyelvű tartalom automatikus azonosítása és átírása

A Video Indexer támogatja az automatikus nyelvi azonosítást és a többszörös nyelvi tartalomban való átírást. Ezzel a folyamattal automatikusan azonosítható a beszélt nyelv különböző szegmensekben a hanganyagból, elküldve a médiafájl minden egyes szegmensét, és az átírást egyesítheti egy egységes átírással. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Többnyelvű azonosítás kiválasztása az indexeléshez a portálon

A videó feltöltésekor és indexelése során a **többnyelvű észlelést** is választhatja. Azt is megteheti, hogy a videó újbóli indexelése során kiválaszthatja a **többnyelvű észlelést**  is. Az alábbi lépések az újraindexelést ismertetik:

1. Nyissa meg a [Video Indexer](https://vi.microsoft.com/) webhelyét, és jelentkezzen be.
1. Nyissa meg a **könyvtár** lapot, és vigye a kurzort az újraindexelni kívánt videó nevére. 
1. A jobb alsó sarokban kattintson a **videó újraindexelése** gombra. 
1. A **videó újraindexelése** párbeszédpanelen válassza a **többnyelvű felismerés** lehetőséget a **videó forrása nyelv** legördülő listából.

    * Ha egy videó több nyelvként van indexelve, a betekintési oldal tartalmazza ezt a lehetőséget, és megjelenik egy további betekintési típus, amely lehetővé teszi a felhasználó számára, hogy megtekintse, melyik szegmenst írja le a rendszer a "beszélt nyelv" nyelvre.
    * Az összes nyelvre való fordítás teljes mértékben elérhető a többnyelvű átiratból.
    * A rendszer az összes többi elemzést a fő nyelven fogja észlelni – ez az a nyelv, amely a legtöbb hangon megjelent.
    * A lejátszón a kódolt feliratok is elérhetők a több nyelven is.

![Portal-felület](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Többnyelvű azonosítás kiválasztása API-val való indexeléshez

Amikor az API használatával indexel vagy [újraindexel](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) egy videót, válassza a `multi-language detection` paraméterben található lehetőséget `sourceLanguage` .

### <a name="model-output"></a>A modell kimenete

A modell lekéri az összes, a videóban észlelt nyelvet egy listában

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Emellett az átírási szakasz minden példánya tartalmazza azt a nyelvet, amelyben az átirata szerepelt

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Irányelvek és korlátozások

* Támogatott nyelvek készlete: angol, francia, német, spanyol.
* Többnyelvű tartalom támogatása legfeljebb három támogatott nyelvvel.
* Ha a hang a fenti támogatott listán kívül más nyelveket is tartalmaz, az eredmény nem várt.
* A szegmens minimális hossza az egyes nyelvek észleléséhez – 15 másodperc.
* A nyelvfelismerés eltolása 3 másodperc az átlagnál.
* A beszédfelismerésnek várhatóan folyamatosnak kell lennie. A nyelvek közötti gyakori váltakozás hatással lehet a modellek teljesítményére.
* A nem natív hangszórókról szóló beszéd hatással lehet a modell teljesítményére (például ha a hangszórók a natív nyelvüket használják, és más nyelvre váltanak).
* A modell úgy lett kialakítva, hogy felismerje az ésszerű hang-akusztikai (nem hangparancsokat, éneklést stb.) hangvezérelt beszédet.
* A projekt létrehozása és szerkesztése jelenleg nem érhető el többnyelvű videókhoz.
* A többnyelvű észlelés használata esetén az egyéni nyelvi modellek nem érhetők el.
* A kulcsszavak hozzáadása nem támogatott.
* A lezárt képaláírás-fájlok exportálásakor a nyelvi jelzés nem fog megjelenni.
* A frissítési átirat API nem támogatja több nyelvi fájl használatát.

## <a name="next-steps"></a>Következő lépések

[A Video Indexer áttekintése](video-indexer-overview.md)
