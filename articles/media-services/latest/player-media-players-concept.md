---
title: Media Services Media Players – áttekintés
description: Mely multimédia-lejátszók használhatják a Azure Media Services? Azure Media Player, a és a Video.js eddig.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 43d0a8ee82a84a57be7c8ded9e7f5afc172bd9d6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282405"
---
# <a name="media-players-for-media-services"></a>Media Services médialejátszók

Media Services használatával több multimédia-lejátszót is használhat.

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player egy videolejátszó a böngészők és az eszközök széles köréhez. A Azure Media Player az iparági szabványokat, például a HTML5-t, a Media Source Extensions (MSE) és a titkosított adathordozó-bővítményeket (EME) használja a dúsított Adaptív átviteli élmény biztosításához. Ha ezek a szabványok nem érhetők el egy eszközön vagy böngészőben, Azure Media Player a Flash és a Silverlight protokollt használja tartalék technológiaként. A fejlesztők az API-k elérésére szolgáló egységes JavaScript-felülettel rendelkeznek a használt lejátszási technológiától függetlenül. A Azure Media Services által kiszolgált tartalom számos eszközön és böngészőben is játszható, külön erőfeszítés nélkül.

Tekintse meg a [Azure Media Player dokumentációját](../azure-media-player/azure-media-player-overview.md).

## <a name="shaka"></a>Shaka

A videotár-lejátszó egy nyílt forráskódú JavaScript-kódtár az adaptív adathordozók számára. Az adaptív adathordozó-formátumokat (például KÖTŐJELet és HLS) egy böngészőben, beépülő modulok vagy flash használata nélkül játssza le. Ehelyett a következő a nyílt webes szabványok Media Source Extensions and encrypted Media Extensions használatával működik.

Tekintse [meg, hogyan használhatja a Azure Media Services-vel rendelkező, a-t](player-shaka-player-how-to.md).

## <a name="videojs"></a>Video.js

Video.js egy olyan videolejátszó, amely adaptív adathordozó-formátumokat (például DASH és HLS) játszik egy böngészőben. Video.js az Open Web Standards MediaSource Extensions and encrypted Media Extensions szolgáltatást használja. Támogatja a videolejátszás asztali számítógépeken és mobileszközökön. A hivatalos dokumentációja a következő címen érhető el: https://docs.videojs.com/ .

Tekintse [meg, hogyan használhatja a Video.js lejátszót a Azure Media Services](player-how-to-video-js-player.md).


## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](../azure-media-player/azure-media-player-quickstart.md)