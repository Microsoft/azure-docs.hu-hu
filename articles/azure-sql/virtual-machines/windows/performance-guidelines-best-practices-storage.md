---
title: 'Storage: Teljesítménnyel kapcsolatos ajánlott eljárások és & útmutató'
description: Ajánlott tárolási eljárásokat és irányelveket biztosít a virtuális gépek SQL Server azure-beli virtuális gépeken való használatának optimalizálásához.
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
ms.openlocfilehash: 23e006c637285ad484e98b23b2a9f506156f519c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389723"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Tárolás: Azure-beli virtuális gépeken SQL Server teljesítményre vonatkozó ajánlott eljárások
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk ajánlott eljárásokat és irányelveket tartalmaz a tároláshoz az Azure SQL Server virtuális gépeken Virtual Machines teljesítmény optimalizálásához.

Általában a költségek optimalizálása és a teljesítményre való optimalizálás között van különbség. A teljesítménnyel kapcsolatos ajánlott eljárások  sorozatának célja, hogy a lehető legjobb teljesítményt SQL Server Azure-Virtual Machines. Ha a számítási feladat kevésbé teljesítményigényes, előfordulhat, hogy nincs szükség minden javasolt optimalizálásra. A javaslatok kiértékelése során vegye figyelembe a teljesítménybeli igényeket, a költségeket és a számítási feladatok mintáit.

További tudnivalókért tekintse meg a sorozat további cikkeit: Teljesítmény-ellenőrzőlista, virtuálisgép-méret [és](performance-guidelines-best-practices-vm-size.md) [Alapkonfiguráció gyűjtése.](performance-guidelines-best-practices-collect-baseline.md) [](performance-guidelines-best-practices-checklist.md) 

## <a name="checklist"></a>Ellenőrzőlista

Tekintse át az alábbi ellenőrzőlistát, amely rövid áttekintést nyújt a tárolási ajánlott eljárásokról, amelyekről a cikk további részében részletesebben is olvashat: 

- A lemez [](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) típusának kiválasztása előtt figyelje az alkalmazást, és határozza meg SQL Server adatok, naplók és tempdb fájlok tárolási sávszélességére és késésére vonatkozó követelményeket. 
- A tárolási teljesítmény optimalizálása érdekében tervezze meg a lehető legjobb elérhető IOPS-t, és [](../../../virtual-machines/premium-storage-performance.md#throttling)használja az adat-gyorsítótárazást teljesítmény-szolgáltatásként az adatbeolvasott adatokhoz, miközben a virtuális gép és a lemezek nem korlátoznak.
- Helyezze az adatokat, a naplókat és a tempdb-fájlokat külön meghajtókra.
    - Az adatmeghajtóhoz csak prémium [szintű P30- és P40-lemezeket](../../../virtual-machines/disks-types.md#premium-ssd) használjon a gyorsítótár-támogatás rendelkezésre állásának biztosításához
    - A kapacitásra és a teljesítményt és a költségeket vizsgáló naplómeghajtóra vonatkozó tervhez a prémium [P30– P80 lemezek értékelésekor](../../../virtual-machines/disks-types.md#premium-ssd)
      - Ha ezredmásodperc alatti tárolási késésre van szükség, használja az [Azure ultralemezeket](../../../virtual-machines/disks-types.md#ultra-disk) a tranzakciónaplóhoz. 
      - Az M-sorozatú virtuális gépek üzembe helyezésekor fontolja meg az [írásgyorsító](../../../virtual-machines/how-to-enable-write-accelerator.md) azure-beli ultralemezek használatával való felhasználásával való használatot.
    - A legtöbb számítási feladathoz helyezze a [tempdb-t](/sql/relational-databases/databases/tempdb-database) a helyi rövid SQL Server SSD-meghajtóra az `D:\` optimális virtuálisgép-méret kiválasztása után. 
      - Ha a helyi meghajtó kapacitása nem elegendő a tempdb számára, fontolja meg a virtuális gép méretezését. További [információ: Adatfájl-gyorsítótárazás házirendek.](#data-file-caching-policies)
- Több Azure-beli adatlemez csíkozása a [Tárolóhelyek](/windows-server/storage/storage-spaces/overview) használatával az I/O-sávszélességnek a cél virtuális gép IOPS- és átviteli sebességkorlátjainak megfelelő növeléséhez.
- Állítsa [a gazdagép gyorsítótárazása](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) csak olvashatóra az adatfájllemezek számára.
- A [naplófájllemezek](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) gazdagép-gyorsítótárazása beállításnál adja meg a nincs adatokat.
    - Ne engedélyezze az olvasási/írási gyorsítótárazást olyan lemezeken, amelyek SQL Server tartalmaznak. 
    - Mindig állítsa le a SQL Server szolgáltatást a lemez gyorsítótár-beállításainak módosítása előtt.
- A fejlesztési és tesztelési számítási feladatokhoz, valamint a hosszú távú biztonsági mentési archiváláshoz érdemes lehet standard szintű tárolót használni. Éles számítási feladatokhoz nem ajánlott standard HDD/SDD-t használni.
- [A hitelképesség-alapú lemezlökesztés](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) csak kisebb fejlesztési/tesztelési számítási feladatokhoz és részlegek rendszeréhez megfontolható.
- A tárfiókot a tárfiókkal azonos régióban SQL Server VM. 
- Tiltsa le az Azure georedundáns tárolást (georeplikáció), és használjon LRS-t (helyileg redundáns tárolást) a tárfiókon.
- Formázza az adatlemezt úgy, hogy a 64 KB-os blokkméretet (a lemezfoglalási egység méretét) használja az ideiglenes meghajtón (alapértelmezés szerint 4 KB-os) meghajtón elhelyezett összes `D:\` adatfájlhoz. SQL Server virtuális gépek Azure Marketplace blokkmérettel formázott adatlemezekkel és 64 KB-ra beállított tárolókészlet összefűzével vannak. 

A tárolási ellenőrzőlista és a többi összehasonlításához tekintse meg a teljesítménnyel kapcsolatos ajánlott eljárások átfogó [ellenőrzőlistát.](performance-guidelines-best-practices-checklist.md) 

## <a name="overview"></a>Áttekintés

Az Azure-beli virtuális gépeken SQL Server számítási feladatok leghatékonyabb konfigurációjának megkereséhez kezdje az üzleti alkalmazás tárolási [teljesítményének mérésével.](performance-guidelines-best-practices-collect-baseline.md#storage) Ha már ismertek a tárolási követelmények, válasszon ki egy virtuális gépet, amely támogatja a szükséges IOPS-t és átviteli sebességet a megfelelő memória–virtuális mag arány mellett. 

Válasszon egy olyan virtuálisgép-méretet, amely elegendő tárolómérettel rendelkezik a számítási feladatokhoz, és olyan lemezeket vegyesen (általában egy tárolókészletben), amelyek megfelelnek a vállalat kapacitási és teljesítménybeli követelményeinek. 

A lemez típusa a lemezen üzemeltetett fájltípustól és a csúcsteljesítményre vonatkozó követelményektől is függ.

> [!TIP]
> A SQL Server VM Azure Portal segítségével kiépítve végigvezeti a tárolókonfigurációs folyamaton, és megvalósítja a legtöbb ajánlott tárolási eljárást, például különálló tárolókészleteket hoz létre az adatokhoz és a naplófájlok számára, célként a tempdb-t a meghajtóra, és engedélyezi az optimális gyorsítótárazás `D:\` házirendet. További információ a tárolók üzembehelyezésről és konfigurálásról: [SQL-alapú virtuális gépek tárolókonfigurációja.](storage-configuration.md) 

## <a name="vm-disk-types"></a>Virtuálisgép-lemezek típusai

A lemezek teljesítményszintje alapján választhat. A mögöttes tárolóként (a teljesítmény növelése alapján listázva) elérhető felügyelt lemezek típusai a standard merevlemez-meghajtók (HDD), a standard SSD-k, a prémium SSD-meghajtók és az ultralemezek. 

A lemez teljesítménye a kapacitással együtt [](../../../virtual-machines/disks-types.md#premium-ssd) nő, prémium lemezcímkék szerint csoportosítva, például a P1 4 GiB-hellyel és 120 IOPS-sel a P80-ra, 32 TiB tárhelytel és 20 000 IOPS-sel. A Premium Storage támogatja a tárolási gyorsítótárat, amely bizonyos számítási feladatok olvasási és írási teljesítményének javításához nyújt segítséget. További információ: [Felügyelt lemezek áttekintése.](../../../virtual-machines/managed-disks-overview.md) 

Az Azure-beli virtuális gépen SQL Server lemeztípust is érdemes figyelembe venni: egy operációsrendszer-lemez, egy ideiglenes lemez és az adatlemezek esetében. [](../../../virtual-machines/managed-disks-overview.md#disk-roles) Gondosan válassza ki az operációsrendszer-meghajtón és a ideiglenes ideiglenes `(C:\)` meghajtón tárolt `(D:\)` adatokat. 

### <a name="operating-system-disk"></a>Operációsrendszer-lemez

Az operációsrendszer-lemezek olyan vhD-fájlok, amelyek az operációs rendszer futó verziójaként indíthatóak el és csatlakoztathatóak, és meghajtóként vannak `C:\` megcímkézve. Azure-beli virtuális gép létrehozásakor a platform legalább egy lemezt csatol a virtuális géphez az operációsrendszer-lemez számára. A meghajtó az alkalmazástelepítések és a `C:\` fájlkonfiguráció alapértelmezett helye. 

Éles környezetekben SQL Server ne használja az operációsrendszer-lemezt adatfájlokhoz, naplófájlokhoz és hibanaplókhoz. 

### <a name="temporary-disk"></a>Ideiglenes lemez

Számos Azure-beli virtuális gép tartalmaz egy másik lemeztípust, az úgynevezett ideiglenes lemezt (meghajtóként `D:\` címkézve). A virtuálisgép-sorozattól és mérettől függően a lemez kapacitása eltérő lehet. Az ideiglenes lemez ideiglenes lemez, ami azt jelenti, hogy a lemeztároló újból létre lesz hozva (például felszabadítja és újból lefoglalja), [](/troubleshoot/azure/virtual-machines/understand-vm-reboot)a virtuális gép újraindításakor vagy egy másik gazdagépre való áthelyezésekor (például a szolgáltatás javítóeszközeként). 

Az ideiglenes tárolót nem őrzi meg a rendszer a távoli tárolóban, ezért nem tárolhat felhasználói adatbázisfájlokat, tranzakciós naplófájlokat vagy bármi olyan fájlt, amit meg kell őrizni. 

Helyezze a tempdb-t a helyi ideiglenes SSD-meghajtóra SQL Server számítási feladatokhoz, kivéve, ha a helyi gyorsítótár `D:\` használata aggodalomra ad okot. Ha olyan virtuális gépet használ, amely nem rendelkezik ideiglenes lemezzel, javasoljuk, hogy a tempdb-t [helyezze a](../../../virtual-machines/azure-vms-no-temp-disk.md) saját elkülönített lemezére vagy tárolókészletére úgy, hogy a gyorsítótárazás csak olvasható legyen. További információ: [tempdb adat-gyorsítótárazás szabályzatok.](performance-guidelines-best-practices-storage.md#data-file-caching-policies)

### <a name="data-disks"></a>Adatlemezek

Az adatlemezek olyan távoli tárolólemezek, amelyek gyakran tárolókészletekbe vannak létrehozva, hogy meghaladják azt a kapacitást és teljesítményt, amit bármely lemez kínálhat a virtuális gépnek. [](/windows-server/storage/storage-spaces/overview)

Csatolja a számítási feladat IOPS-, átviteli és kapacitáskövetelményeinek megfelelő lemezek minimális számát. Ne lépi túl az átméretezni tervezni tervezhető legkisebb virtuális gép adatlemezei maximális számát.

Helyezze az adatokat és a naplófájlokat a teljesítménybeli követelményeknek leginkább megfelelő kiépített adatlemezre. 

Formázza az adatlemezt úgy, hogy 64 KB-os lemezfoglalási egységméretet használjon az ideiglenes meghajtótól (alapértelmezés szerint 4 KB-os) meghajtón elhelyezett összes `D:\` adatfájlhoz. SQL Server virtuális gépek Azure Marketplace lemezei foglalási egységmérettel vannak formázva, és összefűzve a 64 KB-ra beállított tárolókészlethez. 

## <a name="premium-disks"></a>Prémium szintű lemezek

Prémium SSD-lemezek használata adatokhoz és naplófájlokhoz éles SQL Server számítási feladatokhoz. prémium SSD IOPS és sávszélesség mérete a lemez méretétől és [típusától függ.](../../../virtual-machines/disks-types.md) 

Éles számítási feladatokhoz használja a P30- és/vagy P40-lemezeket SQL Server-adatfájlokhoz a gyorsítótárazás támogatásához, és használja a P30–P80 SQL Server tranzakciós naplófájlok esetében.  A legjobb teljes tulajdonosi költség eléréséhez kezdje a P30-asokkal (5000 IOPS/200 MBPS) az adatok és naplófájlok esetén, és csak akkor válasszon magasabb kapacitást, ha szabályozni kell a virtuális gép lemezszámát.

OLTP számítási feladatok esetén a lemezenkénti (vagy tárolókészletenkénti) cél IOPS-értékeket a számítási feladatok csúcsidőszakokban és teljesítményszámlálók használatával teljesítménnyel kapcsolatos `Disk Reads/sec`  +  `Disk Writes/sec` követelményeinek megfelelően kell megfeleltetni. Az adattárház és a jelentéskészítési számítási feladatok esetében a számítási feladatok csúcsidőszakokban és a használatával egyeznek meg a cél átviteli `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` sebességet. 

A Tárolóhelyek használatával optimális teljesítményt érhet el, konfigurálnia kell két készletet, egyet a naplófájl(k)hoz, egyet pedig az adatfájlokhoz. Ha nem használ lemezsávolást, használjon két prémium SSD-lemezt, amelyek külön meghajtókra vannak leképezve, ahol az egyik meghajtó tartalmazza a naplófájlt, a másik pedig az adatokat.

A [tárolókészlet részeként használt,](../../../virtual-machines/disks-types.md#premium-ssd) lemezenkénti kiépített IOPS és átviteli sebesség. A lemezek kombinált IOPS- és átviteli sebességi képességei a virtuális gép átviteli sebességének maximuma.

Az ajánlott eljárás az, ha a lehető legkevesebb lemezt használja, ugyanakkor megfelel az IOPS (és az átviteli sebesség) és a kapacitás minimális követelményeinek. Az ár és a teljesítmény egyensúlya azonban általában jobb, ha nagy számú kis lemezzel, és nem kevés nagy lemezzel.

### <a name="scaling-premium-disks"></a>Prémium szintű lemezek skálázása

Az Azure Managed Disk első üzembe helyezésekor az adott lemez teljesítményszintja a kiépített lemezméreten alapul. Jelölje ki a teljesítményszintet az üzembe helyezéskor, vagy ezt követően módosítsa a lemez méretének módosítása nélkül. Ha az igények növekednek, az üzleti igényeknek megfelelően növelheti a teljesítményszintet. 

A teljesítményszint módosítása lehetővé teszi a rendszergazdák számára, hogy lemezlökés-igénye nélkül készüljenek fel a nagyobb igényekre, és hogy ki is [egészüljenek.](../../../virtual-machines/disk-bursting.md#credit-based-bursting) 

Szükség esetén használja a nagyobb teljesítményt, ha a számlázást úgy tervezték, hogy megfeleljen a tárolási teljesítményszintnek. A kapacitás növelése nélkül frissítse a szintet, hogy megfeleljen a teljesítményre vonatkozó követelményeknek. Térjen vissza az eredeti szintre, ha már nincs szükség további teljesítményre.

Ez a költséghatékony és ideiglenes teljesítménybővítés erős eset olyan célzott eseményeknél, mint a vásárlás, a teljesítményttesztelés, a betanítási események és egyéb rövid időtartamok, ahol csak rövid távon van szükség nagyobb teljesítményre. 

További információ: Felügyelt lemezek [teljesítményszintek.](../../../virtual-machines/disks-change-performance.md) 

## <a name="azure-ultra-disk"></a>Azure ultralemez

Ha kisebb késésű, ezredmásodperc alatti válaszidőre van szükség, fontolja meg [az Azure ultralemezt](../../../virtual-machines/disks-types.md#ultra-disk) az SQL Server-naplómeghajtóhoz, vagy akár az adatmeghajtót olyan alkalmazásokhoz, amelyek rendkívül érzékenyek az I/O-késésre. 

Az ultralemez konfigurálható úgy, hogy a kapacitás és az IOPS egymástól függetlenül skálázható legyen. Az ultralemezek rendszergazdái az alkalmazás igényeinek megfelelően kiépíthet egy lemezt a kapacitásra, az IOPS-teljesítményre és az átviteli sebességre vonatkozó követelményekkel. 

Az ultralemez nem támogatott minden virtuálisgép-sorozaton, és más korlátozásokkal rendelkezik, például a régiók rendelkezésre állása, a redundancia és a virtuális gépek Azure Backup. További információ: [Az Azure ultralemezek használata](../../../virtual-machines/disks-enable-ultra-ssd.md) a korlátozások teljes listájáért. 

## <a name="standard-hdds-and-ssds"></a>Standard HDD-k és HDD-k

[A standard HDD-k](../../../virtual-machines/disks-types.md#standard-hdd) és az SSD-k késése és sávszélessége eltérő, és csak fejlesztési/tesztelési számítási feladatokhoz ajánlottak. Az éles számítási feladatoknak prémium SZINTŰ SSD-ket kell használniuk. Ha virtuális gépeket standard SSD (fejlesztési/tesztelési forgatókönyvek), javasoljuk, hogy adja hozzá a [](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) virtuális gép mérete által támogatott adatlemezek maximális számát, és használjon lemezes csíkozást a Tárolóhelyekkel a legjobb teljesítmény érdekében.

## <a name="caching"></a>Gyorsítótárazás

A Premium Storage-gyorsítótárazást támogató virtuális gépek kihasználhatják az Azure BlobCache vagy a gazdagép-gyorsítótárazás nevű további funkciót a virtuális gépek IOPS- és átviteli sebességi képességeinek kibővítésében. A Premium Storage és a Premium Storage gyorsítótárazása számára is engedélyezett virtuális gépekre ez a két különböző sávszélesség-korlátozás vonatkozik, amelyek együttesen a tárolási teljesítmény javítására használhatók.

Az IOPS és MBps átviteli sebesség gyorsítótárazás nélküli száma a virtuális gép gyorsítótárazás nélküli lemez-átviteli sebességkorlátai között. A gyorsítótárazott maximális korlátok egy további puffert biztosítanak az olvasáshoz, amely segít a növekedés és a váratlan csúcsok címében.

Engedélyezze a prémium gyorsítótárazást, ha a beállítás támogatott, hogy további költségek nélkül jelentősen javítsa az adat meghajtón való olvasások teljesítményét. 

Az Azure BlobCache-be való olvasások és írások (gyorsítótárazott IOPS és átviteli sebesség) nem számítanak bele a virtuális gép gyorsítótárazott IOPS- és átviteli sebességkorlátaiba.

> [!NOTE]
> A lemez-gyorsítótárazás nem támogatott a 4 TiB és nagyobb (P50 vagy nagyobb) lemezeken. Ha több lemez van csatlakoztatva a virtuális géphez, minden 4 TiB-osnál kisebb lemez támogatni fogja a gyorsítótárazást. További információ: [Lemez-gyorsítótárazás.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 

### <a name="uncached-throughput"></a>Nem gyorsítótárazó átviteli sebesség

A lemez maximális nem gyorsítótárazó IOPS-értékével és átviteli sebességével a virtuális gép által kezelhető maximális távoli tárterület-korlát. Ez a korlát a virtuális gépen van meghatározva, és nem a mögöttes lemeztároló korlátja. Ez a korlát csak a virtuális géphez távolról csatlakoztatott adatmeghajtókra vonatkozik, az ideiglenes meghajtón (meghajtón) vagy az operációsrendszer-meghajtón található helyi I/O-re `D:\` nem.

A virtuális gépekhez elérhető gyorsítótárazás nélküli IOPS-érték és átviteli sebesség a virtuális gép dokumentációjában ellenőrizhető.

Az [M-sorozat](../../../virtual-machines/m-series.md) dokumentációja például azt mutatja, hogy a Standard_M8ms virtuális gép maximális gyorsítótárazás nélküli átviteli sebessége 5000 IOPS és 125 MB/s gyorsítótárazás nélküli lemez-átviteli sebesség. 

![Az M-sorozat nem gyorsítótárazású lemez átviteli sebességének dokumentációját bemutató képernyőkép.](./media/performance-guidelines-best-practices/m-series-table.png)

Hasonlóképpen azt is láthatja, hogy a Standard_M32ts 20 000 nem gyorsítótárazó lemez IOPS-t és 500 MB/s gyorsítótárazás nélküli lemez-átviteli sebességet támogat. Ez a korlát a virtuális gép szintjén van korlátozva, függetlenül a mögöttes prémium szintű lemeztárolótól.

További információ: [gyorsítótárazásmentes és gyorsítótárazott korlátok.](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits)


### <a name="cached-and-temp-storage-throughput"></a>Gyorsítótárazott és ideiglenes tároló átviteli sebesség

A gyorsítótárazott és az ideiglenes tárterület maximális átviteli sebességének korlátja külön korlát a virtuális gép gyorsítótárazáson nem tárolt átviteli sebességének korlátja. Az Azure BlobCache a virtuális gép gazdagépe véletlenszerűen elérhető memóriája és a helyileg csatlakoztatott SSD kombinációjából áll. A virtuális gépen található ideiglenes meghajtó (meghajtó) ezen a helyi `D:\` SSD-n is üzemel.

A gyorsítótárazott és ideiglenes tároló maximális átviteli sebességének korlátja csak akkor szabályozza az I/O-t a helyi ideiglenes meghajtóra (meghajtó) és az Azure BlobCache-re, ha a gazdagép `D:\` gyorsítótárazása engedélyezve van.  

Ha a gyorsítótárazás engedélyezve van a prémium szintű tárolón, a virtuális gépek a távoli tároló nem gyorsítótárazású virtuálisgép-IOPS-korlátja és az átviteli sebesség korlátain túl is skálázhatók.  

Csak bizonyos virtuális gépek támogatják a prémium szintű tárolást és a prémium szintű tárolók gyorsítótárazást is (ezt a virtuális gépek dokumentációjában kell ellenőrizni). Az [M-sorozat dokumentációja](../../../virtual-machines/m-series.md) például azt jelzi, hogy a prémium szintű tárolás és a prémium szintű tárolók gyorsítótárazása is támogatott: 

![Képernyőkép az M sorozatú Premium Storage támogatásról.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

A gyorsítótár korlátai a virtuális gép méretétől függően változnak. Az Standard_M8ms virtuális gép például 10000 gyorsítótárazott lemez IOPS-t és 1000 MB/s gyorsítótárazott lemez-átviteli sebességet támogat 793 GiB teljes gyorsítótár-mérettel. Hasonlóképpen, a Standard_M32ts virtuális gép 40000 gyorsítótárazott lemez IOPS-t és 400 MBps gyorsítótárazott lemez-átviteli sebességet támogat 3174 GiB teljes gyorsítótár-mérettel. 

![Képernyőkép az M-sorozat gyorsítótárazott lemezének átviteli sebességével kapcsolatos dokumentációról.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

A gazdagépek gyorsítótárazása manuálisan is engedélyezhető egy meglévő virtuális gépen. Állítsa le az alkalmazás számítási feladatait és SQL Server a virtuális gép gyorsítótárazó házirendjére vonatkozó módosítások előtt. A virtuális gép gyorsítótár-beállításainak módosítása esetén a céllemez a beállítások alkalmazása után lesz leválasztva, majd újra lesz gyorsítótárazva.

### <a name="data-file-caching-policies"></a>Adatfájl-gyorsítótárazás házirendek

A tároló-gyorsítótárazás szabályzata a meghajtón SQL Server adatfájlok típusától függően változik. 

Az alábbi táblázat összefoglalja az ajánlott gyorsítótárazó házirendeket a gyorsítótárazás típusa SQL Server alapján: 

|SQL Server lemez |Ajánlás |
|---------|---------|
| **Adatlemez** | Engedélyezze `Read-only` a gyorsítótárazást az adatfájlokat SQL Server lemezeken. <br/> A gyorsítótárból való olvasás gyorsabb lesz, mint az adatlemez gyorsítótárazásmentes olvasása. <br/> A nem gyorsítótárazott IOPS és átviteli sebesség, valamint a gyorsítótárazott IOPS és az átviteli sebesség a virtuális gép által a virtuális gépek korlátain belül elérhető teljes lehetséges teljesítményt fogja eredményezni, de a tényleges teljesítmény a számítási feladat gyorsítótár-használatra vonatkozó képességének (gyorsítótár-találati arány) függvényében változik. <br/>|
|**Tranzakciónapló-lemez**|Állítsa a gyorsítótárazás házirendet a következőre `None` a tranzakciónaplót üzemeltető lemezeken: .  A tranzakciós naplólemez gyorsítótárazása nem jár teljesítménnyel, és akár az, akár a naplózási meghajtón engedélyezve van, csökkentheti a meghajtó írási teljesítményét, és csökkentheti az adatmeghajtón való olvasáshoz rendelkezésre álló gyorsítótár `Read-only` `Read/Write` mennyiségét.  |
|**Operációsrendszer-lemez** | Az alapértelmezett gyorsítótárazás házirend az operációs rendszer `Read-only` meghajtója vagy `Read/write` lehet. <br/> Az operációsrendszer-meghajtó gyorsítótárazási szintjének módosítása nem ajánlott.  |
| **tempdb**| Ha a tempdb a kapacitás miatt nem helyezhető el a ideiglenes meghajtón, méretezze át a virtuális gépet, hogy nagyobb ideiglenes meghajtót kap, vagy helyezze a tempdb-t egy külön adat meghajtóra, konfigurált `D:\` `Read-only` gyorsítótárazással. <br/> A virtuális gép gyorsítótára és a ideiglenes meghajtó is a helyi SSD-t használja, ezért ezt tartsa szem előtt, amikor tempdb I/O-méretezést használ, beleszámol a gyorsítótárazott IOPS-értékbe és az átviteli sebesség virtuálisgép-korlátaiba, ha a ideiglenes meghajtón van üzemeltetve.| 
| | | 


További információ: [Lemez-gyorsítótárazás.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 


## <a name="disk-striping"></a>Lemezsávolás

Elemezze az SQL-adatfájlokhoz szükséges átviteli sebességet és sávszélességet az adatlemezek számának meghatározásához, beleértve a naplófájlt és a tempdb-t. Az átviteli sebességre és a sávszélességre vonatkozó korlátok a virtuális gép méretétől függően változnak. További információ: Virtuális gép [mérete](../../../virtual-machines/sizes.md)

Adjon hozzá további adatlemezeket, és használjon lemezsávolást a nagyobb átviteli sebesség érdekében. Egy 12 000 IOPS-t és 180 MB/s átviteli sebességet igényló alkalmazás például három csíkozott P30-lemezt használhat a 15 000 IOPS és 600 MB/s átviteli sebesség kézbesítésére. 

A lemezek csíkozásának konfigurálását lásd: [lemezsávolás.](storage-configuration.md#disk-striping) 

## <a name="disk-capping"></a>Lemezkorlátozás 

A lemez és a virtuális gép szintjén is vannak átviteli korlátok. A virtuális gépekre és lemezekre vonatkozó maximális IOPS-korlátok különböznek, és egymástól függetlenek.

Az ezeken a korlátokon túli erőforrásokat felhozó alkalmazások szabályozása (más néven korlátozott). Válasszon ki egy olyan virtuális gépet és lemezméretet egy olyan lemezsávon, amely megfelel az alkalmazás követelményeinek, és nem fog korlátozásokkal szembesülni. A korlátozott használathoz használjon gyorsítótárazást, vagy finomhangolja az alkalmazást úgy, hogy kisebb átviteli sebességre van szükség.

Egy 12 000 IOPS-t és 180 MB/s-t szükséges alkalmazás például a következőre képes: 
- Használja a [Standard_M32ms](../../../virtual-machines/m-series.md) 20 000 IOPS és 500 MB/s maximális gyorsítótárazás nélküli lemez-átviteli sebességgel rendelkezik.
- Három P30 lemez csíkozása 15 000 IOPS és 600 MB/s átviteli sebesség érdekében.
- Használjon egy [Standard_M16ms](../../../virtual-machines/m-series.md) virtuális gépet, és használja a gazdagép-gyorsítótárazást, hogy a helyi gyorsítótárat használja a felhasznált átviteli sebességhez. 

A nagy kihasználtság idején a felskálához konfigurált virtuális gépeknek elegendő IOPS-t és átviteli sebességet kell kiépítenünk a tárolóban a maximális virtuálisgép-méret támogatásához, miközben a lemezek teljes számát a használni kívánt legkisebb virtuálisgép-termékváltozat által támogatott maximális számnál kisebb vagy egyenlően kell tartani.

A lemezkorlátozási korlátozásokkal és a gyorsítótárazás korlátozásának elkerülésével kapcsolatos további információkért lásd: [Lemez I/O-korlátja.](../../../virtual-machines/disks-performance.md)

> [!NOTE] 
> Bizonyos lemezkorlátozások továbbra is kielégítő teljesítményt eredményezhetnek a felhasználók számára; A vállalat költségeinek és teljesítményének kiegyensúlyozása érdekében a számítási feladatok finomhangolása és fenntartása ahelyett, hogy átméretez egy nagyobb virtuális gépre. 


## <a name="write-acceleration"></a>Írásgyorsítás

Az írásgyorsítás egy olyan lemez funkció, amely csak [az M sorozatú](https://docs.microsoft.com/azure/virtual-machines/m-series) virtuális gépekhez Virtual Machines érhető el. Az írásgyorsítás célja, hogy javítsa az Írások I/O-késését az Azure Premium Storage-ban, ha egyszámjegyű I/O-késésre van szükség a nagy mennyiségű kritikus fontosságú OLTP számítási feladatok vagy adattárház-környezetek miatt. 

Az írásgyorsítással javíthatja a naplófájlokat üzemeltető meghajtó írási késését. Ne használja az írásgyorsítást SQL Server adatfájlokhoz. 

írásgyorsító lemez IOPS-korlátja megegyezik a virtuális gépével. A csatlakoztatott lemezek nem haladhatják írásgyorsító virtuális gép IOPS-korlátját.  

Az alábbi táblázat a virtuális gépenként támogatott adatlemezek és IOPS-értékeket ismerteti: 

| Virtuális gép termékváltozata  | # írásgyorsító lemezek  | írásgyorsító lemez IOPS-értékének virtuális gépenkénti lekezelése  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Az írásgyorsítás használatának számos korlátozása van. További információ: [Korlátozások a](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator)írásgyorsító.


### <a name="comparing-to-azure-ultra-disk"></a>Az Azure ultralemezének összehasonlítása

Az írásgyorsítás és az Azure-beli ultralemezek között az a legnagyobb különbség, hogy az írásgyorsítás egy csak M sorozatú virtuálisgép-funkció, az Azure-beli ultralemezek pedig tárolási lehetőségek. Az írásgyorsítás egy írásra optimalizált gyorsítótár, amely a virtuális gép méretétől függően saját korlátozásokkal is jár. Az Azure ultralemezei alacsony késésű lemeztárolási lehetőséget kínálnak az Azure Virtual Machines. 

Ha lehetséges, használja az írásgyorsítást az ultralemezek felett a tranzakciónapló-lemezhez. Olyan virtuális gépek esetében, amelyek nem támogatják az írásgyorsítást, de kis késést igényelnek a tranzakciónaplóhoz, azure-beli ultralemezeket használjon. 

## <a name="monitor-storage-performance"></a>Tárolási teljesítmény figyelése

A tárolási igények felmérése és a tárterület végrehajtásához szükséges teljesítmény meghatározásához tisztában kell vele, hogy mit kell mérni, és mit jelentenek ezek a mutatók. 

[Az IOPS (bemenet/kimenet másodpercenként) az](../../../virtual-machines/premium-storage-performance.md#iops) alkalmazás által a tárterületre másodpercenként lekért kérelmek száma. Az IOPS mérése teljesítményfigyelő számlálók és `Disk Reads/sec` `Disk Writes/sec` használatával. [Az OLTP- (online tranzakciófeldolgozási) alkalmazásoknak](/azure/architecture/data-guide/relational-data/online-transaction-processing) magasabb IOPS-t kell elérniük az optimális teljesítmény eléréséhez. OLTP-alkalmazások például például a fizetési rendszerek, az online vásárlás és az értékesítési pontrendszerek.

[Az átviteli](../../../virtual-machines/premium-storage-performance.md#throughput) sebesség a mögöttes tárolóba küldött adatok mennyisége, amelyet gyakran megabájt/másodpercben mérnek. Az átviteli sebesség mérése a Teljesítményfigyelő számlálói és `Disk Read Bytes/sec` `Disk Write Bytes/sec` segítségével. [Az adatraktározás](/azure/architecture/data-guide/relational-data/data-warehousing) az IOPS-hez maximális átviteli sebességre van optimalizálva. Az olyan alkalmazások, mint az elemzési, jelentéskészítési, ETL-munkafolyamok és más üzletiintelligencia-célok, mind példaként szolgálnak az adatraktározási alkalmazásokra.

Az I/O-egységek méretei befolyásolják az IOPS-t és az átviteli sebességet, mivel a kisebb I/O-méretek nagyobb IOPS-t eredményeznek, a nagyobb I/O-méretek pedig nagyobb átviteli sebességet eredményeznek. SQL Server az optimális I/O-méretet választja automatikusan. További információ: [Optimize IOPS, throughput, and latency for your applications (Az IOPS,](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance)az átviteli sebesség és a késés optimalizálása az alkalmazások számára). 

Vannak olyan Azure Monitor metrikák, amelyek felbecsülhetetlen értékűek a virtuális gép és a lemez szintjén, valamint az AzureBlob-gyorsítótár használatának és állapotának felderítésére. A monitorozási megoldáshoz hozzáadható legfontosabb számlálók azonosításához és az irányítópult Azure Portal: [Storage utilization metrics (Tárhelyhasználati metrikák).](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics) 

> [!NOTE]
> Azure Monitor jelenleg nem kínál lemezszintű metrikákat a ideiglenes `(D:\)` meghajtóhoz. A vm cached IOPS Consumed Percentage (Virtuális gép gyorsítótárazott IOPS-kihasznált aránya) és a Vm Cached Bandwidth Consumed Percentage (A virtuális gép gyorsítótárazott sávszélességének kihasznált százaléka) a ideiglenes meghajtó és a gazdagép gyorsítótárazása együttes IOPS-értékét és átviteli sebességét `(D:\)` tükrözi.


## <a name="next-steps"></a>Következő lépések

A teljesítménnyel kapcsolatos ajánlott eljárásokkal kapcsolatos további információkért tekintse meg a sorozat további cikkeit:
- [Gyors ellenőrzőlista](performance-guidelines-best-practices-checklist.md)
- [Virtuális gép mérete](performance-guidelines-best-practices-vm-size.md)
- [Alapkonfiguráció gyűjtése](performance-guidelines-best-practices-collect-baseline.md)

A biztonsággal kapcsolatos ajánlott eljárásokért lásd: Biztonsági szempontok az [Azure SQL Server való Virtual Machines.](security-considerations-best-practices.md)

Az Azure-beli virtuális SQL Server TPC-E és TPC_C teljesítménytesztek használatával végzett részletes teszteléséhez tekintse meg az [Optimize OLTP performance (OLTP](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794)teljesítményének optimalizálása) blogot.

Tekintse át a SQL Server virtuális gépekről az [Azure SQL Server áttekintésében Virtual Machines cikkeket.](sql-server-on-azure-vm-iaas-what-is-overview.md) Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).
