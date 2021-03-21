---
title: Migrálás az indexelő v1 és v2 rendszerről Azure Media Services Video Indexerra | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan lehet áttérni Azure Media Indexer v1 és v2 rendszerről Azure Media Services Video Indexerre.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: eb4f662a4b34d748da0e97da9b64a386be3f5c49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016423"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrálás Media Indexer és Media Indexer 2 – Video Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Javasoljuk, hogy az ügyfelek migrálása az indexelő v1 és az indexelő v2 rendszerről a [Media Services v3 AudioAnalyzerPreset alapszintű mód](../latest/analyzing-video-audio-files-concept.md)használatára. Folyamatban van a [Azure Media Indexer](media-services-index-content.md) adathordozó-feldolgozó és [Azure Media Indexer 2 előzetes](./legacy-components.md) verziójú adathordozó-feldolgozó kivonása. A nyugdíjazási dátumokért tekintse meg ezt a [régi összetevőket](legacy-components.md) ismertető témakört.

A Azure Media Services Video Indexer Azure Media Analytics, Azure Cognitive Search, Cognitive Servicesra épül (például a Face API, a Microsoft Translator, a Computer Vision API és Custom Speech Service). Lehetővé teszi, hogy megállapításokat nyerjen ki a videóiból a Video Indexer hang- és videómodelljeivel. Ha szeretné megtekinteni, hogy milyen forgatókönyvek Video Indexer használhatók a alkalmazásban, milyen funkciókat kínál, és hogyan kezdheti el az első lépéseket, tekintse meg [video Indexer videó-és hangmodelleket](../video-indexer/video-indexer-overview.md). 

A videó-és hangfájlokból kinyerheti az elemzéseket az [Azure Media Services v3 Analyzer-előállítók](../latest/analyzing-video-audio-files-concept.md) használatával, vagy közvetlenül a [video Indexer API](https://api-portal.videoindexer.ai/)-k használatával. Jelenleg a Video Indexer API-k és a Media Services V3 API-k által kínált funkciók átfedésben vannak.

> [!NOTE]
> Az Video Indexer vs. Media Services Analyzer-készletek közötti különbségek megismeréséhez tekintse meg az [összehasonlító dokumentumot](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Ez a cikk az áttelepítésének lépéseit ismerteti a Azure Media Indexer és Azure Media Indexer a 2 Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Migrálási lehetőségek

|Ha szükséges  |akkor |
|---|---|
|egy olyan megoldás, amely beszéd-szöveg átírást biztosít bármilyen médiafájl formátumhoz a kódolt képfájlok formátumában: VTT, SRT vagy TTML<br/>továbbá további hangelemzéseket, például a kulcsszavakat, a témaköröket, az akusztikus eseményeket, a hangszórók diarization, az entitások kinyerését és fordítását.| frissítse alkalmazásait az Azure Video Indexer képességeinek az Video Indexer v2 REST API vagy a Azure Media Services v3 audio Analyzer-készlettel való használatához.|
|beszéd és szöveg közötti képességek| használja közvetlenül a Cognitive Services Speech API-t.|  

## <a name="getting-started-with-video-indexer"></a>A Video Indexer első lépései

A következő szakasz a kapcsolódó hivatkozásokra mutat: Hogyan szerezhetem be a [video Indexer?](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Media Services V3 API-k – első lépések

A Azure Media Services V3 API lehetővé teszi, hogy kinyerje a videóból és hangfájlokból származó elemzéseket a [Azure Media Services v3 Analyzer-előkészletből](../latest/analyzing-video-audio-files-concept.md).

A **AudioAnalyzerPreset** lehetővé teszi több hang-elemzés kinyerését hang-vagy videofájl használatával. A kimenet tartalmaz egy VTT vagy TTML-fájlt a hangátirathoz és egy JSON-fájlhoz (az összes további hangelemzéssel). A hangelemzések közé tartoznak a kulcsszavak, a hangszórók indexelése és a beszéd hangulatának elemzése. A AudioAnalyzerPreset az egyes nyelvekhez is támogatja a nyelvfelismerés használatát. Részletes információk: [átalakítások](/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Bevezetés

Első lépésként tekintse meg a következőt:

* [Oktatóanyag](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset-minták: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) vagy [.net SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset-minták: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) vagy [.net SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Cognitive Services Speech Services – első lépések

Az [Azure Cognitive Services](../../cognitive-services/index.yml) egy beszéd-szöveges szolgáltatást biztosít, amely valós időben írja át a hangadatfolyamokat a szöveggé, hogy alkalmazásai, eszközei vagy eszközei képesek legyenek a felhasználásra vagy a megjelenítésre. [A saját akusztikai modell, a nyelvi modell vagy a kiejtési modell testre szabására](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)használhatja a beszédfelismerést a szövegre. További információ: [Cognitive Services beszéd – szöveg](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> A beszédfelismerési szolgáltatás nem készíti el a videofájl formátumait, és csak [bizonyos hangformátumokat](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats)fogad el. 

További információ a szöveg-beszéd szolgáltatásról és az első lépésekről: [Mi az a beszéd – szöveg?](../../cognitive-services/speech-service/speech-to-text.md)

## <a name="known-differences-from-deprecated-services"></a>Elavult szolgáltatások ismert eltérései

Azt tapasztalja, hogy Video Indexer, Azure Media Services v3 AudioAnalyzerPreset, és Cognitive Services a Speech Services-szolgáltatások megbízhatóbbak, és jobb minőségű kimenetet eredményeznek, mint a kivont Azure Media Indexer 1 és Azure Media Indexer 2 processzor.  

Néhány ismert különbség:

* Cognitive Services Speech Services nem támogatja a kulcsszavak kinyerését. A Video Indexer és a Media Services v3 AudioAnalyzerPreset azonban a kulcsszavakat a JSON-fájlformátumban is hatékonyabban kínálja.

## <a name="support"></a>Támogatás

A támogatási jegy megnyitásához lépjen az [új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-steps"></a>Következő lépések

* [Örökölt összetevők](legacy-components.md)
* [Díjszabás lap](https://azure.microsoft.com/pricing/details/media-services/#encoding)