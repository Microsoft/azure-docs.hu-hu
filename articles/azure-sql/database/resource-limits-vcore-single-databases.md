---
title: Önálló adatbázis – Virtuális magokra vonatkozó erőforráskorlátok
description: Ez az oldal a Azure SQL Database egyetlen adatbázisának néhány gyakori virtuális mag-erőforrás-korlátját ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/09/2021
ms.openlocfilehash: 7f96e6638bd021777d2f4bb888ef70c49c42c716
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313793"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Önálló adatbázisok erőforráskorlátai a virtuálismag-alapú vásárlási modell használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk a Azure SQL Database önálló adatbázisainak részletes erőforrás-korlátozásait tartalmazza a virtuális mag beszerzési modell használatával.

* A kiszolgálón található önálló adatbázisokra vonatkozó DTU megvásárlásával kapcsolatban lásd: a [kiszolgálók erőforrás-korlátainak áttekintése](resource-limits-logical-server.md).
* Az Azure SQL Database DTU beszerzési modellre vonatkozó korlátozásait lásd: a DTU erőforrás-korlátozásai az [önálló adatbázisok](resource-limits-dtu-single-databases.md) és a [DTU-erőforrások korlátai rugalmas készletek](resource-limits-dtu-elastic-pools.md).
* A virtuális mag erőforrás-korlátaival kapcsolatban lásd: virtuális mag-erőforrás korlátai [– Azure SQL Database](resource-limits-vcore-single-databases.md) és [virtuális mag erőforrás-korlátok – rugalmas készletek](resource-limits-vcore-elastic-pools.md).
* A különböző vásárlási modellekkel kapcsolatos további információkért lásd: [modellek és szolgáltatási szintek beszerzése](purchasing-models.md).

Minden írásvédett replika saját erőforrásokkal rendelkezik, mint például a virtuális mag, a memória, az adatIOPS, a TempDB, a feldolgozók és a munkamenetek. Az egyes írásvédett replikák a cikk későbbi részében részletesen ismertetett erőforrás-korlátokra vonatkoznak.

Megadhatja a szolgáltatási szintet, a számítási méretet (a szolgáltatás célját) és az egyes adatbázisok tárolási mennyiségét a következő használatával:

* [Transact-SQL](single-database-manage.md#transact-sql-t-sql) az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database) használatával
* [Azure Portal](single-database-manage.md#the-azure-portal)
* [PowerShell](single-database-manage.md#powershell)
* [Azure CLI](single-database-manage.md#the-azure-cli)
* [REST API](single-database-manage.md#rest-api)

> [!IMPORTANT]
> Az útmutatás és a megfontolások méretezésével kapcsolatban lásd: [önálló adatbázis](single-database-scale.md)skálázása.

## <a name="general-purpose---serverless-compute---gen5"></a>Általános célú kiszolgáló nélküli számítás – Gen5

A [kiszolgáló nélküli számítási rétegek](serverless-tier-overview.md) jelenleg csak Gen5 hardveren érhetők el.

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generációja (1. rész)

|Számítási méret (szolgáltatási cél)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|
|Minimális – maximális virtuális mag|0.5-1|0.5-2|0,5 – 4|0,75 – 6|1.0-8|
|Minimális memória maximális mérete (GB)|2.02 – 3|2.05 – 6|2.10-12|2,25 – 18|3,00 – 24|
|Minimum – maximális automatikus szüneteltetési késleltetés (perc)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Oszlopcentrikus-támogatás|Igen*|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|512|1024|1024|1024|1536|
|Napló maximális mérete (GB)|154|307|307|307|461|
|TempDB maximális adatméret (GB)|32|64|128|192|256|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS \*\*|320|640|1280|1920|2560|
|Maximális naplózási arány (MBps)|4,5|9|18|27|36|
|Egyidejű feldolgozók maximális száma (kérelem)|75|150|300|450|600|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|1|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* A kisebb maximális virtuális mag-konfigurációval rendelkező szolgáltatási célkitűzések nem rendelkeznek elegendő memóriával az oszlopos tároló-indexek létrehozásához és használatához.  Ha teljesítménnyel kapcsolatos problémákat tapasztal az oszlopos tárolóban, növelje a maximális virtuális mag-konfigurációt, hogy növelje a rendelkezésre álló maximális memóriát.  
\*\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Számítási méret (szolgáltatási cél)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|
|Minimális – maximális virtuális mag|1,25 – 10|1,50 – 12|1,75 – 14|2,00 – 16|
|Minimális memória maximális mérete (GB)|3,75 – 30|4.50 – 36|5.25 – 42|6,00 – 48|
|Minimum – maximális automatikus szüneteltetési késleltetés (perc)|60-10080|60-10080|60-10080|60-10080|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|1536|3072|3072|3072|
|Napló maximális mérete (GB)|461|461|461|922|
|TempDB maximális adatméret (GB)|320|384|448|512|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|3200|3840|4480|5120|
|Maximális naplózási arány (MBps)|45|50|50|50|
|Egyidejű feldolgozók maximális száma (kérelem)|750|900|1050|1200|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|
|Replikák száma|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-3"></a>Gen5 számítási generációja (3. rész)

|Számítási méret (szolgáltatási cél)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|
|Minimális – maximális virtuális mag|2,25 – 18|2,5 – 20|3–24|4-32|5-40|
|Minimális memória maximális mérete (GB)|6.75 – 54|7,5 – 60|9-72|12-96|15-120|
|Minimum – maximális automatikus szüneteltetési késleltetés (perc)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|3072|3072|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|1024|1024|1024|
|TempDB maximális adatméret (GB)|576|640|768|1024|1280|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|5760|6400|7680|10240|12800|
|Maximális naplózási arány (MBps)|50|50|50|50|50|
|Egyidejű feldolgozók maximális száma (kérelem)|1350|1500|1800|2400|3000|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|1|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).


## <a name="hyperscale---provisioned-compute---gen4"></a>Nagy kapacitású – kiépített számítás – Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generációja (1. rész)

|Számítási méret (szolgáltatási cél)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100|
|Napló maximális mérete (TB)|Korlátlan |Korlátlan |Korlátlan |Korlátlan |Korlátlan |Korlátlan |
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|Tárolási típus| [1. Megjegyzés](#notes) |[1. Megjegyzés](#notes)|[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |
|Maximális helyi SSD-IOPS *|4000 |8000 |12000 |16000 |20000 |24000 |
|Maximális naplózási arány (MBps)|100 |100 |100 |100 |100 |100 |
|IO-késés (hozzávetőleges)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Yes|
|Biztonsági mentési tár megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

\* A helyi SSD IO mellett a számítási feladatok a távoli [Page Server](service-tier-hyperscale.md#page-server) IO-t használják. A hatékony IOPS a munkaterheléstől függ. Részletekért lásd: [ADATio-irányítás](resource-limits-logical-server.md#resource-governance)és [adatio-adatok az erőforrás-kihasználtsági statisztikában](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generációja (2. rész)

|Számítási méret (szolgáltatási cél)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|[RBPEX](service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |
|Napló maximális mérete (TB)|Korlátlan |Korlátlan |Korlátlan |Korlátlan |Korlátlan |Korlátlan |
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|Tárolási típus| [1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |
|Maximális helyi SSD-IOPS *|28000 |32000 |36000 |40000 |64000 |76800 |
|Maximális naplózási arány (MBps)|100 |100 |100 |100 |100 |100 |
|IO-késés (hozzávetőleges)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|
|Egyidejű feldolgozók maximális száma (kérelem)|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Yes|
|Biztonsági mentési tár megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

\* A helyi SSD IO mellett a számítási feladatok a távoli [Page Server](service-tier-hyperscale.md#page-server) IO-t használják. A hatékony IOPS a munkaterheléstől függ. Részletekért lásd: [ADATio-irányítás](resource-limits-logical-server.md#resource-governance)és [adatio-adatok az erőforrás-kihasználtsági statisztikában](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

## <a name="hyperscale---provisioned-compute---gen5"></a>Nagy kapacitású – kiépített számítás – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generációja (1. rész)

|Számítási méret (szolgáltatási cél)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |100|
|Napló maximális mérete (TB)|Korlátlan |Korlátlan |Korlátlan |Korlátlan |Korlátlan |Korlátlan |Korlátlan |
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|448|
|Tárolási típus| [1. Megjegyzés](#notes) |[1. Megjegyzés](#notes)|[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |
|Maximális helyi SSD-IOPS *|8000 |16000 |24000 |32000 |40000 |48000 |56000 |
|Maximális naplózási arány (MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO-késés (hozzávetőleges)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Biztonsági mentési tár megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

\* A helyi SSD IO mellett a számítási feladatok a távoli [Page Server](service-tier-hyperscale.md#page-server) IO-t használják. A hatékony IOPS a munkaterheléstől függ. Részletekért lásd: [ADATio-irányítás](resource-limits-logical-server.md#resource-governance)és [adatio-adatok az erőforrás-kihasználtsági statisztikában](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Számítási méret (szolgáltatási cél)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |100 |
|Napló maximális mérete (TB)|Korlátlan |Korlátlan |Korlátlan |Korlátlan |Korlátlan |Korlátlan |Korlátlan |
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus| [1. Megjegyzés](#notes) |[1. Megjegyzés](#notes)|[1. Megjegyzés](#notes)|[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |
|Maximális helyi SSD-IOPS *|64000 |72000 |80000 |96000 |128000 |160000 |204800 |
|Maximális naplózási arány (MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO-késés (hozzávetőleges)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|
|Egyidejű feldolgozók maximális száma (kérelem)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Biztonsági mentési tár megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

\* A helyi SSD IO mellett a számítási feladatok a távoli [Page Server](service-tier-hyperscale.md#page-server) IO-t használják. A hatékony IOPS a munkaterheléstől függ. Részletekért lásd: [ADATio-irányítás](resource-limits-logical-server.md#resource-governance)és [adatio-adatok az erőforrás-kihasználtsági statisztikában](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

#### <a name="notes"></a>Jegyzetek

**1. Megjegyzés**: a nagy kapacitású egy többrétegű architektúra, külön számítási és tárolási összetevőkkel: a [nagy kapacitású szolgáltatási réteg architektúrája](service-tier-hyperscale.md#distributed-functions-architecture)

**2. Megjegyzés**: a késés 1-2 MS a helyi számítási replika SSD-re vonatkozó adatok esetében, amely a leggyakrabban használt adatlapokat gyorsítótárazza. A lapozófájlokból beolvasott adatok nagyobb késése.

## <a name="hyperscale---provisioned-compute---dc-series"></a>Nagy kapacitású – kiépített számítás – DC sorozat

|Számítási méret (szolgáltatási cél)|HS_DC_2|HS_DC_4|HS_DC_6|HS_DC_8|
|:--- | --: |--: |--: |--: |---: | 
|Számítási generáció|DC sorozat|DC sorozat|DC sorozat|DC sorozat|
|Virtuális mag|2|4|6|8|
|Memória (GB)|9|18|27|36|
|[RBPEX](service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (TB)|100 |100 |100 |100 |
|Napló maximális mérete (TB)|Korlátlan |Korlátlan |Korlátlan |Korlátlan |
|TempDB maximális adatméret (GB)|64|128|192|256|
|Tárolási típus| [1. Megjegyzés](#notes) |[1. Megjegyzés](#notes)|[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |
|Maximális helyi SSD-IOPS *|14000|28000|42000|44800|
|Maximális naplózási arány (MBps)|100 |100 |100 |100 |
|IO-késés (hozzávetőleges)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|
|Egyidejű feldolgozók maximális száma (kérelem)|160|320|480|640|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|Igen|Igen|Igen|Yes|
|Biztonsági mentési tár megőrzése|7 nap|7 nap|7 nap|7 nap|
|||

### <a name="notes"></a>Jegyzetek

**1. Megjegyzés**: a nagy kapacitású egy többrétegű architektúra, külön számítási és tárolási összetevőkkel: a [nagy kapacitású szolgáltatási réteg architektúrája](service-tier-hyperscale.md#distributed-functions-architecture)

**2. Megjegyzés**: a késés 1-2 MS a helyi számítási replika SSD-re vonatkozó adatok esetében, amely a leggyakrabban használt adatlapokat gyorsítótárazza. A lapozófájlokból beolvasott adatok nagyobb késése.

## <a name="general-purpose---provisioned-compute---gen4"></a>Általános célú kiépített számítás – Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generációja (1. rész)

|Számítási méret (szolgáltatási cél)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|
|Napló maximális mérete (GB)|307|307|461|461|461|922|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|320|640|960|1280|1600|1920|
|Maximális naplózási arány (MBps)|4,5|9|13,5|18|22,5|27|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generációja (2. rész)

|Számítási méret (szolgáltatási cél)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|3072|3072|3072|3072|4096|4096|
|Napló maximális mérete (GB)|922|922|922|922|1229|1229|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)
|Maximális adatmennyiség IOPS *|2240|2560|2880|3200|5120|7680|
|Maximális naplózási arány (MBps)|31,5|36|40,5|45|50|50|
|Egyidejű feldolgozók maximális száma (kérelem)|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Általános célú kiépített számítás – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generációja (1. rész)

|Számítási méret (szolgáltatási cél)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|461|461|461|922|922|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|384|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|640|1280|1920|2560|3200|3840|4480|
|Maximális naplózási arány (MBps)|9|18|27|36|45|50|50|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|1|1|1|1|1|1|1|
|Több – AZ|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Számítási méret (szolgáltatási cél)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1024|1024|1024|1024|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|5120|5760|6400|7680|10240|12800|12800|
|Maximális naplózási arány (MBps)|50|50|50|50|50|50|50|
|Egyidejű feldolgozók maximális száma (kérelem)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|1|1|1|1|1|1|1|
|Több – AZ|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Általános célú kiépített számítás – Fsv2 sorozat

### <a name="fsv2-series-compute-generation-part-1"></a>Fsv2 sorozatú számítási generáció (1. rész)

|Számítási méret (szolgáltatási cél)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Számítási generáció|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|
|Virtuális mag|8|10|12|14|16|
|Memória (GB)|15,1|18,9|22,7|26,5|30,2|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|1024|1024|1024|1024|1536|
|Napló maximális mérete (GB)|336|336|336|336|512|
|TempDB maximális adatméret (GB)|37|46|56|65|74|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|2560|3200|3840|4480|5120|
|Maximális naplózási arány (MBps)|36|45|50|50|50|
|Egyidejű feldolgozók maximális száma (kérelem)|400|500|600|700|800|
|Egyidejű bejelentkezések maximális száma|800|1000|1200|1400|1600|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|1|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

### <a name="fsv2-series-compute-generation-part-2"></a>Fsv2 sorozatú számítási generáció (2. rész)

|Számítási méret (szolgáltatási cél)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Számítási generáció|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|
|Virtuális mag|18|20|24|32|36|72|
|Memória (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|1536|1536|1536|3072|3072|4096|
|Napló maximális mérete (GB)|512|512|512|1024|1024|1024|
|TempDB maximális adatméret (GB)|83|93|111|148|167|333|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|5760|6400|7680|10240|11520|12800|
|Maximális naplózási arány (MBps)|50|50|50|50|50|50|
|Egyidejű feldolgozók maximális száma (kérelem)|900|1000|1200|1600|1800|3600|
|Egyidejű bejelentkezések maximális száma|1800|2000|2400|3200|3600|7200|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---dc-series"></a>Általános célú kiépített számítás – DC sorozat

|Számítási méret (szolgáltatási cél)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8| 
|:---| ---:|---:|---:|---:|
|Számítási generáció|DC sorozat|DC sorozat|DC sorozat|DC sorozat|
|Virtuális mag|2|4|6|8|
|Memória (GB)|9|18|27|36|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|1024|1536|3072|3072|
|Napló maximális mérete (GB)|307|461|922|922|
|TempDB maximális adatméret (GB)|64|128|192|256|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS *|640|1280|1920|2560|
|Maximális naplózási arány (MBps)|9|18|27|36|
|Egyidejű feldolgozók maximális száma (kérelem)|160|320|480|640|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|
|Replikák száma|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|


\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Üzleti szempontból kritikus – kiépített számítás – Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generációja (1. rész)

|Számítási méret (szolgáltatási cél)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|1|2|3|4|5|6|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|307|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS *|4,000|8,000|12 000|16000|20 000|24 000|
|Maximális naplózási arány (MBps)|8|16|24|32|40|48|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Yes|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generációja (2. rész)

|Számítási méret (szolgáltatási cél)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|7|8|9,5|11|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|307|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS |28 000|32 000|36 000|40,000|64 000|76 800|
|Maximális naplózási arány (MBps)|56|64|64|64|64|64|
|Egyidejű feldolgozók maximális száma (kérelem)|1400|1600|1800|2000|3200|4800|
|Egyidejű bejelentkezések maximális száma (kérelmek)|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Yes|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Üzleti szempontból kritikus – kiépített számítás – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generációja (1. rész)

|Számítási méret (szolgáltatási cél)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|1,57|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|461|461|461|922|922|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|448|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS *|8000|16000|24 000|32 000|40,000|48 000|56 000|
|Maximális naplózási arány (MBps)|24|48|72|96|96|96|96|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|1400|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|4|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Számítási méret (szolgáltatási cél)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|15.77|18,14|20,51|25,25|37,94|52,23|131,64|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1024|1024|1024|1024|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS *|64 000|72 000|80,000|96 000|128 000|160 000|204 800|
|Maximális naplózási arány (MBps)|96|96|96|96|96|96|96|
|Egyidejű feldolgozók maximális száma (kérelem)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű bejelentkezések maximális száma|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Replikák száma|4|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Üzleti szempontból kritikus – kiépített számítás – M sorozat

### <a name="m-series-compute-generation-part-1"></a>M sorozatú számítási generáció (1. rész)

|Számítási méret (szolgáltatási cél)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Számítási generáció|M sorozat|M sorozat|M sorozat|M sorozat|M sorozat|M sorozat|
|Virtuális mag|8|10|12|14|16|18|
|Memória (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|64|80|96|112|128|150|
|Maximális adatméret (GB)|512|640|768|896|1024|1152|
|Napló maximális mérete (GB)|171|213|256|299|341|384|
|TempDB maximális adatméret (GB)|256|320|384|448|512|576|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS *|12 499|15 624|18 748|21 873|24 998|28 123|
|Maximális naplózási arány (MBps)|48|60|72|84|96|108|
|Egyidejű feldolgozók maximális száma (kérelem)|800|1,000|1200|1400|1600|1800|
|Egyidejű bejelentkezések maximális száma|800|1,000|1200|1400|1600|1800|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Nem|Nem|Nem|Nem|Nem|Nem|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

### <a name="m-series-compute-generation-part-2"></a>M sorozatú számítási generáció (2. rész)

|Számítási méret (szolgáltatási cél)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Számítási generáció|M sorozat|M sorozat|M sorozat|M sorozat|M sorozat|
|Virtuális mag|20|24|32|64|128|
|Memória (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|172|216|304|704|1768|
|Maximális adatméret (GB)|1280|1536|2048|4096|4096|
|Napló maximális mérete (GB)|427|512|683|1024|1024|
|TempDB maximális adatméret (GB)|4096|2048|1024|768|640|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS *|31 248|37 497|49 996|99 993|160 000|
|Maximális naplózási arány (MBps)|120|144|192|264|264|
|Egyidejű feldolgozók maximális száma (kérelem)|2000|2 400|3 200|6 400|12 800|
|Egyidejű bejelentkezések maximális száma|2000|2 400|3 200|6 400|12 800|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|
|Több – AZ|Nem|Nem|Nem|Nem|Nem|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

## <a name="business-critical---provisioned-compute---dc-series"></a>Üzleti szempontból kritikus – kiépített számítás – DC sorozat

|Számítási méret (szolgáltatási cél)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|Számítási generáció|DC sorozat|DC sorozat|DC sorozat|DC sorozat|
|Virtuális mag|2|4|6|8|
|Memória (GB)|9|18|27|36|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|1.7|3.7|5.9|8.2|
|Maximális adatméret (GB)|768|768|768|768|
|Napló maximális mérete (GB)|230|230|230|230|
|TempDB maximális adatméret (GB)|64|128|192|256|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS *|14000|28000|42000|44800|
|Maximális naplózási arány (MBps)|24|48|72|96|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|
|Replikák száma|4|4|4|4|
|Több – AZ|Nem|Nem|Nem|Nem|
|Olvasási felskálázás|Nem|Nem|Nem|Nem|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\* Az i/o-méretek maximális értéke 8 KB és 64 KB között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).


## <a name="next-steps"></a>Következő lépések

- Egyetlen adatbázis DTU erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a DTU beszerzési modell használatával](resource-limits-dtu-single-databases.md)
- A rugalmas készletek virtuális mag erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a virtuális mag beszerzési modell használatával](resource-limits-vcore-elastic-pools.md)
- A rugalmas készletek DTU erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a DTU beszerzési modell használatával](resource-limits-dtu-elastic-pools.md)
- Az SQL felügyelt példány erőforrás-korlátaival kapcsolatban lásd: [SQL felügyelt példányok erőforrás-korlátai](../managed-instance/resource-limits.md).
- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- További információ a kiszolgálók erőforrás-korlátairól: a kiszolgálók [erőforrás-korlátainak áttekintése](resource-limits-logical-server.md) a kiszolgáló és az előfizetés szintjein lévő korlátozásokkal kapcsolatban.
