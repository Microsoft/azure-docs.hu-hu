---
title: Azure Media Player áttekintése
description: További információ a Azure Media Playerról.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 9b99e24e47da69aa0f081e056230f4b0c1aa6bb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93321734"
---
# <a name="azure-media-player-overview"></a>Az Azure Media Player áttekintése #

A Azure Media Player egy webes videolejátszó, amely a különböző böngészők és eszközök széles köréről származó médiatartalom lejátszását [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) . A Azure Media Player az iparági szabványokat, például a HTML5-t, a Media Source Extensions (MSE) és a titkosított adathordozó-bővítményeket (EME) használja a dúsított Adaptív átviteli élmény biztosításához.  Ha ezek a szabványok nem érhetők el egy eszközön vagy böngészőben, Azure Media Player a Flash és a Silverlight protokollt használja tartalék technológiaként. A használt lejátszási technológiától függetlenül a fejlesztőknek egységes JavaScript-felülettel kell rendelkezniük az API-k eléréséhez, így a Azure Media Services által kiszolgált tartalom számos eszközön és böngészőben játszható le anélkül, hogy további erőfeszítésre lenne szükség.

Microsoft Azure Media Services lehetővé teszi, hogy a tartalom a KÖTŐJELtel, a Smooth Streaming és a HLS adatfolyam-formátumokkal legyen kézbesítve a tartalmak lejátszásához. Azure Media Player a különböző formátumokat veszi figyelembe, és a platform/böngésző képességei alapján automatikusan a legjobb hivatkozást játssza le. A Microsoft Azure Media Services a közös titkosítással (PlayReady vagy Widevine) vagy AES-128 bites boríték-titkosítással rendelkező eszközök dinamikus titkosítását is biztosítja. A Azure Media Player lehetővé teszi a PlayReady és az AES-128 bites titkosított tartalom visszafejtését, ha az megfelelően van konfigurálva.  A lejátszó konfigurálásának megismeréséhez tekintse meg a [védett tartalom](azure-media-player-protected-content.md) szakaszt.

Új funkciók igényléséhez adjon meg ötleteket vagy visszajelzést, küldje el azokat a [Azure Media Player UserVoice](https://aka.ms/ampuservoice). Ha rendelkezik és adott problémákkal, kérdésekkel vagy hibákat talál, dobjon minket egy sorba ampinfo@microsoft.com .

> [!NOTE]
> Vegye figyelembe, hogy a Azure Media Player csak a Azure Media Services-adatfolyamokat támogatja.

## <a name="license"></a>Licenc ##

A Azure Media Player licenccel rendelkezik, és a Azure Media Player Microsoft szoftverlicenc-feltételeiben ismertetett feltételekre vonatkozik. Tekintse meg a teljes használati feltételeket [tartalmazó licencfeltételeket](/legal/azure-media-player/azure-media-player-license) . További információkért tekintse meg az [adatvédelmi nyilatkozatot](https://www.microsoft.com/en-us/privacystatement/default.aspx).

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)
