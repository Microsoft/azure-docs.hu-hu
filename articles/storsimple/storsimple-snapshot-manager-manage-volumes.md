---
title: StorSimple Snapshot Manager és kötetek | Microsoft Docs
description: Ismerteti, hogyan használható a StorSimple Snapshot Manager MMC beépülő modul a kötetek megtekintéséhez és kezeléséhez, valamint a biztonsági másolatok konfigurálásához.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 309fa85d0a4d877522a89dd8f1e6e71fb2074744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022938"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Kötetek megtekintése és kezelése StorSimple Snapshot Manager használatával
## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager **kötetek** csomópontot (a **hatókör** ablaktáblán) a kötetek kiválasztásához és a rájuk vonatkozó információk megtekintéséhez használhatja. A kötetek olyan meghajtókként jelennek meg, amelyek megfelelnek a gazdagép által csatlakoztatott köteteknek. A **kötetek** csomópont a StorSimple által támogatott helyi köteteket és köteteket jeleníti meg, beleértve az iSCSI és az eszköz használatával felderített köteteket is. 

További információ a támogatott kötetekről: [több kötet típusának támogatása](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Kötetek listája az eredmények ablaktáblájában](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

A **kötetek** csomópont lehetővé teszi a kötetek újbóli vizsgálatát vagy törlését a StorSimple Snapshot Manager felfedése után. 

Ez az oktatóanyag azt ismerteti, hogyan csatlakoztathatja, inicializálhatja és formázhatja a köteteket, majd a StorSimple-Snapshot Manager használatával a következőket végezheti el:

* Kötetek adatainak megtekintése 
* Kötetek törlése
* Kötetek újraellenőrzése 
* Alapszintű kötet konfigurálása és biztonsági mentése
* Dinamikus tükrözött kötet konfigurálása és biztonsági mentése

> [!NOTE]
> A **műveletek** ablaktáblán a **mennyiségi** csomópont összes művelete is elérhető.
> 
> 

## <a name="mount-volumes"></a>Kötetek csatlakoztatása
A StorSimple-kötetek csatlakoztatásához, inicializálásához és formázásához kövesse az alábbi eljárást. Ez az eljárás a Lemezkezelés eszközt, a merevlemezek felügyeletére szolgáló rendszersegédprogramot, valamint a megfelelő köteteket vagy partíciókat használja. A Lemezkezelés szolgáltatással kapcsolatos további információkért nyissa meg a [Lemezkezelés](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) szolgáltatást a Microsoft TechNet webhelyén.

#### <a name="to-mount-volumes"></a>Kötetek csatlakoztatása
1. Indítsa el a Microsoft iSCSI-kezdeményezőt a gazdagépen.
2. Adja meg a csatoló IP-címeinek egyikét a célként szolgáló portál vagy a felderítési IP-cím mezőben, és kapcsolódjon az eszközhöz. Az eszköz csatlakoztatása után a kötetek elérhetők lesznek a Windows rendszer számára. A Microsoft iSCSI-kezdeményező használatával kapcsolatos további információkért nyissa meg a [Microsoft iSCSI-kezdeményező telepítése és konfigurálása][1]című témakör "csatlakozás iSCSI-tárolóhoz" című szakaszát.
3. A Lemezkezelés elindításához használja a következő lehetőségek egyikét:
   
   * A **Futtatás** mezőbe írja be a diskmgmt. msc parancsot.
   * Indítsa el a Kiszolgálókezelő eszközt, bontsa ki a **tárterület** csomópontot, majd válassza a **Lemezkezelés** elemet.
   * Indítsa el a **felügyeleti eszközöket**, bontsa ki a **Számítógép-kezelés** csomópontot, majd kattintson a **Lemezkezelés** elemre. 
     
     > [!NOTE]
     > A Lemezkezelés futtatásához rendszergazdai jogosultságokat kell használnia.
     > 
     > 
4. A kötet (ek) online állapotba hozatala:
   
   1. A Lemezkezelés alkalmazásban kattintson a jobb gombbal az **Offline állapotú** kötetre.
   2. Kattintson a **lemez újraaktiválása** elemre. A lemezt a lemez újraaktiválása után **online** jelöléssel kell megjelölni.
5. A kötet (ek) inicializálása:
   
   1. Kattintson a jobb gombbal a felderített kötetekre.
   2. A menüben válassza a **lemez inicializálása** elemet.
   3. A **lemez inicializálása** párbeszédpanelen jelölje ki az inicializálni kívánt lemezeket, majd kattintson **az OK** gombra.
6. Egyszerű kötetek formázása:
   
   1. Kattintson a jobb gombbal a formázni kívánt kötetre.
   2. A menüben válassza az **új egyszerű kötet** lehetőséget.
   3. A kötet formázásához használja az új egyszerű kötet varázslót:
      
      * A kötet méretének megadása
      * Adja meg a meghajtóbetűjelet.
      * Válassza ki az NTFS fájlrendszert.
      * Adjon meg 64 KB-os lemezfoglalásiegység-méretet.
      * Hajtson végre egy gyorsformázást.
7. Több partíciós kötetek formázása. Útmutatásért lásd: a [Lemezkezelés eszköz](/previous-versions/tn-archive/dd163556(v=technet.10))"partíciók és kötetek" szakasza.

## <a name="view-information-about-your-volumes"></a>A kötetek adatainak megtekintése
Az alábbi eljárással tekintheti meg a helyi és az Azure-beli StorSimple-kötetekkel kapcsolatos információkat.

#### <a name="to-view-volume-information"></a>A kötet adatainak megtekintése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára. 
2. A **hatókör** ablaktáblán kattintson a **kötetek** csomópontra. A helyi és csatlakoztatott kötetek listája, beleértve az összes Azure StorSimple-kötetet, megjelenik az **eredmények** ablaktábláján. Az **eredmények** ablaktáblájában lévő oszlopok konfigurálhatók. (Kattintson a jobb gombbal a **kötetek** csomópontra, válassza a **nézet**, majd az **Oszlopok hozzáadása/eltávolítása** lehetőséget.)
   
    ![Az oszlopok konfigurálása](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Eredmények oszlop | Leírás |
   |:--- |:--- |
   |  Név |A **Name (név** ) oszlop tartalmazza az egyes felderített kötetekhez rendelt meghajtóbetűjelet. |
   |  Eszköz |Az **eszköz** oszlop tartalmazza a gazdaszámítógéphez csatlakoztatott eszköz IP-címét. |
   |  Eszköz kötetének neve |Az **eszköz kötet neve** oszlop tartalmazza annak az eszköznek a nevét, amelyhez a kiválasztott kötet tartozik. Ez az adott kötethez tartozó Azure Portalban definiált kötet neve. |
   |  Elérési utak |A **hozzáférési útvonalak** oszlop a kötet elérési útját jeleníti meg. Ez az a meghajtóbetűjel vagy csatlakoztatási pont, amelyen a kötet elérhető a gazdaszámítógépen. |

## <a name="delete-a-volume"></a>Kötet törlése
Az alábbi eljárással törölhet köteteket a StorSimple Snapshot Manager.

> [!NOTE]
> Nem törölhet kötetet, ha az egy kötet csoport részét képezi. (A törlési beállítás nem érhető el a mennyiségi csoport tagjaihoz tartozó kötetek esetében.) A kötet törléséhez törölnie kell a teljes kötet csoportot.

#### <a name="to-delete-a-volume"></a>Kötet törlése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a **kötetek** csomópontra. 
3. Az **eredmények** ablaktáblájában kattintson a jobb gombbal a törölni kívánt kötetre.
4. A menüben kattintson a **Törlés** elemre. 
   
    ![Kötet törlése](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Megjelenik a **kötet törlése** párbeszédpanel. Írja be a **megerősítés** szöveget a szövegmezőbe, majd kattintson **az OK** gombra.
6. Alapértelmezés szerint a StorSimple Snapshot Manager biztonsági másolatot készít egy kötetről a törlés előtt. Ezzel az elővigyázatossággal megakadályozhatja az adatvesztést, ha a törlés nem volt szándékos. A StorSimple Snapshot Manager egy **automatikus pillanatkép** -folyamatjelző üzenetet jelenít meg, miközben biztonsági másolatot készít a kötetről. 
   
    ![Automatikus pillanatkép-üzenet](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Kötetek újraellenőrzése
A következő eljárással ellenőrizheti újra a StorSimple-Snapshot Managerhoz csatlakoztatott köteteket.

#### <a name="to-rescan-the-volumes"></a>A kötetek újbóli ellenőrzése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a jobb gombbal a **kötetek** elemre, majd kattintson a **kötetek újravizsgálása** elemre.
   
    ![Kötetek újraellenőrzése](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Ez az eljárás a StorSimple Snapshot Managerával szinkronizálja a kötetek listáját. Az eredményekben megjelennek a változások, például az új kötetek vagy a törölt kötetek.

## <a name="configure-and-back-up-a-basic-volume"></a>Alapszintű kötet konfigurálása és biztonsági mentése
Az alábbi eljárással konfigurálhatja az alapszintű kötetek biztonsági mentését, majd azonnal elindíthatja a biztonsági mentést, vagy létrehozhat egy szabályzatot az ütemezett biztonsági mentésekhez.

### <a name="prerequisites"></a>Előfeltételek
Előkészületek:

* Győződjön meg arról, hogy a StorSimple eszköz és a gazdaszámítógép megfelelően van konfigurálva. További információért látogasson el a helyszíni [StorSimple-eszköz üzembe helyezése](./storsimple-8000-deployment-walkthrough-u2.md)című témakörre.
* Telepítse és konfigurálja a StorSimple Snapshot Manager. További információért látogasson el a [StorSimple Snapshot Manager üzembe helyezése](storsimple-snapshot-manager-deployment.md)című témakörre.

#### <a name="to-configure-backup-of-a-basic-volume"></a>Alapszintű kötet biztonsági mentésének konfigurálása
1. Hozzon létre egy alapszintű kötetet a StorSimple-eszközön.
2. Csatlakoztassa, inicializálja és formázza a kötetet a [csatlakoztatási kötetek](#mount-volumes)című cikkben leírtak szerint. 
3. Kattintson a StorSimple Snapshot Manager ikonjára az asztalon. Megjelenik a StorSimple Snapshot Manager ablak. 
4. A **hatókör** ablaktáblán kattintson a jobb gombbal a **kötetek** csomópontra, majd válassza a **kötetek újraellenőrzése** elemet. A vizsgálat befejezésekor a kötetek listájának szerepelnie kell az **eredmények** ablaktábláján. 
5. Az **eredmények** ablaktáblájában kattintson a jobb gombbal a kötetre, majd válassza a **kötet létrehozása** elemet. 
   
    ![Kötet csoport létrehozása](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. A **kötet létrehozása** párbeszédpanelen adja meg a kötet csoport nevét, a kötetek hozzárendelését, majd kattintson **az OK** gombra.
7. A **hatókör** ablaktáblán bontsa ki a **kötetek csoportjai** csomópontot. Az új kötet csoportnak a **mennyiségi csoportok** csomópont alatt kell megjelennie. 
8. Kattintson a jobb gombbal a kötet csoport nevére.
   
   * Az interaktív (igény szerinti) biztonsági mentési feladatok elindításához kattintson a **biztonsági mentés** készítése gombra. 
   * Ha automatikus biztonsági mentést szeretne ütemezni, kattintson a **biztonsági mentési szabályzat létrehozása** elemre. Az **általános** lapon válasszon ki egy kötet csoportot a listából. Az **ütemterv** lapon adja meg az ütemterv részleteit. Amikor végzett, kattintson az **OK** gombra. 
9. Annak ellenőrzéséhez, hogy elindult-e a biztonsági mentési feladat, bontsa ki a **hatókör** ablaktáblán a **feladatok** csomópontot, majd kattintson a **futó** csomópontra. Az aktuálisan futó feladatok listája megjelenik az **eredmények** ablaktábláján. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Dinamikus tükrözött kötet konfigurálása és biztonsági mentése
A dinamikus tükrözött kötetek biztonsági mentésének konfigurálásához hajtsa végre a következő lépéseket:

* 1. lépés: dinamikus tükrözött kötet létrehozásához használja a Lemezkezelés programot. 
* 2. lépés: a biztonsági mentés konfigurálásához használja a StorSimple Snapshot Manager.

### <a name="prerequisites"></a>Előfeltételek
Előkészületek:

* Győződjön meg arról, hogy a StorSimple eszköz és a gazdaszámítógép megfelelően van konfigurálva. További információért látogasson el a helyszíni [StorSimple-eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md)című témakörre.
* Telepítse és konfigurálja a StorSimple Snapshot Manager. További információért látogasson el a [StorSimple Snapshot Manager üzembe helyezése](storsimple-snapshot-manager-deployment.md)című témakörre.
* Két kötet konfigurálása a StorSimple-eszközön. (A példákban a rendelkezésre álló kötetek az **1** . és a **2. lemez**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>1. lépés: dinamikus tükrözött kötet létrehozásához a Lemezkezelés használatával
A Lemezkezelés a merevlemezek felügyeletére szolgáló rendszersegédprogram, valamint a bennük található kötetek vagy partíciók. A Lemezkezelés szolgáltatással kapcsolatos további információkért nyissa meg a [Lemezkezelés](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) szolgáltatást a Microsoft TechNet webhelyén.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Dinamikus tükrözött kötet létrehozása
1. A Lemezkezelés elindításához használja a következő lehetőségek egyikét: 
   
   * Nyissa meg a **Futtatás** mezőt, írja be a **diskmgmt. msc** parancsot, majd nyomja le az ENTER billentyűt.
   * Indítsa el a Kiszolgálókezelő eszközt, bontsa ki a **tárterület** csomópontot, majd válassza a **Lemezkezelés** elemet. 
   * Indítsa el a **felügyeleti eszközöket**, bontsa ki a **Számítógép-kezelés** csomópontot, majd kattintson a **Lemezkezelés** elemre. 
2. Győződjön meg arról, hogy a StorSimple eszközön két kötet áll rendelkezésre. (A példában a rendelkezésre álló kötetek az **1** . és a **2. lemez**.) 
3. A Lemezkezelés ablak alsó ablaktáblájának jobb oldali oszlopában kattintson a jobb gombbal az **1. lemez** elemre, és válassza az **Új tükrözött kötet** lehetőséget. 
   
    ![Új tükrözött kötet](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Az **Új tükrözött kötet** varázsló lapon kattintson a **tovább** gombra.
5. A **lemezek kiválasztása** lapon válassza ki a **2. lemez** elemet a **kiválasztott** ablaktáblán, kattintson a **Hozzáadás**, majd a **tovább** gombra. 
6. A **meghajtóbetűjel vagy elérési út kiosztása** lapon fogadja el az alapértelmezett értékeket, majd kattintson a **tovább** gombra. 
7. A **kötet formázása** lap **foglalási egység mérete** mezőjében válassza a **64 kb**-ot. Jelölje be a **gyors formátum elvégzése** jelölőnégyzetet, majd kattintson a **tovább** gombra. 
8. Az **Új tükrözött kötet befejezése** lapon tekintse át a beállításokat, majd kattintson a **Befejezés** gombra. 
9. Megjelenik egy üzenet, amely jelzi, hogy az alaplemez dinamikus lemezzé lesz konvertálva. Kattintson a **Yes** (Igen) gombra.
   
    ![Dinamikus lemez konverziójának üzenete](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. A Lemezkezelés alkalmazásban ellenőrizze, hogy az 1. és a 2. lemez dinamikus tükrözött kötetekként jelenik-e meg. (A **dinamikus** értéknek szerepelnie kell az Állapot oszlopban, és a kapacitási sáv színének pirosra kell váltania, amely tükrözött kötetet jelez.) 
    
    ![Lemezes kezelés tükrözött dinamikus lemezek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>2. lépés: a StorSimple Snapshot Manager használata a biztonsági mentés konfigurálásához
A következő eljárással konfigurálhatja a dinamikus tükrözött köteteket, majd a biztonsági mentést azonnal elindíthatja, vagy létrehozhat egy szabályzatot az ütemezett biztonsági mentésekhez.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Dinamikus tükrözött kötet biztonsági másolatának konfigurálása
1. Kattintson a StorSimple Snapshot Manager ikonjára az asztalon. Megjelenik a StorSimple Snapshot Manager ablak. 
2. A **hatókör** ablaktáblán kattintson a jobb gombbal a **kötetek** csomópontra, és válassza a **kötetek újraellenőrzése** elemet. A vizsgálat befejezésekor a kötetek listájának szerepelnie kell az **eredmények** ablaktábláján. A dinamikus tükrözött kötet egyetlen kötetként van felsorolva. 
3. Az **eredmények** ablaktáblájában kattintson a jobb gombbal a dinamikus tükrözött kötetre, majd kattintson a **kötet csoport létrehozása** parancsra. 
4. A **kötet létrehozása** párbeszédpanelen írja be a kötet csoport nevét, rendelje hozzá a dinamikus tükrözött kötetet ehhez a csoporthoz, majd kattintson **az OK** gombra. 
5. A **hatókör** ablaktáblán bontsa ki a **kötetek csoportjai** csomópontot. Az új kötet csoportnak a  **mennyiségi csoportok** csomópont alatt kell megjelennie. 
6. Kattintson a jobb gombbal a kötet csoport nevére. 
   
   * Az interaktív (igény szerinti) biztonsági mentési feladatok elindításához kattintson a **biztonsági mentés** készítése gombra. 
   * Ha automatikus biztonsági mentést szeretne ütemezni, kattintson a **biztonsági mentési szabályzat létrehozása** elemre. Az **általános** lapon válassza ki a kötet csoportot a listából. Az **ütemterv** lapon adja meg az ütemterv részleteit. Amikor végzett, kattintson az **OK** gombra. 
7. A biztonsági mentési feladatot a Futtatás közben is figyelheti. A **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, majd kattintson a **Futtatás** elemre, a feladat részletei megjelennek az **eredmények** ablaktábláján. A biztonsági mentési feladatok befejezése után a rendszer átviszi a részleteket az **elmúlt 24** órás feladatok listájára. 

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan kezelheti [a StorSimple-megoldást a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogyan [hozhat létre és kezelhet StorSimple-Snapshot Manager a kötetek létrehozásához és kezeléséhez](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)