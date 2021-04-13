---
title: 'Ellenőrzőlista: a teljesítményre vonatkozó ajánlott eljárások & irányelvek'
description: A gyors ellenőrzőlista segítségével áttekintheti az ajánlott eljárásokat és irányelveket az Azure-beli virtuális gépen (VM) SQL Server teljesítményének optimalizálásához.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 84f2f4f679de80cd9b5fc986d40e084bae8a4cad
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313759"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>Ellenőrzőlista: az Azure-beli virtuális gépeken való SQL Server teljesítményének bevált eljárásai
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk egy gyors ellenőrzőlistát tartalmaz az Azure Virtual Machines (VM) SQL Server teljesítményének optimalizálásához ajánlott eljárások és irányelvek sorozatával. 

Az átfogó részleteket a következő cikk további cikkeiben találja: [virtuális gép mérete](performance-guidelines-best-practices-vm-size.md), [tárolás](performance-guidelines-best-practices-storage.md), alapkonfiguráció [gyűjtése](performance-guidelines-best-practices-collect-baseline.md). 


## <a name="overview"></a>Áttekintés

Az Azure Virtual Machines SQL Server futtatásakor továbbra is ugyanazokat az adatbázis-teljesítmény-hangolási beállításokat használja, amelyek a helyszíni kiszolgálói környezetekben SQL Serverra alkalmazhatók. A nyilvános Felhőbeli kapcsolatok adatbázisának teljesítménye azonban számos tényezőtől függ, például a virtuális gép méretétől és az adatlemezek konfigurációjától.

Általában a költségek optimalizálása és a teljesítmény optimalizálása közötti kompromisszumok állnak fenn. A teljesítményre vonatkozó ajánlott eljárások sorozat az Azure Virtual Machines SQL Server *legjobb* teljesítményének beszerzésére koncentrál. Ha a számítási feladat kevésbé igényes, előfordulhat, hogy nem igényel minden javasolt optimalizálást. A javaslatok kiértékelése során vegye figyelembe a teljesítményre vonatkozó igényeket, a költségeket és a számítási feladatok mintáit.

## <a name="vm-size"></a>Virtuális gép mérete

Az alábbiakban egy gyors ellenőrzőlista található a virtuálisgép-méretezéssel kapcsolatos ajánlott eljárások a SQL Server Azure-beli virtuális gépen való futtatásához: 

- Használjon 4 vagy több vCPU rendelkező virtuálisgép-méretet, például a [Standard_M8-4ms](../../../virtual-machines/m-series.md), a [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)vagy a [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) vagy magasabbat. 
- A SQL Server számítási feladatok legjobb teljesítményéhez használja a [memóriára optimalizált](../../../virtual-machines/sizes-memory.md) virtuálisgép-méreteket. 
- A [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), az [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) sorozat, az [M-](../../../virtual-machines/m-series.md)és a [Mv2](../../../virtual-machines/mv2-series.md) sorozat a OLTP számítási feladatokhoz szükséges optimális memória-virtuális mag arányt kínálja. Mindkét M sorozatú virtuális gép a legnagyobb virtuális mag arányt kínálja a kritikus fontosságú számítási feladatokhoz, és az adatraktár-munkaterhelésekhez is ideális. 
- Vegye fontolóra a kritikus és az adatraktár-számítási feladatok nagyobb virtuális mag arányát. 
- Az Azure-beli virtuális gépek Marketplace-lemezképeit a SQL Server beállítások és a tárolási beállítások az optimális SQL Server teljesítmény érdekében konfigurálják. 
- Gyűjtse össze a cél számítási feladatok teljesítményének jellemzőit, és használja őket a vállalat megfelelő virtuálisgép-méretének meghatározásához.

További információ: az átfogó virtuálisgép- [mérettel kapcsolatos ajánlott eljárások](performance-guidelines-best-practices-vm-size.md). 

## <a name="storage"></a>Tárolás

A következő lista gyors ellenőrzőlistát biztosít a SQL Server Azure-beli virtuális gépen való futtatásához szükséges tárolási konfigurációról: 

- A lemez típusának kiválasztása előtt figyelje az alkalmazást, és [határozza meg a tárolási sávszélességet és a késési követelményeket](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) a SQL Server adat-, napló-és tempdb-fájlok esetében. 
- A tárolási teljesítmény optimalizálása érdekében tervezze meg a rendelkezésre álló legmagasabb gyorsítótárban lévő IOPS, és az adatolvasások teljesítmény szolgáltatásként való használatát a [virtuális gép és a lemezek korlátozásával/szabályozásával](../../../virtual-machines/premium-storage-performance.md#throttling)elkerülhető.
- Az adatfájlok, a naplók és a tempdb fájljait külön meghajtókon helyezheti el.
    - Az adatmeghajtó esetében csak a [prémium szintű P30 és a P40 lemezeket](../../../virtual-machines/disks-types.md#premium-ssd) használja a gyorsítótár-támogatás rendelkezésre állásának biztosításához
    - A [P30-P80-lemezek](../../../virtual-machines/disks-types.md#premium-ssd)kiértékelése során a kapacitás és a tesztelési teljesítmény, valamint a díjszabási csomag esetében.
      - Ha az almilliszekundum tárolási késése szükséges, használja az [Azure Ultra Disks](../../../virtual-machines/disks-types.md#ultra-disk) -t a tranzakciónaplóhoz. 
      - Az M sorozatú virtuális gépek üzembe helyezése az Azure Ultra Disks használatával [írásgyorsító](../../../virtual-machines/how-to-enable-write-accelerator.md) .
    - A virtuális gépek optimális méretének kiválasztása után helyezze a [tempdb](/sql/relational-databases/databases/tempdb-database) a helyi ideiglenes SSD- `D:\` meghajtón a legtöbb SQL Server számítási feladathoz. 
      - Ha a helyi meghajtó kapacitása nem elegendő a tempdb, vegye fontolóra a virtuális gép méretezését. További információért lásd: [adatfájl-gyorsítótárazási szabályzatok](performance-guidelines-best-practices-storage.md#data-file-caching-policies) .
- Több Azure-adatlemezt is megtalálhat a [tárolóhelyek](/windows-server/storage/storage-spaces/overview) szolgáltatással, hogy növelje az I/O-sávszélességet a célként megadott virtuális gép IOPS és átviteli korlátaival.
- A [gazdagép-gyorsítótárazás](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) beállítása az adatfájlok lemezei csak olvashatók.
- Állítsa be a [gazdagép-gyorsítótárazást](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) a Nincs értékre a naplófájlok lemezein.
    - Ne engedélyezze az olvasási/írási gyorsítótárazást SQL Server fájlokat tartalmazó lemezeken. 
    - A lemez gyorsítótár-beállításainak módosítása előtt mindig állítsa le a SQL Server szolgáltatást.
- A fejlesztési és tesztelési feladatok esetében érdemes a standard szintű tárolókat használni. Az éles számítási feladatokhoz nem ajánlott standard HDD/SDD használata.
- A [kredit-alapú lemez kitörése](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) csak kisebb fejlesztési/tesztelési feladatokhoz és részlegi rendszerekhez vehető figyelembe.
- A Storage-fiók kiépítése ugyanabban a régióban, mint a SQL Server VM. 
- Tiltsa le az Azure geo-redundáns tárterületet (Geo-Replication), és használja a LRS (helyi redundáns tárolás) a Storage-fiókon.
- Formázza az adatlemezt úgy, hogy az a 64 KB-os foglalási egység méretét használja az ideiglenes meghajtótól eltérő meghajtón elhelyezett összes adatfájlhoz `D:\` (amely alapértelmezés szerint 4 KB). SQL Server az Azure Marketplace-en üzembe helyezett virtuális gépekhez a kiosztási egység méretével formázott adatlemezek tartoznak, és a Storage-készlet 64 KB-ra van állítva. 

További információ: átfogó [tárterület – ajánlott eljárások](performance-guidelines-best-practices-storage.md). 


## <a name="azure--sql-feature-specific"></a>Az Azure & SQL-szolgáltatás specifikus

A következőkben az Azure-beli virtuális gépen futtatott SQL Server SQL Server és az Azure-specifikus konfigurációkra vonatkozó ajánlott eljárások gyors listáját tekintheti át: 

- Regisztrálja az [SQL IaaS-ügynök bővítményét](sql-agent-extension-manually-register-single-vm.md) , hogy feloldja a [funkciók számos előnyét](sql-server-iaas-agent-extension-automate-management.md#feature-benefits). 
- Az adatbázis-oldal tömörítésének engedélyezése.
- Az adatfájlok azonnali inicializálásának engedélyezése.
- Az adatbázis újranövekedésének korlátozása.
- Az adatbázis újrazsugorodásának letiltása.
- Az összes adatbázis áthelyezése adatlemezekre, beleértve a rendszeradatbázisokat is.
- Helyezze át SQL Server a hibanapló és a nyomkövetési fájl könyvtárait az adatlemezekre.
- Konfigurálja az alapértelmezett biztonsági mentési és adatbázisfájlok helyét.
- [Zárolt lapok engedélyezése a memóriában](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Értékelje ki és alkalmazza a SQL Server telepített verziójának [legújabb összesített frissítéseit](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) .
- Biztonsági mentés közvetlenül az Azure Blob Storage-ba.
- Több [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) -fájl, 1 fájl/mag, legfeljebb 8 fájl használható.



## <a name="next-steps"></a>Következő lépések

További információért lásd a sorozat további cikkeit:
- [Virtuális gép mérete](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Alapterv összegyűjtése](performance-guidelines-best-practices-collect-baseline.md)

Az ajánlott biztonsági eljárásokért tekintse [meg az Azure Virtual Machines SQL Server biztonsági szempontjait](security-considerations-best-practices.md).

Tekintse át az Azure-beli SQL Server SQL Server virtuális gépekkel kapcsolatos további cikkeket [Virtual Machines áttekintését](sql-server-on-azure-vm-iaas-what-is-overview.md). Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).
