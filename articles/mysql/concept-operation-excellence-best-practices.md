---
title: A MySQL-kiszolgáló operatív ajánlott eljárásai – Azure Database for MySQL
description: Ez a cikk az Azure-beli MySQL-adatbázis üzemeltetésének ajánlott eljárásait ismerteti.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96355032"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Ajánlott eljárások a kiszolgálói műveletekhez Azure Database for MySQL – egyetlen kiszolgálón

Ismerje meg az Azure Database for MySQL használatának ajánlott eljárásait. Ahogy új képességeket adunk a platformhoz, továbbra is az ebben a szakaszban részletezett ajánlott eljárások finomítására fogunk összpontosítani.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Üzemeltetési irányelvek Azure Database for MySQL 

A következő működési irányelvek szükségesek, amelyeket követni kell, amikor együttműködik a Azure Database for MySQL az adatbázis teljesítményének növelése érdekében: 

* **Közös elhelyezés**: a hálózati késés csökkentése érdekében helyezze el az ügyfelet és az adatbázis-kiszolgálót ugyanabban az Azure-régióban.

* **Memória-, processzor-és tárterület-használat figyelése**: beállíthatja, hogy a [riasztások](howto-alert-on-metric.md) a használati minták változásakor vagy az üzemelő példány kapacitásának megközelítése esetén értesítsék a rendszert, így megőrizheti a rendszer teljesítményét és rendelkezésre állását. 

* **Az adatbázis-példány vertikális Felskálázása**: a tárolási kapacitás korlátaira való közeledés esetén [felskálázást](howto-create-manage-server-portal.md) végez. A tárolóban és a memóriában kell lennie egy puffernek, hogy az alkalmazásokból előre nem látható növekedést lehessen kielégíteni. A tárterület automatikus növekedés funkcióját is [engedélyezheti](howto-auto-grow-storage-portal.md) , ha azt szeretné, hogy a szolgáltatás automatikusan méretezi a tárterületet a tárolási korlátok közelében. 

* **Biztonsági másolatok konfigurálása**: engedélyezze a [helyi vagy a földrajzilag redundáns biztonsági mentést](howto-restore-server-portal.md#set-backup-configuration) a vállalat követelménye alapján. Emellett módosítja azt a megőrzési időtartamot is, ameddig a biztonsági mentések elérhetővé válnak az üzletmenet folytonossága érdekében. 

* **I/o-kapacitás javítása**: Ha az adatbázis-munkaterhelés több I/o-t igényel, mint amennyit kiépített, akkor a helyreállítás vagy az adatbázis egyéb tranzakciós műveletei lassúak lesznek. Egy kiszolgálópéldány I/O-kapacitásának növeléséhez tegye a következők egyikét vagy mindegyikét: 

    * A MySQL-hez készült Azure Database IOPS skálázást biztosít a GB-os IOPS-tárolók esetében. [Növelje a kiépített tárolót](howto-create-manage-server-portal.md#scale-storage-up) a IOPS méretezéséhez a jobb teljesítmény érdekében. 

    * Ha már használja a kiépített IOPS-tárolót, akkor [további átviteli kapacitást](howto-create-manage-server-portal.md#scale-storage-up)kell kiépíteni. 

* **Méretezési számítás**: az adatbázis-MUNKATERHELÉS a CPU vagy a memória miatt is korlátozható, és ez komoly hatással lehet a tranzakció feldolgozására. Vegye figyelembe, hogy a számítási (árképzési szintek) csak [általános célú vagy a memória optimalizált](concepts-pricing-tiers.md) szintjei között méretezhető. 

* **Feladatátvételi teszt**: a kiszolgáló példányának manuális feladatátvételi tesztje annak megismeréséhez, hogy mennyi ideig tart a használati eset, és hogy a kiszolgálói példányhoz hozzáférő alkalmazás automatikusan csatlakozni tud-e az új kiszolgálói példányhoz a feladatátvételt követően.

* **Elsődleges kulcs használata**: Ellenőrizze, hogy a táblák rendelkeznek-e elsődleges vagy egyedi kulccsal a Azure Database for MySQL való működés közben. Ez sokat segít a biztonsági másolatok, a replika stb., és javítja a teljesítményt.

* A **TTL értékének konfigurálása**: Ha az ügyfélalkalmazás gyorsítótárazza a kiszolgálói példányok DNS-adatait, állítsa be az élettartam (TTL) értékét 30 másodpercnél rövidebb értékre. Mivel egy kiszolgálópéldány mögöttes IP-címe a feladatátvételt követően változhat, a DNS-adat hosszabb ideig történő gyorsítótárazása kapcsolódási hibákhoz vezethet, ha az alkalmazás olyan IP-címhez próbál csatlakozni, amely már nem található meg a szolgáltatásban.

* A kapcsolatok összevonásával elkerülhető a [maximális kapcsolatok korlátja](concepts-server-parameters.md#max_connections), és az újrapróbálkozási logikával elkerülhetők az átmeneti kapcsolódási problémák. 

* Ha replikát használ, a [ProxySQL használatával kiegyenlítheti a terhelést](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) az elsődleges kiszolgáló és az olvasható másodlagos replika-kiszolgáló között. Tekintse meg a telepítési lépéseket itt. </br> 

* Ha kiépíti az erőforrást, győződjön meg arról, hogy [engedélyezte a Azure Database for MySQL automatikus növelését](howto-auto-grow-storage-portal.md) . Ez nem növeli a további költségeket, és az adatbázis minden olyan szűk keresztmetszetét megóvja, amelyet esetleg futtat. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>A InnoDB használata a Azure Database for MySQL

*   Ha a funkció használatakor a `ibdata1` rendszer az tablespace adatfájlját nem tudja zsugorodni vagy törölni, az adatok a táblából való eldobásával vagy a táblázatnak a táblázatba való áthelyezésével `tablespaces` .

* Az 1 TB-nál nagyobb méretű adatbázisok esetében **innodb_file_per_tableban** létre kell hoznia a táblát `tablespace` . Az 1 TB-nál nagyobb méretű táblák esetében a [partíciós](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) táblát kell használnia.

*   Nagy mennyiségű kiszolgáló esetén `tablespace` a motor indítása nagyon lassú lesz a MySQL indításakor vagy feladatátvételkor a szekvenciális tablespace-vizsgálat miatt. 

* Adja meg a innodb_file_per_table = ON értéket a tábla létrehozása előtt, ha a tábla teljes száma kisebb, mint 500.

* Ha egy adatbázisban több mint 500 táblázat található, tekintse át az egyes táblákhoz tartozó táblázat méretét. A nagyméretű táblák esetében érdemes megfontolni a file-on-Table tablespace használatát, hogy elkerülje a rendszer tablespace-fájljának maximális tárolási korlátját.

> [!NOTE]
> Az 5 GB-nál kisebb méretű táblák esetében érdemes lehet a System tablespace-t használni. 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* Ha nagyon nagy táblázattal rendelkezik, előfordulhat, hogy a táblázat létrehozásakor a tábla [particionálása](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) 1 TB fölé nő.

* Több MySQL-kiszolgálót is használhat, és eloszthatja a táblákat a kiszolgálók között. Ne helyezze el túl sok táblát egyetlen kiszolgálón, ha körülbelül 10000 vagy több táblázata van. 

## <a name="next-steps"></a>Következő lépések
- [Az Azure Database for MySQL teljesítményének bevált gyakorlata](concept-performance-best-practices.md)
- [Ajánlott eljárás a Azure Database for MySQL monitorozásához](concept-monitoring-best-practices.md)
