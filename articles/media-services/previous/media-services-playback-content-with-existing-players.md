---
title: Meglévő játékosok használata a tartalom lejátszásához – Azure | Microsoft Docs
description: Ez a cikk felsorolja azokat a meglévő játékosokat, amelyeket a tartalom lejátszásához használhat.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ce773adace1baf6db8f1b747643ef0ffdc56a97c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008282"
---
# <a name="playing-your-content-with-existing-players"></a>A tartalom lejátszása meglévő lejátszókkal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A Azure Media Services számos népszerű streaming formátumot támogat, például a Smooth Streaming, a HTTP Live Streaming és az MPEG-Dash formátumot. Ez a témakör egy meglévő, a streamek tesztelésére használható játékost mutat be.

## <a name="the-azure-portal-media-services-content-player"></a>A tartalom lejátszó Azure Portal Media Services

Az **Azure** Portal egy tartalommal rendelkező lejátszót biztosít, amellyel tesztelheti a videót.

Kattintson a kívánt videóra (Győződjön meg róla, hogy [közzé lett téve](media-services-portal-publish.md)), majd kattintson a portál alján található **Lejátszás** gombra.

Vegye figyelembe a következőket:

* A **MEDIA SERVICES CONTENT PLAYER** (Media Services tartalomlejátszó) az alapértelmezett streamvégpontból játssza le a fájlokat. Ha egy nem alapértelmezett streamvégpontból szeretne lejátszani valamit, használjon másik lejátszót. Például [Azure Media Player](https://aka.ms/azuremediaplayer).

### <a name="azure-media-player"></a>Azure Media Player

A következő formátumok bármelyikében használhatja a [Azure Media Player](https://aka.ms/azuremediaplayer) a tartalom (tiszta vagy védett) lejátszásához:

* Smooth Streaming
* MPEG DASH
* HLS
* Progresszív MP4

### <a name="flash-player"></a>Flash-lejátszó

#### <a name="playready-with-token"></a>PlayReady Tokenrel

[http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html](http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html)

### <a name="dash-players"></a>DASH-lejátszók

[Dash-lejátszó](http://players.akamai.com/players/dashjs)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Egyéb

A HLS URL-címek teszteléséhez használhatja a következőket is:

* **Safari** iOS-eszközön vagy
* **3IVX HLS-lejátszó** Windows rendszeren.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
