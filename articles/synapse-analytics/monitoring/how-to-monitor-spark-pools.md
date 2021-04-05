---
title: Apache Spark készletek figyelése a szinapszis Studióban
description: Ismerje meg, hogyan figyelheti meg Apache Spark-készleteit a szinapszis Studio használatával.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466573"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>A szinapszis Studio használata a Apache Spark-készletek figyeléséhez

Az Azure szinapszis Analytics segítségével a Spark használatával jegyzetfüzeteket, feladatokat és más típusú alkalmazásokat futtathat a Spark-készletekben a munkaterületen.

Ez a cikk ismerteti, hogyan figyelheti meg Apache Spark készleteit, így nyomon követheti a készletek állapotát, beleértve azt is, hogy a különböző munkaterület-felhasználók hány virtuális mag használnak.

## <a name="access-spark-pools-list"></a>Hozzáférés a Spark-készletek listájához

A munkaterületen lévő Apache Spark készletek listájának megtekintéséhez először [nyissa meg a szinapszis Studio](https://web.azuresynapse.net/) alkalmazást, és válassza ki a munkaterületet.

![Bejelentkezés a munkaterületre](./media/common/login-workspace.png)

Miután megnyitotta a munkaterületet, kattintson a bal oldalon található **figyelő** szakaszra.

![Figyelő központ kiválasztása](./media/common/left-nav.png)

Apache Spark készletek listájának megtekintéséhez válassza ki **Apache Spark készleteket** .

 ![Apache Spark készletek kiválasztása](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>A Spark-készletek szűrése

A Spark-készletek listáját megtekintheti az Önt érdeklő listán. A képernyő felső részén található szűrők segítségével megadhat egy mezőt, amely alapján szűrni szeretne.

A nézetet szűrheti például úgy, hogy csak a "adatelőkészítés" nevet tartalmazó Spark-készleteket lásson:

![Minta szűrő](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>Adott Spark-készlet részleteinek megtekintése

Ha szeretné megtekinteni az egyik Spark-készlet adatait, válassza ki a Spark-készletet a részletek megtekintéséhez.

![Apache Spark készlet részletei](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Következő lépések

A folyamatok figyelésével kapcsolatos további információkért tekintse meg a [folyamatok figyelése a szinapszis Studióban](how-to-monitor-pipeline-runs.md) című cikket. 

A Apache Spark-alkalmazások figyelésével kapcsolatos további információkért lásd: [Apache Spark alkalmazások figyelése a szinapszis Studio-](how-to-monitor-spark-applications.md) cikkben.
