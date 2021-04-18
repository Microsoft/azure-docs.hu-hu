---
title: Beszélt Video Indexer automatikus azonosítása a Video Indexer használatával – Azure
titleSuffix: Azure Media Services
description: Ez a cikk azt ismerteti, Video Indexer nyelvi azonosítási modell hogyan használható a beszélt nyelv automatikus azonosítására egy videóban.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 40f2e146956919e154f59d90b56a1b03379abbb2
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600637"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>A beszélt nyelv automatikus felismerése nyelvazonosítási modellel

Video Indexer támogatja az automatikus nyelvazonosítást (AUTOMATIC), amely a hanganyagból származó beszélt nyelvi tartalom automatikus azonosítását és a médiafájlnak a domináns azonosított nyelven történő átírását jelenti. 

Jelenleg a KÖVETKEZŐ nyelveket támogatja: angol, spanyol, francia, német, olasz, mandarin kínai, japán, orosz és portugál (brazíliai). 

Mindenképpen tekintse át az [alábbi Irányelvek és korlátozások](#guidelines-and-limitations) szakaszt.

## <a name="choosing-auto-language-identification-on-indexing"></a>Az indexelés automatikus nyelvazonosításának kiválasztása

Amikor az API [használatával indexel](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) vagy újra indexel egy videót, válassza a `auto detect` paraméterben a `sourceLanguage` lehetőséget.

A portál használata esetén  a kezdőlapon Video Indexer fiókvideóit, és vigye az egérmutatót az újraindexelni kívánt videó nevére. [](https://www.videoindexer.ai/) A jobb alsó sarokban kattintson az Index újraindexelése gombra. A Videó **újraindexelése párbeszédpanelen** válassza az *Automatikus észlelés* lehetőséget a **Videó forrásnyelve** legördülő listában.

![automatikus észlelés](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>A modell kimenete

Video Indexer a legvalószínűbb nyelvnek megfelelően írja át a videót, ha a nyelv megbízhatósága `> 0.6` . Ha a nyelv nem azonosítható magabiztosan, azt feltételezi, hogy a beszélt nyelv az angol. 

A modell domináns nyelve attribútumként érhető el az elemzés JSON-fájlban `sourceLanguage` (a root/videos/insights alatt). A attribútum alatt egy megfelelő megbízhatósági pontszám is `sourceLanguageConfidence` elérhető.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Irányelvek és korlátozások

* Az automatikus nyelvazonosítás (AUTOMATIC Language Identification, A MIT) a következő nyelveket támogatja: 

    Angol, spanyol, francia, német, olasz, mandarin kínai, japán, orosz és portugál (brazíliai).
* Bár a Video Indexer támogatja az arab (Modern Standard és Levantine), hindi és koreai nyelveket, ezek a nyelvek nem támogatottak a TUD-ban.
* Ha a hanganyag a fenti támogatott listán kívül más nyelveket is tartalmaz, az eredmény váratlan lesz.
* Ha Video Indexer nem tudja azonosítani a nyelvet elég magabiztosan ( ), a tartalék nyelv `>0.6` az angol.
* A vegyes nyelvű hangfájlokat jelenleg nem támogatja a rendszer. Ha a hanganyag vegyes nyelveket tartalmaz, az eredmény váratlan. 
* A gyenge minőségű hanganyag hatással lehet a modell eredményeire.
* A modellnek legalább egy perc beszédre van szüksége a hanganyagban.
* A modell úgy lett kialakítva, hogy felismerje a beszélgetési beszédeket (nem pedig hangparancsokat, egyebeket).

## <a name="next-steps"></a>Következő lépések

* [Áttekintés](video-indexer-overview.md)
* [Többnyelvű tartalom automatikus azonosítása és átírása](multi-language-identification-transcription.md)
