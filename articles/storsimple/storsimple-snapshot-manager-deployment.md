---
title: A StorSimple Snapshot Manager üzembe helyezése | Microsoft Docs
description: Megtudhatja, hogyan töltheti le és telepítheti a StorSimple Snapshot Manager, egy MMC beépülő modult a StorSimple-adatvédelem és a biztonsági mentési funkciók kezeléséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 5f94564c5e769d3cf0e0abbe92a309a1ee2117a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003871"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>A StorSimple Snapshot Manager MMC beépülő modul üzembe helyezése

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modul, amely leegyszerűsíti az adatvédelmet és a biztonsági mentési felügyeletet egy Microsoft Azure StorSimple környezetben. A StorSimple Snapshot Manager segítségével a helyszíni és a Felhőbeli tárolást úgy Microsoft Azure StorSimple kezelheti, mintha teljes mértékben integrált tárolási rendszer lenne, így egyszerűbbé válik a biztonsági mentési és visszaállítási folyamatok egyszerűsítése és a költségek csökkentése. 

Ez az oktatóanyag ismerteti a konfigurációs követelményeket, valamint a StorSimple-Snapshot Manager telepítésének, eltávolításának és frissítésének eljárásait.

> [!NOTE]
> * A StorSimple Snapshot Manager nem használhatók Microsoft Azure StorSimple virtuális tömbök (más néven StorSimple-alapú virtuális eszközök) kezelésére.
> * Ha a StorSimple 2. frissítését tervezi telepíteni a StorSimple-eszközön, töltse le a Snapshot Manager StorSimple legújabb verzióját, és telepítse a **StorSimple Update 2 telepítése előtt**. A StorSimple Snapshot Manager legújabb verziója visszamenőlegesen kompatibilis, és a Microsoft Azure StorSimple összes kiadott verziójával működik. Ha a StorSimple Snapshot Manager korábbi verzióját használja, frissítenie kell (az új verzió telepítése előtt nem kell eltávolítania a korábbi verziót).


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple Snapshot Manager telepítése
A StorSimple Snapshot Manager telepíthetők a Windows Server 2008 R2 SP1, a Windows Server 2012 vagy a Windows Server 2012 R2 operációs rendszert futtató számítógépekre. Windows 2008 R2 rendszert futtató kiszolgálókon telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3,0 alkalmazást is.

Mielőtt telepítené vagy frissítené a Microsoft Management Console (MMC) StorSimple Snapshot Manager beépülő modulját, győződjön meg arról, hogy a Microsoft Azure StorSimple eszköz és az állomás-kiszolgáló megfelelően van konfigurálva.

## <a name="configure-prerequisites"></a>Előfeltételek konfigurálása
A következő lépések magas szintű áttekintést nyújtanak a StorSimple Snapshot Manager telepítése előtt elvégzendő konfigurációs feladatokról. A Microsoft Azure StorSimple konfigurálásához és beállításához, beleértve a rendszerkövetelményeket és a részletes útmutatást a helyszíni [StorSimple-eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md)című témakörben talál.

> [!IMPORTANT]
> Mielőtt elkezdené, tekintse át az [üzembe helyezési konfigurációs ellenőrzőlistát](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) és az [üzembe helyezés előfeltételeit](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) a helyszíni [StorSimple-eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md)című cikkben.
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>A StorSimple telepítése előtt Snapshot Manager
1. Csomagolja ki, csatlakoztatja és csatlakoztassa a Microsoft Azure StorSimple eszközt a [StorSimple 8100-eszköz telepítése](storsimple-8100-hardware-installation.md) vagy [a StorSimple 8600](storsimple-8600-hardware-installation.md)-eszköz telepítése című témakörben leírtak szerint.
2. Győződjön meg arról, hogy a gazdaszámítógép a következő operációs rendszerek egyikét futtatja:
   
   * Windows Server 2008 R2 (Windows 2008 R2 rendszert futtató kiszolgálókon telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3,0 alkalmazást is)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     StorSimple virtuális eszköz esetén a gazdagépnek Microsoft Azure virtuális gépnek kell lennie.
3. Győződjön meg arról, hogy megfelel a Microsoft Azure StorSimple összes konfigurációs követelményének. Részletekért lépjen az [üzembe helyezés előfeltételeire](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Kapcsolódjon az eszközhöz a gazdagéphez, és hajtsa végre a kezdeti konfigurálást. Részletekért lépjen az [üzembe helyezési lépések](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps)elemre.
5. Hozzon létre köteteket az eszközön, rendelje hozzá őket a gazdagéphez, és ellenőrizze, hogy a gazdagép csatlakoztatható-e és használható-e. A StorSimple Snapshot Manager a következő típusú köteteket támogatja:
   
   * Alapszintű kötetek
   * Egyszerű kötetek
   * Dinamikus kötetek
   * Tükrözött dinamikus kötetek (RAID 1)
   * Fürt megosztott kötetei
     
     A kötetek a StorSimple-eszközön vagy a StorSimple virtuális eszközön való létrehozásával kapcsolatos információkért lépjen a [6. lépés: kötet létrehozása](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)a helyszíni [StorSimple-eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md)című témakörbe.

## <a name="install-a-new-storsimple-snapshot-manager"></a>Új StorSimple telepítése Snapshot Manager
A StorSimple Snapshot Manager telepítése előtt győződjön meg arról, hogy a StorSimple-eszközön vagy StorSimple virtuális eszközön létrehozott kötetek csatlakoztatva vannak, inicializálva és formázva vannak, az [Előfeltételek konfigurálása](#configure-prerequisites)című szakaszban leírtak szerint.

> [!IMPORTANT]
> * StorSimple virtuális eszköz esetén a gazdagépnek Microsoft Azure virtuális gépnek kell lennie. 
> * A gazdagépen Windows 2008 R2, Windows Server 2012 vagy Windows Server 2012 R2 rendszernek kell futnia. Ha a kiszolgálón a Windows Server 2008 R2 fut, telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3,0 alkalmazást is.
> * Ahhoz, hogy csatlakoztatni lehessen az eszközt a StorSimple Snapshot Managerhoz, konfigurálnia kell egy iSCSI-kapcsolatot a gazdagépről a StorSimple-eszközre.

A StorSimple Snapshot Manager friss telepítésének befejezéséhez kövesse az alábbi lépéseket. Ha frissítést telepít, lépjen a [frissítés vagy a StorSimple újratelepítése Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* 1. lépés: a StorSimple telepítése Snapshot Manager 
* 2. lépés: a StorSimple Snapshot Manager csatlakoztatása egy eszközhöz 
* 3. lépés: az eszközhöz való kapcsolódás ellenőrzése 

### <a name="step-1-install-storsimple-snapshot-manager"></a>1. lépés: a StorSimple telepítése Snapshot Manager
A StorSimple Snapshot Manager telepítéséhez kövesse az alábbi lépéseket.

#### <a name="to-install-storsimple-snapshot-manager"></a>A StorSimple telepítése Snapshot Manager
1. Töltse le a StorSimple Snapshot Manager szoftvert (Nyissa meg a [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) a Microsoft letöltőközpontban), és mentse helyileg a gazdagépen.
2. A Fájlkezelőben kattintson a jobb gombbal a tömörített mappára, majd válassza az **összes kibontása** menüpontot.
3. A **Tömörített mappák kibontása** ablakban a **célhely kiválasztása és a fájlok kibontása** mezőben írja be vagy tallózással keresse meg azt az elérési utat, ahová ki szeretné olvasni a fájlt.
   
    > [!IMPORTANT]
    > Telepítenie kell a StorSimple Snapshot Manager a C: meghajtón.
    
4. Jelölje be a **kibontott fájlok megjelenítése befejezéskor** jelölőnégyzetet, majd kattintson a **Kibontás** gombra.
   
    ![Fájlok kibontása párbeszédpanel](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. A Kibontás befejezésekor megnyílik a célmappa. Kattintson duplán a célmappában megjelenő Application Setup (alkalmazás beállítása) ikonra.
6. Amikor megjelenik a **sikeres telepítés** üzenet, kattintson a **Bezárás** gombra. Ekkor megjelenik a StorSimple Snapshot Manager ikon az asztalon.
   
    ![asztal ikon](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>2. lépés: a StorSimple Snapshot Manager csatlakoztatása egy eszközhöz
A következő lépésekkel csatlakozhat a StorSimple-Snapshot Manager egy StorSimple-eszközhöz.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>A StorSimple Snapshot Manager csatlakoztatása egy eszközhöz
1. Kattintson a StorSimple Snapshot Manager ikonjára az asztalon. Megjelenik a StorSimple Snapshot Manager ablak. Az ablakban a **hatókör** panel, az **eredmények** ablaktábla és a **műveletek** ablaktábla található. 
   
    ![StorSimple Snapshot Manager felhasználói felület](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * A **hatókör** panel (a bal oldali ablaktábla) a fastruktúrában rendezett csomópontok listáját tartalmazza. Egyes csomópontok kibontásával kiválaszthatja az adott csomóponthoz kapcsolódó nézetet vagy adott adatkészletet. Kattintson a nyíl ikonra a csomópont kibontásához vagy összecsukásához. Kattintson a jobb gombbal egy elemre a **hatókör** ablaktáblán az adott elemhez elérhető műveletek listájának megtekintéséhez.
   * Az **eredmények** ablaktábla (a középső ablaktábla) részletes állapotinformációkat tartalmaz a **hatókör** ablaktáblán kiválasztott csomópontról, nézetről vagy adatokról.
   * A **műveletek** ablaktábla felsorolja azokat a műveleteket, amelyeket végrehajthat a **hatókör** ablaktáblán kiválasztott csomóponton, nézetben vagy adatokon.
     
     A StorSimple Snapshot Manager felhasználói felületének teljes leírását lásd: [StorSimple Snapshot Manager felhasználói felület](storsimple-use-snapshot-manager.md).
2. A **hatókör** ablaktáblán kattintson a jobb gombbal az **eszközök** csomópontra, majd kattintson az **eszköz konfigurálása** parancsra. Megjelenik az **eszköz konfigurálása** párbeszédpanel.
   
    ![Eszköz konfigurálása](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Az **eszközök** listájában válassza ki a Microsoft Azure StorSimple eszköz vagy virtuális eszköz IP-címét. A **jelszó** szövegmezőbe írja be a Azure Portalben az eszközhöz létrehozott StorSimple Snapshot Manager jelszavát. Kattintson az **OK** gombra.
4. A StorSimple Snapshot Manager megkeresi az Ön által azonosított eszközt. Ha az eszköz elérhető, a StorSimple Snapshot Manager hozzáadja a csatlakozást. Az [eszközhöz való kapcsolódás ellenőrzéséhez ellenőrizheti](#to-verify-the-connection) , hogy a rendszer sikeresen felvette-e a kapcsolódást.
   
    Ha az eszköz valamilyen okból nem érhető el, a StorSimple Snapshot Manager egy hibaüzenetet ad vissza. A hibaüzenet bezárásához kattintson az **OK** gombra, majd kattintson a **Mégse** gombra az **eszköz konfigurálása** párbeszédpanel bezárásához.
5. Amikor egy eszközhöz csatlakozik, a StorSimple Snapshot Manager importálja az adott eszközhöz konfigurált összes kötetet, ha a kötet csoporthoz biztonsági másolatok tartoznak. A társított biztonsági másolatokkal nem rendelkező kötetek importálása nem történik meg. Továbbá a mennyiségi csoportokhoz létrehozott biztonsági mentési szabályzatok nem lesznek importálva. Az importált csoportok megjelenítéséhez kattintson a jobb gombbal a legfelső szintű **kötetek csoportok** csomópontra a **hatókör** ablaktáblán, és kattintson az **importált csoportok váltása** elemre.

### <a name="step-3-verify-the-connection-to-the-device"></a>3. lépés: az eszközhöz való kapcsolódás ellenőrzése
A következő lépésekkel ellenőrizheti, hogy a StorSimple Snapshot Manager kapcsolódik-e a StorSimple-eszközhöz.

#### <a name="to-verify-the-connection"></a>A kapcsolódás ellenőrzése
1. A **hatókör** ablaktáblán kattintson az **eszközök** csomópontra.
   
    ![StorSimple Snapshot Manager eszköz állapota](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Tekintse át az **eredményeket** tartalmazó ablaktáblát: 
   
   * Ha megjelenik egy zöld kijelző az eszköz ikonján, és **elérhető** az **állapot** oszlopban, akkor az eszköz csatlakoztatva van. 
   * Ha az eszköz ikonján piros kijelző jelenik meg, és nem érhető el, akkor az **állapot** oszlopban az eszköz nincs csatlakoztatva. 
   * Ha a **frissítés** megjelenik az **állapot** oszlopban, akkor a StorSimple Snapshot Manager beolvassa a kötetek csoportjait és a kapcsolódó biztonsági másolatokat egy csatlakoztatott eszközhöz.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>A StorSimple frissítése vagy újratelepítése Snapshot Manager
A StorSimple Snapshot Manager teljesen el kell távolítania a szoftver frissítése vagy újratelepítése előtt. 

A StorSimple Snapshot Manager újratelepítése előtt biztonsági mentést készíthet a gazdagépen lévő meglévő StorSimple Snapshot Manager-adatbázisról. Ezzel menti a biztonsági mentési házirendeket és a konfigurációs adatokat, így egyszerűen visszaállíthatja ezeket az adatokat a biztonsági mentésből.

Az alábbi lépéseket követve frissítheti vagy újratelepítheti a StorSimple Snapshot Manager:

* 1. lépés: a StorSimple eltávolítása Snapshot Manager 
* 2. lépés: a StorSimple Snapshot Manager adatbázisának biztonsági mentése 
* 3. lépés: a StorSimple újratelepítése Snapshot Manager és az adatbázis visszaállítása 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>1. lépés: a StorSimple eltávolítása Snapshot Manager
A StorSimple Snapshot Manager eltávolításához kövesse az alábbi lépéseket.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>A StorSimple eltávolítása Snapshot Manager
1. A gazdagépen nyissa meg a **Vezérlőpultot**, kattintson a **programok**, majd a **programok és szolgáltatások** elemre.
2. A bal oldali ablaktáblán kattintson az **Eltávolítás vagy a program módosítása** elemre.
3. Kattintson a jobb gombbal a **StorSimple Snapshot Manager** elemre, majd kattintson az **Eltávolítás** parancsra.
4. Ezzel elindítja a StorSimple Snapshot Manager telepítőprogramot. Kattintson a **telepítés módosítása** elemre, majd az **Eltávolítás** gombra.
   
   > [!NOTE]
   > Ha van olyan MMC-folyamat, amely a háttérben fut, például a StorSimple Snapshot Manager vagy a Lemezkezelés, az Eltávolítás sikertelen lesz, és egy üzenet jelenik meg az összes MMC-példány bezárásához, mielőtt megpróbálja eltávolítani a programot. Válassza az **alkalmazások automatikus lezárása lehetőséget, majd próbálja meg újraindítani őket a telepítés befejezése után**, majd kattintson **az OK** gombra.
   > 
   > 
5. Ha az eltávolítási folyamat elkészült, a **telepítő sikeres** üzenet jelenik meg. Kattintson a **Bezárás** gombra.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>2. lépés: a StorSimple Snapshot Manager adatbázisának biztonsági mentése
A következő lépésekkel hozhat létre és menthet másolatot a StorSimple Snapshot Manager adatbázisáról.

#### <a name="to-back-up-the-database"></a>Az adatbázis biztonsági mentése
1. Állítsa le a Microsoft StorSimple-kezelő szolgáltatást:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópult **eszközök** menüjében válassza a **szolgáltatások** lehetőséget.
   3. A **szolgáltatások** lapon válassza a **Microsoft StorSimple-kezelő szolgáltatás** elemet.
   4. A jobb oldali ablaktábla **Microsoft StorSimple-kezelő szolgáltatása** területén kattintson **a szolgáltatás leállítása** elemre.
      
        ![A StorSimple Eszközkezelő szolgáltatás leállítása](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Tallózás a C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > A ProgramData mappa rejtett.
  
3. Keresse meg a katalógus XML-fájlját, másolja a fájlt, és tárolja a másolatot egy biztonságos helyen vagy a felhőben.
   
    ![StorSimple biztonságimásolat-katalógus fájlja](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Indítsa újra a Microsoft StorSimple-kezelő szolgáltatást: 
   
   1. A Kiszolgálókezelő irányítópult **eszközök** menüjében válassza a **szolgáltatások** lehetőséget.
   2. A **szolgáltatások** lapon válassza ki a **Microsoft StorSimple-kezelő szolgáltatást**.
   3. A jobb oldali ablaktábla **Microsoft StorSimple-kezelő szolgáltatása** területén kattintson **a szolgáltatás újraindítása** elemre. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>3. lépés: a StorSimple újratelepítése Snapshot Manager és az adatbázis visszaállítása
A StorSimple Snapshot Manager újratelepítéséhez kövesse az [új StorSimple Snapshot Manager telepítése](#install-a-new-storsimple-snapshot-manager)című témakör lépéseit. Ezután a következő eljárással állíthatja vissza a StorSimple Snapshot Manager adatbázisát.

#### <a name="to-restore-the-database"></a>Az adatbázis visszaállítása
1. Állítsa le a Microsoft StorSimple-kezelő szolgáltatást:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópult **eszközök** menüjében válassza a **szolgáltatások** lehetőséget.
   3. A **szolgáltatások** lapon válassza a **Microsoft StorSimple-kezelő szolgáltatás** elemet.
   4. A jobb oldali ablaktábla **Microsoft StorSimple-kezelő szolgáltatása** területén kattintson **a szolgáltatás leállítása** elemre.
2. Tallózás a C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > A ProgramData mappa rejtett.
   > 
   > 
3. Törölje a katalógus XML-fájlját, és cserélje le a korábban mentett verzióra.
4. Indítsa újra a Microsoft StorSimple-kezelő szolgáltatást: 
   
   1. A Kiszolgálókezelő irányítópult **eszközök** menüjében válassza a **szolgáltatások** lehetőséget.
   2. A **szolgáltatások** lapon válassza a **Microsoft StorSimple-kezelő szolgáltatás** elemet.
   3. A jobb oldali ablaktábla **Microsoft StorSimple-kezelő szolgáltatása** területén kattintson **a szolgáltatás újraindítása** elemre.

## <a name="next-steps"></a>Következő lépések
* Ha többet szeretne megtudni a StorSimple Snapshot Managerról, olvassa el a [Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)című témakört.
* Ha többet szeretne megtudni a StorSimple Snapshot Manager felhasználói felületéről, nyissa meg a [StorSimple Snapshot Manager felhasználói felületét](storsimple-use-snapshot-manager.md).
* Ha többet szeretne megtudni a StorSimple Snapshot Manager használatáról, látogasson el a [StorSimple Snapshot Manager használatára a StorSimple-megoldás felügyeletéhez](storsimple-snapshot-manager-admin.md).

