---
title: 'Oktatóanyag: Ismerkedés az Azure szinapszis Analytics szolgáltatással – a szinapszis-munkaterület monitorozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan figyelheti a tevékenységeket a szinapszis-munkaterületen.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 7e8525dbebb42e1f387ee8f0c192efd5e64c9453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426040"
---
# <a name="monitor-your-synapse-workspace"></a>A szinapszis-munkaterület figyelése

Ebből az oktatóanyagból megtudhatja, hogyan figyelheti a tevékenységeket a szinapszis-munkaterületen. Az SQL, a Apache Spark és a folyamatok aktuális és korábbi tevékenységeit is figyelheti. 

## <a name="introduction-to-the-monitor-hub"></a>A figyelő központ bemutatása

Nyissa meg a szinapszis Studio alkalmazást, és navigáljon a **figyelő** központhoz. Itt láthatja az összes olyan tevékenység előzményeit, amely a munkaterületen zajlik, és melyek aktívak. 

* Az **integráció** alatt nyomon követheti a folyamatokat, az eseményindítókat és az integrációs modulokat.
* A **tevékenységek** területen nyomon követheti a Spark és az SQL tevékenységeket. 

## <a name="integration"></a>Integráció

1. Navigáljon az **integrációs > folyamatának futtatásához**. Ebben a nézetben láthatja, hogy a folyamat minden alkalommal fut a munkaterületen. 
1. Keresse meg az előző lépésben futtatott folyamatot, és kattintson a **folyamatának nevére** a részletek megtekintéséhez.
1. A szinapszis Studio felső részén található **navigációs sáv** lehetőségre kattintva az előző nézethez való visszatéréshez kattintson a **minden folyamat futtatása** elemre.

## <a name="apache-spark-activities"></a>Apache Spark tevékenységek

1. Navigáljon a **tevékenységek > Apache Spark alkalmazások** elemre. Most már megtekintheti az összes olyan Spark-alkalmazást, amely fut vagy fut a munkaterületen.
1. Keressen olyan alkalmazást, amely már nem fut, és kattintson az **alkalmazás nevére**. Most megtekintheti a Spark-alkalmazás részleteit.
1. Ha már ismeri a Apache Sparkt, megkeresheti a standard Apache Spark History Server felhasználói felületet a **Spark History Serverre** kattintva.

## <a name="sql-activities"></a>SQL-tevékenységek

1. Navigáljon a **tevékenységek > SQL-kérelmek** elemre.
1. Ebben a nézetben az SQL-kérelmeket láthatja.
1. Válasszon ki egy figyelni kívánt **készletet** a **készlet** szűrőből. Most láthatja, hogy az összes futó SQL-kérelem vagy a munkaterületen fut a készletben.
1. Keressen egy adott SQL-kérést, és kattintson a **további** HIVATKOZÁSRA az SQL-kérelem teljes szövegének megtekintéséhez.

    > [!NOTE] 
    > A szinapszis studión keresztül elküldött SQL-kérelmek egy olyan munkaterületen, amelyen engedélyezve van a dedikált SQL-készlet (korábbi nevén SQL DW), megtekinthetők a figyelő központban. Az összes többi figyelési tevékenységhez lépjen Azure Portal dedikált SQL-készlet (korábban SQL DW) figyelése lehetőségre.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Knowledge centerrel](get-started-knowledge-center.md)
