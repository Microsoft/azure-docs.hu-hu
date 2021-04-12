---
title: Helyszíni NAS-áttelepítés az Azure file shares szolgáltatásba
description: Ismerje meg, hogyan telepítheti át a fájlokat egy helyszíni hálózati tároló (NAS) helyről az Azure-fájlmegosztás Azure DataBox való használatával.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 666e9f01d090acf29b8013470ed0264cd83f6d47
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293634"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>A DataBox használata hálózati csatlakoztatott tárolóról (NAS) az Azure-fájlmegosztásba való Migrálás céljából

Ez az áttelepítési cikk a kulcsszavas NAS és az Azure DataBox egyike. Ellenőrizze, hogy a jelen cikk a forgatókönyvre vonatkozik-e:

> [!div class="checklist"]
> * Adatforrás: hálózati csatlakoztatott tároló (NAS)
> * Áttelepítési útvonal: NAS &rArr; DataBox &rArr; Azure-fájlmegosztás
> * A helyszíni gyorsítótárazási fájlok: nem, a végső cél az Azure-fájlmegosztás közvetlen használata a felhőben. Azure File Sync használatára nincs terv.

Ha a forgatókönyv eltérő, tekintse át az [áttelepítési útmutatók táblázatát](storage-files-migration-overview.md#migration-guides).

Ebből a cikkből megtudhatja, hogyan lehet teljes körűen áttelepíteni a NAS-készülékről a funkcionális Azure-fájlmegosztás számára szükséges tervezési, üzembe helyezési és hálózati konfigurációkat. Ez az útmutató az Azure DataBox-t használja tömeges adatátvitelhez (offline adatátvitelhez).

## <a name="migration-goals"></a>Migrálási célok

A cél az, hogy áthelyezi a NAS-készüléken lévő megosztásokat az Azure-ba, és natív Azure-fájlmegosztásként használhassa azokat, amelyeket anélkül használhat, hogy Windows Serverre lenne szükség. Ezt az áttelepítést olyan módon kell végrehajtani, amely garantálja az adatáttelepítés során a termelési adatok és a rendelkezésre állás integritását. Az utóbbi megköveteli, hogy a leállások minimálisra kerüljenek, így az csak kis mértékben meghaladhatja a normál karbantartási időszakokat.

## <a name="migration-overview"></a>Migrálás áttekintése

Az áttelepítési folyamat több fázisból áll. Üzembe kell helyeznie az Azure Storage-fiókokat és-megosztásokat, konfigurálnia kell a hálózatkezelést, át kell telepíteni az Azure DataBox-t, meg kell adnia a változásokat a RoboCopy segítségével, végül pedig a felhasználókat az újonnan létrehozott Azure-fájlmegosztás számára. A következő szakaszok részletesen ismertetik az áttelepítési folyamat szakaszait.

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

## <a name="phase-4-provision-a-temporary-windows-server"></a>4. fázis: ideiglenes Windows-kiszolgáló kiépítése

Amíg megvárja az Azure-DataBox (ek) megérkezését, már üzembe helyezhet egy vagy több Windows-kiszolgálót, amelyekre szüksége lesz a RoboCopy-feladatok futtatásához. 

- Ezeknek a kiszolgálóknak a első használata a fájlok másolása a DataBox.
- Ezeknek a kiszolgálóknak a második használata a DataBox a NAS-berendezésen történt módosításaival együtt történik. Ez a módszer a forrás oldalán lévő állásidőt minimálisra tartja.

A RoboCopy-feladatok működésének sebessége főleg a következő tényezőktől függ:

* IOPS a forrás-és cél tárolón
* a közöttük elérhető hálózati sávszélesség </br> További részleteket a hibaelhárítási szakaszban talál: [IOPS és sávszélességgel kapcsolatos megfontolások](#iops-and-bandwidth-considerations)
* a névtérben lévő fájlok és mappák gyors feldolgozásának lehetősége </br> További részleteket a hibaelhárítási szakaszban talál: [feldolgozási sebesség](#processing-speed)
* a RoboCopy futtatása közötti változások száma </br> További részleteket a hibaelhárítási szakaszban talál: a [szükségtelen munka elkerülése](#avoid-unnecessary-work)

Fontos, hogy az ideiglenes Windows-kiszolgáló (k) számára a RAM és a szálak számának meghatározásakor vegye figyelembe a hivatkozott részleteket.

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>5. fázis: Felkészülés az Azure-fájlmegosztás használatára

Az idő megtakarításához folytassa ezt a fázist, amíg meg nem érkezik a DataBox. Az ebben a fázisban található információk alapján eldöntheti, hogy az Azure-beli és az Azure-on kívüli kiszolgálók és felhasználók hogyan használhatják az Azure-fájlmegosztás használatát. A legfontosabb döntések a következők:

- **Hálózatkezelés:** Engedélyezze a hálózatoknak az SMB-forgalom útválasztását.
- **Hitelesítés:** Konfigurálja az Azure Storage-fiókokat a Kerberos-hitelesítéshez. A AdConnect és tartományhoz való csatlakozás lehetővé teszi az alkalmazások és a felhasználók számára, hogy az AD-identitást használják a hitelesítéshez
- **Engedélyezés:** Az egyes Azure-fájlmegosztás megosztási szintű ACL-jei lehetővé teszik, hogy az AD-felhasználók és-csoportok hozzáférjenek egy adott megosztáshoz és egy Azure-fájlmegosztás keretén belül, a natív NTFS ACL-ek átveszik azt. A fájl-és mappa ACL-jei alapján történő engedélyezés ugyanúgy működik, mint a helyszíni SMB-megosztásokhoz.
- **Üzletmenet folytonossága:** Az Azure-fájlmegosztás meglévő környezetbe való integrálása gyakran magában foglalja a meglévő megosztási címek megőrzését. Ha még nem használja az elosztott fájlrendszerbeli névtereket, érdemes lehet létrehoznia azt a környezetében. Megtarthatja a megosztási címek használatát a felhasználók és a parancsfájlok számára, változatlanul. A DFS-N elosztott fájlrendszerbeli névtér-útválasztási szolgáltatásként való használata az áttelepítés után átirányítja DFS-Namespace-tárolókat az Azure-fájlmegosztás számára.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Ebből a videóból megtudhatja, hogyan teheti lehetővé az Azure-fájlmegosztás közvetlen biztonságos kihelyezését az információkkal dolgozó szakemberek és alkalmazások számára öt egyszerű lépésben.</br>
        A videó az egyes témakörökre vonatkozó dedikált dokumentációra hivatkozik:

* [Az identitások áttekintése](storage-files-active-directory-overview.md)
* [A tartományhoz való csatlakozás egy Storage-fiókhoz](storage-files-identity-auth-active-directory-enable.md)
* [Az Azure-fájlmegosztás hálózatkezelésének áttekintése](storage-files-networking-overview.md)
* [Nyilvános és magánhálózati végpontok konfigurálása](storage-files-networking-endpoints.md)
* [S2S VPN konfigurálása](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN konfigurálása](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN konfigurálása](storage-files-configure-p2s-vpn-linux.md)
* [A DNS-továbbítás konfigurálása](storage-files-networking-dns.md)
* [Az elosztott fájlrendszer konfigurálása – N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>6. fázis: fájlok másolása a DataBox

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

## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>7. fázis: a felzárkózás RoboCopy a NAS-ból

Ha a DataBox azt jelzi, hogy az összes fájl és mappa el lett helyezve a tervezett Azure-fájlmegosztásba, folytathatja ezt a szakaszt.
A felzárkózás RoboCopy csak akkor szükséges, ha az DataBox-példány elindítása óta előfordulhat, hogy a NAS-beli adatmennyiség módosult. Bizonyos forgatókönyvekben, amelyekben megosztást használ archiválás céljára, előfordulhat, hogy az áttelepítés befejeződése után le tudja állítani a megosztás módosításait a NAS kiszolgálón. Az üzleti igényeket úgy is kiszolgálhatja, hogy a NAS-megosztásokat csak olvashatóra állítja az áttelepítés során.

Olyan esetekben, ahol szükség van egy megosztásra az áttelepítés során, és csak kisméretű állásidőt tud felvenni, akkor ez a felzárkózás RoboCopy lépése fontos, mielőtt a felhasználói hozzáférés feladatátvétele közvetlenül az Azure-fájlmegosztás része legyen.

Ebben a lépésben a RoboCopy-feladatokat fogja futtatni a Felhőbeli megosztások a legújabb módosításaival való elfogásához, mivel a megosztások a DataBox való elágazásakor.
Ez a felzárkózó RoboCopy gyorsan vagy eltarthat egy ideig, attól függően, hogy mekkora a hálózati hozzáférés-megosztáson történt forgalom.

Futtassa az első helyi másolatot a Windows Server célmappájában:

1. Azonosítsa a NAS-berendezés első helyét.
1. Azonosítsa a megfelelő Azure-fájlmegosztást.
1. Az Azure-fájlmegosztás csatlakoztatása helyi hálózati meghajtóként az ideiglenes Windows-kiszolgálón
1. Indítsa el a másolást a RoboCopy használatával a következő módon

### <a name="mounting-an-azure-file-share"></a>Azure-fájlmegosztás csatlakoztatása

A RoboCopy használata előtt elérhetővé kell tennie az Azure-fájlmegosztást az SMB protokollon keresztül. A legegyszerűbb módszer, ha a megosztást helyi hálózati meghajtóként csatlakoztatja a Windows Serverhez, amelyet a RoboCopy használatához tervez. 

> [!IMPORTANT]
> Ahhoz, hogy sikeresen csatlakoztatjon egy Azure-fájlmegosztást egy helyi Windows Server-kiszolgálóhoz, befejezett szakaszt kell végrehajtania: Felkészülés az Azure-fájlmegosztás használatára!

Ha elkészült, tekintse át az [Azure-fájlmegosztás használata a Windows-](storage-how-to-use-files-windows.md) ban című cikket, és csatlakoztassa az Azure-fájlmegosztást, amelyen el szeretné indítani a NAS-beli felzárkózás Robocopy-t.

### <a name="robocopy"></a>RoboCopy

A következő RoboCopy parancs csak a különbségeket (frissített fájlokat és mappákat) másolja a NAS-tárolóból az Azure-fájlmegosztás felé. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> [Tekintse meg a hibaelhárítás szakaszt](#troubleshoot) , ha a Robocopy hatással van az éles környezetre, és sok hibát jelez, vagy nem a vártnál gyorsabban halad.

### <a name="user-cut-over"></a>Felhasználó kivágása

Amikor első alkalommal futtatja a RoboCopy parancsot, a felhasználók és az alkalmazások továbbra is hozzáférhetnek a NAS-fájlokhoz, és potenciálisan megváltoztathatják azokat. Lehetséges, hogy a RoboCopy egy könyvtárat dolgoz fel, a következőre lép, majd egy felhasználó a forrás helye (NAS) egy olyan fájlt ad hozzá, módosít vagy töröl, amely most nem lesz feldolgozva ebben a RoboCopy-futtatásban. Ez várt működés.

Az első futtatás arról szól, hogy az áthelyezett információk nagy részét az Azure-fájlmegosztás felé mozgatja. Ez az első másolat eltarthat egy ideig. Tekintse meg a [hibaelhárítási szakaszt](#troubleshoot) , és ismerkedjen meg a Robocopy sebességét befolyásoló további információkkal.

A kezdeti Futtatás befejezése után futtassa újra a parancsot.

Ha egy második alkalommal futtatja a RoboCopy-t ugyanarra a megosztásra, az gyorsabb lesz, mert csak az utolsó Futtatás óta történt módosításokat kell továbbítania. Ugyanazzal a megosztással ismétlődő feladatokat is futtathat.

Ha figyelembe veszi az állásidőt, akkor el kell távolítania a felhasználói hozzáférést a NAS-alapú megosztásokhoz. Ezt megteheti bármely olyan lépéssel, amely megakadályozza, hogy a felhasználók megváltoztassák a fájl-és mappák struktúráját és tartalmát. Egy példa arra, hogy a DFS-Namespace egy nem létező helyre mutasson, vagy módosítsa a gyökér ACL-jeit a megosztáson.

Futtasson egy utolsó RoboCopy kört. Felveszi a módosításokat, amelyek esetleg kimaradtak.
Az utolsó lépés elvégzésének időtartama a RoboCopy vizsgálat sebességétől függ. A korábbi Futtatás időtartamának mérésével megbecsülheti az időt (amely az állásidővel egyenlő).

Hozzon létre egy megosztást a Windows Server mappában, és módosítsa a DFS-N központi telepítését úgy, hogy mutasson rá. Ügyeljen arra, hogy ugyanazokat a megosztási szintű engedélyeket adja meg, mint a NAS SMB-megosztás. Ha nagyvállalati szintű tartományhoz csatlakoztatott NAS-kiszolgálóval rendelkezett, akkor a felhasználói biztonsági azonosítók automatikusan egyeznek, ahogy a felhasználók szerepelnek Active Directoryban, és a RoboCopy teljes hűséggel másolja a fájlokat és a metaadatokat. Ha helyi felhasználókat használt a NAS-on, újra létre kell hoznia ezeket a felhasználókat a Windows Server helyi felhasználóként, és le kell képeznie a meglévő SID-ket a Windows Serverre az új, Windows Server helyi felhasználók biztonsági azonosítói között.

Elvégezte a megosztások/csoportok egy közös gyökerébe vagy kötetbe való áttelepítését. (Az 1. fázisból származó leképezéstől függően)

A másolatok közül néhányat párhuzamosan is futtathat. Javasoljuk, hogy egyszerre egy Azure-fájlmegosztás hatókörét dolgozza fel.

## <a name="troubleshoot"></a>Hibaelhárítás

Egy adott RoboCopy futtatásának sebessége és sikerességi aránya több tényezőtől függ:

* IOPS a forrás-és cél tárolón
* a közöttük elérhető hálózati sávszélesség
* a névtérben lévő fájlok és mappák gyors feldolgozásának lehetősége
* a RoboCopy futtatása közötti változások száma


### <a name="iops-and-bandwidth-considerations"></a>IOPS és sávszélességgel kapcsolatos megfontolások

Ebben a kategóriában meg kell fontolnia a **forrás** (a NAS), a **cél** (Azure DataBox és újabb Azure-fájlmegosztás) képességeit, valamint a hozzájuk csatlakozó **hálózatot** . A lehetséges maximális átviteli sebességet a három összetevő leglassabb értéke határozza meg. A standard szintű DataBox kettős 10 GB/s hálózati adaptereket tartalmaz. A NAS-től függően lehetséges, hogy meg tudja feleltetni a következőt:. Győződjön meg arról, hogy a hálózati infrastruktúra úgy van konfigurálva, hogy az optimális átviteli sebességet támogassa a legjobb képességeinek megfelelően.

> [!CAUTION]
> A lehető leggyorsabban a lehető leghamarabb másolja a helyi hálózat és a NAS berendezés kihasználtságát más, gyakran üzleti szempontból kritikus fontosságú feladatokhoz.

Előfordulhat, hogy a lehető leggyorsabban másolja a másolást, ha fennáll a kockázata annak, hogy az áttelepítés kisajátíthatja a rendelkezésre álló erőforrásokat.

* Gondolja át, hogy mikor érdemes a környezetében áttelepítést futtatni: napközben, munkaidőn kívül vagy hétvégén.
* Vegye fontolóra a hálózatkezelési QoS-t a Windows Serveren a RoboCopy sebességének szabályozására, és ezáltal a NAS és a hálózat hatására.
* Kerülje az áttelepítési eszközök szükségtelen működését.

A RobCopy önmagában is képes a csomagok közötti késések beszúrására a `/IPG:n` `n` Robocopy-csomagok közötti ezredmásodpercben mért kapcsoló megadásával. Ezzel a kapcsolóval elkerülhetők az erőforrások monopolizálása az i/o korlátozott NAS-eszközökön és a magas kihasználtságú hálózati kapcsolatokon keresztül. 

`/IPG:n` nem használható pontos hálózati sávszélesség-szabályozáshoz egy adott Mbps-hez. Használja helyette a Windows Server Network QoS szolgáltatást. A RoboCopy teljes mértékben az SMB protokollra támaszkodik az összes hálózatra vonatkozóan, így nem tudja befolyásolni a hálózat átviteli sebességét, de lelassíthatja annak kihasználtságát. 

Hasonló, a NAS-on megfigyelt IOPS is érvényes. A fürt mérete a NAS-köteten, a csomagok mérete és más tényezők tömbje befolyásolja a megfigyelt IOPS. A csomagok közötti késleltetés bevezetése gyakran a legegyszerűbb módszer a NAS terhelésének szabályozására. Több érték tesztelése, például körülbelül 20 ezredmásodperc (n = 20) a többszörösére, hogy megtekintse, hogy mennyi késleltetéssel teszi lehetővé a többi követelmény kiszolgálását, miközben a RoboCopy sebességét maximálisan megtarthatja a megkötések esetében.

### <a name="processing-speed"></a>Feldolgozási sebesség

A RoboCopy áthalad a névtéren, és kiértékeli az összes fájlt és mappát a másoláshoz. A rendszer minden fájlt kiértékel a kezdeti másolat során, például egy másolatot a helyi hálózaton keresztül egy DataBox, és még az Azure-fájlmegosztás számára a WAN-kapcsolaton keresztül is.

Gyakran az alapértelmezett érték a sávszélesség megtervezése az áttelepítés leginkább korlátozó tényezője – és ez igaz lehet. A névtér enumerálásának lehetősége azonban befolyásolhatja, hogy a nagyobb méretű, kisebb fájlokkal rendelkező névterek esetében a teljes idő még nagyobb legyen. Vegye figyelembe, hogy a kisméretű fájlok 1 TiB-as verziójának másolása sokkal hosszabb időt vesz igénybe, mint a kevesebb, de nagyobb fájlok 1 TiB-as másolása.

Ennek a különbségnek az az oka, hogy a feldolgozási teljesítmény szükséges a névtér átjárásához. A RoboCopy támogatja a többszálas másolást a `/MT:n` (z) paraméterrel, ahol n a processzor-szálak számát jelenti. Tehát amikor kifejezetten a RoboCopy számára helyez üzembe egy gépet, vegye figyelembe a processzor-magok számát és az általa megadott szálak számával létesített kapcsolatukat. A leggyakoribb két szál egy mag esetében. A gép alapvető és szálak száma fontos adatpontnak számít, hogy eldöntse, milyen többszálas értékeket `/MT:n` kell megadnia. Azt is vegye figyelembe, hogy hány RoboCopy-feladat fut párhuzamosan egy adott gépen.

Több szálat is gyorsan átmásoljuk a kisebb fájlok 1Tib, mint a kevesebb szál. Ugyanakkor a nagy mennyiségű fájl 1Tib egyre csökken a befektetések megtérülése. A rendszer továbbra is gyorsabban másolja a hozzárendelt szálakat, de valószínűbb, hogy a hálózati sávszélesség vagy az IO korlátozott.

### <a name="avoid-unnecessary-work"></a>A szükségtelen munka elkerülése

Kerülje el a névtér nagy léptékű módosításait. Ez magában foglalja a fájlok könyvtárak közötti áthelyezését, a tulajdonságok nagy méretekben történő módosítását vagy az engedélyek módosítását (NTFS ACL), mert gyakran kaszkádolt változás lép fel, ha a mappa ACL-jei a megosztás gyökeréhez közelebb vannak módosítva. A következmények a következőket okozhatják:

* kibővített RoboCopy-feladatok futási ideje az ACL-változás által érintett fájlok és mappák frissítésének szükségessége miatt
* az első helyen a DataBox használatának hatékonysága csökkenhet, ha a mappák struktúrái megváltoznak, miután a fájlok egy DataBox lettek másolva. A RoboCopy-feladatok nem tudják "lejátszani" a névtér változását, ezért az Azure-fájlmegosztás felé szállított fájlokat ki kell üríteni, és újra fel kell tölteni a fájlokat az új mappa struktúrájába az Azure-ba.

Egy másik fontos szempont a RoboCopy eszköz hatékony használata. Az ajánlott RoboCopy-szkripttel a hibákat tartalmazó naplófájlt fog létrehozni és menteni. A másolási hibák előfordulhatnak – ez normális. Ezek a hibák gyakran szükségessé teszik, hogy egy másolási eszköz (például a RoboCopy) több fordulóját futtassák. Egy kezdeti Futtatás, például a NAS-ról a DataBox-re, és egy vagy több extra a/MIR kapcsolóval, hogy megszerezze a nem másolt fájlokat, és próbálkozzon újra.

Fel kell készülnie arra, hogy a RoboCopy több fordulóját futtassuk egy adott névtér-hatókörön. Az egymást követő futtatások gyorsabban futnak, ahogy a másolásuk kevesebb, de a névtér feldolgozásának gyorsasága egyre nagyobb mértékben korlátozott. Ha több kört futtat, felgyorsíthatja az egyes körök felgyorsítását, mivel a RoboCopy-t nem kell megismételni, hogy az első kísérlet során mindent másoljon. Ezek a RoboCopy-kapcsolók jelentős különbséget tehetnek:

* `/R:n` n = milyen gyakran próbálkozzon újra egy hibás fájl másolásával és 
* `/W:n` n = hány másodpercig kell várni az újrapróbálkozások között

`/R:5 /W:5` egy ésszerű beállítás, amelyet tetszés szerint módosíthat. Ebben a példában a rendszer ötször újrapróbálkozik egy hibás fájllal, és az újrapróbálkozások közötti öt másodperces várakozási idővel. Ha a fájl továbbra sem másolható, a következő RoboCopy-feladattal újra próbálkozik, és gyakran meghiúsulnak a fájlok, mert azok használatban vannak, vagy az időtúllépési problémák miatt előfordulhat, hogy a rendszer sikeresen átmásolja a fájlt.


## <a name="next-steps"></a>Következő lépések

További információ az Azure-fájlmegosztás használatáról. A következő cikkek segítséget nyújtanak a speciális beállítások, az ajánlott eljárások és a hibaelhárítással kapcsolatos súgó megismerésében. Ezek a cikkek szükség szerint az [Azure file share-dokumentációra](storage-files-introduction.md) mutató hivatkozást tartalmaznak.

* [Migrálás áttekintése](storage-files-migration-overview.md)
* [Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [A közvetlen hozzáféréssel kapcsolatos hálózati megfontolások](storage-files-networking-overview.md)
* [Biztonsági mentés: Azure file share-Pillanatképek](storage-snapshots-files.md)
