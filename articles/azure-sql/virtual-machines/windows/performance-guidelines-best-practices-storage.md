---
title: 'Tárolás: ajánlott eljárások a teljesítményhez & irányelvek'
description: Ajánlott eljárásokat és irányelveket biztosít a SQL Server Azure-beli virtuális gépen (VM) való teljesítményének optimalizálásához.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 001a9a15c259d0b0d73eec9c9a39ad7c27f26721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572414"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Tárolás: ajánlott eljárások az Azure-beli virtuális gépek SQL Serveréhez
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk az Azure Virtual Machines (VM) SQL Server teljesítményének optimalizálására szolgáló ajánlott eljárásokat és irányelveket tartalmazza.

Általában a költségek optimalizálása és a teljesítmény optimalizálása közötti kompromisszumok állnak fenn. A teljesítményre vonatkozó ajánlott eljárások sorozat az Azure Virtual Machines SQL Server *legjobb* teljesítményének beszerzésére koncentrál. Ha a számítási feladat kevésbé igényes, előfordulhat, hogy nem igényel minden javasolt optimalizálást. A javaslatok kiértékelése során vegye figyelembe a teljesítményre vonatkozó igényeket, a költségeket és a számítási feladatok mintáit.

További információ: a sorozat egyéb cikkei: [teljesítmény ellenőrzőlista](performance-guidelines-best-practices-checklist.md), virtuálisgép- [méret](performance-guidelines-best-practices-vm-size.md)és alapkonfiguráció [gyűjtése](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Ellenőrzőlista

Tekintse át a következő feladatlistát, amely röviden áttekinti az ajánlott eljárásokat, amelyekkel a cikk további részében részletesebben tájékozódhat: 

- A lemez típusának kiválasztása előtt figyelje az alkalmazást, és [határozza meg a tárolási sávszélességet és a késési követelményeket](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) a SQL Server adat-, napló-és tempdb-fájlok esetében. 
- A tárolási teljesítmény optimalizálása érdekében tervezze meg a rendelkezésre álló legmagasabb gyorsítótárban lévő IOPS, és az adatolvasások teljesítménybeli szolgáltatásaként használja az adat-gyorsítótárazást, miközben elkerüli a [virtuális gép és a lemezek maximális korlátját](../../../virtual-machines/premium-storage-performance.md#throttling).
- Az adatfájlok, a naplók és a tempdb fájljait külön meghajtókon helyezheti el.
    - Az adatmeghajtó esetében csak a [prémium szintű P30 és a P40 lemezeket](../../../virtual-machines/disks-types.md#premium-ssd) használja a gyorsítótár-támogatás rendelkezésre állásának biztosításához
    - A [prémium szintű P30-P80 lemezek](../../../virtual-machines/disks-types.md#premium-ssd) kiértékelése során a kapacitás és a tesztelési teljesítmény, valamint a költséghatékony
      - Ha az almilliszekundum tárolási késése szükséges, használja az [Azure Ultra Disks](../../../virtual-machines/disks-types.md#ultra-disk) -t a tranzakciónaplóhoz. 
      - Az M-sorozatú virtuális gépek központi telepítései esetében érdemes lehet az Azure Ultra Disks szolgáltatással [írni a gázpedált](../../../virtual-machines/how-to-enable-write-accelerator.md) .
    - A virtuális gépek optimális méretének kiválasztása után helyezze a [tempdb](/sql/relational-databases/databases/tempdb-database) a helyi ideiglenes SSD- `D:\` meghajtón a legtöbb SQL Server számítási feladathoz. 
      - Ha a helyi meghajtó kapacitása nem elegendő a tempdb, vegye fontolóra a virtuális gép méretezését. További információért lásd: [adatfájl-gyorsítótárazási szabályzatok](#data-file-caching-policies) .
- Több Azure-adatlemezt is megtalálhat a [tárolóhelyek](/windows-server/storage/storage-spaces/overview) szolgáltatással, hogy növelje az I/O-sávszélességet a célként megadott virtuális gép IOPS és átviteli korlátaival.
- A [gazdagép-gyorsítótárazás](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) beállítása az adatfájlok lemezei csak olvashatók.
- Állítsa be a [gazdagép-gyorsítótárazást](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) a Nincs értékre a naplófájlok lemezein.
    - Ne engedélyezze az olvasási/írási gyorsítótárazást SQL Server fájlokat tartalmazó lemezeken. 
    - A lemez gyorsítótár-beállításainak módosítása előtt mindig állítsa le a SQL Server szolgáltatást.
- A fejlesztési és tesztelési feladatokhoz, valamint a hosszú távú biztonsági mentési archiváláshoz a standard szintű tárolást kell használni. Az éles számítási feladatokhoz nem ajánlott standard HDD/SDD használata.
- A [kredit-alapú lemez kitörése](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) csak kisebb fejlesztési/tesztelési feladatokhoz és részlegi rendszerekhez vehető figyelembe.
- A Storage-fiók kiépítése ugyanabban a régióban, mint a SQL Server VM. 
- Tiltsa le az Azure geo-redundáns tárterületet (Geo-Replication), és használja a LRS (helyi redundáns tárolás) a Storage-fiókon.
- Formázza az adatlemezt úgy, hogy az a 64 KB-os foglalási egység méretét használja az ideiglenes meghajtótól eltérő meghajtón elhelyezett összes adatfájlhoz `D:\` (amely alapértelmezés szerint 4 KB). SQL Server az Azure Marketplace-en üzembe helyezett virtuális gépekhez a kiosztási egység méretével formázott adatlemezek tartoznak, és a Storage-készlet 64 KB-ra van állítva. 

Ha össze szeretné hasonlítani a tárolási ellenőrzőlistát a többiekkel, tekintse meg az átfogó [teljesítményre vonatkozó ajánlott eljárásokat tartalmazó feladatlistát](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Áttekintés

Az Azure-beli virtuális gépen SQL Server munkaterhelések leghatékonyabb konfigurációjának megkereséséhez kezdje az [üzleti alkalmazás tárolási teljesítményének mérésével](performance-guidelines-best-practices-collect-baseline.md#storage). A tárolási követelmények ismerete után válasszon ki egy virtuális gépet, amely támogatja a szükséges IOPS és átviteli sebességet a megfelelő memória-virtuális mag aránnyal. 

Válasszon olyan virtuálisgép-méretet, amely elegendő tárterület-méretezhetőséggel rendelkezik a számítási feladatokhoz, valamint egy olyan lemez (általában egy tárolóban), amely megfelel a vállalat kapacitási és teljesítménybeli követelményeinek. 

A lemez típusa a lemezen tárolt fájl típusától és a maximális teljesítmény követelményeitől függ.

> [!TIP]
> A SQL Server VM a Azure Portal segítségével történő kiépítésével végigvezeti a tárolási konfigurációs folyamaton, és megvalósíthatja a legtöbb tárolási ajánlott eljárást, például külön tárolók létrehozását az adataihoz és naplófájljaihoz, a tempdb célzáshoz, `D:\` valamint az optimális gyorsítótárazási házirend engedélyezéséhez. További információ a tároló üzembe helyezéséről és konfigurálásáról: [SQL VM Storage-konfiguráció](storage-configuration.md). 

## <a name="vm-disk-types"></a>Virtuálisgép-lemezek típusai

A lemezek teljesítmény szintjén választhat. A mögöttes tárolóként elérhető felügyelt lemezek típusai (a teljesítmény növelésével) szabványos merevlemez-meghajtók (HDD), standard SSD-k, prémium SSD-meghajtók (SSD) és ultra Disks. 

A lemez teljesítménye a kapacitással nő, [prémium szintű lemezes címkék](../../../virtual-machines/disks-types.md#premium-ssd) szerint csoportosítva, mint például a P1, 4 GIB szóközzel és 120 IOPS a P80 a 32 TiB Storage és a 20 000 IOPS. A Premium Storage olyan tárolási gyorsítótárat támogat, amely bizonyos számítási feladatok esetében segít az olvasási és írási teljesítmény javításában. További információ: [Managed Disks – áttekintés](../../../virtual-machines/managed-disks-overview.md). 

A SQL Server az Azure-beli virtuális gépen (operációsrendszer-lemez, ideiglenes lemez és az adatlemezek) is [figyelembe kell venni](../../../virtual-machines/managed-disks-overview.md#disk-roles) . Körültekintően válassza ki, hogy mit tárol az operációs rendszer meghajtóján `(C:\)` és az ideiglenes átmeneti meghajtón `(D:\)` . 

### <a name="operating-system-disk"></a>Operációsrendszer-lemez

Az operációsrendszer-lemez olyan virtuális merevlemez, amely az operációs rendszer futó verziójának megfelelően indítható el és csatlakoztatható, és meghajtóként van megjelölve `C:\` . Azure-beli virtuális gép létrehozásakor a platform legalább egy lemezt csatlakoztat a virtuális géphez az operációsrendszer-lemezhez. A `C:\` meghajtó az alkalmazás telepítésének és a fájl konfigurációjának alapértelmezett helye. 

Éles SQL Server környezetekben ne használja az operációs rendszer lemezét adatfájlokhoz, naplófájlokhoz és hibanaplóekhez. 

### <a name="temporary-disk"></a>Ideiglenes lemez

Számos Azure-beli virtuális gép tartalmaz egy másik, az ideiglenes lemezt ( `D:\` meghajtóként megjelölt) lemezt. A virtuálisgép-sorozattól és méretétől függően a lemez kapacitása eltérő lesz. Az ideiglenes lemez elmúló jellegű, ami azt jelenti, hogy a lemez tárterületét újra létrehozza a rendszer (a (z)-ben, a lefoglalást és a foglalást újra lefoglalják), a virtuális gép újraindításakor vagy egy másik gazdagépre (például a [szolgáltatás-gyógyításra](/troubleshoot/azure/virtual-machines/understand-vm-reboot)) áthelyezve. 

Az ideiglenes tárolóeszköz nem marad meg a távoli tárolóban, ezért nem szabad tárolni a felhasználói adatbázisfájlok, a tranzakciós naplófájlok fájljait, vagy bármit, amelyet meg kell őrizni. 

Helyezze a tempdb a helyi ideiglenes SSD-meghajtóra SQL Server számítási feladatokhoz, `D:\` kivéve, ha a helyi gyorsítótár használata aggodalomra ad okot. Ha olyan virtuális gépet használ, amely [nem rendelkezik ideiglenes lemezzel](../../../virtual-machines/azure-vms-no-temp-disk.md) , javasoljuk, hogy a tempdb a saját elkülönített lemezére vagy a Storage-készletbe helyezze a gyorsítótárazási beállítással, hogy csak olvasható legyen. További információ: tempdb- [adatgyorsítótárazási szabályzatok](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Adatlemezek

Az adatlemezek olyan távoli tároló lemezek, amelyek gyakran jönnek létre a [tárolókban](/windows-server/storage/storage-spaces/overview) , hogy meghaladják azt a kapacitást és teljesítményt, amelyet egyetlen lemez kínálhat a virtuális géphez.

Csatolja a számítási feladatok IOPS, átviteli sebességére és kapacitására vonatkozó követelményeket teljesítő lemezek minimális számát. Ne lépje túl az átméretezni kívánt legkisebb virtuális gép adatlemezek maximális számát.

Az adat-és naplófájlok elhelyezése az adatlemezeken a legjobb teljesítménybeli követelményeknek megfelelően. 

Formázza az adatlemezt úgy, hogy az a 64 KB-os foglalási egység méretét használja az ideiglenes meghajtótól eltérő meghajtón elhelyezett összes adatfájlhoz `D:\` (amely alapértelmezés szerint 4 KB). SQL Server az Azure Marketplace-en üzembe helyezett virtuális gépekhez a kiosztási egység méretével formázott adatlemezek tartoznak, és a Storage-készlet 64 KB-ra van állítva. 

## <a name="premium-disks"></a>Prémium szintű lemezek

Az adatokhoz és naplófájlokhoz prémium szintű SSD-lemezeket használjon éles SQL Server munkaterhelések esetén. Prémium SSD IOPS és a sávszélesség a [lemez méretétől és típusától](../../../virtual-machines/disks-types.md)függően változik. 

Éles számítási feladatokhoz használja a P30 és/vagy a P40 lemezeket SQL Server adatfájlok számára, hogy biztosítsa a gyorsítótárazást, és használja a P30 legfeljebb P80 SQL Server tranzakciós naplófájlok számára.  A legjobb teljes tulajdonlási díjas P30s (5000 IOPS/200 MBPS) az adatfájlok és a naplófájlok esetében, és csak akkor válassza a nagyobb kapacitást, ha a virtuális gépek lemezének számát kell vezérelni.

A OLTP számítási feladatokhoz a IOPS (vagy a Storage-készletet) a teljesítményre vonatkozó követelményekkel kell megegyeznie a munkaterhelések csúcsidőben és a teljesítményszámlálók használatával `Disk Reads/sec`  +  `Disk Writes/sec` . Az adatraktár-és jelentéskészítési számítási feladatokhoz a munkaterhelések csúcsidőben és a `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Használja a tárolóhelyeket az optimális teljesítmény eléréséhez, konfigurálja a két készletet, egyet a naplófájl (ok) hoz és a másikat az adatfájlokhoz. Ha nem használ lemezes csíkozást, használjon két, különálló meghajtókra leképezett prémium SSD-lemezt, ahol az egyik meghajtó tartalmazza a naplófájlt, a másik pedig tartalmazza az adatfájlt.

A tárolási készlet részeként használt [kiépített IOPS és átviteli sebesség](../../../virtual-machines/disks-types.md#premium-ssd) . A lemezek együttes IOPS és átviteli kapacitása a virtuális gép átviteli sebességére vonatkozó korlátok maximális kapacitása.

Az ajánlott eljárás a lehető legkevesebb lemez használata a IOPS (és az átviteli sebesség) és a kapacitás minimális követelményeinek teljesítése során. Az ár és a teljesítmény egyensúlya azonban sokkal nagyobb méretű kis méretű lemezekkel, nem pedig kis számú nagyméretű lemezzel.

### <a name="scaling-premium-disks"></a>Prémium szintű lemezek skálázása

Egy Azure-beli felügyelt lemez első telepítésekor a lemez teljesítményszint a kiosztott lemez méretétől függ. Jelölje ki a teljesítmény szintet a telepítéskor, vagy később módosítsa azt a lemez méretének módosítása nélkül. Ha a kereslet növekszik, növelheti a teljesítmény szintjét az üzleti igények kielégítése érdekében. 

A teljesítmény szintjének módosítása lehetővé teszi a rendszergazdák számára, hogy a [lemezre való kitörés](../../../virtual-machines/disk-bursting.md#credit-based-bursting)nélkül készítsék elő és találkozzanak magasabb igényekkel. 

Ha a számlázást úgy tervezték, hogy az megfeleljen a tárolási teljesítmény szintjének, használja a nagyobb teljesítményt, ha szükséges. A kapacitás növelése nélkül frissítse a szintet, hogy az megfeleljen a teljesítmény követelményeinek. Térjen vissza az eredeti szintjére, ha a további teljesítmény már nem szükséges.

Ez a költséghatékony és a teljesítmény átmeneti bővülése erős használati eset olyan megcélzó eseményekhez, mint például a vásárlás, a teljesítmény tesztelése, a betanítási események és más rövid ablakok, ahol nagyobb teljesítményre van szükség, csak rövid távon. 

További információ: [teljesítmény szintjei a felügyelt lemezekhez](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Azure Ultra Disk

Ha kisebb késéssel rendelkező ezredmásodperces válaszidő szükséges, érdemes lehet [Azure Ultra diskt](../../../virtual-machines/disks-types.md#ultra-disk) használni a SQL Server log meghajtóhoz, vagy akár az adatmeghajtót olyan alkalmazásokhoz is, amelyek rendkívül érzékenyek az I/O-késésre. 

Az ultra Disk konfigurálható, ahol a kapacitás és a IOPS egymástól függetlenül méretezhető. Az ultra Disk-rendszergazdák a kapacitással, a IOPS és az átviteli sebességgel kapcsolatos követelményekkel rendelkezhetnek az alkalmazás igényei alapján. 

Az ultravékony lemez nem támogatott az összes virtuálisgép-sorozatban, és más korlátozásokkal rendelkezik, mint például a régió rendelkezésre állása, a redundancia és a Azure Backup támogatása. További információ: az [Azure Ultra Disks használata](../../../virtual-machines/disks-enable-ultra-ssd.md) a korlátozások teljes listájára. 

## <a name="standard-hdds-and-ssds"></a>Standard HDD-k és SSD-k

A [szabványos HDD](../../../virtual-machines/disks-types.md#standard-hdd) -k és SSD-k eltérő késéssel és sávszélességgel rendelkeznek, és csak fejlesztési/tesztelési feladatokhoz ajánlottak. Az éles számítási feladatoknak prémium SSD-ket kell használniuk. Ha standard SSD (fejlesztési/tesztelési forgatókönyvet) használ, a javaslat a virtuálisgép- [méret](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) által támogatott adatlemezek maximális számának hozzáadására szolgál, és a lehető legjobb teljesítmény érdekében a lemezes csíkozást használja a tárolóhelyekkel.

## <a name="caching"></a>Gyorsítótárazás

A Premium Storage gyorsítótárazást támogató virtuális gépek kihasználhatják az Azure BlobCache vagy a gazdagép gyorsítótárazása nevű további funkciót, amely kibővíti a virtuális gépek IOPS és átviteli kapacitását. A Premium Storage-hoz és a Premium Storage-gyorsítótárazáshoz engedélyezett virtuális gépekhez két különböző tárolási sávszélességre vonatkozó korlátozás vonatkozik, amelyek együttesen használhatók a tárolási teljesítmény javítása érdekében.

A gyorsítótárazás nélküli IOPS és MB/s átviteli sebesség a virtuális gép nem gyorsítótárazott lemezes átviteli sebességének korlátaira vonatkozik. A gyorsítótárazott maximális határértékek egy további puffert biztosítanak, amely segít a növekedés és a váratlan csúcsok kezelésében.

A prémium szintű gyorsítótárazás engedélyezése, ha a lehetőség támogatott az adatmeghajtón lévő olvasási teljesítmény jelentős javítása érdekében további díjak nélkül. 

Az Azure BlobCache (gyorsítótárazott IOPS és átviteli sebesség) beolvasása és írása nem számít bele a virtuális gép nem gyorsítótárazott IOPS és átviteli sebességére vonatkozó korlátozásokkal.

> [!NOTE]
> A lemezes gyorsítótárazás nem támogatott a 4 TiB-s és nagyobb (P50 és nagyobb) lemezek esetén. Ha több lemez van csatlakoztatva a virtuális géphez, akkor minden 4 TiB-nál kisebb lemez támogatja a gyorsítótárazást. További információ: [lemezes gyorsítótárazás](../../../virtual-machines/premium-storage-performance.md#disk-caching). 

### <a name="uncached-throughput"></a>Nem gyorsítótárazott átviteli sebesség

A nem gyorsítótárazott lemez maximális IOPS és átviteli sebessége a virtuális gép által kezelhető maximális tárterület. Ez a korlát a virtuális gépen van meghatározva, és nem korlátozza a mögöttes lemezes tárolást. Ez a korlát csak az I/O-re vonatkozik a virtuális géphez távolról csatlakoztatott adatmeghajtókon, nem pedig a helyi I/O-t a temp meghajtón ( `D:\` meghajtón) vagy az operációsrendszer-meghajtón.

A virtuális gép számára elérhető nem gyorsítótárazott IOPS és átviteli sebesség ellenőrizhető a virtuális géphez tartozó dokumentációban.

Az [M-sorozat](../../../virtual-machines/m-series.md) dokumentációja például azt mutatja, hogy a Standard_M8ms virtuális gép maximális gyorsítótár nélküli átviteli sebessége 5000 IOPS és 125 Mbps a nem gyorsítótárazott lemez átviteli sebessége. 

![Képernyőfelvétel: az M-sorozat nem gyorsítótárazott lemez-átviteli sebességének dokumentációja.](./media/performance-guidelines-best-practices/m-series-table.png)

Hasonlóképpen láthatja, hogy a Standard_M32ts támogatja a 20 000 gyorsítótár nélküli lemez-IOPS és a 500 MBps gyorsítótár nélküli lemez átviteli sebességét. Ez a korlát a virtuális gép szintjére vonatkozik, függetlenül az alapul szolgáló prémium szintű lemezes tárterülettől.

További információ: nem [gyorsítótárazott és gyorsítótárazott korlátok](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits).


### <a name="cached-and-temp-storage-throughput"></a>Gyorsítótárazott és ideiglenes tárolási sebesség

A maximális gyorsítótárazott és ideiglenes tárolási átviteli sebesség korlátja a virtuális gép nem gyorsítótárazott átviteli korlátjának külön korlátja. Az Azure-BlobCache a virtuális gép gazdagépének véletlen elérésű memóriájának és helyileg csatlakoztatott SSD-hálózatának kombinációjából áll. A virtuális gép Temp meghajtója ( `D:\` meghajtója) is ezen a helyi SSD-n fut.

A gyorsítótárazott és a temp tároló maximális átviteli sebességének korlátja csak akkor szabályozza az I/O-t a helyi ideiglenes meghajtón ( `D:\` meghajtón) és az Azure BlobCache, **Ha** engedélyezve van a gazdagép gyorsítótárazása. 

Ha a gyorsítótárazás engedélyezve van a Premium Storage-on, a virtuális gépek a távoli tárolók nem gyorsítótárazott virtuális gép IOPS és az átviteli sebesség korlátain túl is méretezhetők.  

Csak bizonyos virtuális gépek támogatják a Premium Storage-t és a Premium Storage-gyorsítótárazást (amelyet ellenőrizni kell a virtuális gép dokumentációjában). Az [M-sorozat](../../../virtual-machines/m-series.md) dokumentációja például azt jelzi, hogy a Premium Storage és a Premium Storage gyorsítótárazása is támogatott: 

![Az M-sorozat Premium Storage támogatását ábrázoló képernyőkép.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

A gyorsítótár korlátai a virtuális gép méretétől függően változnak. Például a Standard_M8ms virtuális gép a 10000 gyorsítótárazott lemez IOPS és 1000 MBps gyorsítótárazott lemez átviteli sebességét támogatja a 793 GiB teljes gyorsítótár-méretével. Hasonlóképpen, a Standard_M32ts virtuális gép a 40000 gyorsítótárazott lemez IOPS és 400 MBps gyorsítótárazott lemez átviteli sebességét támogatja a 3174 GiB teljes gyorsítótár-méretével. 

![Az M-sorozat gyorsítótárazott lemezének adatátviteli dokumentációját ábrázoló képernyőfelvétel.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

A gazdagépek gyorsítótárazását manuálisan is engedélyezheti egy meglévő virtuális gépen. A virtuális gép gyorsítótárazási házirendjének módosítása előtt állítsa le az összes alkalmazás-munkaterhelést és a SQL Server-szolgáltatást. A virtuális gép gyorsítótár-beállításainak bármelyikének módosításakor a rendszer leválasztja a célként megadott lemezt, és a beállítások alkalmazása után újra csatolja őket.

### <a name="data-file-caching-policies"></a>Adatfájl-gyorsítótárazási házirendek

A tárolási gyorsítótárazási házirend a meghajtón tárolt SQL Server adatfájlok típusától függően változhat. 

A következő táblázat összefoglalja az ajánlott gyorsítótárazási házirendeket az SQL Server-adattípusok alapján: 

|SQL Server lemez |Ajánlás |
|---------|---------|
| **Adatlemez** | Engedélyezze `Read-only` a gyorsítótárazást a SQL Server adatfájlokat üzemeltető lemezeken. <br/> A gyorsítótárból való beolvasás gyorsabb lesz, mint az adatlemezről származó nem gyorsítótárazott olvasások. <br/> A nem gyorsítótárazott IOPS és az átviteli sebesség, valamint a gyorsítótárazott IOPS és az átviteli sebesség a virtuális gépek teljes lehetséges teljesítményét eredményezi a virtuális gép korlátain belül, a tényleges teljesítmény azonban a munkaterhelés a gyorsítótár használatának lehetősége alapján változhat (gyorsítótár-találati arány). <br/>|
|**Tranzakciós napló lemeze**|Állítsa be a gyorsítótárazási házirendet a `None` tranzakciós naplót üzemeltető lemezekre.  Nincs teljesítménybeli előny a tranzakciónapló-lemez gyorsítótárazásának engedélyezéséhez, és ha a `Read-only` naplófájlban engedélyezve van vagy gyorsítótárazás engedélyezett, a meghajtón lévő `Read/Write` írások teljesítménye csökkenhet, és csökkentheti az adatmeghajtón való olvasáshoz elérhető gyorsítótár mennyiségét.  |
|**Operációs rendszer lemeze** | Az alapértelmezett gyorsítótárazási házirend lehet `Read-only` vagy `Read/write` az operációsrendszer-meghajtó. <br/> Az operációs rendszer meghajtójának gyorsítótárazási szintjének módosítása nem ajánlott.  |
| **tempdb**| Ha a tempdb nem helyezhetők el az ideiglenes meghajtón a `D:\` Kapacitási okok miatt, vagy méretezze át a virtuális gépet úgy, hogy egy nagyobb, ideiglenes meghajtót kapjon, vagy helyezze el a tempdb egy külön adatmeghajtón, amelyen a `Read-only` gyorsítótárazás konfigurálva van. <br/> A virtuális gép gyorsítótára és az ideiglenes meghajtó egyaránt a helyi SSD-t használja, ezért ne feledje, hogy a tempdb I/O-méretezése a gyorsítótárazott IOPS és az átviteli sebességű virtuális gépekre vonatkozó korlátok alapján történik, amikor az ideiglenes meghajtón fut.| 
| | | 


További információ: [lemezes gyorsítótárazás](../../../virtual-machines/premium-storage-performance.md#disk-caching). 


## <a name="disk-striping"></a>Lemezek csíkozása

Elemezze az SQL-adatfájlokhoz szükséges átviteli sebességet és sávszélességet az adatlemezek számának meghatározásához, beleértve a naplófájlt és a tempdb is. Az átviteli sebesség és a sávszélesség korlátai a virtuális gépek méretétől függően változnak. További információ: [virtuális gép mérete](../../../virtual-machines/sizes.md)

További adatlemezeket adhat hozzá, és további átviteli sebességhez használhatja a lemezes sávokat. Például egy 12 000 IOPS és 180 MB/s sebességű adatátviteli sebességet igénylő alkalmazás három csíkozott P30-lemezt is használhat a 15 000 IOPS és a 600 MB/s átviteli sebesség eléréséhez. 

A lemezek csíkozásának konfigurálásához lásd: [lemezes csíkozás](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Lemezterület-korlátozó 

A lemez és a virtuális gép szintjén is vannak átviteli korlátok. A virtuális gépen és a lemezen lévő maximális IOPS határértékek eltérnek egymástól.

Azokat az alkalmazásokat, amelyek ezen korlátokon túl erőforrásokat használnak, szabályozva lesznek (más néven a határértékek is). Válassza ki a virtuális gépet és a lemez méretét egy olyan szalagon, amely megfelel az alkalmazás követelményeinek, és nem fogja megszabni a korlátozásokat. A maximális kapacitás eléréséhez használja a gyorsítótárazást, vagy hangolja az alkalmazást úgy, hogy kevesebb átviteli sebességre van szükség.

Például egy 12 000 IOPS és 180 MB/s kapacitást igénylő alkalmazás a következőket teheti: 
- Használja azt a [Standard_M32ms](../../../virtual-machines/m-series.md) , amely a 20 000 IOPS és 500 Mbps max.
- Az 15 000 IOPS és a 600 MB/s átviteli sebesség biztosításához három P30 lemezt kell megadnia.
- Használjon [Standard_M16ms](../../../virtual-machines/m-series.md) virtuális gépet, és használja a gazdagép-gyorsítótárazást a helyi gyorsítótár felhasználásához az adatátviteli sebesség használatával. 

A magas kihasználtsági idő alatt felskálázásra konfigurált virtuális gépeknek elegendő IOPS és átviteli sebességű tárterületet kell kiépíteniük a virtuális gép maximális méretének támogatásához, miközben a lemezek teljes számát a használni kívánt legkisebb VM-SKU által támogatott maximális számmal vagy azzal egyenlően kell tartani.

További információ a lemezes korlátok korlátozásáról és a maximálisan szükséges gyorsítótárazás használatáról: a [lemez i/o-határértékének](../../../virtual-machines/disks-performance.md)korlátozása.

> [!NOTE] 
> Előfordulhat, hogy a lemezek maximális korlátja továbbra is kielégítő teljesítményt eredményezhet a felhasználók számára; a munkaterhelések finomhangolása és karbantartása ahelyett, hogy átméretezni egy nagyobb virtuális gépre, hogy kiegyensúlyozzák a vállalat költségeit és teljesítményét. 


## <a name="write-acceleration"></a>Írási gyorsítás

Az írási gyorsítás egy olyan lemez-szolgáltatás, amely csak az [M sorozatú](https://docs.microsoft.com/azure/virtual-machines/m-series) Virtual Machines (VM) esetében érhető el. Az írási gyorsítás célja, hogy javítsa az írások I/O-késését az Azure-Premium Storageon, ha a nagy mennyiségű, kritikus fontosságú OLTP számítási feladatnak vagy adattárház-környezetnek köszönhetően egyszámjegyű I/O-késésre van szüksége. 

Az írási gyorsítással javíthatja az írási késést a naplófájlokat üzemeltető meghajtón. Ne használjon írási gyorsítást SQL Server adatfájlokhoz. 

Írásgyorsító lemezek ugyanazt a IOPS-korlátot használják, mint a virtuális gép. A csatlakoztatott lemezek nem haladhatják meg a virtuális gép írásgyorsító IOPS korlátját.  

Az alábbi táblázat a virtuális gépek által támogatott adatlemezek és IOPS számát ismerteti: 

| Virtuális gép termékváltozata  | írásgyorsító lemezek száma  | írásgyorsító lemez IOPS virtuális gépenként  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms  | 1 | 1250 |

Az írási gyorsulás használatának számos korlátozása van. További információ: korlátozások a [írásgyorsító használatakor](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Összehasonlítás az Azure Ultra Disk használatával

Az írási gyorsulás és az Azure Ultra Disks közötti legnagyobb különbség az, hogy az írási gyorsulás egy virtuálisgép-funkció, amely csak az M sorozathoz és az Azure Ultra Disks szolgáltatáshoz használható. Az írási gyorsítás a virtuális gép méretétől függően a saját korlátozásait tartalmazó írható gyorsítótár. Az Azure Ultra Disks alacsony késésű lemezes tárolást biztosít az Azure Virtual Machines számára. 

Ha lehetséges, a tranzakciós napló lemezéhez használja az írási gyorsítást az ultrakönnyű lemezeken. Olyan virtuális gépek esetében, amelyek nem támogatják az írási gyorsítást, de kis késleltetést igényelnek a tranzakciónaplóban, használja az Azure Ultra Disks használatát. 

## <a name="monitor-storage-performance"></a>Tárolási teljesítmény figyelése

A tárolási igények felmérése és a tárolási feladatok elvégzésének meghatározása érdekében meg kell ismernie, hogy mit kell mérni, és mit jelent ezek a mutatók. 

A [IOPS (bemeneti/kimeneti/másodpercenkénti)](../../../virtual-machines/premium-storage-performance.md#iops) érték az alkalmazás által a tárterület tárolására szolgáló kérések másodpercenkénti száma. IOPS mérése a teljesítményfigyelő számlálóinak használatával `Disk Reads/sec` és `Disk Writes/sec` . Az optimális teljesítmény érdekében a [OLTP (online tranzakció-feldolgozási)](/azure/architecture/data-guide/relational-data/online-transaction-processing) alkalmazásoknak magasabb IOPS kell vezetniük. Az olyan alkalmazások, mint például a fizetési feldolgozó rendszerek, az online vásárlás és a kereskedelmi pénztári rendszerek mind a OLTP-alkalmazások példái.

Az [átviteli sebesség](../../../virtual-machines/premium-storage-performance.md#throughput) a mögöttes tárolóba küldendő adatok mennyisége, amelyet általában megabájt/másodperc alapján mérnek. Mérje fel az átviteli sebességet a Teljesítményfigyelő számlálókkal `Disk Read Bytes/sec` és `Disk Write Bytes/sec` . Az [adattárházak](/azure/architecture/data-guide/relational-data/data-warehousing) a IOPS-on keresztüli maximális átviteli sebességre optimalizáltak. Az adattárolási alkalmazások, például az Analysis, a Reporting, az ETL workstreams és más üzleti intelligenciát használó alkalmazások az adattárház-alkalmazásokra vonatkozó példák.

Az i/O-egységek mérete befolyásolja a IOPS és az átviteli kapacitást, mivel a kisebb I/O-méretek nagyobb IOPS és nagyobb I/O-méretet eredményeznek. SQL Server automatikusan kiválasztja az optimális I/O-méretet. A szolgáltatással kapcsolatos további információkért lásd: az [alkalmazások IOPS, átviteli sebességének és késésének optimalizálása](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance). 

Vannak olyan Azure Monitor mérőszámok, amelyek nem értékesek a virtuális gép és a lemez szintjén, valamint a AzureBlob cache felhasználásának és állapotának megállapításához. A figyelési megoldáshoz és Azure Portal irányítópulthoz hozzáadandó teljesítményszámlálók azonosításához tekintse meg a [tárolási kihasználtsági metrikákat](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> A Azure Monitor jelenleg nem biztosít lemez szintű metrikákat az ideiglenes Temp meghajtóhoz `(D:\)` . A virtuális gép gyorsítótárazott IOPS felhasznált százalék és a virtuális gép gyorsítótárazott sávszélességének százalékos aránya a IOPS és az átviteli sebesség értékét is tükrözi az ideiglenes Temp meghajtó `(D:\)` és a gazdagép gyorsítótárazása között.


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a teljesítményre vonatkozó ajánlott eljárásokról, tekintse meg a sorozat további cikkeit:
- [Gyors ellenőrzőlista](performance-guidelines-best-practices-checklist.md)
- [Virtuális gép mérete](performance-guidelines-best-practices-vm-size.md)
- [Alapterv összegyűjtése](performance-guidelines-best-practices-collect-baseline.md)

Az ajánlott biztonsági eljárásokért tekintse [meg az Azure Virtual Machines SQL Server biztonsági szempontjait](security-considerations-best-practices.md).

Az Azure-beli virtuális gépeken a TPC-E és a TPC_C referenciaértékekkel rendelkező SQL Server teljesítményének részletes teszteléséhez tekintse meg a blogot, hogy [optimalizálja a OLTP teljesítményét](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

Tekintse át az Azure-beli SQL Server SQL Server virtuális gépekkel kapcsolatos további cikkeket [Virtual Machines áttekintését](sql-server-on-azure-vm-iaas-what-is-overview.md). Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).
