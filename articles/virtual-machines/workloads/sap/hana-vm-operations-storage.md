---
title: Azure-beli virtuális gépek tárolási konfigurációinak SAP HANAa | Microsoft Docs
description: Tárolási javaslatok a SAP HANA üzembe helyezett virtuális gépekhez.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, Storage ultrakönnyű lemez, Premium Storage
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/03/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0c0fbb1280fc2a7eaca1d97e7e016cf480873c8b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666576"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure-beli virtuális gépek tárkonfigurációi

Az Azure különböző típusú tárhelyeket biztosít, amelyek a SAP HANA rendszert futtató Azure-beli virtuális gépekhez használhatók. A **SAP HANA minősített Azure Storage-típusok** , amelyek a SAP HANA központi telepítések listájához vehetők figyelembe, például: 

- Azure Premium SSD vagy Premium Storage 
- [Ultralemez](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Az ilyen típusú lemezekkel kapcsolatos további tudnivalókért tekintse meg az [Azure tárolási típusai az SAP](./planning-guide-storage.md) számítási feladatokhoz és [a lemez típusának kiválasztása](../../disks-types.md) című cikket.

Az Azure két üzembe helyezési módszert kínál a VHD-k számára az Azure standard és a Premium Storage szolgáltatásban. Reméljük, hogy kihasználja az Azure-beli [felügyelt lemez](https://azure.microsoft.com/services/managed-disks/) előnyeit az Azure Block Storage-példányok számára. 

A IOPS és a tárolási átviteli sebességű tárolási típusok listáját és SLA-kat a [felügyelt lemezek Azure-dokumentációjában](https://azure.microsoft.com/pricing/details/managed-disks/)tekintheti meg.

> [!IMPORTANT]
> A kiválasztott Azure Storage-típustól függetlenül az adott operációs rendszer és az adatbázis-kezelő rendszer számára az SAP-nak támogatnia kell a tárterületen használt fájlrendszert. Az [SAP-támogatási megjegyzés #2972496](https://launchpad.support.sap.com/#/notes/2972496) a különböző operációs rendszerekhez és adatbázisokhoz támogatott fájlrendszerek listáját tartalmazza, beleértve a SAP HANA is. Ez minden olyan kötetre vonatkozik, SAP HANA a feladathoz való olvasáshoz és íráshoz is hozzáférhet. Kifejezetten az Azure-beli NFS-t használja a SAP HANAhoz, az NFS-verziók további korlátozásait a cikk későbbi részében leírtak szerint alkalmazza 


A különböző tárolási típusok minimális SAP HANA tanúsított feltételei a következők: 

- Az Azure Premium Storage- **/Hana/log** szükséges az Azure [írásgyorsító](../../how-to-enable-write-accelerator.md)támogatásához. A **/Hana/Data** -kötetet a Premium Storage-ra lehet helyezni az Azure írásgyorsító vagy az ultra Disk nélkül
- Legalább a **/Hana/log** -kötethez tartozó Azure Ultra Disk. A **/Hana/Data** -kötetet az Azure írásgyorsító nélküli Premium Storage-ra vagy az ultra Disk gyorsabb újraindítására lehet helyezni
- A **/Hana/log és a/hana/data** Azure NetApp Files felső részén található **NFS v 4.1** kötetek. A/Hana/Shared mennyisége NFS v3 vagy NFS v 4.1 protokollt használhat.

Egyes tárolási típusok kombinálhatók. Előfordulhat például, hogy az **/Hana/Data** -t a Premium Storage-ra helyezi, és a **/Hana/log** a szükséges kis késleltetés érdekében a lemezes tárolásra is helyezhető. Ha a **/HANA/Data** ANF alapuló kötetet használ, a  **/Hana/log** -kötetet a ANF-on felüli NFS-en kell alapulnia. A ANF-en keresztüli NFS használata az egyik kötetre (például a/Hana/Data-ra) és az Azure Premium Storage-ra, vagy a másik kötethez tartozó Ultra Disk (például **/Hana/log**) **nem támogatott**.

A helyszíni világban ritkán kell foglalkoznia az I/O-alrendszerekkel és képességeivel. Ennek az az oka, hogy a készülék gyártójának meg kell győződnie arról, hogy a minimális tárolási követelmények teljesülnek SAP HANA esetén. Ha saját maga hozza létre az Azure-infrastruktúrát, tisztában kell lennie az egyes SAP-kiállított követelményekkel. Az SAP által ajánlott minimális átviteli sebességek közül néhány:

- Írási/olvasási sebesség 250 MB/s **/Hana/log** 1 MB I/O-mérettel
- Olvasási tevékenység legalább 400 MB/s a **/Hana/Data** 16 MB és 64 MB I/O méret esetén
- Legalább 250 MB/s írási tevékenység **/Hana/Data** 16 mb és 64 MB I/O mérettel

Mivel az adatbázis-kezelő rendszerekben az alacsony tárolási késés kritikus fontosságú, még az adatbázis-kezelői rendszerek, például a SAP HANA esetében is, az adatmemóriában marad. A tárolóban a kritikus elérési út általában az adatbázis-kezelő rendszerek tranzakciónapló-írásai körébe esik. Az összeomlás utáni helyreállítást követően azonban az olyan műveletek, mint például a visszaállítási pontok írása vagy a memóriában tárolt adatok betöltése is kritikus lehet. Ezért a **/Hana/Data** és a **/Hana/log** kötetek esetében **kötelező** az Azure Premium Storage, az ultra Disk vagy a ANF használata. 


A HANA tárolási konfigurációjának kiválasztásakor a következő néhány irányadó elv látható:

- Döntse el a tároló típusát az [Azure Storage-típusok](./planning-guide-storage.md) alapján az SAP számítási feladatokhoz, és [válassza ki a lemez típusát](../../disks-types.md)
- A teljes VM I/O-átviteli sebesség és a IOPS a virtuális gép méretezése vagy meghatározása során figyelembe kell venni. A virtuálisgép-tárolók teljes átviteli sebességét a cikk a [memória-optimalizált virtuális gépek méreteit](../../sizes-memory.md) ismertető cikkben dokumentálja.
- A tárolási konfiguráció eldöntése során próbálja meg a virtuális gép teljes átviteli sebességét a **/Hana/Data** -kötet konfigurációjával. A visszaállítási pontok írása SAP HANA lehet agresszív kiállító I/o-kiadás. A mentésipont írásakor könnyen lehet leküldeni a **/Hana/Data** -kötet átviteli korlátait. Ha a **/Hana/Data** -kötetet felépítő lemez (ek) nagyobb átviteli sebességgel rendelkezik, mint amennyit a virtuális gép engedélyez, akkor olyan helyzetekben futhat, ahol a mentésipont-írás által használt átviteli sebesség ütközik az Ismétlési naplók írási követelményeivel. Olyan helyzet, amely hatással lehet az alkalmazás átviteli sebességére


> [!IMPORTANT]
> A tárolási konfigurációkra vonatkozó javaslatok a kezdéshez szükségesek. A számítási feladatok futtatása és a tárolási kihasználtsági minták elemzése során előfordulhat, hogy nem használja az összes megadott tárolási sávszélességet vagy IOPS. Érdemes megfontolni a tárterület leépítését. Ellenkező esetben előfordulhat, hogy a számítási feladathoz több tárterület-átviteli sebességre van szükség, mint amit ezekkel a konfigurációkkal javasolt. Ennek eredményeképpen előfordulhat, hogy a kapacitást, a IOPS vagy az átviteli sebességet kell telepítenie. A tárolókapacitás megkövetelt tárolási kapacitása, a tárolási késleltetés, a tárolási teljesítmény és a IOPS szükséges és a legkevésbé költséges konfiguráció terén az Azure elegendő különböző tárolási típust kínál különböző képességekkel és különböző díjszabási pontokkal, hogy megkeresse és módosítsa az Ön és a HANA számítási feladatának megfelelő kompromisszumot.


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>A Stripe-készletek és az adatmennyiség-particionálás SAP HANA
Az Azure Premium Storage használata esetén a legjobb ár/teljesítmény arányt követheti, ha a **/Hana/Data** és/vagy a **/Hana/log** -kötetet több Azure-lemez között is kiszűri. Ahelyett, hogy nagyobb méretű lemezeket helyezzen üzembe, amelyek további IOPS vagy átviteli sebességet igényelnek. Eddig a Linux részét képező LVM-és MDADM-kötet-kezelők lettek megvalósítva. A lemezek csíkozásának módszere évtizedes és jól ismert. Mivel ezek a csíkozott kötetek hasznosak, a IOPS vagy az átviteli sebességre lehet szükség, a csíkozott kötetek kezelésének bonyolultságát is felveszi. Különösen azokban az esetekben, amikor a köteteknek bővíteni kell a kapacitást. Legalább a **/Hana/Data** esetében az SAP egy alternatív módszert vezetett be, amely ugyanazt a célt éri el, mint a több Azure-lemezre kiterjedő csíkozás. Mivel SAP HANA 2,0 SPS03, a HANA IndexServer képes a különböző Azure-lemezeken található több HANA-adatfájlhoz tartozó I/O-tevékenységre. Ennek előnye, hogy nem kell gondoskodnia a csíkozott kötetek különböző Azure-lemezeken való létrehozásáról és kezeléséről. Az adatmennyiség-particionálás SAP HANA funkcióit részletesen ismerteti a következő témakörben:

- [A HANA rendszergazdai útmutatója](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog about SAP HANA – az adatkötetek particionálása](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP-Megjegyzés #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP-Megjegyzés #2700123](https://launchpad.support.sap.com/#/notes/2700123)

A részletek beolvasása nyilvánvalóvá válik, hogy a funkció kihasználása a Volume Manager-alapú csíkkészletek összetettségét veszi igénybe. Azt is tapasztalja, hogy a HANA-adatmennyiség particionálása nem csak az Azure Block Storage esetében működik, például az Azure Premium Storage szolgáltatásban. Ezt a funkciót is használhatja az NFS-megosztások közötti szalagok elérésére arra az esetre, ha ezek a megosztások IOPS vagy átviteli sebességre vonatkozó korlátozásokkal rendelkeznek.  


## <a name="linux-io-scheduler-mode"></a>Linux I/O-ütemező mód
A Linux számos különböző I/O-ütemezési módot tartalmaz. A Linux-szállítók és az SAP gyakori javaslata, hogy újrakonfigurálja az I/O-ütemező módot a lemezes kötetekhez az **MQ-alapú** vagy a **Kyber** módból a **NOOP** (nem többhelyes) vagy a **none** (többsoros) MÓDRA, ha még nem tette meg a SLES saptune-profilok. A részletekre a következők hivatkoznak: 

- [SAP-Megjegyzés #1984787](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP-Megjegyzés #2578899](https://launchpad.support.sap.com/#/notes/2578899) 
- [Probléma a NOOP beállítással a SLES 12 SP4-ben](https://www.suse.com/support/kb/doc/?id=000019547)

A Red Hat-on hagyja meg a beállításokat a különböző SAP-alkalmazásokhoz tartozó hangolási profilok által meghatározott beállítások alapján.


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Prémium szintű Storage-és Azure-írásgyorsító megoldások az Azure M sorozatú virtuális gépekhez
Az Azure írásgyorsító egy olyan funkció, amely kizárólag az Azure M sorozatú virtuális gépekhez érhető el. A név szerint a funkció célja az, hogy javítsa az írási időt az Azure Premium Storage szolgáltatásban. SAP HANA esetén a rendszer csak a **/Hana/log** köteten használja írásgyorsító. Ezért a **/Hana/Data** és a **/Hana/log** különálló kötetek, amelyek az Azure-írásgyorsító támogatják a **/Hana/log** -kötetet. 

> [!IMPORTANT]
> Az Azure Premium Storage használatakor a **/Hana/log** -kötethez tartozó Azure [írásgyorsító](../../how-to-enable-write-accelerator.md) használata kötelező. Írásgyorsító a Premium Storage és az M-Series és a Mv2-Series virtuális gépek esetében is elérhető. A írásgyorsító nem működik együtt más Azure-beli virtuálisgép-családokkal, például a Esv3 vagy a Edsv4-mel.

Az alábbi Azure Premium-lemezekre vonatkozó gyorsítótárazási javaslatok feltételezik, hogy a lista SAP HANA I/O-jellemzői:

- A HANA-adatfájlok esetében alig van bármilyen olvasási munkaterhelés. A kivételek nagy méretű I/o-k a HANA-példány újraindítása után, vagy amikor az adatfeltöltés a HANA-ba történik. Az adatfájlok elleni nagyobb olvasási I/o-t egy másik esetben a HANA-adatbázis biztonsági mentései is lehetnek. Ennek eredményeképpen az olvasási gyorsítótárazás többnyire nem logikus, mert a legtöbb esetben az összes adatfájl kötetét teljes egészében el kell olvasni. 
- Az adatfájlok írása a HANA visszaállítási pontok és a HANA összeomlás-helyreállítási szolgáltatáson alapul. A visszaállítási pontok írása aszinkron módon történik, és nem tart fenn felhasználói tranzakciókat. Az összeomlás utáni helyreállítás során az adat megírása kritikus fontosságú ahhoz, hogy a rendszer gyorsan reagáljon. Az összeomlások helyreállítása azonban kivételes helyzetekben nem lehetséges
- A HANA által visszaadott fájlokból alig történik olvasási művelet. A kivételek nagyméretű I/o-műveletek a tranzakciós naplók biztonsági másolatainak, összeomlás-helyreállításának vagy a HANA-példány újraindítási fázisának végrehajtásakor.  
- A rendszer a SAP HANA visszaállítási naplófájljának fő terhelését írja le. A számítási feladatok jellegétől függően az I/O-t 4 KB-os vagy más esetekben 1 MB vagy annál nagyobb méretű i/O-méretekkel lehet elkészíteni. A SAP HANA az Ismétlési naplóba való írási késleltetés kritikus fontosságú.
- Az összes írást megbízható módon kell megőrizni a lemezen

**Javaslat: a megfigyelt I/O-mintákat SAP HANA alapján a különböző kötetek gyorsítótárazását az Azure Premium Storage használatával kell beállítani, például:**

- **/Hana/Data** – nincs gyorsítótárazás vagy olvasási gyorsítótárazás
- **/Hana/log** – nincs gyorsítótárazás – kivétel az M-és Mv2-Series virtuális gépek esetében, amelyeknél engedélyezni kell az Azure írásgyorsító 
- **/Hana/Shared** – olvasási gyorsítótárazás
- **Operációsrendszer-lemez** – ne módosítsa az Azure által a virtuális gép létrehozási idején beállított alapértelmezett gyorsítótárazást


Ha az LVM vagy a mdadm használatával különböző Azure Premium-lemezeken hoz létre Stripe-készleteket, meg kell határoznia a sávok méretét. Ezek a méretek különböznek a **/Hana/Data** és a **/Hana/log** között. **Javaslat: a Stripe-méretek a következő javaslatokat használják:**

- 256 KB a **/Hana/Data**
- 64 KB a **/Hana/log**

> [!NOTE]
> A **/Hana/Data** szalagos mérete a korábbi javaslatoktól a 64 kb-ra vagy 128 kb-ra irányuló, a legújabb Linux 256-verziókon alapuló ügyfelek tapasztalatai alapján változott. Az 256 KB mérete valamivel jobb teljesítményt nyújt. A 32 KB-ról 64 KB-ra készült **/Hana/log** -sávokra vonatkozó javaslatot is módosítottuk, hogy elegendő átviteli sebesség legyen nagyobb I/O-méretekkel.

> [!NOTE]
> Nem kell olyan redundancia-szintet konfigurálnia, amely RAID-köteteket használ, mivel az Azure Block Storage egy virtuális merevlemez három rendszerképét tárolja. Az Azure Premium-lemezekkel rendelkező Stripe-készlet használata kizárólag a megfelelő IOPS és/vagy I/O-teljesítményt biztosító kötetek konfigurálására szolgál.

A Stripe-készlet alatt álló számos Azure virtuális merevlemez felhalmozódása egy IOPS és egy tárolási átviteli sebességi oldalról történik. Tehát, ha az Azure Premium Storage-lemezeken több mint 3 x P30-készletet helyez üzembe, akkor a IOPS háromszor kell megadnia az egyetlen Azure Premium Storage P30-lemez tárolási sebességét.

> [!IMPORTANT]
> Abban az esetben, ha az LVM vagy a mdadm-t használja a Volume Manager használatával több Azure Premium-lemezre, akkor a/Data, a/log és a/Shared három SAP HANA fájlrendszer nem helyezhető el alapértelmezett vagy gyökérszintű kötet-csoportba. Javasoljuk, hogy kövesse a Linux-szállítók útmutatását, amely általában különálló mennyiségi csoportok létrehozása a/Data, a/log és a/Shared.


### <a name="azure-burst-functionality-for-premium-storage"></a>Azure burst-funkciók a Premium Storage-hoz
Az Azure Premium Storage-lemezek esetében kisebb vagy egyenlő, mint a 512 GiB kapacitás, a burst funkció elérhető. A lemez-kitörés működésének pontos módját a [lemez kitörése](../../disk-bursting.md)című cikk ismerteti. A cikk elolvasása során megérti a IOPS és az átviteli sebesség felmerülésének koncepcióját abban az esetben, amikor az I/O-munkaterhelés a lemezek névleges IOPS és átviteli sebessége alá esik (a névleges átviteli sebességről lásd: [felügyelt lemez díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)). A IOPS és az átviteli sebesség különbözetét fogja felhalmozni a jelenlegi használat és a lemez névleges értékei között. A törések legfeljebb 30 percre vannak korlátozva.

Azok az ideális esetek, ahol a burst funkció megtervezhető, valószínűleg a különböző adatbázis-kezelő rendszer adatfájljait tartalmazó kötetek vagy lemezek lesznek. A várt I/O-számítási feladat várható a kötetek esetében, különösen kis-és közepes hatótávolságú rendszerek esetén a következőnek kell kinéznie:

- Kis-és közepes olvasási munkaterhelés, mivel az adatok ideális esetben a memóriában vannak gyorsítótárazva, vagy a HANA esetében a memóriában lévőnek kell lennie.
- Adatbázis-ellenőrzőpontok vagy visszaállítási pontok által kiváltott írások
- Olyan biztonsági mentési feladat, amely folyamatos streamet olvas be azokban az esetekben, amikor a biztonsági mentések nem a tárolási pillanatképeken keresztül futnak
- A SAP HANA a memóriába való betöltést egy példány újraindítása után

Különösen a kisebb adatbázis-kezelő rendszerek esetében, amelyekben a számítási feladatok csak néhány száz tranzakciót kezelnek, az ilyen adatfeldolgozási funkciók a tranzakciót tároló lemezek vagy kötetek számára is hasznosak lehetnek. Az ilyen lemezek vagy kötetek várható munkaterhelése a következőképpen néz ki:

- A munkaterheléstől és a számítási feladattól függő, rendszeres írás a lemezre, mivel az alkalmazás által kiadott összes véglegesített művelet valószínűleg I/O-műveletet indít el
- Nagyobb számítási feladatok az átviteli sebességben az üzemeltetési feladatok esetében, például indexek létrehozása vagy újraépítése
- Adatsorozatok beolvasása tranzakciónaplók végrehajtásakor vagy a naplók biztonsági másolatainak visszaállításakor


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Az Azure Premium Storage-on alapuló éles környezetben ajánlott tárolási megoldás

> [!IMPORTANT]
> Az Azure M sorozatú virtuális gépek SAP HANA minősítése kizárólag az Azure írásgyorsító a **/Hana/log** -kötethez. Ennek eredményeképpen az Azure M sorozatú virtuális gépeken SAP HANA üzemelő példányok üzembe helyezését az Azure írásgyorsító kell konfigurálni a **/Hana/log** -kötethez.  

> [!NOTE]
> Az Azure Premium Storage-t érintő forgatókönyvekben a rendszerindítási képességek bevezetését használjuk a konfigurációban. Ahogy bármilyen alakú vagy formájú tárolási tesztelési eszközt használ, tartsa szem előtt az [Azure Premium Disk burst](../../disk-bursting.md) eszközét. Az SAP-HWCCT vagy a HCMT eszközön keresztül továbbított tárolási tesztek futtatása nem várható, hogy az összes teszt átadja a feltételeket, mivel a tesztek némelyike túllépi a felhalmozódott feltört krediteket. Különösen akkor, ha a tesztek egymás után, megszakítás nélkül futnak.


> [!NOTE]
> Éles forgatókönyvek esetén győződjön meg arról, hogy a IAAS SAP- [dokumentációja](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)támogatja-e egy adott virtuálisgép-típus támogatását az sap által SAP HANA.

**Javaslat: az Azure Premium Storage szolgáltatással az éles környezetekben javasolt konfigurációk a következőképpen néznek ki:**

Az SAP **/Hana/Data** -kötet konfigurációja:

| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | /hana/data | Kiosztott átviteli sebesség | Adatbursás maximális átviteli sebessége | IOPS | Burst IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14 000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14 000 |
| M64ls | 512 GiB | 1 000 MBps | 4 x P10 | 400 MBps | 680 MBps | 2000 | 14 000 |
| M64s | 1 000 GiB | 1 000 MBps | 4 x P15 | 500 MBps | 680 MBps | 4 400 | 14 000 |
| M64ms | 1 750 GiB | 1 000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9 200 | 14 000 |  
| M128s | 2 000 GiB | 2 000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9 200| 14 000 | 
| M128ms | 3 800 GiB | 2 000 MBps | 4 x P30 | 800 MBps | nincs feltört | 20 000 | nincs feltört | 
| M208s_v2 | 2 850 GiB | 1 000 MBps | 4 x P30 | 800 MBps | nincs feltört | 20 000| nincs feltört | 
| M208ms_v2 | 5 700 GiB | 1 000 MBps | 4 x P40 | 1 000 MBps | nincs feltört | 30 000 | nincs feltört |
| M416s_v2 | 5 700 GiB | 2 000 MBps | 4 x P40 | 1 000 MBps | nincs feltört | 30 000 | nincs feltört |
| M416ms_v2 | 11 400 GiB | 2 000 MBps | 4 x P50 | 2 000 MBps | nincs feltört | 30 000 | nincs feltört |


A **/Hana/log** kötethez. a konfiguráció a következőképpen fog kinézni:

| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | **/Hana/log** -kötet | Kiosztott átviteli sebesség | Adatbursás maximális átviteli sebessége | IOPS | Burst IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10 500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10 500 | 
| M64ls | 512 GiB | 1 000 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10 500 | 
| M64s | 1 000 GiB | 1 000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3 300 | 10 500 | 
| M64ms | 1 750 GiB | 1 000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3 300 | 10 500 |  
| M128s | 2 000 GiB | 2 000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3 300 | 10 500|  
| M128ms | 3 800 GiB | 2 000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3 300 | 10 500 | 
| M208s_v2 | 2 850 GiB | 1 000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3 300 | 10 500 |  
| M208ms_v2 | 5 700 GiB | 1 000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3 300 | 10 500 |  
| M416s_v2 | 5 700 GiB | 2 000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3 300 | 10 500 |  
| M416ms_v2 | 11 400 GiB | 2 000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3 300 | 10 500 | 


A többi kötet esetében a konfiguráció a következőképpen fog kinézni:

| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | /hana/shared | /root-kötet | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBps | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1 000 GiB | 1 000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1 750 GiB | 1 000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2 000 GiB | 2 000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3 800 GiB | 2 000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2 850 GiB | 1 000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5 700 GiB | 1 000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5 700 GiB | 2 000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11 400 GiB | 2 000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


Győződjön meg arról, hogy a különböző javasolt kötetek tárolási átviteli sebessége megfelel-e a futtatni kívánt munkaterhelésnek. Ha a munkaterhelés nagyobb köteteket igényel a **/Hana/Data** és a **/Hana/log** számára, növelje az Azure Premium Storage virtuális merevlemezek számát. Az Azure-beli virtuálisgép-típus korlátain belül a kötetek méretezése a IOPS és az I/O-átviteli sebesség növelésével növekszik.

Az Azure írásgyorsító csak az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)szolgáltatással együtt működik. Így legalább a **/Hana/log** -kötetet alkotó Azure Premium Storage-lemezeket felügyelt lemezként kell telepíteni. Az Azure írásgyorsító részletes utasításait és korlátozásait a cikkben találja [írásgyorsító](../../how-to-enable-write-accelerator.md).

Az Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) -család HANA-tanúsítvánnyal rendelkező virtuális gépei és a [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)esetében ANF kell a **/Hana/Data** és a **/Hana/log** kötetet. Vagy az Azure Premium Storage helyett az Azure Ultra Disk Storage használatát kell kihasználnia a **/Hana/log** -kötethez. Ennek eredményeképpen az Azure Premium Storage **/Hana/Data** -kötetének konfigurációi a következőhöz hasonlóak:

| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | /hana/data | Kiosztott átviteli sebesség | Adatbursás maximális átviteli sebessége | IOPS | Burst IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10 500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  300 MBps | 510 MBps | 1500 | 10 500|
| E48ds_v4 | 384 GiB | 1 152 MBps | 3 x P15 |  375 MBps |510 MBps | 3 300  | 10 500 | 
| E64ds_v4 | 504 GiB | 1 200 MBps | 3 x P15 |  375 MBps | 510 MBps | 3 300 | 10 500 | 
| E64s_v3 | 432 GiB | 1 200 MB/s | 3 x P15 |  375 MBps | 510 MBps | 3 300 | 10 500 | 

A többi kötet esetében, beleértve az **/Hana/log** is, a konfiguráció az alábbihoz hasonló:

| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | /Hana/log-kötet | /Hana/log I/O-átviteli sebesség | /Hana/log IOPS | /hana/shared | /root-kötet | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1 152 MBps | 192 GB | 250 MBps | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1 200 MBps | 256 GB | 250 MBps | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1 200 MBps | 220 GB | 250 MBps | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Az Azure ultrakönnyű lemezes tárolásának konfigurációja SAP HANA
Egy másik Azure Storage-típus neve [Azure Ultra Disk](../../disks-types.md#ultra-disk). Az Azure Storage által kínált eddigi jelentős különbség az, hogy a lemez képességei többé nincsenek a lemez méretéhez kötve. Ügyfélként meghatározhatja ezeket a képességeket az ultra Disk számára:

- Lemez mérete 4 GiB-ról 65 536 GiB-ig
- A IOPS tartománya 100 IOPS – 160K IOPS (a maximum a virtuálisgép-típusoktól függ)
- Tárolási sebesség 300 MB/s-tól 2 000 MB/s-ig

Az ultravékony lemez lehetővé teszi, hogy egyetlen lemezt adjon meg, amely megfelel a méretének, a IOPS és a lemez átviteli sebességi tartományának. Ahelyett, hogy a logikai kötetek, például az LVM vagy a MDADM az Azure Premium Storage-on keresztül legyenek felhasználva, hogy olyan köteteket lehessen létrehozni, amelyek megfelelnek a IOPS és a Az ultra Disk és a Premium Storage közötti konfigurációs kombinációt is futtathat. Ennek eredményeképpen korlátozhatja az ultra Disk használatát a teljesítmény szempontjából kritikus fontosságú **/Hana/Data** és **/Hana/log** , valamint a többi kötetet az Azure Premium Storage használatával.

Az ultra Disk egyéb előnyei a jobb olvasási késések a Premium Storage szolgáltatáshoz képest. A gyorsabb olvasási késés előnyeit kihasználhatja a HANA indítási idejének és az adatmennyiségnek a memóriába való későbbi betöltésének csökkentése érdekében. Az ultrakönnyű lemezes tárolás előnyei is érezhetőek lehetnek, ha HANA visszaállítási pontok ír. 

> [!NOTE]
> Az ultra Disk még nem szerepel az összes Azure-régióban, és még nem támogatja az alább felsorolt virtuálisgép-típusokat. Részletes információkat talál arról, hogy mely virtuális gépek támogatottak, és milyen virtuálisgép-családokat támogatnak? tekintse meg az [Azure-ban elérhető lemez-típusok](../../disks-types.md#ultra-disk)című cikket.

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Éles környezetben ajánlott tárolási megoldás tiszta, ultra Disk konfigurációval
Ebben a konfigurációban a **/Hana/Data** és a **/Hana/log** kötetek külön maradnak. A javasolt értékek abból a KPI-ből származnak, amelyet az SAP-nak hitelesítenie kell a SAP HANA és a tárolási konfigurációk virtuálisgép-típusainak az [SAP TDI tárolási](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)tanulmányában javasolt módon.

A javaslatok gyakran meghaladják az SAP minimális követelményeit a cikkben korábban leírtak szerint. A felsorolt javaslatok az SAP méretével kapcsolatos javaslatok és a különböző virtuálisgép-típusok által biztosított maximális tárolási teljesítmény közötti kompromisszumok.

> [!NOTE]
> Az Azure Ultra Disk egy lemez IOPS legalább 2 GB-os kapacitásának kikényszerítése


| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | /Hana/Data-kötet | /Hana/Data I/O-átviteli sebesség | /Hana/Data IOPS | /Hana/log-kötet | /Hana/log I/O-átviteli sebesség | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/s | 200 GB | 400 MBps | 2500 | 80 GB | 250 MB | 1800 |
| E32ds_v4 | 256 GiB | 768 MB/s | 300 GB | 400 MBps | 2500 | 128 GB | 250 MBps | 1800 |
| E48ds_v4 | 384 GiB | 1152 MB/s | 460 GB | 400 MBps | 3,000 | 192 GB | 250 MBps | 1800 |
| E64ds_v4 | 504 GiB | 1200 MB/s | 610 GB | 400 MBps | 3,500 |  256 GB | 250 MBps | 1800 |
| E64s_v3 | 432 GiB | 1 200 MB/s | 610 GB | 400 MBps | 3,500 | 220 GB | 250 MB | 1800 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 2500 | 96 GB | 250 MBps  | 1800 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 2500 | 256 GB | 250 MBps  | 1800 |
| M64ls | 512 GiB | 1 000 MB/s | 620 GB | 400 MBps | 3,500 | 256 GB | 250 MBps  | 1800 |
| M64s | 1 000 GiB | 1 000 MB/s |  1 200 GB | 600 MBps | 5000 | 512 GB | 250 MBps  | 2500 |
| M64ms | 1 750 GiB | 1 000 MB/s | 2 100 GB | 600 MBps | 5000 | 512 GB | 250 MBps  | 2500 |
| M128s | 2 000 GiB | 2 000 MB/s |2 400 GB | 750 MBps | 7 000 | 512 GB | 250 MBps  | 2500 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 800 GB | 750 MBps |9600 | 512 GB | 250 MBps  | 2500 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 500 GB | 750 MBps | 7 000 | 512 GB | 250 MBps  | 2500 | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 200 GB | 750 MBps | 14 400 | 512 GB | 250 MBps  | 2500 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 200 GB | 1 000 MBps | 14 400 | 512 GB | 400 MBps  | 4,000 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 14 400 GB | 1 500 MBps | 28 800 | 512 GB | 400 MBps  | 4,000 |   

**A felsorolt értékek kiindulási pontként szolgálnak, és a valós igények alapján kell kiértékelni őket.** Az Azure Ultra Disk előnye, hogy a IOPS és az átviteli sebesség értékei a virtuális gép leállításának vagy a rendszeren alkalmazott számítási feladatok leállításának szükségessége nélkül módosíthatók.   

> [!NOTE]
> Eddig a tárolási Pillanatképek nem érhetők el az ultra Disk Storage szolgáltatásban. Ez blokkolja a virtuálisgép-Pillanatképek használatát Azure Backup szolgáltatásokkal


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>NFS-v 4.1 kötetek Azure NetApp Files
A HANA ANF kapcsolatos részletekért olvassa el az [NFS-v 4.1-es kötetek Azure NetApp Files a SAP HANA](./hana-vm-operations-netapp.md)




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Cost tudatos megoldás az Azure Premium Storage szolgáltatással
Eddig a jelen dokumentumban ismertetett Azure Premium Storage-megoldás a [Premium Storage és az azure írásgyorsító for Azure M sorozatú virtuális gépekre vonatkozó megoldások](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) című szakaszban szerepelt SAP HANA éles környezetben támogatott forgatókönyvek esetében. Az éles környezetben támogatott konfigurációk egyik jellemzője a kötetek elkülönítése SAP HANA adatok számára, és a naplózás megismétlése két különböző kötetre. Az ilyen elkülönítés oka, hogy a kötetek munkaterhelés-jellemzői eltérnek. A javasolt üzemi konfigurációk esetében azonban szükség lehet a különböző típusú gyorsítótárazásokra vagy az Azure Block-tárolók különböző típusaira. A nem éles környezetekben az éles rendszerekre vonatkozó megfontolások némelyike nem alkalmazható a fejlettebb, nem éles üzemi rendszerekre. Ennek eredményeképpen a HANA-adatbázis és a naplózási kötet egyesíthető. Habár végül bizonyos bűnösökkel, például az éles rendszerekhez szükséges bizonyos átviteli vagy késési KPI-kkel nem találkoznak. Az ilyen környezetekben a költségek csökkentésének egy másik aspektusa lehet az [Azure standard SSD Storage](./planning-guide-storage.md#azure-standard-ssd-storage)használata. Ne feledje, hogy az Azure Storage standard SSD vagy standard HDD kiválasztásával hatással van az egyetlen virtuálisgép-SLA-ra, ahogy az a  [Virtual Machines SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)-ban szerepel.

Az ilyen konfigurációknál a kevésbé költséges alternatíva a következőképpen néz ki:


| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | /Hana/Data és/Hana/log<br /> az LVM vagy a MDADM szalagos | /hana/shared | /root-kötet | /usr/sap | Megjegyzések |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Nem érhető el kevesebb, mint 1ms tárolási késés<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | A virtuális gép típusa nem HANA-tanúsítvánnyal rendelkezik <br /> Nem érhető el kevesebb, mint 1ms tárolási késés<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 5 000<sup>2</sup> értékre fogja korlátozni |
| E20ds_v4 | 160 GiB | 480 MB/s | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | Nem érhető el kevesebb, mint 1ms tárolási késés<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | A virtuális gép típusa nem HANA-tanúsítvánnyal rendelkezik <br /> Nem érhető el kevesebb, mint 1ms tárolási késés<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Nem érhető el kevesebb, mint 1ms tárolási késés<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 5 000<sup>2</sup> értékre fogja korlátozni |
| E48ds_v4 | 384 GiB | 1 152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Nem érhető el kevesebb, mint 1ms tárolási késés<sup>1</sup> |
| E64v3 | 432 GiB | 1 200 MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Nem érhető el kevesebb, mint 1ms tárolási késés<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Nem érhető el kevesebb, mint 1ms tárolási késés<sup>1</sup> |
| M64ls | 512 GiB | 1 000 MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 10 000<sup>2</sup> értékre fogja korlátozni |
| M64s | 1 000 GiB | 1 000 MB/s | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 10 000<sup>2</sup> értékre fogja korlátozni |
| M64ms | 1 750 GiB | 1 000 MB/s | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 10 000<sup>2</sup> értékre fogja korlátozni |
| M128s | 2 000 GiB | 2 000 MB/s |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 20 000<sup>2</sup> értékre fogja korlátozni |
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 10 000<sup>2</sup> értékre fogja korlátozni |
| M128ms | 3 800 GiB | 2 000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 20 000<sup>2</sup> értékre fogja korlátozni |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 10 000<sup>2</sup> értékre fogja korlátozni |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 20 000<sup>2</sup> értékre fogja korlátozni |
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | A írásgyorsító használata a kombinált és a naplózási kötetek esetében a IOPS arányt 20 000<sup>2</sup> értékre fogja korlátozni |


<sup>1</sup> az [Azure-írásgyorsító](../../how-to-enable-write-accelerator.md) nem használhatók a EV4 és a Ev4 VM-családokkal. Az Azure Premium Storage használatának eredményeképpen az I/O-késés nem lesz kevesebb, mint 1ms

<sup>2</sup> a virtuálisgép-család támogatja az [Azure írásgyorsító](../../how-to-enable-write-accelerator.md)-t, de lehetséges, hogy az írási gyorsító IOPS korlátja korlátozhatja a lemezes konfigurációk IOPS képességeit.

Ha a SAP HANA adatmennyiségét és a naplózási kötetet egyesíti, a csíkozott kötetet felépítő lemezek nem rendelkezhetnek olvasási gyorsítótárral, vagy az írási/olvasási gyorsítótár engedélyezve van.

Vannak felsorolva olyan virtuálisgép-típusok, amelyek nem rendelkeznek SAP-tanúsítvánnyal, és amelyek nem szerepelnek az ún. [SAP HANA Hardware könyvtárban](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Az ügyfelek visszajelzései voltak, hogy ezek a nem felsorolt virtuálisgép-típusok sikeresen használatba kerültek néhány nem üzemi feladathoz.


## <a name="next-steps"></a>Következő lépések
További információkért lásd:

- [SAP HANA magas rendelkezésre állású útmutató Azure-beli virtuális gépekhez](./sap-hana-availability-overview.md).