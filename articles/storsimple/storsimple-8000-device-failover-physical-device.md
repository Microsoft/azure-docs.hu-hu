---
title: Feladatátvétel, vész-helyreállítás egy másik StorSimple 8000-eszközön
description: Megtudhatja, hogyan hajthat végre feladatátvételt a StorSimple 8000 sorozatú fizikai eszközön egy másik fizikai eszközre.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 67d6fb1f3cc359288ed942d915e186542a62b0fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017134"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Feladatátvétel egy StorSimple 8000 sorozatú fizikai eszközre

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyek szükségesek a StorSimple 8000 sorozatú fizikai eszköz feladatátvételéhez egy másik StorSimple fizikai eszközre, ha van ilyen katasztrófa. A StorSimple az eszköz feladatátvételi funkciójával áttelepíti az adatközpontban lévő forrás fizikai eszköz adatait egy másik fizikai eszközre. Az oktatóanyagban szereplő útmutatás a StorSimple 8000 Series fizikai eszközökre vonatkozik, amelyek a 3. és újabb verziójú szoftverfrissítéseket futtatják.

Ha többet szeretne megtudni az eszközök feladatátvételéről, valamint arról, hogy miként használható a katasztrófa elleni felépülésre, ugorjon a feladatátvétel és a vész- [helyreállítás a StorSimple 8000 Series-eszközökre](storsimple-8000-device-failover-disaster-recovery.md)című témakörben.

Ha StorSimple fizikai eszközt szeretne átadni egy StorSimple Cloud Appliance, ugorjon át a [feladatátvétel egy StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Ha a fizikai eszközt saját magára szeretné átadni, ugorjon [a feladatátvétel ugyanarra a StorSimple fizikai eszközre](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy áttekintette az eszköz feladatátvételével kapcsolatos szempontokat. További információért látogasson el az [eszközök feladatátvételének gyakori szempontjaira](storsimple-8000-device-failover-disaster-recovery.md).

- Az adatközpontban üzembe helyezett StorSimple 8000 sorozatú fizikai eszköznek kell lennie. Az eszközön futtatnia kell a 3. frissítést vagy újabb verziót. További információért látogasson el a helyszíni [StorSimple-eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md)című témakörre.


## <a name="steps-to-fail-over-to-a-physical-device"></a>Fizikai eszközre történő feladatátvétel lépései

A következő lépések végrehajtásával állítsa vissza az eszközt egy célként szolgáló fizikai eszközre.

1. Ellenőrizze, hogy a felvenni kívánt kötet-tároló hozzá van-e rendelve Felhőbeli pillanatképekhez. További információért látogasson el a [StorSimple Eszközkezelő szolgáltatás használata a biztonsági másolatok létrehozásához](storsimple-8000-manage-backup-policies-u2.md).
2. Lépjen a StorSimple Eszközkezelő, majd kattintson az **eszközök** elemre. A **Devices (eszközök** ) panelen lépjen a szolgáltatáshoz csatlakoztatott eszközök listájára.
    ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Válassza ki a forrás eszközt, majd kattintson rá. A forrásoldali eszközön szerepelnek a felvenni kívánt mennyiségi tárolók. Válassza a **beállítások > kötetek tárolók lehetőséget**.
4. Válasszon ki egy olyan mennyiségi tárolót, amelyet át szeretne adni egy másik eszközre. Kattintson a kötet tárolóra a tárolóban található kötetek listájának megjelenítéséhez. Válasszon ki egy kötetet, kattintson rá a jobb gombbal, majd kattintson az **Offline** állapotba állítás lehetőségre a kötet offline állapotba helyezéséhez. Ismételje meg a folyamatot a mennyiségi tároló összes kötetén.
5. Ismételje meg az előző lépést az összes olyan mennyiségi tárolónál, amelyet át szeretne adni egy másik eszközre.
6. Lépjen vissza az **eszközök** panelre. A parancssorban kattintson a **feladatátvétel** elemre.
    ![Kattintson a feladatátvétel lehetőségre](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. A **feladatátvétel** panelen hajtsa végre a következő lépéseket:
   
   1. Kattintson a **forrás** lehetőségre. Megjelennek a Felhőbeli pillanatképekkel társított kötet-tárolók. Csak a megjelenő tárolók jogosultak a feladatátvételre. A mennyiségi tárolók listájában válassza ki a felvenni kívánt kötetek tárolóit. **Csak a társított Felhőbeli pillanatképekkel és kapcsolat nélküli kötetekkel rendelkező tárolók jelennek meg.**

       ![Forrás kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Kattintson a **cél** elemre. Az előző lépésben kiválasztott mennyiségi tárolók esetében válassza ki a kívánt eszközt az elérhető eszközök legördülő listájából. A listában csak azok az eszközök jelennek meg, amelyek elegendő kapacitással rendelkeznek a forrásként szolgáló tárolók befogadásához.

        ![Cél kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Végezetül tekintse át a feladatátvételi beállításokat az **Összefoglalás** területen. Miután áttekintette a beállításokat, jelölje be a jelölőnégyzetet, amely azt jelzi, hogy a kiválasztott mennyiségi tárolókban lévő kötetek offline állapotban vannak. Kattintson az **OK** gombra.

       ![Feladatátvételi beállítások áttekintése](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. A StorSimple feladatátvételi feladatot hoz létre. Kattintson a feladat értesítésére a feladatátvételi feladat nyomon követéséhez a **feladatok** panelen.

    Ha a feladatátvétel alatt álló kötet helyi kötetekkel rendelkezik, akkor a tárolóban minden helyi kötetre (nem a lépcsőzetes kötetek esetében) egyedi visszaállítási feladatok láthatók. A visszaállítási feladatok végrehajtása hosszabb időt is igénybe vehet. Valószínű, hogy a feladatátvételi feladatok korábban is befejeződik. Ezek a kötetek csak a visszaállítási feladatok befejezését követően kapnak helyi garanciát.

    ![Feladatátvételi feladatok figyelése](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. A feladatátvétel befejezése után lépjen az **eszközök** panelre.
   
   1. Válassza ki a feladatátvételi folyamat célként használt eszközét.

       ![2. eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Nyissa meg a **Volume containers** panelt. Az összes kötet-tárolót, valamint a régi eszköz köteteit is fel kell listázni.

       ![Cél mennyiségi tárolók megtekintése](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Következő lépések

* A feladatátvétel elvégzése után szükség lehet [a StorSimple-eszköz inaktiválására vagy törlésére](storsimple-8000-deactivate-and-delete-device.md).
* További információ a StorSimple Eszközkezelő szolgáltatás használatáról: [a StorSimple-eszköz felügyeletéhez használja a StorSimple Eszközkezelő szolgáltatást](storsimple-8000-manager-service-administration.md).

