---
title: StorSimple-adapter telepítése a SharePointhoz | Microsoft Docs
description: Leírja, hogyan telepítheti és konfigurálhatja a SharePoint-StorSimple-adaptert a SharePoint-farmban.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: alkohli
ms.openlocfilehash: 639efab3a9eaef400b3fbe3d0b134d02f458b865
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966292"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>A SharePointhoz készült StorSimple-adapter telepítése és konfigurálása
## <a name="overview"></a>Áttekintés
A SharePointhoz készült StorSimple-adapter olyan összetevő, amely lehetővé teszi, hogy Microsoft Azure StorSimple rugalmas tárterületet és adatvédelmet biztosítson a SharePoint Server-farmok számára. Az adapter használatával áthelyezheti a bináris nagyméretű objektum-(BLOB-) tartalmakat a SQL Server tartalmi adatbázisaiból a Microsoft Azure StorSimple Hybrid Cloud Storage-eszközre.

A SharePointhoz készült StorSimple-adapter távoli BLOB Storage-(RBS-) szolgáltatóként működik, és a SQL Server távoli BLOB Storage szolgáltatással tárolja a strukturálatlan SharePoint-tartalmakat (Blobok formájában) egy olyan fájlkiszolgálón, amelyet egy StorSimple-eszköz támogat.

> [!NOTE]
> A SharePointhoz készült StorSimple-adapter támogatja a SharePoint Server 2010 távoli BLOB Storage (RBS) használatát. Nem támogatja a SharePoint Server 2010 külső BLOB Storage (EBS) használatát.


* A SharePointhoz készült StorSimple-adapter letöltéséhez nyissa meg a [sharepointhoz készült StorSimple-adaptert][1] a Microsoft letöltőközpontban.
* Az RBS és az RBS korlátozásának megtervezéséről az [RBS használata a sharepoint 2013-ben][2] vagy [az RBS-terv (SharePoint Server 2010)][3]című témakörben tájékozódhat.

Az Áttekintés további része röviden leírja a SharePointhoz készült StorSimple-adapter szerepét, valamint a SharePoint-kapacitást és a teljesítményre vonatkozó korlátozásokat, amelyeket az adapter telepítése és konfigurálása előtt érdemes tudnia. Az információk áttekintése után nyissa meg a [StorSimple-adapter a SharePoint rendszerhez](#storsimple-adapter-for-sharepoint-installation) című témakört az adapter beállításának megkezdéséhez.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple-adapter SharePoint-előnyökhöz
Egy SharePoint-webhelyen a tartalom strukturálatlan BLOB-adatként tárolódik egy vagy több tartalmi adatbázisban. Alapértelmezés szerint ezek az adatbázisok a SQL Server rendszert futtató számítógépeken vannak tárolva, és a SharePoint Server-farmban találhatók. A Blobok gyorsan növelhetik a méretet, így nagy mennyiségű helyszíni tárterületet igényelnek. Emiatt előfordulhat, hogy egy másik, kevésbé költséges tárolási megoldást szeretne találni. A SQL Server olyan távoli Blob Storage (RBS) nevű technológiát biztosít, amely lehetővé teszi a BLOBok tartalmának tárolását a fájlrendszerben a SQL Server-adatbázison kívül. Az RBS használatával a Blobok a SQL Server rendszert futtató számítógép fájlrendszerében találhatók, vagy egy másik kiszolgáló számítógépének fájlrendszerében is tárolhatók.

Az RBS használatához RBS-szolgáltatót kell használni, például a SharePointhoz készült StorSimple-adaptert, hogy az RBS engedélyezve legyen a SharePointban. A SharePointhoz készült StorSimple-adapter az RBS használatával működik, és lehetővé teszi a Blobok áthelyezését a Microsoft Azure StorSimple rendszer által készített kiszolgálóra. Microsoft Azure StorSimple a BLOB-adatokat helyileg vagy a felhőben tárolja a használat alapján. A nagyon aktív Blobok (általában az 1. réteg vagy a forró adatok) helyileg találhatók. A felhőben kevesebb aktív adat és archiválási adat található. Miután engedélyezte az RBS-t egy tartalmi adatbázisban, a SharePointban létrehozott összes új BLOB-tartalmat a rendszer a StorSimple-eszközön tárolja, és nem a tartalom-adatbázisba.

Az RBS Microsoft Azure StorSimple megvalósítása a következő előnyöket biztosítja:

* Ha áthelyezi a BLOB tartalmát egy különálló kiszolgálóra, csökkentheti a lekérdezés terhelését SQL Serveron, ami javíthatja SQL Server a rugalmasságot. 
* Az Azure StorSimple a deduplikálás és a tömörítés használatával csökkenti az adatméretet.
* Az Azure StorSimple helyi és Felhőbeli Pillanatképek formájában biztosítja az adatvédelmet. Ha maga a StorSimple-eszközre helyezi az adatbázist, biztonsági mentést készíthet a tartalom-adatbázisról, és összekapcsolhatja a blobokat az összeomlás-konzisztens módon. (A tartalom-adatbázisnak az eszközre való áthelyezése csak a StorSimple 8000 sorozatú eszközön támogatott. Ez a funkció a 5000-es vagy a 7000-es sorozat esetében nem támogatott.)
* Az Azure StorSimple olyan vész-helyreállítási funkciókat tartalmaz, mint például a feladatátvétel, a fájlok és a kötetek helyreállítása (beleértve a teszt helyreállítást is) és az adatfeldolgozások gyors helyreállítása.
* Használhatja az adat-helyreállítási szoftvert, például a Kroll Ontrack PowerControls, a BLOB-adatok StorSimple pillanatképeit a SharePoint-tartalmak elemszintű helyreállításának elvégzéséhez. (Ez az adathelyreállítási szoftver egy külön vásárlás.)
* A SharePoint rendszerhez készült StorSimple-adapter a SharePoint központi felügyeleti portálján található, amely lehetővé teszi a teljes SharePoint-megoldás központi helyről történő kezelését.

A BLOB tartalmának a fájlrendszerbe való áthelyezése további költségmegtakarítást és előnyöket biztosít. Az RBS használata például csökkentheti a költséges 1. szintű tárolók szükségességét, mivel ez csökkenti a tartalom-adatbázist, az RBS csökkentheti a SharePoint-kiszolgálófarm számára szükséges adatbázisok számát. Azonban más tényezők, például az adatbázis méretére vonatkozó korlátozások és a nem RBS-tartalom mennyisége is befolyásolhatja a tárolási követelményeket. További információ az RBS használatának költségeiről és előnyeiről: az [RBS tervezése (SharePoint Foundation 2010)][4] , valamint az [RBS használatának eldöntése a SharePoint 2013-ben][5].

### <a name="capacity-and-performance-limits"></a>Kapacitás és teljesítménybeli korlátok
Mielőtt fontolóra veszi az RBS használatát a SharePoint-megoldásban, ismernie kell a SharePoint Server 2010 és a SharePoint Server 2013 tesztelt teljesítmény-és kapacitási korlátait, valamint azt, hogy ezek a korlátok hogyan kapcsolódnak az elfogadható teljesítményhez. További információ: [a szoftver határai és korlátai a SharePoint 2013-](/SharePoint/install/software-boundaries-and-limits-0)hoz.

Az RBS konfigurálása előtt tekintse át a következőket:

* Győződjön meg arról, hogy a tartalom teljes mérete (a tartalom-adatbázis mérete és a társított külső Blobok mérete) nem lépi túl a SharePoint által támogatott RBS-mérethatárt. Ez a korlát 200 GB. 
  
    **A tartalom-adatbázis és a BLOB méretének mérése**
  
  1. Futtassa ezt a lekérdezést a központi adminisztrációs WFE. Indítsa el a SharePoint felügyeleti rendszerhéját, majd írja be a következő Windows PowerShell-parancsot a tartalom-adatbázisok méretének lekéréséhez:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Ez a lépés lekéri a tartalom-adatbázis méretét a lemezen.
  2. Futtassa a következő SQL-lekérdezések egyikét az SQL Management Studioban az egyes tartalom-adatbázisokban található SQL Server-kiszolgálón, és adja hozzá az 1. lépésben kapott értéket.
     
     A SharePoint 2013 tartalmi adatbázisaiban írja be a következőt:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     A SharePoint 2010 tartalmi adatbázisaiban írja be a következőt:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Ez a lépés beolvassa a külsőleg kihelyezett Blobok méretét.
* Javasoljuk, hogy az összes BLOB-és adatbázis-tartalmat helyileg tárolja a StorSimple-eszközön. A StorSimple-eszköz egy két csomópontos fürt a magas rendelkezésre állás érdekében. A tartalmi adatbázisok és Blobok elhelyezése a StorSimple-eszközön magas rendelkezésre állást biztosít.
  
    A hagyományos SQL Server áttelepítéssel kapcsolatos ajánlott eljárások segítségével áthelyezheti a tartalom-adatbázist a StorSimple-eszközre. Az adatbázis áthelyezése csak azután, hogy az adatbázisból származó összes BLOB-tartalmat áthelyezte a fájlmegosztás RBS használatával. Ha úgy dönt, hogy a tartalom-adatbázist a StorSimple-eszközre helyezi át, javasoljuk, hogy a tartalom-adatbázis tárterületét elsődleges kötetként konfigurálja az eszközön.
* Ha Microsoft Azure StorSimple, a többkötetes kötetek használata esetén nincs mód annak biztosítására, hogy a StorSimple-eszközön helyileg tárolt tartalom ne legyen feldolgozva a felhőalapú tárolás Microsoft Azure. Ezért javasoljuk, hogy a StorSimple helyileg rögzített köteteket a SharePoint RBS-sel együtt használja. Ez biztosítja, hogy az összes BLOB-tartalom helyileg maradjon a StorSimple-eszközön, és nem kerül át Microsoft Azureba.
* Ha nem tárolja a tartalom-adatbázisokat a StorSimple-eszközön, használja az RBS-t támogató hagyományos SQL Server magas rendelkezésre állású ajánlott eljárásokat. SQL Server fürtszolgáltatás támogatja az RBS-t, míg a SQL Server tükrözés nem. 

> [!WARNING]
> Ha nem engedélyezte az RBS használatát, nem javasoljuk a tartalom-adatbázis áthelyezését a StorSimple-eszközre. Ez egy nem tesztelt konfiguráció.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple-adapter a SharePoint telepítéséhez
Mielőtt telepítené a SharePointhoz készült StorSimple-adaptert, konfigurálnia kell a StorSimple eszközt, és meg kell győződnie arról, hogy a SharePoint-kiszolgálófarm és a SQL Server példánya megfelel az összes előfeltételnek. Ez az oktatóanyag ismerteti a konfigurációs követelményeket, valamint a SharePointhoz készült StorSimple-adapter telepítésének és frissítésének eljárásait.

## <a name="configure-prerequisites"></a>Előfeltételek konfigurálása
Mielőtt telepítené a SharePointhoz készült StorSimple-adaptert, győződjön meg arról, hogy a StorSimple-eszköz, a SharePoint-kiszolgálófarm és a SQL Server példánya megfelel a következő előfeltételeknek.

### <a name="system-requirements"></a>System requirements (Rendszerkövetelmények)
A SharePointhoz készült StorSimple-adapter a következő hardverekkel és szoftverekkel működik:

* Támogatott operációs rendszer – Windows Server 2008 R2 SP1, Windows Server 2012 vagy Windows Server 2012 R2
* Támogatott SharePoint-verziók – SharePoint Server 2010 vagy SharePoint Server 2013
* Támogatott SQL Server verziók – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition vagy SQL Server 2012 Enterprise Edition
* Támogatott StorSimple-eszközök – StorSimple 8000 sorozat, StorSimple 7000 sorozat vagy StorSimple 5000 sorozat.

### <a name="storsimple-device-configuration-prerequisites"></a>StorSimple-eszközök konfigurálásának előfeltételei
A StorSimple eszköz egy blokkos eszköz, ezért olyan fájlkiszolgálón van szükség, amelyen az adott adattároló található. Javasoljuk, hogy a SharePoint-farmról egy meglévő kiszolgáló helyett egy különálló kiszolgálót használjon. Ennek a kiszolgálónak ugyanazon a helyi hálózaton (LAN) kell lennie, mint a tartalom-adatbázisokat futtató SQL Server számítógépnek.

> [!TIP]
> * Ha a SharePoint-farmot magas rendelkezésre állásra konfigurálja, akkor a fájlkiszolgáló a magas rendelkezésre állás érdekében is üzembe helyezhető.
> * Ha nem tárolja a tartalom-adatbázist a StorSimple-eszközön, használja az RBS-t támogató hagyományos magas rendelkezésre állású ajánlott eljárásokat. SQL Server fürtszolgáltatás támogatja az RBS-t, míg a SQL Server tükrözés nem. 


Győződjön meg arról, hogy a StorSimple-eszköz megfelelően van konfigurálva, és hogy a SharePoint-telepítést támogató megfelelő kötetek konfigurálva vannak és elérhetők a SQL Server számítógépről. Ha még nem telepítette és konfigurálta a StorSimple-eszközt, folytassa a helyszíni [StorSimple-eszköz üzembe helyezésével](storsimple-8000-deployment-walkthrough-u2.md) . Jegyezze fel a StorSimple-eszköz IP-címét; szüksége lesz rá a SharePoint-telepítés StorSimple-adaptere során.

Továbbá győződjön meg arról, hogy a BLOB externalizációja használni kívánt kötet megfelel a következő követelményeknek:

* A kötetet 64 KB-os kiosztási egység méretével kell formázni.
* A webes előtér (WFE) és az alkalmazáskiszolgáló számára elérhetőnek kell lennie a kötethez egy univerzális elnevezési konvenció (UNC) elérési útján.
* A SharePoint-kiszolgálófarmot úgy kell konfigurálni, hogy a kötetre írjon.

> [!NOTE]
> Miután telepítette és konfigurálta az adaptert, az összes BLOB-externalizációja át kell haladnia a StorSimple-eszközön (az eszköz bemutatja a köteteket a tárolási rétegek SQL Server és kezeléséhez). Nem használhat más célokat a BLOB-externalizációja.


Ha azt tervezi, hogy StorSimple-Snapshot Manager használ a BLOB-és adatbázis-adatpillanatképek készítéséhez, akkor ügyeljen arra, hogy az adatbázis-kiszolgálón telepítse a StorSimple-Snapshot Manager, hogy az SQL-író szolgáltatás a Windows Kötet árnyékmásolata szolgáltatás (VSS) megvalósítására is használható legyen.

> [!IMPORTANT]
> A StorSimple Snapshot Manager nem támogatja a SharePoint VSS-író használatát, és nem tudja az alkalmazással konzisztens pillanatképeket készíteni a SharePoint-adatbázisról. Egy SharePoint-forgatókönyvben a StorSimple Snapshot Manager csak az összeomlás-konzisztens biztonsági mentéseket biztosítja.


## <a name="sharepoint-farm-configuration-prerequisites"></a>A SharePoint-farm konfigurálásának előfeltételei
Győződjön meg arról, hogy a SharePoint-kiszolgálófarm megfelelően van konfigurálva, a következőképpen:

* Győződjön meg arról, hogy a SharePoint-kiszolgálófarm kifogástalan állapotban van, és ellenőrizze a következőket:
* A farmban regisztrált összes SharePoint-WFE és-alkalmazáskiszolgáló fut, és a kiszolgálóról pingelhető, amelyre telepíteni fogja a SharePoint rendszerhez készült StorSimple-adaptert.
* A SharePoint időzítő szolgáltatása (SPTimerV3 vagy SPTimerV4) minden WFE-kiszolgálón és alkalmazáskiszolgálón fut.
* Mind a SharePoint-időzítő szolgáltatás, mind az IIS-alkalmazáskészlet, amely alatt a SharePoint központi felügyeleti hely fut, rendszergazdai jogosultságokkal rendelkezik.
* Győződjön meg arról, hogy az Internet Explorer fokozott biztonsági környezete (IE ESC) le van tiltva. Az IE ESC letiltásához kövesse az alábbi lépéseket:
  
  1. Az Internet Explorer összes példányának lezárása.
  2. Indítsa el a Kiszolgálókezelő alkalmazást.
  3. A bal oldali ablaktáblán kattintson a **helyi kiszolgáló** elemre.
  4. A jobb oldali ablaktáblán, az **Internet Explorer fokozott biztonsági beállításai** mellett kattintson **a be** gombra.
  5. A **rendszergazdák** területen kattintson a **ki** gombra.
  6. Kattintson az **OK** gombra.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Távoli BLOB Storage-(RBS-) előfeltételek
Győződjön meg arról, hogy a SQL Server támogatott verzióját használja. Csak a következő verziók támogatottak, és képesek az RBS használatára:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

A Blobok csak azokon a köteteken helyezhetők el, amelyeket a StorSimple-eszköz a SQL Server számára jelent. A BLOB-externalizációja nem támogatottak más célok.

Az összes előfeltételként szükséges konfigurációs lépés elvégzése után nyissa meg [a StorSimple-adapter telepítése a SharePoint rendszerhez](#install-the-storsimple-adapter-for-sharepoint)című részt.

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>A SharePointhoz készült StorSimple-adapter telepítése
A SharePointhoz készült StorSimple-adapter telepítéséhez kövesse az alábbi lépéseket. Ha újratelepíti a szoftvert, tekintse [meg a StorSimple-adapter frissítése vagy újratelepítése a sharepointhoz](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint)című témakört. A telepítéshez szükséges idő a SharePoint-kiszolgálófarm SharePoint-adatbázisainak teljes számától függ.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>RBS konfigurálása
Miután telepítette a StorSimple-adaptert a SharePointhoz, konfigurálja az RBS-t az alábbi eljárásban leírtak szerint.

> [!TIP]
> A SharePoint rendszerhez készült StorSimple-adapter a SharePoint központi felügyelet lapjára csatlakozik, amely lehetővé teszi, hogy az RBS engedélyezve legyen vagy le legyen tiltva a SharePoint-farm minden tartalmi adatbázisán. Az RBS engedélyezése vagy letiltása azonban a tartalmi adatbázisban az IIS alaphelyzetbe állítását eredményezi, amely a farm konfigurációjától függően megszakíthatja a SharePoint webes kezelőfelületének (WFE) rendelkezésre állását. (Például az előtér-terheléselosztó használata, az aktuális kiszolgáló munkaterhelése stb.) korlátozhatja vagy elháríthatja ezt a megszakadást.) A felhasználók megszakadásának megakadályozása érdekében javasoljuk, hogy csak a tervezett karbantartási időszakban engedélyezze vagy tiltsa le az RBS-t.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>A Garbage-gyűjtemény konfigurálása
Ha egy SharePoint-webhelyről töröl objektumokat, azokat a rendszer nem törli automatikusan az RBS-tároló kötetéről. Ehelyett egy aszinkron, háttérben futó karbantartási program törli az árva blobokat a fájl tárolójából. A rendszergazdák rendszeres időközönként ütemezhetik ezt a folyamatot, vagy ha szükséges, elindíthatják azt.

Ezt a karbantartási programot (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) automatikusan telepíti a rendszer az összes SharePoint WFE-kiszolgálóra és az alkalmazás-kiszolgálóra az RBS engedélyezésekor. A program a következő helyen van telepítve: *rendszerindító meghajtó*: \PROGRAM Files\Microsoft SQL távoli Blob Storage 10.50 \ karbantartó \

További információ a karbantartási program konfigurálásáról és használatáról: az [RBS megőrzése a SharePoint Server 2013-ben][8].

> [!IMPORTANT]
> Az RBS-karbantartó program erőforrás-igényes. A SharePoint-farmon csak a fénytevékenységi időszakok alatt kell ütemeznie a futtatást.


### <a name="delete-orphaned-blobs-immediately"></a>Árva Blobok azonnali törlése
Ha azonnal törölni kell az árva blobokat, az alábbi utasításokat használhatja. Vegye figyelembe, hogy ez az útmutató a SharePoint 2013-környezetekben a következő összetevőkkel végezhető el:

* A tartalom-adatbázis neve WSS_Content.
* A SQL Server neve SHRPT13-SQL12\SHRPT13.
* A webalkalmazás neve SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>A SharePointhoz készült StorSimple-adapter frissítése vagy újratelepítése
A következő eljárással frissítheti a SharePoint-kiszolgálót, majd újratelepítheti a SharePoint StorSimple-adapterét, vagy egyszerűen frissítheti vagy újratelepítheti az adaptert egy meglévő SharePoint Server-farmon.

> [!IMPORTANT]
> A SharePoint szoftver frissítése és/vagy a SharePoint StorSimple-adapterének frissítése vagy újratelepítése előtt tekintse át a következő információkat:
> 
> * A korábban az RBS-n keresztül külső tárolóba áthelyezett fájlok mindaddig nem lesznek elérhetők, amíg az újratelepítés be nem fejeződik, és az RBS szolgáltatás újból engedélyezve lesz. A felhasználói hatás korlátozásához hajtson végre minden frissítést vagy újratelepítést egy tervezett karbantartási időszak alatt.
> * A frissítés/újratelepítéshez szükséges idő a SharePoint-kiszolgálófarm SharePoint-adatbázisainak teljes számától függően változhat.
> * A frissítés/újratelepítés befejezése után engedélyeznie kell az RBS-t a tartalom-adatbázisok számára. További információkért lásd: az [RBS konfigurálása](#configure-rbs) .
> * Ha olyan SharePoint-farm esetében konfigurálja az RBS-t, amely nagyon sok adatbázissal rendelkezik (200-nál nagyobb), akkor előfordulhat, hogy a **SharePoint központi felügyelet** lapja időtúllépést okoz. Ha ez történik, frissítse a lapot. Ez nincs hatással a konfigurációs folyamatra.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple-adapter a SharePoint eltávolításához
Az alábbi eljárások azt írják le, hogyan helyezheti át a blobokat a SQL Server Content Database-be, majd távolítsa el a SharePointhoz készült StorSimple-adaptert. 

> [!IMPORTANT]
> Az adapter szoftverének eltávolítása előtt a blobokat vissza kell helyezni a tartalmi adatbázisba.


### <a name="before-you-begin"></a>Előkészületek
Gyűjtse össze az alábbi információkat, mielőtt visszahelyezi az adatokat a SQL Server tartalom-adatbázisaiba, és megkezdi az adapter-eltávolítási folyamatot:

* Az összes olyan adatbázis neve, amelyhez az RBS engedélyezve van
* A beállított BLOB-tároló UNC elérési útja

### <a name="move-the-blobs-back-to-the-content-databases"></a>A Blobok visszaállítása a tartalom adatbázisaiba
A SharePoint szoftverhez készült StorSimple-adapter eltávolítása előtt át kell telepítenie az összes olyan blobot, amely vissza lett mentve a SQL Server tartalom-adatbázisokra. Ha megkísérli eltávolítani a SharePointhoz készült StorSimple-adaptert, mielőtt az összes blobot visszahelyezi a tartalom-adatbázisba, a következő figyelmeztető üzenet jelenik meg.

![Figyelmeztető üzenet](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>A Blobok visszahelyezése a tartalom-adatbázisba
1. Töltse le a külsőleg kihelyezett objektumokat.
2. Nyissa meg a **Központi SharePoint-felügyelet** oldalt, és keresse meg a **Rendszerbeállítások** lapot.
3. Az **Azure StorSimple** alatt kattintson a **StorSimple-adapter konfigurálása** elemre.
4. Az **StorSimple-adapter konfigurálása** lapon kattintson a **Letiltás** gombra az egyes tartalmi adatbázisok alatt, amelyeket el szeretne távolítani a külső blob-tárolóból. 
5. Törölje az objektumokat a SharePointból, majd töltse fel újra.

Másik lehetőségként használhatja a `RBS Migrate()` sharepointhoz mellékelt Microsoft PowerShell-parancsmagot is. További információ: [tartalom migrálása az RBS-be vagy](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14))abból.

Miután visszahelyezte a blobokat a tartalom-adatbázisba, ugorjon a következő lépésre: [távolítsa el az adaptert](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Az adapter eltávolítása
Miután visszahelyezte a blobokat a SQL Server tartalom-adatbázisba, az alábbi lehetőségek egyikével távolítsa el a SharePointhoz készült StorSimple-adaptert.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>A telepítőprogram használata az adapter eltávolításához
1. Rendszergazdai jogosultságokkal rendelkező fiókot használhat a webes előtér-(WFE-) kiszolgálóra való bejelentkezéshez.
2. Kattintson duplán a SharePoint Installer StorSimple-adapterére. Elindul a telepítővarázsló.
   
    ![Telepítővarázsló](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Kattintson a **Tovább** gombra. Megjelenik a következő oldal.
   
    ![Telepítővarázsló eltávolítása lap](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Az eltávolítási folyamat kiválasztásához kattintson az **Eltávolítás** gombra. Megjelenik a következő oldal.
   
    ![Telepítővarázsló megerősítése lap](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Az Eltávolítás megerősítéséhez kattintson az **Eltávolítás** gombra. Megjelenik a következő folyamat lap.
   
    ![A telepítővarázsló állapotjelző lapja](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Az Eltávolítás befejezésekor megjelenik a Befejezés lap. A telepítővarázsló bezárásához kattintson a **Befejezés** gombra.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>A Vezérlőpult használata az adapter eltávolításához
1. Nyissa meg a Vezérlőpultot, majd kattintson a **programok és szolgáltatások** elemre.
2. Válassza **a StorSimple-adapter a sharepointhoz** lehetőséget, majd kattintson az **Eltávolítás** gombra.

## <a name="next-steps"></a>Következő lépések
[További információ a StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: /SharePoint/administration/rbs-planning
[3]: /previous-versions/office/sharepoint-server-2010/ff628583(v=office.14)
[4]: /previous-versions/office/sharepoint-foundation-2010/ff628569(v=office.14)
[5]: /SharePoint/administration/rbs-planning
[8]: /SharePoint/administration/maintain-rbs