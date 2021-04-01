---
title: 'Rövid útmutató: a munkaterhelés elkülönítésének konfigurálása – portál'
description: A Azure Portal használatával konfigurálhatja a számítási feladatok elkülönítését a dedikált SQL-készlethez.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 5773691852ddc723ec84503edee37c678bbbcfd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98677504"
---
# <a name="quickstart-configure-dedicated-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Rövid útmutató: a dedikált SQL Pool számítási feladatok elkülönítésének beállítása munkaterhelés-csoporttal a Azure Portal

Ebben a rövid útmutatóban a [munkaterhelés elkülönítését](sql-data-warehouse-workload-isolation.md) úgy konfigurálja, hogy létrehoz egy munkaterhelés-csoportot az erőforrások megőrzéséhez.  Ebben az oktatóanyagban a meghívott adatok betöltése munkaterhelési csoportot hozunk létre `DataLoads` . A munkaterhelés-csoport a rendszererőforrások 20%-át fogja lefoglalni.  Az adatterhelések 20%-os elkülönítése esetén azok garantált erőforrások, amelyek lehetővé teszik számukra, hogy kitalálják a SLA-kat.  A munkaterhelési csoport létrehozása után [hozzon létre egy](quickstart-create-a-workload-classifier-portal.md) számítási feladattal rendelkező számítási feladatot, amely lekérdezéseket rendel ehhez a munkaterhelés-csoporthoz


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.


## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

> [!NOTE]
> Ha egy dedikált SQL Pool-példányt hoz létre az Azure szinapszis Analytics szolgáltatásban, akkor egy új számlázható szolgáltatást eredményezhet.  További információ: az [Azure szinapszis Analytics díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik egy dedikált SQL Pool-példánnyal a szinapszis SQL-ben, és rendelkezik a VEZÉRLÉSi adatbázis engedélyeivel. Ha létre kell hoznia egyet, használja a rövid útmutató [: DEDIKÁLT SQL-készlet létrehozása-portált](../quickstart-create-sql-pool-portal.md) egy **mySampleDataWarehouse** nevű adattárház létrehozásához.

>[!IMPORTANT] 
>A számítási feladatok kezelésének konfigurálásához a dedikált SQL-készletnek online állapotban kell lennie. 

## <a name="configure-workload-isolation"></a>Munkaterhelés elkülönítésének konfigurálása

A dedikált SQL Pool-erőforrások elkülönítése és meghatározott számítási feladatokhoz való lefoglalása munkaterhelési csoportok létrehozásával lehetséges.  Tekintse meg a munkaterhelés [elkülönítési](sql-data-warehouse-workload-isolation.md) koncepciójának dokumentációját, ahol további információt talál arról, hogy a munkaterhelés-csoportok hogyan segítik a számítási feladatok kezelését.  A [Létrehozás és összekapcsolás-portál](create-data-warehouse-portal.md) rövid útmutatója létrehozta a **mySampleDataWarehouse** , és inicializálta azt a DW100c címen. A következő lépésekkel hozhat létre munkaterhelés-csoportot a **mySampleDataWarehouse**-ben.

Munkaterhelés-csoport létrehozása 20%-os elkülönítéssel:
1.  Navigáljon a **mySampleDataWarehouse** dedikált SQL-készlet lapjához.
1.  Válassza a  **munkaterhelés-kezelés** lehetőséget.
1.  Válassza az **új munkaterhelési csoport** lehetőséget.
1.  Válassza az **Egyéni** lehetőséget.

    ![Kattintson az egyéni elemre.](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Adja meg `DataLoads` a **munkaterhelés csoportot**.
7.  Adja meg `20` a **(z)%. erőforrások minimális** értékeit.
8.  Adja meg `5` a **(z)%-os minimális erőforrások számát**.
9.  Adja meg `100` a **Cap-erőforrások%**-át.
10. Adja meg a **Mentés** gombot.

   ![Kattintson a Save (Mentés) gombra.](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

A munkaterhelési csoport létrehozásakor megjelenik egy portál értesítése.  A munkaterhelési csoport erőforrásai a konfigurált értékek alatt jelennek meg a diagramokon.

   ![Kattintson a végleges gombra](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az `DataLoads` oktatóanyagban létrehozott munkaterhelés-csoport törlése:
1. Kattintson a **`...`** `DataLoads` munkaterhelés csoport jobb oldalán.
2. Kattintson a **munkaterhelési csoport törlése** elemre.
3. Kattintson az **Igen** gombra, amikor a rendszer felszólítja a munkaterhelés-csoport törlésének megerősítésére.
4. Kattintson a **Save (Mentés**) gombra.

   ![Kattintson a Törlés gombra.](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Az adattárház-egységek és az adattárházban tárolt adatforgalomért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha szeretné az adatokat megtartani a tárolóban, a számítási erőforrásokat szüneteltetheti, amíg nem használja az adattárházat. A számítás felfüggesztésével csak az adattárolás díját számítjuk fel. Ha készen áll az adatok feldolgozására, folytassa a számítást.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

Az erőforrások tisztításához kövesse az alábbi lépéseket.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza ki a dedikált SQL-készletet.

    ![Az erőforrások eltávolítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **szüneteltetés** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítás folytatásához kattintson a **Start** gombra.

3. Ha el szeretné távolítani az adattárházat, hogy ne számítsa ki a számítási és a tárolási díjat, válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

A `DataLoads` munkaterhelés csoport használatához létre [kell](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) hoznia egy számítási feladatot, amely a kérelmeknek a munkaterhelés-csoportba való továbbításához szükséges.  Folytassa a számítási [feladatok besorolása](quickstart-create-a-workload-classifier-portal.md) oktatóanyagot a számítási feladatok besorolásának létrehozásához `DataLoads` .

## <a name="see-also"></a>Lásd még
A számítási feladatok kezelésével kapcsolatos további információkért tekintse meg a számítási feladatok kezelésével [és figyelésével](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) kapcsolatos cikket.
