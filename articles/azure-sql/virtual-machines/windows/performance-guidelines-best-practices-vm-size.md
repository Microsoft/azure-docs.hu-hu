---
title: 'Virtuális gép mérete: ajánlott eljárások a teljesítményhez & irányelvek'
description: A virtuális gépek méretére vonatkozó irányelveket és ajánlott eljárásokat biztosít a SQL Server teljesítményének optimalizálásához az Azure virtuális gépen (VM).
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 9427ae1b9bd68f63df40d24122cc13b5460fbc27
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572409"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>Virtuálisgép-méret: az Azure-beli virtuális gépeken SQL Server teljesítményre vonatkozó ajánlott eljárások
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk a virtuális gépek méretével kapcsolatos útmutatást nyújt az Azure Virtual Machines (VM) SQL Server teljesítményének optimalizálásához ajánlott eljárások és irányelvek sorozatával.

Általában a költségek optimalizálása és a teljesítmény optimalizálása közötti kompromisszumok állnak fenn. A teljesítményre vonatkozó ajánlott eljárások sorozat az Azure Virtual Machines SQL Server *legjobb* teljesítményének beszerzésére koncentrál. Ha a számítási feladat kevésbé igényes, előfordulhat, hogy nem igényel minden javasolt optimalizálást. A javaslatok kiértékelése során vegye figyelembe a teljesítményre vonatkozó igényeket, a költségeket és a számítási feladatok mintáit.


## <a name="checklist"></a>Ellenőrzőlista

Tekintse át a következő feladatlistát a virtuálisgép-mérettel kapcsolatos ajánlott eljárások rövid áttekintéséhez, amelyet a cikk további részében talál részletesebben: 

- Használjon 4 vagy több vCPU rendelkező virtuálisgép-méretet, például a [Standard_M8-4ms](/../../virtual-machines/m-series), a [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)vagy a [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) vagy magasabbat. 
- A SQL Server számítási feladatok legjobb teljesítményéhez használja a [memóriára optimalizált](../../../virtual-machines/sizes-memory.md) virtuálisgép-méreteket. 
- A [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), az [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) sorozat, az [M-](../../../virtual-machines/m-series.md)és a [Mv2](../../../virtual-machines/mv2-series.md) sorozat a OLTP számítási feladatokhoz szükséges optimális memória-virtuális mag arányt kínálja. Mindkét M sorozatú virtuális gép a legnagyobb virtuális mag arányt kínálja a kritikus fontosságú számítási feladatokhoz, és az adatraktár-munkaterhelésekhez is ideális. 
- Vegye fontolóra a kritikus és az adatraktár-számítási feladatok nagyobb virtuális mag arányát. 
- Használja ki az Azure-beli virtuális gépek Marketplace-lemezképeit, mivel a SQL Server beállítások és a tárolási beállítások az optimális SQL Server teljesítményre vannak konfigurálva. 
- Gyűjtse össze a cél számítási feladatok teljesítményének jellemzőit, és használja őket a vállalat megfelelő virtuálisgép-méretének meghatározásához.

Ha össze szeretné hasonlítani a virtuálisgép-méretezési ellenőrzőlistát a többiekkel, tekintse meg az átfogó [teljesítményre vonatkozó ajánlott eljárásokat ellenőrző listát](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Áttekintés

SQL Server Azure-beli virtuális gépen való létrehozásakor körültekintően vegye figyelembe a szükséges számítási feladatok típusát. Ha meglévő környezetet telepít át, [Gyűjtse össze a teljesítmény](performance-guidelines-best-practices-collect-baseline.md) alapkonfigurációját, hogy meghatározza az Azure-beli virtuális gépekre vonatkozó követelményeket SQL Server. Ha ez egy új virtuális gép, akkor hozza létre az új SQL Server VM a gyártói követelmények alapján. 

Ha új SQL Server VM hoz létre a felhőhöz készült új alkalmazással, egyszerűen megváltoztathatja a SQL Server VM, mert az adatok és a használati követelmények fejlődnek.
Indítsa el a fejlesztési környezeteket az alsó szintű D sorozatú, B sorozatú vagy Av2 sorozattal, és növelje a környezetét az idő múlásával. 

Használja a SQL Server VM Marketplace-lemezképeket a portál tárolási konfigurációjával. Ez megkönnyíti a munkaterhelésekhez szükséges méret, IOPS és átviteli sebesség beszerzéséhez szükséges tárolási készletek megfelelő létrehozását. Fontos kiválasztani SQL Server virtuális gépeket, amelyek támogatják a Premium Storage-t és a Premium Storage-gyorsítótárazást. További információért tekintse meg a [Storage](performance-guidelines-best-practices-storage.md) -cikket. 

Az éles OLTP környezet számára ajánlott minimális érték 4 virtuális mag, 32 GB memória, valamint egy memória – virtuális mag arány 8. Új környezetek esetén 4 virtuális mag-gépet kell kezdenie, és az adatok és a számítási követelmények változása esetén a méretezést 8, 16, 32 virtuális mag vagy más értékre kell állítani. OLTP átviteli sebesség esetén a TARGET SQL Server virtuális gépek, amelyek minden virtuális mag 5000 IOPS rendelkeznek. 

SQL Server adattárház és a kritikus fontosságú környezetek gyakran a 8 virtuális mag arányon felül kell méretezni. Közepes környezetekben érdemes lehet 16 virtuális mag arányt választani, valamint egy 32 memória-virtuális mag arányt a nagyobb adattárház-környezetekhez. 

SQL Server adattárház-környezetek gyakran kihasználják a nagyobb gépek párhuzamos feldolgozását. Emiatt az M sorozat és a Mv2 sorozat erős lehetőségeket biztosít a nagyobb adattárház-környezetek számára.

Használja a vCPU és a memória konfigurációját a forrásoldali gépről az aktuális helyszíni SQL Server adatbázis áttelepítésére az Azure-beli virtuális gépeken való SQL Server. Az Azure alapszintű licencének kihasználásával kihasználhatja a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) , és SQL Server licencelési költségekkel is elvégezheti a mentést.

## <a name="memory-optimized"></a>Memóriaoptimalizált

A [memóriára optimalizált virtuálisgép-méretek](../../../virtual-machines/sizes-memory.md) elsődleges cél SQL Server virtuális gépek számára, és a Microsoft által ajánlott választás. A memória-optimalizált virtuális gépek erősebb memória-CPU arányt és közepes – nagy gyorsítótár-beállításokat kínálnak. 

### <a name="m-mv2-and-mdsv2-series"></a>M, Mv2 és Mdsv2 sorozat

Az [M-sorozat](../../../virtual-machines/m-series.md) virtuális mag-számokkal és memóriával rendelkezik a legnagyobb SQL Server munkaterhelések esetében.  

A [Mv2 sorozat](../../../virtual-machines/mv2-series.md) a legmagasabb virtuális mag-számokkal és-memóriával rendelkezik, és ajánlott a kritikus fontosságú és az adattárház-munkaterhelésekhez. A Mv2 sorozat példányai a memóriára optimalizált virtuálisgép-méretek, amelyek páratlan számítási teljesítményt biztosítanak a nagy memóriában lévő adatbázisok és munkaterhelések támogatásához, amelyek nagy memória-CPU aránnyal rendelkeznek, ami tökéletes megoldás a kapcsolódó adatbázis-kiszolgálók, a nagyméretű gyorsítótárak és a memóriabeli elemzések számára.

A [Standard_M64ms](../../../virtual-machines/m-series.md) 28 virtuális mag arányt tartalmaz, például:.

A [Mdsv2 közepes memóriájának sorozata](../../..//virtual-machines/msv2-mdsv2-series.md) egy új m-sorozat, amely jelenleg [előzetes](https://aka.ms/Mv2MedMemoryPreview) verzióban érhető el, és számos m sorozatú Azure-beli virtuális gépet kínál egy midtier. Ezek a gépek kiválóan alkalmasak a SQL Server számítási feladatokhoz, amelyek legalább 10 virtuális mag támogatást biztosítanak akár 30-ig.

Az M és a Mv2 sorozat számos funkciója vonzó a SQL Server teljesítményhez, beleértve a [Premium Storage](../../../virtual-machines/premium-storage-performance.md) -t és a [Premium Storage gyorsítótárazási](../../../virtual-machines/premium-storage-performance.md#disk-caching) támogatását, az [Ultra-lemezes](../../../virtual-machines/disks-enable-ultra-ssd.md) támogatást és az [írási gyorsítást](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Edsv4 sorozat

A [Edsv4-sorozat](../../../virtual-machines/edv4-edsv4-series.md) nagy mennyiségű, nagy teljesítményű alkalmazásokhoz készült. Ezeknek a virtuális gépeknek nagy a helyi tárolója SSD-kapacitása, erős helyi lemez-IOPS, akár 504 GiB a RAM. A legtöbb virtuális gép esetében egy virtuális mag csaknem konzisztens 8 GiB memóriával rendelkezik, amely ideális a standard SQL Server számítási feladatokhoz. 

Ebben a csoportban van egy új virtuális gép a [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) , amely 80 virtuális mag, 504 GB memóriát, valamint egy virtuális mag arányt biztosít 6. Ez a virtuális gép azért fontos, mert el van [különítve](../../../virtual-machines/isolation.md) , így az csak a gazdagépen futó egyetlen virtuális gép számára garantált, ezért a többi ügyfél-munkaterheléstől el van különítve. Ez olyan virtuális mag arányt tartalmaz, amely alacsonyabb, mint a SQL Server esetében ajánlott, ezért csak akkor kell használni, ha elkülönítésre van szükség.

A Edsv4 sorozatú virtuális gépek támogatják a [Premium Storage](../../../virtual-machines/premium-storage-performance.md)-t és a [Premium Storage-gyorsítótárazást](../../../virtual-machines/premium-storage-performance.md#disk-caching).

### <a name="dsv2-series-11-15"></a>DSv2 – 11-15-es sorozat

A [DSv2-sorozat 11-15-](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) es memóriája és a lemez konfigurációja megegyezik az előző D sorozattal. Ez a sorozat konzisztens memória-CPU aránnyal rendelkezik az összes virtuális gépen. Ez a legkisebb memória-optimalizált adatsorozat, amely jó, alacsony díjszabású lehetőséget biztosít a beléptetési szintű SQL Server munkaterhelésekhez.

A [DSv2-series 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) támogatja a [Premium Storage](../../../virtual-machines/premium-storage-performance.md) és a [Premium Storage gyorsítótárazást](../../../virtual-machines/premium-storage-performance.md#disk-caching), ami erősen ajánlott az optimális teljesítmény érdekében.

## <a name="general-purpose"></a>Általános célú

Az [általános célú virtuálisgép-méretek](../../../virtual-machines/sizes-general.md) úgy vannak kialakítva, hogy a kisebb beléptetési szintű munkaterhelések, például a fejlesztés és a tesztelés, a webkiszolgálók és a kisebb adatbázis-kiszolgálók kiegyensúlyozott virtuális mag arányát biztosítsák. 

Az általános célú virtuális gépekkel való kisebb virtuális mag arány miatt fontos a memória-alapú teljesítményszámlálók körültekintő figyelése, hogy SQL Server képes legyen a szükséges puffer-gyorsítótár memóriájának beszerzésére. További információért lásd a [memória-teljesítmény alaptervét](performance-guidelines-best-practices-collect-baseline.md#memory) . 

Mivel az éles számítási feladatokhoz való virtuális mag arány 8, a SQL Server-t futtató általános célú virtuális gépek minimálisan ajánlott konfigurációja 4 vCPU és 32 GB memóriával rendelkezik. 

### <a name="ddsv4-series"></a>Ddsv4 sorozat

A [Ddsv4 sorozat](../../../virtual-machines/ddv4-ddsv4-series.md) a vCPU, a memória és az ideiglenes lemez megfelelő kombinációját kínálja, de kisebb memória-virtuális mag támogatással. 

A Ddsv4 virtuális gépek alacsonyabb késést és nagyobb sebességű helyi tárolót tartalmaznak.

Ezek a gépek ideálisak a párhuzamos SQL-és alkalmazás-telepítésekhez, amelyek gyors hozzáférést igényelnek a temp Storage és a tanszéki kapcsolati adatbázisokhoz. Az ebben a sorozatban található összes virtuális gépen létezik egy standard virtuális mag arány 4. 

Ezért javasoljuk, hogy az ebben a sorozatban lévő kezdő virtuális gépként használja a D8ds_v4, amely 8 virtuális mag és 32 GB memóriával rendelkezik. A legnagyobb gép a D64ds_v4, amely 64 virtuális mag és 256 GB memóriát tartalmaz.

A [Ddsv4 sorozatú](../../../virtual-machines/ddv4-ddsv4-series.md) virtuális gépek támogatják a [Premium Storage](../../../virtual-machines/premium-storage-performance.md) -t és a [Premium Storage-gyorsítótárazást](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> A [Ddsv4 sorozathoz](../../../virtual-machines/ddv4-ddsv4-series.md) nem tartozik a (SQL Server számítási feladatokhoz javasolt 8. számú memória-virtuális mag arány. Ennek megfelelően a virtuális gépeket csak kisebb alkalmazás-és fejlesztési számítási feladatokhoz használhatja.

### <a name="b-series"></a>B sorozat

A [kitört B sorozatú virtuálisgép-](../../../virtual-machines/sizes-b-series-burstable.md) méretek ideálisak olyan munkaterhelésekhez, amelyeknek nincs szükségük konzisztens teljesítményre, például a koncepció igazolására és a nagyon kis alkalmazás-és fejlesztési kiszolgálókra. 

A [kitört B sorozatú](../../../virtual-machines/sizes-b-series-burstable.md) virtuálisgép-méretek többsége virtuális mag aránya 4. A gépek közül a legnagyobb a [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) 20 virtuális mag és 80 GB memóriával.

Ez a sorozat egyedi, mivel az alkalmazások a munkaidőben, a gép méretétől függően változó **feltört** kreditekkel rendelkeznek. 

A kreditek kimerítése után a virtuális gép visszaadja az alapkonfigurációt a gép teljesítményére.

A B sorozat előnye, hogy milyen számítási megtakarítások érhetők el a többi virtuálisgép-mérethez képest, különösen akkor, ha a feldolgozási teljesítményt takarékosan kell megtakarítani a nap folyamán.

Ez a sorozat támogatja a [Premium Storage](../../../virtual-machines/premium-storage-performance.md)használatát, de **nem támogatja** a [Premium Storage-gyorsítótárazást](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> A [feltört B sorozathoz](../../../virtual-machines/sizes-b-series-burstable.md) nem tartozik a virtuális mag arány 8, amelyet SQL Server számítási feladatokhoz ajánlott használni. Ezért érdemes lehet ezeket a virtuális gépeket kisebb alkalmazásokhoz, webkiszolgálókhoz és fejlesztési számítási feladatokhoz használni.

### <a name="av2-series"></a>Av2-sorozat

A [Av2 sorozatú](../../../virtual-machines/av2-series.md) virtuális gépek a legalkalmasabbak a beléptetési szintű munkaterhelésekhez, például a fejlesztéshez és teszteléshez, az alacsony forgalmú webkiszolgálók, a kis-és közepes alkalmazások adatbázisaihoz, valamint a bizonyítási fogalmakhoz.

Ezek az első három virtuális gép esetében csak a [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 virtuális mag és 16GBs), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 virtuális mag és 32GBs), valamint a [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 virtuális mag és 64GBs) megfelelő memória-virtuális mag arány 8. 

Ezek a virtuális gépek egyaránt jó választás a kisebb fejlesztési és tesztelési SQL Server gépekhez. 

A 8 virtuális mag [Standard_A8m_v2](../../../virtual-machines/av2-series.md) is jó megoldás lehet a kis alkalmazások és webkiszolgálók számára.

> [!NOTE] 
> A Av2 sorozat nem támogatja a prémium szintű tárolást, ezért nem ajánlott éles SQL Server számítási feladatokhoz, még azokhoz a virtuális gépekhez is, amelyekhez a virtuális mag aránya 8.

## <a name="storage-optimized"></a>Tárolásra optimalizált

A [tárterületre optimalizált](../../../virtual-machines/sizes-storage.md) virtuálisgép-méretek bizonyos használati esetekre vonatkoznak. Ezek a virtuális gépek kifejezetten optimalizált lemezes átviteli sebességgel és IO-vel vannak kialakítva. 

### <a name="lsv2-series"></a>Lsv2 sorozat

A [Lsv2 sorozat](../../../virtual-machines/lsv2-series.md) nagy teljesítményű, kis késleltetésű és helyi NVMe-tárolóval rendelkezik. A Lsv2 sorozatú virtuális gépek úgy vannak optimalizálva, hogy az állandó adatlemezek használata helyett közvetlenül a virtuális géphez csatolt helyi lemezt használják. 

Ezek a virtuális gépek erős lehetőségek a big data, az adattárház, a jelentéskészítés és az ETL számítási feladatokhoz. A helyi NVMe-tároló nagy átviteli sebessége és IOPS hasznos az adatbázisba betöltendő fájlok feldolgozásához, illetve olyan egyéb forgatókönyvekhez, amelyekben az adatok a forrásrendszer vagy más adattárakból, például az Azure Blob Storage-ból vagy a Azure Data Lakeból is létrehozhatók. [Lsv2 sorozat](../../../virtual-machines/lsv2-series.md) A virtuális gépek egyszerre akár 30 percig is elhelyezhetik a lemez teljesítményét.

Ezek a virtuális gépek mérete 8 – 80 vCPU, vCPU 8 GiB memóriával, és minden 8 vCPU esetében 1,92 TB NVMe SSD. Ez azt jelenti, hogy a sorozat legnagyobb virtuális gépe, a [L80s_v2](../../../virtual-machines/lsv2-series.md)80 vCPU és 640 Bib memória, 10X 1.92 TB NVMe-tárolóval.  Az összes virtuális gép esetében konzisztens virtuális mag-arány 8.

A NVMe-tároló elmúló, ami azt jelenti, hogy ha felszabadítja a virtuális gépet, vagy egy másik gazdagépre helyezi át a szolgáltatást, a rendszer elveszi az adatvesztést a lemezen.

A Lsv2 és az ls sorozat támogatja a [Premium Storage](../../../virtual-machines/premium-storage-performance.md)-t, de a Premium Storage gyorsítótárazása nem. A IOPs növelésére szolgáló helyi gyorsítótár létrehozása nem támogatott. 

> [!WARNING]
> Az adatfájlok ideiglenes NVMe-tárolóban való tárolása adatvesztést eredményezhet a virtuális gép kiosztása esetén. 

## <a name="constrained-vcores"></a>Korlátozott virtuális mag

A nagy teljesítményű SQL Server számítási feladatokhoz gyakran nagyobb mennyiségű memóriára, I/O-re és átviteli sebességre van szükség a magasabb virtuális mag-számok nélkül. 

A legtöbb OLTP számítási feladat nagy számú kisebb tranzakció által vezérelt alkalmazás-adatbázisok. A OLTP számítási feladatokkal csak kis mennyiségű adat olvasása vagy módosítása történik, de a felhasználói számlálások által vezérelt tranzakciók mennyisége sokkal magasabb. Fontos, hogy a SQL Server memória elérhető legyen a gyorsítótár-csomagok számára, tárolja a legutóbb elért adatokat a teljesítmény érdekében, és gondoskodjon arról, hogy a fizikai olvasások gyorsan beolvashatók legyenek a memóriába. 

Ezeknek a OLTP-környezeteknek nagyobb mennyiségű memóriára, gyors tárterületre és az optimális teljesítményhez szükséges I/O-sávszélességre van szükségük. 

A magasabb szintű SQL Server licencelési költségek nélkül az Azure olyan virtuálisgép-méreteket biztosít, amelyek [korlátozott vCPU számítanak](../../../virtual-machines/constrained-vcpu.md). 

Ez segít a licencelési költségek szabályozásában azáltal, hogy csökkenti a rendelkezésre álló virtuális mag, miközben megtartja a szülő virtuális gép ugyanazon memóriáját, tárhelyét és I/O-sávszélességét.

A vCPU száma a virtuális gép eredeti méretének egynegyedére korlátozható. A virtuális gép számára elérhető virtuális mag csökkentése nagyobb memória-virtuális mag arányokat eredményez, de a számítási díj változatlan marad.

Ezek az új virtuálisgép-méretek egy utótaggal rendelkeznek, amely meghatározza az aktív vCPU számát, hogy könnyebben azonosítható legyen. 

Például a [M64-32ms](../../../virtual-machines/constrained-vcpu.md) csak a SQL Server 32-as licencelést igényli, és a [M64ms](../../../virtual-machines/m-series.md) és a M64-16ms virtuális mag a memória, I/O és átviteli sebesség, valamint a [virtuális mag-](../../../virtual-machines/constrained-vcpu.md) csak 16 szükséges.  Bár míg a [M64-16ms](../../../virtual-machines/constrained-vcpu.md) rendelkezik a M64ms licencelési díjainak egy SQL Server negyedével, a virtuális gép számítási díja azonos lesz.

> [!NOTE] 
> - A közepes és nagy adattárház-munkaterhelések továbbra is kihasználhatják a [korlátozott virtuális mag-alapú virtuális gépek](../../../virtual-machines/constrained-vcpu.md)előnyeit, de az adattárház-munkaterheléseket általában kevesebb felhasználó és folyamat kezeli, és a párhuzamosan futó lekérdezési csomagok révén nagyobb mennyiségű adat kezelésére képes. 
> - A számítási díj, amely magában foglalja az operációs rendszer licencelését is, megegyeznek a szülő virtuális géppel. 



## <a name="next-steps"></a>Következő lépések

További információért lásd a sorozat további cikkeit:
- [Gyors ellenőrzőlista](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Alapterv összegyűjtése](performance-guidelines-best-practices-collect-baseline.md)

Az ajánlott biztonsági eljárásokért tekintse [meg az Azure Virtual Machines SQL Server biztonsági szempontjait](security-considerations-best-practices.md).

Tekintse át az Azure-beli SQL Server SQL Server virtuális gépekkel kapcsolatos további cikkeket [Virtual Machines áttekintését](sql-server-on-azure-vm-iaas-what-is-overview.md). Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).
