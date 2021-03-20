---
title: Élő stream-elemzés Video Indexer használatával
titleSuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan végezhető élő stream-elemzés a Video Indexer használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74185997"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Élő stream-elemzés Video Indexer

A Azure Media Services Video Indexer egy Azure-szolgáltatás, amely a videó-és hangfájlok kapcsolat nélküli kinyerésére szolgál. Ez egy korábban már létrehozott adott médiafájl elemzése. Bizonyos felhasználási esetekben azonban fontos, hogy az élő hírcsatornából származó adathordozó-megállapítások minél gyorsabbak legyenek az operatív és egyéb használati esetek időben történő megnyomásával. Az élő streamek ilyen gazdag metaadatait például a tartalmi gyártók használhatják a TV-termelés automatizálására.

A cikkben ismertetett megoldás lehetővé teszi, hogy az ügyfelek az élő hírcsatornák közel valós idejű határozataiban Video Indexer használják. Az indexelés késése akár négy perc is lehet a megoldás használatával, az indexelt adattömböktől, a bemeneti felbontástól, a tartalom típusától és a folyamathoz használt számítási feladatoktól függően.

![Az élő stream Video Indexer metaadatai](./media/live-stream-analysis/live-stream-analysis01.png)

*1. ábra – a Video Indexer metaadatokat megjelenítő minta lejátszó az élő streamen*

A [stream Analysis Solution megoldás](https://aka.ms/livestreamanalysis) a Azure functions és két Logic Apps használatával dolgozza fel az élő programot egy élő csatornáról a video Indexer Azure Media Services, és megjeleníti az eredményt a közel valós idejű, eredményül kapott adatfolyamot ábrázoló Azure Media Player.

Magas szinten a két fő lépésből áll. Az első lépés 60 másodpercen belül fut, és az utolsó 60 másodpercből álló alklipet hoz létre, amely létrehoz egy objektumot, és indexeli azt Video Indexer használatával. Ezt követően a második lépést az indexelés befejezése után hívja meg a rendszer. A rendszer feldolgozza a rögzített észleléseket, elküldje Azure Cosmos DB, és az indexelt alklipet is törli.

A minta lejátszó az élő streamet játssza le, és beolvassa a Azure Cosmos DB, egy dedikált Azure-függvény használatával. Megjeleníti a metaadatokat és a miniatűröket az élő videóval szinkronizálva.

![Az élő streamet a felhőben percenként feldolgozó két logikai alkalmazás](./media/live-stream-analysis/live-stream-analysis02.png)

*2. ábra – a két logikai alkalmazás, amely az élő streamet dolgozza fel percenként a felhőben.*

## <a name="step-by-step-guide"></a>Részletes útmutató 

Az eredmények üzembe helyezéséhez szükséges teljes kód és lépésenkénti útmutató a [GitHub Project for Live Media Analytics](https://aka.ms/livestreamanalysis)szolgáltatásban video Indexer használatával érhető el. 

## <a name="next-steps"></a>Következő lépések

[A Video Indexer áttekintése](video-indexer-overview.md)
