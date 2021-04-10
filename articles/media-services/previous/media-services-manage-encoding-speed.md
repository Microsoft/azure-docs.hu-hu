---
title: A kódolás sebességének és egyidejűségének kezelése a Azure Media Servicessal | Microsoft Docs
description: Ez a cikk rövid áttekintést nyújt arról, hogyan kezelheti a kódolási feladatok/feladatok sebességét és egyidejűségét Azure Media Services használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f01235e5b02bf2a61a359fa5571c6e6d575c1232
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014808"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>A kódolás sebességének és egyidejűségének kezelése

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Ez a cikk rövid áttekintést nyújt a kódolási feladatok/feladatok sebességének és egyidejűségének kezeléséről.

## <a name="overview"></a>Áttekintés

Media Services a **fenntartott egység típusa** határozza meg, hogy milyen sebességgel dolgozza fel a rendszer a média feldolgozási feladatait. A következő fenntartott egység típusok közül választhat: **S1**, **S2** vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett. A [skálázási kódolási egységek](media-services-scale-media-processing-overview.md) témakör egy olyan táblázatot mutat be, amely segít a különböző kódolási sebességek közötti választásban.

A fenntartott egység típusának meghatározása mellett megadhatja, hogy a fiók a **fenntartott egységekkel** legyen kiépítve. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. Ha például a fiókja öt fenntartott egységgel rendelkezik, akkor az öt adathordozó-feladat egyszerre fut, amíg a feladatok feldolgozására sor kerül. A hátralévő feladatok megvárhatják a várólistán, és a folyamatban lévő tevékenységek befejezését követően is felveszik a feldolgozást. Ha egy fiókhoz nincs kiépítve fenntartott egység, a rendszer szekvenciálisan felveszi a tevékenységeket. Ebben az esetben a várakozási idő egy feladat befejezése és a következő kezdés a rendszer erőforrásainak rendelkezésre állása alapján fog függeni.

A kódolási egységek méretezését bemutató részletes információkat és példákat [a következő témakörben talál:](media-services-scale-media-processing-overview.md) .

## <a name="next-step"></a>Következő lépés

[Kódolási egységek méretezése](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

