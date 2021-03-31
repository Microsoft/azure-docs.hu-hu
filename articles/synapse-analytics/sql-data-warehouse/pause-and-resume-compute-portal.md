---
title: 'Rövid útmutató: a számítási feladat szüneteltetése és folytatása a dedikált SQL-készletben a Azure Portal használatával'
description: A Azure Portal használatával szüneteltetheti a dedikált SQL-készlet számítási feladatait a költségek megtakarítása érdekében. Ha készen áll az adattárház használatára, folytassa a számítást.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f9cc5083c4b515454b9d5cbc40ed3b48cba80211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602162"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>Rövid útmutató: a számítási feladat szüneteltetése és folytatása a dedikált SQL-készletben a Azure Portal használatával

A Azure Portal a dedikált SQL Pool számítási erőforrások szüneteltetésére és folytatására használható. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

A [create és a összekapcsolás-Portal](../quickstart-create-sql-pool-portal.md) használatával hozzon létre egy **MYSAMPLEDATAWAREHOUSE** nevű dedikált SQL-készletet. 

## <a name="pause-compute"></a>Számítás szüneteltetése

A költségek csökkentése érdekében igény szerint szüneteltetheti és folytathatja a számítási erőforrásokat. Ha például nem fogja használni az adatbázist az éjszaka és a hétvégén, szüneteltetheti ezeket az időpontokban, és a nap folyamán folytathatja.
 
>[!NOTE]
>A számítási erőforrásokért nem számítunk fel díjat, amíg az adatbázis szüneteltetve van. Azonban továbbra is a tárterületért kell fizetnie. 

Az alábbi lépéseket követve szüneteltetheti a dedikált SQL-készletet:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az SQL-készlet megnyitásához nyissa meg a **DEDIKÁLT SQL-készlet** lapot. 
3. A  figyelmeztetés állapota **online**.

    ![Számítás online](././media/pause-and-resume-compute-portal/compute-online.png)

4. A dedikált SQL-készlet szüneteltetéséhez kattintson a **szüneteltetés** gombra. 
5. A folytatáshoz egy megerősítést kérő kérdés jelenik meg. Kattintson a **Yes** (Igen) gombra.
6. Várjon néhány percet, és figyelje meg, hogy  az állapot **szünetel**.

    ![Képernyőfelvétel: a minta adattárház Azure Portal egy szüneteltetési állapottal.](./media/pause-and-resume-compute-portal/pausing.png)

7. A szüneteltetési művelet befejezésekor a rendszer **szünetelteti** az állapotot, és a beállítás gombja **folytatódik**.
8. A dedikált SQL-készlet számítási erőforrásai mostantól offline állapotban vannak. A számítási feladatokért nem számítunk fel díjat, amíg újra nem folytatja a szolgáltatást.

    ![Számítás offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Számítás folytatása

A dedikált SQL-készlet folytatásához kövesse az alábbi lépéseket.

1. Az SQL-készlet megnyitásához nyissa meg a **DEDIKÁLT SQL-készlet** lapot.
3. A **mySampleDataWarehouse** lapon a figyelmeztetés **állapota** **szünetel**.

    ![Számítás offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Az SQL-készlet folytatásához kattintson a **Folytatás** gombra. 
1. Egy megerősítő kérdés jelenik meg, amely megkérdezi, hogy szeretné-e elindítani. Kattintson a **Yes** (Igen) gombra.
1. Figyelje meg, hogy az **állapot** **folytatása** folyamatban van.

    ![A képernyőképen egy minta adattárház Azure Portal látható a Start gomb kiválasztásával és a folytatás állapot értékével.](./media/pause-and-resume-compute-portal/resuming.png)

1. Ha az SQL-készlet ismét online állapotba kerül, az állapota **online** állapotban van, a választókapcsoló pedig **szünetel**.
1. Az SQL-készlet számítási erőforrásai online állapotban vannak, és használhatja a szolgáltatást. A számítási költségek újraindultak.

    ![Számítás online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adatraktár-egységekért és a dedikált SQL-készletben tárolt adatért számítunk fel díjat. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva. 

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást.
- Ha el szeretné távolítani a jövőbeli díjakat, törölheti a dedikált SQL-készletet. 

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza ki a dedikált SQL-készletet.

    ![Az erőforrások eltávolítása](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. A számítási erőforrások szüneteltetéshez kattintson a **Szüneteltetés** gombra. 

1. Ha el szeretné távolítani a dedikált SQL-készletet, hogy ne kelljen fizetnie a számításért vagy a tárolásért, kattintson a **Törlés** gombra.



## <a name="next-steps"></a>Következő lépések

Ezzel szüneteltette és folytatta a számítást a dedikált SQL-készlethez. A következő cikkből megtudhatja, hogyan tölthetők be az információk [egy DEDIKÁLT SQL-készletbe](./load-data-from-azure-blob-storage-using-copy.md). A számítási képességek kezelésével kapcsolatos további információkért tekintse meg a [számítás áttekintését](sql-data-warehouse-manage-compute-overview.md) ismertető cikket.