---
title: Erőforrások skálázása
description: Ez a cikk azt ismerteti, hogyan méretezheti az adatbázist Azure SQL Database és SQL felügyelt példányon a lefoglalt erőforrások hozzáadásával vagy eltávolításával.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 06/25/2019
ms.openlocfilehash: ca1a2edec70b13f111ffd89278aa39d1ddea7f67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105035642"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Adatbázis-erőforrások dinamikus méretezése minimális állásidővel
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database és az SQL felügyelt példánya lehetővé teszi, hogy a minimális [állásidővel](https://azure.microsoft.com/support/legal/sla/sql-database)dinamikusan felvegyen további erőforrásokat az adatbázisba. van azonban egy olyan időszak, amelyben a kapcsolat megszakadt az adatbázisba rövid idő alatt, ami az újrapróbálkozási logika használatával enyhíthető.

## <a name="overview"></a>Áttekintés

Ha az alkalmazás iránti kereslet néhány eszközről és ügyfélről több millió, Azure SQL Database és az SQL felügyelt példányának méretére nő, menet közben, minimális állásidővel. A skálázhatóság a platform szolgáltatásként használt legfontosabb jellemzői egyike, amely lehetővé teszi, hogy szükség esetén dinamikusan vegyen fel további erőforrásokat a szolgáltatásba. A Azure SQL Database lehetővé teszi az adatbázisok számára lefoglalt erőforrások (CPU-teljesítmény, memória, IO-átviteli sebesség és tárterület) egyszerű módosítását.

A teljesítményproblémák enyhítése az alkalmazás nagyobb kihasználtsága miatt, amely nem oldható fel az indexelés vagy a lekérdezés Újraírási módszereivel. A további erőforrások hozzáadásával gyorsan reagálhat, ha az adatbázis eléri az aktuális erőforrás-korlátokat, és nagyobb teljesítményre van szüksége a bejövő munkaterhelés kezeléséhez. A Azure SQL Database az erőforrások méretezését is lehetővé teszi, ha a költségeket nem szükséges csökkenteni.

Nem kell aggódnia a hardver megvásárlása és a mögöttes infrastruktúra megváltozása miatt. Az adatbázisok méretezése egyszerűen elvégezhető a Azure Portal egy csúszka használatával.

![Az adatbázis teljesítményének méretezése](./media/scale-resources/scale-performance.svg)

Azure SQL Database a [DTU-alapú vásárlási modellt](service-tiers-dtu.md) és a [virtuális mag-alapú vásárlási modellt](service-tiers-vcore.md)kínálja, míg az Azure SQL felügyelt példánya csak a [virtuális mag-alapú vásárlási modellt](service-tiers-vcore.md)kínálja. 

- A [DTU-alapú vásárlási modell](service-tiers-dtu.md) a számítási, a memória-és az I/O-erőforrások keverékét kínálja három szolgáltatási szinten a könnyű és a nehéz adatbázis-számítási feladatok támogatásához: alapszintű, standard és prémium. Az egyes szolgáltatásszintek teljesítményszintjei ezen erőforrások különféle keverékét kínálják, amelyhez további tárterület-erőforrások is hozzáadhatók.
- A [virtuális mag-alapú vásárlási modell](service-tiers-vcore.md) segítségével kiválaszthatja a virtuális mag számát, a mennyiségét vagy a memóriát, valamint a tárterület mennyiségét és sebességét. Ez a vásárlási modell három szolgáltatási szintet kínál: általános célú, üzletileg kritikus és nagy kapacitású.

Az alapszintű, standard vagy általános célú szolgáltatási szinten egy kis méretű, önálló adatbázison hozhatja létre első alkalmazását, majd a szolgáltatási szintet manuálisan vagy programozottan, a prémium vagy a üzletileg kritikus szolgáltatási szinten módosíthatja, hogy megfeleljen a megoldás igényeinek. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

> [!NOTE]
> A dinamikus méretezhetőség különbözik az automatikus skálázástól. Az automatikus skálázás akkor történik meg, amikor a szolgáltatás automatikusan a feltételek alapján méretezi a szolgáltatásokat, míg a dinamikus méretezhetőség lehetővé teszi a manuális skálázást minimális állásidővel.

A Azure SQL Database önálló adatbázisai támogatják a manuális dinamikus méretezhetőséget, de nem automatikus méretezést. Ha *automatikus* megoldást keres, érdemes megfontolni a rugalmas készletek használatát, amely lehetővé teszi, hogy az adatbázisok osztozzanak egy készlet erőforrásain az egyes adatbázisok egyedi igényei alapján.
Vannak azonban olyan parancsfájlok, amelyek segítségével automatizálható az egyes adatbázisok skálázhatósága Azure SQL Databaseban. Erre az [Egyetlen SQL-adatbázis monitorozása és skálázása a PowerShell használatával](scripts/monitor-and-scale-database-powershell.md) című témakörben láthat példát.

A [DTU-szolgáltatásszinteket](service-tiers-dtu.md) vagy a [virtuális magok jellemzőit](resource-limits-vcore-single-databases.md) bármikor módosíthatja, az alkalmazás minimális állásideje (általában átlagosan kevesebb mint négy másodperc) mellett. Számos vállalkozás és alkalmazás számára elegendő az, ha adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. Ebben a forgatókönyvben egy rugalmas készletet használ bizonyos számú Edtu, amelyek a készlet több adatbázisa között vannak megosztva.

![Bevezetés az SQL Database-be: önálló adatbázis DTU-k rétegek és szintek szerint](./media/scale-resources/single_db_dtus.png)

A Azure SQL Database lehetővé teszi az adatbázisok dinamikus méretezését:

- [Egyetlen adatbázis](single-database-scale.md)esetén [DTU](resource-limits-dtu-single-databases.md) -vagy [virtuális mag](resource-limits-vcore-single-databases.md) -modelleket is használhat az egyes adatbázisokhoz hozzárendelhető erőforrások maximális mennyiségének meghatározásához.
- A [rugalmas készletek](elastic-pool-scale.md) lehetővé teszik a készletben lévő adatbázisok csoportjára vonatkozó maximális erőforrás-korlátozás meghatározását.

Az Azure SQL felügyelt példánya lehetővé teszi a méretezést is: 

- Az [SQL felügyelt példánya](../managed-instance/sql-managed-instance-paas-overview.md) [virtuális mag](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) módot használ, és lehetővé teszi a maximális CPU-magok és a példányhoz lefoglalt maximális tárterület meghatározását. A felügyelt példányon belüli összes adatbázis osztozik a példányhoz lefoglalt erőforrásokon.

A vertikális fel-vagy leskálázási műveletek kezdeményezése bármely aroma esetében újraindítja az adatbázismotor folyamatát, és szükség esetén áthelyezi egy másik virtuális gépre. Ha az adatbázismotor folyamatát egy új virtuális gépre helyezi át, az **online folyamat** , ahol továbbra is használhatja a meglévő Azure SQL Database szolgáltatást, miközben a folyamat folyamatban van. Ha a célként megadott adatbázis-motor teljesen inicializálva van, és készen áll a lekérdezések feldolgozására, a rendszer a [forrásról a célként megadott adatbázis-motorra vált](single-database-scale.md#impact).

> [!NOTE]
> A felügyelt példány skálázása nem ajánlott, ha egy hosszan futó tranzakció, például az adatimportálás, az adatfeldolgozási feladatok, az index-Újraépítés stb. fut, vagy ha a példányon aktív a kapcsolódás. Annak megakadályozása érdekében, hogy a skálázás a szokásosnál hosszabb időt vesz igénybe, érdemes a példányt az összes hosszan futó művelet befejezésekor méretezni.

> [!NOTE]
> A vertikális Felskálázási/skálázási folyamat befejeződése után rövid kapcsolódási szünet várható. Ha implementálta az [újrapróbálkozási logikát a normál átmeneti hibákhoz](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors), akkor nem fogja észrevenni a feladatátvételt.

## <a name="alternative-scale-methods"></a>Alternatív méretezési módszerek

Az erőforrások méretezése a legegyszerűbb és a leghatékonyabb módszer az adatbázis teljesítményének javítására az adatbázis vagy az alkalmazás kódjának módosítása nélkül. Bizonyos esetekben a legmagasabb szintű szolgáltatási szintek, a számítási méretek és a teljesítmény-optimalizálási feladatok nem kezelhetik a munkaterhelést sikeres és költséghatékony módon. Ebben az esetben ezekkel a további beállításokkal méretezheti az adatbázist:

- Az [olvasási felskálázás](read-scale-out.md) egy elérhető szolgáltatás, amelyben egy írásvédett replikát kap az adatairól, ahol a csak olvasható lekérdezéseket, például jelentéseket futtathat. A csak olvasási jogosultsággal rendelkező replikák az elsődleges adatbázis erőforrás-használatának befolyásolása nélkül kezelik az írásvédett munkaterhelést.
- Az [adatbázis](elastic-scale-introduction.md) -skálázás olyan technikák összessége, amelyek lehetővé teszik az adatmegosztást több adatbázisra, és egymástól függetlenül méretezheti azokat.

## <a name="next-steps"></a>Következő lépések

- További információ az adatbázis-teljesítmény javításáról az adatbázis kódjának módosításával: [teljesítményre vonatkozó javaslatok keresése és alkalmazása](database-advisor-find-recommendations-portal.md).
- További információ a beépített adatbázis-intelligenciával való optimalizálásáról: [automatikus hangolás](automatic-tuning-overview.md).
- További információ a Azure SQL Database olvasási felskálázásáról: az írásvédett [replikák használata az írásvédett lekérdezési feladatok terheléselosztásához](read-scale-out.md).
- További információ az adatbázisok horizontális [Felskálázásáról: horizontális felskálázás Azure SQL Database](elastic-scale-introduction.md).
