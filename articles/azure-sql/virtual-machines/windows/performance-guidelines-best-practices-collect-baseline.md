---
title: 'Az alapkonfiguráció összegyűjtése: a teljesítményre vonatkozó ajánlott eljárások & irányelvek'
description: A teljesítmény-alapkonfiguráció begyűjtésének lépései a SQL Server Azure-beli virtuális gépen (VM) való teljesítményének optimalizálása érdekében.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572380"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Alapkonfiguráció gyűjtése: az Azure-beli virtuális gépen SQL Server teljesítményének bevált eljárásai
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ebből a cikkből megtudhatja, hogyan gyűjthet teljesítmény-alapkonfigurációt az Azure Virtual Machines (VM) SQL Server teljesítményének optimalizálása érdekében ajánlott eljárások és irányelvek sorozatával.

Általában a költségek optimalizálása és a teljesítmény optimalizálása közötti kompromisszumok állnak fenn. A teljesítményre vonatkozó ajánlott eljárások sorozat az Azure Virtual Machines SQL Server *legjobb* teljesítményének beszerzésére koncentrál. Ha a számítási feladat kevésbé igényes, előfordulhat, hogy nem igényel minden javasolt optimalizálást. A javaslatok kiértékelése során vegye figyelembe a teljesítményre vonatkozó igényeket, a költségeket és a számítási feladatok mintáit.

## <a name="overview"></a>Áttekintés

Az előírásos megközelítéshez a teljesítményszámlálók a PerfMon/LogMan és a capture SQL Server WAIT statisztikát használják a forrás-környezet általános terhelésének és potenciális szűk keresztmetszetének jobb megismeréséhez. 

Először gyűjtsön össze a CPU, a memória, a [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), az [átviteli sebesség](../../../virtual-machines/premium-storage-performance.md#throughput)és a [késés](../../../virtual-machines/premium-storage-performance.md#latency) a forrás számítási [feladatra vonatkozóan az alkalmazás teljesítményének ellenőrzőlista](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)utáni csúcsidőben. 

Az adatok gyűjtése csúcsidőben, például munkaterhelések alatt, a szokásos munkanap során, de más nagy terhelésű folyamatok, például a napi feldolgozás és a hétvégi ETL-munkaterhelések esetében is. Érdemes lehet erőforrásokat felméretezni a szokatlanul nagy számítási feladatokhoz, például a negyedév végén történő feldolgozáshoz, majd a munkaterhelés befejezése után a méretezést. 

A teljesítmény elemzése lehetőséggel válassza ki a [virtuális gép méretét](../../../virtual-machines/sizes-memory.md) , amely méretezhető a számítási feladatok teljesítményére vonatkozó követelmények alapján.


## <a name="storage"></a>Tárolás

A SQL Server teljesítmény nagy mértékben függ az I/O-alrendszertől és a tárolási teljesítménytől a IOPS és az átviteli sebesség méri. Ha az adatbázisa nem fér bele a fizikai memóriába, SQL Server folyamatosan a pufferben lévő és kívüli adatbázis-lapokat hozza ki. A SQL Server adatfájljait eltérően kell kezelni. A naplófájlokhoz való hozzáférés szekvenciális, kivéve, ha egy tranzakciót vissza kell állítani, ahol az adatfájlok, beleértve a tempdb is, véletlenszerűen érhetők el. Ha lassú I/O-alrendszert használ, a felhasználók olyan teljesítménnyel kapcsolatos problémákat tapasztalhatnak, mint például a lassú válaszidő és az időtúllépés miatt nem teljesített feladatok. 

Az Azure Marketplace-beli virtuális gépek olyan fizikai lemezeken található naplófájlok, amelyek alapértelmezés szerint különállóak az adatfájlokból. A tempdb-adatfájlok száma és mérete megfelel az ajánlott eljárásoknak, és az ideiglenes meghajtót célozza meg `D:\` . 

A következő PerfMon-számlálók segítségével ellenőrizheti a SQL Server által igényelt IO-átviteli sebességet: 
* **\LogicalDisk\Disk Olvasás gyakorisága** (olvasási sebesség/mp) (IOPS olvasása)
* **\LogicalDisk\Disk írás gyakorisága (művelet/mp** ) (írás IOPS) 
* **\LogicalDisk\Disk olvasási sebesség (bájt/mp** ) (olvasási átviteli követelmények az adatokhoz, a naplóhoz és a tempdb fájlokhoz)
* **\LogicalDisk\Disk írási sebesség (bájt/s** ) (írási teljesítményre vonatkozó követelmények az adatokhoz, naplóhoz és tempdb fájlokhoz)

A IOPS és az átviteli sebességre vonatkozó követelmények csúcs szinten való használata esetén a mérések kapacitásának megfelelő virtuálisgép-méreteket kell kiértékelni. 

Ha a számítási feladathoz 20000 olvasási IOPS és 10K írási IOPS van szüksége, választhat E16s_v3 (legfeljebb 32K gyorsítótárazott és 25600 gyorsítótárral ellátott IOPS) vagy M16_s (legfeljebb 20000 gyorsítótárazott és 10K gyorsítótáras IOPS), 2 P30 lemezzel a tárolóhelyek használatával. 

Ügyeljen arra, hogy a számítási feladatok átviteli és IOPS követelményeit a virtuális gépek különböző méretezési korlátokkal rendelkezzenek a IOPS és az átviteli sebesség tekintetében.

## <a name="memory"></a>Memória

Nyomon követheti az operációs rendszer által használt külső memóriát, valamint SQL Server által belsőleg használt memóriát is. Bármelyik összetevő esetében a terhelés azonosítása a virtuális gépek méretét és a hangolási lehetőségek azonosítását segíti. 

A következő PerfMon-számlálók segítségével ellenőrizheti SQL Server virtuális gép memóriájának állapotát: 
* [\Memory\Available MB-ban](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: memória-Manager\Target kiszolgáló memóriája (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: memória-Manager\Total kiszolgáló memóriája (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: puffer Manager\Lazy írás gyakorisága (művelet/mp)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: a puffer Manager\page Reads várható élettartama](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Compute

Az Azure-beli számításokat a helyszínen eltérően kezelik. A helyszíni kiszolgálók az elmúlt néhány évben a felügyeleti terhelés és az új hardver beszerzésének költségei miatt nem frissültek. A virtualizálás elhárítja ezeket a problémákat, de az alkalmazások a mögöttes hardver legnagyobb előnyeit optimalizálják, ami azt jelenti, hogy az erőforrás-felhasználás jelentős változása szükségessé teszi a teljes fizikai környezet újrakiegyensúlyozását. 

Ez nem olyan kihívás az Azure-ban, ahol egy új virtuális gép egy másik sorozatban, vagy akár egy másik régióban is könnyen elérhető. 

Az Azure-ban a lehető legtöbb előnyt kívánja kihasználni a virtuális gépek erőforrásainak, ezért az Azure-beli virtuális gépeket úgy kell konfigurálni, hogy az átlagos CPU-t a lehető legmagasabbra állítsa, a munkaterhelés befolyásolása nélkül. 

A következő PerfMon-számlálók segíthetnek ellenőrizni egy SQL Server virtuális gép számítási állapotát:
* **\Processor információ (_Total) \% processzoridő**
* **\Process (sqlservr) \% processzoridő**

> [!NOTE] 
> Ideális esetben próbálja meg a számítási kapacitás 80%-át használni, a 90% feletti csúcsok mellett, de nem éri el a 100%-ot bármilyen tartós időszakra. Alapvetően csak szeretné kiépíteni az alkalmazás által igényelt számítási feltételt, majd az üzleti igényeknek megfelelően meg kell terveznie a méretezést. 


## <a name="next-steps"></a>Következő lépések

További információért lásd a sorozat további cikkeit:
- [Gyors ellenőrzőlista](performance-guidelines-best-practices-checklist.md)
- [Virtuális gép mérete](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)


Az ajánlott biztonsági eljárásokért tekintse [meg az Azure Virtual Machines SQL Server biztonsági szempontjait](security-considerations-best-practices.md).

Tekintse át az Azure-beli SQL Server SQL Server virtuális gépekkel kapcsolatos további cikkeket [Virtual Machines áttekintését](sql-server-on-azure-vm-iaas-what-is-overview.md). Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).
