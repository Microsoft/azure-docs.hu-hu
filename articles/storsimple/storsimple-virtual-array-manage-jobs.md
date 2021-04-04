---
title: StorSimple virtuális Array-feladatok megtekintése és kezelése | Microsoft Docs
description: Ismerteti a StorSimple Eszközkezelő szolgáltatási feladatok lapját, valamint azt, hogy miként használható a legutóbbi és aktuális feladatok nyomon követésére a StorSimple virtuális tömbben.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: d806d8a04dc1bd8547808d20c77bfec310f7dd06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992985"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple virtuális tömbhöz tartozó feladatok megtekintéséhez
## <a name="overview"></a>Áttekintés
A **feladatok** panel egyetlen központi portált biztosít a StorSimple Eszközkezelő szolgáltatáshoz csatlakozó virtuális tömbökben elindított feladatok megtekintéséhez és kezeléséhez. Több virtuális eszközön is megtekintheti a futó, befejezett és sikertelen feladatokat. Az eredmények táblázatos formátumban jelennek meg.

![Feladatok panel](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Gyorsan megtalálhatja az Önt érdeklő feladatokat a következő mezők szűrésével:

* **Időtartomány** – a feladatok a dátum-és időtartomány alapján szűrhetők.
* **Eszközök** – a feladatok a szolgáltatáshoz csatlakoztatott adott eszközön kezdődnek. A szűrt feladatok a következő attribútumok alapján lesznek táblázatos formában:
  
  * **Név** – a feladat neve lehet az **összes**, a **biztonsági mentés**, a **klónozás**, a **feladatátvétel**, a **frissítések letöltése** vagy a **frissítések telepítése**.
  * **Állapot** – a feladatok lehetnek **mind**, **folyamatban**, **sikeres** vagy **sikertelen**, vagy **megszakadt**.
  * **Entitás** – a feladatok egy kötethez, megosztáshoz vagy eszközhöz társíthatók.
  * **Eszköz** – annak az eszköznek a neve, amelyen a feladatot elindították.
  * **Elindítva** – a feladatok elindításának időpontja.
  * **Időtartam** – a feladatok futtatásának időtartama.
* **Állapot** – megkeresheti az összes, futó, befejezett vagy sikertelen feladatot.
* **Feladattípus** – a feladattípus lehet az összes, a biztonsági mentés, a visszaállítás, a feladatátvétel, a frissítések letöltése vagy a frissítések telepítése.

A feladatok listája 30 másodpercenként frissül.

## <a name="view-job-details"></a>Feladatok részleteinek megjelenítése
A feladatok részleteinek megtekintéséhez hajtsa végre a következő lépéseket.

#### <a name="to-view-job-details"></a>A feladatok részleteinek megtekintése
1. A **feladatok** panelen jelenítse meg azokat a feladatokat, amelyek érdeklik a megfelelő szűrőket tartalmazó lekérdezés futtatásával. Kereshet a befejezett vagy futó feladatok között.
2. Válasszon ki egy feladatot a feladatok táblázatos listájából.
   
    ![Feladatok panelje](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. A lap alján kattintson a **részletek** elemre.
4. A **részletek** párbeszédpanelen megtekintheti az állapot, a részletek és az idő statisztikáit. Az alábbi ábrán egy példa látható a **biztonsági mentési feladatok részletei** párbeszédpanelre.
   
    ![Feladat részletei](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Sikertelen feladatok, ha a virtuális gép a hypervisorban szünetel
Ha egy feladat folyamatban van a StorSimple virtuális tömbben, és az eszköz (a hypervisorban kiépített virtuális gép) 15 percnél hosszabb ideig szünetel, a feladat meghiúsul. Ennek az az oka, hogy a StorSimple virtuális tömbje nem szinkronizál a Microsoft Azure idővel. 

A következő hibaüzenet jelenik meg: "az eszköz ideje nem szinkronizálható a Microsoft Azure idővel több mint 15 percen belül. Győződjön meg arról, hogy a hypervisor és az eszköz időpontját szinkronizálja egy NTP-kiszolgálóval. Ellenőrizze, hogy nincsenek-e kapcsolódási problémák. A kapcsolódási problémák elhárításához Futtasson diagnosztikai teszteket a virtuális eszköz helyi webes FELÜLETén. "

Ezek a hibák a biztonsági mentési, visszaállítási, frissítési és feladatátvételi feladatokra vonatkoznak. Ha a virtuális gép a Hyper-V-ben lett kiépítve, a gép végül szinkronizálja az időt a hypervisorral. Ha ez bekövetkezik, indítsa újra a feladatot.

## <a name="next-steps"></a>Következő lépések
[Megtudhatja, hogyan használhatja a helyi webes felhasználói felületet a StorSimple virtuális tömb felügyeletére](storsimple-ova-web-ui-admin.md).

