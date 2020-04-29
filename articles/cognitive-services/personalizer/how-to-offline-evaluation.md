---
title: Offline próbaverzió végrehajtása – személyre szabás
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan használható az offline értékelés az alkalmazás hatékonyságának méréséhez és a tanulási hurok elemzéséhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77622790"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>A tanulási hurok elemzése offline kiértékeléssel

Ismerje meg, hogyan végezheti el az offline kiértékelést, és megismerheti az eredményeket.

Az offline értékelések segítségével mérhetővé válik, hogy az alkalmazás alapértelmezett viselkedéséhez képest mennyire hatékony a személyre szabott funkció, hogy milyen funkciók járulnak hozzá leginkább a személyre szabáshoz, és automatikusan Fedezze fel az új gépi tanulási értékeket.

További információért olvassa el a [kapcsolat nélküli értékeléseket](concepts-offline-evaluation.md) ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

* Konfigurált személyre szabott hurok
* A személyre szabott huroknak reprezentatív mennyiségű adatnak kell lennie, és a naplókban legalább 50 000 eseményt javasolt kiértékelési eredményekre. Előfordulhat, hogy korábban már exportálta a _tanulási szabályzatok_ fájljait, amelyeket összevetheti és tesztelheti ugyanabban az értékelésben.

## <a name="run-an-offline-evaluation"></a>Offline kiértékelés futtatása

1. A [Azure Portal](https://azure.microsoft.com/free/)keresse meg a személyre szabott erőforrást.
1. A Azure Portal lépjen az **értékelések** szakaszra, és válassza az **Értékelés létrehozása**elemet.
    ![A Azure Portal nyissa meg a * * értékelések * * szakaszt, és válassza a * * Create reértékelés * * elemet.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Konfigurálja a következő értékeket:

    * Egy kiértékelés neve.
    * Kezdési és befejezési dátum – ezek a dátumok határozzák meg a kiértékelésben használandó adatok tartományát. Ezeknek az adatoknak szerepelniük kell a naplókban az [adatmegőrzési](how-to-settings.md) értékben megadott módon.
    * Az optimalizálási felderítés az **Igen**értékre van állítva.

    > [!div class="mx-imgBorder"]
    > ![Offline kiértékelési beállítások kiválasztása](./media/offline-evaluation/create-an-evaluation-form.png)

1. A kiértékelés elindításához kattintson **az OK gombra**.

## <a name="review-the-evaluation-results"></a>A kiértékelés eredményeinek áttekintése

Az értékelések hosszú időt vehetnek igénybe a feldolgozandó adatmennyiségtől, az összehasonlítani kívánt tanulási szabályzatok számától és a kért optimalizálástól függően.

Ha elkészült, kiválaszthatja az értékelést az értékelések listájából, majd kiválaszthatja **az alkalmazás pontszámának összehasonlítását más lehetséges tanulási beállításokkal**. Akkor válassza ezt a funkciót, ha meg szeretné tekinteni, hogyan végzi el a jelenlegi tanulási szabályzatot az új szabályzathoz képest.

1. Tekintse át a [tanulási szabályzatok](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)teljesítményét.

    > [!div class="mx-imgBorder"]
    > [![Értékelés eredményeinek áttekintése](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Az **alkalmaz** gombra kattintva alkalmazza a szabályzatot, amely fejleszti az Ön adatai számára legmegfelelőbb modellt.

## <a name="next-steps"></a>További lépések

* További információ az [Offline értékelések működéséről](concepts-offline-evaluation.md).
