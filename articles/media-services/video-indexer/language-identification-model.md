---
title: A beszélt nyelvek automatikus azonosításának Video Indexer használata az Azure-ban
titleSuffix: Azure Media Services
description: Ez a cikk azt ismerteti, hogyan használható a Video Indexer nyelvi azonosító modell a beszélt nyelvnek a videóban való automatikus azonosítására.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81687124"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>A beszélt nyelv automatikus felismerése nyelvazonosítási modellel

A Video Indexer támogatja az automatikus nyelvi azonosítást (FEDŐt), amely automatikusan azonosítja a beszélt nyelvi tartalmakat a hangból, és elküldi a médiafájlt a dominánsan azonosított nyelven. 

A fedél jelenleg a következőket támogatja: angol, spanyol, francia, német, olasz, mandarin kínai, Japán, orosz és portugál (brazíliai). 

Tekintse át az alábbi [irányelvek és korlátozások](#guidelines-and-limitations) szakaszt.

## <a name="choosing-auto-language-identification-on-indexing"></a>Automatikus nyelvi azonosítás kiválasztása az indexeléshez

Ha az API használatával indexel vagy [újraindexel](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) egy videót, válassza a `auto detect` paraméterben található lehetőséget `sourceLanguage` .

A portál használatakor a [video Indexer](https://www.videoindexer.ai/) kezdőlapján keresse meg a **fiókja videóit** , és vigye az egérmutatót az újra indexelni kívánt videó nevére. A jobb alsó sarokban kattintson az újra index gombra. A **videó újraindexelése** párbeszédpanelen válassza az *automatikus észlelés* lehetőséget a **videó forrása nyelv** legördülő listából.

![automatikus észlelés](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>A modell kimenete

Video Indexer a videót a legvalószínűbb nyelven írja át, ha az adott nyelv megbízhatósága `> 0.6` . Ha a nyelv nem azonosítható magabiztosan, feltételezi, hogy a beszélt nyelv angol. 

A modell domináns nyelve az adatellenőrzési JSON-ban az `sourceLanguage` attribútumként érhető el (a root/videos/bepillantás alatt). A megfelelő megbízhatósági pontszám az attribútum alatt is elérhető `sourceLanguageConfidence` .

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

* Az automatikus nyelvi azonosító (fedél) a következő nyelveket támogatja: 

    Angol, spanyol, francia, német, olasz, mandarin Chines, Japán, orosz és portugál (brazíliai).
* Bár a Video Indexer támogatja az Arab (modern standard és levantei), a hindi és a Koreai nyelvet, a fedél nem támogatja ezeket a nyelveket.
* Ha a hang a fenti támogatott listán kívül más nyelveket is tartalmaz, az eredmény nem várt.
* Ha Video Indexer nem tudja azonosítani a nyelvet elég magas megbízhatósággal ( `>0.6` ), a tartalék nyelv angol.
* A vegyes nyelvi hanggal rendelkező fájlok jelenleg nem támogatottak. Ha a hang vegyes nyelveket tartalmaz, az eredmény nem várt. 
* Az alacsony minőségű hang befolyásolhatja a modell eredményeit.
* A modellhez legalább egy perces beszéd szükséges a hanganyagban.
* A modell úgy lett kialakítva, hogy felismerje a spontán társalgási beszédeket (nem hangparancsokat, éneklést stb.).

## <a name="next-steps"></a>Következő lépések

* [Áttekintés](video-indexer-overview.md)
* [Többnyelvű tartalom automatikus azonosítása és átírása](multi-language-identification-transcription.md)
