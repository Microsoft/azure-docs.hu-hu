---
title: StorSimple Snapshot Manager biztonsági mentési feladatok | Microsoft Docs
description: Ismerteti, hogyan használható a StorSimple Snapshot Manager MMC beépülő modul az ütemezett, jelenleg futó és befejezett biztonsági mentési feladatok megtekintéséhez és kezeléséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 3c26a84e32a17cba83b5ca895f146e561072fa62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998192"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>A StorSimple Snapshot Manager használata a biztonsági mentési feladatok megtekintéséhez és kezeléséhez

## <a name="overview"></a>Áttekintés
A **hatókör** ablaktáblán a **feladatok** csomópont megjeleníti az **ütemezett**, az **elmúlt 24 órában**, **valamint az** interaktív módon vagy egy konfigurált szabályzat által kezdeményezett biztonsági mentési feladatokat. 

Ez az oktatóanyag azt ismerteti, hogyan használható a **feladatok** csomópont az ütemezett, legutóbbi és jelenleg futó biztonsági mentési feladatok adatainak megjelenítéséhez. (A feladatok listája és a kapcsolódó információk az **eredmények** ablaktábláján jelennek meg.) Emellett a jobb gombbal a felsorolt feladatokra kattintva megtekintheti az elérhető műveleteket felsoroló helyi menüt.

## <a name="view-scheduled-jobs"></a>Ütemezett feladatok megtekintése
Az ütemezett biztonsági mentési feladatok megtekintéséhez kövesse az alábbi eljárást.

#### <a name="to-view-scheduled-jobs"></a>Ütemezett feladatok megtekintése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára. 
2. A **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, majd kattintson az **ütemezett** elemre. Az **eredményeket** tartalmazó ablaktáblán az alábbi információk jelennek meg:
   
   * **Name (név** ) – az ütemezett pillanatkép neve
   * **Következő Futtatás** – a következő ütemezett pillanatkép dátuma és időpontja
   * **Utolsó Futtatás** – a legutóbbi ütemezett pillanatkép dátuma és időpontja
     
     > [!NOTE]
     > Csak egyszeri Pillanatképek esetén a **következő Futtatás** és **utolsó Futtatás** ugyanaz lesz.
     
     ![Ütemezett biztonsági mentési feladatok](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Egy adott feladathoz tartozó további műveletek elvégzéséhez kattintson a jobb gombbal a feladatra a **találatok** ablaktáblán, és válassza a menüpontot.

## <a name="view-recent-jobs"></a>Legutóbbi feladatok megtekintése
Az alábbi eljárással megtekintheti az elmúlt 24 órában elvégzett biztonsági mentési és visszaállítási feladatokat.

#### <a name="to-view-recent-jobs"></a>A legutóbbi feladatok megtekintése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, majd kattintson az **elmúlt 24 óra** elemre. Az **eredmények** ablaktábla az elmúlt 24 órában (legfeljebb 64 feladat) jeleníti meg a biztonsági mentési feladatokat. Az alábbi információk az **eredményeket** tartalmazó ablaktáblán jelennek meg a megadott **megtekintési** beállításoktól függően:
   
   * **Name (név** ) – az ütemezett pillanatkép neve.
   * Elindítva – a pillanatkép **megkezdésének** dátuma és időpontja.
   * **Leállítva** – a pillanatkép befejezésének és befejezésének dátuma és időpontja.
   * **Eltelt** – az **elindított** és **leállított** időpontok közötti idő.
   * **Status (állapot** ) – a legutóbb Befejezett feladatok állapota. A **siker** azt jelzi, hogy a biztonsági mentés sikeresen létrejött. A **sikertelenség** azt jelzi, hogy a feladatot nem sikerült futtatni.
   * **Információ** – a hiba oka.
   * **Feldolgozott bájtok (MB)** – a feldolgozott kötet (MBS) adatainak mennyisége. 
     
     ![Az elmúlt 24 órában futtatott feladatok](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Egy adott feladathoz tartozó további műveletek elvégzéséhez kattintson a jobb gombbal a feladatra a **találatok** ablaktáblán, és válassza a menüpontot.
   
    ![Feladat törlése](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Jelenleg futó feladatok megtekintése
A jelenleg futó feladatok megtekintéséhez kövesse az alábbi eljárást.

#### <a name="to-view-currently-running-jobs"></a>Aktuálisan futó feladatok megtekintése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, majd kattintson a **Futtatás** elemre. A megadott **megtekintési** beállításoktól függően az alábbi információk jelennek meg az **eredmények** ablaktáblájában:
   
   * **Name (név** ) – az ütemezett pillanatkép neve.
   * Elindítva – a pillanatkép **megkezdésének** dátuma és időpontja.
   * **Ellenőrzőpont** – a biztonsági mentés aktuális művelete.
   * **Status (állapot** ) – a Befejezés százalékos értéke.
   * **Eltelt** – a biztonsági mentés óta eltelt idő mennyisége. 
   * **Átlagos átviteli sebesség (MB)** – a feldolgozott adatok összesített bájtjainak és a feldolgozáshoz szükséges teljes idő (MBS) aránya.
   * **Feldolgozott bájtok (MB)** – a feldolgozott adatmennyiség teljes mérete (MB).
   * **Írt bájtok (MB)** – az összes írt adat (MBS). Magában foglalja az adatokat és a metaadatokat is, ezért általában nagyobb, mint a feldolgozott bájtok.
     
     ![Jelenleg futó feladatok](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Egy adott feladathoz tartozó további műveletek elvégzéséhez kattintson a jobb gombbal a feladatra a **találatok** ablaktáblán, és válassza a menüpontot.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan kezelheti [a StorSimple-megoldást a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogyan [kezelheti a biztonsági mentési katalógust a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-manage-backup-catalog.md).

