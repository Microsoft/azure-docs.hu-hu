---
title: Azure SQL Database nagy kapacitású – gyakori kérdések
description: Válaszok a gyakori kérdésekre az ügyfeleknek a nagy kapacitású szolgáltatási rétegben (nagy kapacitású-adatbázis) általánosan megjelenő SQL Database adatbázissal kapcsolatos információkat kérnek.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 02/03/2021
ms.openlocfilehash: c6faa1703b0935d66d291281f33027b3a66a59d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526822"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL Database nagy kapacitású – gyakori kérdések
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk válaszokat ad az ügyfelek számára a Azure SQL Database nagy kapacitású szolgáltatási szinten található adatbázisra vonatkozó gyakori kérdésekre, amelyek a gyakori kérdések további részében csak nagy kapacitású szerepelnek. Ez a cikk a nagy kapacitású által támogatott forgatókönyveket és a nagy kapacitású kompatibilis szolgáltatásokat ismerteti.

- Ez a gyakori kérdések olyan olvasók számára készültek, akik röviden megértették a nagy kapacitású szolgáltatási szintet, és arra keresnek, hogy konkrét kérdéseiket és szempontjaikat is megválaszolják.
- Ez a GYIK nem azt jelenti, hogy a nagy kapacitású-adatbázis használatáról szóló útikönyv vagy kérdések megválaszolására szolgál. A nagy kapacitású bevezetését javasoljuk, hogy tekintse át a [Azure SQL Database nagy kapacitású](service-tier-hyperscale.md) dokumentációját.

## <a name="general-questions"></a>Általános kérdések

### <a name="what-is-a-hyperscale-database"></a>Mi az a nagy kapacitású-adatbázis?

A nagy kapacitású-adatbázis a nagy kapacitású szolgáltatási rétegben lévő, a nagy kapacitású kibővíthető tárolási technológiával támogatott SQL Database adatbázis. A nagy kapacitású-adatbázisok akár 100 TB-os adatmennyiséget is támogatnak, és magas átviteli sebességet és teljesítményt biztosítanak, valamint gyors méretezést tesznek lehetővé a számítási feladatok követelményeinek megfelelően. A skálázás átlátható az alkalmazás – kapcsolat, lekérdezés-feldolgozás stb., mint bármely más adatbázis Azure SQL Databaseban.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Milyen típusú erőforrástípusok és beszerzési modellek támogatják a nagy kapacitású-t

A nagy kapacitású szolgáltatási réteg csak az önálló adatbázisok esetében érhető el a Azure SQL Database virtuális mag-alapú vásárlási modelljét használva.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Miben különbözik a nagy kapacitású szolgáltatási szintjei a általános célú és üzletileg kritikus szolgáltatási szintjeitől

A virtuális mag-alapú szolgáltatási rétegek a következő táblázatban leírtak szerint differenciálva vannak az adatbázisok rendelkezésre állása és a tárolási típus, a teljesítmény és a maximális méret alapján.

| | Erőforrás típusa | Általános célú |  Rugalmas skálázás | Üzletileg kritikus |
|:---:|:---:|:---:|:---:|:---:|
| **A következőkre alkalmas** |Mind|A költségvetés-orientált kiegyensúlyozott számítási és tárolási lehetőségeket kínál.|A legtöbb üzleti számítási feladat. A tárterület automatikus skálázása 100 TB-ig, gyors vertikális és horizontális számítási skálázás, gyors adatbázis-visszaállítás.|OLTP alkalmazások nagy tranzakciós sebességgel és alacsony IO-késéssel. Maximális rugalmasságot biztosít a hibák és a gyors feladatátvételek esetében, több szinkronban frissített replika használatával.|
|  **Erőforrás típusa** ||SQL Database/SQL felügyelt példány | Önálló adatbázis | SQL Database/SQL felügyelt példány |
| **Számítási méret**|SQL Database * | 1 – 80 virtuális mag | 1 – 80 virtuális mag * | 1 – 80 virtuális mag |
| **Számítási méret**|SQL Managed Instance | 8, 16, 24, 32, 40, 64, 80 virtuális mag | N/A | 8, 16, 24, 32, 40, 64, 80 virtuális mag |
| **Tárolási típus** | Mind |Prémium szintű távoli tárterület (/példány) | A leválasztott tárterület helyi SSD-gyorsítótárral (/példány) | Villámgyors helyi SSD-tároló (példány) |
| **Tárterület mérete** | SQL Database *| 5 GB – 4 TB | Akár 100 TB | 5 GB – 4 TB |
| **Tárterület mérete** | SQL Managed Instance  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **IOPS** | Önálló adatbázis | 500 IOPS/virtuális mag 7000 maximális IOPS | A nagy kapacitású egy többrétegű architektúra, több szinten történő gyorsítótárazással. A hatékony IOPS a munkaterheléstől függ. | 5000 IOPS 200 000 maximális IOPS|
| **IOPS** | SQL Managed Instance | Fájlmérettől függ | N/A | 1375 IOPS/virtuális mag |
|**Rendelkezésre állás**|Mind|1 replika, nincs olvasási felskálázás, nincs helyi gyorsítótár | Több replika, legfeljebb 4 olvasási felskálázás, részleges helyi gyorsítótár | 3 replika, 1 olvasási felskálázás, zóna – redundáns HA, teljes helyi tárterület |
|**Biztonsági másolatok**|Mind|RA-GRS, 7-35 napos megőrzés (alapértelmezés szerint 7 nap)| RA-GRS, 7 napos megőrzés, állandó időpontok közötti helyreállítási idő (PITR) | RA-GRS, 7-35 napos megőrzés (alapértelmezés szerint 7 nap) |

\* A rugalmas készletek nem támogatottak a nagy kapacitású szolgáltatási szinten

### <a name="who-should-use-the-hyperscale-service-tier"></a>Kinek érdemes a nagy kapacitású szolgáltatási szintet használnia

A nagy kapacitású szolgáltatási réteg olyan ügyfelek számára készült, akik nagyméretű helyszíni SQL Server-adatbázisokkal rendelkeznek, és az alkalmazásait a felhőbe való áttéréssel, illetve olyan ügyfelek számára kívánják modernizálni, akik már használják a Azure SQL Databaset, és jelentős mértékben ki szeretnék bővíteni a lehetséges adatbázis-növekedést. A nagy kapacitású olyan ügyfelek számára is ajánlott, akik nagy teljesítményt és magas skálázhatóságot keresnek. A nagy kapacitású a következőket kapja:

- Adatbázis mérete akár 100 TB-ig
- Gyors adatbázisok biztonsági mentése az adatbázis méretétől függetlenül (a biztonsági másolatok tárolási pillanatképeken alapulnak)
- Gyors adatbázis-visszaállítás az adatbázis méretétől függetlenül (a visszaállítások a tárolási pillanatképekről származnak)
- Nagyobb naplózási teljesítmény az adatbázis méretétől és a virtuális mag számától függetlenül
- Olvassa el a méretezési felskálázást egy vagy több írásvédett replikával, amelyek olvasási kiszervezéshez és gyors készenléti állapothoz használatosak.
- A számítási feladatok gyors vertikális felskálázása állandó idő alatt, hogy hatékonyabb legyen a nehéz munkaterhelésnek megfelelően alkalmazkodni, majd az állandó idő alatt lekicsinyíthető legyen. Ez hasonló a P6 és a P11 közötti felskálázáshoz, például sokkal gyorsabb, mivel ez nem az adatműveletek mérete.

### <a name="what-regions-currently-support-hyperscale"></a>A jelenleg támogatott régiók nagy kapacitású

A nagy kapacitású szolgáltatási szintje jelenleg a [Azure SQL Database nagy kapacitású áttekintése](service-tier-hyperscale.md#regions)területen felsorolt régiókban érhető el.

### <a name="can-i-create-multiple-hyperscale-databases-per-server"></a>Több nagy kapacitású-adatbázis is létrehozható kiszolgálónként

Igen. A kiszolgálók nagy kapacitású-adatbázisainak számával kapcsolatos további információkért és korlátokért tekintse meg a [kiszolgálók egy-és készletezett adatbázisainak SQL Database erőforrás-korlátait](resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>A nagy kapacitású-adatbázisok teljesítményének jellemzői

A nagy kapacitású architektúra nagy teljesítményt és teljesítményt biztosít a nagyméretű adatbázisok méretének támogatása mellett.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>A nagy kapacitású-adatbázisok skálázhatósága

A nagy kapacitású a számítási feladatok igénye alapján gyors skálázhatóságot biztosít.

- **Felfelé/lefelé skálázás**

  A nagy kapacitású segítségével az elsődleges számítási méretet az erőforrások, például a processzor és a memória, valamint az állandó idő szerinti skálázás alapján méretezheti fel. Mivel a tárterület meg van osztva, a skálázás és a skálázás nem az adatművelet mérete.  
- **Méretezés be/ki**

  A nagy kapacitású lehetővé teszi egy vagy több további számítási replika kiépítését, amelyeket az olvasási kérések kiszolgálására használhat fel. Ez azt jelenti, hogy ezeket a további számítási replikákat csak olvasható replikáként használhatja az olvasási munkaterhelés az elsődleges számításból való kiszervezéséhez. A csak olvasható adatok mellett ezek a replikák is meleg készenléti állapotot biztosítanak az elsődlegestől érkező feladatátvétel esetén.

  Ezen további számítási replikák kiépítés állandó időben és online művelettel végezhető el. Ehhez a további írásvédett számítási replikához is csatlakozhat, ha a `ApplicationIntent` kapcsolati karakterlánc argumentumát a értékre állítja `ReadOnly` . Az `ReadOnly` alkalmazás szándékával létesített kapcsolatokat a rendszer automatikusan átirányítja a további írásvédett számítási replikák egyikére.

## <a name="deep-dive-questions"></a>Részletes kérdések

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-server"></a>Használhatok nagy kapacitású és önálló adatbázisokat egyetlen kiszolgálón

Igen, írhat.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>A nagy kapacitású meg kell változtatni az alkalmazás programozási modelljét

Nem, az alkalmazás programozási modellje a következőképpen marad. A kapcsolódási karakterláncot a szokásos módon, a másik pedig a nagy kapacitású-adatbázissal való interakcióhoz használhatja.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>A nagy kapacitású-adatbázis alapértelmezett tranzakció-elkülönítési szintje

Az elsődleges replikán az alapértelmezett tranzakció elkülönítési szintje RCSI (olvasási véglegesített pillanatkép-elkülönítés). Az olvasási felskálázás másodlagos replikái esetében az alapértelmezett elkülönítési szint a pillanatkép.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Használhatom a helyszíni vagy IaaS SQL Server licencet a nagy kapacitású

Igen, [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) elérhető a nagy kapacitású számára. Minden SQL Server Standard mag 1 nagy kapacitású-virtuális mag képezhető le. Minden SQL Server Enterprise mag 4 nagy kapacitású-virtuális mag képezhető le. Másodlagos replikák esetén nem szükséges SQL-licenc. A rendszer automatikusan alkalmazza a Azure Hybrid Benefit árát a kibővíthető (másodlagos) replikák olvasására.

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Milyen számítási feladatokat terveztek a nagy kapacitású

A nagy kapacitású támogatja az összes SQL Server munkaterhelést, de elsődlegesen a OLTP-re van optimalizálva. A hibrid (HTAP) és az analitikus (data mart) számítási feladatokat is használhatja.

### <a name="how-can-i-choose-between-azure-synapse-analytics-and-azure-sql-database-hyperscale"></a>Hogyan választhatok az Azure szinapszis Analytics és a Azure SQL Database nagy kapacitású között

Ha jelenleg interaktív elemzési lekérdezéseket futtat SQL Server adattárházként, a nagy kapacitású nagyszerű megoldás, mivel a kis-és közepes méretű adattárházak (például néhány TB akár 100 TB-ig) alacsonyabb díjszabás mellett is telepíthetők, és az SQL Server adattárház-munkaterheléseket a minimális T-SQL-nagy kapacitású áttelepítheti.

Ha az adatelemzést nagy méretekben futtatja összetett lekérdezésekkel és tartós betöltési sebességgel, amely nagyobb, mint 100 MB/s, vagy párhuzamos adattárház (PDW), Teradata vagy más nagymértékben párhuzamos feldolgozási (MPP) adattárházak használata, az Azure szinapszis Analytics lehet a legjobb választás.
  
## <a name="hyperscale-compute-questions"></a>Nagy kapacitású számítási kérdések

### <a name="can-i-pause-my-compute-at-any-time"></a>Bármikor szüneteltethető a számításom

Jelenleg azonban a számítások és a replikák számának csökkentése érdekében a költségeket a nem csúcsidőben is csökkentheti.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Létrehozhatok egy extra RAM-mal rendelkező számítási replikát a nagy memória-igényű munkaterhelés számára

Nem. A RAM további mennyiségének megkezdéséhez magasabb számítási méretre kell frissítenie. További információ: [nagy kapacitású Storage és számítási méretek](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Több különböző méretű számítási replika is kiépíthető

Nem.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Hány olvasási kibővített replika támogatott

Alapértelmezés szerint a nagy kapacitású-adatbázisok egyetlen olvasási kibővített replikával (két replikával, köztük az elsődlegesvel) jönnek létre. Az írásvédett replikák számát 0 és 4 között méretezheti [Azure Portal](https://portal.azure.com) vagy [REST API](/rest/api/sql/databases/createorupdate)használatával.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>A magas rendelkezésre állás érdekében további számítási replikákat kell kiépíteni

A nagy kapacitású-adatbázisokban az adatrugalmasságot a tárolási szinten biztosítjuk. Csak egy replikára van szükség a rugalmasság biztosításához. Ha a számítási replika nem érhető el, a rendszer automatikusan létrehoz egy új replikát adatvesztés nélkül.

Ha azonban csak egy replika van, eltarthat egy ideig, hogy a feladatátvételt követően létrejöjjön a helyi gyorsítótár az új replikában. A gyorsítótár-újraépítési fázisban az adatbázis közvetlenül az oldalról kérdezi le az adatforrásokat, ami nagyobb tárolási késést és csökkentett teljesítményű lekérdezési teljesítményt eredményez.

A magas rendelkezésre állást igénylő, kritikus fontosságú alkalmazások esetében minimális feladatátvételi hatás esetén legalább 2 számítási replikát kell kiépíteni, beleértve az elsődleges számítási replikát is. Ez az alapértelmezett beállítás. Így rendelkezésre áll egy olyan gyors készenléti replika, amely feladatátvételi célként szolgál.

## <a name="data-size-and-storage-questions"></a>Az adatméretre és a tárolásra vonatkozó kérdések

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>A nagy kapacitású által támogatott maximális adatbázis-méret

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Mi a nagy kapacitású rendelkező tranzakciónapló mérete

A nagy kapacitású és a tranzakciós napló gyakorlatilag végtelen. Nem kell aggódnia a naplózási területnek a nagy adatátviteli sebességű rendszeren való futtatásával. A log generálási arány azonban a folyamatos, agresszíven írt számítási feladatok esetében is szabályozható. A tartós log-generálási sebesség 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Növekszik az `tempdb` adatbázisom méretezése

Az `tempdb` adatbázis a helyi SSD-tárolón található, és az Ön által kiépített számítási mérettel arányos mérettel rendelkezik. A `tempdb` szolgáltatás a maximális teljesítménybeli előnyök biztosítására van optimalizálva. `tempdb` a méret nem konfigurálható és kezelhető.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Az adatbázis méretének automatikus növekedése vagy az adatfájlok méretének kezelése szükséges

Az adatbázis mérete automatikusan növekszik, amikor több adatot szúr be/tölt be.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Mi az a legkisebb adatbázis-méret, amelyet a nagy kapacitású támogat vagy elindít

40 GB. A nagy kapacitású-adatbázis 10 GB-os kezdő mérettel jön létre. Ezt követően 10 GB-onként 10 percenként növekszik, amíg el nem éri a 40 GB-os méretet. A 10 GB-os adattömbök mindegyike egy másik kiszolgálóoldali kiszolgálón van lefoglalva, hogy nagyobb IOPS és nagyobb I/O-párhuzamosságot biztosítson. Az optimalizálás miatt még akkor is, ha a kezdeti adatbázis mérete 40 GB-nál kisebb, az adatbázis legalább 40 GB-ra növekedni fog.

### <a name="in-what-increments-does-my-database-size-grow"></a>Milyen növekményekben növekszik az adatbázis mérete

Az egyes adatfájlok mérete 10 GB. Egyszerre több adatfájl is növekedhet.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>A nagy kapacitású helyi vagy távoli tárolója

A nagy kapacitású-ben az adatfájlok tárolása az Azure standard Storage szolgáltatásban történik. Az adatok teljes mértékben gyorsítótárazva vannak a helyi SSD-tárolón, a számítási replikához közelebb eső kiszolgálókon. Emellett a számítási replikák a helyi SSD-meghajtón és a memóriában is rendelkeznek adatgyorsítótárral, így csökkenthetik az adatok távoli kiszolgálóról való beolvasásának gyakoriságát.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Kezelhetem vagy Megadhatom a fájlokat vagy fájlcsoportok a nagy kapacitású használatával

Nem. Az adatfájlok automatikusan hozzáadódnak. A további fájlcsoportok létrehozásának leggyakoribb okai a nagy kapacitású tárolási architektúrájában nem érvényesek.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Létrehozhatok-e szigorú korlátot az adatbázis adatnövekedéséhez

Nem.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Hogyan történik az adatfájlok nagy kapacitású való megállapítása

Az adatfájlokat a lapozófájlok vezérlik, és az adatfájlok egy oldal-kiszolgálóval rendelkeznek. Ahogy az adatméret növekszik, az adatfájlok és a hozzájuk társított lapok kiszolgálói is felkerülnek.

### <a name="is-database-shrink-supported"></a>Támogatott-e az adatbázis-zsugorodás

Nem.

### <a name="is-data-compression-supported"></a>Az adattömörítés támogatott

Igen, beleértve a sort, az oldalt és a oszlopcentrikus tömörítést.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Ha óriási táblázattal rendelkezem, a táblázat adatai több adatfájlba kerülnek

Igen. Egy adott táblához tartozó adatlapokon több adatfájl is végződhet, amelyek mindegyike azonos fájlcsoportja tartozik. A SQL Server [arányos kitöltési stratégiát](/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) használ az adatfájlokon keresztüli adatterjesztésre.

## <a name="data-migration-questions"></a>Az adatáttelepítés kérdései

### <a name="can-i-move-my-existing-databases-in-azure-sql-database-to-the-hyperscale-service-tier"></a>Áthelyezhetem a meglévő adatbázisokat Azure SQL Database a nagy kapacitású szolgáltatási szintjére

Igen. A meglévő adatbázisait Azure SQL Database nagy kapacitású helyezheti át. Ez egy egyirányú áttelepítés. Az adatbázisok nem helyezhetők át a nagy kapacitású egy másik szolgáltatási szintjére. A (Pócsi) koncepció igazolására azt javasoljuk, hogy készítsen másolatot az adatbázisról, és telepítse át a másolást a nagy kapacitású. 

Egy meglévő adatbázis nagy kapacitású való áthelyezéséhez szükséges idő az adatok másolásának ideje, valamint az adatok másolása során a forrásadatbázisban végrehajtott módosítások visszajátszásának ideje. Az adatmásolási idő arányos az adatmérettel. A változtatások ideje rövidebb lesz, ha az áthelyezés egy alacsony írási tevékenység alatt történik.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Áthelyezhetem a nagy kapacitású-adatbázisokat más szolgáltatási rétegekbe

Nem. Jelenleg nem helyezhető át egy nagy kapacitású-adatbázis egy másik szolgáltatási szintjére.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Elveszítem a nagy kapacitású szolgáltatási szintjére való Migrálás után elérhető funkciókat és képességeket

Igen. Néhány Azure SQL Database funkció még nem támogatott a nagy kapacitású-ben, beleértve a hosszú távú biztonsági másolatok megőrzését. Miután áttelepítette az adatbázisokat a nagy kapacitású, ezek a funkciók nem működnek.  Elvárjuk, hogy ezek a korlátozások ideiglenesek legyenek.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Áthelyezhetem a helyszíni SQL Server-adatbázist vagy a saját SQL Server-adatbázist egy felhőalapú virtuális gépen a nagy kapacitású

Igen. Az összes meglévő áttelepítési technológia segítségével áttelepítheti a nagy kapacitású, beleértve a tranzakciós replikációt és bármely más adatáthelyezési technológiát (tömeges másolás, Azure Data Factory, Azure Databricks, SSIS). Lásd még a [Azure Database Migration Service](../../dms/dms-overview.md), amely számos áttelepítési forgatókönyvet támogat.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Milyen állásidő van a helyszíni vagy a virtuálisgép-környezetből a nagy kapacitású való Migrálás során, és hogyan csökkenthetim

A nagy kapacitású-re való áttelepítéshez használt állásidő ugyanaz, mint az adatbázisok más Azure SQL Database szolgáltatási szintjeire való áttelepítésekor. A [tranzakciós replikálással](replication-to-sql-database.md#data-migration-scenario
) csökkentheti az adatbázisok leállási áttelepítését akár néhány TB-os méretig. A nagy méretű adatbázisok (10 + TB) esetében érdemes lehet az ADF-, Spark-vagy más adatáthelyezési technológiák használatával áttelepíteni az adatátvitelt.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Mennyi időt kellene igénybe venni, hogy X mennyiségű adattal nagy kapacitású

A nagy kapacitású képes az új/módosított adatok 100 MB/s értékének felhasználására, de az adatok Azure SQL Database adatbázisba való áthelyezéséhez szükséges idő az elérhető hálózati átviteli sebesség, a forrás olvasási sebessége és a célként megadott adatbázis-szolgáltatási szint célkitűzés is hatással van.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-azure-synapse-analytics"></a>Beolvasható az adatok a blob Storage-ból, és gyors betöltési műveletek (például az Azure szinapszis Analyticsben az alapszintű)

Lehet, hogy egy ügyfélalkalmazás beolvassa az Azure Storage-ból származó adatait, és betölti az adatterhelést egy nagy kapacitású-adatbázisba (ugyanúgy, mint bármely más adatbázishoz Azure SQL Database). A Azure SQL Database jelenleg nem támogatja a Base használatát. A gyors betöltéshez használhatja a [Azure Data Factoryt](../../data-factory/index.yml), vagy használhat egy Spark-feladatot a [Azure Databricksban](/azure/azure-databricks/) az SQL- [hez készült Spark-összekötővel](spark-connector.md). A Spark-összekötő az SQL-hez támogatja a tömeges beszúrást.

Az Azure Blob Store-ból az BULK INSERT vagy a OPENROWSET használatával tömegesen is olvashatók az adatok, [például az azure blob Storageban tárolt adatok tömeges hozzáférésére](/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

Az egyszerű helyreállítás vagy a tömeges naplózási modell nem támogatott a nagy kapacitású. A magas rendelkezésre állás és az időponthoz tartozó helyreállítás biztosításához teljes helyreállítási modell szükséges. A nagy kapacitású-naplózási architektúra azonban jobb adatfeldolgozási sebességet biztosít a többi Azure SQL Database szolgáltatási szintjéhez képest.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>A nagy kapacitású lehetővé teszi több csomópont kihelyezését a nagy mennyiségű adatfeldolgozás párhuzamos betöltéséhez

Nem. A nagy kapacitású egy szimmetrikus többprocesszoros (SMP) architektúra, és nem egy nagymértékben párhuzamos feldolgozás (MPP) vagy egy több főkiszolgálós architektúra. Csak a csak olvasási feladatok skálázásához több replikát is létrehozhat.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Mi a legrégebbi SQL Server verziója támogatott a nagy kapacitású való áttelepítéshez

SQL Server 2005. További információ: [áttelepítés önálló adatbázisra vagy készletezett adatbázisra](migrate-to-database-from-sql-server.md#migrate-to-a-single-database-or-a-pooled-database). Kompatibilitási problémák esetén tekintse meg az [adatbázis-áttelepítési kompatibilitási problémák megoldását](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues)ismertető témakört.

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Támogatja a nagy kapacitású az áttelepítést más adatforrásokból, például az Amazon Aurora, a MySQL, a PostgreSQL, az Oracle, a DB2 és más adatbázis-platformok használatával.

Igen. A [Azure Database Migration Service](../../dms/dms-overview.md) számos áttelepítési forgatókönyvet támogat.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Üzletmenet-folytonossági és vész-helyreállítási kérdések

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Milyen SLA-kat biztosítanak egy nagy kapacitású-adatbázishoz

Lásd: [Azure SQL Database SLA](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/)-ja. A további másodlagos számítási replikák a rendelkezésre állás növelését, akár 99,99%-ot biztosítanak egy olyan adatbázis esetében, amelynek két vagy több másodlagos számítási replikája van.

### <a name="are-the-database-backups-managed-for-me-by-azure-sql-database"></a>A Azure SQL Database által kezelt adatbázis-biztonsági másolatok

Igen.

### <a name="how-often-are-the-database-backups-taken"></a>Az adatbázis biztonsági másolatainak gyakorisága

A nagy kapacitású-adatbázisok esetében nincsenek a hagyományos teljes, differenciális és naplózott biztonsági másolatok. Ehelyett a rendszer az adatfájlok rendszeres tárolási pillanatképeit is felhasználja. A létrehozott napló egyszerűen megőrizhető a konfigurált megőrzési időtartamra, így a visszaállíthatók a megőrzési időtartamon belül bármely időpontra.

### <a name="does-hyperscale-support-point-in-time-restore"></a>A nagy kapacitású támogatja az időponthoz való visszaállítást

Igen.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Mi a helyreállítási pont célkitűzése (RPO)/Recovery időcélkitűzése (RTO) az adatbázis-visszaállításhoz a nagy kapacitású-ben

A RPO 0 perc. A legtöbb visszaállítási művelet 60 percen belül befejeződik, az adatbázis méretétől függetlenül. A visszaállítási idő hosszabb lehet a nagyobb adatbázisok esetében, és ha az adatbázis jelentős írási tevékenységet észlelt a visszaállítási pont előtt és közben.

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Az adatbázis biztonsági mentése hatással van az elsődleges vagy másodlagos replikák számítási teljesítményére

Nem. A biztonsági mentéseket a tárolási alrendszer kezeli, és a tárolási Pillanatképek kihasználása. Nem érintik a felhasználói munkaterheléseket.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Végezhetek geo-visszaállítást egy nagy kapacitású-adatbázissal

Igen. A Geo-visszaállítás teljes mértékben támogatott. Az időponthoz képesti visszaállítástól eltérően a Geo-visszaállításhoz adatmennyiség-adatműveletre van szükség. Az adatfájlok másolása párhuzamosan történik, így a művelet időtartama elsősorban az adatbázis legnagyobb fájljának méretétől függ, nem pedig az adatbázis teljes méretétől. A Geo-visszaállítási idő jelentősen rövidebb lesz, ha a rendszer visszaállítja az adatbázist az Azure-régióban, amely a forrás-adatbázis régiójával [párosítva](../../best-practices-availability-paired-regions.md) van.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Beállítható a Geo-replikáció a nagy kapacitású-adatbázissal

Jelenleg nem.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Készíthetek nagy kapacitású-adatbázis biztonsági mentését, és Visszaállíthatom azt a helyszíni kiszolgálóra vagy egy virtuális gépen lévő SQL Serverra

Nem. A nagy kapacitású-adatbázisok tárolási formátuma eltér a SQL Server bármely kiadott verziójától, és nem szabályozza a biztonsági mentéseket, és nem fér hozzájuk. Az adatok nagy kapacitású-adatbázisból való kinyeréséhez bármilyen adatáthelyezési technológiával kinyerheti az adatait, például a Azure Data Factory, a Azure Databricks, a SSIS stb.

## <a name="cross-feature-questions"></a>Több funkcióval kapcsolatos kérdések

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Elveszítem a nagy kapacitású szolgáltatási szintjére való Migrálás után elérhető funkciókat és képességeket

Igen. Egyes Azure SQL Database szolgáltatások nem támogatottak a nagy kapacitású-ben, beleértve a hosszú távú biztonsági másolatok megőrzését. Miután áttelepítette az adatbázisokat a nagy kapacitású, ezek a funkciók nem működnek.

### <a name="will-polybase-work-with-hyperscale"></a>A nagy kapacitású együtt fog működni

Nem. A Azure SQL Database nem támogatja a Base használatát.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>A nagy kapacitású támogatja az R és a Python használatát

Jelenleg nem.

### <a name="are-compute-nodes-containerized"></a>Tárolóban lévő számítási csomópontok

Nem. A nagy kapacitású folyamatok [Service Fabric](https://azure.microsoft.com/services/service-fabric/) csomópontokon (virtuális gépeken) futnak, nem tárolókban.

## <a name="performance-questions"></a>Teljesítménnyel kapcsolatos kérdések

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Mennyi írási sebességre lehet leküldeni a nagy kapacitású-adatbázist

A tranzakciónapló átviteli sebességének felső határa 100 MB/s értékre van állítva bármilyen nagy kapacitású számítási méretnél. Ennek a díjszabásnak a megvalósítása több tényezőtől függ, többek között a munkaterhelés típusától, az ügyfél konfigurációjától és az elsődleges számítási replika megfelelő számítási kapacitásával, így a napló ezen a sebességgel hozható létre.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Hány IOPS kapok a legnagyobb számítási feladatokhoz

A IOPS és az IO-késés a munkaterhelés-mintáktól függően változhat. Ha az elérni kívánt adatok a számítási replikán vannak gyorsítótárazva, a helyi SSD-vel hasonló IO-teljesítményt fog látni.

### <a name="does-my-throughput-get-affected-by-backups"></a>A biztonsági másolatok hatással vannak a teljesítményre

Nem. A számítás a tárolási rétegből van leválasztva. Ez kiküszöböli a biztonsági mentés teljesítményére gyakorolt hatást.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Hatással van-e az átviteli sebesség a további számítási replikák kiépítésekor

Mivel a tárterület meg van osztva, és nincs közvetlen fizikai replikáció az elsődleges és a másodlagos számítási replikák között, a másodlagos replikák hozzáadásával az elsődleges replika átviteli sebessége nem lesz közvetlenül hatással. A másodlagos replikák esetében azonban lehetséges a folyamatos, agresszív írásos munkaterhelések szabályozása az elsődlegesen, hogy a napló a másodlagos replikák és a lapok kiszolgálóin is érvénybe lépjen, így elkerülhető a gyenge olvasási teljesítmény.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Nagy kapacitású-adatbázis teljesítménybeli problémáinak diagnosztizálása és hibaelhárítása Hogyan

A legtöbb teljesítménnyel kapcsolatos probléma esetén, különösen a tárolási teljesítményhez nem feltörtek, az általános SQL diagnosztikai és hibaelhárítási lépések érvényesek. A nagy kapacitású-specifikus tárolási diagnosztika esetében lásd: [SQL nagy kapacitású Performance hibaelhárítási diagnosztika](hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Méretezésre vonatkozó kérdések

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Mennyi időt vesz igénybe a számítási replika vertikális fel-és leskálázása

A számítások felfelé vagy lefelé skálázása általában akár 2 percet is igénybe vehet, az adatok méretétől függetlenül.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Az adatbázis offline állapotban van, amíg folyamatban van a méretezés felfelé/lefelé művelete.

Nem. A felfelé és lefelé történő skálázás online állapotú lesz.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Ha a skálázási műveletek folyamatban vannak, a csatlakozás eldobása várható.

Ha a skálázási művelet végén a feladatátvétel történik, a felfelé vagy lefelé irányuló folyamat a meglévő kapcsolatok eldobását eredményezi. A másodlagos replikák hozzáadása nem eredményezi a kapcsolatok elejtését.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>A számítási replikák automatikus vagy végfelhasználó által aktivált műveletének méretezése

Végfelhasználó. Nem automatikus.  

### <a name="does-the-size-of-my-tempdb-database-and-rbpex-cache-also-grow-as-the-compute-is-scaled-up"></a>Az `tempdb` adatbázis és a RBPEX gyorsítótár mérete is nő, ahogy a számítási felskálázás

Igen. A `tempdb` számítási csomópontok adatbázis-és [RBPEX-gyorsítótárának](service-tier-hyperscale.md#distributed-functions-architecture) mérete automatikusan felskálázásra kerül a magok számának növelésével.

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Több elsődleges számítási replika is kiépíthető, például egy több főkiszolgálós rendszer, ahol több elsődleges számítási fej is lehet magasabb szintű párhuzamosságot vezetni

Nem. Csak az elsődleges számítási replika fogad írási/olvasási kérelmeket. A másodlagos számítási replikák csak olvasási kérelmeket fogadnak el.

## <a name="read-scale-out-questions"></a>Felskálázási kérdések olvasása

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Hány másodlagos számítási replikát lehet kiépíteni

Alapértelmezés szerint egy másodlagos replikát hozunk létre a nagy kapacitású-adatbázisokhoz. Ha módosítani szeretné a replikák számát, [Azure Portal](https://portal.azure.com) vagy [REST API](/rest/api/sql/databases/createorupdate)használatával teheti meg.

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Hogyan kapcsolódás ehhez a másodlagos számítási replikához

Ehhez a további írásvédett számítási replikához is csatlakozhat, ha a `ApplicationIntent` kapcsolati karakterlánc argumentumát a értékre állítja `ReadOnly` . A `ReadOnly` rendszer automatikusan átirányítja a (val) jelölésű kapcsolatokat a további írásvédett számítási replikák egyikére. Részletekért lásd: írásvédett [replikák használata a csak olvasási lekérdezési feladatok kiszervezéséhez](read-scale-out.md).

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Hogyan ellenőrizni, hogy sikerült-e csatlakozni a másodlagos számítási replikához a SSMS vagy más ügyféleszközök használatával?

A következő T-SQL-lekérdezést végezheti el: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')` .
Az eredmény az, `READ_ONLY` Ha egy írásvédett másodlagos replikához csatlakozik, és `READ_WRITE` Ha csatlakozik az elsődleges replikához. Vegye figyelembe, hogy az adatbázis-környezetet a nagy kapacitású-adatbázis nevére kell beállítani, nem pedig az `master` adatbázishoz.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Létrehozhatok dedikált végpontot egy olvasási kibővíthető replika számára

Nem. A következő megadásával csak a kibővíthető kibővített replikákat lehet csatlakozni `ApplicationIntent=ReadOnly` .

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Az olvasási feladat intelligens terheléselosztása a rendszeren történik

Nem. A csak olvasási szándékkal rendelkező új kapcsolatok egy tetszőleges olvasási Felskálázási replikára vannak átirányítva.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>A másodlagos számítási replikák az elsődleges replikától függetlenül méretezhetők és leállíthatók

Nem. A másodlagos számítási replika a magas rendelkezésre állású feladatátvételi célokként is használatos, ezért a feladatátvételt követően a várt teljesítmény biztosításához meg kell egyezniük az elsődleges konfigurációval.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Eltérő `tempdb` méretezést kapok az elsődleges számítási és a további másodlagos számítási replikák esetében

Nem. Az `tempdb` adatbázis a számítási méret kiépítés alapján van konfigurálva, a másodlagos számítási replikák mérete megegyezik az elsődleges számítási feladatokkal.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Hozzáadhatok indexeket és nézeteket a másodlagos számítási replikákban

Nem. A nagy kapacitású-adatbázisok megosztott tárolóval rendelkeznek, ami azt jelenti, hogy minden számítási replika ugyanazokat a táblákat, indexeket és nézeteket látja. Ha azt szeretné, hogy a másodlagos olvasásra optimalizált további indexek legyenek, akkor azokat az elsődlegesen kell felvennie.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Mennyi késleltetéssel kell számolni az elsődleges és a másodlagos számítási replikák között

Az adatok késése attól az időponttól kezdve, amikor egy tranzakció véglegesítve lett az elsődlegesen a másodlagos olvasáskor, az aktuális log-generálási aránytól, a tranzakciók méretétől, a replika terhelésének és egyéb tényezőktől függ. A kisméretű tranzakciók tipikus adatkésése több tízezer ezredmásodperc, de nincs felső korlát az adatkéséshez. Egy adott másodlagos replikán lévő adat mindig tranzakciós szempontból konzisztens. Azonban egy adott időpontban az adatkésés eltérő lehet a másodlagos replikák esetében. Azokat a munkaterheléseket, amelyeknek azonnal el kell olvasniuk a véglegesített adatokról, az elsődleges replikán kell futniuk.

## <a name="next-steps"></a>Következő lépések

További információ a nagy kapacitású szolgáltatási szintjéről: [nagy kapacitású szolgáltatási szintje](service-tier-hyperscale.md).