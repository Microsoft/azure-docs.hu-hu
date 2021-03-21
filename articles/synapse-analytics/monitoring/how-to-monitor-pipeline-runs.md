---
title: Folyamat-futtatások figyelése a szinapszis Studio használatával
description: A szinapszis Studio használatával figyelheti a munkaterület-folyamat futtatását.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: dbd890117c78274392d5745e0563332371b404c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452583"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>A szinapszis Studio használata a munkaterület-feldolgozási folyamatok figyeléséhez

Az Azure szinapszis Analytics segítségével összetett folyamatokat hozhat létre, amelyek automatizálják és integrálják az adatáthelyezést, az adatátalakítást és a számítási tevékenységeket a megoldáson belül. Ezeket a folyamatokat a szinapszis Studio használatával hozhatja létre és figyelheti.

Ez a cikk ismerteti, hogyan figyelheti meg a folyamat futásait, így nyomon követheti a folyamatok legújabb állapotát, problémáit és előrehaladását.

## <a name="access-pipeline-runs-list"></a>Hozzáférési folyamat futtatási listája

A munkaterületen futó folyamatok listájának megtekintéséhez először [nyissa meg a szinapszis Studio](https://web.azuresynapse.net/) alkalmazást, és válassza ki a munkaterületet.

![Bejelentkezés a munkaterületre](./media/common/login-workspace.png)

Miután megnyitotta a munkaterületet, kattintson a bal oldalon található **figyelő** szakaszra.

![Figyelő központ kiválasztása](./media/common/left-nav.png)

Válassza a **folyamat futtatása** lehetőséget a folyamat-futtatások listájának megtekintéséhez.

![Folyamat futtatásának kiválasztása](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filter-your-pipeline-runs"></a>A folyamat futtatásának szűrése

A folyamatok listájának szűrésével megtekintheti azokat, amelyekre kíváncsi. A képernyő felső részén található szűrők segítségével megadhat egy mezőt, amely alapján szűrni szeretne.

A nézetet szűrheti például úgy, hogy csak a "Holiday" nevű folyamathoz tartozó folyamat fusson.

![Minta szűrő](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="view-details-about-a-specific-pipeline-run"></a>Adott folyamat futtatási részleteinek megtekintése

A folyamat futtatásával kapcsolatos részletek megtekintéséhez válassza ki a folyamat futtatását. Ezután tekintse meg a folyamat futtatásához társított tevékenységek futtatását. Ha a folyamat továbbra is fut, nyomon követheti a folyamatot. 
  
## <a name="next-steps"></a>Következő lépések

Az alkalmazások figyelésével kapcsolatos további tudnivalókért tekintse meg a [Apache Spark alkalmazások figyelése](how-to-monitor-spark-applications.md) című cikket. 
