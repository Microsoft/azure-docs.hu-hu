---
title: Többnyelvű tartalom automatikus azonosítása és átírása Video Indexer
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan azonosíthatja és írhatja át automatikusan a többnyelvű tartalmakat a Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 319bd408943c560622dc3208a6701417b8ca010c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532907"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Többnyelvű tartalom automatikus azonosítása és átírása

Video Indexer támogatja a többnyelvű tartalmak automatikus nyelvazonosítását és átírását. Ez a folyamat magában foglalja a beszélt nyelv automatikus azonosítását a hang különböző szegmenseiből, a médiafájl egyes szegmensei átírását, majd az átírás egyetlen egységes átírásba való összevonását. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Többnyelvű azonosítás kiválasztása a portálon való indexeléshez

A videó **feltöltésekor** és indexelésekor választhat a többnyelvű detektálás közül. A videó újraindexelésekor a többnyelvű észlelést is választhatja.  A következő lépések az újraindexelést ismertetik:

1. Nyissa meg a [Video Indexer](https://vi.microsoft.com/) webhelyét, és jelentkezzen be.
1. Az **Újraindexelni kívánt** videó nevére vigye az egérmutatót az Library (Könyvtár) lapra. 
1. A jobb alsó sarokban kattintson a **Videó újraindexelése gombra.** 
1. A Videó **újraindexelése párbeszédpanelen** válassza a  Videóforrás nyelvének észlelése legördülő menüt. 

    * Ha egy videó több nyelvként van indexelve, az elemzés oldalán megjelenik egy további elemzéstípus, amely lehetővé teszi a felhasználó számára, hogy megtekintse, melyik nyelven írták át a "Beszélt nyelv" szakaszt.
    * A fordítás minden nyelvre teljes körűen elérhető a többnyelvű átiratból.
    * Minden egyéb elemzés megjelenik az észlelt főnyelven – ez az a nyelv, amely a legtöbbet jelent meg a hanganyagban.
    * A lejátszó feliratai több nyelven is elérhetők.

![Portal-felület](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Többnyelvű azonosítás kiválasztása az API-val való indexeléshez

Amikor az API használatával indexel vagy [újraindexel](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) egy videót, válassza a `multi-language detection` paraméterben megadott `sourceLanguage` lehetőséget.

### <a name="model-output"></a>A modell kimenete

A modell lekéri a videóban észlelt összes nyelvet egy listában

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Emellett az átírási szakasz minden példánya tartalmazza az átírás nyelvét

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

* Támogatott nyelvek: angol, francia, német, spanyol.
* Többnyelvű tartalom támogatása legfeljebb három támogatott nyelvvel.
* Ha a hanganyag a fenti támogatott listán kívül más nyelveket is tartalmaz, az eredmény nem várt.
* Minimális szegmenshossz az egyes nyelveken – 15 másodperc.
* A nyelvfelismerés eltolása átlagosan 3 másodperc.
* A beszéd várhatóan folyamatos lesz. A nyelvek közötti gyakori váltakozódások hatással lehetnek a modellek teljesítményére.
* A nem natív beszélők beszéde befolyásolhatja a modell teljesítményét (például ha a beszélők a natív hangjukat használják, és egy másik nyelvre váltanának).
* A modell úgy lett kialakítva, hogy felismerje a beszédhangot ésszerű hangkusztika (nem hangparancsok, egyebek stb.) segítségével.
* A projektek létrehozása és szerkesztése jelenleg nem érhető el a többnyelvű videókhoz.
* Az egyéni nyelvi modellek nem érhetők el a többnyelvű detektálás használata esetén.
* Kulcsszavak hozzáadása nem támogatott.
* A feliratfájlok exportálásakor a nyelvi jelzés nem jelenik meg.
* A frissítési átirat API nem támogatja a több nyelvi fájlt.

## <a name="next-steps"></a>Következő lépések

[A Video Indexer áttekintése](video-indexer-overview.md)
