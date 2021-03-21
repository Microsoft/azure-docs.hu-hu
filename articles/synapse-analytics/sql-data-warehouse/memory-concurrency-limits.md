---
title: Memória és egyidejűségi korlátok
description: Megtekintheti az Azure szinapszis Analytics szolgáltatásban található dedikált SQL-készlethez tartozó különböző teljesítményszint és erőforrás-osztályok számára lefoglalt memóriát és egyidejűségi korlátokat.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fb0ad93fb4a1269b4cca02b114c0427f0c44a31b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96455358"
---
# <a name="memory-and-concurrency-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>A dedikált SQL-készlet memóriájának és egyidejűségének korlátai az Azure szinapszis Analyticsben

Megtekintheti a különböző teljesítményszint és erőforrás-osztályok számára lefoglalt memóriát és egyidejűségi korlátokat az Azure szinapszis Analyticsben.  

## <a name="data-warehouse-capacity-settings"></a>Adatraktár kapacitásának beállításai

Az alábbi táblázatokban az adatraktár maximális kapacitása látható a különböző teljesítményszint esetén. A teljesítmény szintjének módosításához tekintse meg a [számítások – portál méretezése](quickstart-scale-compute-portal.md)című témakört.

### <a name="service-levels"></a>Szolgáltatási szintek

A szolgáltatási szintek a DW100c és a DW30000c között vannak.

| Teljesítményszint | Számítási csomópontok | Disztribúciók/számítási csomópontok | Memória/adattárház (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c lehetőséget            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

A maximális szolgáltatási szint a DW30000c, amelynek 60 számítási csomópontja van, és a számítási csomópontok egy eloszlása. Például egy 600 TB-os adattárház a DW30000c-on a számítási csomópont körülbelül 10 TB-ot dolgoz fel.

## <a name="concurrency-maximums-for-workload-groups"></a>Munkaterhelés-csoportok egyidejűségi maximális száma

A [munkaterhelési csoportok](sql-data-warehouse-workload-isolation.md)bevezetésével az egyidejű tárolóhelyek fogalma már nem érvényes.  A kérelmekre vonatkozó erőforrások százalékos arányban vannak lefoglalva, és a munkaterhelés csoport definíciójában vannak megadva.  A párhuzamossági tárolóhelyek eltávolításával azonban a szolgáltatási szint alapján a lekérdezésekhez szükséges minimális mennyiségű erőforrásra van szükség.  Az alábbi táblázat a szolgáltatási szintek és a hozzájuk kapcsolódó egyidejűségek által lekérdezett erőforrások minimális mennyiségét definiálta.

|Szolgáltatásszint|Egyidejű lekérdezések maximális száma|A REQUEST_MIN_RESOURCE_GRANT_PERCENT minimális%-a támogatott|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12,5%|
|DW300c|12|8%|
|DW400c|16|6,25%|
|DW500c lehetőséget|20|5%|
|DW1000c|32|3%|
|DW1500c|32|3%|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1,5%|
|DW5000c|64|1,5%|
|DW6000c|128|0,75%|
|DW7500c|128|0,75%|
|DW10000c|128|0,75%|
|DW15000c|128|0,75%|
|DW30000c|128|0,75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Erőforrás-osztályok egyidejűségi maximális száma

Annak biztosítása érdekében, hogy az egyes lekérdezések hatékony végrehajtásához elegendő erőforrás legyen, a szinapszis SQL nyomon követi az erőforrás-használatot, ha egyidejűségi tárolóhelyeket rendel az egyes lekérdezésekhez. A rendszer a lekérdezéseket a fontos és a párhuzamossági tárolóhelyek alapján a várólistára helyezi. A lekérdezések csak akkor várnak a várólistára, ha elegendő egyidejűségi tárolóhely áll rendelkezésre. A [fontosság](sql-data-warehouse-workload-importance.md) és a egyidejűségi tárolóhelyek a CPU rangsorolását határozzák meg. További információ: [a munkaterhelés elemzése](analyze-your-workload.md)

**Statikus erőforrás-osztályok**

A következő táblázat a [statikus erőforrások osztályának](resource-classes-for-workload-management.md)maximális egyidejű lekérdezéseit és egyidejűségi tárolóhelyeit mutatja be.  

| Szolgáltatásszint | Egyidejű lekérdezések maximális száma | Elérhető egyidejűségi bővítőhelyek | A staticrc10 által használt bővítőhelyek | A staticrc20 erőforrásosztályhoz által használt bővítőhelyek | A staticrc30 által használt bővítőhelyek | A staticrc40 által használt bővítőhelyek | A staticrc50 által használt bővítőhelyek | A staticrc60 által használt bővítőhelyek | A staticrc70 által használt bővítőhelyek | A staticrc80 által használt bővítőhelyek |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c lehetőséget        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dinamikus erőforrás-osztályok**

Az alábbi táblázat az egyes [dinamikus erőforrás-osztályok](resource-classes-for-workload-management.md)maximális egyidejű lekérdezéseit és egyidejűségi tárolóhelyeit mutatja be. A dinamikus erőforrás-osztályok a 3-10-22-70-es memória-kiosztást használják a kis-közepes méretű XLarge erőforrás-osztályokhoz az összes szolgáltatási szinten.

| Szolgáltatásszint | Egyidejű lekérdezések maximális száma | Elérhető egyidejűségi bővítőhelyek | A smallrc által használt bővítőhelyek | A mediumrc által használt bővítőhelyek | A largerc által használt bővítőhelyek | A xlargerc által használt bővítőhelyek |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c lehetőséget        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |

Ha nincs elegendő egyidejűségi tárolóhely a lekérdezés végrehajtásának megkezdéséhez, a lekérdezéseket a rendszer a fontosság alapján várólistára helyezi és végrehajtja.  Ha ennek megfelelő jelentősége van, a lekérdezéseket a rendszer az első, első kijelentkezési alapon hajtja végre.  A lekérdezések befejeződése után a lekérdezések és a tárolóhelyek száma a határértékek alá esik, az Azure szinapszis Analytics várólistán lévő lekérdezéseket szabadít fel.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogyan használhat erőforrás-osztályokat a számítási feladatok további optimalizálása érdekében, tekintse át a következő cikkeket:

* [Erőforrás-osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md)
* [Számítási feladatok elemzése](analyze-your-workload.md)
