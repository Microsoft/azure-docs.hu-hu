---
title: Az AK-fürtöket befolyásoló Resource Health események keresése (előzetes verzió)
description: Az AK-fürt állapotának ellenõrzése Azure Resource Health használatával.
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92070656"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>Az AK-fürtöket befolyásoló Resource Health események keresése (előzetes verzió)


Ha a tároló számítási feladatait az AK-on futtatja, azt szeretné, hogy a probléma megoldásához a számítási feladatok lehető leghamarabb, a munkaterhelések rendelkezésre állásának csökkentése érdekében javítsa a problémákat. [Azure Resource Health](../service-health/resource-health-overview.md) betekintést nyújt a különböző egészségügyi eseményekre, amelyek hatására előfordulhat, hogy az AK-fürt nem érhető el.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Resource Health megnyitása

### <a name="to-access-resource-health-for-your-aks-cluster"></a>Resource Health elérése az AK-fürthöz:

- Navigáljon az AK-fürthöz a [Azure Portal](https://portal.azure.com).
- A bal oldali navigációs sávon válassza a **Resource Health** lehetőséget.

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>Az előfizetéshez tartozó összes fürt Resource Healthának elérése:

- Keresse meg **Service Health** a [Azure Portalban](https://portal.azure.com) , és navigáljon hozzá.
- A bal oldali navigációs sávon válassza az **erőforrás állapota** lehetőséget.
- Válassza ki az előfizetését, és állítsa be az erőforrás típusát az Azure Kubernetes Service (ak) értékre.

![A képernyőfelvételen a K S fürtök erőforrás-állapota látható.](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>Állapot állapotának ellenõrzése

Azure Resource Health segítséget nyújt az Azure-erőforrásokat érintő szolgáltatási problémák diagnosztizálásában és támogatásában. Resource Health jelentéseket az erőforrások jelenlegi és múltbeli állapotáról, és segít megállapítani, hogy a problémát egy felhasználó által kezdeményezett művelet vagy egy platform esemény okozza-e.

A Resource Health jeleket fogad a felügyelt fürthöz a fürt állapotának megállapításához. Megvizsgálja az AK-fürt állapotát, és az egyes állapot-jelzésekhez szükséges műveleteket jelenti. Ezek a jelek a felhasználó által kezdeményezett műveletek által okozott problémák automatikus feloldásával, tervezett frissítésekkel, nem tervezett állapotokkal és a rendelkezésre állással kapcsolatosak. Ezeket a jeleket a Azure Resource Health állapota: *elérhető*, nem *érhető* el, *ismeretlen* és *csökkentett teljesítményű* állapotba soroltuk.

- **Elérhető**: Ha nincs olyan ismert probléma, amely hatással van a fürt állapotára, Resource Health a fürtöt *elérhetőként* jelenti.

- Nem **érhető** el: Ha a fürt állapotát érintő platform vagy nem platform típusú esemény van, Resource Health a fürtöt nem *elérhetőként* jelenti.

- **Ismeretlen**: Ha a fürt állapot-metrikáinak ideiglenes kapcsolódási adatvesztést okoz, Resource Health a fürtöt *ismeretlenként* jelenti.

- **Csökkentett teljesítményű**: Ha a beavatkozást igénylő állapotra vonatkozó probléma merül fel, Resource Health a fürtöt *csökkentett teljesítményű* jelenti.

Az egyes állapotok állapotával kapcsolatos további részletekért tekintse meg a [Resource Health áttekintését](../service-health/resource-health-overview.md#health-status).

### <a name="view-historical-data"></a>Korábbi adatértékek megtekintése

Megtekintheti az elmúlt 30 nap korábbi Resource Health információkat is az **állapotadatok** szakaszban.

## <a name="next-steps"></a>Következő lépések

Futtasson ellenőrzéseket a fürtön a fürtökkel kapcsolatos problémák további hibaelhárításához az [AK-diagnosztika](./concepts-diagnostics.md)használatával.