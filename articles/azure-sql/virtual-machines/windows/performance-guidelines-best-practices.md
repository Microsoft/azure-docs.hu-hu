---
title: Teljesítményre vonatkozó irányelvek az Azure-beli SQL Serverhoz | Microsoft Docs
description: Útmutatást nyújt a Microsoft Azure Virtual Machines SQL Server teljesítményének optimalizálásához.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6ba30436b363353ad183396e07111b33ca912dbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737438"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Teljesítményre vonatkozó irányelvek az Azure Virtual Machines szolgáltatásban futó SQL Serverhez
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk útmutatást nyújt a Microsoft Azure Virtual Machines SQL Server teljesítményének optimalizálásához.

## <a name="overview"></a>Áttekintés

Az Azure Virtual Machines SQL Server futtatásakor javasoljuk, hogy továbbra is ugyanazt az adatbázis-teljesítmény-hangolási beállítást használja, amely a helyszíni kiszolgálói környezetekben SQL Serverre alkalmazható. A relációs adatbázisok nyilvános felhőbeli teljesítménye azonban számos tényezőtől függ, például a virtuális gép méretétől és az adatlemezek konfigurációjától.

[SQL Server a Azure Portal kiépített rendszerképeket az](sql-vm-create-portal-quickstart.md) általános tárolási [konfiguráció ajánlott eljárásainál](storage-configuration.md)követheti. A kiépítés után érdemes lehet a cikkben ismertetett egyéb optimalizálásokat alkalmazni. Kiválaszthatja a számítási feladatokat, és ellenőrizheti a tesztelést.

> [!TIP]
> Általában a költségek optimalizálása és a teljesítmény optimalizálása közötti kompromisszumok állnak fenn. Ez a cikk az Azure Virtual Machines SQL Server *legjobb* teljesítményének beszerzésére koncentrál. Ha a számítási feladat kevésbé igényes, előfordulhat, hogy az alábbiakban felsorolt összes optimalizálás nem szükséges. A javaslatok kiértékelése során vegye figyelembe a teljesítményre vonatkozó igényeket, a költségeket és a számítási feladatok mintáit.

## <a name="quick-checklist"></a>Gyors ellenőrzőlista

A következőkben egy gyors ellenőrzőlista található az Azure Virtual Machines SQL Server optimális teljesítményéről:

| Terület | Optimalizálás |
| --- | --- |
| [Virtuális gép mérete](#vm-size-guidance) | – Használjon 4 vagy több vCPU rendelkező virtuálisgép-méretet, például a [Standard_M8-4ms](../../../virtual-machines/m-series.md), a [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)vagy a [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) vagy magasabbat. <br/><br/> – A SQL Server számítási feladatok legjobb teljesítményéhez használja a [memóriára optimalizált](../../../virtual-machines/sizes-memory.md) virtuálisgép-méreteket. <br/><br/> – A [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), az [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) sorozat, az [M-](../../../virtual-machines/m-series.md)és a [Mv2](../../../virtual-machines/mv2-series.md) sorozat a OLTP számítási feladatokhoz szükséges optimális memória-virtuális mag arányt kínálja. Mindkét M sorozatú virtuális gép a legnagyobb virtuális mag arányt kínálja a kritikus fontosságú számítási feladatokhoz, és az adatraktár-munkaterhelésekhez is ideális. <br/><br/> – A kritikus és az adatraktár-munkaterhelések esetében nagyobb virtuális mag arányra lehet szükség. <br/><br/> – Az Azure-beli virtuális gépek Marketplace rendszerképeinek kihasználása a SQL Server beállítások és a tárolási beállítások az optimális SQL Server teljesítményre vannak konfigurálva. <br/><br/> – Gyűjtsön a cél számítási feladatának teljesítmény-jellemzőit, és használja őket a vállalat megfelelő virtuálisgép-méretének meghatározásához.|
| [Storage](#storage-guidance) | – A SQL Server teljesítményének részletes tesztelése az Azure Virtual Machines TPC-E és TPC_C referenciaértékekkel című részében tájékozódhat a blog [OLTP teljesítményének optimalizálásáról](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> – A [prémium SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) -k használata a legjobb ár/teljesítmény előnyökkel. Konfigurálja az [írásvédett gyorsítótárat](../../../virtual-machines/premium-storage-performance.md#disk-caching) az adatfájlokhoz, és ne legyen gyorsítótár a naplófájlhoz. <br/><br/> – Használjon [Ultra-lemezeket](../../../virtual-machines/disks-types.md#ultra-disk) , ha a munkaterhelés kevesebb, mint 1 MS tárolási késést igényel. További információért lásd: [áttérés az ultra diskre](storage-migrate-to-ultradisk.md) . <br/><br/> – A lemez típusának kiválasztása előtt gyűjtsön adatokat a SQL Server adatok, a napló és [a temp](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) db-fájlok tárolási késési követelményeiről. Ha < 1 – MS tárolási késések szükségesek, akkor használjon Ultra-lemezeket, ellenkező esetben a prémium SSD-t használja. Ha kevés késésre van szükség a naplófájlhoz, és nem az adatfájlok esetében, akkor a szükséges IOPS és az átviteli sebesség szintjén kell [kiépíteni az ultra-lemezt](../../../virtual-machines/disks-enable-ultra-ssd.md) a naplófájlhoz. <br/><br/>  – A standard szintű tárolás csak fejlesztési és tesztelési célokra, illetve biztonsági mentési fájlokhoz ajánlott, és nem használható éles számítási feladatokhoz. <br/><br/> – A [Storage-fiók](../../../storage/common/storage-account-create.md) és a SQL Server VM megtartása ugyanabban a régióban.<br/><br/> – Tiltsa le az Azure [geo-redundáns tárterületet](../../../storage/common/storage-redundancy.md) (Geo-replikáció) a Storage-fiókon.  |
| [Lemezek](#disks-guidance) | – Legalább 2 [prémium SSD-lemez](../../../virtual-machines/disks-types.md#premium-ssd) (1 a naplófájl és 1 adatfájlok esetén) használata. <br/><br/> -< 1 MS IO-késést igénylő munkaterhelések esetében engedélyezze az M sorozat írási gyorsító használatát, és vegye fontolóra ultra SSD lemezeket az es és a DS sorozathoz. <br/><br/> – Engedélyezze az [írásvédett gyorsítótárazást](../../../virtual-machines/premium-storage-performance.md#disk-caching) az adatfájlokat tároló lemezeken.<br/><br/> – A számítási feladatokhoz szükséges további 20%-os prémium szintű IOPS/adatátviteli kapacitás hozzáadása [, ha SQL Server adat-, napló-és tempdb-fájlok tárolását konfigurálja](storage-configuration.md) <br/><br/> -Ne használjon operációs rendszert vagy ideiglenes lemezeket az adatbázis-tároláshoz vagy a naplózáshoz.<br/><br/> -Ne engedélyezze a naplófájlt futtató lemez (ek) gyorsítótárazását.  **Fontos**: az Azure Virtual Machines lemez gyorsítótár-beállításainak módosításakor állítsa le a SQL Server szolgáltatást.<br/><br/> – Több Azure-adatlemezt is megnövelve növelheti a tárolási teljesítményt.<br/><br/> – Dokumentált kiosztási méretekkel rendelkező formátum. <br/><br/> – Helyezze el a TempDB a helyi SSD `D:\` -meghajtón a kritikus fontosságú SQL Server számítási feladatokhoz (a virtuális gép méretének kiválasztása után). Ha a virtuális gépet a Azure Portal vagy az Azure rövid útmutató sablonjai alapján hozza létre, és [a helyi lemezen helyezi el a temp db](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)-t, akkor nincs szükség további műveletekre. minden más esetben kövesse a blogban található lépéseket a  [tempdb tárolásához](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) , hogy megakadályozza a hibákat az újraindítás után. Ha a helyi meghajtó kapacitása nem elegendő a temp adatbázis méretéhez, helyezze a temp DB-t a prémium SSD-lemezeken a [csak olvasható gyorsítótárazással](../../../virtual-machines/premium-storage-performance.md#disk-caching)ellátott Storage- [készletre](../../../virtual-machines/premium-storage-performance.md) . |
| [I/O](#io-guidance) |– Adatbázis-oldal tömörítésének engedélyezése.<br/><br/> – Az azonnali fájl inicializálásának engedélyezése az adatfájlokhoz.<br/><br/> – Korlátozza az adatbázis újranövekedését.<br/><br/> – Tiltsa le az adatbázis autozsugorodását.<br/><br/> – Az összes adatbázis áthelyezése adatlemezekre, beleértve a rendszeradatbázisokat is.<br/><br/> – Helyezze át SQL Server a hibanapló és a nyomkövetési fájl könyvtárait az adatlemezekre.<br/><br/> – Az alapértelmezett biztonsági mentési és adatbázisfájl-tárolóhelyek konfigurálása.<br/><br/> - [Zárolt lapok engedélyezése a memóriában](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).<br/><br/> – Értékelje ki és alkalmazza a SQL Server telepített verziójának [legújabb összesített frissítéseit](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) . |
| [Szolgáltatás-specifikus](#feature-specific-guidance) | – Közvetlenül az Azure Blob Storage-ba történő biztonsági mentés.<br/><br/>– A 12 TB-nál nagyobb adatbázisok esetében a [fájlok pillanatképének biztonsági mentéseit](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) használja. <br/><br/>-Több Temp DB-fájl, 1 fájl/mag, legfeljebb 8 fájl használata.<br/><br/>-A maximális kiszolgálói memória beállítása 90%-os vagy akár 50 GB-ig, az operációs rendszer számára balra. <br/><br/>– Engedélyezze a Soft NUMA-t. |


<br/>
Ha *többet szeretne megtudni* ezekről az optimalizálásokról, tekintse át a következő részben ismertetett *részleteket és* útmutatást.
<br/><br/>

## <a name="getting-started"></a>Első lépések

Ha új SQL Server hoz létre az Azure-beli virtuális gépen, és nem telepít át egy aktuális forrásrendszer-rendszert, akkor a gyártói követelmények alapján hozza létre az új SQL Server VM.  A SQL Server VM gyártói követelményei megegyeznek a helyszíni üzembe helyezésével. 

Ha új SQL Server VM hoz létre a felhőhöz készült új alkalmazással, egyszerűen megváltoztathatja a SQL Server VM, mert az adatok és a használati követelmények fejlődnek.
Indítsa el a fejlesztési környezeteket az alsó szintű D sorozatú, B sorozatú vagy Av2 sorozattal, és növelje a környezetét az idő múlásával. 

Az éles OLTP környezet számára ajánlott minimális érték 4 virtuális mag, 32 GB memória, valamint egy memória – virtuális mag arány 8. Új környezetek esetén 4 virtuális mag-gépet kell kezdenie, és az adatok és a számítási követelmények változása esetén a méretezést 8, 16, 32 virtuális mag vagy más értékre kell állítani. OLTP átviteli sebesség esetén a TARGET SQL Server virtuális gépek, amelyek minden virtuális mag 5000 IOPS rendelkeznek. 

Használja a SQL Server VM Marketplace-lemezképeket a portál tárolási konfigurációjával. Ez megkönnyíti a munkaterhelésekhez szükséges méret, IOPS és átviteli sebesség beszerzéséhez szükséges tárolási készletek megfelelő létrehozását. Fontos kiválasztani SQL Server virtuális gépeket, amelyek támogatják a Premium Storage-t és a Premium Storage-gyorsítótárazást. További információért lásd a [Storage](#storage-guidance) szakaszt. 

SQL Server adattárház és a kritikus fontosságú környezetek gyakran a 8 virtuális mag arányon felül kell méretezni. Közepes környezetekben érdemes lehet 16 mag-memória arányt választani, valamint egy 32-es mag-memória arányt a nagyobb adattárház-környezetekhez. 

SQL Server adattárház-környezetek gyakran kihasználják a nagyobb gépek párhuzamos feldolgozását. Emiatt az M sorozat és a Mv2 sorozat erős lehetőségeket biztosít a nagyobb adattárház-környezetek számára.

## <a name="vm-size-guidance"></a>VIRTUÁLIS gépek méretére vonatkozó útmutató

Használja a vCPU és a memória konfigurációját a forrásoldali gépről az aktuális helyszíni SQL Server adatbázis áttelepítésére az Azure-beli virtuális gépeken való SQL Server. Az Azure alapszintű licencének kihasználásával kihasználhatja a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) , és SQL Server licencelési költségekkel is elvégezheti a mentést.

**A Microsoft azt javasolja, hogy a virtuális mag arány 8 legyen kiindulási pontként az éles SQL Server munkaterhelések számára.** A kisebb arányok elfogadhatók a nem éles környezetben üzemelő számítási feladatokhoz. 

A számítási feladatok (OLTP vagy adattárház) alapján kiválaszthat egy [optimalizált](../../../virtual-machines/sizes-memory.md), [általános célú](../../../virtual-machines/sizes-general.md), [tárterületre optimalizált](../../../virtual-machines/sizes-storage.md)vagy [korlátozott virtuális mag](../../../virtual-machines/constrained-vcpu.md) virtuálisgép-méretet, amely a legalkalmasabb a SQL Server teljesítményhez. 

### <a name="memory-optimized"></a>Memóriaoptimalizált

A [memóriára optimalizált virtuálisgép-méretek](../../../virtual-machines/sizes-memory.md) elsődleges cél SQL Server virtuális gépek számára, és a Microsoft által ajánlott választás. A memória-optimalizált virtuális gépek erősebb memória-CPU arányt és közepes – nagy gyorsítótár-beállításokat kínálnak. 

#### <a name="m-and-mv2-series"></a>M és Mv2 sorozat

Az [M-sorozat](../../../virtual-machines/m-series.md) virtuális mag-számokkal és memóriával rendelkezik a legnagyobb SQL Server munkaterhelések esetében.  

A [Mv2 sorozat](../../../virtual-machines/mv2-series.md) a legmagasabb virtuális mag-számokkal és-memóriával rendelkezik, és ajánlott a kritikus fontosságú és az adattárház-munkaterhelésekhez. A Mv2 sorozat példányai a memóriára optimalizált virtuálisgép-méretek, amelyek páratlan számítási teljesítményt biztosítanak a nagy memóriában lévő adatbázisok és munkaterhelések támogatásához, amelyek nagy memória-CPU aránnyal rendelkeznek, ami tökéletes megoldás a kapcsolódó adatbázis-kiszolgálók, a nagyméretű gyorsítótárak és a memóriabeli elemzések számára.

A [Standard_M64ms](../../../virtual-machines/m-series.md) 28 virtuális mag arányt tartalmaz, például:.

Az M és a Mv2 sorozat számos funkciója vonzó a SQL Server teljesítményhez, beleértve a [Premium Storage](../../../virtual-machines/premium-storage-performance.md) -t és a [Premium Storage gyorsítótárazási](../../../virtual-machines/premium-storage-performance.md#disk-caching) támogatását, az [Ultra-lemezes](../../../virtual-machines/disks-enable-ultra-ssd.md) támogatást és az [írási gyorsítást](../../../virtual-machines/how-to-enable-write-accelerator.md).

#### <a name="edsv4-series"></a>Edsv4 sorozat

A [Edsv4-sorozat](../../../virtual-machines/edv4-edsv4-series.md) nagy mennyiségű, nagy teljesítményű alkalmazásokhoz készült. Ezek a virtuális gépek nagy méretű helyi tárolási SSD-kapacitással, erős helyi lemez-IOPS, akár 504 GiB RAM-mal, valamint a korábbi Ev3-/Esv3-méretekhez képest, Gen2 virtuális gépekkel. Ezekhez a virtuális gépekhez nagyjából 8 virtuális mag arány érhető el, ami ideális a standard SQL Server számítási feladatokhoz. 

Ez a virtuálisgép-sorozat ideális olyan nagy teljesítményű nagyvállalati alkalmazásokhoz és alkalmazásokhoz, amelyek alacsony késésű, nagy sebességű helyi tárterületet igényelnek.

A Edsv4 sorozatú virtuális gépek támogatják a [Premium Storage](../../../virtual-machines/premium-storage-performance.md)-t és a [Premium Storage-gyorsítótárazást](../../../virtual-machines/premium-storage-performance.md#disk-caching).

#### <a name="dsv2-series-11-15"></a>DSv2 – 11-15-es sorozat

A [DSv2-sorozat 11-15-](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) es memóriája és a lemez konfigurációja megegyezik az előző D sorozattal. Ez a sorozat konzisztens memória-CPU aránnyal rendelkezik az összes virtuális gépen. 

A [DSv2-series 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) támogatja a [Premium Storage](../../../virtual-machines/premium-storage-performance.md) és a [Premium Storage gyorsítótárazást](../../../virtual-machines/premium-storage-performance.md#disk-caching), ami erősen ajánlott az optimális teljesítmény érdekében.

### <a name="general-purpose"></a>Általános célú

Az [általános célú virtuálisgép-méretek](../../../virtual-machines/sizes-general.md) úgy vannak kialakítva, hogy a kisebb beléptetési szintű munkaterhelések, például a fejlesztés és a tesztelés, a webkiszolgálók és a kisebb adatbázis-kiszolgálók kiegyensúlyozott virtuális mag arányát biztosítsák. 

Az általános célú virtuális gépekkel való kisebb virtuális mag arány miatt fontos a memória-alapú teljesítményszámlálók körültekintő figyelése, hogy SQL Server képes legyen a szükséges puffer-gyorsítótár memóriájának beszerzésére. További információért lásd a [memória-teljesítmény alaptervét](#memory) . 

Mivel az éles számítási feladatokhoz való virtuális mag arány 8, a SQL Server-t futtató általános célú virtuális gépek minimálisan ajánlott konfigurációja 4 vCPU és 32 GB memóriával rendelkezik. 

#### <a name="ddsv4-series"></a>Ddsv4 sorozat

A [Ddsv4 sorozat](../../../virtual-machines/ddv4-ddsv4-series.md) a vCPU, a memória és az ideiglenes lemez megfelelő kombinációját kínálja, de kisebb memória-virtuális mag támogatással. 

A Ddsv4 virtuális gépek alacsonyabb késést és nagyobb sebességű helyi tárolót tartalmaznak.

Ezek a gépek ideálisak a párhuzamos SQL-és alkalmazás-telepítésekhez, amelyek gyors hozzáférést igényelnek a temp Storage és a tanszéki kapcsolati adatbázisokhoz. Az ebben a sorozatban található összes virtuális gépen létezik egy standard virtuális mag arány 4. 

Ezért javasoljuk, hogy az ebben a sorozatban lévő kezdő virtuális gépként használja a D8ds_v4, amely 8 virtuális mag és 32 GB memóriával rendelkezik. A legnagyobb gép a D64ds_v4, amely 64 virtuális mag és 256 GB memóriát tartalmaz.

A [Ddsv4 sorozatú](../../../virtual-machines/ddv4-ddsv4-series.md) virtuális gépek támogatják a [Premium Storage](../../../virtual-machines/premium-storage-performance.md) -t és a [Premium Storage-gyorsítótárazást](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> A [Ddsv4 sorozathoz](../../../virtual-machines/ddv4-ddsv4-series.md) nem tartozik a (SQL Server számítási feladatokhoz javasolt 8. számú memória-virtuális mag arány. Ennek megfelelően a virtuális gépeket csak kisebb alkalmazás-és fejlesztési számítási feladatokhoz használhatja.

#### <a name="b-series"></a>B sorozat

A [kitört B sorozatú virtuálisgép-](../../../virtual-machines/sizes-b-series-burstable.md) méretek ideálisak olyan munkaterhelésekhez, amelyeknek nincs szükségük konzisztens teljesítményre, például a koncepció igazolására és a nagyon kis alkalmazás-és fejlesztési kiszolgálókra. 

A [kitört B sorozatú](../../../virtual-machines/sizes-b-series-burstable.md) virtuálisgép-méretek többsége virtuális mag aránya 4. A gépek közül a legnagyobb a [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) 20 virtuális mag és 80 GB memóriával.

Ez a sorozat egyedi, mivel az alkalmazások a munkaidőben, a gép méretétől függően változó **feltört** kreditekkel rendelkeznek. 

A kreditek kimerítése után a virtuális gép visszaadja az alapkonfigurációt a gép teljesítményére.

A B sorozat előnye, hogy milyen számítási megtakarítások érhetők el a többi virtuálisgép-mérethez képest, különösen akkor, ha a feldolgozási teljesítményt takarékosan kell megtakarítani a nap folyamán.

Ez a sorozat támogatja a [Premium Storage](../../../virtual-machines/premium-storage-performance.md)használatát, de **nem támogatja** a [Premium Storage-gyorsítótárazást](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> A [feltört B sorozathoz](../../../virtual-machines/sizes-b-series-burstable.md) nem tartozik a virtuális mag arány 8, amelyet SQL Server számítási feladatokhoz ajánlott használni. Ezért érdemes lehet ezeket a virtuális gépeket kisebb alkalmazásokhoz, webkiszolgálókhoz és fejlesztési számítási feladatokhoz használni.

#### <a name="av2-series"></a>Av2-sorozat

A [Av2 sorozatú](../../../virtual-machines/av2-series.md) virtuális gépek a legalkalmasabbak a beléptetési szintű munkaterhelésekhez, például a fejlesztéshez és teszteléshez, az alacsony forgalmú webkiszolgálók, a kis-és közepes alkalmazások adatbázisaihoz, valamint a bizonyítási fogalmakhoz.

Ezek az első három virtuális gép esetében csak a [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 virtuális mag és 16GBs), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 virtuális mag és 32GBs), valamint a [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 virtuális mag és 64GBs) megfelelő memória-virtuális mag arány 8. 

Ezek a virtuális gépek egyaránt jó választás a kisebb fejlesztési és tesztelési SQL Server gépekhez. 

A 8 virtuális mag [Standard_A8m_v2](../../../virtual-machines/av2-series.md) is jó megoldás lehet a kis alkalmazások és webkiszolgálók számára.

> [!NOTE] 
> A Av2 sorozat nem támogatja a prémium szintű tárolást, ezért nem ajánlott éles SQL Server számítási feladatokhoz, még azokhoz a virtuális gépekhez is, amelyekhez a virtuális mag aránya 8.

### <a name="storage-optimized"></a>Tárolásra optimalizált

A [tárterületre optimalizált](../../../virtual-machines/sizes-storage.md) virtuálisgép-méretek bizonyos használati esetekre vonatkoznak. Ezek a virtuális gépek kifejezetten optimalizált lemezes átviteli sebességgel és IO-vel vannak kialakítva. Ez a virtuálisgép-sorozat big data forgatókönyvekhez, adatraktározáshoz és nagy tranzakciós adatbázisokhoz készült. 

#### <a name="lsv2-series"></a>Lsv2 sorozat

A [Lsv2 sorozat](../../../virtual-machines/lsv2-series.md) nagy teljesítményű, kis késleltetésű és helyi NVMe-tárolóval rendelkezik. A Lsv2 sorozatú virtuális gépek úgy vannak optimalizálva, hogy az állandó adatlemezek használata helyett közvetlenül a virtuális géphez csatolt helyi lemezt használják. 

Ezek a virtuális gépek erős lehetőségek a big data, az adattárház, a jelentéskészítés és az ETL számítási feladatokhoz. A helyi NVMe-tároló nagy átviteli sebessége és IOPs hasznos az adatbázisba betöltendő fájlok feldolgozásához, illetve olyan egyéb forgatókönyvekhez, amelyekben a forrásadatok újra létrehozhatók a forrásrendszer vagy más Tárházak, például az Azure Blob Storage vagy a Azure Data Lake segítségével. [Lsv2 sorozat](../../../virtual-machines/lsv2-series.md) A virtuális gépek egyszerre akár 30 percig is elhelyezhetik a lemez teljesítményét.

Ezek a virtuális gépek mérete 8 – 80 vCPU, vCPU 8 GiB memóriával, és minden 8 vCPU esetében 1,92 TB NVMe SSD. Ez azt jelenti, hogy a sorozat legnagyobb virtuális gépe, a [L80s_v2](../../../virtual-machines/lsv2-series.md)80 vCPU és 640 Bib memória, 10X 1.92 TB NVMe-tárolóval.  Az összes virtuális gép esetében konzisztens virtuális mag-arány 8.

A NVMe-tároló elmúló, ami azt jelenti, hogy a virtuális gép újraindításakor a rendszer elveszi az adatvesztést a lemezeken.

A Lsv2 és az ls sorozat támogatja a [Premium Storage](../../../virtual-machines/premium-storage-performance.md)-t, de a Premium Storage gyorsítótárazása nem. A IOPs növelésére szolgáló helyi gyorsítótár létrehozása nem támogatott. 

> [!WARNING]
> Az adatfájlok ideiglenes NVMe-tárolóban való tárolása adatvesztést eredményezhet a virtuális gép kiosztása esetén. 

### <a name="constrained-vcores"></a>Korlátozott virtuális mag

A nagy teljesítményű SQL Server munkaterheléseknek gyakran nagyobb mennyiségű memóriára, IO-ra és átviteli sebességre van szükségük a magasabb virtuális mag-szám nélkül. 

A legtöbb OLTP számítási feladat nagy számú kisebb tranzakció által vezérelt alkalmazás-adatbázisok. A OLTP számítási feladatokkal csak kis mennyiségű adat olvasása vagy módosítása történik, de a felhasználói számlálások által vezérelt tranzakciók mennyisége sokkal magasabb. Fontos, hogy a SQL Server memória elérhető legyen a gyorsítótár-csomagok számára, tárolja a legutóbb elért adatokat a teljesítmény érdekében, és gondoskodjon arról, hogy a fizikai olvasások gyorsan beolvashatók legyenek a memóriába. 

Ezeknek a OLTP-környezeteknek nagyobb mennyiségű memóriára, gyors tárterületre és az optimális teljesítményhez szükséges I/O-sávszélességre van szükségük. 

A magasabb szintű SQL Server licencelési költségek nélkül az Azure olyan virtuálisgép-méreteket biztosít, amelyek [korlátozott vCPU számítanak](../../../virtual-machines/constrained-vcpu.md). 

Ez segít a licencelési költségek szabályozásában azáltal, hogy csökkenti a rendelkezésre álló virtuális mag, miközben megtartja a szülő virtuális gép ugyanazon memóriáját, tárhelyét és I/O-sávszélességét.

A vCPU száma a virtuális gép eredeti méretének egynegyedére korlátozható. A virtuális gép számára elérhető virtuális mag csökkentése nagyobb memória-virtuális mag arányokat érhet el.

Ezek az új virtuálisgép-méretek egy utótaggal rendelkeznek, amely meghatározza az aktív vCPU számát, hogy könnyebben azonosítható legyen. 

A [M64-32ms](../../../virtual-machines/constrained-vcpu.md) például csak a SQL Server 32-as licencelést igényli a [M64ms](../../../virtual-machines/m-series.md) memóriával, IO-vel és átviteli sebességével, valamint a [M64-16ms](../../../virtual-machines/constrained-vcpu.md) virtuális mag csak 16 virtuális mag szükséges.  Bár míg a [M64-16ms](../../../virtual-machines/constrained-vcpu.md) rendelkezik a M64ms licencelési díjainak egy SQL Server negyedével, a virtuális gép számítási díja azonos lesz.

> [!NOTE] 
> - A közepes és nagy adattárház-munkaterhelések továbbra is kihasználhatják a [korlátozott virtuális mag-alapú virtuális gépek](../../../virtual-machines/constrained-vcpu.md)előnyeit, de az adattárház-munkaterheléseket általában kevesebb felhasználó és folyamat kezeli, és a párhuzamosan futó lekérdezési csomagok révén nagyobb mennyiségű adat kezelésére képes. 
> - A számítási díj, amely magában foglalja az operációs rendszer licencelését is, megegyeznek a szülő virtuális géppel. 

## <a name="storage-guidance"></a>Storage – útmutató

Az Azure Virtual Machines a TPC-E és a TPC-C teljesítménytesztek SQL Server teljesítményének részletes teszteléséhez tekintse meg a blog [OLTP teljesítményének optimalizálása](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)című témakört. 

Az Azure Blob cache prémium SSD-k használata minden éles számítási feladathoz ajánlott. 

> [!WARNING]
> A szabványos HDD-k és SSD-k eltérő késéssel és sávszélességgel rendelkeznek, és csak fejlesztési/tesztelési feladatokhoz ajánlottak. Az éles számítási feladatoknak prémium SSD-ket kell használniuk.

Azt javasoljuk továbbá, hogy az Azure Storage-fiókot ugyanabban az adatközpontban hozza létre, mint a SQL Server virtuális gépek az átvitel késésének csökkentése érdekében. A Storage-fiók létrehozásakor a földrajzi replikálást a több lemezre kiterjedő konzisztens írási sorrendként tiltsa le. Ehelyett érdemes lehet egy SQL Server vész-helyreállítási technológiát beállítani két Azure-adatközpont között. További információk: [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md) (Magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó SQL Serveren).

## <a name="disks-guidance"></a>Útmutató a lemezekhez

Az Azure Virtual Machines szolgáltatásban három fő lemez létezik:

* **Operációsrendszer-lemez**: Ha Azure-beli virtuális gépet hoz létre, a platform legalább egy lemezt ( **c** meghajtóként címkézett) csatlakoztat a virtuális géphez az operációs rendszer lemezéhez. Ez a lemez egy, a tárolóban blobként tárolt VHD.
* **Ideiglenes lemez**: az Azure Virtual Machines egy másik lemezt tartalmaz, amely az ideiglenes lemez ( **D**: meghajtóként van megjelölve). Ez egy lemez a csomóponton, amely felhasználható a tárhelyhez.
* **Adatlemezek**: további lemezeket is csatolhat a virtuális géphez adatlemezként, és ezeket a rendszer a tárolóban, az oldal blobokban tárolja.

A következő szakaszok a különböző lemezek használatára vonatkozó javaslatokat ismertetik.

### <a name="operating-system-disk"></a>Operációsrendszer-lemez

Az operációsrendszer-lemez olyan virtuális merevlemez, amely az operációs rendszer futó verziójaként indítható el és csatlakoztatható, és **C** meghajtóként van megjelölve.

Az operációs rendszer lemezének alapértelmezett gyorsítótárazási házirendje **írható/olvasható**. A teljesítményre érzékeny alkalmazások esetében javasoljuk, hogy az operációs rendszer lemeze helyett adatlemezeket használjon. Tekintse meg az alábbi adatlemezek című szakaszt.

### <a name="temporary-disk"></a>Ideiglenes lemez

A **D** meghajtóként megjelölt ideiglenes tárolóeszköz nem marad meg az Azure Blob Storage-ban. Ne tárolja a felhasználói adatbázis fájljait vagy a felhasználói tranzakció naplófájljait a **D**: meghajtón.

Helyezze a TempDB a helyi SSD `D:\` -meghajtóra a kritikus fontosságú SQL Server számítási feladatokhoz (a virtuális gép méretének kiválasztása után). Ha a virtuális gépet a Azure Portal vagy az Azure rövid útmutató sablonjai alapján hozza létre, és [a helyi lemezen helyezi el a temp db](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)-t, akkor nincs szükség további műveletekre. minden más esetben kövesse a blogban található lépéseket a  [tempdb tárolásához](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) , hogy megakadályozza a hibákat az újraindítás után. Ha a helyi meghajtó kapacitása nem elegendő a temp adatbázis méretéhez, helyezze a temp DB-t a prémium SSD-lemezeken a [csak olvasható gyorsítótárazással](../../../virtual-machines/premium-storage-performance.md#disk-caching)ellátott Storage- [készletre](../../../virtual-machines/premium-storage-performance.md) .

A prémium SSD-ket támogató virtuális gépek esetében a TempDB olyan lemezen is tárolhatja, amely támogatja a prémium SSD-ket, és engedélyezve van az olvasási gyorsítótárazás.


### <a name="data-disks"></a>Adatlemezek

* **Prémium szintű SSD-lemezek használata az adatfájlokhoz és naplófájlokhoz**: Ha nem használ lemezes csíkozást, akkor használjon két prémium szintű SSD-lemezt, ahol az egyik lemez tartalmazza a naplófájlt, a másik pedig tartalmazza az adatfájlt. A prémium szintű SSD-k számos IOPS és sávszélességet (MB/s) biztosítanak a mérettől függően, a cikkben látható módon, a [lemez típusának kiválasztásával](../../../virtual-machines/disks-types.md). Ha lemezes csíkozási technikát (például tárolóhelyeket) használ, az optimális teljesítményt úgy érheti el, hogy két készlettel rendelkezik, egyet a naplófájl (ok) hoz, és a másikat az adatfájlokhoz. Ha azonban SQL Server feladatátvevő fürt példányait (verzió) kívánja használni, konfigurálnia kell egy készletet, vagy a [prémium fájlmegosztás](failover-cluster-instance-premium-file-share-manually-configure.md) használatát kell használnia.

   > [!TIP]
   > - A különböző lemezekre és munkaterhelés-konfigurációkra vonatkozó tesztelési eredményekért tekintse meg a következő blogbejegyzést: [tárolási konfigurációs irányelvek az Azure Virtual Machines SQL Serverához](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm).
   > - A ~ 50 000 IOPS-t igénylő SQL-kiszolgálók esetében a kritikus fontosságú teljesítmény érdekében érdemes megfontolni a 10 P30 lemez cseréjét egy ultra SSD. További információkért tekintse meg a következő blogbejegyzést: a [kritikus teljesítmény](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)kiosztása Ultra SSDokkal.

   > [!NOTE]
   > Amikor kiépít egy SQL Server VM a portálon, lehetősége van a tárolási konfiguráció szerkesztésére. A konfigurációtól függően az Azure egy vagy több lemezt konfigurál. Több lemez egyetlen, csíkozásos tárolóba van összevonva. Ebben a konfigurációban az adatfájlok és a naplófájlok is együtt találhatók. További információ: [SQL Server virtuális gépek tárolási konfigurációja](storage-configuration.md).

* **Lemezes csíkozás**: a további átviteli sebesség érdekében további adatlemezeket adhat hozzá, és a lemezek csíkozását is használhatja. Az adatlemezek számának meghatározásához elemezni kell a naplófájl (ok) hoz szükséges IOPS és sávszélességet, valamint az adatait és a TempDB-fájl (oka) t. Figyelje meg, hogy a különböző virtuálisgép-méretek eltérő korlátokkal rendelkeznek a támogatott IOPs és sávszélességgel kapcsolatban: a IOPS lévő táblák a [virtuális gépek méretétől](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)függően. Használja az alábbi irányelveket:

  * Windows 8/Windows Server 2012 vagy újabb rendszer esetén a [tárolóhelyek](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) a következő irányelvekkel használhatók:

      1. Az adattárház-számítási feladatokhoz a OLTP számítási feladatainak és a 256 KB 262 144 (65 536 bájt) értékének beállításával állítsa be az adattárolási munkaterhelések teljesítményének romlását, hogy elkerülje 64 a partíciók hibás igazítása miatti hatást. Ezt a PowerShell-lel kell beállítani.
      2. Oszlopok számának beállítása = fizikai lemezek száma. A PowerShell használata a több mint 8 lemez konfigurálásához (a Kiszolgálókezelő felhasználói felülete nem). 

    A következő PowerShell például létrehoz egy új tárolási készletet az összevont mérettel 64 KB értékre, és az oszlopok számát, amely a tárolási készlet fizikai lemezének mennyiségével egyenlő:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 vagy korábbi verzió esetén a dinamikus lemezeket (operációsrendszer-csíkozott kötetek) is használhatja, és a sáv mérete mindig 64 KB. Ez a beállítás a Windows 8/Windows Server 2012 operációs rendszertől kezdve elavult. További információ: a virtuális lemez szolgáltatás támogatási nyilatkozata a [Windows Storage Management API-ra való áttérést](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api)ismerteti.

  * Ha [közvetlen tárolóhelyekt (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) használ [SQL Server feladatátvevő fürt példányaival](failover-cluster-instance-storage-spaces-direct-manually-configure.md), egyetlen készletet kell konfigurálnia. Bár a különböző kötetek hozhatók létre az adott készleten, mind ugyanazok a jellemzők, mint például a gyorsítótárazási házirend.

  * Határozza meg a tárolási készlethez társított lemezek számát a betöltési várakozások alapján. Ne feledje, hogy a különböző virtuálisgép-méretek különböző számú csatlakoztatott adatlemezt tesznek lehetővé. További információ: [virtuális gépek méretei](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Ha nem a prémium szintű SSD-ket (fejlesztési/tesztelési forgatókönyvek) használja, a javaslat a [virtuális gép mérete](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) által támogatott adatlemezek maximális számának hozzáadására és a lemezes csíkozás használatára.

* **Gyorsítótárazási házirend**: a tárolási konfigurációtól függően a gyorsítótárazási házirendre vonatkozó következő ajánlásokat kell figyelembe venni.

  * Ha külön lemezeket használ az adatfájlok és a naplófájlok számára, engedélyezze az olvasási gyorsítótárazást az adatfájlokat tároló adatlemezeken és a TempDB-adatfájlokban. Ez jelentős teljesítménybeli haszonhoz vezethet. A naplófájlt tároló lemezen ne engedélyezze a gyorsítótárazást, mivel ez kisebb mértékű csökkenést okoz a teljesítményben.

  * Ha egyetlen tárolóban használ lemezes szalagot, a legtöbb számítási feladat kihasználhatja az olvasási gyorsítótárazást. Ha a napló-és adatfájlok külön tárolóval rendelkeznek, engedélyezze az olvasási gyorsítótárazást csak az adatfájlok tárolási készletén. Bizonyos nagy mennyiségű írási feladatokban a jobb teljesítmény a gyorsítótárazás nélkül is megvalósítható. Ezt csak teszteléssel lehet meghatározni.

  * Az előző javaslatok a prémium SSD-k esetében érvényesek. Ha nem használ prémium SSD-ket, ne engedélyezzen semmilyen gyorsítótárazást az adatlemezeken.

  * A lemezek gyorsítótárazásának konfigurálásával kapcsolatos utasításokért tekintse meg a következő cikkeket. A klasszikus (ASM) üzembe helyezési modellnél lásd: [set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) és [set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). A Azure Resource Manager telepítési modell esetében lásd: [set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) és [set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Állítsa le a SQL Server szolgáltatást az Azure Virtual Machines-lemezek gyorsítótár-beállításainak módosításakor, hogy elkerülje az adatbázis sérülésének lehetőségét.

* **NTFS-foglalási egység mérete**: az adatlemez formázása esetén ajánlott 64 KB-os foglalási egység méretet használni az adatfájlok és a naplófájlok, valamint a tempdb számára. Ha a TempDB az ideiglenes lemezre van helyezve (D:\ meghajtó) a meghajtót kihasználó teljesítmény a 64 KB-os foglalási egység méretének szükségességét méri. 

* **Lemezkezelés – ajánlott eljárások**: adatlemez eltávolításakor vagy a gyorsítótár típusának módosításához állítsa le a SQL Server szolgáltatást a módosítás során. Ha megváltoznak a gyorsítótárazási beállítások az operációsrendszer-lemezen, az Azure leállítja a virtuális gépet, megváltoztatja a gyorsítótár típusát, és újraindítja a virtuális gépet. Az adatlemezek gyorsítótár-beállításainak módosításakor a virtuális gép nem áll le, de az adatlemez le van választva a virtuális gépről a módosítás során, majd újra csatolva lesz.

  > [!WARNING]
  > Ha nem sikerül leállítani a SQL Server szolgáltatást ezen műveletek során, az adatbázis sérülését okozhatja.


## <a name="io-guidance"></a>I/O-útmutatás

* Az alkalmazás és a kérések integrálással a legjobb prémium SSD-k eredményei érhetők el. A prémium SSD-k olyan forgatókönyvek esetén lettek kialakítva, ahol az IO-várólista mélysége 1-nél nagyobb, így az egyszálas soros kérések esetében kevés vagy semmilyen teljesítménybeli nyereség fog megjelenni (még akkor is, ha a tárterület-igényes). Ez hatással lehet például a teljesítmény-elemzési eszközök, például a SQLIO egyszálas tesztelési eredményeire.

* Érdemes lehet az [adatbázis-lapok tömörítését](/sql/relational-databases/data-compression/data-compression) használni, mivel az nagy I/O-igényű számítási feladatok teljesítményének javításához is hozzájárul. Előfordulhat azonban, hogy az adattömörítés növelheti a CPU-felhasználást az adatbázis-kiszolgálón.

* Érdemes lehet engedélyezni az azonnali fájl inicializálását, hogy csökkentse a kezdeti fájl lefoglalásához szükséges időt. Az azonnali fájl inicializálásának kihasználásához adja meg a SQL Server (MSSQLSERVER) szolgáltatásfiókot a SE_MANAGE_VOLUME_NAME, és vegye fel a **kötet-karbantartási feladatok elvégzése** biztonsági szabályzatba. Ha SQL Server platform-rendszerképet használ az Azure-hoz, a rendszer nem adja hozzá az alapértelmezett szolgáltatásfiókot (NT Service\MSSQLSERVER) a **kötet-karbantartási feladatok végrehajtása** biztonsági házirendhez. Más szóval az azonnali fájl inicializálása nincs engedélyezve egy SQL Server Azure platform-rendszerképben. Miután hozzáadta a SQL Server szolgáltatásfiókot a **kötet-karbantartási feladatok elvégzése** biztonsági házirendhez, indítsa újra a SQL Server szolgáltatást. Ennek a funkciónak a használatához biztonsági megfontolásokat lehet használni. További információ: [adatbázisfájlok inicializálása](/sql/relational-databases/databases/database-instant-file-initialization).

* Vegye **figyelembe, hogy az automatikus** növekedés csak váratlan növekedést okozó várakozási kockázat. Az automatikus növekedéssel nem kezelheti az adatait, és napi rendszerességgel is naplózhatja a növekedést. Ha az automatikus növekedés használatban van, a méret kapcsoló használatával növelje a fájl előzetes növekedését.

* Ügyeljen arra, hogy a szükségtelen terhelés elkerülése érdekében az **AutoShrink** le legyen tiltva, ami negatív hatással lehet a teljesítményre.

* Az összes adatbázis áthelyezése adatlemezekre, beleértve a rendszeradatbázisokat is. További információ: [rendszeradatbázisok áthelyezése](/sql/relational-databases/databases/move-system-databases).

* Helyezze át SQL Server a hibanapló és a nyomkövetési fájl könyvtárait az adatlemezekre. Ezt a SQL Server Konfigurációkezelő az SQL Server példányra jobb gombbal kattintva, majd a tulajdonságok elemet választva végezheti el. A hibanapló és a nyomkövetési fájl beállításai módosíthatók az **Indítási paraméterek** lapon. A memóriakép könyvtára a **speciális** lapon van megadva. Az alábbi képernyőfelvételen a hibanapló indítási paraméterének megkeresése látható.

    ![Az SQL ErrorLog képernyőképe](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Állítsa be az alapértelmezett biztonsági mentési és adatbázisfájlok helyét. Használja az ebben a cikkben szereplő javaslatokat, majd hajtsa végre a módosításokat a kiszolgáló tulajdonságai ablakban. Útmutatásért lásd: az adatfájlok [és naplófájlok alapértelmezett helyeinek megtekintése vagy módosítása (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files). Az alábbi képernyőképen a módosításokat hajthatja végre.

    ![Az SQL-adatnapló és a biztonságimásolat-fájlok](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Az i/o és a lapozási tevékenységek csökkentéséhez engedélyezze a zárolt lapokat. További információ: [a zárolási lapok engedélyezése a memóriában (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).

* Ha SQL Server 2012-es verzióját futtatja, telepítse a 10. összegző frissítést. Ez a frissítés azt a javítást tartalmazza, amely gyenge teljesítményt tesz elérhetővé az I/O-ben, ha a SELECT INTO ideiglenes TABLE utasítást SQL Server 2012-ban hajtja végre. További információkért tekintse meg ezt a [tudásbázist ismertető cikket](https://support.microsoft.com/kb/2958012).

* Az Azure-ba történő átvitelkor érdemes lehet bármilyen adatfájlt tömöríteni.

## <a name="feature-specific-guidance"></a>Szolgáltatás-specifikus útmutató

Néhány üzemelő példány további teljesítménybeli előnyöket érhet el fejlettebb konfigurációs módszerek használatával. Az alábbi lista néhány olyan SQL Server funkciót mutat be, amelyek segítségével jobb teljesítményt érhet el:

### <a name="back-up-to-azure-storage"></a>Biztonsági mentés az Azure Storage-ba
Ha az Azure Virtual Machines-ban futó SQL Server biztonsági mentéseit hajtja végre, az [URL-címre SQL Server biztonsági mentést](/sql/relational-databases/backup-restore/sql-server-backup-to-url)használhat. Ez a funkció SQL Server 2012 SP1 CU2 kezdődően érhető el, és ajánlott a csatlakoztatott adatlemezekre történő biztonsági mentéshez. Az Azure Storage-ba történő biztonsági mentés/visszaállítás során kövesse az Azure [Storage-ban tárolt biztonsági másolatokból származó ajánlott eljárásokat és hibaelhárítási és visszaállítási SQL Server biztonsági mentéssel](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)kapcsolatos ajánlásokat. Ezeket a biztonsági mentéseket automatizálhatja [Az Azure Virtual Machines SQL Server automatikus biztonsági mentésével](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)is.

SQL Server 2012 előtt a [SQL Server Backup az Azure-eszközre](https://www.microsoft.com/download/details.aspx?id=40740)használható. Ez az eszköz segít a biztonsági másolatok átviteli sebességének növelésében több biztonsági mentési szalagos cél használatával.

### <a name="sql-server-data-files-in-azure"></a>Adatfájlok SQL Server az Azure-ban

Ez az új funkció, [SQL Server adatfájlok az Azure-ban](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure), a SQL Server 2014-es verziótól kezdődően érhető el. Az Azure-beli adatfájlokkal rendelkező SQL Server az Azure-adatlemezeket használó hasonló teljesítménybeli jellemzőkkel bírnak.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Feladatátvevő fürt példánya és tárolóhelyek

Ha tárolóhelyeket használ, amikor csomópontokat ad hozzá a fürthöz a **jóváhagyás** lapon, törölje az **összes megfelelő tároló hozzáadása a fürthöz** feliratú jelölőnégyzet jelölését. 

![Jogosult tároló törlése](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Ha tárolóhelyeket használ, és nem törli **az összes megfelelő tároló hozzáadása a fürthöz** lehetőséget, a Windows leválasztja a virtuális lemezeket a fürtözési folyamat során. Ennek eredményeképpen azok nem jelennek meg a Lemezkezelés eszközben vagy az Intézőben, amíg a tárolóhelyek el nem lesznek távolítva a fürtből, és nem kapcsolódnak újra a PowerShell használatával. A tárolóhelyek több lemezt is csoportosítanak a Storage-készletekbe. További információ: [tárolóhelyek](/windows-server/storage/storage-spaces/overview).

## <a name="multiple-instances"></a>Több példány 

Ha több SQL Server példányt telepít egyetlen virtuális gépre, vegye figyelembe az alábbi ajánlott eljárásokat: 

- Állítsa be az egyes SQL Server-példányok maximális kiszolgálói memóriáját, és győződjön meg róla, hogy az operációs rendszer megmaradt a memóriában. Ügyeljen arra, hogy frissítse a SQL Server példányok memória-korlátozásait, ha módosítja a virtuális gép számára lefoglalt memória mennyiségét. 
- Külön LUN-nak kell lennie az adatokhoz, a naplókhoz és a TempDB, mivel ezek mind eltérő számítási feladatok, és nem szeretné, hogy azok hatással legyenek egymásra. 
- Alaposan tesztelje környezetét nagy teljesítményű, hasonló számítási feladatokkal, hogy az alkalmazáson belül képes legyen kezelni a maximális munkaterhelés-kapacitást. 

A túlterhelt rendszerek jelei tartalmazhatnak, de nem korlátozódnak a munkavégző szálra, a lassú válaszidőra és/vagy az elakadt kiosztó rendszermemóriára. 



## <a name="collect-performance-baseline"></a>Teljesítmény-alapterv gyűjtése

A részletesebb megközelítés érdekében a teljesítményszámlálók a PerfMon/LogMan és a capture SQL Server WAIT statisztikát használják, hogy jobban megértsék a forrás-környezet általános terheléseit és potenciális szűk keresztmetszeteit. 

Először gyűjtsön össze a CPU, a memória, a [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), az [átviteli sebesség](../../../virtual-machines/premium-storage-performance.md#throughput)és a [késés](../../../virtual-machines/premium-storage-performance.md#latency) a forrás számítási [feladatra vonatkozóan az alkalmazás teljesítményének ellenőrzőlista](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)utáni csúcsidőben. 

Az adatok gyűjtése csúcsidőben, például munkaterhelések alatt, a szokásos munkanap során, de más nagy terhelésű folyamatok, például a napi feldolgozás és a hétvégi ETL-munkaterhelések esetében is. Érdemes lehet erőforrásokat felméretezni a szokatlanul nagy számítási feladatokhoz, például a negyedév végén történő feldolgozáshoz, majd a munkaterhelés befejezése után a méretezést. 

A teljesítmény elemzése lehetőséggel válassza ki a [virtuális gép méretét](../../../virtual-machines/sizes-memory.md) , amely méretezhető a számítási feladatok teljesítményére vonatkozó követelmények alapján.


### <a name="iops-and-throughput"></a>IOPS és átviteli sebesség

SQL Server a teljesítmény nagy mértékben függ az I/O-alrendszertől. Ha az adatbázisa nem fér bele a fizikai memóriába, SQL Server folyamatosan a pufferben lévő és kívüli adatbázis-lapokat hozza ki. A SQL Server adatfájljait eltérően kell kezelni. A naplófájlokhoz való hozzáférés szekvenciális, kivéve, ha egy tranzakciót vissza kell állítani, ahol az adatfájlok, beleértve a TempDB is, véletlenszerűen érhetők el. Ha lassú I/O-alrendszert használ, a felhasználók olyan teljesítménnyel kapcsolatos problémákat tapasztalhatnak, mint például a lassú válaszidő és az időtúllépés miatt nem teljesített feladatok. 

Az Azure Marketplace-beli virtuális gépek olyan fizikai lemezeken található naplófájlok, amelyek alapértelmezés szerint különállóak az adatfájlokból. A TempDB-adatfájlok száma és mérete megfelel az ajánlott eljárásoknak, és az elmúló D:/ meghajtó.. 

A következő PerfMon-számlálók segítségével ellenőrizheti a SQL Server által igényelt IO-átviteli sebességet: 
* **\LogicalDisk\Disk olvasás/mp** (olvasási és írási IOPS)
* **\LogicalDisk\Disk írás gyakorisága (művelet/mp** ) (olvasási és írási IOPS) 
* **\LogicalDisk\Disk bájt/mp** (adatátviteli követelmények az adatokhoz, naplóhoz és tempdb fájlokhoz)

A IOPS és az átviteli sebességre vonatkozó követelmények csúcs szinten való használata esetén a mérések kapacitásának megfelelő virtuálisgép-méreteket kell kiértékelni. 

Ha a számítási feladathoz 20 K olvasási IOPS és 10K írási IOPS van szüksége, akkor választhat E16s_v3 (legfeljebb 32 K gyorsítótáras és 25600 gyorsítótár nélküli IOPS) vagy M16_s (legfeljebb 20 K gyorsítótárazott és 10K gyorsítótáras IOPS), 2 P30 lemezzel, a tárolóhelyek használatával. 

Ügyeljen arra, hogy a számítási feladatok átviteli és IOPS követelményeit a virtuális gépek különböző méretezési korlátokkal rendelkezzenek a IOPS és az átviteli sebesség tekintetében.

### <a name="memory"></a>Memória

Nyomon követheti az operációs rendszer által használt külső memóriát, valamint SQL Server által belsőleg használt memóriát is. Bármelyik összetevő esetében a terhelés azonosítása a virtuális gépek méretét és a hangolási lehetőségek azonosítását segíti. 

A következő PerfMon-számlálók segítségével ellenőrizheti SQL Server virtuális gép memóriájának állapotát: 
* [\Memory\Available MB-ban](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: memória-Manager\Target kiszolgáló memóriája (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: memória-Manager\Total kiszolgáló memóriája (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: puffer Manager\Lazy írás gyakorisága (művelet/mp)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: a puffer Manager\page Reads várható élettartama](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>Számítás/feldolgozás

Az Azure-beli számításokat a helyszínen eltérően kezelik. A helyszíni kiszolgálók az elmúlt néhány évben a felügyeleti terhelés és az új hardver beszerzésének költségei miatt nem frissültek. A virtualizálás elhárítja ezeket a problémákat, de az alkalmazások a mögöttes hardver legnagyobb előnyeit optimalizálják, ami azt jelenti, hogy az erőforrás-felhasználás jelentős változása szükségessé teszi a teljes fizikai környezet újrakiegyensúlyozását. 

Ez nem olyan kihívás az Azure-ban, ahol egy új virtuális gép egy másik sorozatban, vagy akár egy másik régióban is könnyen elérhető. 

Az Azure-ban a lehető legtöbb előnyt kívánja kihasználni a virtuális gépek erőforrásainak, ezért az Azure-beli virtuális gépeket úgy kell konfigurálni, hogy az átlagos CPU-t a lehető legmagasabbra állítsa, a munkaterhelés befolyásolása nélkül. 

A következő PerfMon-számlálók segíthetnek ellenőrizni egy SQL Server virtuális gép számítási állapotát:
* **\Processor információ (_Total) \% processzoridő**
* **\Process (sqlservr) \% processzoridő**

> [!NOTE] 
> Ideális esetben próbálja meg a számítási kapacitás 80%-át használni, a 90% feletti csúcsok mellett, de nem éri el a 100%-ot bármilyen tartós időszakra. Alapvetően csak szeretné kiépíteni az alkalmazás által igényelt számítási feltételt, majd az üzleti igényeknek megfelelően meg kell terveznie a méretezést. 

## <a name="next-steps"></a>Következő lépések

Az ajánlott biztonsági eljárásokért tekintse [meg az Azure Virtual Machines SQL Server biztonsági szempontjait](security-considerations-best-practices.md).

Tekintse át az Azure-beli SQL Server SQL Server virtuális gépekkel kapcsolatos további cikkeket [Virtual Machines áttekintését](sql-server-on-azure-vm-iaas-what-is-overview.md). Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).