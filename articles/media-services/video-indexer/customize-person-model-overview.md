---
title: Személy modell testreszabása Video Indexerban – Azure
titleSuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt arról, hogy mi a Video Indexer személy modellje, és hogyan szabható testre.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 613ad2d0ab90bef016e80a33be12b71cba5e2f71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87047052"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Személy modell testreszabása Video Indexerban

Video Indexer támogatja a híresség-felismerést a videókban. A híresség-felismerési funkció a gyakran igényelt adatforrások (például a IMDB, a wikipedia és a legnépszerűbb LinkedIn-befolyásoló) alapján körülbelül 1 000 000 arcot ölel fel. A Video Indexer által fel nem ismert arcok továbbra is észlelhetők, de nem lesznek megnevezve. Az ügyfelek egyéni modelleket építhetnek ki, és lehetővé teszik, hogy a Video Indexer olyan arcokat ismerjen fel, amelyeket nem ismer fel alapértelmezés szerint. Az ügyfelek létrehozhatják ezeket a modelleket úgy, hogy a személy nevét a személy arcához tartozó képfájlokkal párosítják.  

Ha a fiókja különböző használati esetekre is vonatkozik, akkor használhatja a több személyből származó modellek fiókkal történő létrehozását. Ha például a fiókjában lévő tartalom különböző csatornákra van rendezve, érdemes lehet külön személy modellt létrehozni minden csatornához. 

> [!NOTE]
> Minden egyes személy legfeljebb 1 000 000 személyt támogat, és minden fióknak 50-os maximális modellje van. 

A modell létrehozása után használhatja azt egy adott személy modell AZONOSÍTÓjának megadásával, amikor feltölti/indexeli vagy újraindexeli a videót. Új arc betanítása egy videóra, frissíti azt a konkrét egyéni modellt, amelyhez a videó társítva lett. 

Ha nincs szüksége a több személyre vonatkozó modell támogatására, ne rendeljen hozzá egy személy modell-azonosítót a videóhoz feltöltés/indexelés vagy újraindexelés során. Ebben az esetben a Video Indexer az alapértelmezett person modellt fogja használni a fiókjában. 

A Video Indexer webhelyről szerkesztheti a videóban észlelt arcokat, és több egyéni személy modelljét is kezelheti a fiókjában, a [személy modell testreszabása webhely használatával](customize-person-model-with-website.md) című témakörben leírtak szerint. Használhatja az API-t is a következő témakörben ismertetett módon: [személyre szabott modell testreszabása API](customize-person-model-with-api.md)-k használatával.
