---
title: Feladatok megtekintése és kezelése a StorSimple 8000 sorozathoz | Microsoft Docs
description: Ismerteti a StorSimple Eszközkezelő szolgáltatási feladatok panelt, valamint azt, hogyan használható a legutóbbi, aktuális és ütemezett biztonsági mentési feladatok nyomon követésére.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dc630021cbf93074119d4feedcb3a09fae1453e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91949428"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Feladatok megtekintése és kezelése a StorSimple Eszközkezelő szolgáltatással (3. frissítés és újabb)

## <a name="overview"></a>Áttekintés
A **feladatok** panel egyetlen központi portált biztosít a StorSimple Eszközkezelő szolgáltatáshoz csatlakoztatott eszközökön elindított feladatok megtekintéséhez és kezeléséhez. Több eszköz esetében az ütemezett, futó, befejezett, megszakított és sikertelen feladatok is megtekinthetők. Az eredmények táblázatos formátumban jelennek meg.

![Feladatok panel](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Gyorsan megtalálhatja az Önt érdeklő feladatokat a következő mezők szűrésével:

* **Állapot** – a feladatok lehetnek folyamatban, sikeres, megszakított, sikertelen, megszakítási vagy sikeres hibákkal.
* **Időtartomány** – a feladatok a dátum-és időtartomány alapján szűrhetők. A tartományok az elmúlt 1 óra, az elmúlt 24 óra, az elmúlt 7 nap, az elmúlt 30 nap, az elmúlt év vagy az egyéni dátum.
* **Típus** – a feladat típusa lehet ütemezett biztonsági mentés, manuális biztonsági mentés, biztonsági másolat visszaállítása, kötet klónozása, mennyiségi tárolók feladatátvétele, helyileg rögzített kötet létrehozása, Kötet módosítása, frissítések telepítése, támogatási naplók összegyűjtése és felhőalapú berendezés létrehozása.
* **Eszközök** – a feladatok a szolgáltatáshoz csatlakoztatott bizonyos eszközökön kezdődnek.
  
A szűrt feladatok ezután a következő attribútumok alapján lesznek táblázatos formában:
  
* **Név** – ütemezett biztonsági mentés, manuális biztonsági mentés, biztonsági másolat visszaállítása, kötet klónozása, mennyiségi tárolók feladatátvétele, helyileg rögzített kötet létrehozása, Kötet módosítása, frissítések telepítése, támogatási naplók gyűjtése vagy felhőalapú berendezés létrehozása.
* **Állapot** – futó, befejezett, megszakított, sikertelen, megszakítás vagy hibákkal fejeződött be.
* **Entitás** – a feladatok egy kötethez, egy biztonsági mentési szabályzathoz vagy egy eszközhöz társíthatók. A klónozott feladatok például egy kötethez vannak társítva, míg az ütemezett biztonsági mentési feladatok egy biztonsági mentési szabályzathoz vannak társítva. A rendszer a vész-helyreállítási (DR) vagy visszaállítási művelet eredményeképpen létrehoz egy eszköz-feladatot.
* **Eszköz** – annak az eszköznek a neve, amelyen a feladatot elindították.
* **Elindítva** – a feladatok elindításának időpontja.
* **Időtartam** – a feladatok végrehajtásához szükséges idő.

A feladatok listája 30 másodpercenként frissül.

Ezen az oldalon a következő feladattal kapcsolatos műveletek végezhetők el:

* Feladatok részleteinek megjelenítése
* Feladat megszakítása

## <a name="view-job-details"></a>Feladatok részleteinek megjelenítése
A feladatok részleteinek megtekintéséhez hajtsa végre a következő lépéseket.

#### <a name="to-view-job-details"></a>A feladatok részleteinek megtekintése
1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, majd kattintson a **feladatok** lehetőségre.

2. A **feladatok** panelen jelenítse meg azokat a feladatokat, amelyek érdeklik a megfelelő szűrőket tartalmazó lekérdezés futtatásával. Kereshet a befejezett, a futó és a megszakított feladatok között.

    ![Feladatok panel 2](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Válasszon ki egy feladatot, és kattintson rá.

    ![3. feladatok](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. A feladatok részletei panelen megtekintheti az állapot, a részletek, az idő statisztikáit és az adatok statisztikáit.
   
    ![Feladat részletei](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Feladat megszakítása
Egy futó feladat megszakításához hajtsa végre az alábbi lépéseket.

> [!NOTE]
> Bizonyos feladatok, például a kötetek módosítása a kötet típusának módosításához vagy a kötetek bővítéséhez nem lehet megszakítani.


### <a name="to-cancel-a-job"></a>Feladat megszakítása
1. A **feladatok** lapon jelenítse meg a megszakítani kívánt futó feladatokat a megfelelő szűrőket tartalmazó lekérdezés futtatásával. Válassza ki a feladatot.

2. Kattintson a jobb gombbal a kiválasztott feladatokra a helyi menü meghívásához, majd kattintson a **Mégse** gombra.

    ![A feladatok részletei 2](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. Ez a feladat most meg lett szakítva.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [kezelheti a StorSimple biztonsági mentési szabályzatait](storsimple-8000-manage-backup-policies-u2.md).
* Ismerje meg, hogyan kezelheti a [StorSimple-eszközt a StorSimple Eszközkezelő szolgáltatással](storsimple-8000-manager-service-administration.md).

