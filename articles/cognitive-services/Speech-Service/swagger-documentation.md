---
title: Hencegő dokumentáció – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A kivágási dokumentáció használatával számos programozási nyelvhez automatikusan létrehozhat SDK-kat. A szolgáltatás minden műveletét a hencegés támogatja
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636268"
---
# <a name="swagger-documentation"></a>A Swagger dokumentációja

A Speech Service olyan hencegő specifikációt kínál, amely az adatok importálásához, a modellek létrehozásához, a modell pontosságához, az egyéni végpontok létrehozásához, a kötegelt átírások végrehajtásához és az előfizetések kezeléséhez használatos REST API-k használatával kommunikál. A [Speech Studio Custom Speech területén](https://aka.ms/customspeech) keresztül elérhető legtöbb művelet programozott módon is elvégezhető ezen API-k használatával.

> [!NOTE]
> A beszédfelismerési szolgáltatás több REST API-val rendelkezik a [beszédfelismerés](rest-speech-to-text.md) és a [szöveg](rest-text-to-speech.md)közötti kommunikációhoz.  
>
> A hencegés specifikációjában azonban csak a [beszéd-szöveg REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) és a v 2.0 dokumentációja látható. Tekintse meg az előző bekezdésben hivatkozott dokumentumokat az összes többi Speech Services REST API-val kapcsolatos információért.

## <a name="generating-code-from-the-swagger-specification"></a>Kód generálása a hencegés specifikációból

A [hencegés specifikációja](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) olyan lehetőségeket kínál, amelyekkel gyorsan tesztelheti a különböző útvonalakat. Előfordulhat azonban, hogy minden elérési útra vonatkozóan létre kell hoznia egy kódot, így egyetlen, a jövőbeli megoldások alapjául szolgáló hívásokat hozhat létre. Vessünk egy pillantást a Python-könyvtár létrehozási folyamatára.

Be kell állítania a felvágást a beszédfelismerési erőforrás régiójában. A régiót a beszédfelismerési erőforrás beállításainak **Áttekintés** részében ellenőrizheti Azure Portalban. A támogatott régiók teljes listája [itt](regions.md#speech-to-text)érhető el.

1. A böngészőben nyissa meg a [terület](regions.md#speech-to-text)hencegő specifikációját:  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Az oldalon kattintson az **API-definíció** elemre, majd kattintson a **hencegés** elemre. Másolja a megjelenő lap URL-címét.
1. Új böngészőben nyissa meg a következőt: [https://editor.swagger.io](https://editor.swagger.io)
1. Kattintson a **fájl** menü **URL importálása** parancsára, illessze be az URL-címet, majd kattintson **az OK** gombra.
1. Kattintson az **ügyfél előállítása** és a **Python** elemre. Az ügyféloldali kódtár egy fájlban tölti le a számítógépre `.zip` .
1. Mindent kibonthat a letöltésből. Lehet `tar -xf` , hogy mindent Kinyer.
1. Telepítse a kibontott modult a Python-környezetbe:  
      `pip install path/to/package/python-client`
1. A telepített csomag neve `swagger_client` . Győződjön meg arról, hogy a telepítés működött:  
       `python -c "import swagger_client"`

Használhatja a [githubon a Speech Service-mintákkal](https://aka.ms/csspeech/samples)generált Python-könyvtárat.

## <a name="reference-documents"></a>Dokumentáció

* [Hencegés: beszéd – szöveg REST API v 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [Diktálás REST API](rest-speech-to-text.md)
* [Szövegfelolvasás REST API](rest-text-to-speech.md)

## <a name="next-steps"></a>Következő lépések

* [Speech Service-minták a githubon](https://aka.ms/csspeech/samples).
* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)
