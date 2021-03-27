---
title: Áttelepítés SQL Serverról Azure SQL felügyelt példányra
description: Ismerje meg, hogyan telepíthet át egy adatbázist a SQL Serverról az Azure SQL felügyelt példányára.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: ccc6acfd27a1430a4f6a31886c06322c5c09e224
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628373"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>SQL Server példány áttelepítése az Azure SQL felügyelt példányára
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ebből a cikkből megtudhatja, hogyan telepíthet át egy SQL Server 2005-es vagy újabb verziójú példányt az [Azure SQL felügyelt példányára](sql-managed-instance-paas-overview.md). Az önálló adatbázisra vagy rugalmas készletre való áttelepítéssel kapcsolatos információkért tekintse meg a [Migrálás áttekintése: SQL Server SQL Database](../migration-guides/database/sql-server-to-sql-database-overview.md). A más platformokról való áttelepítéssel és az eszközök és beállítások útmutatásával kapcsolatos további információkért lásd: [áttelepítés az Azure SQL](../migration-guides/index.yml)szolgáltatásba.

> [!NOTE]
> Ha gyorsan szeretné elindítani és kipróbálni az Azure SQL felügyelt példányát, érdemes lehet ezen oldal helyett a gyors üzembe [helyezési útmutatót](quickstart-content-reference-guide.md) használni.

Az adatbázis-áttelepítési folyamat magas szinten a következőképpen néz ki:

![Migrálási folyamat](./media/migrate-to-instance-from-sql-server/migration-process.png)

- A [felügyelt SQL-példányok kompatibilitásának felmérése](#assess-sql-managed-instance-compatibility) , ha biztosítania kell, hogy ne legyenek blokkoló problémák az áttelepítés megakadályozása érdekében.
  
  Ez a lépés a [teljesítmény](#create-a-performance-baseline) alapkonfigurációjának létrehozását is magában foglalja a forrás-SQL Server példány erőforrás-felhasználásának meghatározásához. Erre a lépésre akkor van szükség, ha megfelelő méretű felügyelt példányt kíván üzembe helyezni, és azt szeretné ellenőrizni, hogy az áttelepítést követően nem érinti-e a teljesítményt.
- [Válassza az alkalmazás-kapcsolódási beállítások lehetőséget](connect-application-instance.md).
- [Helyezzen üzembe egy optimálisan méretezett felügyelt példányon](#deploy-to-an-optimally-sized-managed-instance) , ahol kiválaszthatja a felügyelt példány technikai jellemzőit (virtuális mag száma, memória mennyisége) és teljesítményszint (üzletileg kritikus, általános célú).
- [Válassza ki az áttelepítési módszert, és telepítse át](#select-a-migration-method-and-migrate) az adatbázisokat offline áttelepítés (natív biztonsági mentés/visszaállítás, adatbázis importálása/exportálás) vagy online áttelepítés (Azure adatáttelepítési szolgáltatás, tranzakciós replikáció) használatával.
- [Figyelje az alkalmazásokat](#monitor-applications) , és győződjön meg arról, hogy a teljesítmény várható.

> [!NOTE]
> Ha önálló adatbázist szeretne áttelepíteni egyetlen adatbázisba vagy rugalmas készletbe, tekintse meg [a SQL Server-adatbázis Áttelepítését Azure SQL Databasere](../database/migrate-to-database-from-sql-server.md)című témakört.

## <a name="assess-sql-managed-instance-compatibility"></a>Felügyelt SQL-példányok kompatibilitásának felmérése

Először határozza meg, hogy az SQL felügyelt példány kompatibilis-e az alkalmazás adatbázis-követelményeivel. Az SQL felügyelt példánya úgy lett kialakítva, hogy a SQL Servert használó meglévő alkalmazások többsége számára könnyen áthelyezhető legyen a lift és a váltás. Előfordulhat azonban, hogy esetenként olyan szolgáltatásokat vagy képességeket igényel, amelyek még nem támogatottak, és a megkerülő megoldás megvalósításának díja túl magas.

A [Data Migration Assistant](/sql/dma/dma-overview) használatával észlelheti a Azure SQL Databaseon az adatbázis-funkciókat érintő lehetséges kompatibilitási problémákat. Ha néhány blokkolt blokkolási probléma van, előfordulhat, hogy fontolóra kell vennie egy másik lehetőséget, például [SQL Server az Azure virtuális gépen](https://azure.microsoft.com/services/virtual-machines/sql-server/). Íme néhány példa:

- Ha közvetlen hozzáférésre van szüksége az operációs rendszerhez vagy a fájlrendszerhez, például a külső gyártótól származó vagy az egyéni ügynököket ugyanarra a virtuális gépre telepíti SQL Server.
- Ha olyan funkciókkal rendelkezik, amelyek még nem támogatottak, például a FileStream/leválasztható, a bázisterület és a több példány tranzakciója.
- Ha mindenképpen a SQL Server adott verziójára van szüksége (például 2012).
- Ha a számítási követelmények sokkal alacsonyabbak, mint a felügyelt példányok ajánlatai (egy virtuális mag, például), és az adatbázis-összevonás nem elfogadható megoldás.

Ha feloldotta az összes azonosított áttelepítési blokkot, és folytatja az SQL felügyelt példányra való áttelepítést, vegye figyelembe, hogy egyes módosítások befolyásolhatják a munkaterhelés teljesítményét:

- A kötelező teljes helyreállítási modell és a rendszeres automatikus biztonsági mentés ütemezése hatással lehet a számítási feladatok teljesítményére vagy a karbantartási/ETL-műveletekre, ha rendszeresen használ egyszerű/tömegesen naplózott modellt, vagy igény szerint állított le biztonsági mentést.
- Különböző kiszolgálói vagy adatbázis-szintű konfigurációk, például nyomkövetési jelzők vagy kompatibilitási szintek.
- A használt új funkciók, például az átlátszó adatbázis-titkosítás (TDE) vagy az automatikus feladatátvételi csoportok befolyásolhatják a CPU-t és az IO-használatot.

A felügyelt SQL-példányok a 99,99%-os rendelkezésre állást a kritikus helyzetekben is garantálják, így a szolgáltatások által okozott terhelés nem tiltható le. További információkért tekintse [meg a SQL Server és az Azure SQL felügyelt példányának különböző teljesítményét okozó kiváltó okokat](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

#### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (memóriára optimalizált táblák)

A SQL Server In-Memory OLTP képességet biztosít, amely lehetővé teszi a memóriára optimalizált táblák, a memóriára optimalizált táblázatok és a natív módon lefordított SQL-modulok használatát a nagy átviteli sebességű és kis késésű tranzakciós feldolgozási követelményekkel rendelkező munkaterhelések futtatásához. 

> [!IMPORTANT]
> In-Memory OLTP csak az Azure SQL felügyelt példányának üzletileg kritikus szintjében támogatott (és a általános célú szinten nem támogatott).

Ha a helyszíni SQL Server memóriára optimalizált táblákat vagy memóriára optimalizált táblázatokat használ, és az Azure SQL felügyelt példányára szeretne áttérni, akkor a következők egyikét kell tennie:

- Válassza ki üzletileg kritikus szintet a cél Azure SQL felügyelt példányához, amely támogatja a In-Memory OLTP, vagy
- Ha az Azure SQL felügyelt példányának általános célú szintjére kíván áttérni, távolítsa el a memóriára optimalizált táblákat, a memóriára optimalizált táblázatos típusokat és a natív módon lefordított SQL-modulokat, amelyek az adatbázis (ok) áttelepítése előtt a memóriára optimalizált objektumokat használják. A következő T-SQL-lekérdezéssel azonosíthatók az összes olyan objektum, amelyet el kell távolítani a általános célúi rétegbe való áttelepítés előtt:

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

A memórián belüli technológiákkal kapcsolatos további információkért lásd: [a teljesítmény optimalizálása a memóriában lévő technológiák használatával Azure SQL Database és az Azure SQL felügyelt példányain](../in-memory-oltp-overview.md) .

### <a name="create-a-performance-baseline"></a>Teljesítmény-alapterv létrehozása

Ha össze kell hasonlítani a számítási feladatok teljesítményét egy felügyelt példányon a SQL Serveron futó eredeti számítási feladattal, létre kell hoznia egy, az összehasonlításhoz használandó alapkonfigurációt.

A teljesítmény alapkonfigurációja olyan paraméterek összessége, mint például az átlagos/maximális CPU-használat, az átlagos/maximális lemez i/o-késés, az átviteli sebesség, a IOPS, az átlagos/maximális oldal várható élettartama és a tempdb átlagos maximális mérete. A Migrálás után hasonló vagy még jobb paramétereket szeretne biztosítani, ezért fontos a paraméterek alapértékének mérése és rögzítése. A rendszerparaméterek mellett ki kell választania a reprezentatív lekérdezéseket vagy a legfontosabb lekérdezéseket a számítási feladatban, és mérnie kell a minimális/átlagos/maximális időtartamot és a CPU-használatot a kiválasztott lekérdezésekhez. Ezek az értékek lehetővé teszik a felügyelt példányon futó munkaterhelések teljesítményének összehasonlítását a forrás SQL Server példányán lévő eredeti értékekre.

A SQL Server-példány méréséhez szükséges paraméterek némelyike a következő:

- [Figyelje a CPU-használatot a SQL Server példányon](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) , és jegyezze fel az átlagos és a maximális CPU-használatot.
- [Figyelje a memória használatát a SQL Server példányon](/sql/relational-databases/performance-monitor/monitor-memory-usage) , és határozza meg a különböző összetevők, például a puffer készlet, a terv gyorsítótár, az erőforráskészlet-készlet, [a memórián belüli OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)stb. által használt memória mennyiségét. Emellett meg kell találnia az oldal átlagos és csúcsérték értékét a memória várható élettartama teljesítményszámláló számára.
- A lemez i/o-használatának figyelése a forrás SQL Server példányon [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) nézet vagy [teljesítményszámlálók](/sql/relational-databases/performance-monitor/monitor-disk-usage)használatával.
- A számítási feladatok és a lekérdezési teljesítmény, illetve a SQL Server példány figyelése a dinamikus felügyeleti nézetek vagy a lekérdezési tárolók vizsgálatával, ha az áttelepítés egy SQL Server 2016 + verzióról történik. Azonosítsa a számítási feladat legfontosabb lekérdezéseinek átlagos időtartamát és CPU-felhasználását, hogy összehasonlítsa őket a felügyelt példányon futó lekérdezésekkel.

> [!Note]
> Ha a számítási feladattal kapcsolatos problémát észlel SQL Server például a CPU-használatot, az állandó memóriát, a tempdb vagy a paraméterezés kapcsolatos problémákat, próbálja meg feloldani azokat a forrás SQL Server példányán az alapterv és az áttelepítés előtt. Az ismert problémák bármely új rendszerbe történő áttelepítése váratlan eredményeket eredményezhet, és érvénytelenítheti a teljesítmény összehasonlítását.

Ennek a tevékenységnek az eredménye a processzor, a memória és az i/o használatának átlagos és maximális értékének dokumentálása a forrásrendszer esetében, valamint a számítási feladat domináns és legkritikusabb lekérdezésének átlagos és maximális időtartama és CPU-használata. Ezeket az értékeket később kell használni a felügyelt példányon a számítási feladatok teljesítményének összehasonlításához a forrás SQL Server-példány számítási feladatainak alapteljesítményével.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Üzembe helyezés az optimálisan méretezett felügyelt példányon

Az SQL felügyelt példánya olyan helyszíni számítási feladatokra van szabva, amelyek a felhőbe való áttérést tervezik. Egy [új vásárlási modellt](../database/service-tiers-vcore.md) vezet be, amely nagyobb rugalmasságot biztosít a számítási feladatok megfelelő szintjének kiválasztásában. A helyszíni világban valószínűleg megszokta ezeket a számítási feladatokat fizikai magok és IO-sávszélesség használatával. A felügyelt példány beszerzési modellje a virtuális magokon vagy a "virtuális mag"-on alapul, és a további tárhely és az IO külön érhető el. A virtuális mag modell a Felhőbeli számítási követelmények megismerésének egyszerű módja, és a helyszíni környezetek használata. Ez az új modell lehetővé teszi, hogy a felhőben jobb méretű legyen a célként megadott környezet. Néhány általános irányelv, amely segíthet kiválasztani a megfelelő szolgáltatási szintet és jellemzőket:

- Az alapkonfiguráció CPU-használata alapján olyan felügyelt példányt építhet ki, amely megfelel a SQL Server használt magok számának, figyelembe véve, hogy a processzor jellemzőit úgy kell méretezni, hogy megfeleljenek a [felügyelt példány telepítési helyéül szolgáló virtuális gépek jellemzőinek](resource-limits.md#hardware-generation-characteristics).
- Az alapkonfiguráció memóriahasználat alapján válassza ki [a megfelelő memóriát tartalmazó szolgáltatási szintet](resource-limits.md#hardware-generation-characteristics). A memória mennyisége nem választható közvetlenül, ezért ki kell választania a felügyelt példányt a megfelelő memóriával rendelkező virtuális mag (például 5,1 GB/virtuális mag a Gen5-ben).
- A fájl alrendszer alapértékének i/o-késése alapján választhat a általános célú (az 5 MS-nál nagyobb késés) és üzletileg kritikus (a 3 MS-nál kisebb késés).
- Az alapteljesítmény alapján a várt i/o-teljesítmény érdekében az adatok vagy a naplófájlok méretét előre le kell foglalni.

Kiválaszthatja a számítási és tárolási erőforrásokat a központi telepítési időszakban, majd később módosíthatja azt anélkül, hogy az alkalmazás leállását bevezesse a [Azure Portal](../database/scale-resources.md)használatával:

![Felügyelt példányok méretezése](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

A VNet-infrastruktúra és a felügyelt példány létrehozásával kapcsolatos további információkért lásd: [felügyelt példány létrehozása](instance-create-quickstart.md).

> [!IMPORTANT]
> Fontos, hogy a cél VNet és alhálózatát a [felügyelt példányok VNet követelményeinek](connectivity-architecture-overview.md#network-requirements)megfelelően tartsa. Minden inkompatibilitás megakadályozhatja, hogy új példányokat hozzon létre, vagy a már létrehozott személyeket használja. További információ a meglévő hálózatok [létrehozásáról](virtual-network-subnet-create-arm-template.md) és [konfigurálásáról](vnet-existing-add-subnet.md) .

## <a name="select-a-migration-method-and-migrate"></a>Áttelepítési módszer kiválasztása és áttelepítése

A felügyelt SQL-példányok olyan felhasználói forgatókönyveket céloznak meg, amelyek a helyszíni vagy az Azure-beli virtuálisgép-adatbázis implementációjában tömeges adatbázis-áttelepítés szükséges Ezek az optimális választás, ha a példányok szintjének és/vagy az adatbázisok közötti funkcionalitást rendszeresen használó alkalmazások hátterének megszüntetéséhez és átváltásához szükséges. Ha ez az Ön forgatókönyve, a teljes példányt áthelyezheti egy megfelelő Azure-környezetbe anélkül, hogy újra kellene terveznie az alkalmazásokat.

Az SQL-példányok áthelyezéséhez körültekintően kell megterveznie a következőket:

- Az összes olyan adatbázis áttelepítése, amelyet közös elhelyezésű kell (amelyek ugyanazon a példányon futnak).
- Az alkalmazástól függő példány-szintű objektumok áttelepítése, beleértve a bejelentkezéseket, a hitelesítő adatokat, az SQL-ügynök feladatait és a kezelőket, valamint a kiszolgálói szintű eseményindítókat.

Az SQL felügyelt példány egy felügyelt szolgáltatás, amely lehetővé teszi, hogy a szokásos DBA-tevékenységek egy részét a beépített platformra delegálja. Ezért előfordulhat, hogy egyes példány-szintű adatokat nem kell áttelepíteni, például a rendszeres biztonsági mentéshez vagy az Always On konfigurációhoz tartozó karbantartási feladatokat, mivel a [magas rendelkezésre állás](../database/high-availability-sla.md) beépített.

Az SQL felügyelt példánya a következő adatbázis-áttelepítési lehetőségeket támogatja (jelenleg ez az egyetlen támogatott áttelepítési módszer):

- Azure Database Migration Service – az áttelepítés közel nulla állásidővel.
- Natív `RESTORE DATABASE FROM URL` – natív biztonsági mentéseket használ SQL Server, és némi állásidőt igényel.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

A [Azure Database Migration Service](../../dms/dms-overview.md) egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy a zökkenőmentes áttelepítések több adatbázisból az Azure-beli adatplatformokra minimális állásidővel. Ez a szolgáltatás egyszerűsíti a meglévő külső felek és SQL Server adatbázisok Azure-ba való áthelyezéséhez szükséges feladatokat. A nyilvános előzetes verzióban elérhető üzembe helyezési lehetőségek közé tartoznak az Azure-beli virtuális gépek Azure SQL Database és SQL Server adatbázisai. A vállalati munkaterhelések esetében a Database Migration Service ajánlott áttelepítési módszer.

Ha SQL Server Integration Servicest (SSIS) használ a helyszíni SQL Servern, Database Migration Service még nem támogatja a SSIS-csomagokat tároló SSIS-katalógus (SSISDB) áttelepítését, de Azure-SSIS Integration Runtime (IR) is kiépíthető a Azure Data Factoryba, amely létrehoz egy új SSISDB egy felügyelt példányban, így újból üzembe helyezheti a csomagokat. Lásd: [Azure-SSIS IR létrehozása a Azure Data Factoryban](../../data-factory/create-azure-ssis-integration-runtime.md).

Ha többet szeretne megtudni erről a forgatókönyvről és a Database Migration Service konfigurációs lépéseiről, tekintse [meg a helyszíni adatbázis áttelepítését felügyelt példányra Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md)című témakört.  

### <a name="native-restore-from-url"></a>Natív VISSZAÁLLÍTÁS URL-címből

Az [Azure Storage](https://azure.microsoft.com/services/storage/)-ban elérhető, SQL Server példányról származó natív biztonsági másolatok (. bak fájlok) visszaállítása az SQL felügyelt példányának egyik fő funkciója, amely lehetővé teszi a gyors és egyszerű offline adatbázisok áttelepítését.

Az alábbi ábra a folyamat magas szintű áttekintését tartalmazza:

![A diagramon SQL Server egy, az Azure Storage-ba irányuló URL-cím és az Azure Storage-ba irányuló URL-cím átadására szolgáló nyíl.](./media/migrate-to-instance-from-sql-server/migration-flow.png)

Az alábbi táblázat a forrás SQL Server a futtatott verziótól függően használható módszerekkel kapcsolatos további információkat tartalmazza:

|Lépés|SQL-motor és-verzió|Biztonsági mentési/visszaállítási módszer|
|---|---|---|
|Biztonsági mentés készítése az Azure Storage-ba|2012 SP1 előtt CU2|A. bak fájl feltöltése közvetlenül az Azure Storage-ba|
||2012 SP1 CU2 – 2016|A közvetlen biztonsági mentés [a hitelesítő adatok](/sql/t-sql/statements/restore-statements-transact-sql) szintaxisával elavult|
||2016 és újabb verziók|Közvetlen biztonsági mentés [sas-hitelesítő adat](/sql/relational-databases/backup-restore/sql-server-backup-to-url) használatával|
|Visszaállítás az Azure Storage-ból egy felügyelt példányra|[Visszaállítás az URL-címről SAS-HITELESÍTő ADATOKkal](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Ha [transzparens adattitkosítás](../database/transparent-data-encryption-tde-overview.md) által védett adatbázist felügyelt példányra telepít át natív visszaállítási lehetőséggel, akkor a helyszíni vagy az Azure-beli virtuális SQL Server gép megfelelő tanúsítványát át kell telepíteni az adatbázis-visszaállítás előtt. A részletes lépésekért lásd: [TDE-tanúsítvány áttelepítése felügyelt példányra](tde-certificate-migrate.md).
> - A rendszeradatbázisok visszaállítása nem támogatott. Ha a példány-szintű objektumokat (Master vagy msdb adatbázisokban tárolja) szeretné áttelepíteni, javasoljuk, hogy parancsfájlokat futtasson, és T-SQL-parancsfájlokat futtasson a célhelyen.

Az adatbázis biztonsági másolatának egy felügyelt példányra SAS-hitelesítő adatokkal történő visszaállítását bemutató rövid útmutató a [biztonsági másolatból egy felügyelt példányra történő visszaállítással](restore-sample-database-quickstart.md)foglalkozó témakörben olvasható.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Alkalmazások figyelése

Miután befejezte az áttelepítést egy felügyelt példányra, nyomon követheti a számítási feladatok viselkedését és teljesítményét. Ez a folyamat a következő tevékenységeket tartalmazza:

- [Hasonlítsa össze a felügyelt példányon futó munkaterhelések teljesítményét](#compare-performance-with-the-baseline) a [forrás SQL Server példányán létrehozott teljesítmény-alapterv alapján](#create-a-performance-baseline).
- [A számítási feladatok teljesítményének folyamatos figyelésével](#monitor-performance) azonosíthatja a lehetséges problémákat és a fejlesztést.

### <a name="compare-performance-with-the-baseline"></a>A teljesítmény összehasonlítása az alaptervvel

A sikeres áttelepítés után elvégzendő első tevékenység a számítási feladat teljesítményének összevetése a számítási feladatok teljesítményével. Ennek a tevékenységnek a célja annak ellenőrzése, hogy a felügyelt példány munkaterhelés-teljesítménye megfelel-e az igényeinek.

Az adatbázisok felügyelt példányra történő áttelepítése az esetek többségében megőrzi az adatbázis-beállításokat és az eredeti kompatibilitási szintet. Ha lehetséges, az eredeti beállítások megmaradnak, hogy csökkentse a teljesítmény romlását a forrás SQL Server-példányhoz képest. Ha a felhasználói adatbázis kompatibilitási szintje 100 vagy magasabb volt az áttelepítés előtt, akkor az áttelepítés után is változatlan marad. Ha a felhasználói adatbázis kompatibilitási szintje 90 volt az áttelepítés előtt, a frissített adatbázisban a kompatibilitási szint a 100 értékre van állítva, amely a felügyelt példányok legalacsonyabb támogatott kompatibilitási szintje. A rendszeradatbázisok kompatibilitási szintje 140. Mivel a felügyelt példányra való Migrálás valójában a SQL Server adatbázismotor legújabb verziójára van áttelepítve, érdemes figyelembe vennie, hogy újra kell tesztelni a számítási feladatok teljesítményét, hogy elkerülje a problémák bizonyos meglepő teljesítményét.

Előfeltételként ellenőrizze, hogy végrehajtotta-e a következő tevékenységeket:

- A felügyelt példány beállításainak igazítása a forrás SQL Server példány beállításaival a különböző példányok, adatbázisok, tempdb-beállítások és konfigurációk vizsgálatával. Győződjön meg arról, hogy nem módosította a beállításokat, például a kompatibilitási szinteket vagy a titkosítást az első teljesítmény-összehasonlítás futtatása előtt, vagy fogadja el azt a kockázatot, hogy az Ön által engedélyezett új funkciók némelyike bizonyos lekérdezésekre is hatással lehet. Az áttelepítési kockázatok csökkentése érdekében csak a teljesítmény figyelése után módosítsa az adatbázis kompatibilitási szintjét.
- Az [általános célúre vonatkozó ajánlott eljárások](https://techcommunity.microsoft.com)bevezetése, például a fájlok méretének előzetes lefoglalása a jobb teljesítmény érdekében.
- Ismerje meg azokat a [kulcsfontosságú környezeti különbségeket, amelyek a felügyelt példányok és a SQL Server közötti teljesítménybeli különbségeket okozhatják](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/), és azonosíthatja azokat a kockázatokat, amelyek hatással lehetnek a teljesítményre.
- Győződjön meg arról, hogy a felügyelt példányon megtartja az engedélyezett lekérdezési tárolót és az automatikus finomhangolást. Ezek a funkciók lehetővé teszik a számítási feladatok teljesítményének mérését és a lehetséges teljesítménybeli problémák automatikus kijavítását. Ismerje meg, hogyan használhatja a Query Store-t optimális eszközként a munkaterhelés teljesítményének az adatbázis-kompatibilitási szint változása előtt és után történő beolvasásához, ahogy az a [teljesítmény stabilitásának fenntartása a frissítés során újabb SQL Server verzióra](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Miután előkészítette a környezetet, amely a lehető legnagyobb mértékben összehasonlítható a helyszíni környezettel, megkezdheti a számítási feladatok futtatását és a teljesítmény mérését. A mérési folyamatnak meg kell egyeznie a megadott paraméterekkel, amelyeket a számítási feladatoknak a [forrás SQL Server-példányon való kiindulási teljesítményének létrehozásakor](#create-a-performance-baseline)mért.
Ennek eredményeképpen össze kell hasonlítani a teljesítménnyel kapcsolatos paramétereket az alapkonfigurációval, és meg kell határoznia a kritikus különbségeket.

> [!NOTE]
> Sok esetben nem fog tudni pontosan egyező teljesítményt kapni a felügyelt példányon, és SQL Server. Az Azure SQL felügyelt példánya egy SQL Server adatbázismotor, de a felügyelt példányok infrastruktúrája és magas rendelkezésre állási konfigurációja bizonyos eltéréseket eredményezhet. Előfordulhat, hogy néhány lekérdezés gyorsabb, míg mások lassabbak lehetnek. Az összehasonlítás célja annak ellenőrzése, hogy a felügyelt példány terhelési teljesítménye megegyezik-e SQL Server teljesítményével (átlagosan), és azonosítsa az eredeti teljesítménnyel nem egyező teljesítménnyel rendelkező kritikus lekérdezéseket.

A teljesítmény-összehasonlítás eredménye a következőket teheti:

- A felügyelt példányon a számítási feladatok teljesítményének igazítása vagy jobb, mint SQL Server. Ebben az esetben sikeresen megerősítette, hogy az áttelepítés sikeres volt.
- A teljesítménnyel kapcsolatos paraméterek többsége és a számítási feladatok teljesítménye megfelelően működik, és bizonyos kivételek a csökkentett teljesítmény miatt. Ebben az esetben meg kell határoznia a különbségeket és azok fontosságát. Ha van néhány fontos lekérdezés a csökkentett teljesítményű teljesítménnyel kapcsolatban, vizsgálja meg, hogy az alapul szolgáló SQL-csomagok módosultak-e, vagy hogy a lekérdezések bizonyos erőforrás-korlátokat mutatnak-e. Ebben az esetben előfordulhat, hogy a kritikus lekérdezésekre (például a kompatibilitási szintre, az örökölt kardinális kalkulátorra) vonatkozó mutatókat közvetlenül vagy a tervezési útmutatók használatával kell alkalmazni, vagy olyan statisztikákat és indexeket kell létrehoznia, amelyek hatással lehetnek a csomagokra.
- A lekérdezések többsége egy felügyelt példányon lassabban szerepel a forrás SQL Server-példányhoz képest. Ebben az esetben próbálja meg megállapítani a különbség kiváltó okait, például az [egyes erőforrások korlátozásait](resource-limits.md#service-tier-characteristics) , például az IO-korlátokat, a memória korlátját, a példányok naplózási sebességének korlátját stb. Ha nincs olyan erőforrás-korlát, amely a különbséget okozhatja, próbálja meg módosítani az adatbázis kompatibilitási szintjét, vagy módosítsa az adatbázis beállításait, például a örökölt kardinális becslést, és indítsa újra a tesztet. Tekintse át a felügyelt példány vagy a lekérdezési tár nézetei által megadott javaslatokat a romlott teljesítményű lekérdezések azonosításához.

> [!IMPORTANT]
> Az Azure SQL felügyelt példánya beépített automatikus csomag-javítási funkcióval rendelkezik, amely alapértelmezés szerint engedélyezve van. Ez a funkció biztosítja, hogy a beillesztés során kitöltött lekérdezések a jövőben ne legyenek csökkenhetve. Győződjön meg arról, hogy ez a funkció engedélyezve van, és hogy az új beállítások módosítása előtt a régi beállításokon túl hosszú ideig hajtotta végre a munkaterhelést, hogy a felügyelt példány tudjon többet megtudni az alapkonfigurációról és a csomagokról.

Végezze el a paraméterek módosítását, vagy frissítse a szolgáltatási szinteket az optimális konfigurációhoz, amíg meg nem éri az igényeinek megfelelő számítási feladatok teljesítményét.

### <a name="monitor-performance"></a>Teljesítmény figyelése

A felügyelt SQL-példányok számos speciális eszközt biztosítanak a figyeléshez és a hibaelhárításhoz, és ezeket érdemes használni a példány teljesítményének figyeléséhez. A figyelni kívánt paraméterek némelyike a következő:

- A példány CPU-használata annak megállapítására, hogy a kiépített virtuális mag száma megfelelő-e a számítási feladathoz.
- Lap – várható élettartam a felügyelt példányon annak megállapítása érdekében, hogy [szüksége van-e további memóriára](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- A statisztikához hasonló `INSTANCE_LOG_GOVERNOR` vagy `PAGEIOLATCH` megtudható, hogy van-e a tárhely i/o-problémája, különösen a általános célú szinten, ahol szükség lehet a fájlok előzetes lefoglalására, hogy jobb IO-teljesítményt kapjon.

## <a name="leverage-advanced-paas-features"></a>Speciális Pásti funkciók kihasználása

Ha teljes körűen felügyelt platformot használ, és ellenőrizte, hogy a számítási feladatok teljesítménye megfelel a SQL Server munkaterhelés teljesítményének, használja a szolgáltatás részeként automatikusan biztosított előnyöket.

Még ha nem végez módosításokat a felügyelt példányon az áttelepítés során, nagy eséllyel előfordulhat, hogy bizonyos új funkciók bekapcsolására akkor van szükség, amikor a példányát használja a legújabb adatbázismotor-tökéletesítések kihasználásához. Néhány módosítás csak az [adatbázis-kompatibilitási szint módosítása](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)után engedélyezett.

Például nem kell biztonsági másolatokat létrehoznia a felügyelt példányon – a szolgáltatás automatikusan készít biztonsági másolatokat. Többé nem kell aggódnia a biztonsági mentések ütemezésével, megkezdésével és kezelésével kapcsolatban. Az SQL felügyelt példánya lehetővé teszi, hogy az [időponthoz tartozó helyreállítás (PITR)](../database/recovery-using-backups.md#point-in-time-restore)használatával bármely időpontra vissza tudja állítani a megőrzési időtartamot. Emellett nem kell aggódnia a magas rendelkezésre állás beállításával kapcsolatban, mivel a [magas rendelkezésre állás](../database/high-availability-sla.md) beépített.

A biztonság megerősítése érdekében érdemes lehet [Azure Active Directory hitelesítést](../database/security-overview.md), [naplózást](auditing-configure.md), [veszélyforrások észlelését](../database/azure-defender-for-sql.md), [sor szintű biztonságot](/sql/relational-databases/security/row-level-security)és [dinamikus adatmaszkolást](/sql/relational-databases/security/dynamic-data-masking)használni.

A speciális felügyeleti és biztonsági funkciók mellett a felügyelt példányok olyan speciális eszközöket biztosítanak, amelyek segítségével [figyelheti és beállíthatja a számítási feladatokat](../database/monitor-tune-overview.md). [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) lehetővé teszi a felügyelt példányok nagy készletének figyelését és a nagy számú példány és adatbázis figyelését. A felügyelt példányok [automatikus finomhangolása](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) folyamatosan FIGYELI az SQL-terv végrehajtási statisztikáinak teljesítményét, és automatikusan kijavítja az azonosított teljesítménnyel kapcsolatos problémákat.

## <a name="next-steps"></a>Következő lépések

- További információ az Azure SQL felügyelt példányáról: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- A biztonsági másolatból történő visszaállítást tartalmazó oktatóanyagért tekintse meg [a felügyelt példány létrehozása](instance-create-quickstart.md)című témakört.
- A Database Migration Service használatával történő áttelepítést bemutató oktatóanyagért lásd: [a helyszíni adatbázis áttelepítése az Azure SQL felügyelt példányára Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md).