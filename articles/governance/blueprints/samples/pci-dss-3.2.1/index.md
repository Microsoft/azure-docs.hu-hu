---
title: Minta – PCI-DSS v3.2.1 terv – Áttekintés
description: A Payment Card Industry Data Security Standard v3.2.1-tervminta áttekintése. Ennek a tervmintának a segítségével az ügyfelek felmérhetnek adott vezérlőket.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: c05f3457b4195c9054a1ce99f2567097a034ebd3
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248251"
---
# <a name="overview-of-the-pci-dss-v321-blueprint-sample"></a>A PCI-DSS v3.2.1-tervminta áttekintése

A PCI-DSS v3.2.1-tervminta egy olyan szabályzatkészlet, amely segít a PCI-DSS 3.2.1-es verziójának való megfelelőség elérésében. Ez a minta segítséget nyújt az ügyfeleknek a PCI-DSS számítási feladatokat tartalmazó felhőalapú környezetek irányításában.
A PCI-DSS-terv bármely, ilyen akkreditációt igénylő, Azure által üzembe helyezett architektúrához üzembe helyez egy alapvető szabályzatkészletet.

## <a name="control-mapping"></a>Vezérlőelem-leképezés

A vezérlőelem-leképezésről szóló szakasz részletesen ismerteti a kezdeményezésben található szabályzatokat, és hogy ezek hogyan segítik elő a PCI-DSS v3.2.1 által meghatározott különböző vezérlőknek való megfelelést. Az Azure Policy kiértékeli, hogy az architektúrához rendelt erőforrások megfelelnek-e a hozzájuk rendelt szabályzatoknak.

A terv hozzárendelése után az Azure Policy megfelelőségi irányítópultján megtekintheti az Azure-környezetek megfelelőségi szintjét.

## <a name="next-steps"></a>További lépések

Ezzel megismerte a PCI-DSS v3.2.1-terv áttekintését. Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlőelem-leképezést és a minta üzembe helyezését ismertetik:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1-terv – Vezérlőelem-leképezés](./control-mapping.md)
> [PCI-DSS v3.2.1-terv – Az üzembe helyezés lépései](./deploy.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.