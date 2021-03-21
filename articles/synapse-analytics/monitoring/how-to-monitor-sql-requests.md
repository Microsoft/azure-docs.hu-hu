---
title: SQL-kérelmek figyelése a szinapszis Studióban
description: Ismerje meg, hogyan figyelheti meg az SQL-kéréseket a szinapszis Studio használatával.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466651"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Az SQL-kérelmek figyelése a szinapszis Studio használatával

A szinapszis Studio használatával SQL-parancsfájlokat futtathat a munkaterületen található SQL-készleteken.

Ez a cikk ismerteti, hogyan figyelheti meg az SQL-kérelmeket, így nyomon követheti a futó kérelmek állapotát, és felderítheti a korábbi kérelmek részleteit.

## <a name="access-sql-requests-list"></a>Hozzáférés az SQL-kérelmek listájához

A munkaterületen található SQL-kérelmek listájának megtekintéséhez először [nyissa meg a szinapszis Studio](https://web.azuresynapse.net/) alkalmazást, és válassza ki a munkaterületet.

![Bejelentkezés a munkaterületre](./media/common/login-workspace.png)

Miután megnyitotta a munkaterületet, kattintson a bal oldalon található **figyelő** szakaszra.

![Figyelő központ kiválasztása](./media/common/left-nav.png)

Válassza az **SQL-kérelmek** lehetőséget az SQL-kérelmek listájának megtekintéséhez.

 ![SQL-kérelmek kiválasztása](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>SQL-készlet kiválasztása

Alapértelmezés szerint ebben a nézetben jelennek meg az SQL-kérelmek előzményei a beépített kiszolgáló nélküli SQL-készlethez. Kiválaszthatja az egyik dedikált SQL-készletet a készlet SQL-kérelmi előzményeinek megtekintéséhez.

![SQL-készlet kiválasztása](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>SQL-kérelmek szűrése

Az SQL-kérelmek listáját az Önt érdeklő értékekre szűrheti. A képernyő felső részén található szűrők segítségével megadhat egy mezőt, amely alapján szűrni szeretne.

A nézetet szűrheti például úgy, hogy csak a által küldött SQL-kérelmeket jelenítse meg `maria@contoso.com` :

![Minta szűrő](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>Adott SQL-kérelem részleteinek megtekintése

Az egyik SQL-kérelem részleteinek megtekintéséhez nyissa meg az SQL-kérést, és navigáljon a részletek nézethez. A dedikált SQL-készleteken futó összetett kérelmek esetében nyomon követheti a folyamat előrehaladását.

## <a name="next-steps"></a>Következő lépések

A folyamatok figyelésével kapcsolatos további információkért tekintse meg a [folyamatok figyelése a szinapszis Studióban](how-to-monitor-pipeline-runs.md) című cikket. 

A Apache Spark-alkalmazások figyelésével kapcsolatos további információkért lásd: [Apache Spark alkalmazások figyelése a szinapszis Studio-](how-to-monitor-spark-applications.md) cikkben.