---
title: Lejátszás Azure Media Player-Azure-val
description: A Azure Media Player egy webvideós lejátszó, amely a Microsoft Azure Media Services különböző böngészők és eszközök által készített médiatartalom lejátszására készült.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: inhenkel
ms.openlocfilehash: cf4916341a97868de757804b570212f1cc1105b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898121"
---
# <a name="playback-with-azure-media-player"></a>Lejátszás Azure Media Player

A Azure Media Player egy webvideós lejátszó, amely a Microsoft Azure Media Services különböző böngészők és eszközök által készített médiatartalom lejátszására készült. A Azure Media Player az iparági szabványokat, például a HTML5-t, a Media Source Extensions (MSE) és a titkosított adathordozó-bővítményeket (EME) használja a dúsított Adaptív átviteli élmény biztosításához. Ha ezek a szabványok nem érhetők el egy eszközön vagy böngészőben, Azure Media Player a Flash és a Silverlight protokollt használja tartalék technológiaként. A használt lejátszási technológiától függetlenül a fejlesztőknek egységes JavaScript-felülettel kell rendelkezniük az API-k eléréséhez. Ez lehetővé teszi, hogy a Azure Media Services által kiszolgált tartalmak a további erőfeszítések nélkül, széles körű eszközökön és böngészőkön keresztül legyenek játszhatók.

Microsoft Azure Media Services lehetővé teszi, hogy a tartalmak a HLS, a DASH, a Smooth Streaming streaming formátumban legyenek kézbesítve a tartalmak lejátszásához. Azure Media Player a különböző formátumokat veszi figyelembe, és a platform/böngésző képességei alapján automatikusan a legjobb hivatkozást játssza le. A Media Services lehetővé teszi az eszközök dinamikus titkosítását PlayReady titkosítással vagy AES-128 bites boríték-titkosítással. A Azure Media Player lehetővé teszi a PlayReady és az AES-128 bites titkosított tartalom visszafejtését, ha az megfelelően van konfigurálva.

> [!NOTE]
> A titkosított tartalom Widevine HTTPS-lejátszás szükséges.

## <a name="use-azure-media-player-demo-page"></a>Azure Media Player bemutató oldal használata

### <a name="start-using"></a>Használat megkezdése

A [Azure Media Player bemutató oldalon](https://aka.ms/azuremediaplayer) Azure Media Services mintákat vagy a saját streamjét is használhatja.  

Új videó lejátszásához illesszen be egy másik URL-címet, és kattintson a **frissítés** gombra.

A különböző lejátszási beállítások (például a technológia, a nyelv vagy a titkosítás) konfigurálásához nyomja meg a **Speciális beállítások lehetőséget**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Videó stream diagnosztika monitorozása

A videó streamek diagnosztizálásának figyeléséhez használhatja a [Azure Media Player bemutató oldalt](https://aka.ms/azuremediaplayer) .

![Azure Media Player diagnosztika](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Azure Media Player beállítása a HTML-ben

A Azure Media Player egyszerűen beállítható. Csak néhány percet vesz igénybe, hogy lekérje a médiatartalom alapszintű lejátszását a Media Services-fiókjából. A Azure Media Player beállításával és konfigurálásával kapcsolatos részletekért tekintse meg [Azure Media Player dokumentációját](../azure-media-player/azure-media-player-overview.md) .

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Media Player dokumentációja](../azure-media-player/azure-media-player-overview.md)
* [Azure Media Player minták](https://github.com/Azure-Samples/azure-media-player-samples)