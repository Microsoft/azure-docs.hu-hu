---
title: Tanulási szabályzat – személyre szabás
description: A képzési beállítások határozzák meg a *hiperparaméterek beállításaát* . A különböző tanulási beállításokba betanított két modell eltérő lesz.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1d2038e3796df843736eb80f7e5645f4141c82f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91253632"
---
# <a name="learning-policy-and-settings"></a>Képzési szabályzat és beállítások

A képzési beállítások határozzák meg a *hiperparaméterek beállításaát* . A különböző tanulási beállításokba betanított két modell eltérő lesz.

A [képzési szabályzat és a beállítások](how-to-settings.md#configure-rewards-for-the-feedback-loop) a Azure Portal személyre szabott erőforrására vannak beállítva.

## <a name="import-and-export-learning-policies"></a>Képzési szabályzatok importálása és exportálása

A Azure Portalból importálhat és exportálhat tanulási-házirend fájlokat. Ezzel a módszerrel mentheti a meglévő szabályzatokat, tesztelheti és lecserélheti őket, és archiválhatja őket a forráskód vezérlőelemben a későbbi referenciák és a naplózás érdekében.

Megtudhatja, [Hogyan](how-to-manage-model.md#import-a-new-learning-policy) importálhat és exportálhat egy tanulási szabályzatot a Azure Portal a személyre szabott erőforráshoz.

## <a name="understand-learning-policy-settings"></a>A tanulási szabályzat beállításainak megismerése

A tanulási szabályzat beállításai nem változnak. Csak akkor módosítsa a beállításokat, ha tisztában van azzal, hogyan befolyásolják a személyre szabást. Ezen ismeret nélkül problémákat okozhat, beleértve a személyre szabott modellek érvénytelenítését is.

A személyre szabott [vowpalwabbit](https://github.com/VowpalWabbit) -t használ az események betanítására és kiértékelésére. Tekintse meg a [vowpalwabbit dokumentációját](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) , amely bemutatja, hogyan szerkesztheti a tanulási beállításokat a vowpalwabbit használatával. Ha a parancssori argumentumok megfelelőek, mentse a parancsot egy fájlba a következő formátumban (az argumentumok tulajdonság értékét cserélje le a kívánt parancsra), majd töltse fel a fájlt a személyre szabott erőforráshoz tartozó Azure Portal **modell és tanulási beállítások** paneljén található tanulási beállítások importálásához.

A következő `.json` példa egy tanulási szabályzatot mutat be.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Tanulási szabályzatok összehasonlítása

Összehasonlíthatja, hogy a különböző képzési szabályzatok hogyan végezhetők el a személyre szabott naplók korábbi adatain az [Offline értékelések](concepts-offline-evaluation.md)használatával.

[Töltse fel saját képzési szabályzatait](how-to-manage-model.md) , hogy összehasonlítsa őket az aktuális tanulási házirenddel.

## <a name="optimize-learning-policies"></a>Tanulási szabályzatok optimalizálása

A személyre szabott képzési szabályzatok egy [Offline próbaverzióban](how-to-offline-evaluation.md)is létrehozhatók. Egy optimalizált tanulási szabályzat, amely jobb jutalmakat tartalmaz az offline értékelésekben, jobb eredményeket fog eredményezni, amikor online használatban van a személyre Szabásban.

A tanulási szabályzatok optimalizálása után közvetlenül is alkalmazhatja a személyre szabást, hogy azonnal felváltsa az aktuális házirendet. Vagy mentheti az optimalizált szabályzatot a további értékeléshez, és később eldöntheti, hogy elveti, menti vagy alkalmazza.

## <a name="next-steps"></a>Következő lépések

* [Az aktív és az inaktív események](concept-active-inactive-events.md)megismerése.
