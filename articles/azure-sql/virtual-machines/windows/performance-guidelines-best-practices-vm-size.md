---
title: 'Virtuális gép mérete: Teljesítménnyel kapcsolatos ajánlott eljárások és & útmutató'
description: A virtuális gépek méretével kapcsolatos irányelveket és ajánlott eljárásokat tartalmaz az Azure-beli virtuális SQL Server virtuális gépeken található virtuális gépek teljesítményének optimalizálásához.
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
ms.openlocfilehash: 88adef7ea50744f913780d99594ce3baadade84b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600896"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>Virtuális gép mérete: Azure-beli virtuális SQL Server teljesítményével kapcsolatos ajánlott eljárások
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk útmutatást nyújt a virtuális gépek méretéhez az Azure SQL Server (VM) szolgáltatásban Virtual Machines teljesítmény optimalizálásához.

Általában a költségek optimalizálása és a teljesítményre való optimalizálás között van különbség. A teljesítménnyel kapcsolatos ajánlott eljárások  sorozatának célja, hogy a lehető legjobb teljesítményt SQL Server Azure-Virtual Machines. Ha a számítási feladat kevésbé teljesítményigényes, előfordulhat, hogy nincs szükség minden javasolt optimalizálásra. A javaslatok kiértékelése során vegye figyelembe a teljesítménybeli igényeket, a költségeket és a számítási feladatok mintáit.


## <a name="checklist"></a>Ellenőrzőlista

Az alábbi ellenőrzőlista rövid áttekintést nyújt a virtuális gép méretével kapcsolatos ajánlott eljárásokról, amelyekről a cikk további részében részletesebben olvashat: 

- Használjon 4 vagy több vCPU-val (például [Standard_M8-4ms,](/azure/virtual-machines/m-series) [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series), vagy a DS12_v2 [nagyobb](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) méretű virtuális gépeket. 
- Használjon [memóriaoptimalizált](../../../virtual-machines/sizes-memory.md) virtuálisgép-méreteket a számítási feladatok SQL Server érdekében. 
- A [DSv2 11–15,](../../../virtual-machines/dv2-dsv2-series-memory.md) [az Edsv4](../../../virtual-machines/edv4-edsv4-series.md) sorozat, az [M–](/azure/virtual-machines/m-series)és az [Mv2 sorozat](../../../virtual-machines/mv2-series.md) az OLTP számítási feladatokhoz szükséges optimális memória–virtuális arányt kínálják. Mindkét M sorozatú virtuális gép a kritikus fontosságú számítási feladatokhoz szükséges legmagasabb memória–virtuális arányt kínálják, és ideálisak az adattárház számítási feladataihoz is. 
- Vegye figyelembe a kritikus fontosságú és az adattárház számítási feladataihoz szükséges memória–virtuális mag arányt. 
- Használja ki az Azure Virtual Machine Marketplace-rendszerképeket, mivel SQL Server beállítások és tárolási lehetőségek az optimális teljesítmény SQL Server konfigurálva. 
- Gyűjtse össze a cél számítási feladat teljesítményjellemzőit, és használja őket a vállalata számára megfelelő virtuálisgép-méret meghatározásához.

A virtuális gép méretének ellenőrzőlistája a többivel való összehasonlításához tekintse meg a teljesítményre vonatkozó ajánlott eljárások átfogó [ellenőrzőlistát.](performance-guidelines-best-practices-checklist.md) 

## <a name="overview"></a>Áttekintés

Amikor Azure-beli virtuális SQL Server hoz létre, alaposan mérlegelje a szükséges számítási feladatok típusát. Meglévő környezet áttelepítésekor gyűjtsön [](performance-guidelines-best-practices-collect-baseline.md) teljesítmény-alapkonfigurációt az Azure-beli virtuális SQL Server követelményeinek megfelelő teljesítmény meghatározásához. Ha ez egy új virtuális gép, hozza létre az új SQL Server VM a szállítói követelmények alapján. 

Ha új alkalmazást SQL Server VM a felhőhöz készült új alkalmazással, az adat- és használati követelmények SQL Server VM méretének megfelelően könnyedén méretelheti az alkalmazásokat.
Indítsa el a fejlesztési környezeteket az alacsonyabb szintű D-, B- vagy Av2-sorozatokkal, és idővel növelje környezetét. 

Használja a SQL Server VM Marketplace-rendszerképeket a tárolókonfigurációval a portálon. Így könnyebben hozhatja létre megfelelően a számítási feladatokhoz szükséges méretet, IOPS-t és átviteli sebességet a tárolókészletek létrehozásához. Fontos, hogy olyan virtuális gépeket SQL Server, amelyek támogatják a prémium szintű tárolást és a prémium szintű tároló-gyorsítótárazást. További információt [a storage-cikkben](performance-guidelines-best-practices-storage.md) talál. 

Éles OLTP-környezet esetén a javasolt minimális érték 4 virtuális mag, 32 GB memória, a memória és a virtuális magok aránya pedig 8. Új környezetek esetén kezdjen 4 virtuális maggal, és skálázható 8, 16, 32 virtuális magra vagy többre, ha az adatokra és a számítási feladatokra vonatkozó követelmények változnak. Az OLTP átviteli sebességének eléréséhez minden virtuális SQL Server 5000 IOPS-t meg kell célozni. 

SQL Server adattárházat és az alapvető fontosságú környezeteket gyakran a 8 memória–virtuális mag arányon túl kell méretezni. Közepes környezetben 16 memória–virtuális mag arányt, nagyobb adattárház-környezetek esetén pedig 32 memória–virtuális mag arányt választhat. 

SQL Server adattárház-környezetek esetében gyakran előnyös a nagyobb gépek párhuzamos feldolgozása. Emiatt az M és az Mv2 sorozat erős lehetőségeket kínál a nagyobb adattárház-környezetekhez.

A forrásgép vCPU- és memóriakonfigurációját alapkonfigurációként használhatja az aktuális helyszíni SQL Server adatbázis Azure-beli virtuális gépekre SQL Server való áttelepítésére. Az alaplicenc az Azure-ban való használatával kihasználhatja a [Azure Hybrid Benefit,](https://azure.microsoft.com/pricing/hybrid-benefit/) és pénzt takaríthat meg SQL Server licencelési költségekből.

## <a name="memory-optimized"></a>Memóriaoptimalizált

A [memóriaoptimalizált virtuálisgép-méretek](../../../virtual-machines/sizes-memory.md) elsődleges célként SQL Server virtuális gépekhez és a Microsoft által javasolt választáshoz. A memóriaoptimalizált virtuális gépek erősebb memória–PROCESSZOR arányt, valamint közepes és nagy gyorsítótár-beállításokat kínálnak. 

### <a name="m-mv2-and-mdsv2-series"></a>M, Mv2 és Mdsv2 sorozat

Az [M-sorozat](/azure/virtual-machines/m-series) virtuális magok számát és memóriát kínál a legnagyobb számítási SQL Server számára.  

Az [Mv2 sorozat](../../../virtual-machines/mv2-series.md) rendelkezik a legmagasabb virtuális magszámokkal és memóriával, és a kritikus fontosságú és az adattárház számítási feladataihoz ajánlott. Az Mv2-sorozat példányai memóriaoptimalizálatlan számítási teljesítményt nyújtó, memóriaoptimalizálatlan virtuálisgép-méretek, amelyek nagy memóriabeli adatbázisokat és számítási feladatokat támogatnak magas memória-processzor arány mellett, ami tökéletes a relációs adatbázis-kiszolgálókhoz, a nagy gyorsítótárakhoz és a memórián belül való elemzéshez.

A [Standard_M64ms](/azure/virtual-machines/m-series) 28 memória–virtuális mag arányt mutat.

[Az Mdsv2 Közepes](../../..//virtual-machines/msv2-mdsv2-series.md) memória sorozat egy új [](https://aka.ms/Mv2MedMemoryPreview) M-sorozat, amely jelenleg előzetes verzióban érhető el, és több M-es szintű Azure-beli virtuális gépet kínál középszintű memóriaajánlattal. Ezek a gépek az olyan számítási feladatokhoz SQL Server, amelyek legalább 10, legfeljebb 30 memória–virtuális mag támogatással vannak támogatva.

Az M és az Mv2 sorozat teljesítménybeli vonzó funkciói [](../../../virtual-machines/premium-storage-performance.md) közé tartozik [](../../../virtual-machines/premium-storage-performance.md#disk-caching) SQL Server prémium szintű tárolás és a prémium szintű tároló-gyorsítótárazás támogatása, az [ultralemezes](../../../virtual-machines/disks-enable-ultra-ssd.md) támogatás és az [írásgyorsítás.](../../../virtual-machines/how-to-enable-write-accelerator.md)

### <a name="edsv4-series"></a>Edsv4 sorozat

Az [Edsv4 sorozat memóriaigényes](../../../virtual-machines/edv4-edsv4-series.md) alkalmazásokhoz készült. Ezek a virtuális gépek nagy helyi TÁROLÓ SSD-kapacitással, erős helyi lemez IOPS-értékekkel és akár 504 GiB RAM-mal rendelkeznek. A virtuális gépek nagy része közel konzisztensen 8 GiB memóriát biztosít virtuális magonként, ami ideális a standard SQL Server számítási feladatokhoz. 

Ebben a csoportban található egy új virtuális gép a [Standard_E80ids_v4,](../../../virtual-machines/edv4-edsv4-series.md) amely 80 virtuális magot és 504 GBs memóriát kínál, és a memória és a virtuális magok aránya 6. Ez a virtuális gép azért [](../../../virtual-machines/isolation.md) fontos, mert el van különítve, ami azt jelenti, hogy ez lesz a gazdagépen futó egyetlen virtuális gép, ezért el van különítve a többi ügyfél számítási feladattól. Ez a memória–virtuális mag arány alacsonyabb, mint az ajánlott SQL Server, ezért csak akkor érdemes használni, ha elkülönítésre van szükség.

Az Edsv4 sorozatú virtuális gépek támogatják a [prémium](../../../virtual-machines/premium-storage-performance.md)szintű tárolást és a [prémium szintű tároló-gyorsítótárazást.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

### <a name="dsv2-series-11-15"></a>DSv2 sorozat, 11–15

A [DSv2 11–15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) sorozat memória- és lemezkonfigurációi ugyanazok, mint az előző D-sorozatnak. Ez a sorozat 7-es konzisztens memória-processzor arányt biztosít az összes virtuális gépen. Ez a memóriaoptimalált sorozatok legkisebb része, és jó alacsony költségű megoldás a belépési szintű SQL Server számítási feladatokhoz.

A [DSv2 11–15-ös](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) sorozata támogatja a prémium szintű tárolást és a prémium szintű tárolók gyorsítótárazást, ami erősen ajánlott az optimális teljesítmény érdekében. [](../../../virtual-machines/premium-storage-performance.md) [](../../../virtual-machines/premium-storage-performance.md#disk-caching)

## <a name="general-purpose"></a>Általános célú

Az [általános célú virtuálisgép-méretek](../../../virtual-machines/sizes-general.md) úgy vannak kialakítva, hogy kiegyensúlyozott memória–virtuális arányokat biztosítanak a kisebb, belépési szintű számítási feladatokhoz, például a fejlesztéshez és teszteléshez, a webkiszolgálókhoz és a kisebb adatbázis-kiszolgálókhoz. 

Az általános célú virtuális gépek memória–virtuális mag arányának kisebb aránya miatt fontos a memóriaalapú teljesítményszámlálók körültekintő figyelése, hogy az SQL Server el tudja-e kapni a szükséges puffer-gyorsítótár-memóriát. További [információkért lásd a memóriateljesítmény](performance-guidelines-best-practices-collect-baseline.md#memory) alapkonfigurációját. 

Mivel az éles számítási feladatokra vonatkozó kezdő javaslat 8-as memória–virtuális mag arány, a SQL Server-t futtató általános célú virtuális gépek minimális javasolt konfigurációja 4 vCPU és 32 GB memória. 

### <a name="ddsv4-series"></a>Ddsv4 sorozat

A [Ddsv4 sorozat](../../../virtual-machines/ddv4-ddsv4-series.md) a vCPU, a memória és az ideiglenes lemez megfelelő kombinációját kínálja, de kisebb memória–virtuális mag támogatással. 

A Ddsv4 virtuális gépek kisebb késést és nagyobb sebességű helyi tárterületet tartalmaznak.

Ezek a gépek olyan egymás mellett üzemelő SQL- és alkalmazástelepítések esetén ideálisak, amelyek gyors hozzáférést igényelnek az ideiglenes tárolókhoz és a részlegek relációs adatbázisához. A sorozat összes virtuális gépe esetében a standard memória–virtuális mag arány 4. 

Ezért javasoljuk, hogy a D8ds_v4 a sorozat kezdő virtuális gépeként használja, amely 8 virtuális magot és 32 GBs memóriát biztosít. A legnagyobb gép a D64ds_v4, amely 64 virtuális magot és 256 GBs memóriát rendelkezik.

A [Ddsv4 sorozatú](../../../virtual-machines/ddv4-ddsv4-series.md) virtuális gépek támogatják a [prémium szintű tárolást](../../../virtual-machines/premium-storage-performance.md) és a prémium szintű [tároló-gyorsítótárazást.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE]
> A [Ddsv4 sorozat](../../../virtual-machines/ddv4-ddsv4-series.md) nem rendelkezik 8-as memória–virtuális mag aránysal, amely ajánlott SQL Server számítási feladatokhoz. Ezért érdemes megfontolni, hogy ezeket a virtuális gépeket csak kisebb alkalmazás- és fejlesztési számítási feladatokhoz használja.

### <a name="b-series"></a>B sorozat

Az [adatlökhető B sorozatú virtuálisgép-méretek](../../../virtual-machines/sizes-b-series-burstable.md) ideálisak az olyan számítási feladatokhoz, amelyekhez nem szükséges konzisztens teljesítmény, például a koncepció igazolása, valamint nagyon kis méretű alkalmazás- és fejlesztőkiszolgálók. 

Az [adatlökésekkel kezelhető B sorozatú](../../../virtual-machines/sizes-b-series-burstable.md) virtuális gépek legtöbb méretének memória–virtuális mag aránya 4. A gépek közül a legnagyobb a [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) 20 virtuális maggal és 80 GB memóriával.

Ez a sorozat azért egyedi,  mert az alkalmazások a gép méretétől függően változó, adatlökhető kreditekkel képesek a munkaidőben meglökkedni. 

Ha a kreditek kimerülnek, a virtuális gép visszatér az alapgép teljesítményéhez.

A B sorozat előnye, hogy a számítási megtakarítások a többi sorozat más virtuálisgép-méretekhez képest elérhetőek, különösen akkor, ha a napi feldolgozási teljesítmény takarékosan szükséges.

Ez a sorozat támogatja a [prémium szintű tárolást,](../../../virtual-machines/premium-storage-performance.md)de **nem támogatja** a Premium [Storage gyorsítótárazást.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE] 
> Az [adatlökhető B-sorozat](../../../virtual-machines/sizes-b-series-burstable.md) nem rendelkezik 8-as memória–virtuális mag aránysal, amely ajánlott a SQL Server számítási feladatokhoz. Ezért érdemes ezeket a virtuális gépeket kisebb alkalmazásokhoz, webkiszolgálókhoz és fejlesztési számítási feladatokhoz használni.

### <a name="av2-series"></a>Av2-sorozat

Az [Av2-sorozat](../../../virtual-machines/av2-series.md) virtuális gépei olyan belépési szintű számítási feladatokhoz a legalkalmasabbak, mint a fejlesztés és tesztelés, az alacsony forgalmú webkiszolgálók, a kis és közepes alkalmazás-adatbázisok és a koncepció igazolása.

Ezen [három virtuális gép](../../../virtual-machines/av2-series.md) esetében csak a Standard_A2m_v2 (2 virtuális mag és 16 GBs memória), a [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 virtuális mag és 32 GBs memória) és a [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 virtuális mag és 64 GBs memória) aránya jó 8. 

Ezek a virtuális gépek egyaránt jó lehetőségek a kisebb fejlesztési és tesztelési SQL Server gépekhez. 

A 8 [virtuális magos Standard_A8m_v2](../../../virtual-machines/av2-series.md) kis alkalmazások és webkiszolgálók esetén is jó választás lehet.

> [!NOTE] 
> Az Av2 sorozat nem támogatja a prémium szintű tárolást, ezért nem ajánlott éles SQL Server számítási feladatokhoz még a 8-as memória–virtuális mag arányú virtuális gépek esetében sem.

## <a name="storage-optimized"></a>Tárolásra optimalizált

A [tárolásra optimalizált virtuálisgép-méretek](../../../virtual-machines/sizes-storage.md) adott esetekben használhatók. Ezek a virtuális gépek kifejezetten optimalizált lemez-átviteli sebességre és I/O-teljesítményre vannak tervezve. 

### <a name="lsv2-series"></a>Lsv2 sorozat

Az [Lsv2 sorozat](../../../virtual-machines/lsv2-series.md) nagy átviteli sebességet, kis késést és helyi NVMe-tárolót biztosít. Az Lsv2 sorozatú virtuális gépek úgy vannak optimalizálva, hogy tartós adatlemezek helyett a közvetlenül a virtuális géphez csatlakoztatott csomóponton lévő helyi lemezt használják. 

Ezek a virtuális gépek erős lehetőségek a big data, az adattárház, a jelentéskészítés és az ETL számítási feladatokhoz. A helyi NVMe-tároló nagy átviteli sebességét és IOPS-ét akkor használhatja, ha olyan fájlokat kell feldolgoznia, amelyek az adatbázisba lesznek betöltve, illetve olyan forgatókönyvek esetén, ahol az adatok újra létrehozhatók a forrásrendszerből vagy más adattárakból, például az Azure Blob Storage-ból vagy az Azure Data Lake-ből. [Lsv2 sorozat](../../../virtual-machines/lsv2-series.md) A virtuális gépek lemezteljesítményét akár 30 percig is meglökheti.

Ezek a virtuális gépek 8–80 vCPU méretűek vCPU-nként 8 GiB memóriával, és minden 8 vCPU-hoz 1,92 TB NVMe SSD van. Ez azt jelenti, hogy a sorozat legnagyobb virtuális gépe, a [L80s_v2](../../../virtual-machines/lsv2-series.md)80 vCPU és 640 BIB memória 10x1,92 GB NVMe-tárolóval.  Ezen virtuális gépek memória–virtuális mag aránya konzisztens 8.

Az NVMe-tárolók hamarosan el fognak veszni ezeken a lemezeken, ha felszabadítja a virtuális gépet, vagy ha egy másik gazdagépre helyezték át a szolgáltatás kigyógyítása érdekében.

Az Lsv2 és az Ls sorozat támogatja a [prémium](../../../virtual-machines/premium-storage-performance.md)szintű tárolást, de a prémium szintű tárolók gyorsítótárazása nem. Helyi gyorsítótár létrehozása az I/O-k számának növelése érdekében nem támogatott. 

> [!WARNING]
> Az adatfájlok a virtuális gép felszabadítása esetén adatvesztést eredményezhetnek a élettartamú NVMe-tárolóban. 

## <a name="constrained-vcores"></a>Korlátozott virtuális magok

A nagy teljesítményű SQL Server számítási feladatokhoz gyakran nagyobb mennyiségű memóriára, I/O-re és átviteli sebességre van szükség a virtuális magok magas száma nélkül. 

A legtöbb OLTP számítási feladat olyan alkalmazás-adatbázis, amelyet nagy számú kisebb tranzakció vezérel. OLTP számítási feladatok esetén csak kis mennyiségű adatot olvasnak vagy módosítunk, de a felhasználószámok által vezérelt tranzakciók mennyisége sokkal nagyobb. Fontos, hogy a SQL Server rendelkezésre álló memória gyorsítótárazható legyen, a teljesítmény érdekében a közelmúltban elért adatokat tárolja, és hogy a fizikai olvasások gyorsan beolvashatóak a memóriába. 

Ezeknek az OLTP-környezetnek nagyobb mennyiségű memóriára, gyors tárterületre és az optimális teljesítményhez szükséges I/O-sávszélességre van szüksége. 

Annak érdekében, hogy ez a teljesítmény a magasabb licencelési költségek SQL Server nélkül is fenntartsa, az Azure korlátozott vCPU-számokkal kínál [virtuálisgép-méreteket.](../../../virtual-machines/constrained-vcpu.md) 

Ez segít szabályozni a licencelési költségeket a rendelkezésre álló virtuális magok csökkentésével, miközben a szülő virtuális gép memória-, tárolási és I/O-sávszélességét tartja fenn.

A vCPU-k száma az eredeti virtuálisgép-méret felére vagy negyedére korlátozható. A virtuális gép számára elérhető virtuális magok számának csökkentésével magasabb memória–virtuális mag arány érhető el, de a számítási költségek változatlanok maradnak.

Ezek az új virtuálisgép-méretek utótaggal vannak megszava, amely meghatározza az aktív vCPU-k számát, hogy könnyebb legyen azonosítani őket. 

Az [M64-32ms](../../../virtual-machines/constrained-vcpu.md) például csak 32 SQL Server virtuális mag licencelését igényli, memóriával, I/O-val és az [M64m-ek](/azure/virtual-machines/m-series) és az [M64-16m-ek](../../../virtual-machines/constrained-vcpu.md) átviteli sebességéhez csak 16 virtuális mag licencelése szükséges.  Bár az [M64–16 ezredms](../../../virtual-machines/constrained-vcpu.md) az SQL Server M64ms licencelési költségeinek negyede, a virtuális gép számítási költsége azonos lesz.

> [!NOTE] 
> - A közepes és nagy adattárház [](../../../virtual-machines/constrained-vcpu.md)munkaterhelései továbbra is kihasználják a korlátozott virtuális magokkal rendelkező virtuális gépeket, de az adattárház számítási feladatait általában kevesebb felhasználó és folyamat jellemzi, amelyek nagy mennyiségű adatot foglalkoznak párhuzamosan futó lekérdezéstervekkel. 
> - Az operációs rendszer licencelését tartalmazó számítási költség ugyanaz marad, mint a szülő virtuális gép. 



## <a name="next-steps"></a>Következő lépések

További tudnivalókért tekintse meg a sorozat további cikkeit:
- [Gyors ellenőrzőlista](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Alapkonfiguráció gyűjtése](performance-guidelines-best-practices-collect-baseline.md)

A biztonsággal kapcsolatos ajánlott eljárásokért tekintse meg [az Azure SQL Server biztonsági szempontokat Virtual Machines.](security-considerations-best-practices.md)

Tekintse át a SQL Server virtuális gépekről az [Azure SQL Server áttekintésében Virtual Machines cikkeket.](sql-server-on-azure-vm-iaas-what-is-overview.md) Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md). 
