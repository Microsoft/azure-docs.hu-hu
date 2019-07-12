---
title: A lejátszás az Azure Media Player – Azure |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt az Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/10/2018
ms.author: juliako
ms.openlocfilehash: c599bc2bba27a3b3603d8d67e2c6049dc2a8b08b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840162"
---
# <a name="azure-media-player-overview"></a>Az Azure Media Player áttekintése

Az Azure Media Player egy webes videólejátszó médiatartalmak lejátszására a Microsoft Azure Media Services számos különböző böngészők és eszközök is. Az Azure Media Player iparági szabványok – például HTML5, Media forrás Extensions (MSE) és az Encrypted Media Extensions (EME) adjon meg egy streamelési megoldást használja. Ha ezek a szabványok nem érhetők el, az eszközön vagy böngészőben, az Azure Media Player használja Flashre és Silverlightra tartalék technológia. A fejlesztők a lejátszási technológiától függetlenül egy egységes JavaScript-felület API-k elérése kell. Ez lehetővé teszi az Azure Media Services által kiszolgált tartalom wide-számos eszköz és bármely extra erőfeszítést böngészőkben lejátszhatók legyenek.

A Microsoft Azure Media Services lehetővé teszi, hogy a tartalom kiszolgálása HLS, DASH, Smooth Streaming formátumban vissza tartalom lejátszása. Az Azure Media Player a különféle formátumokat figyelembe veszi, és automatikusan játszik a legjobb hivatkozás, a platformjától és böngészői képességeitől alapján. A Media Services emellett lehetővé teszi, hogy a dinamikus titkosítást, PlayReady-titkosítást az eszközök, vagy a 128 bites AES-boríték-titkosítást. Az Azure Media Player lehetővé teszi a PlayReady-titkosítás visszafejtésére és 128 bites AES-titkosított tartalmat, ha megfelelően konfigurálva. 

[Az ingyenes próbaverzió használatának megkezdéséhez](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Az Azure Media Player demó lapon

### <a name="start-using"></a>Használatának megkezdése

Használhatja a [Azure Media Player demó lap](http://aka.ms/azuremediaplayer) lejátszása az Azure Media Services-minták vagy a saját stream.  

Új videó lejátszása, illessze be egy másik URL-CÍMÉT, és nyomja meg **frissítés**.

Különböző lejátszási beállítások (például technológia, nyelv vagy a titkosításhoz) konfigurálását **speciális beállítások**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>A video-adatfolyamot diagnosztika figyelése

Használhatja a [Azure Media Player demó lap](http://aka.ms/azuremediaplayer) , video-adatfolyamot diagnosztika figyelése. 

![Az Azure Media Player diagnostics](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Állítsa be az Azure Media Player a HTML-ben

Állítsa be az Azure Media Player ördöngösség. Csak megkapni a alapszintű médiatartalom lejátszása a Media Services-fiók néhány percet vesz igénybe. Lásd: [dokumentáció az Azure Media Player](https://aka.ms/ampdocs) való telepítése és konfigurálása az Azure Media Player számára. 

## <a name="next-steps"></a>További lépések

- [Az Azure Media Player dokumentációja](https://aka.ms/ampdocs)
- [Azure Media Player-minták](https://aka.ms/ampsamples)
