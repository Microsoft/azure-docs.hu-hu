---
title: Apache Spark alkalmazások figyelése a szinapszis Studióban
description: Megtudhatja, hogyan figyelheti Apache Spark alkalmazásait a szinapszis Studio használatával.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 5f9733866e85d79bdb85b8a24d1878e1169c2479
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586040"
---
# <a name="how-to-use-synapse-studio-to-monitor-your-apache-spark-applications"></a>A szinapszis Studio használata a Apache Spark-alkalmazások figyelésére

Az Azure szinapszis Analytics segítségével a Spark használatával jegyzetfüzeteket, feladatokat és más típusú alkalmazásokat futtathat a Spark-készletekben a munkaterületen.

Ez a cikk ismerteti, hogyan figyelheti meg Apache Spark-alkalmazásait, így nyomon követheti a legújabb állapotot, problémákat és előrehaladást.

## <a name="access-apache-spark-applications-list"></a>Hozzáférési Apache Spark alkalmazások listája

A munkaterületen lévő Apache Spark alkalmazások listájának megtekintéséhez először [nyissa meg a szinapszis studiót](https://web.azuresynapse.net/) , és válassza ki a munkaterületet.

![Bejelentkezés a munkaterületre](./media/common/login-workspace.png)

Miután megnyitotta a munkaterületet, kattintson a bal oldalon található **figyelő** szakaszra.

![Figyelő központ kiválasztása](./media/common/left-nav.png)

Válassza **Apache Spark alkalmazások** lehetőséget Apache Spark alkalmazások listájának megtekintéséhez.

 ![Spark-alkalmazások kiválasztása](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>Apache Spark-alkalmazások szűrése

A Apache Spark-alkalmazások listáját szűrheti azokra, akikre kíváncsi. A képernyő felső részén található szűrők segítségével megadhat egy mezőt, amely alapján szűrni szeretne.

Például szűrheti a nézetet úgy, hogy csak a "Sales" nevet tartalmazó Apache Spark alkalmazásokat jelenítse meg:

![Minta szűrő](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Adott Apache Spark alkalmazás részleteinek megtekintése

Ha meg szeretné tekinteni az egyik Apache Spark alkalmazás részleteit, válassza ki a Apache Spark alkalmazást, és tekintse meg a részleteket. Ha a Apache Spark alkalmazás még fut, nyomon követheti a folyamatot. [További információk](apache-spark-applications.md).

## <a name="next-steps"></a>Következő lépések

A folyamatok figyelésével kapcsolatos további információkért tekintse meg a [folyamatok figyelése a szinapszis Studio](how-to-monitor-pipeline-runs.md) -ban című cikket. 

Apache Spark alkalmazás hibakeresésével kapcsolatos további információkért tekintse meg a [Apache Spark alkalmazások monitorozása a szinapszis Studióban](apache-spark-applications.md) című cikket.