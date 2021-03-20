---
title: Pontos pillanatok keresése a videókban Video Indexer
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan keresheti meg a videók pontos pillanatait Video Indexer használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96030534"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Pontos pillanatok keresése a videókban Video Indexer

Ebből a témakörből megtudhatja, hogyan használhatja a Video Indexer webhelyét a videók pontos pillanatainak megkereséséhez.

1. Lépjen a [video Indexer](https://www.videoindexer.ai/) webhelyére, és jelentkezzen be.
1. Adja meg a keresési kulcsszavakat, és a keresés a fiók könyvtára összes videója között elvégezhető. 

    A keresést a **szűrők** lehetőség kiválasztásával szűrheti. Az alábbi példában a "Microsoft" kifejezésre keresünk, amely csak képernyős szövegként jelenik meg (OCR).

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Szűrés, csak szöveg":::
1. Kattintson a **Keresés** gombra az eredmény megtekintéséhez.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Videó keresési eredménye":::

    Ha a találatok egyikét választja, a lejátszó a videóban megadott időpontra viszi.
1. Tekintse meg és keresse meg a videó összesített eredményeit a videó **Lejátszás** gombjára kattintva, vagy válasszon egyet az eredeti keresési eredmények közül. 

    Megtekintheti, megkeresheti és **szerkesztheti az eredményeket**. Ha kijelöli az egyik bepillantást, a lejátszó a videóban a pontos pillanatot adja.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="A videó tartalmának megtekintése, keresése és szerkesztése":::

    Ha Video Indexer widgeteket ágyaz be a videóba, az alkalmazásban elérheti a lejátszó/betekintési nézetet és a szinkronizálást. További információ: [video Indexer widgetek beágyazása az alkalmazásba](video-indexer-embed-widgets.md).
1. Megtekintheti, megkeresheti és szerkesztheti a átiratokat, ha az **Idősor** lapra kattint. 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="A videó átiratait megtekintheti, megkeresheti és szerkesztheti":::

    A szöveg szerkesztéséhez válassza a **Szerkesztés** lehetőséget a jobb felső sarokban, és szükség szerint módosítsa a szöveget. 

    Az átiratokat a jobb felső sarokban található megfelelő lehetőség kiválasztásával is lefordíthatja és letöltheti. 

## <a name="embed-download-create-projects"></a>Beágyazás, letöltés, projektek létrehozása

A videó beágyazásához **</>beágyazás** lehetőség kiválasztásával teheti meg a videót. Részletekért lásd: [Visual widgetek beágyazása az alkalmazásba](video-indexer-embed-widgets.md).

A videóban a **Letöltés** lehetőségre kattintva letöltheti a videó forrásait, bepillantást nyerhet.

A **Megnyitás a szerkesztőben** lehetőségre kattintva létrehozhat egy klipet az adott vonalak és pillanatok videója alapján. Ezután szerkessze a videót, és mentse a projektet. Részletekért lásd: [a videók](use-editor-create-project.md)részletes információinak használata.

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Videó beágyazása, letöltése, projektek létrehozása":::

## <a name="next-steps"></a>Következő lépések

[Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
