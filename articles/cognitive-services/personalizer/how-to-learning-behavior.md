---
title: Tanulási viselkedés konfigurálása
description: A gyakornoki mód megbízhatóságot biztosít a személyre szabott szolgáltatásban és a gépi tanulási képességeiben, és olyan mérőszámokat biztosít, amelyekkel a szolgáltatás az online forgalom kockáztatása nélkül is elküldhető.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 57a03b107678f83200b11f408784f6455cbceffd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94579291"
---
# <a name="configure-the-personalizer-learning-behavior"></a>A személyre szabott tanulási viselkedés konfigurálása

A [gyakornoki mód](concept-apprentice-mode.md) megbízhatóságot és megbízhatóságot biztosít a személyre szabott szolgáltatásban és a gépi tanulási képességeiben, és gondoskodik arról, hogy a szolgáltatás olyan információt küldjön, amely az online forgalom kockáztatása nélkül is elsajátítható.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Gyakornoki mód konfigurálása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com)a személyre szabott erőforráshoz.

1. A **konfiguráció** oldalon, a **tanulási viselkedés** lapon válassza az alapértékek **visszaküldése műveletet, majd a tanulók** , majd a **Mentés** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Képernyőkép a Apprentice mód tanulási viselkedésének konfigurálásáról Azure Portal](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>A meglévő alkalmazás módosításai

A meglévő alkalmazásnak nem kell megváltoztatnia, hogy jelenleg hogyan válassza ki a megjeleníteni kívánt **műveleteket, vagy** hogy az alkalmazás hogyan határozza meg a művelet értékét. Előfordulhat, hogy az alkalmazás csak a személyre szabási API-nak eljuttatott műveletek sorrendjét változtatja meg. Az alkalmazás aktuálisan megjelenített művelete a művelet listán szereplő _első műveletként_ lesz elküldve. A [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) ezt az első műveletet használja a személyre szabott modell betanításához.

### <a name="configure-your-application-to-call-the-rank-api"></a>Az alkalmazás konfigurálása a Rank API meghívásához

Ahhoz, hogy testreszabni lehessen az alkalmazáshoz, meg kell hívnia a Rank és a jutalmazási API-kat.

1. Adja hozzá a [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) -hívást a meglévő alkalmazás-logikában található pont után, ahol meghatározhatja a műveletek listáját és azok funkcióit. A műveletek listán szereplő első műveletnek a meglévő logika által kiválasztott műveletnek kell lennie.

1. Állítsa be a kódot úgy, hogy megjelenjen a Rank API-válasz **jutalmazási műveletéhez** társított művelet.

### <a name="configure-your-application-to-call-reward-api"></a>Az alkalmazás konfigurálása a jutalmazási API meghívásához

1. A megjelenő művelet **jutalmazásának** kiszámításához használja a meglévő üzleti logikát. Az értéknek 0 és 1 közé kell esnie. Küldje el ezt a jutalmat személyre a [jutalmazási API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)használatával. A jutalom értéke azonnal nem várható, és az üzleti logikától függően késleltethető egy adott időszakon belül.

1. Ha nem ad vissza a jutalmat a beállított **jutalom várakozási idején** belül, a rendszer az alapértelmezett jutalmat használja helyette.

## <a name="evaluate-apprentice-mode"></a>Tanulói mód kiértékelése

A Azure Portal a személyre szabott erőforrás **értékelés** lapján tekintse át az **aktuális tanulási viselkedés teljesítményét**.

> [!div class="mx-imgBorder"]
> ![Képernyőkép a tanulói mód tanulási viselkedésének értékeléséről Azure Portal](media/settings/evaluate-apprentice-mode.png)

A gyakornoki mód a következő **értékelési mérőszámokat** biztosítja:
* **Alapterv – átlagos jutalom**: az alkalmazás alapértelmezett alapértéke (alapterv).
* **Személyre szabás – átlagos jutalom**: a teljes jutalom személyre szabásának átlaga valószínűleg elérte ezt.
* **Jutalmazza a legutóbbi 1000-események megtérülési arányát**: az alapkonfiguráció és a személyre szabott jutalom aránya – a legutóbbi 1000-események esetében normalizálva.

## <a name="switch-behavior-to-online-mode"></a>Viselkedés váltás online módra

Ha úgy dönt, hogy személyre szabott, 75-85%-os átlagos mozgóátlagmal van betanítva, a modell készen áll az online módra való váltásra.

A személyre szabott erőforrás Azure Portal a **konfiguráció** lapon, a **tanulási viselkedés** lapon válassza **a legjobb művelet visszaadása** lehetőséget, majd válassza a **Mentés** lehetőséget.

Nem kell módosítania a Rank és a jutalmazási API-hívásokat.

## <a name="next-steps"></a>Következő lépések

* [Modell- és tanulási beállítások kezelése](how-to-manage-model.md)
