---
title: Helyszíni NAS-áttelepítés Azure File Sync Azure DataBox keresztül
description: Ismerje meg, hogyan telepítheti át a fájlokat egy helyszíni hálózati tároló (NAS) helyről egy hibrid Felhőbeli üzemelő példányra az Azure DataBox keresztül Azure File Sync használatával.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583853"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>A DataBox használata hálózati csatlakoztatott tárolóról (NAS) a hibrid felhőbe való Migrálás Azure File Sync

Ez az áttelepítési cikk a többek között a következő kulcsszavakat foglalja magában: NAS, Azure File Sync és Azure DataBox. Ellenőrizze, hogy a jelen cikk a forgatókönyvre vonatkozik-e:

> [!div class="checklist"]
> * Adatforrás: hálózati csatlakoztatott tároló (NAS)
> * Áttelepítési útvonal &rArr; : &rArr; a NAS DataBox &rArr; Az Azure fájlmegosztás szinkronizálását a Windows Serverrel
> * Helyi gyorsítótárazási fájlok: igen, a végső cél egy Azure File Sync üzemelő példány.

Ha a forgatókönyv eltérő, tekintse át az [áttelepítési útmutatók táblázatát](storage-files-migration-overview.md#migration-guides).

Azure File Sync a közvetlenül csatlakoztatott tároló (DAS) helyein működik, és nem támogatja a hálózati csatolású tárolók (NAS) helyein való szinkronizálást.
Ez a tény a szükséges fájlok áttelepítését végzi el, és ez a cikk végigvezeti Önt a Migrálás megtervezésén és végrehajtásán.

## <a name="migration-goals"></a>Migrálási célok

A cél a NAS-berendezésen lévő megosztások áthelyezése Windows Serverre. Ezután használja a Azure File Synct a hibrid felhőalapú telepítéshez. Ezt az áttelepítést olyan módon kell végrehajtani, amely garantálja az adatáttelepítés során a termelési adatok és a rendelkezésre állás integritását. Az utóbbi megköveteli, hogy a leállások minimálisra kerüljenek, így az csak kis mértékben meghaladhatja a normál karbantartási időszakokat.

## <a name="migration-overview"></a>Migrálás áttekintése

Az áttelepítési folyamat több fázisból áll. Üzembe kell helyeznie az Azure Storage-fiókokat és a fájlmegosztást, telepítenie kell egy helyszíni Windows Servert, konfigurálnia Azure File Sync, át kell telepítenie a RoboCopy használatával, végül pedig el kell végeznie a kivágást. A következő szakaszok részletesen ismertetik az áttelepítési folyamat szakaszait.

> [!TIP]
> Ha visszatér erre a cikkre, a jobb oldalon lévő navigálással ugorjon arra az áttelepítési fázisra, ahol abbahagyta.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1. fázis: annak meghatározása, hogy hány Azure-fájlmegosztás szükséges

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>2. fázis: az Azure Storage-erőforrások üzembe helyezése

Ebben a fázisban az 1. fázisban található leképezési táblázat alapján kell kiépíteni az Azure Storage-fiókok és-megosztások megfelelő számát.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>3. fázis: az Azure DataBox-berendezések számának meghatározása

Csak akkor kezdje el ezt a lépést, ha befejezte az előző fázist. Most létre kell hozni az Azure Storage-erőforrásokat (Storage-fiókokat és-fájlmegosztást). A DataBox sorrendjében meg kell adnia, hogy a DataBox mely tárolási fiókokba helyezi át az adatátvitelt.

Ebben a fázisban le kell képeznie az áttelepítési terv eredményeit az előző fázisból az elérhető DataBox lehetőségek korlátaira. Ezek a szempontok segítséget nyújtanak egy olyan terv létrehozásához, amely DataBox-beállításokat választhat, és hogy hányan kell áthelyeznie a NAS-megosztásokat az Azure-fájlmegosztásba.

Ha meg szeretné határozni, hogy milyen típusú eszközökre van szüksége, vegye figyelembe ezeket a fontos korlátokat:

* Bármely Azure-DataBox akár 10 Storage-fiókba is áthelyezheti az adatátvitelt. 
* Minden DataBox-beállítás saját felhasználható kapacitással rendelkezik. Lásd: [DataBox-beállítások](#databox-options).

Tekintse át az áttelepítési tervet a létrehozandó Storage-fiókok számának és az egyes megosztásoknak a létrehozásához. Ezután tekintse meg az egyes megosztások méretét a NAS-on. Ezen információk kombinálásával optimalizálhatja és eldöntheti, hogy mely berendezéseket kell elküldeni a Storage-fiókoknak. Két DataBox-eszköz is áthelyezheti a fájlokat ugyanabba a Storage-fiókba, de egyetlen fájlmegosztás tartalmát ne Ossza szét 2 DataBoxes között.

### <a name="databox-options"></a>DataBox-beállítások

Normál áttelepítéshez a következő három DataBox-beállítás egyikét vagy kombinációját kell kiválasztani: 

* A DataBox-lemezek a Microsoft egy és legfeljebb öt SSD-lemezt küld Önnek, amelyek mindegyike 8 TiB-os kapacitással rendelkezik, és legfeljebb 40 TiB-t biztosít. A felhasználható kapacitás körülbelül 20%-kal kevesebb, a titkosítás és a fájlrendszer terhelése miatt. További információ: [DataBox Disks – dokumentáció](../../databox/data-box-disk-overview.md).
* DataBox ez a leggyakoribb lehetőség. A rendszer a NAS-hoz hasonló robusztus DataBox-berendezést küld Önnek. 80 TiB felhasználható kapacitással rendelkezik. További információ: DataBox- [dokumentáció](../../databox/data-box-overview.md).
* DataBox Heavy ez a beállítás egy robusztus DataBox-berendezést tartalmaz a kerekeken, amely a NAS-hoz hasonló módon működik, 1 PiB kapacitással. A felhasználható kapacitás körülbelül 20%-kal kevesebb, a titkosítás és a fájlrendszer terhelése miatt. További információ: [DataBox Heavy dokumentáció](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>4. fázis: megfelelő Windows Server-kiszolgáló kiépítése a helyszínen

Amíg megvárja az Azure-DataBox (ek) megérkezését, már megkezdheti egy vagy több olyan Windows-kiszolgáló szükségletének áttekintését, amelyeket a Azure File Sync fog használni.

* Hozzon létre egy Windows Server 2019-at a minimális 2012R2 virtuális gép vagy fizikai kiszolgálóként. A Windows Server feladatátvételi fürt is támogatott.
* Hozzon létre vagy adjon hozzá közvetlenül csatlakoztatott tárolót (a DAS-t a NAS-hez képest, amely nem támogatott).

A telepített Windows Server erőforrás-konfigurációja (számítás és RAM) a szinkronizálni kívánt elemek (fájlok és mappák) számától függ. Ha bármilyen probléma merül fel, nagyobb teljesítmény-konfiguráció javasolt.

[Megtudhatja, hogyan méretezhető a Windows Server a szinkronizálni kívánt elemek (fájlok és mappák) száma alapján.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> A korábban csatolt cikk egy olyan táblázatot mutat be, amely a kiszolgáló memóriájának (RAM) tartományát tartalmazza. A kiszolgáló kisebb számára irányíthatja a kiszolgálót, de várhatóan a kezdeti szinkronizálás sokkal több időt vehet igénybe.

## <a name="phase-5-copy-files-onto-your-databox"></a>5. fázis: fájlok másolása a DataBox

Amikor a DataBox megérkezik, be kell állítania a DataBox a NAS-berendezésben. Kövesse a megrendelt DataBox-típus telepítési dokumentációját.

* [A Data Box beállítása](../../databox/data-box-quickstart-portal.md)
* [A Data Box Disk beállítása](../../databox/data-box-disk-quickstart-portal.md)
* [A Data Box Heavy beállítása](../../databox/data-box-heavy-quickstart-portal.md)

A DataBox típusától függően lehetséges, hogy az Ön számára elérhetővé DataBox a másolási eszközök. Ezen a ponton nem ajánlott áttelepítést végezni az Azure-fájlmegosztás számára, mivel nem másolják a fájlokat teljes hűséggel a DataBox. Használja helyette a RoboCopy parancsot.

Amikor a DataBox megérkezik, a rendelés időpontjában megadott összes Storage-fiókhoz előre kiosztott SMB-megosztások lesznek elérhetők.

* Ha a fájlok egy prémium szintű Azure-fájlmegosztás részét képezik, a prémium szintű "file Storage" Storage-fiókban egy SMB-megosztás lesz.
* Ha a fájlok standard Storage-fiókba kerülnek, akkor a standard szintű (GPv1 és GPv2) Storage-fiókban három SMB-megosztás lesz. A Migrálás esetében csak a záró fájlmegosztás szükséges `_AzFiles` . Hagyja figyelmen kívül a blokk-és az oldal blob-megosztásait.

Kövesse az Azure DataBox dokumentációjának lépéseit:

1. [Csatlakozás a Data Boxhoz](../../databox/data-box-deploy-copy-data.md)
1. Adatok másolása a Data Boxra
1. [A DataBox előkészítése az Azure-ba való távozásra](../../databox/data-box-deploy-picked-up.md)

A csatolt DataBox dokumentációja a RoboCopy parancsot adja meg. A parancs azonban nem alkalmas a teljes fájl és a mappa megbízhatóságának megőrzésére. Használja inkább ezt a parancsot:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>6. fázis: a Azure File Sync felhőalapú erőforrás üzembe helyezése

Mielőtt folytatná az útmutatót, várjon, amíg az összes fájl megérkezett a megfelelő Azure-fájlmegosztás számára. A DataBox-adatmennyiség szállításának és betöltésének folyamata időt vesz igénybe.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>7. fázis: az Azure File Sync-ügynök üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>8. fázis: Azure File Sync konfigurálása a Windows Serveren

A regisztrált helyszíni Windows Servernek késznek kell lennie az internethez való csatlakozásra ehhez a folyamathoz.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Kapcsolja be a Cloud rétegű funkciót, és válassza a "csak névtér" lehetőséget a kezdeti Letöltés szakaszban.

> [!IMPORTANT]
> A felhő-rétegek az AFS szolgáltatás, amely lehetővé teszi, hogy a helyi kiszolgáló kevesebb tárolókapacitással rendelkezzen, mint amennyit a felhőben tárol, de a teljes névtér elérhetővé válik. Helyileg érdekes adat a gyors elérés érdekében helyileg is gyorsítótárazható. A felhőalapú rétegek a Azure File Sync "kiszolgálói végpont" választható funkciói. Ezt a funkciót akkor kell használni, ha nem rendelkezik elegendő helyi merevlemezzel a Windows Serveren az összes Felhőbeli adatok tárolásához, és ha el szeretné kerülni az összes felhőből származó adatok letöltését.

Ismételje meg a szinkronizálási csoport létrehozásának lépéseit és a megfelelő kiszolgáló mappa hozzáadását kiszolgálói végpontként az összes Azure-fájlmegosztás/-kiszolgáló helye számára, amelyet a szinkronizáláshoz kell konfigurálni. Várjon, amíg a névtér szinkronizálása befejeződik. A következő szakasz részletesen ismerteti, hogy miként biztosítható a szolgáltatás.

> [!NOTE]
> A kiszolgálói végpont létrehozását követően a szinkronizálás működik. A szinkronizálásnak azonban fel kell sorolnia a DataBox-ben áthelyezett fájlokat és mappákat az Azure-fájlmegosztás használatával. A névtér méretétől függően ez jelentős időt vehet igénybe, mielőtt a felhő névtére megjelenjen a kiszolgálón.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>9. fázis: Várjon, amíg a névtér teljes mértékben megjelenjen a kiszolgálón

Fontos, hogy várjon az áttelepítés minden további lépésével, hogy a kiszolgáló teljes mértékben letöltötte a névteret a Felhőbeli megosztásról. Ha túl korán kezdi a fájlok áthelyezését a kiszolgálóra, a szükségtelen feltöltések és a fájl-szinkronizálási ütközések kockázata is fennáll.

Ha meg szeretné tudni, hogy a kiszolgáló befejezte-e a letöltést, nyissa meg Eseménynapló a szinkronizált Windows Server kiszolgálón, és használja a Azure File Sync telemetria-eseménynaplót.
Az telemetria-Eseménynapló az alkalmazások és a Services\Microsoft\FileSync\Agent. területen található Eseménynapló

Keresse meg a legutóbbi 9102 eseményt. A 9102-es AZONOSÍTÓJÚ esemény a szinkronizálási munkamenet befejeződése után van naplózva. Az itt található esemény szövege a letöltés szinkronizálásának irányát tartalmazó mező. ( `HResult` nulla és fájlok letöltése is szükséges).
 
Két egymást követő eseményt szeretne látni, amelyek alapján megállapítható, hogy a kiszolgáló befejezte a névtér letöltését. Rendben van, ha a 2 9102-események között különböző események vannak elégetése.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>10. fázis: a felzárkózás RoboCopy a NAS-ból

Ha a kiszolgáló befejezte a teljes névtér kezdeti szinkronizálását a Felhőbeli megosztásból, folytathatja ezt a lépést. Ennek a lépésnek a folytatása előtt fontos, hogy ez a lépés befejeződjön. A részletekért olvassa el az előző szakaszt.

Ebben a lépésben a RoboCopy-feladatokat fogja futtatni a Felhőbeli megosztások a legújabb módosításaival való elfogásához, mivel a megosztások a DataBox való elágazásakor.
Ez a felzárkózó RoboCopy gyorsan vagy eltarthat egy ideig, attól függően, hogy mekkora a hálózati hozzáférés-megosztáson történt forgalom.

> [!WARNING]
> A Windows Server 2019-es romlott RoboCopy-viselkedése miatt a RoboCopy/MIR kapcsolója nem kompatibilis a lépcsőzetes célként megadott könyvtárral. A Migrálás ezen fázisában nem használhatja a Windows Server 2019-es vagy Windows 10-ügyfelet. Használja a RoboCopy szolgáltatást egy köztes Windows Server 2016-es porton.

Az alapszintű áttelepítési módszer egy RoboCopy a NAS-készülékről a Windows Serverre, és Azure File Sync az Azure-fájlmegosztást.

Futtassa az első helyi másolatot a Windows Server célmappájában:

1. Azonosítsa a NAS-berendezés első helyét.
1. Azonosítsa a Windows Server megfelelő mappáját, amely már konfigurálva van Azure File Sync.
1. A másolás elindítása a RoboCopy használatával

A következő RoboCopy parancs csak a különbségeket (frissített fájlokat és mappákat) másolja át a NAS-tárolóból a Windows Server célmappába. A Windows Server ezután szinkronizálja őket az Azure-fájlmegosztás (ok) hoz. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Ha kevesebb tárterületet telepített a Windows-kiszolgálón, mint amennyit a fájlok felvesznek a NAS-készülékre, akkor konfigurálta a Felhőbeli adatmennyiséget. Mivel a helyi Windows Server-kötet betelik, a [Felhőbeli rétegek](storage-sync-cloud-tiering-overview.md) beindulnak, és a már sikeresen szinkronizált fájlokat. A Felhőbeli rétegek létrehozásához elegendő hely áll rendelkezésre, hogy továbbra is a NAS-berendezésből folytassa a másolást. A Felhőbeli rétegek ellenőrzése óránként egyszer megtekintheti, hogy mi szinkronizált, és szabadítson fel lemezterületet a 99%-os mennyiségű szabad terület eléréséhez.
Lehetséges, hogy a RoboCopy eszköznek számos fájlt kell áthelyeznie, több, mint a Windows Server helyi tárterülete. Átlagosan előfordulhat, hogy a RoboCopy sokkal gyorsabban mozog, mint Azure File Sync feltöltheti a fájljait, és a helyi köteten is megadhatja őket. A RoboCopy sikertelen lesz. Azt javasoljuk, hogy a megosztásokat egy olyan sorozatban hajtsa meg, amely meggátolja a működését. Ha például nem indítja el a RoboCopy-feladatokat az összes megosztáshoz, vagy csak olyan megosztásokat helyez át, amelyek megfelelnek a Windows Server jelenlegi szabad területének, néhányat említve. A jó hír az, hogy a/MIR kapcsoló csak a változásokat helyezi át, és a változás után egy újraindított feladatoknak nem kell újra áthelyeznie ezt a fájlt.

### <a name="user-cut-over"></a>Felhasználó kivágása

Amikor első alkalommal futtatja a RoboCopy parancsot, a felhasználók és az alkalmazások továbbra is hozzáférhetnek a NAS-fájlokhoz, és potenciálisan megváltoztathatják azokat. Lehetséges, hogy a RoboCopy egy könyvtárat dolgoz fel, a következőre lép, majd egy felhasználó a forrás helye (NAS) egy olyan fájlt ad hozzá, módosít vagy töröl, amely most nem lesz feldolgozva ebben a RoboCopy-futtatásban. Ez várt működés.

Az első futtatás arról szól, hogy az áthelyezett információk nagy részét a Windows Serverre és a felhőbe szeretné áthelyezni Azure File Syncon keresztül. Ez az első másolat hosszú időt is igénybe vehet, attól függően, hogy:

* a feltöltési sávszélesség
* a helyi hálózati sebesség és a szám, hogy az optimálisan hány RoboCopy-szálnak felel meg
* a RoboCopy és a Azure File Sync által feldolgozandó elemek (fájlok és mappák) száma

A kezdeti Futtatás befejezése után futtassa újra a parancsot.

Ha egy második alkalommal futtatja a RoboCopy-t ugyanarra a megosztásra, az gyorsabb lesz, mert csak az utolsó Futtatás óta történt módosításokat kell továbbítania. Ugyanazzal a megosztással ismétlődő feladatokat is futtathat.

Ha figyelembe veszi az állásidőt, akkor el kell távolítania a felhasználói hozzáférést a NAS-alapú megosztásokhoz. Ezt megteheti bármely olyan lépéssel, amely megakadályozza, hogy a felhasználók megváltoztassák a fájl-és mappák struktúráját és tartalmát. Egy példa arra, hogy a DFS-Namespace egy nem létező helyre mutasson, vagy módosítsa a gyökér ACL-jeit a megosztáson.

Futtasson egy utolsó RoboCopy kört. Felveszi a módosításokat, amelyek esetleg kimaradtak.
Az utolsó lépés elvégzésének időtartama a RoboCopy vizsgálat sebességétől függ. A korábbi Futtatás időtartamának mérésével megbecsülheti az időt (amely az állásidővel egyenlő).

Hozzon létre egy megosztást a Windows Server mappában, és módosítsa a DFS-N központi telepítését úgy, hogy mutasson rá. Ügyeljen arra, hogy ugyanazokat a megosztási szintű engedélyeket adja meg, mint a NAS SMB-megosztás. Ha nagyvállalati szintű tartományhoz csatlakoztatott NAS-kiszolgálóval rendelkezett, akkor a felhasználói biztonsági azonosítók automatikusan egyeznek, ahogy a felhasználók szerepelnek Active Directoryban, és a RoboCopy teljes hűséggel másolja a fájlokat és a metaadatokat. Ha helyi felhasználókat használt a NAS-on, újra létre kell hoznia ezeket a felhasználókat a Windows Server helyi felhasználóként, és le kell képeznie a meglévő SID-ket a Windows Serverre az új, Windows Server helyi felhasználók biztonsági azonosítói között.

Elvégezte a megosztások/csoportok egy közös gyökerébe vagy kötetbe való áttelepítését. (Az 1. fázisból származó leképezéstől függően)

A másolatok közül néhányat párhuzamosan is futtathat. Javasoljuk, hogy egyszerre egy Azure-fájlmegosztás hatókörét dolgozza fel.

## <a name="troubleshoot"></a>Hibaelhárítás

A legvalószínűbb probléma az, hogy a RoboCopy parancs a Windows Server oldalon a *"teljes kötet"* művelettel meghiúsul. A felhő-rétegek a szinkronizált helyi Windows Server-lemezről óránként egyszer elürítik a tartalmat. A cél az, hogy eléri a 99%-os szabad területet a köteten.

A szinkronizálási folyamat és a Felhőbeli rétegek felszabadítása szabad lemezterületet szabadít fel. Megfigyelheti, hogy a Fájlkezelőben a Windows Serveren.

Ha a Windows-kiszolgáló elegendő rendelkezésre álló kapacitással rendelkezik, a parancs újbóli futtatása megoldja a problémát. Ha ezt a helyzetet tapasztalja, nem szakítja meg a biztonságot, és nyugodtan mozoghat. A parancs futtatásának kellemetlensége az egyetlen következmény.

A következő szakaszban található hivatkozásra kattintva megtudhatja, hogyan hibaelhárítási Azure File Sync problémákat.

## <a name="next-steps"></a>Következő lépések

További információ az Azure-fájlmegosztás és a Azure File Sync. A következő cikkek segítséget nyújtanak a speciális beállítások, az ajánlott eljárások és a hibaelhárítással kapcsolatos súgó megismerésében. Ezek a cikkek szükség szerint az [Azure file share-dokumentációra](storage-files-introduction.md) mutató hivatkozást tartalmaznak.

* [Migrálás áttekintése](storage-files-migration-overview.md)
* [Az AFS áttekintése](./storage-sync-files-planning.md)
* [Az AFS telepítési útmutatója](./storage-how-to-create-file-share.md)
* [AFS – hibaelhárítás](storage-sync-files-troubleshoot.md)
