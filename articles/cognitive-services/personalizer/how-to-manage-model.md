---
title: Modell-és tanulási beállítások kezelése – személyre szabás
description: A gép által megtanult modell-és tanulási beállítások a saját verziókövetés rendszerében exportálhatók a biztonsági mentéshez.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 48e954eab9da5dfc638f93a7c4e55c675c4f2ac9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97797247"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Modell-és tanulási beállítások kezelése

A gép által megtanult modell-és tanulási beállítások a saját verziókövetés rendszerében exportálhatók a biztonsági mentéshez.

## <a name="export-the-personalizer-model"></a>A személyre szabott modell exportálása

A **modell-és tanulási beállítások** erőforrás-kezelés szakaszában tekintse át a modell létrehozásának és utolsó frissítésének dátumát, és exportálja az aktuális modellt. A Azure Portal vagy a személyre szabható API-k használatával archiválhatja a modelleket archiválás céljából.

![Aktuális személyre szabott modell exportálása](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Az adatgyűjtési hurokhoz tartozó adattörlés

1. A Azure Portal a személyre szabott erőforráshoz a **modell és tanulás beállításai** lapon válassza az **adattörlés** lehetőséget.
1. Az összes érték törléséhez és a tanulási hurok eredeti állapotba való visszaállításához jelölje be a 3 jelölőnégyzetet.

    ![A Azure Portalban törölje a jelet az adatok személyre szabása erőforrásból elemre.](./media/settings/clear-data-from-personalizer-resource.png)

    |Érték|Cél|
    |--|--|
    |Naplózott személyre szabás és jutalmazási érték.|A naplózási adatai kapcsolat nélküli értékelésekben használatosak. Törölje az adatait, ha alaphelyzetbe állítja az erőforrást.|
    |A személyre szabott modell alaphelyzetbe állítása.|Ez a modell minden átképzésen megváltozik. A betanítás gyakorisága a konfiguráció lapon a **feltöltési modell gyakorisága** **beállításban** van megadva. |
    |Állítsa be a képzési szabályzatot alapértelmezett értékre.|Ha az offline kiértékelés részeként módosította a képzési szabályzatot, a rendszer visszaállítja az eredeti tanulási szabályzatot.|

1. A törlési folyamat elindításához válassza a **kijelölt adatelemek törlése** lehetőséget. Az állapot az Azure-értesítésekben szerepel, a jobb felső navigációs sávon.

## <a name="import-a-new-learning-policy"></a>Új képzési szabályzat importálása

A képzési [szabályzat](concept-active-learning.md#understand-learning-policy-settings) beállításai határozzák meg a _hiperparaméterek beállításaát_ . Egy új képzési szabályzat megtalálásához végezzen [Offline értékelést](how-to-offline-evaluation.md) .

1. Nyissa meg a [Azure Portal](https://portal.azure.com), és válassza ki a személyre szabott erőforrást.
1. Válassza ki a **modell-és tanulási beállításokat** az **Erőforrás-kezelés** szakaszban.
1. A **tanulási beállítások importálása** lehetőségnél válassza ki a fent megadott JSON-formátummal létrehozott fájlt, majd kattintson a **feltöltés** gombra.

    Várjon, amíg befejeződik a tanulási szabályzat feltöltése.

## <a name="export-a-learning-policy"></a>Képzési szabályzat exportálása

1. Nyissa meg a [Azure Portal](https://portal.azure.com), és válassza ki a személyre szabott erőforrást.
1. Válassza ki a **modell-és tanulási beállításokat** az **Erőforrás-kezelés** szakaszban.
1. A **tanulási beállítások importálása** gombra kattintva válassza a **tanulási Beállítások exportálása** gombot. Ezzel menti a `json` fájlt a helyi számítógépre.

## <a name="next-steps"></a>Következő lépések

[A tanulási hurok elemzése offline kiértékeléssel](how-to-offline-evaluation.md)
