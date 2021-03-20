---
title: Oktatóanyag – feladatátvétel a StorSimple fizikai eszközön ugyanarra az eszközre
description: Megtudhatja, hogy milyen lépések szükségesek ahhoz, hogy a StorSimple 8000 sorozatú fizikai eszköz feladatátvétele megtörténjen.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: 17c116194aa52a82246bcee9114824e8a918ebbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017100"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Feladatátvétel a StorSimple fizikai eszközön ugyanarra az eszközre

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti azokat a lépéseket, amelyek szükségesek ahhoz, hogy a StorSimple 8000 sorozatú fizikai eszköz feladatátvétele megtörténjen. A StorSimple az eszköz feladatátvételi funkciójával áttelepíti az adatközpontban lévő forrás fizikai eszköz adatait egy másik fizikai eszközre. Az oktatóanyagban szereplő útmutatás a StorSimple 8000 Series fizikai eszközökre vonatkozik, amelyek a 3. és újabb verziójú szoftverfrissítéseket futtatják.

Ha többet szeretne megtudni az eszközök feladatátvételéről, valamint arról, hogy miként használható a katasztrófa elleni felépülésre, ugorjon a feladatátvétel és a vész- [helyreállítás a StorSimple 8000 Series-eszközökre](storsimple-8000-device-failover-disaster-recovery.md)című témakörben.

Ha fizikai eszközt szeretne átadni egy másik fizikai eszközre, folytassa [a feladatátvételt ugyanarra a StorSimple fizikai eszközre](storsimple-8000-device-failover-physical-device.md). Ha StorSimple fizikai eszközt szeretne átadni egy StorSimple Cloud Appliance, ugorjon át a [feladatátvétel egy StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy áttekintette az eszköz feladatátvételével kapcsolatos szempontokat. További információért látogasson el az [eszközök feladatátvételének gyakori szempontjaira](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>A feladatátvétel lépései ugyanarra az eszközre

Hajtsa végre az alábbi lépéseket, ha ugyanarra az eszközre kell átadnia a feladatátvételt.

1. Készítsen Felhőbeli pillanatképeket az eszközön található összes kötetről. További információért látogasson el a [StorSimple Eszközkezelő szolgáltatás használata a biztonsági másolatok létrehozásához](storsimple-8000-manage-backup-policies-u2.md).
2. Állítsa vissza az eszközt a gyári beállításokra. Kövesse a [StorSimple-eszköz gyári beállításainak alaphelyzetbe állítására](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)vonatkozó részletes útmutatást.
3. Lépjen a StorSimple Eszközkezelő szolgáltatásra, majd válassza az **eszközök** elemet. Az **eszközök** panelen a régi eszköznek **offline állapotba** kell mutatnia.

    ![Forrás eszköz offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Konfigurálja az eszközt, és regisztrálja újra a StorSimple Eszközkezelő szolgáltatásával. Az újonnan regisztrált eszköznek **készen kell állnia a beállításra**. Az új eszköz neve megegyezik a régi eszközzel, de számjegyek hozzáfűzésével jelzi, hogy az eszköz visszaáll a gyári alapértékre, és ismét regisztrálva van.

    ![Az újonnan regisztrált eszköz készen áll a beállításra](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Az új eszköz esetében fejezze be az eszköz beállítását. További információért lépjen a [4. lépés: a minimális eszköz beállításának befejezése szakaszra](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Az **eszközök** panelen az eszköz állapota **online** állapotra változik.

   > [!IMPORTANT]
   > **Először hajtsa végre a minimális konfigurációt, vagy a DR sikertelen lehet.**

    ![Újonnan regisztrált eszköz online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Válassza ki a régi eszközt (offline állapot), majd a parancssorban kattintson a **feladatátvétel** elemre. A **feladatátvétel** panelen válassza a régi eszköz lehetőséget forrásként, majd adja meg a célként megadott eszközt az újonnan regisztrált eszközként.

    ![Feladatátvétel összegzése](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Részletes utasításokért tekintse át a feladatátvétel egy másik fizikai eszközre című témakört.

7. Létrejön egy eszköz-visszaállítási feladat, amelyet a **feladatok** panelről lehet figyelni.

8. A feladatok sikeres befejeződése után nyissa meg az új eszközt, és navigáljon a **Volume containers (mennyiségi tárolók** ) panelre. Győződjön meg arról, hogy a régi eszközön lévő összes kötet-tároló át lett telepítve az új eszközre.

   ![Áttelepített mennyiségi tárolók](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. A feladatátvétel befejeződése után inaktiválhatja és törölheti a régi eszközt a portálról. Válassza ki a régi eszközt (offline), kattintson a jobb gombbal, majd válassza az **inaktiválás** lehetőséget. Az eszköz inaktiválása után a rendszer frissíti az eszköz állapotát.

     ![A forrásoldali eszköz inaktiválva](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Válassza ki a deaktivált eszközt, kattintson a jobb gombbal, majd válassza a **Törlés** lehetőséget. Ezzel törli az eszközt az eszközök listájáról.

    ![Forrásoldali eszköz törölve](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Következő lépések

* A feladatátvétel elvégzése után szükség lehet [a StorSimple-eszköz inaktiválására vagy törlésére](storsimple-8000-deactivate-and-delete-device.md).
* További információ a StorSimple Eszközkezelő szolgáltatás használatáról: [a StorSimple-eszköz felügyeletéhez használja a StorSimple Eszközkezelő szolgáltatást](storsimple-8000-manager-service-administration.md).

