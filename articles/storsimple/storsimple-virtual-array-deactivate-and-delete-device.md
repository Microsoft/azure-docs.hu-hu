---
title: Microsoft Azure StorSimple virtuális tömb inaktiválása és törlése | Microsoft Docs
description: Leírja, hogyan távolíthatja el a StorSimple eszközt a szolgáltatásból, ha először inaktiválja, majd törli azt.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 84fd1a2d3d0dc4ce6960469ea3212b8ca1d4d07c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023023"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Virtuális StorSimple-tömb inaktiválása és törlése

## <a name="overview"></a>Áttekintés

Ha inaktiválja a StorSimple virtuális tömböt, akkor megszakítja a kapcsolatot az eszköz és a hozzá tartozó StorSimple Eszközkezelő szolgáltatás között. Ez az oktatóanyag a következőket ismerteti:

* Eszköz inaktiválása 
* Inaktivált eszköz törlése

A cikkben található információk csak a StorSimple virtuális tömbökre vonatkoznak. További információ az 8000 sorozatról: [eszköz inaktiválása vagy törlése](./storsimple-8000-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Mikor kell inaktiválni?

Az Inaktiválás állandó művelet, ezért nem vonható vissza. A StorSimple Eszközkezelő szolgáltatással nem regisztrálható inaktivált eszköz. Előfordulhat, hogy inaktiválnia kell egy StorSimple virtuális tömböt, és törölnie kell a következő helyzetekben:

* **Tervezett feladatátvétel** : az eszköz online állapotban van, és az eszköz feladatátvételét tervezi. Ha nagyobb eszközre kíván frissíteni, lehet, hogy feladatátvételt kell végeznie az eszközön. Az adattulajdonos átvitele és a feladatátvétel befejezése után a rendszer automatikusan törli a forrás eszközt.
* Nem **tervezett feladatátvétel** : az eszköz offline állapotban van, és feladatátvételt kell végeznie az eszközön. Ez a helyzet akkor fordulhat elő, ha az adatközpontban áramkimaradás történik, és az elsődleges eszköz nem áll le. Azt tervezi, hogy feladatátvételt hajt végre az eszközön egy másodlagos eszközön. Az adattulajdonos átvitele és a feladatátvétel befejezése után a rendszer automatikusan törli a forrás eszközt.
* **Leszerelés** : az eszközt le szeretné szerelni. Ehhez először inaktiválnia kell az eszközt, majd törölnie kell azt. Ha inaktiválja az eszközt, a továbbiakban nem férhet hozzá a helyileg tárolt összes adattal. Csak a felhőben tárolt adatokat érheti el és állíthatja helyre. Ha azt tervezi, hogy inaktiválás után megtartja az eszköz adatait, akkor az eszköz inaktiválása előtt készítsen egy Felhőbeli pillanatképet az összes adatairól. Ez a Felhőbeli pillanatkép lehetővé teszi, hogy egy későbbi időpontban helyreállítsa az összes adatokat.

## <a name="deactivate-a-device"></a>Eszköz inaktiválása

Az eszköz inaktiválásához hajtsa végre az alábbi lépéseket.

#### <a name="to-deactivate-the-device"></a>Az eszköz inaktiválása

1. A szolgáltatásban nyissa meg a **felügyeleti > eszközöket**. Az **eszközök** panelen kattintson és jelölje ki az inaktiválni kívánt eszközt.
   
    ![Inaktiválni kívánt eszköz kiválasztása](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Az **eszköz irányítópult** paneljén kattintson a **... elemre. Továbbiak** és a listából válassza az **inaktiválás** lehetőséget.
   
    ![Az Inaktiválás elemre kattintva](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Az **inaktiválás** panelen írja be az eszköz nevét, majd kattintson az **inaktiválás** elemre. 
   
    ![Inaktiválás megerősítése](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    A inaktiválási folyamat elindul, és néhány percet vesz igénybe.
   
    ![Inaktiválás folyamatban](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Az inaktiválást követően az eszközök listája frissül.
   
    ![Inaktiválás befejezve](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Most már törölheti az eszközt.

## <a name="delete-the-device"></a>Eszköz törlése

Először inaktiválnia kell az eszközt a törléshez. Egy eszköz törlésével a szolgáltatáshoz csatlakoztatott eszközök listájáról törlődik. A szolgáltatás ezután már nem tudja kezelni a törölt eszközt. Az eszközhöz társított adatai azonban a felhőben maradnak. Ezek az adatok ezután felszámítják a díjakat.

Az eszköz törléséhez hajtsa végre a következő lépéseket.

#### <a name="to-delete-the-device"></a>Az eszköz törlése

1. A StorSimple Eszközkezelő lépjen a **felügyeleti > eszközök** elemre. Válassza ki a törölni kívánt inaktivált eszközt az **eszközök** panelen.
2. Az **eszköz irányítópult** paneljén kattintson a **... elemre. Továbbiak** , majd kattintson a **Törlés** gombra.
   
   ![Válassza ki a törölni kívánt eszközt](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. A **Törlés** panelen írja be az eszköz nevét a törlés megerősítéséhez, majd kattintson a **Törlés** gombra. Az eszköz törlése nem törli az eszközhöz társított Felhőbeli adatmennyiséget. 
   
   ![Törlés megerősítése](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. A törlés elindul, és néhány percet vesz igénybe.
   
   ![Törlés folyamatban](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Az eszköz törlése után megtekintheti az eszközök frissített listáját.

## <a name="next-steps"></a>Következő lépések

* További információ a feladatátvételi feladatokról: [a StorSimple virtuális tömb feladatátvétele és vész-helyreállítása](storsimple-virtual-array-failover-dr.md).

* Ha többet szeretne megtudni a StorSimple Eszközkezelő szolgáltatás használatáról, látogasson el [a StorSimple Eszközkezelő szolgáltatás használatára a StorSimple virtuális tömb felügyeletéhez](storsimple-virtual-array-manager-service-administration.md).