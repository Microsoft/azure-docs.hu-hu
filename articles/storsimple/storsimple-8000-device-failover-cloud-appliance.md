---
title: Feladatátvételi és vész-helyreállítási StorSimple Cloud Appliance
description: Megtudhatja, hogyan hajthat végre feladatátvételt a StorSimple 8000 sorozatú fizikai eszközön egy felhőalapú berendezésen.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 18b34ff466f3935cb5cd18d46e6d26e36e756a9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91774460"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Feladatátvétel a StorSimple Cloud Appliance

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti azokat a lépéseket, amelyek szükségesek ahhoz, hogy StorSimple 8000 sorozatú fizikai eszközt lehessen felvenni egy StorSimple Cloud Appliance, ha van katasztrófa. A StorSimple az eszköz feladatátvételi funkciójával áttelepítheti az adatközpontban lévő forrás fizikai eszköz adatait az Azure-ban futó felhőalapú készülékre. Az oktatóanyagban szereplő útmutatás a StorSimple 8000 sorozatú fizikai eszközökre és a 3. frissítést és újabb verziókat futtató felhőalapú készülékekre vonatkozik.

Ha többet szeretne megtudni az eszközök feladatátvételéről, valamint arról, hogy miként használható a katasztrófa elleni felépülésre, ugorjon a feladatátvétel és a vész- [helyreállítás a StorSimple 8000 Series-eszközökre](storsimple-8000-device-failover-disaster-recovery.md)című témakörben.

Ha feladatátvételt szeretne átadni egy StorSimple fizikai eszközről egy másik fizikai eszközre, ugorjon [át a feladatátvétel StorSimple fizikai eszközre](storsimple-8000-device-failover-physical-device.md)elemre. Ha az eszközt saját magára szeretné átadni, folytassa [a feladatátvételt ugyanarra a StorSimple fizikai eszközre](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy áttekintette az eszköz feladatátvételével kapcsolatos szempontokat. További információért látogasson el az [eszközök feladatátvételének gyakori szempontjaira](storsimple-8000-device-failover-disaster-recovery.md).

- Az eljárás futtatása előtt létre kell hoznia és konfigurálnia kell egy StorSimple Cloud Appliance. Ha a 3. frissítés szoftverét vagy újabb verzióját futtatja, érdemes lehet egy 8020 felhőalapú készüléket használni a DR. Az 8020-es modell 64 TB-os, és Premium Storage használ. További információért látogasson el a [StorSimple Cloud Appliance üzembe helyezéséhez és kezeléséhez](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>A felhőalapú berendezés feladatátvételének lépései

A következő lépések végrehajtásával állítsa vissza az eszközt egy célként StorSimple Cloud Appliance.

1.  Ellenőrizze, hogy a felvenni kívánt kötet-tároló hozzá van-e rendelve Felhőbeli pillanatképekhez. További információért látogasson el a [StorSimple Eszközkezelő szolgáltatás használata a biztonsági másolatok létrehozásához](storsimple-8000-manage-backup-policies-u2.md).
2. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. A **Devices (eszközök** ) panelen lépjen a szolgáltatáshoz csatlakoztatott eszközök listájára.
    ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Válassza ki a forrás eszközt, majd kattintson rá. A forrásoldali eszközön szerepelnek a felvenni kívánt mennyiségi tárolók. Válassza a **beállítások > kötetek tárolók lehetőséget**.

    ![2. eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Válasszon ki egy olyan mennyiségi tárolót, amelyet át szeretne adni egy másik eszközre. Kattintson a kötet tárolóra a tárolóban található kötetek listájának megjelenítéséhez. Válasszon ki egy kötetet, kattintson rá a jobb gombbal, majd kattintson az **Offline** állapotba állítás lehetőségre a kötet offline állapotba helyezéséhez.

    ![3. eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Ismételje meg a folyamatot a mennyiségi tároló összes kötetén.

     ![4. eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Ismételje meg az előző lépést az összes olyan mennyiségi tárolónál, amelyet át szeretne adni egy másik eszközre.

7. Lépjen vissza az **eszközök** panelre. A parancssorban kattintson a **feladatátvétel** elemre.

    ![Kattintson a feladatátvétel lehetőségre](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. A **feladatátvétel** panelen hajtsa végre a következő lépéseket:
   
    1. Kattintson a **forrás** lehetőségre. Válassza ki a feladatátvételhez használandó mennyiségi tárolókat. **Csak a társított Felhőbeli pillanatképekkel és kapcsolat nélküli kötetekkel rendelkező tárolók jelennek meg.**
        ![Forrás kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Kattintson a **cél** elemre. Válasszon ki egy cél felhőalapú készüléket az elérhető eszközök legördülő listájából. **A listában csak azok az eszközök jelennek meg, amelyek elegendő kapacitással rendelkeznek a forrásként szolgáló tárolók befogadásához.**

        ![Cél kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Tekintse át a feladatátvételi beállításokat az **Összefoglalás** területen, és jelölje be a jelölőnégyzetet, amely azt jelzi, hogy a kiválasztott mennyiségi tárolók kötetei offline állapotban vannak. 

        ![Feladatátvételi beállítások áttekintése](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. A rendszer létrehoz egy feladatátvételi feladatot. A feladatátvételi feladatok figyeléséhez kattintson a feladatok értesítésére.

    ![Feladatátvételi feladatok figyelése](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. A feladatátvétel befejezése után lépjen vissza az **eszközök** panelre.

    1. Válassza ki azt az eszközt, amelyet a feladatátvétel céljaként használt.

       ![5. eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Kattintson a **kötetek tárolók** elemre. Az összes kötet-tárolót, valamint a régi eszköz köteteit is fel kell listázni.

       Ha a feladatátvétel alatt álló kötet helyileg rögzített kötetekkel rendelkezik, akkor ezek a kötetek a lépcsőzetes kötetek felett lesznek felmentve. A helyileg rögzített kötetek nem támogatottak StorSimple Cloud Applianceon.

       ![Cél mennyiségi tárolók megtekintése](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Következő lépések

* A feladatátvétel elvégzése után szükség lehet [a StorSimple-eszköz inaktiválására vagy törlésére](storsimple-8000-deactivate-and-delete-device.md).

* További információ a StorSimple Eszközkezelő szolgáltatás használatáról: [a StorSimple-eszköz felügyeletéhez használja a StorSimple Eszközkezelő szolgáltatást](storsimple-8000-manager-service-administration.md).

