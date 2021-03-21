---
title: Számítási feladatok fontosságának konfigurálása dedikált SQL-készlethez
description: Ismerje meg, hogyan állíthatja be a kérelmek szintjének fontosságát az Azure szinapszis Analyticsben.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 38fb842cf90c110266f53b79a9ab2ef6157025b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681285"
---
# <a name="configure-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>A számítási feladatok fontosságának konfigurálása az Azure szinapszis Analytics dedikált SQL-készletében

Az Azure Szinapszishoz készült dedikált SQL-készlet fontosságának beállítása lehetővé teszi a lekérdezések ütemezésének befolyásolását. A nagyobb jelentőségű lekérdezéseket a rendszer úgy ütemezi, hogy az alacsonyabb fontosságú lekérdezések előtt fusson. A lekérdezések fontosságának kiosztásához létre kell hoznia egy számítási feladatok besorolását.

## <a name="create-a-workload-classifier-with-importance"></a>Számítási feladatok besorolása fontossággal

Gyakran egy adattárház-forgatókönyvben a felhasználók egy foglalt rendszeren vannak, akiknek gyorsan le kell futtatniuk a lekérdezéseket.  Előfordulhat, hogy a felhasználó a vállalat vezetőinek kell futnia, vagy ha a felhasználó egy ad hoc lekérdezést futtató elemző. A fontosság kiosztásához létre kell hoznia egy számítási feladatok besorolását, és a fontosságot egy lekérdezéshez rendeli hozzá.  Az alábbi példák a  [munkaterhelés-osztályozó létrehozása](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) szintaxist használják két osztályozó létrehozásához. `Membername` egyetlen felhasználó vagy csoport lehet.  A meglévő dedikált SQL Pool-felhasználók megkereséséhez futtassa a következőt:

```sql
Select name from sys.sysusers
```

A számítási feladatok besorolásának létrehozásához nagyobb jelentőségű felhasználó esetén a következőt kell futtatni:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

A számítási feladatok besorolásának létrehozása az olyan felhasználók számára, akik az alkalmi lekérdezéseket alacsonyabb fontossággal futtatják:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Következő lépések

- A számítási feladatok kezelésével kapcsolatos további információkért lásd: [munkaterhelés besorolása](sql-data-warehouse-workload-classification.md)
- További információ a Fontosságról: számítási [feladatok fontossága](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ugrás a számítási feladatok fontosságának kezeléséhez és figyeléséhez](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
