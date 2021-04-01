---
title: Beszéd – szöveg áttekintése – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszéd – szöveges szoftverek lehetővé teszik a hangos adatfolyamok valós idejű átírását szöveggé. Alkalmazásait, eszközeit és eszközeit felhasználhatják, megjeleníthetik, és műveleteket végezhetnek ezen a szövegbevitelen. Ez a cikk áttekintést nyújt a beszédfelismerési szolgáltatás előnyeiről és képességeiről.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: beszéd – szöveg, beszéd – szöveges szoftver
ms.openlocfilehash: 5e593202a8cd68dfc4a92f991d573b4b06051b36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434312"
---
# <a name="what-is-speech-to-text"></a>Mi az a diktálás?

Ebben az áttekintésben megismerheti a beszédfelismerési szolgáltatás előnyeit és képességeit.
A beszédfelismerést, más néven beszédfelismerést is lehetővé teszi a hangadatfolyamok valós idejű átírását szöveggé. Alkalmazásai, eszközei vagy eszközei felhasználhatják, megjeleníthetik és megtehetik ezt a szöveget a parancs bemenetként. Ezt a szolgáltatást a Microsoft a Cortana és az Office-termékekhez használt felismerési technológiával látja el. Zökkenőmentesen együttműködik a <a href="./speech-translation.md" target="_blank">fordítási </a> és <a href="./text-to-speech.md" target="_blank">szöveg-beszéd </a> szolgáltatási ajánlatokkal. Az elérhető beszéd – szöveg nyelvek teljes listáját a [támogatott nyelvek](language-support.md#speech-to-text)című részben tekintheti meg.

A beszédfelismerési szolgáltatás alapértelmezés szerint az univerzális nyelvi modellt használja. Ez a modell a Microsoft tulajdonában lévő adataival lett betanítva, és a felhőben van üzembe helyezve. Ez optimális a társalgási és a diktálási forgatókönyvek esetében. A beszédfelismerés és az átiratok egyedi környezetben való használatakor egyéni akusztikai, nyelvi és kiejtési modelleket hozhat létre és állíthat be. A Testreszabás hasznos lehet a környezeti zaj vagy az iparágra jellemző szókincs kezelése során.

A szövegbeviteli és a beszéd-szöveges szolgáltatás további hivatkozásokkal is lehetővé teszi a [kiejtés értékelését](rest-speech-to-text.md#pronunciation-assessment-parameters) , hogy kiértékelje a beszéd kiejtését, és visszajelzéseket küldjön a beszélők pontosságáról és a beszélt hangról. A kiejtés értékelésével a nyelvi tanulók felhasználhatják az azonnali visszajelzéseket, és javíthatják a kiejtését, hogy biztosan beszéljenek és a helyükön legyenek. A pedagógusok képesek a több beszélő kiejtésének valós idejű kiértékelésére. A szolgáltatás jelenleg az USA angol nyelvét támogatja, és a szakértők által készített beszédfelismerési felmérésekkel szorosan összefügg.

> [!NOTE]
> A Bing Speech 2019. október 15-én lett leszerelve. Ha alkalmazásai, eszközei vagy termékei a Bing Speech API-kat használják, a beszédfelismerési szolgáltatásba való Migrálás megkönnyítéséhez útmutatókat hoztunk létre.
> - [Áttelepítés Bing Speechról a beszédfelismerési szolgáltatásba](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>Bevezetés

Tekintse [meg a gyors](get-started-speech-to-text.md) üzembe helyezési útmutatót. A szolgáltatás a [SPEECH SDK](speech-sdk.md), a [REST API](rest-speech-to-text.md#pronunciation-assessment-parameters)és a [Speech CLI](spx-overview.md)használatával érhető el.

## <a name="sample-code"></a>Mintakód

A Speech SDK mintakód a GitHubon érhető el. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata.

- [Beszéd – szöveg minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Kiejtés-értékelési minták (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Testreszabás

A standard Speech Service-modellen kívül egyéni modelleket is létrehozhat. A testreszabással leküzdheti a beszédfelismerési akadályokat, például a beszéd stílusát, a szókincset és a háttérzajt, lásd: [Custom Speech](./custom-speech-overview.md). A testreszabási lehetőségek nyelv/területi beállítás szerint változnak, a támogatás ellenőrzéséhez lásd a [támogatott nyelveket](./language-support.md) .

## <a name="batch-transcription"></a>Kötegelt átírás

A Batch átírása REST API művelet, amely lehetővé teszi nagy mennyiségű hang tárolását. A hangfájlokra egy közös hozzáférésű jogosultságkód (SAS) URI-jával mutathat rá, és aszinkron módon megkaphatja az átírás eredményét. További információ a Batch-átírási API használatáról: [útmutató](batch-transcription.md) .

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Következő lépések

- [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)
- [A Speech SDK beszerzése](speech-sdk.md)