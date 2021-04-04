---
title: SQL-készletek figyelése a szinapszis Studióban
description: Ismerje meg, hogyan figyelheti meg az SQL-készleteket a szinapszis Studio használatával.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466662"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Az SQL-készletek figyelése a szinapszis Studio használatával

A szinapszis Studio használatával SQL-parancsfájlokat futtathat a munkaterületen található SQL-készleteken.

Ez a cikk ismerteti, hogyan figyelheti meg az SQL-készleteket, így nyomon követheti a készletek állapotát és tevékenységeit.

## <a name="access-sql-pools-list"></a>Hozzáférés az SQL-készletek listájához

Ha szeretné megtekinteni az SQL-készletek listáját a munkaterületen, először [nyissa meg a szinapszis Studio](https://web.azuresynapse.net/) alkalmazást, és válassza ki a munkaterületet.

![Bejelentkezés a munkaterületre](./media/common/login-workspace.png)

Miután megnyitotta a munkaterületet, kattintson a bal oldalon található **figyelő** szakaszra.

![Figyelő központ kiválasztása](./media/common/left-nav.png)

Válassza az **SQL-készletek** lehetőséget az SQL-készletek listájának megtekintéséhez.

 ![SQL-készletek kiválasztása](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>SQL-készletek szűrése

Az SQL-készletek listáját az Önt érdeklő értékekre szűrheti. A képernyő felső részén található szűrők segítségével megadhat egy mezőt, amely alapján szűrni szeretne.

A nézetet szűrheti például úgy, hogy csak a "salesrecords" nevet tartalmazó SQL-készleteket lásson:

![Minta szűrő](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>Adott SQL-készlet adatainak megtekintése

Ha meg szeretné tekinteni az egyik SQL-készlet adatait, válassza ki az SQL-készletet a részletek megtekintéséhez.

![SQL-készlet részletei](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>Következő lépések

A folyamatok figyelésével kapcsolatos további információkért tekintse meg a [folyamatok figyelése a szinapszis Studióban](how-to-monitor-pipeline-runs.md) című cikket. 

Az SQL-kérelmek figyelésével kapcsolatos további információkért tekintse meg az [SQL-kérelmek figyelése a szinapszis Studióban](how-to-monitor-sql-requests.md) című cikket.