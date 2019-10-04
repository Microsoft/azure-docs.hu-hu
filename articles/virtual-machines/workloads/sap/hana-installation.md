---
title: Az Azure-beli SAP HANA SAP HANA telepítése (nagyméretű példányok) | Microsoft Docs
description: SAP HANA telepítése Azure-beli SAP HANA (nagyméretű példányok esetén).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebf4a0f892e65bf96e07e333cf5446d3036108a0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099792"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>SAP HANA (nagyméretű példányok) telepítése és konfigurálása az Azure-ban

A cikk elolvasása előtt megismerheti a [Hana nagyméretű példányainak általános kifejezéseit](hana-know-terms.md) és a [Hana nagyméretű példányainak SKU](hana-available-skus.md)-t.

A SAP HANA telepítése az Ön felelőssége. Az Azure-beli virtuális hálózatok és a HANA nagyméretű példány-egység (ek) közötti kapcsolat létrehozása után megkezdheti az új SAP HANA telepítését az Azure-beli (nagyméretű példányok) kiszolgálón. 

> [!Note]
> SAP-házirend esetén a SAP HANA telepítését olyan személynek kell végrehajtania, aki megfelelt a Certified SAP Technology Associate vizsgának, SAP HANA a telepítési tanúsítási vizsgának, vagy egy SAP-tanúsítvánnyal rendelkező rendszerintegrátor (SI).

A HANA 2,0 telepítésének tervezésekor tekintse [meg az SAP-támogatási Megjegyzés #2235581 – SAP HANA: A támogatott operációs](https://launchpad.support.sap.com/#/notes/2235581/E) rendszerek gondoskodnak arról, hogy az operációs rendszer támogatott legyen a telepítendő SAP HANA kiadásban. A HANA 2,0 támogatott operációs rendszere szigorúbb, mint a HANA 1,0 támogatott operációs rendszere. 

> [!IMPORTANT] 
> A II típusú egységek esetében jelenleg csak a SLES 12 SP2 operációsrendszer-verzió támogatott. 

A HANA telepítésének megkezdése előtt ellenőrizze az alábbiakat:
- [HLI egység (ek)](#validate-the-hana-large-instance-units)
- [Operációs rendszer konfigurációja](#operating-system)
- [Hálózati konfiguráció](#networking)
- [Tároló konfigurálása](#storage)


## <a name="validate-the-hana-large-instance-units"></a>A HANA nagyméretű példány-egység (ek) ellenőrzése

Miután megkapta a HANA nagyméretű példányának egységét a Microsofttól, ellenőrizze a következő beállításokat, és szükség szerint módosítsa azt.

Az **első lépés** a HANA nagyméretű példányának megérkezése és a példányok elérésének és kapcsolatának létrehozása után a Azure Portal, hogy a példány (ok) megjelenik-e a megfelelő SKU-k és operációs rendszer Azure Portal. Olvassa el az [Azure HANA nagyméretű példányok vezérlését Azure Portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal) az ellenőrzések végrehajtásához szükséges lépésekhez.

A **második lépés** a HANA nagyméretű példányának megérkezése és a példányok elérésének és kapcsolatának létrehozása után a példány operációs rendszerének regisztrálása az operációs rendszer szolgáltatójával. Ez a lépés tartalmazza a SUSE Linux operációs rendszernek az Azure-beli virtuális gépen üzembe helyezett SUSE SMT-példányban való regisztrálását. 

A HANA nagyméretű példány egysége csatlakozhat ehhez az SMT-példányhoz. (További információ: az [SMT-kiszolgáló beállítása SUSE Linux](hana-setup-smt.md)rendszerhez). Azt is megteheti, hogy a Red Hat operációs rendszernek regisztrálnia kell a Red Hat előfizetés-kezelővel, amelyhez csatlakoznia kell. További információkért tekintse [meg a mi SAP HANA az Azure-ban (nagyméretű példányok)](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakörben található megjegyzéseket. 

Ez a lépés az operációs rendszer javításához szükséges, amely az ügyfél feladata. A SUSE-es verzióban keresse meg az SMT telepítéséhez és konfigurálásához szükséges [](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)dokumentációt ezen az oldalon.

A **harmadik lépés** az adott operációsrendszer-kiadás/-verzió új javításának és javításának keresése. Ellenőrizze, hogy a HANA nagyméretű példányának javítási szintje a legújabb állapotban van-e. Előfordulhatnak olyan esetek, amikor a legújabb javítások nem szerepelnek benne. A HANA nagyméretű példányok egységének átvétele után meg kell vizsgálni, hogy szükséges-e a javítások alkalmazása.

A **negyedik lépés** a SAP HANA telepítésére és konfigurálására vonatkozó SAP-megjegyzések megkeresése az adott operációsrendszer-kiadáson/verzión. Az egyes telepítési forgatókönyvekre vonatkozó javaslatok vagy SAP-megjegyzések vagy konfigurációk módosítása miatt a Microsoft nem mindig képes tökéletesen beállítani a HANA nagyméretű példányok egységét. 

Ezért az ügyfélnek kell elolvasnia az SAP HANAhoz kapcsolódó SAP-megjegyzéseket a Linux-kiadás pontos kiadása érdekében. Ellenőrizze az operációs rendszer kiadása/verziója konfigurációját is, és ha még nem tette meg, alkalmazza a konfigurációs beállításokat.

Pontosan vizsgálja meg a következő paramétereket, és végül a következőhöz igazodva:

- net. Core. rmem_max = 16777216
- net. Core. wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net. IPv4. TCP _wmem = 65536 16777216 16777216

A SLES12 SP1 és a RHEL 7,2 verziótól kezdődően ezeket a paramétereket be kell állítani egy konfigurációs fájlban a/etc/sysctl.d könyvtárban. Például létre kell hozni egy 91-NetApp-HANA. conf nevű konfigurációs fájlt. A régebbi SLES és RHEL kiadások esetében ezeket a paramétereket a/etc/sysctl. conf fájlban kell megadni.

A RHEL 6,3-től kezdődően az összes RHEL-kiadás esetében vegye figyelembe a következőket: 
- A sunrpc. TCP _slot_table_entries = 128 paramétert a/etc/modprobe. d/sunrpc-local. conf fájlban kell megadni. Ha a fájl nem létezik, először létre kell hoznia a bejegyzést a következő bejegyzés hozzáadásával: 
    - beállítások sunrpc tcp_max_slot_table_entries = 128

Az **ötödik lépés** a HANA nagyméretű példány-egység rendszeridejének ellenõrzése. A példányok rendszer-időzónával vannak telepítve. Ez az időzóna annak az Azure-régiónak a helyét jelöli, amelyben a HANA nagyméretű példányának bélyegzője található. Módosíthatja a saját példányok rendszeridejét vagy időzónáját. 

Ha több példányt rendel a bérlőhöz, akkor módosítania kell az újonnan leszállított példányok időzónáját. A Microsoft nem tekinti át az átadást követő példányokkal beállított időzónát. Így előfordulhat, hogy az újonnan üzembe helyezett példányok nem állíthatók be ugyanabban az időzónában, mint a módosította. A felhasználó feladata, hogy szükség esetén módosítsa az átadott példány (ok) időzónáját. 

A **hatodik lépés** az etc/hosts szolgáltatás megkeresése. Ahogy a pengék átadása megtörténik, különböző IP-címekkel rendelkeznek, amelyek különböző célokra vannak hozzárendelve. Keresse meg az etc/hosts fájlt. Ha az egységeket egy meglévő bérlőhöz adja hozzá, nem várható, hogy az újonnan telepített rendszerek etc/gazdagépei megfelelően vannak karbantartva a korábban továbbított rendszerek IP-címeivel. Az ügyfél feladata, hogy az újonnan üzembe helyezett példányok kommunikálhatnak és feloldják a bérlőn korábban üzembe helyezett egységek nevét. 


## <a name="operating-system"></a>Operációs rendszer

> [!IMPORTANT] 
> A II típusú egységek esetében jelenleg csak a SLES 12 SP2 operációs rendszer verziója támogatott. 

A továbbított operációsrendszer-rendszerkép swap-területe 2 GB-ra van beállítva az [SAP-támogatási Megjegyzés #1999997 – gyakori kérdések: SAP HANA memória](https://launchpad.support.sap.com/#/notes/1999997/E). Ha más beállítást szeretne használni, saját magának kell megadnia.

Az [SAP-alkalmazások SUSE Linux Enterprise Server 12 SP1](https://www.suse.com/products/sles-for-sap/download/) az Azure-ban (nagyméretű példányok) SAP HANA telepített Linux-disztribúció. Ez az adott terjesztés SAP-specifikus képességeket biztosít "kívülről" (beleértve az SAP on SLES hatékony futtatására szolgáló előre beállított paramétereket is).

A SLES (beleértve a magas rendelkezésre állást és a biztonsági megerősítő szolgáltatást is) a SUSE webhelyén és az [SAP-on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) -ben található [Resource Library/White Papers](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) (az sap közösségi hálózata SAP HANA) az SAP-műveletekre és egyebekre vonatkozik.)

A következő további és hasznos SAP a SUSE-hez kapcsolódó hivatkozásokat tartalmaz:

- [SAP HANA SUSE Linux-helyen](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Ajánlott eljárások az SAP-hez: Sorba helyezni-replikáció – SAP NetWeaver a SUSE Linux Enterprise 12 rendszeren](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – SLES VÍRUSVÉDELMI SAP-](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) hoz (beleértve az SLES 12 for SAP-alkalmazásokhoz)

A következő SAP-támogatási megjegyzések a 12. SLES SAP HANA megvalósítására alkalmazhatók:

- [SAP-támogatás Megjegyzés #1944799 – SAP HANA irányelvek a SLES operációs rendszer telepítéséhez](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [SAP-támogatás Megjegyzés #2205917 – SAP HANA DB ajánlott operációsrendszer-beállítások az SLES 12 for SAP-alkalmazásokhoz](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP-támogatás Megjegyzés #1984787 – SUSE Linux Enterprise Server 12: telepítési megjegyzések](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP-támogatás Megjegyzés #171356 – SAP szoftver Linux rendszeren:  Általános információk](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP-támogatás Megjegyzés #1391070 – Linux UUID-megoldások](https://launchpad.support.sap.com/#/notes/1391070)

A [SAP HANA Red Hat Enterprise Linux](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) egy másik ajánlat, amellyel a SAP HANA a HANA nagyméretű példányain futtathatja. A 6,7-es és a 7,2-es RHEL-kiadások elérhetők. Vegye figyelembe, hogy az olyan natív Azure-beli virtuális gépekkel szemben, ahol csak a RHEL 7,2 és újabb kiadásai támogatottak, a HANA Large-példányok támogatják a RHEL 6,7-et is. Javasoljuk azonban, hogy használjon RHEL 7. x kiadást.

A következő további hasznos SAP on Red Hat kapcsolódó hivatkozásokat tartalmaz:
- [A Red Hat Linux-webhelyen SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

A következő SAP-támogatási megjegyzések a Red Hat SAP HANA megvalósítására alkalmazhatók:

- [SAP-támogatás Megjegyzés #2009879-SAP HANA Red Hat Enterprise Linux (RHEL) operációs rendszerre vonatkozó irányelvek](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP-támogatás Megjegyzés #2292690-SAP HANA DB: Ajánlott operációsrendszer-beállítások a RHEL 7 rendszerhez](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP-támogatás Megjegyzés #2247020-SAP HANA DB: A RHEL 6,7 ajánlott operációsrendszer-beállításai](https://launchpad.support.sap.com/#/notes/2247020)
- [SAP-támogatás Megjegyzés #1391070 – Linux UUID-megoldások](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP-támogatás Megjegyzés #2228351 – Linux: SAP HANA Database SPS 11 változat 110 (vagy újabb) a RHEL 6 vagy SLES 11 rendszeren](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP-támogatás Megjegyzés #2397039 – gyakori kérdések: SAP on RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP-támogatás Megjegyzés #1496410-Red Hat Enterprise Linux 6. x: Telepítés és frissítés](https://launchpad.support.sap.com/#/notes/1496410)
- [SAP-támogatás Megjegyzés #2002167-Red Hat Enterprise Linux 7. x: Telepítés és frissítés](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Idő szinkronizálása

Az SAP NetWeaver architektúrára épülő SAP-alkalmazások érzékenyek az SAP-rendszer részét képező különböző összetevők időbeli eltérésére. Az SAP ABAP rövid memóriaképei, amelyekben a ZDATE\_nagy\_idő\_diff neve hibás, valószínűleg ismerősek. Ennek az az oka, hogy ezek a rövid memóriaképek akkor jelennek meg, ha a különböző kiszolgálók vagy virtuális gépek rendszerideje túl távol sodródik egymástól.

SAP HANA az Azure-ban (nagyméretű példányok) az Azure-ban végzett időszinkronizálás nem vonatkozik a nagyméretű példányokban lévő számítási egységekre. Ez a szinkronizálás nem alkalmazható natív Azure-beli virtuális gépeken futó SAP-alkalmazások futtatására, mert az Azure biztosítja, hogy a rendszer időben szinkronizálva legyen. 

Ennek eredményeképpen be kell állítania egy külön időkiszolgálót, amelyet az Azure-beli virtuális gépeken futó SAP-alkalmazások és a HANA nagyméretű példányokon futó SAP HANA adatbázis-példányok használhatnak. A nagyméretű példányokban tárolt tárolási infrastruktúra időközben szinkronizálva van az NTP-kiszolgálókkal.


## <a name="networking"></a>Hálózat
Feltételezzük, hogy követte az Azure-beli virtuális hálózatok megtervezésének és a virtuális hálózatok a HANA nagyméretű példányokhoz való csatlakoztatásának javaslatait, az alábbi dokumentumokban leírtak szerint:

- [SAP HANA (nagyméretű példány) áttekintése és architektúrája az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA (nagyméretű példányok) infrastruktúrája és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Néhány részletet érdemes megemlíteni az önálló egységek hálózatkezelésével kapcsolatban. Minden HANA nagyméretű példány-egység két vagy három IP-címmel rendelkezik, amelyek két vagy három NIC-porthoz vannak rendelve. Három IP-cím használatos a HANA kibővíthető konfigurációkban és a HANA rendszer replikációs forgatókönyvében. Az egység hálózati adapteréhez rendelt egyik IP-cím kívül esik a kiszolgáló IP-készletéből, amely [SAP HANA (nagyméretű példányok) áttekintésében és az Azure architektúrájában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)szerepel.

Az architektúra Ethernet-adataival kapcsolatos további információkért tekintse meg a [HLI által támogatott forgatókönyveket](hana-supported-scenario.md).

## <a name="storage"></a>Storage

Az Azure-beli SAP HANA tárolási elrendezését (nagyméretű példányok) az Azure `service management` -ban az SAP által ajánlott irányelvek alapján SAP HANA konfigurálni. Ezek az irányelvek dokumentálva vannak a [SAP HANA Storage-követelmények](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) című tanulmányban. 

A különböző HANA nagyméretű példányokkal rendelkező különféle kötetek durva méretei a [SAP HANA (nagyméretű példányok) áttekintésében és az Azure architektúrájában](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)vannak dokumentálva.

A tárolási kötetek elnevezési konvenciói az alábbi táblázatban láthatók:

| Tárterület-használat | Csatlakoztatás neve | Kötet neve | 
| --- | --- | ---|
| HANA-adathalmazok | /hana/data/SID/mnt0000\<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-napló | /hana/log/SID/mnt0000\<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA-napló biztonsági mentése | /hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA megosztott | /hana/shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/SAP | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

A *SID* a HANA-példány rendszer-azonosítója. 

Bérlő telepítésekor a *bérlő* a műveletek belső enumerálása.

A HANA usr/SAP ugyanazon a köteten osztozik. A csatolási nómenklatúrája tartalmazza a HANA-példányok rendszerazonosítóját, valamint a csatlakoztatási számot. A Felskálázási központi telepítések esetében csak egy csatlakoztatás van, például mnt00001. A kibővített üzemelő példányok esetében a több csatlakoztatási pont jelenik meg, mint a feldolgozók és a főcsomópontok. 

A kibővíthető környezetek, az adat-, a napló-és a biztonsági mentési kötetek a kibővített konfiguráció minden csomópontja számára meg vannak osztva és csatolva vannak. Több SAP-példányt tartalmazó konfigurációk esetén a rendszer egy másik készletet hoz létre, és a HANA nagyméretű példány-egységhez csatolja a köteteket. A forgatókönyv tárolási elrendezésével kapcsolatos részletekért lásd: [HLI által támogatott forgatókönyvek](hana-supported-scenario.md).

Ha egy HANA nagyméretű példány egységét tekinti át, akkor tisztában lesz azzal, hogy az egységek nagyvonalú lemezterületet biztosítanak a HANA/az adatmennyiséghez, valamint hogy van egy kötet HANA/log/Backup. A HANA/az adatmennyiség olyan nagy, hogy a tárolási Pillanatképek azt ajánljuk Önnek, hogy az ügyfél ugyanazt a lemezt használja. Minél több tárolási pillanatkép van végrehajtva, annál több helyet használ a pillanatképek a hozzárendelt tárolási köteteken. 

A HANA/log/Backup kötet nem lehet az adatbázis biztonsági másolatainak kötete. Ez a méret a HANA-tranzakciónapló biztonsági mentései biztonsági mentési kötetként használható. További információ: [SAP HANA (nagyméretű példányok) magas rendelkezésre állása és vész-helyreállítás az Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-ban. 

A megadott tárterületen kívül 1 TB-os növekményekben is vásárolhat további tárolókapacitást. Ezt a további tárhelyet új kötetekként lehet hozzáadni a HANA nagyméretű példányaihoz.

Az Azure `service management`-beli SAP HANA bevezetése során az ügyfél egy felhasználói azonosítót (UID) és csoportazonosító (GID) határoz meg a sidadm felhasználói és sapsys csoport számára (például: 1 000 500). A SAP HANA rendszer telepítése során ugyanezeket az értékeket kell használnia. Mivel több HANA-példányt szeretne üzembe helyezni egy egységen, több kötetet kap (egy készletet az egyes példányokhoz). Ennek eredményeképpen a telepítéskor meg kell határoznia a következőket:

- A különböző HANA-példányok SID-azonosítója (sidadm származik).
- A különböző HANA-példányok memóriájának mérete. A memória mérete/példánya határozza meg a kötetek méretét az egyes kötetek készletében.

A tárolási szolgáltató javaslatai alapján a következő csatlakoztatási beállítások vannak konfigurálva az összes csatlakoztatott kötethez (kivéve a rendszerindító LUN-t):

- NFS RW, vers = 4, Hard, Timeo = 600, rsize = 1048576, wsize = 1048576, intr, noatime, Lock 0 0

Ezek a csatlakoztatási pontok az/etc/fstab-ben vannak konfigurálva, ahogy az alábbi ábrán is látható:

![csatlakoztatott kötetek fstab a HANA nagyméretű példány egységében](./media/hana-installation/image1_fstab.PNG)

A DF-h parancs kimenete a S72m HANA nagyméretű példány egységében a következőképpen néz ki:

![csatlakoztatott kötetek fstab a HANA nagyméretű példány egységében](./media/hana-installation/image2_df_output.PNG)


A nagyméretű példányokban lévő tároló vezérlő és csomópontjai szinkronizálva vannak az NTP-kiszolgálókkal. Ha az Azure-ban (nagyméretű példányok) és az Azure-beli virtuális gépeken lévő SAP HANA az NTP-kiszolgálóval szinkronizálja, nem szabad jelentős időbeli eltolódást végezni az infrastruktúra és a számítási egységek között az Azure-ban vagy nagyméretű példányokban.

Az alábbi SAP HANA konfigurációs paraméterek megadásával optimalizálhatja SAP HANA az alatta lévő tárterületre:

- max_parallel_io_requests 128
- async_read_submit bekapcsolva
- async_write_submit_active on
- összes async_write_submit_blocks
 
A SAP HANA 1,0 verziójú SPS12-ig ezek a paraméterek a SAP HANA-adatbázis telepítése során állíthatók be, az [SAP HANA-adatbázis SAP-megjegyzés #2267798 – konfiguráció](https://launchpad.support.sap.com/#/notes/2267798)szakaszában leírtak szerint.

A paramétereket a SAP HANA adatbázis telepítése után is konfigurálhatja a hdbparam keretrendszer használatával. 

A HANA nagyméretű példányaiban használt tárterület fájlméret-korlátozást tartalmaz. A [méretre vonatkozó korlátozás 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) /fájl. Az EXT3 fájlrendszerbeli korlátozásokkal ellentétben a HANA nem ismeri implicit módon a HANA Large instances Storage által kényszerített tárolási korlátozást. Ennek eredményeképpen a HANA nem hoz létre automatikusan új adatfájlt, ha a 16TB elérte a fájlméretet. Mivel a HANA a 16 TB-nál nagyobb mennyiségű fájlt próbál növelni, a HANA hibát jelez, és az index-kiszolgáló összeomlik a végén.

> [!IMPORTANT]
> Annak megakadályozása érdekében, hogy a HANA a HANA nagyméretű példányok tárterületének 16 TB-os fájlméret-korlátján kívüli adatfájlokat próbáljon növelni, a következő paramétereket kell beállítania a SAP HANA Global. ini konfigurációs fájlban.
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Lásd még: SAP-Megjegyzés [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Vegye figyelembe az SAP-Megjegyzés [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


A SAP HANA 2,0 esetében a hdbparam-keretrendszer elavult. Ennek eredményeképpen a paramétereket SQL-parancsok használatával kell beállítani. További információ [: SAP Note #2399079: A hdbparam megszüntetése a HANA 2](https://launchpad.support.sap.com/#/notes/2399079)-ben.

Tekintse át a [HLI által támogatott forgatókönyveket](hana-supported-scenario.md) az architektúra tárolási elrendezésének megismeréséhez.


**Következő lépések**

- Tekintse [meg a HANA telepítését a HLI-on](hana-example-installation.md)










































 







 




