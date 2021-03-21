---
title: Linux-áttelepítés Azure File Sync
description: Megtudhatja, hogyan telepítheti át a fájlokat egy Linux-kiszolgálóról egy hibrid Felhőbeli üzembe helyezésre Azure File Sync és Azure-fájlmegosztás használatával.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: ff26318cafdf493579961fc718643f831ae9efeb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564254"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrálás Linuxról hibrid Felhőbeli üzembe helyezésre Azure File Sync

Ez az áttelepítési cikk a többek között az NFS és a Azure File Sync kulcsszavakat is magában foglalja. Ellenőrizze, hogy a jelen cikk a forgatókönyvre vonatkozik-e:

> [!div class="checklist"]
> * Adatforrás: hálózati csatlakoztatott tároló (NAS)
> * Áttelepítési útvonal: linuxos kiszolgáló a SAMBA &rArr; Windows Server 2012R2 vagy újabb &rArr; szinkronizálás az Azure-fájlmegosztás (ok) val
> * Helyi gyorsítótárazási fájlok: igen, a végső cél egy Azure File Sync üzemelő példány.

Ha a forgatókönyv eltérő, tekintse át az [áttelepítési útmutatók táblázatát](storage-files-migration-overview.md#migration-guides).

A Azure File Sync a közvetlenül csatlakoztatott tárolóval (DAS) rendelkező Windows Server-példányokon működik. Nem támogatja a Linux-ügyfelek vagy a távoli SMB-megosztások, illetve a hálózati fájlrendszer (NFS) megosztások közötti szinkronizálást.

Ennek eredményeképpen a Fájlszolgáltatások hibrid telepítésre való átalakítása a Windows Serverre való áttelepítést teszi szükségessé. Ez a cikk végigvezeti Önt az áttelepítés megtervezésén és végrehajtásán.

## <a name="migration-goals"></a>Migrálási célok

A cél a linuxos Samba-kiszolgálón lévő megosztások áthelyezése egy Windows Server-példányra. Ezután használja a Azure File Synct a hibrid Felhőbeli üzembe helyezéshez. Ezt az áttelepítést olyan módon kell végrehajtani, amely garantálja az adatáttelepítés során a termelési adatok és a rendelkezésre állás integritását. Az utóbbi megköveteli, hogy a leállások minimálisra kerüljenek, így az csak kis mértékben meghaladhatja a normál karbantartási időszakokat.

## <a name="migration-overview"></a>Migrálás áttekintése

Ahogy azt a Azure Files [áttelepítésének áttekintése című cikkben](storage-files-migration-overview.md)említettük, fontos a megfelelő másolási eszköz és megközelítés használata. A Linux Samba-kiszolgáló az SMB-megosztásokat közvetlenül a helyi hálózaton teszi közzé. A Windows Server rendszerbe épített Robocopy a legjobb módszer a fájlok áthelyezésére ebben az áttelepítési forgatókönyvben.

Ha nem futtatja a Samba-t a Linux-kiszolgálón, és inkább a Windows Server hibrid telepítésére szeretné telepíteni a mappákat, a Robocopy helyett a linuxos másolási eszközöket is használhatja. Vegye figyelembe a másolási eszköz hűségi képességeit. Tekintse át az áttelepítési [alapismeretek szakaszt](storage-files-migration-overview.md#migration-basics) az áttelepítés áttekintése című cikkben, amelyből megtudhatja, mit kell keresni a másolási eszközben.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1. fázis: annak meghatározása, hogy hány Azure-fájlmegosztás szükséges

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>2. fázis: megfelelő Windows Server-példány kiépítése a helyszínen

* Hozzon létre egy Windows Server 2019-példányt virtuális géppel vagy fizikai kiszolgálóként. A minimális követelmény a Windows Server 2012 R2. A Windows Server feladatátvevő fürtök is támogatottak.
* Közvetlenül csatlakoztatott tároló (DAS) kiépítése vagy hozzáadása. A hálózati csatolású tároló (NAS) nem támogatott.

  Az Ön által kiépített tárterület mérete kisebb lehet, mint amit jelenleg a linuxos Samba-kiszolgálón használ, ha a Azure File Sync [Cloud rétegű](storage-sync-cloud-tiering-overview.md) funkciót használja. 

A kiépített tárterület mérete kisebb lehet, mint amit jelenleg a linuxos Samba-kiszolgálón használ. Ehhez a konfigurációhoz az Azure file syncs [Cloud rétegű](storage-sync-cloud-tiering-overview.md) szolgáltatást is használni kell. Ha azonban egy későbbi fázisban másolja a fájlokat a nagyobb Linux Samba-kiszolgáló területéről a kisebb Windows Server-kötetre, akkor a kötegekben kell működnie:

  1. Helyezze át a lemezre illeszkedő fájlok készletét.
  2. A fájlok szinkronizálása és a Felhőbeli rétegek bevonása.
  3. Ha a köteten több szabad terület jön létre, folytassa a következő batch-fájllal. Másik megoldásként tekintse át a RoboCopy parancsot a közelgő [Robocopy szakaszban](#phase-7-robocopy) az új `/LFSM` kapcsoló használatához. A használatával `/LFSM` jelentősen egyszerűsítheti a Robocopy-feladatokat, de nem kompatibilis más Robocopy-kapcsolókkal, amelyektől függ.
    
  Ezt a Batch-módszert elkerülheti azzal, hogy kiépíti az azzal egyenértékű helyet a Windows Server-példányon, amelyet a fájlok a Linux Samba-kiszolgálón foglalnak el. Érdemes lehet a Windowsban engedélyezni a deduplikálás szolgáltatást. Ha nem szeretné véglegesen véglegesíteni ezt a nagy mennyiségű tárterületet a Windows Server-példány számára, csökkentheti a kötet méretét az áttelepítés után, és a felhő-előállítási házirendek módosítása előtt. Az Azure-fájlmegosztás kisebb helyszíni gyorsítótárát hozza létre.

A telepített Windows Server-példány erőforrás-konfigurációja (számítás és RAM) a szinkronizálni kívánt elemek (fájlok és mappák) számától függ. Ha bármilyen probléma merül fel, javasoljuk, hogy nagyobb teljesítményű konfigurációt végezzen.

[Megtudhatja, hogyan méretezhető a Windows Server-példány a szinkronizálni kívánt elemek (fájlok és mappák) száma alapján.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> A korábban csatolt cikk egy olyan táblázatot mutat be, amely a kiszolgáló memóriájának (RAM) tartományát tartalmazza. A kiszolgáló kisebb számának irányába irányíthatja a kiszolgálót, de a kezdeti szinkronizálás várhatóan jóval több időt vehet igénybe.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>3. fázis: a Azure File Sync felhőalapú erőforrás üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>4. fázis: az Azure Storage-erőforrások üzembe helyezése

Ebben a fázisban az 1. fázisban található leképezési táblázat alapján kell kiépíteni az Azure Storage-fiókok és-megosztások megfelelő számát.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>5. fázis: az Azure File Sync-ügynök üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>6. fázis: Azure File Sync konfigurálása a Windows Server-telepítésben

A regisztrált helyszíni Windows Server-példánynak készen kell állnia, és az internethez kell csatlakoznia ehhez a folyamathoz.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> A felhő-rétegek a Azure File Sync funkció, amely lehetővé teszi, hogy a helyi kiszolgáló kevesebb tárolókapacitással rendelkezzen, mint amennyit a felhőben tárolnak, de a teljes névtér elérhető. Helyileg érdekes adat a gyors elérés érdekében helyileg is gyorsítótárazható. A felhőalapú rétegek az egyes Azure File Sync kiszolgálói végpontok választható funkciói.

> [!WARNING]
> Ha kevesebb tárterületet telepített a Windows Server-köteteken, mint a Linux Samba-kiszolgálón használt adatai, akkor a Felhőbeli rétegek kiosztása kötelező. Ha nem kapcsolja be a Felhőbeli rétegek bekapcsolását, akkor a kiszolgáló nem szabadít fel lemezterületet az összes fájl tárolásához. Állítsa be az áttelepítéshez ideiglenesen az 99%-os rendelkezésre állási házirendet a kötethez. A Migrálás befejezése után térjen vissza a Felhőbeli rétegek beállításaihoz, és a házirendet a hosszú távú hasznos szintre állítsa be.

Ismételje meg a szinkronizálási csoport létrehozásának lépéseit és a megfelelő kiszolgáló mappa hozzáadását kiszolgálói végpontként minden olyan Azure-fájlmegosztás és-kiszolgáló esetében, amelyet a szinkronizáláshoz kell konfigurálni.

Az összes kiszolgálói végpont létrehozása után a szinkronizálás működik. Létrehozhat egy tesztoldalt, és megtekintheti a kiszolgáló helyéről a csatlakoztatott Azure-fájlmegosztás (a szinkronizálási csoport Felhőbeli végpontja által leírtak szerint) szinkronizálását.

A két helyszín, a kiszolgáló mappái és az Azure-fájlmegosztás nem üresek, és az adatra várnak. A következő lépésben átmásolja a fájlokat a Windows Server-példányba Azure File Sync a felhőbe való áthelyezéshez. Ha engedélyezte a Felhőbeli rétegek használatát, a kiszolgáló ekkor elkezdi a rétegek fájljait, ha a helyi kötetek kapacitása kifogyott.

## <a name="phase-7-robocopy"></a>7. fázis: Robocopy

Az alapszintű áttelepítési módszer a Robocopy használata a fájlok másolásához és a Azure File Sync használata a szinkronizálás végrehajtásához.

Futtassa az első helyi másolatot a Windows Server célmappájában:

1. Azonosítsa a Linux Samba-kiszolgáló első helyét.
1. Azonosítsa a megfelelő mappát azon a Windows Server-példányon, amelyen már konfigurálva van Azure File Sync.
1. Indítsa el a példányt a Robocopy használatával.

A következő Robocopy parancs a Linux rendszerű Samba-kiszolgáló tárterületéről másolja a fájlokat a Windows Server célmappába. A Windows Server szinkronizálja az Azure-fájlmegosztást. 

Ha kevesebb tárterületet telepített a Windows Server-példányon, mint amennyit a fájlok felvesznek a Linux Samba-kiszolgálón, akkor konfigurálta a Felhőbeli rétegek létrehozását. Ahogy a helyi Windows Server-kötet betelik, a [Felhőbeli rétegek](storage-sync-cloud-tiering-overview.md) elindulnak, és a már sikeresen szinkronizált fájlokat is elindítják. A Felhőbeli rétegek kiszolgálása elég helyet eredményez a Linux Samba-kiszolgálóról történő másolás folytatásához. A Felhőbeli rétegek ellenőrzése óránként egyszer megtekintheti, hogy mi szinkronizált, és szabadítson fel lemezterületet a kötet 99%-os szabad területére vonatkozó szabályzat eléréséhez.

Lehetséges, hogy a Robocopy gyorsabban helyezi át a fájlokat, mint amennyire a felhőbe és a szintjére tud szinkronizálni, így elfogyhat a helyi lemezterület. A Robocopy ekkor sikertelen lesz. Azt javasoljuk, hogy a megosztásokat egy olyan sorozatban hajtsa át, amely megakadályozza a problémát. Tegyük fel például, hogy nem indítja el a Robocopy-feladatokat egyszerre az összes megosztáshoz. Vagy vegye fontolóra a Windows Server-példányon aktuálisan rendelkezésre álló szabad területhez illeszkedő megosztások áthelyezését. Ha a Robocopy feladat nem sikerül, akkor mindig futtassa újra a parancsot, ha a következő tükrözési/kiürítési lehetőséget használja:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>8. fázis: felhasználói kivágás

Amikor első alkalommal futtatja a Robocopy parancsot, a felhasználók és az alkalmazások továbbra is hozzáférhetnek a Linux Samba-kiszolgálón lévő fájlokhoz, és esetleg megváltoznak. Lehetséges, hogy a Robocopy feldolgozta a könyvtárat, és továbblép a következőre, majd a forrás helye (Linux) egyik felhasználója olyan fájlt vesz fel, módosít vagy töröl, amely már nem lesz feldolgozva ebben az aktuális Robocopy-futtatásban. Ez várt működés.

Az első futtatás arról szól, hogy az adatmennyiséget a Windows Server-példányba és a felhőbe helyezi át Azure File Syncon keresztül. Ez az első másolat hosszú időt is igénybe vehet, attól függően, hogy:

* A letöltési sávszélesség.
* A feltöltési sávszélesség.
* A helyi hálózati sebesség, valamint a Robocopy szálak számának optimális számának a megegyezőnek kell lennie.
* A Robocopy és Azure File Sync által feldolgozandó elemek (fájlok és mappák) száma.

A kezdeti Futtatás befejezése után futtassa újra a parancsot.

A második alkalommal gyorsabban fejeződik be, mert csak az utolsó Futtatás óta végrehajtott módosításokat kell továbbítania. Ebben a másodpercben az új módosítások futtatása továbbra is felhalmozható.

Ismételje meg ezt a folyamatot, amíg meggyőződött arról, hogy egy adott helyhez tartozó Robocopy művelet végrehajtásához szükséges idő egy elfogadható ablakban van az állásidőhöz.

Ha figyelembe veszi az állásidőt, és készen áll arra, hogy offline állapotba hozza a linuxos helyet, módosíthatja a megosztási gyökér ACL-jeit úgy, hogy a felhasználók már nem férhetnek hozzá a helyhez. Vagy bármilyen más megfelelő lépést is megtehet, amely megakadályozza, hogy a tartalom ne változzon meg a Linux-kiszolgálón ebben a mappában.

Futtasson egy utolsó Robocopy kört. Felveszi az esetlegesen kihagyott módosításokat. Az utolsó lépés elvégzésének időtartama a Robocopy vizsgálat sebességétől függ. A korábbi Futtatás időtartamának mérésével megbecsülheti az időt (amely az állásidővel egyenlő).

Hozzon létre egy megosztást a Windows Server mappában, és módosítsa a DFS-N központi telepítését úgy, hogy mutasson rá. Ügyeljen arra, hogy ugyanazt a megosztási szintű engedélyeket adja meg, mint a Linux Samba-kiszolgáló SMB-megosztásait. Ha a linuxos Samba-kiszolgálón helyi felhasználókat használt, ezeket a felhasználókat újra létre kell hoznia Windows Server helyi felhasználóként. Le kell képeznie azokat a meglévő biztonsági azonosítókat is, amelyeket a Robocopy áthelyezett a Windows Server-példányra az új Windows Server-beli helyi felhasználók biztonsági azonosítói között. Ha Active Directory fiókokat és hozzáférés-vezérlési listákat használt, a Robocopy a következőképpen lesz áthelyezve, és nincs szükség további műveletre.

Elvégezte egy megosztás vagy megosztások egy csoportjának egy közös gyökérbe vagy kötetre való áttelepítését (az 1. fázistól függően).

A másolatok közül néhányat párhuzamosan is futtathat. Javasoljuk, hogy egyszerre egy Azure-fájlmegosztás hatókörét dolgozza fel.

> [!WARNING]
> Miután áthelyezte a Linux Samba-kiszolgálóról a Windows Server-példányra, és az áttelepítés elkészült, térjen vissza a Azure Portal *összes*  szinkronizálási csoportjához. Állítsa be a Felhőbeli rétegű kötetek szabad területének százalékos arányát a gyorsítótár kihasználtságának megfelelő értékre, például 20 százalékra. 

A felhő-rétegek kötetének szabad területére vonatkozó szabályzat a kötet szintjén működik, és lehetséges, hogy több kiszolgálói végpont is szinkronizálja azt. Ha nem szeretné, hogy a szabad terület még egy kiszolgálói végponton is módosítható legyen, a szinkronizálás továbbra is alkalmazza a legszigorúbb szabályt, és a szabad lemezterület 99%-ban való megtartására tesz kísérletet. Előfordulhat, hogy a helyi gyorsítótárat a rendszer nem a várt módon hajtja végre. A teljesítmény elfogadható lehet, ha a cél az, hogy egy olyan kötethez tartozó névtér legyen, amely csak ritkán használt archivált adatmennyiséget tartalmaz, és egy másik forgatókönyv esetén a tárterület többi részét is fenntartja.

## <a name="troubleshoot"></a>Hibaelhárítás

A leggyakoribb probléma az, hogy a Robocopy parancs a Windows Server oldalon **megtelt kötettel** meghiúsul. A felhő-rétegek a szinkronizált helyi Windows Server-lemezről minden órában óránként egyszer elürítik a tartalmat. A cél az, hogy a köteten 99%-os szabad terület legyen elérhető.

A szinkronizálási folyamat és a Felhőbeli rétegek felszabadítása szabad lemezterületet szabadít fel. Megfigyelheti, hogy a Fájlkezelőben a Windows Serveren.

Ha a Windows Server-példány elegendő rendelkezésre álló kapacitással rendelkezik, a parancs újbóli futtatása megoldja a problémát. Ha ezt a helyzetet tapasztalja, semmi sem szakad, és nyugodtan haladhat. A parancs futtatásának kellemetlensége az egyetlen következmény.

A következő szakaszban található hivatkozásra kattintva megtudhatja, hogyan hibaelhárítási Azure File Sync problémákat.

## <a name="next-steps"></a>Következő lépések

További információ az Azure-fájlmegosztás és a Azure File Sync. A következő cikkek speciális beállításokat, ajánlott eljárásokat és hibaelhárítási segítséget tartalmaznak. Ezek a cikkek szükség szerint az [Azure file share-dokumentációra](storage-files-introduction.md) mutató hivatkozást tartalmaznak.

* [Azure File Sync áttekintése](./storage-sync-files-planning.md)
* [Azure File Sync telepítési útmutató](./storage-how-to-create-file-share.md)
* [Azure File Sync-hibaelhárítás](storage-sync-files-troubleshoot.md)