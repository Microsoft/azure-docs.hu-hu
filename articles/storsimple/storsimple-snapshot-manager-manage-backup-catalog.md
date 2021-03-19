---
title: StorSimple Snapshot Manager Backup katalógus | Microsoft Docs
description: Ismerteti, hogyan használható a StorSimple Snapshot Manager MMC beépülő modul a biztonsági mentési katalógus megtekintéséhez és kezeléséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 2efc35e65ca1db2b5241e1d3b2798e068880c87e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90054993"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>A StorSimple Snapshot Manager használata a biztonsági mentési katalógus kezeléséhez

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager elsődleges funkciója, hogy lehetővé teszi, hogy az alkalmazással konzisztens biztonsági másolatokat hozzon létre a StorSimple-kötetekről Pillanatképek formájában. A pillanatképek ezután egy *biztonsági mentési katalógus* nevű XML-fájlban vannak felsorolva. A biztonságimásolat-katalógus a pillanatképeket mennyiségi csoport, majd helyi pillanatkép vagy Felhőbeli pillanatkép szerint rendezi.

Ez az oktatóanyag azt ismerteti, hogyan használható a **Backup Catalog** csomópont a következő feladatok elvégzéséhez:

* Kötet visszaállítása
* Kötet vagy számítógépcsoport klónozása
* Biztonsági másolat törlése
* Fájl helyreállítása
* A Storsimple Snapshot Manager adatbázisának visszaállítása

A biztonsági mentési katalógus megtekintéséhez bontsa ki a **biztonsági mentési katalógus** csomópontot a **hatókör** ablaktáblán, majd bontsa ki a kötet csoportot.

* Ha a kötet csoport nevére kattint, az **eredmények** ablaktáblán látható a kötet csoport számára elérhető helyi Pillanatképek és a Felhőbeli Pillanatképek száma. 
* Ha a **helyi pillanatkép** vagy a **Felhőbeli pillanatkép** lehetőségre kattint, az **eredmények** ablaktábla az alábbi információkat jeleníti meg az egyes biztonsági mentési pillanatképekről (a **nézet** beállításaitól függően):
  
  * **Name (név** ) – a pillanatkép készítésének időpontja.
  * **Típus** – az, hogy ez egy helyi pillanatkép vagy egy Felhőbeli pillanatkép.
  * **Tulajdonos** – a tartalom tulajdonosa. 
  * **Elérhető** – azt határozza meg, hogy a pillanatkép jelenleg elérhető-e. Az **igaz** érték azt jelzi, hogy a pillanatkép elérhető, és visszaállítható; A **false (hamis** ) érték azt jelzi, hogy a pillanatkép már nem érhető el. 
  * **Importálva** – azt, hogy a biztonsági mentés importálása megtörtént-e. Az **igaz** érték azt jelzi, hogy a biztonsági mentés a StorSimple Eszközkezelő szolgáltatásból lett importálva, amikor az eszköz konfigurálva volt a StorSimple Snapshot Manager; A **false (hamis** ) érték azt jelzi, hogy nem lett importálva, de a StorSimple Snapshot Manager hozta létre. (Az importált kötetek egyszerűen azonosíthatók, mert a rendszer olyan utótagot ad hozzá, amely azonosítja azt az eszközt, amelyből a kötetet importálta.)
    
    ![Biztonsági mentési katalógus](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Ha kibontja a **helyi pillanatkép** vagy a **Felhőbeli pillanatkép** elemet, majd egy egyéni pillanatkép nevére kattint, az **eredmények** ablaktáblán az alábbi információk láthatók a kiválasztott pillanatképről:
  
  * **Name (név** ) – a kötet a meghajtóbetűjel alapján azonosítva. 
  * **Helyi név** – a meghajtó helyi neve (ha elérhető). 
  * **Eszköz** – annak az eszköznek a neve, amelyen a kötet található. 
  * **Elérhető** – azt határozza meg, hogy a pillanatkép jelenleg elérhető-e. Az **igaz** érték azt jelzi, hogy a pillanatkép elérhető, és visszaállítható; A **false (hamis** ) érték azt jelzi, hogy a pillanatkép már nem érhető el. 

## <a name="restore-a-volume"></a>Kötet visszaállítása
A kötet biztonsági másolatból történő visszaállításához kövesse az alábbi eljárást.

#### <a name="prerequisites"></a>Előfeltételek
Ha még nem tette meg, hozzon létre egy kötetet és egy kötet csoportot, majd törölje a kötetet. Alapértelmezés szerint a StorSimple Snapshot Manager biztonsági másolatot készít egy kötetről, mielőtt engedélyezi a törlését. Ez az elővigyázatosság megakadályozza az adatvesztést, ha a kötetet véletlenül törlik, vagy ha az adatmennyiséget valamilyen okból vissza kell állítani. 

A StorSimple Snapshot Manager a következő üzenetet jeleníti meg, miközben létrehozza az elővigyázatosságból származó biztonsági mentést.

![Automatikus pillanatkép-üzenet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Mennyiségi csoport részét képező kötet nem törölhető. A törlési lehetőség nem érhető el. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Kötet visszaállítása
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára. 
2. A **hatókör** ablaktáblán bontsa ki a **biztonsági mentési katalógus** csomópontot, bontsa ki a kötet csoportot, majd kattintson a **helyi Pillanatképek** vagy a **Felhőbeli Pillanatképek** lehetőségre. A biztonsági mentési Pillanatképek listája megjelenik az **eredmények** ablaktábláján.
3. Keresse meg a visszaállítani kívánt biztonsági másolatot, kattintson a jobb gombbal, majd kattintson a **visszaállítás** elemre.
   
    ![Biztonsági mentési katalógus visszaállítása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. A jóváhagyás lapon tekintse át a részleteket, írja be a **Confirm** értéket, majd kattintson az **OK** gombra. A StorSimple Snapshot Manager a biztonsági mentés használatával állítja vissza a kötetet.
   
    ![Visszaigazoló üzenet visszaállítása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. A visszaállítási műveletet a Futtatás közben is figyelheti. A **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, majd kattintson a **Futtatás** elemre. A feladatok részletei az **eredmények** ablaktábláján jelennek meg. A visszaállítási feladatok befejezése után a rendszer átviszi a feladatok részleteit az **elmúlt 24 óra** listára.

## <a name="clone-a-volume-or-volume-group"></a>Kötet vagy számítógépcsoport klónozása
Az alábbi eljárással hozhat létre duplikált (klónt) kötet-vagy kötet-csoportból.

#### <a name="to-clone-a-volume-or-volume-group"></a>Kötet vagy mennyiségi csoport klónozása
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán bontsa ki a **biztonsági mentési katalógus** csomópontot, bontsa ki a kötet csoportot, majd kattintson a **Felhőbeli Pillanatképek** elemre. A biztonsági másolatok listája megjelenik az **eredmények** ablaktábláján.
3. Keresse meg a klónozott kötetet vagy kötetet, kattintson a jobb gombbal a kötet vagy a kötet csoport nevére, majd kattintson a **klónozás** elemre. Megjelenik a **klónozott felhő pillanatképének** párbeszédpanele.
   
    ![Felhőbeli pillanatkép klónozása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. A következő módon hajtsa végre a **klónozási felhő pillanatképének** párbeszédpanelét: 
   
   1. A **név** szövegmezőbe írja be a klónozott kötet nevét. Ez a név jelenik meg a **kötetek** csomópontban. 
   2. (Nem kötelező) válassza a **meghajtó** lehetőséget, majd válasszon egy meghajtóbetűjelet a legördülő listából.
   3. (Nem kötelező) válassza a **mappa (NTFS)** lehetőséget, és írja be a mappa elérési útját, vagy kattintson a Tallózás gombra, és válasszon egy helyet a mappához. 
   4. Kattintson a **Létrehozás** lehetőségre.
5. Ha a klónozási folyamat elkészült, inicializálnia kell a klónozott kötetet. Indítsa el a Kiszolgálókezelő alkalmazást, majd indítsa el a Lemezkezelés segédprogramot. Részletes útmutatást a [kötetek csatlakoztatása](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)című témakörben talál. Az inicializálás után a kötet a **hatókör** ablaktábla **kötetek** csomópontjában jelenik meg. Ha nem látja a felsorolt kötetet, frissítse a kötetek listáját (kattintson a jobb gombbal a **kötetek** csomópontra, majd kattintson a **frissítés** parancsra).

## <a name="delete-a-backup"></a>Biztonsági másolat törlése
A következő eljárással törölhet egy pillanatképet a biztonsági mentési katalógusból. 

> [!NOTE]
> A pillanatképek törlése törli a pillanatképhez társított biztonsági másolati adatok mennyiségét. A felhőben tárolt adatok törlésének folyamata azonban hosszabb időt is igénybe vehet.<br>


#### <a name="to-delete-a-backup"></a>Biztonsági másolat törlése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán bontsa ki a **biztonsági mentési katalógus** csomópontot, bontsa ki a kötet csoportot, majd kattintson a **helyi Pillanatképek** vagy a **Felhőbeli Pillanatképek** lehetőségre. A pillanatképek listája megjelenik az **eredmények** ablaktábláján.
3. Kattintson a jobb gombbal a törölni kívánt pillanatképre, majd kattintson a **Törlés** parancsra.
4. Amikor megjelenik a megerősítő üzenet, kattintson **az OK** gombra.

## <a name="recover-a-file"></a>Fájl helyreállítása
Ha egy fájl véletlenül törlődik egy kötetről, a fájlt helyreállíthatja egy olyan pillanatkép beolvasásával, amely a törlést előre törli, a pillanatkép használatával létrehozhatja a kötet klónját, majd átmásolhatja a fájlt a klónozott kötetről az eredeti kötetre.

#### <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, ellenőrizze, hogy van-e aktuális biztonsági másolata a kötet csoportról. Ezután törölje az adott kötet egyik kötetén tárolt fájlt. Végül a következő lépésekkel állíthatja vissza a törölt fájlt a biztonsági másolatból. 

#### <a name="to-recover-a-deleted-file"></a>Törölt fájl helyreállítása
1. Kattintson a StorSimple Snapshot Manager ikonjára az asztalon. Megjelenik a StorSimple Snapshot Manager konzol ablak. 
2. A **hatókör** ablaktáblán bontsa ki a **biztonságimásolat-katalógus** csomópontot, és tallózással keresse meg a törölt fájlt tartalmazó pillanatképet. Általában olyan pillanatképet kell kiválasztania, amely közvetlenül a törlés előtt jött létre.
3. Keresse meg a klónozott kötetet, kattintson rá a jobb gombbal, majd kattintson a **klónozás** elemre. Megjelenik a **klónozott felhő pillanatképének** párbeszédpanele.
   
    ![Felhőbeli pillanatkép klónozása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. A következő módon hajtsa végre a **klónozási felhő pillanatképének** párbeszédpanelét: 
   
   1. A **név** szövegmezőbe írja be a klónozott kötet nevét. Ez a név jelenik meg a **kötetek** csomópontban. 
   2. Választható Válassza a **meghajtó** lehetőséget, majd válasszon egy meghajtóbetűjelet a legördülő listából. 
   3. Választható Válassza a **mappa (NTFS)** lehetőséget, és írja be a mappa elérési útját, vagy kattintson a **Tallózás** gombra, és válassza ki a mappa helyét. 
   4. Kattintson a **Létrehozás** lehetőségre. 
5. Ha a klónozási folyamat elkészült, inicializálnia kell a klónozott kötetet. Indítsa el a Kiszolgálókezelő alkalmazást, majd indítsa el a Lemezkezelés segédprogramot. Részletes útmutatást a [kötetek csatlakoztatása](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)című témakörben talál. Az inicializálás után a kötet a **hatókör** ablaktábla **kötetek** csomópontjában jelenik meg. 
   
    Ha nem látja a felsorolt kötetet, frissítse a kötetek listáját (kattintson a jobb gombbal a **kötetek** csomópontra, majd kattintson a **frissítés** parancsra).
6. Nyissa meg a klónozott kötetet tartalmazó NTFS mappát, bontsa ki a **kötetek** csomópontot, majd nyissa meg a klónozott kötetet. Keresse meg a helyreállítani kívánt fájlt, és másolja az elsődleges kötetre.
7. A fájl visszaállítása után törölheti a klónozott kötetet tartalmazó NTFS-mappát.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>A StorSimple Snapshot Manager adatbázisának visszaállítása
Rendszeresen készítsen biztonsági másolatot a StorSimple Snapshot Manager adatbázisáról a gazdaszámítógépen. Ha vészhelyzet történik, vagy a gazdaszámítógép valamilyen okból meghiúsul, visszaállíthatja a biztonsági mentésből. Az adatbázis biztonsági mentésének létrehozása manuális folyamat.

#### <a name="to-back-up-and-restore-the-database"></a>Az adatbázis biztonsági mentése és visszaállítása
1. Állítsa le a Microsoft StorSimple-kezelő szolgáltatást:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópult **eszközök** menüjében válassza a **szolgáltatások** lehetőséget.
   3. A **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple-kezelő szolgáltatást**.
   4. A jobb oldali ablaktábla **Microsoft StorSimple-kezelő szolgáltatása** területén kattintson **a szolgáltatás leállítása** elemre.
2. A gazdagépen keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > A ProgramData mappa rejtett.
   > 
   > 
3. Keresse meg a katalógus XML-fájlját, másolja a fájlt, és tárolja a másolatot egy biztonságos helyen vagy a felhőben. Ha a gazdagép meghibásodik, a biztonságimásolat-fájl segítségével helyreállíthatja a StorSimple Snapshot Managerban létrehozott biztonsági mentési házirendeket.
   
    ![Azure StorSimple biztonságimásolat-katalógus fájlja](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Indítsa újra a Microsoft StorSimple-kezelő szolgáltatást: 
   
   1. A Kiszolgálókezelő irányítópult **eszközök** menüjében válassza a **szolgáltatások** lehetőséget.
   2. A **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple-kezelő szolgáltatást**.
   3. A jobb oldali ablaktábla **Microsoft StorSimple-kezelő szolgáltatása** területén kattintson **a szolgáltatás újraindítása** elemre.
5. A gazdagépen keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Törölje a katalógus XML-fájlját, és cserélje le a létrehozott biztonsági mentési verzióra. 
7. A StorSimple Snapshot Manager elindításához kattintson az asztali StorSimple Snapshot Manager ikonra. 

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple Snapshot Manager használatáról a StorSimple-megoldás felügyeletéhez](storsimple-snapshot-manager-admin.md).
* További információ a [StorSimple Snapshot Manager feladatokról és munkafolyamatokról](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

