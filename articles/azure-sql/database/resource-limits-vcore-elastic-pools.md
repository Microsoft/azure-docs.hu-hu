---
title: Rugalmas készlet – Virtuális magokra vonatkozó erőforráskorlátok
description: Ez az oldal néhány gyakori virtuális mag-erőforrás-korlátot ismertet a rugalmas készletek Azure SQL Databaseban.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 01/22/2021
ms.openlocfilehash: 619f4deee81c97f2d7a0b4359e2b999c476b1ae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737557"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Rugalmas készletek erőforrás-korlátai a virtuális mag beszerzési modell használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk részletes erőforrás-korlátokat biztosít a rugalmas készletek és a készletezett adatbázisok Azure SQL Database a virtuális mag beszerzési modell használatával.

A DTU megvásárlására vonatkozó korlátokat lásd: [SQL Database DTU erőforrás-korlátok – rugalmas készletek](resource-limits-dtu-elastic-pools.md).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

A szolgáltatási szintet, a számítási méretet (a szolgáltatás célját) és a tárterületet a [Azure Portal](elastic-pool-manage.md#azure-portal), a [PowerShell](elastic-pool-manage.md#powershell), az [Azure CLI](elastic-pool-manage.md#azure-cli)vagy a [REST API](elastic-pool-manage.md#rest-api)használatával állíthatja be.

> [!IMPORTANT]
> Az útmutatás és a megfontolások méretezésével kapcsolatban lásd: [rugalmas készlet](elastic-pool-scale.md)skálázása.

## <a name="general-purpose---provisioned-compute---gen4"></a>Általános célú kiépített számítás – Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Általános célú szolgáltatási szintek: 4. generációs számítási platform (1. rész)

|Számítási méret (szolgáltatási cél)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Adatbázisok maximális száma <sup>1</sup> . készletben|100|200|500|500|500|500|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|512|756|1536|1536|1536|2048|
|Napló maximális mérete|154|227|461|461|461|614|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|Tárolási típus|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup> |400|800|1200|1600|2000|2400|
|Maximális naplózási arány (MB/s)|6|12|18|24|30|36|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup> |210|420|630|840|1050|1260|
|Egyidejű bejelentkezések maximális száma/készlet <sup>3</sup> |210|420|630|840|1050|1260|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Általános célú szolgáltatási szintek: 4. generációs számítási platform (2. rész)

|Számítási méret (szolgáltatási cél)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Adatbázisok maximális száma <sup>1</sup> . készletben|500|500|500|500|500|500|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|2048|2048|2048|2048|3584|4096|
|Napló maximális mérete (GB)|614|614|614|614|1075|1229|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|Tárolási típus|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Maximális naplózási arány (MB/s)|42|48|48|48|48|48|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Egyidejű bejelentkezések maximális száma (kérelmek) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).    

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="general-purpose---provisioned-compute---gen5"></a>Általános célú kiépített számítás – Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Általános célú szolgáltatási szintek: 5. generációs számítási platform (1. rész)

|Számítási méret (szolgáltatási cél)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Adatbázisok maximális száma <sup>1</sup> . készletben|100|200|500|500|500|500|500|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|512|756|1536|1536|1536|2048|2048|
|Napló maximális mérete (GB)|154|227|461|461|461|614|614|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|448|
|Tárolási típus|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Maximális naplózási arány (MB/s)|12|24|36|48|48|48|48|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Replikák száma|1|1|1|1|1|1|1|
|Több – AZ|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Általános célú szolgáltatási szintek: 5. generációs számítási platform (2. rész)

|Számítási méret (szolgáltatási cél)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Adatbázisok maximális száma <sup>1</sup> . készletben|500|500|500|500|500|500|500|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|2048|3072|3072|3072|4096|4096|4096|
|Napló maximális mérete (GB)|614|922|922|922|1229|1229|1229|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup> |6 400|7 200|8,000|9600|12 800|16000|16000|
|Maximális naplózási arány (MB/s)|48|48|48|48|48|48|48|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Replikák száma|1|1|1|1|1|1|1|
|Több – AZ|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Elérhető előzetes verzióban](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Általános célú kiépített számítás – Fsv2 sorozat

### <a name="fsv2-series-compute-generation-part-1"></a>Fsv2 sorozatú számítási generáció (1. rész)

|Számítási méret (szolgáltatási cél)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Számítási generáció|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|
|Virtuális mag|8|10|12|14|16|
|Memória (GB)|15,1|18,9|22,7|26,5|30,2|
|Adatbázisok maximális száma <sup>1</sup> . készletben|500|500|500|500|500|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|1024|1024|1024|1024|1536|
|Napló maximális mérete (GB)|336|336|336|336|512|
|TempDB maximális adatméret (GB)|333|333|333|333|333|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|2560|3200|3840|4480|5120|
|Maximális naplózási arány (MB/s)|48|48|48|48|48|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|400|500|600|700|800|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|800|1000|1200|1400|1600|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0-8|0-10|0-12|0-14|0-16|
|Replikák száma|1|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|


<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="fsv2-series-compute-generation-part-2"></a>Fsv2 sorozatú számítási generáció (2. rész)

|Számítási méret (szolgáltatási cél)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Számítási generáció|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|Fsv2 sorozat|
|Virtuális mag|18|20|24|32|36|72|
|Memória (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Adatbázisok maximális száma <sup>1</sup> . készletben|500|500|500|500|500|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|1536|1536|1536|3072|3072|4096|
|Napló maximális mérete (GB)|512|512|512|1024|1024|1024|
|TempDB maximális adatméret (GB)|83,25|92,5|111|148|166,5|333|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|5760|6400|7680|10240|11520|12800|
|Maximális naplózási arány (MB/s)|48|48|48|48|48|48|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|900|1000|1200|1600|1800|3600|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|1800|2000|2400|3200|3600|7200|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0-18|0-20|0-24|0-32|0-36|0-72|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.


## <a name="general-purpose---provisioned-compute---dc-series"></a>Általános célú kiépített számítás – DC sorozat

|Számítási méret (szolgáltatási cél)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8|
|:--- | --: |--: |--: |--: |
|Számítási generáció|DC|DC|DC|DC|
|Virtuális mag|2|4|6|8|
|Memória (GB)|9|18|27|36|
|Adatbázisok maximális száma <sup>1</sup> . készletben|100|400|400|400|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|N.A.|N.A.|N.A.|N.A.|
|Maximális adatméret (GB)|756|1536|2048|2048|
|Napló maximális mérete (GB)|227|461|614|614|
|TempDB maximális adatméret (GB)|64|128|192|256|
|Tárolási típus|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|Prémium (távoli) tárterület|
|IO-késés (hozzávetőleges)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|800|1600|2400|3200|
|Maximális naplózási arány (MB/s)|9,4|18,8|28,1|32,8|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|168|336|504|672|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|168|336|504|672|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|2|2... 4|2... 6|2... 8|
|Replikák száma|1|1|1|1|
|Több – AZ|N.A.|N.A.|N.A.|N.A.|
|Olvasási felskálázás|N.A.|N.A.|N.A.|N.A.|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="business-critical---provisioned-compute---gen4"></a>Üzleti szempontból kritikus – kiépített számítás – Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Üzleti szempontból kritikus szolgáltatási szintek: 4. generációs számítási platform (1. rész)

|Számítási méret (szolgáltatási cél)|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|2|3|4|5|6|
|Memória (GB)|14|21|28|35|42|
|Adatbázisok maximális száma <sup>1</sup> . készletben|50|100|100|100|100|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|2|3|4|5|6|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|
|TempDB maximális adatméret (GB)|64|96|128|160|192|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|9000|13 500|18000|22 500|27 000|
|Maximális naplózási arány (MB/s)|20|30|40|50|60|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|420|630|840|1050|1260|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|420|630|840|1050|1260|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Replikák száma|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Yes|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Üzleti szempontból kritikus szolgáltatási szintek: 4. generációs számítási platform (2. rész)

|Számítási méret (szolgáltatási cél)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Adatbázisok maximális száma <sup>1</sup> . készletben|100|100|100|100|100|100|
|Oszlopcentrikus-támogatás|N.A.|N.A.|N.A.|N.A.|N.A.|N.A.|
|Memóriában tárolt OLTP-tároló (GB)|7|8|9,5|11|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|307|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|31 500|36 000|40 500|45.000|72 000|96 000|
|Maximális naplózási arány (MB/s)|70|80|80|80|80|80|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Yes|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="business-critical---provisioned-compute---gen5"></a>Üzleti szempontból kritikus – kiépített számítás – Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Üzleti szempontból kritikus szolgáltatási szintek: 5. generációs számítási platform (1. rész)

|Számítási méret (szolgáltatási cél)|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|4|6|8|10|12|14|
|Memória (GB)|20,8|31,1|41,5|51,9|62,3|72,7|
|Adatbázisok maximális száma <sup>1</sup> . készletben|50|100|100|100|100|100|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximális adatméret (GB)|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|461|461|922|922|
|TempDB maximális adatméret (GB)|128|192|256|320|384|448|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|18000|27 000|36 000|45.000|54 000|63 000|
|Maximális naplózási arány (MB/s)|60|90|120|120|120|120|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|420|630|840|1050|1260|1470|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|420|630|840|1050|1260|1470|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Yes|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Üzleti szempontból kritikus szolgáltatási szintek: 5. generációs számítási platform (2. rész)

|Számítási méret (szolgáltatási cél)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Adatbázisok maximális száma <sup>1</sup> . készletben|100|100|100|100|100|100|100|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|15.77|18,14|20,51|25,25|37,94|52,23|131,68|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1229|1229|1229|1229|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|72 000|81 000|90,000|108 000|144 000|180 000|256 000|
|Maximális naplózási arány (MB/s)|120|120|120|120|120|120|120|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Replikák száma|4|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="business-critical---provisioned-compute---m-series"></a>Üzleti szempontból kritikus – kiépített számítás – M sorozat

### <a name="m-series-compute-generation-part-1"></a>M sorozatú számítási generáció (1. rész)

|Számítási méret (szolgáltatási cél)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Számítási generáció|M sorozat|M sorozat|M sorozat|M sorozat|M sorozat|M sorozat|
|Virtuális mag|8|10|12|14|16|18|
|Memória (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Adatbázisok maximális száma <sup>1</sup> . készletben|100|100|100|100|100|100|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|64|80|96|112|128|150|
|Maximális adatméret (GB)|512|640|768|896|1024|1152|
|Napló maximális mérete (GB)|171|213|256|299|341|384|
|TempDB maximális adatméret (GB)|256|320|384|448|512|576|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|12 499|15 624|18 748|21 873|24 998|28 123|
|Maximális naplózási arány (MB/s)|48|60|72|84|96|108|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|800|1,000|1200|1400|1600|1800|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|800|1,000|1200|1400|1600|1800|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|0-8|0-10|0-12|0-14|0-16|0-18|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Nem|Nem|Nem|Nem|Nem|Nem|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

Ha a rugalmas készlet összes virtuális mag foglalt, akkor a készletben lévő összes adatbázis egyenlő mennyiségű számítási erőforrást kap a lekérdezések feldolgozásához. Azure SQL Database biztosítja az erőforrások egyenlő elosztását az adatbázisok között azáltal, hogy egyenlő mennyiségű számítási időt biztosít. A rugalmas készlet erőforrásainak megosztása a méltányosság érdekében az egyes adatbázisok számára más módon garantált erőforrásokhoz is, ha a virtuális mag min/adatbázis értéke nem nulla értékre van állítva.

### <a name="m-series-compute-generation-part-2"></a>M sorozatú számítási generáció (2. rész)

|Számítási méret (szolgáltatási cél)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Számítási generáció|M sorozat|M sorozat|M sorozat|M sorozat|M sorozat|
|Virtuális mag|20|24|32|64|128|
|Memória (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Adatbázisok maximális száma <sup>1</sup> . készletben|100|100|100|100|100|100|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|172|216|304|704|1768|
|Maximális adatméret (GB)|1280|1536|2048|4096|4096|
|Napló maximális mérete (GB)|427|512|683|1024|1024|
|TempDB maximális adatméret (GB)|4096|2048|1024|768|640|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|31 248|37 497|49 996|99 993|160 000|
|Maximális naplózási arány (MB/s)|120|144|192|264|264|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|2000|2 400|3 200|6 400|12 800|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|2000|2 400|3 200|6 400|12 800|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|
|Több – AZ|Nem|Nem|Nem|Nem|Nem|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

Ha a rugalmas készlet összes virtuális mag foglalt, akkor a készletben lévő összes adatbázis egyenlő mennyiségű számítási erőforrást kap a lekérdezések feldolgozásához. Azure SQL Database biztosítja az erőforrások egyenlő elosztását az adatbázisok között azáltal, hogy egyenlő mennyiségű számítási időt biztosít. A rugalmas készlet erőforrásainak megosztása a méltányosság érdekében az egyes adatbázisok számára más módon garantált erőforrásokhoz is, ha a virtuális mag min/adatbázis értéke nem nulla értékre van állítva.

## <a name="business-critical---provisioned-compute---dc-series"></a>Üzleti szempontból kritikus – kiépített számítás – DC sorozat

|Számítási méret (szolgáltatási cél)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|Számítási generáció|DC|DC|DC|DC|
|Virtuális mag|2|4|6|8|
|Memória (GB)|9|18|27|36|
|Adatbázisok maximális száma <sup>1</sup> . készletben|50|100|100|100|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Yes|
|Memóriában tárolt OLTP-tároló (GB)|1.7|3.7|5.9|8.2|
|Maximális adatméret (GB)|768|768|768|768|
|Napló maximális mérete (GB)|230|230|230|230|
|TempDB maximális adatméret (GB)|64|128|192|256|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (hozzávetőleges)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS/készletben <sup>2</sup>|15750|31500|47250|56000|
|Maximális naplózási arány (MB/s)|20|60|90|120|
|Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup>|168|336|504|672|
|Egyidejű bejelentkezések maximális száma (kérelem) <sup>3</sup>|168|336|504|672|
|Egyidejű munkamenetek maximális száma|30 000|30 000|30 000|30 000|
|Rugalmas készlet minimális/maximális virtuális mag-választéka adatbázis szerint|2|2... 4|2... 6|2... 8|
|Replikák száma|4|4|4|4|
|Több – AZ|Nem|Nem|Nem|Nem|
|Olvasási felskálázás|Igen|Igen|Igen|Yes|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az i/o-méretek maximális értéke 8 kb és 64 kb között mozog. A tényleges IOPS számítási feladatok függenek. Részletekért lásd: [adat IO-szabályozás](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

## <a name="database-properties-for-pooled-databases"></a>A készletezett adatbázisok adatbázis-tulajdonságai

A következő táblázat a készletezett adatbázisok tulajdonságait ismerteti.

> [!NOTE]
> A rugalmas készletekben található különálló adatbázisok erőforrás-korlátai általában ugyanazok, mint a készleteken kívüli önálló adatbázisok esetében, amelyek ugyanazzal a számítási mérettel rendelkeznek (szolgáltatási cél). Például az GP_Gen4_1-adatbázisok maximális egyidejű feldolgozói 200 feldolgozók. Így a GP_Gen4_1-készletben lévő adatbázisok maximálisan egyidejű feldolgozói is 200 feldolgozók. Vegye figyelembe, hogy GP_Gen4_1 készletben lévő egyidejű feldolgozók száma összesen 210.

| Tulajdonság | Leírás |
|:--- |:--- |
| Virtuális mag maximális száma |A készletben lévő bármely adatbázis által használható virtuális mag maximális száma, ha a készletben lévő más adatbázisok kihasználtsága alapján elérhető. Az adatbázisok maximális virtuális mag nem erőforrás-garancia egy adatbázishoz. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Állítsa be az adatbázis maximális virtuális mag az adatbázis-kihasználtsági csúcsok kezelésére. A rendszer bizonyos fokú túllépést vár el, mivel a készlet általánosságban feltételezi az olyan adatbázisok gyakori és ritka használati mintáit, amelyekben az összes adatbázis nem rendelkezik egyszerre csúcstal.|
| Virtuális mag minimális száma |A készletben lévő bármelyik adatbázis virtuális mag minimális száma garantált. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az adatbázis min. virtuális mag értéke 0, a pedig az alapértelmezett érték is. Ez a tulajdonság 0 és egy adatbázis átlagos virtuális mag-kihasználtsága között van beállítva. A készletben található adatbázisok száma és az adatbázis min virtuális mag nem haladhatja meg a virtuális mag-t.|
| Tárterület maximális száma adatbázison |A felhasználó által a készletben lévő adatbázis számára beállított maximális adatbázis-méret. A készletezett adatbázisok megosztják a lefoglalt készlet tárterületét, így az adatbázis mérete elérheti a fennmaradó készlet tárterületét és az adatbázis méretét. Az adatbázisok maximális mérete az adatfájlok maximális méretére vonatkozik, de nem tartalmazza a naplófájlok által használt területet. |
|||

## <a name="next-steps"></a>Következő lépések

- Egyetlen adatbázis virtuális mag erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a virtuális mag beszerzési modell használatával](resource-limits-vcore-single-databases.md)
- Egyetlen adatbázis DTU erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a DTU beszerzési modell használatával](resource-limits-dtu-single-databases.md)
- A rugalmas készletek DTU erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a DTU beszerzési modell használatával](resource-limits-dtu-elastic-pools.md)
- A felügyelt példányok erőforrás-korlátaival kapcsolatban lásd: [felügyelt példányok erőforrás-korlátai](../managed-instance/resource-limits.md).
- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- A logikai SQL Server erőforrás-korlátaival kapcsolatos információkért lásd: a [logikai SQL Server erőforrás-korlátainak áttekintése](resource-limits-logical-server.md) a kiszolgálók és az előfizetési szintek korlátaival kapcsolatos információkért.
