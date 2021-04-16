---
title: 'Rövid útmutató: Számítási feladatok elkülönítésének konfigurálása – Portal'
description: A Azure Portal konfigurálhatja a számítási feladatok elkülönítését a dedikált SQL-készlethez.
services: synapse-analytics
author: ronortloff
ms.author: rortloff
manager: craigg
ms.reviewer: jrasnick
ms.date: 05/04/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- azure-synapse
- mode-portal
ms.openlocfilehash: c15f21064012c195315a7f91908b3160591dc6f8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534263"
---
# <a name="quickstart-configure-dedicated-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Rövid útmutató: Dedikált SQL-készlet számítási feladatelszigetelésének konfigurálása számítási feladatcsoport használatával a Azure Portal

Ebben a rövid útmutatóban úgy konfigurálhatja a [számítási](sql-data-warehouse-workload-isolation.md) feladatok elkülönítését, hogy létrehoz egy számítási feladatcsoportot az erőforrások lefoglalása érdekében.  Ebben az oktatóanyagban létre fogjuk hozni a nevű számítási feladatcsoportot az adatok `DataLoads` betöltéséhez. A számítási feladatcsoport lefoglalja a rendszererőforrások 20%-át.  Az adatbeterhelések 20%-os elkülönítése garantáltan olyan erőforrásokat biztosít, amelyek lehetővé teszik számukra, hogy el tudják érni az SBA-okat.  A számítási feladatcsoport létrehozása után hozzon létre egy számítási feladat [osztályozóját,](quickstart-create-a-workload-classifier-portal.md) amely a lekérdezéseket ehhez a számítási feladatcsoporthoz rendeli.


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.


## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

> [!NOTE]
> Dedikált SQL-készletpéldány létrehozása a Azure Synapse Analytics új számlázható szolgáltatást eredményezhet.  További információkért lásd: [Azure Synapse Analytics díjszabása.](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy már rendelkezik egy dedikált SQL-készletpéldánysal a Synapse SQL, és hogy rendelkezik CONTROL DATABASE engedélyekkel. Ha létre kell hoznia egyet, használja a következő rövid [útmutatót:](../quickstart-create-sql-pool-portal.md) Dedikált SQL-készlet létrehozása – portál egy **mySampleDataWarehouse nevű adattárház létrehozásához.**

>[!IMPORTANT] 
>A számítási feladatok felügyeletének konfigurálása érdekében a dedikált SQL-készletnek online állapotúnak kell lennie. 

## <a name="configure-workload-isolation"></a>Számítási feladatok elkülönítésének konfigurálása

A dedikált SQL-készlet erőforrásai a számítási feladatok csoportjainak létrehozásával elkülöníthetőek és lefoglalhatók adott számítási feladatokhoz.  A számítási [feladatok elkülönítésével kapcsolatos](sql-data-warehouse-workload-isolation.md) fogalmak dokumentációjában további részleteket talál arról, hogyan segítenek a számítási feladatok csoportjai a számítási feladatok kezelésében.  A [Létrehozás és csatlakozás – portál rövid](create-data-warehouse-portal.md) útmutató létrehozta a **mySampleDataWarehouse** adatokat, és inicializálta azt a DW100c-ben. A következő lépések egy számítási feladatcsoportot hoznak létre a **mySampleDataWarehouse mappában.**

20%-os elkülönítésű számítási feladatcsoport létrehozása:
1.  Lépjen a **mySampleDataWarehouse dedikált SQL-készlet** lapra.
1.  Válassza a **Számítási feladatok kezelése lehetőséget.**
1.  Válassza **az Új számítási feladatcsoport lehetőséget.**
1.  Válassza az **Egyéni lehetőséget.**

    ![Kattintson az Egyéni elemre.](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  A `DataLoads` Számítási feladat **csoportnál adja meg a következőt:**.
7.  Adja meg `20` a **következőt: Min. resources %**.
8.  Adja `5` meg a **következőt: Min. resources % per request ..**
9.  Adja meg `100` a **következőt: Cap resources %**.
10. Írja be a **Mentés gombra.**

   ![Kattintson a Save (Mentés) gombra.](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

A számítási feladatcsoport létrehozásakor megjelenik egy portálértesítés.  A számítási feladatcsoport erőforrásai a konfigurált értékek alatti diagramokon jelennek meg.

   ![Kattintson a Végső gombra](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az `DataLoads` oktatóanyagban létrehozott számítási feladatcsoport törlése:
1. Kattintson a számítási **`...`** feladatcsoporttól jobbra `DataLoads` gombra.
2. Kattintson a **Delete workload group (Számítási feladatcsoport törlése) elemre.**
3. Amikor a rendszer **kéri,** kattintson az Igen gombra a számítási feladatcsoport törlésének megerősítéséhez.
4. Kattintson a **Mentés gombra.**

   ![Kattintson a Törlés gombra.](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Díjat számítunk fel az adattárházegységekért és az adattárházban tárolt adatokért. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha szeretné az adatokat megtartani a tárolóban, a számítási erőforrásokat szüneteltetheti, amíg nem használja az adattárházat. A számítási erőforrások szüneteltetésért csak az adattárolásért kell fizetnie. Ha készen áll az adatokkal való munkára, folytassa a számítást.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

Kövesse az alábbi lépéseket az erőforrások tisztítása érdekében.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)válassza ki a dedikált SQL-készletet.

    ![Az erőforrások eltávolítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéshez válassza a **Szüneteltetés gombot.** Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítás folytatásához válassza az Indítás **lehetőséget.**

3. Ha el szeretné távolítani az adattárházat, hogy ne számítson fel díjat a számítási vagy tárolási erőforrásokért, válassza a **Törlés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

A számítási feladatcsoport használatának érdekében létre kell hozva egy számítási feladat osztályozóját, amely a kéréseket a számítási `DataLoads` feladatcsoporthoz [](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) irányítja.  Folytassa a [Számítási feladat osztályozó](quickstart-create-a-workload-classifier-portal.md) létrehozása oktatóanyagmal, amely egy számítási feladat osztályozóját hozza létre a `DataLoads` számára.

## <a name="see-also"></a>Lásd még
A számítási feladatok [](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) számítási feladatok felügyeletének a számítási feladatok felügyeletével kapcsolatos részletes információkért tekintse meg a Számítási feladatok kezelése és figyelése cikket.
