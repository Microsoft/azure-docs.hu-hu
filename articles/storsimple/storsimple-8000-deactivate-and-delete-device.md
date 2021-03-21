---
title: StorSimple 8000 Series-eszköz inaktiválása és törlése | Microsoft Docs
description: Megtudhatja, hogyan inaktiválhat és törölhet olyan StorSimple-eszközöket, amelyek StorSimple Eszközkezelő szolgáltatáshoz csatlakoznak.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 07d108306fdca9bfe8f793b61660550e43151d71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017185"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>StorSimple-eszköz inaktiválása és törlése

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan lehet inaktiválni és törölni egy StorSimple Eszközkezelő szolgáltatáshoz csatlakozó StorSimple-eszközt. A jelen cikkben található útmutatás csak a StorSimple 8000 sorozatú eszközökre vonatkozik, beleértve a StorSimple Cloud Appliances eszközöket is. Ha StorSimple virtuális tömböt használ, ugorjon az Inaktiválás elemre, [és törölje a StorSimple virtuális tömböt](storsimple-virtual-array-deactivate-and-delete-device.md).

Az Inaktiválás megszakítja az eszköz és a hozzá tartozó StorSimple Eszközkezelő szolgáltatás közötti kapcsolatot. Előfordulhat, hogy a StorSimple eszközt (például ha az eszközt lecseréli vagy frissíti, vagy ha már nem használja a StorSimple). Ha igen, a törlés előtt inaktiválnia kell az eszközt.

Ha inaktiválja az eszközt, az eszközön helyileg tárolt összes adatbázis már nem érhető el. Csak a felhőben tárolt eszközhöz társított adatmennyiség állítható helyre.

> [!WARNING]
> Az Inaktiválás állandó művelet, ezért nem vonható vissza. Inaktivált eszköz nem regisztrálható a StorSimple Eszközkezelő szolgáltatásban, kivéve, ha vissza van állítva a gyári alapértékekre.
>
> A gyári beállítások visszaállítása folyamat törli az eszközön helyileg tárolt összes adathalmazt. Ezért az eszköz inaktiválása előtt el kell végeznie az összes adattal kapcsolatos Felhőbeli pillanatképet. Ez a Felhőbeli pillanatkép lehetővé teszi, hogy egy későbbi időpontban helyreállítsa az összes adatokat.

> [!NOTE]
>
> - A StorSimple fizikai eszköz vagy a felhőalapú berendezés inaktiválása előtt győződjön meg arról, hogy a törölt kötet tárolóból származó adatok ténylegesen törlődnek az eszközről. Figyelemmel kísérheti a Felhőbeli felhasználási diagramokat, és amikor a törölt biztonsági másolatok miatt megjelenik a felhő használatának csökkenése, folytathatja az eszköz inaktiválását. Ha az eszköz inaktiválása előtt inaktiválja az eszközt, az adatok a Storage-fiókban maradnak, és felmerülnek a díjak.
>
> - A StorSimple fizikai eszköz vagy a felhőalapú berendezés inaktiválása előtt állítsa le vagy törölje az adott eszköztől függő ügyfeleket és gazdagépeket.
>
> - Ha a Storage-fiók (ok) vagy a mennyiségi tárolóhoz társított Storage-fiókban lévő tárolók már törölve lettek az adatoknak az eszközről való törlése előtt, hibaüzenetet kap, és előfordulhat, hogy nem tudja törölni az adatokból. Azt javasoljuk, hogy törölje az eszközön lévő adatmennyiséget, mielőtt törölné a tárolási fiókot vagy tárolókat. Ebben az esetben azonban folytatnia kell az eszköz inaktiválását és törlését, feltételezve, hogy az adatok már el lettek távolítva a Storage-fiókból.

Az oktatóanyag elolvasása után a következőket teheti:

- Inaktiválhat egy eszközt, és törölheti az adategységeket.
- Az eszköz inaktiválása és az adat megőrzése.

## <a name="deactivate-and-delete-data"></a>Inaktiválás és az adattörlés

Ha szeretné teljesen törölni az eszközt, és nem szeretné megőrizni az eszközön tárolt összes adatát, hajtsa végre az alábbi lépéseket.

### <a name="to-deactivate-the-device-and-delete-the-data"></a>Az eszköz inaktiválása és az adattörlés

1. Az eszköz inaktiválása előtt törölnie kell az eszközhöz társított összes kötet-tárolót (és a köteteket). A kötetek tárolóit csak akkor törölheti, ha törölte a társított biztonsági másolatokat. A StorSimple fizikai eszköz vagy a felhőalapú berendezés inaktiválása előtt tekintse meg a fenti áttekintésben szereplő megjegyzést.

2. Inaktiválja az eszközt az alábbiak szerint:

   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az **eszközök** panelen jelölje ki az inaktiválni kívánt eszközt, kattintson a jobb gombbal, majd kattintson az **inaktiválás** elemre.

        ![StorSimple-eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Az **inaktiválás** panelen írja be az eszköz nevét a megerősítéshez, majd kattintson az **inaktiválás** elemre. A inaktiválási folyamat elindul, és néhány percet vesz igénybe.

        ![StorSimple-eszköz inaktiválása 2](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Az inaktiválást követően teljesen törölheti az eszközt. Egy eszköz törlésével a szolgáltatáshoz csatlakoztatott eszközök listájáról törlődik. A szolgáltatás ezután már nem tudja kezelni a törölt eszközt. Az eszköz törléséhez kövesse az alábbi lépéseket:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az **eszközök** panelen jelölje ki a törölni kívánt inaktivált eszközt, kattintson a jobb gombbal, majd kattintson a **Törlés** parancsra.

        ![StorSimple-eszköz inaktiválása 3](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. A **Törlés** panelen írja be az eszköz nevét a megerősítéshez, majd kattintson a **Törlés** gombra. A törlés eltarthat néhány percig.

        ![StorSimple-eszköz inaktiválása 4](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. A törlés sikeres befejezését követően értesítést kap. Az eszközök listája is frissül, hogy tükrözze a törlést.

## <a name="deactivate-and-retain-data"></a>Az adat inaktiválása és megőrzése

Ha szeretne törölni az eszközt, de szeretné megőrizni az adattárolást, hajtsa végre a következő lépéseket:

### <a name="to-deactivate-a-device-and-retain-the-data"></a>Eszköz inaktiválása és az adat megőrzése

1. Inaktiválja az eszközt. A kötet tárolói és az eszköz pillanatképei továbbra is megmaradnak.
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az **eszközök** panelen jelölje ki az inaktiválni kívánt eszközt, kattintson a jobb gombbal, majd kattintson az **inaktiválás** elemre.

         ![StorSimple-eszköz inaktiválása 5](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Az **inaktiválás** panelen írja be az eszköz nevét a megerősítéshez, majd kattintson az **inaktiválás** elemre. A inaktiválási folyamat elindul, és néhány percet vesz igénybe.

         ![StorSimple-eszköz inaktiválása 6](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Most már elvégezheti a mennyiségi tárolók és a hozzájuk tartozó Pillanatképek feladatátvételét. Az eljárásokért keresse fel a [StorSimple-eszköz feladatátvételi és vész-helyreállítási szolgáltatását](storsimple-8000-device-failover-disaster-recovery.md).
3. Az Inaktiválás és a feladatátvétel után teljesen törölheti az eszközt. Egy eszköz törlésével a szolgáltatáshoz csatlakoztatott eszközök listájáról törlődik. A szolgáltatás ezután már nem tudja kezelni a törölt eszközt. Az eszköz törléséhez hajtsa végre a következő lépéseket:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az **eszközök** panelen jelölje ki a törölni kívánt inaktivált eszközt, kattintson a jobb gombbal, majd kattintson a **Törlés** parancsra.

       ![StorSimple-eszköz inaktiválása 7](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. A **Törlés** panelen írja be az eszköz nevét a megerősítéshez, majd kattintson a **Törlés** gombra. A törlés eltarthat néhány percig.

       ![StorSimple-eszköz inaktiválása 8](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. A törlés sikeres befejezését követően értesítést kap. Az eszközök listája is frissül, hogy tükrözze a törlést.

## <a name="deactivate-and-delete-a-cloud-appliance"></a>Felhőalapú berendezés inaktiválása és törlése

StorSimple Cloud Appliance esetében a portál inaktiválása megszünteti a virtuális gépet, és törli a kiépítés során létrehozott erőforrásokat. A felhőalapú berendezést az inaktiválás után már nem lehet a korábbi állapotára visszaállítani.

![Inaktiválás StorSimple Cloud Appliance](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Az Inaktiválás a következő műveleteket eredményezi:

* A StorSimple Cloud Appliance el lett távolítva a szolgáltatásból.
* A StorSimple Cloud Appliance virtuális gépe törölve.
* A rendszer megőrzi a StorSimple Cloud Appliancehez létrehozott operációsrendszer-lemezt és adatlemezeket. Ha nem használja ezeket az entitásokat, manuálisan törölje őket.
* A rendszer megőrzi a kiépítés során létrehozott üzemeltetett szolgáltatást és Virtual Network. Ha nem használja ezeket az entitásokat, manuálisan törölje őket.
* A StorSimple Cloud Appliance által létrehozott Felhőbeli Pillanatképek megmaradnak.

A felhőalapú berendezés inaktiválása után törölheti az eszközök listájából. Válassza ki a deaktivált eszközt, kattintson a jobb gombbal, majd kattintson a **Törlés** parancsra. A StorSimple értesítést küld az eszköz törléséről és az eszközök listájának frissítéséről.

## <a name="next-steps"></a>Következő lépések

* A deaktivált eszköz gyári beállításokra történő visszaállításához lépjen [az eszköz visszaállítása a gyári alapértelmezésekre](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)elemre.
* Technikai segítségért [forduljon Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md).
* Ha többet szeretne megtudni a StorSimple Eszközkezelő szolgáltatás használatáról, látogasson el [a StorSimple Eszközkezelő szolgáltatás használatára a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

