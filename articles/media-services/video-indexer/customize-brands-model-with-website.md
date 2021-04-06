---
title: Márkák modell testreszabása a Video Indexer webhellyel
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan szabhatja testre a márkák modelljét a Video Indexer webhelyén.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: kumud
ms.openlocfilehash: a2de9dbb479f43d6b646cd9f6cf604d6a08c8b6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586106"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Márkák modell testreszabása a Video Indexer webhellyel

A Video Indexer támogatja a beszédfelismerés és a vizualizáció szövegének felderítését a videó-és hangtartalom indexelése és újraindexelése során. A márka észlelési funkciója a Bing márkák adatbázisa által javasolt termékek, szolgáltatások és vállalatok megemlítését azonosítja. Ha például a Microsoft szerepel a videóban vagy a hangtartalomban, vagy ha a videó vizualizációs szövegben jelenik meg, akkor Video Indexer a tartalmat a tartalomban márkaként észleli.

Az egyéni márkák modell a következőket teszi lehetővé:

- Válassza ki, hogy szeretné-e Video Indexer a Bing Brands adatbázisból származó márkák észlelését.
- Válassza ki, hogy ki szeretné-e zárni a Video Indexer bizonyos márkák észlelését (lényegében megtagadja a márkák listáját).
- Ha azt szeretné, hogy a Video Indexer olyan márkákat tartalmazzon, amelyeknek a modell részét kell képezniük, amely esetleg nem a Bing márka-adatbázisában található (lényegében a márkák elfogadják a listáját).

Részletes áttekintést ebben az [áttekintésben](customize-brands-model-overview.md)talál.

A jelen témakörben leírtak szerint a Video Indexer weblapon létrehozhat, használhat és szerkeszthet egyéni márkákat tartalmazó modelleket a videóban. Használhatja az API-t is, a [Brands Model API](customize-brands-model-with-api.md)-k használatával történő testreszabása című témakörben leírtak szerint.

> [!NOTE]
> Ha a videó indexelve lett a Brand hozzáadása előtt, újra kell indexelni. A videóhoz társított legördülő menüből megtalálhatja az **újbóli indexelést** . Válassza a **Speciális beállítások**  ->  **márka kategóriák** lehetőséget, és jelölje be **az összes márka** jelölőnégyzetet.

## <a name="edit-brands-model-settings"></a>A Brands modell beállításainak szerkesztése

Lehetősége van beállítani, hogy szeretné-e a márkákat a Bing Brands adatbázisából észlelni. A beállítás megadásához szerkesztenie kell a Brands modell beállításait. Kövesse az alábbi lépéseket:

1. Lépjen a [video Indexer](https://www.videoindexer.ai/) webhelyére, és jelentkezzen be.
1. Ha testre szeretné szabni a fiókban lévő modelleket, válassza ki a **tartalom modell testreszabása** gombot a lap bal oldalán.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="A tartalmi modell testreszabása Video Indexer":::
1. A márkák szerkesztéséhez válassza a **márkák** lapot.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-brand-model/customize-brand-model.png" alt-text="Képernyőfelvétel a tartalmi modell testreszabása párbeszédpanel márkák lapját jeleníti meg":::
1. Tekintse meg a **Bing által javasolt márkák megjelenítése** lehetőséget, ha azt szeretné, video Indexer a Bing által javasolt márkák észlelése – ha nem, akkor hagyja meg a jelölőnégyzet jelölését.

## <a name="include-brands-in-the-model"></a>Márkák belefoglalása a modellbe

A **márkák belefoglalása** szakasz azokat az egyéni márkákat tartalmazza, amelyeket a rendszer az észleléshez video Indexer, még akkor is, ha azokat a Bing nem javasolja.  

### <a name="add-a-brand-to-include-list"></a>Lista hozzáadására szolgáló márka hozzáadása

1. Válassza az **+ új márka létrehozása** lehetőséget.

    Adja meg a nevet (kötelező), a kategóriát (nem kötelező), a leírást (nem kötelező) és a hivatkozási URL-címet (opcionális).
    A Kategória mező célja, hogy segítsen a márkák címkézésében. Ez a mező a video Indexer API-k használatakor a márka *címkéi* jelenik meg. Például az "Azure" márka címkézhető vagy kategorizálható "Cloud"-ként.

    A hivatkozás URL-címe mező a márka bármely hivatkozási webhelye lehet (például a wikipedia oldalára mutató hivatkozás).

2. Válassza a **Mentés** lehetőséget, és látni fogja, hogy a márka hozzá lett adva a **include Brands** listához.

### <a name="edit-a-brand-on-the-include-list"></a>Márka szerkesztése a belefoglalási listán

1. Válassza ki a szerkeszteni kívánt márka melletti ceruza ikont.

    A márka kategóriájának, leírásának vagy hivatkozási URL-címének frissítését végezheti el. A márka neve nem módosítható, mert a márkák nevei egyediek. Ha módosítania kell a márka nevét, törölje a teljes márkát (lásd a következő szakaszt), és hozzon létre egy új márkát az új névvel.

2. Kattintson a **frissítés** gombra a Brand új információval való frissítéséhez.

### <a name="delete-a-brand-on-the-include-list"></a>Márka törlése a belefoglalási listán

1. Válassza ki a törölni kívánt márka melletti Kuka ikont.
2. Válassza a **Törlés** lehetőséget, és a márka többé nem jelenik meg a *belefoglalási márkák* listájában.

## <a name="exclude-brands-from-the-model"></a>Márkák kizárása a modellből

A **márkák kizárása** szakasz azokat a márkákat jelenti, amelyeket nem kíván felderíteni video Indexer.

### <a name="add-a-brand-to-exclude-list"></a>Lista hozzáadása a kizárni kívánt márkához

1. Válassza az **+ új márka létrehozása lehetőséget.**

    Adjon meg egy nevet (kötelező), kategória (nem kötelező).

2. Válassza a **Mentés** lehetőséget, és látni fogja, hogy a márka hozzá lett adva a *kizárási márkák* listájához.

### <a name="edit-a-brand-on-the-exclude-list"></a>Márka szerkesztése a kizárási listán

1. Válassza ki a szerkeszteni kívánt márka melletti ceruza ikont.

    A márka kategóriáját csak frissíteni lehet. A márka neve nem módosítható, mert a márkák nevei egyediek. Ha módosítania kell a márka nevét, törölje a teljes márkát (lásd a következő szakaszt), és hozzon létre egy új márkát az új névvel.

2. Kattintson a **frissítés** gombra a Brand új információval való frissítéséhez.

### <a name="delete-a-brand-on-the-exclude-list"></a>Márka törlése a kizárási listán

1. Válassza ki a törölni kívánt márka melletti Kuka ikont.
2. Válassza a **Törlés** lehetőséget, a márka pedig többé nem jelenik meg a *kizárási márkák* listájában.

## <a name="next-steps"></a>Következő lépések

[A Brands modell testreszabása API-k használatával](customize-brands-model-with-api.md)
