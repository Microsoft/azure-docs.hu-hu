---
title: 'Rövid útmutató: Számítási feladatok szüneteltetése és folytatása dedikált SQL-készletben a Azure Portal'
description: A Azure Portal szüneteltetheti a dedikált SQL-készlet számítási kapacitását a költségek csökkentése érdekében. Ha készen áll az adattárház használatára, folytassa a számítást.
services: synapse-analytics
author: julieMSFT
ms.author: jrasnick
manager: craigg
ms.reviewer: igorstan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: 27587f436013bcaccbacd668ce62f1d939f89e48
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566817"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>Rövid útmutató: Számítási feladatok szüneteltetése és folytatása dedikált SQL-készletben a Azure Portal

A virtuális Azure Portal szüneteltetheti és folytathatja a dedikált SQL-készlet számítási erőforrásait. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

A [Create and Connect - portal használatával hozzon](../quickstart-create-sql-pool-portal.md) létre egy **mySampleDataWarehouse nevű dedikált SQL-készletet.** 

## <a name="pause-compute"></a>Számítás szüneteltetése

A költségek csökkentése érdekében igény szerint szüneteltetheti és folytathatja a számítási erőforrásokat. Ha például nem használja az adatbázist éjszaka és hétvégeken, szüneteltetheti az idő alatt, és a nap folyamán folytathatja.
 
>[!NOTE]
>Az adatbázis szüneteltetése közben nem számítunk fel díjat a számítási erőforrásokért. A tárterületért azonban továbbra is díjat számítunk fel. 

Kövesse az alábbi lépéseket egy dedikált SQL-készlet szüneteltetése érdekében:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Lépjen a Dedikált **SQL-készlet** lapra az SQL-készlet megnyitásához. 
3. Figyelje **meg, hogy az Állapot** **online.**

    ![Online számítás](././media/pause-and-resume-compute-portal/compute-online.png)

4. A dedikált SQL-készlet szüneteltetéshez kattintson a **Szüneteltetés gombra.** 
5. Megjelenik egy megerősítő kérdés, amely megkérdezi, hogy folytatja-e a munkát. Kattintson a **Yes** (Igen) gombra.
6. Várjon néhány percet, és figyelje meg, hogy az **Állapot** **a Szüneteltetés.**

    ![Képernyőkép a Azure Portal adattárházhoz a Szüneteltetés Állapot értékkel.](./media/pause-and-resume-compute-portal/pausing.png)

7. Ha a szüneteltetési művelet befejeződött, az állapot **Szüneteltetve,** a beállításgomb pedig **Resume (Folytatás) lesz.**
8. A dedikált SQL-készlet számítási erőforrásai mostantól offline állapotúak. A számításért a szolgáltatás folytatásáig nem számítunk fel díjat.

    ![Offline számítás](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Számítás folytatása

Kövesse az alábbi lépéseket a dedikált SQL-készlet folytatásához.

1. Lépjen a Dedikált **SQL-készlet** lapra az SQL-készlet megnyitásához.
3. A **mySampleDataWarehouse** oldalon figyelje meg, hogy **az Állapot** **Szüneteltetve.**

    ![Offline számítás](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Az SQL-készlet folytatásához kattintson a **Folytatás gombra.** 
1. Megjelenik egy megerősítő kérdés, amely rákérdez, hogy szeretne-e elindulni. Kattintson a **Yes** (Igen) gombra.
1. Figyelje **meg, hogy a Status** (Állapot) állapota **Resuming (Áttűnés).**

    ![Képernyőkép a Azure Portal adattárházhoz kiválasztott Indítás gombbal és a Resuming Állapot értékkel.](./media/pause-and-resume-compute-portal/resuming.png)

1. Ha az SQL-készlet újra elérhető, az állapot **Online,** a beállításgomb pedig **Pause (Szüneteltetés)** lesz.
1. Az SQL-készlet számítási erőforrásai most már online állapotúak, és használhatja a szolgáltatást. A számítási feladatok díjai visszaestek.

    ![Online számítás](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adattárházegységekért és a dedikált SQL-készletben tárolt adatokért díjat számítunk fel. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva. 

- Ha az adatokat a tárolóban szeretné tartani, szüneteltesse a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti a dedikált SQL-készletet. 

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)és válassza ki a dedikált SQL-készletet.

    ![Az erőforrások eltávolítása](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. 

1. Ha el szeretné távolítani a dedikált SQL-készletet, hogy ne számítson fel díjat a számítási vagy tárolási erőforrásokért, kattintson a **Törlés gombra.**



## <a name="next-steps"></a>Következő lépések

Szüneteltette és folytatta a számítást a dedikált SQL-készlethez. Folytassa a következő cikkel, ha többet szeretne megtudni arról, hogyan lehet [adatokat betölteni egy dedikált SQL-készletbe.](./load-data-from-azure-blob-storage-using-copy.md) A számítási képességek kezelésével kapcsolatos további információkért tekintse meg a [Számítási feladatok kezelése – áttekintés cikket.](sql-data-warehouse-manage-compute-overview.md)
