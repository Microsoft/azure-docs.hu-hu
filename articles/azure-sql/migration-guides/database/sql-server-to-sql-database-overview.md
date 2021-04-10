---
title: 'SQL Server a Azure SQL Databasehoz: áttelepítési áttekintés'
description: Ismerje meg a SQL Server-adatbázisok Azure SQL Databaseba való átadásához rendelkezésre álló eszközöket és lehetőségeket.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f515725ea0f306546039b92d953254a093b15b8b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065174"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Áttelepítési Áttekintés: SQL Server Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Ismerje meg a SQL Server-adatbázisok Azure SQL Databasere való áttelepítésének lehetőségeit és szempontjait. 

A helyszínen vagy a rendszeren futó SQL Server-adatbázisokat áttelepítheti: 

- SQL Server az Azure Virtual Machines.  
- Amazon Web Services (AWS) rugalmas számítási felhő (EC2).
- AWS-hez kapcsolódó adatbázis-szolgáltatás (RDS).
- Számítási motor a Google Cloud Platformban (GCP).  
- Felhőalapú SQL SQL Server a GCP-ben. 

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Áttekintés

[Azure SQL Database](../../database/sql-database-paas-overview.md) a teljes körűen felügyelt platformként (Pásti) megkövetelt SQL Server munkaterhelések esetében ajánlott célként megadott beállítás. SQL Database kezeli a legtöbb adatbázis-kezelési funkciót. Beépített magas rendelkezésre állású, intelligens lekérdezés-feldolgozási, skálázhatósági és teljesítménybeli képességekkel is rendelkezik, amelyek számos különböző típusú alkalmazáshoz használhatók. 

SQL Database rugalmasságot biztosít több [üzembe helyezési modellel](../../database/sql-database-paas-overview.md#deployment-models) és [szolgáltatási szinttel](../../database/service-tiers-vcore.md#service-tiers) , amelyek különböző típusú alkalmazásokhoz vagy munkaterhelésekhez használhatók.

A SQL Databasere való Migrálás egyik legfőbb előnye, hogy az alkalmazást a Pásti-funkciók használatával modernizálhatja. Ezután megtörölheti a példány szintjén hatókörbe tartozó technikai összetevők függőségét, például az SQL-ügynök feladatait.

A költségeket a SQL Server [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) használatával is megtakaríthatja, ha az SQL Server helyszíni licenceit Azure SQL Databasere telepíti át. Ez a lehetőség akkor érhető el, ha a [virtuális mag-alapú vásárlási modellt](../../database/service-tiers-vcore.md)választja.

## <a name="considerations"></a>Megfontolandó szempontok 

Az áttelepítési lehetőségek kiértékelése során megfontolandó legfontosabb tényezők a következők: 

- Kiszolgálók és adatbázisok száma
- Adatbázisok mérete
- Elfogadható üzleti állásidő az áttelepítési folyamat során 

Az útmutatóban felsorolt áttelepítési lehetőségek figyelembe veszik ezeket a tényezőket. A Azure SQL Databaseba való logikai adatáttelepítés esetén az áttelepítési idő az adatbázis objektumainak számától és az adatbázis méretétől függ. 

Az eszközök különböző számítási feladatokhoz és felhasználói beállításokhoz érhetők el. Egyes eszközök segítségével egyetlen adatbázis gyors áttelepítését végezheti el egy felhasználói felületen alapuló eszköz használatával. A többi eszköz képes automatizálni több adatbázis áttelepítését a nagy léptékű áttelepítések kezelésére. 

## <a name="choose-an-appropriate-target"></a>Válasszon ki egy megfelelő célt

Tekintse át az általános irányelveket, amelyekkel kiválaszthatja a Azure SQL Database megfelelő telepítési modelljét és szolgáltatási szintjét. Az üzembe helyezés során kiválaszthatja a számítási és tárolási erőforrásokat, majd [később is megváltoztathatja őket a Azure Portal használatával](../../database/scale-resources.md) anélkül, hogy állásidőt kellene használni az alkalmazáshoz.

**Üzembe helyezési modellek**: megismerheti az alkalmazás számítási feladatait és a használati mintát, hogy egyetlen adatbázis vagy egy rugalmas készlet között döntsön. 

- Az [önálló adatbázisok](../../database/single-database-overview.md) egy teljes körűen felügyelt adatbázist jelentenek, amely a legtöbb modern Felhőbeli alkalmazáshoz és szolgáltatáshoz alkalmas.
- A [rugalmas készlet](../../database/elastic-pool-overview.md) olyan önálló adatbázisok gyűjteménye, amelyekben az erőforrások, például a processzor vagy a memória közös készlete található. Alkalmas arra, hogy a készletben lévő adatbázisokat kiszámítható használati mintákkal kombinálják, amelyek hatékonyan megoszthatják ugyanazt az erőforrás-készletet.

**Vásárlási modellek**: válasszon a virtuális mag, az adatbázis-tranzakciós egység (DTU) vagy a kiszolgáló nélküli beszerzési modellek között. 

- A [virtuális mag modell](../../database/service-tiers-vcore.md) segítségével kiválaszthatja Azure SQL Database virtuális mag számát, így a legegyszerűbb választás a helyszíni SQL Serverból való fordítás során. Ez az egyetlen lehetőség, amely támogatja a licencek költségeinek a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)való mentését. 
- A [DTU-modell](../../database/service-tiers-dtu.md) elvonta a mögöttes számítási, memória-és I/O-erőforrásokat a kevert DTU biztosításához. 
- A [kiszolgáló nélküli modell](../../database/serverless-tier-overview.md) olyan számítási feladatokhoz használható, amelyek automatikus igény szerinti skálázást igényelnek a használati díjak másodpercenkénti száma alapján. A kiszolgáló nélküli számítási rétegek automatikusan szüneteltetik az adatbázisokat az inaktív időszakokban (ahol csak a tárterület számlázása történik). Automatikusan folytatja az adatbázisokat, amikor a tevékenység visszaadja. 

**Szolgáltatási szintek**: választhat három szolgáltatási szintet, amelyek különböző típusú alkalmazásokhoz lettek kialakítva.

- A [általános célú/standard szolgáltatási szint](../../database/service-tier-general-purpose.md) kiegyensúlyozott, költséghatékony megoldást kínál az alkalmazások középső és alsó szintjein való továbbításához. A redundancia a tárolási rétegben a hibákból való helyreállításhoz készült. Ez a legtöbb adatbázis-számítási feladat számára készült. 
- A [üzletileg kritikus/prémium szolgáltatási szint](../../database/service-tier-business-critical.md) olyan magas szintű alkalmazások számára készült, amelyek nagy tranzakciós sebességet, kis késleltetésű I/O-t és magas fokú rugalmasságot igényelnek. A másodlagos replikák elérhetők a feladatátvételhez és az olvasási feladatok kiszervezéséhez.
- Az [nagy kapacitású szolgáltatási szintje](../../database/service-tier-hyperscale.md) olyan adatbázisokhoz használható, amelyek növekvő adatmennyiséggel rendelkeznek, és az adatbázis méretében automatikusan fel kell méretezni a 100 TB-ot. Nagyon nagy méretű adatbázisokhoz készült. 

> [!IMPORTANT]
> A [tranzakciós napló arányának szabályozása](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) Azure SQL Database a nagy mennyiségű betöltési arány korlátozása érdekében. Ilyen esetben előfordulhat, hogy az áttelepítés során méreteznie kell a célként megadott adatbázis-erőforrásokat (virtuális mag vagy DTU) a CPU vagy az átviteli sebesség megkönnyítése érdekében. Válassza ki a megfelelő méretű céladatbázis-adatbázist, de szükség esetén tervezze meg az erőforrások méretezését az áttelepítéshez. 


### <a name="sql-server-vm-alternative"></a>SQL Server VM alternatív megoldás

Előfordulhat, hogy az Ön vállalata olyan követelményeket támaszt, amelyek az [Azure-Virtual Machines SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) megfelelőbb célt tesznek lehetővé, mint Azure SQL Database. 

Ha a következő feltételek egyike vonatkozik a vállalatára, érdemes lehet áthelyezni egy SQL Server virtuális gépre (VM): 

- Közvetlen hozzáférésre van szüksége az operációs rendszerhez vagy a fájlrendszerhez, például a harmadik féltől származó vagy az egyéni ügynökök telepítéséhez a SQL Server használatával azonos virtuális gépen. 
- A továbbra is olyan funkciókkal rendelkezik, amelyek még nem támogatottak, például a FileStream/leválasztható, a bázisterület és a több példány tranzakciója. 
- A (z) SQL Server adott verziójában kell maradni (például: 2012). 
- A számítási követelmények sokkal alacsonyabbak, mint a felügyelt példányok (például egy virtuális mag), és az adatbázis-összevonás nem elfogadható megoldás. 


## <a name="migration-tools"></a>Migrálási eszközök 

A következő áttelepítési eszközöket javasoljuk: 

|Technológia | Leírás|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Ez az Azure-szolgáltatás segít felderíteni és felmérni az SQL-adatkészletet a VMware-en. Az Azure SQL üzembe helyezési javaslatait, a cél méretezését és a havi becsléseket biztosítja. | 
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb)|A Microsoft által készített asztali eszköz zökkenőmentesen értékeli a SQL Server és az Egyadatbázisos áttelepítést Azure SQL Database (a séma és az adatok esetében egyaránt). </br></br>Az eszközt telepítheti a helyszíni kiszolgálóra vagy a helyi gépre, amely a forrás-adatbázisokhoz kapcsolódik. Az áttelepítési folyamat a forrás-és a cél-adatbázisok objektumai közötti logikai adatáthelyezés.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)|Ez az Azure-szolgáltatás áttelepítheti SQL Server adatbázisait, hogy Azure SQL Database a Azure Portal vagy automatikusan a PowerShellen keresztül. Database Migration Service a kiépítés során ki kell választania egy előnyben részesített Azure-beli virtuális hálózatot, hogy biztosítsa a forrás-SQL Server adatbázisokhoz való kapcsolódást. Egyetlen adatbázist vagy nagy léptékű áttelepíthet. |
| | |


Az alábbi táblázat az alternatív áttelepítési eszközöket sorolja fel: 

|Technológia |Leírás  |
|---------|---------|
|[Tranzakciós replikáció](../../database/replication-to-sql-database.md)|Adatok replikálása a forrás SQL Server az adatbázis tábláiból Azure SQL Database egy kiadói előfizetői típus áttelepítési lehetőségének megadásával a tranzakciós konzisztencia fenntartása mellett. A növekményes adatváltozásokat a közzétevők számára elérhetővé kell tennie az előfizetők számára.|
|[Importálási exportálási szolgáltatás/BACPAC](../../database/database-import.md)|A [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) egy. BACPAC kiterjesztésű Windows-fájl, amely egy adatbázis sémáját és az adatkészletet ágyazza be. A BACPAC-t használhatja a SQL Server forrásból származó adatok exportálására és az adatok Azure SQL Databaseba való importálására is. Egy BACPAC-fájl importálható egy új SQL-adatbázisba a Azure Portal keresztül. </br></br> A nagyméretű adatbázisok méretével vagy nagy számú adatbázissal való méretezés és teljesítmény érdekében érdemes a [SqlPackage](../../database/database-import.md#using-sqlpackage) parancssori eszközt használni az adatbázisok exportálásához és importálásához.|
|[Tömeges másolás](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|A [tömeges másolási program (BCP) eszköz](/sql/tools/bcp-utility) SQL Server egy példányának adatait másolja egy adatfájlba. Az eszköz használatával exportálja az adatait a forrásból, és importálja az adatfájlt a cél SQL-adatbázisba. </br></br> A nagy sebességű tömeges másolási műveletek esetében az adatok Azure SQL Databaseba való áthelyezéséhez használhatja az [intelligens tömeges másolási eszközt](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) , amellyel maximalizálhatja az átviteli sebességet a párhuzamos másolási feladatok előnyeinek kihasználásával.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|A [másolási tevékenység](../../../data-factory/copy-activity-overview.md) a Azure Data Factory áttelepíti a forrás SQL Server adatbázisok adatait a Azure SQL Databasere beépített összekötők és egy [integrációs](../../../data-factory/concepts-integration-runtime.md)modul használatával.</br> </br> A Data Factory számos [összekötőt](../../../data-factory/connector-overview.md) támogat az adatok SQL Server forrásokból Azure SQL Databaseba való áthelyezéséhez.|
|[SQL-adatszinkronizálás](../../database/sql-data-sync-data-sql-server-sql-database.md)|A SQL-adatszinkronizálás Azure SQL Database épülő szolgáltatás, amely lehetővé teszi, hogy a kijelölt adatokat a helyszínen és a felhőben egyaránt egyszerre több adatbázis között szinkronizálja.</br>Az adatszinkronizálás olyan esetekben hasznos, amikor a Azure SQL Database vagy SQL Server több adatbázisában frissíteni kell az információkat.|
| | |

## <a name="compare-migration-options"></a>Áttelepítési lehetőségek összehasonlítása

Az áttelepítési lehetőségek összehasonlításával kiválaszthatja az üzleti igényeinek megfelelő elérési utat. 

A következő táblázat összehasonlítja a javasolt áttelepítési lehetőségeket: 

|Áttelepítési lehetőség  |A következő esetekben használja  |Megfontolandó szempontok  |
|---------|---------|---------|
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb) | – Önálló adatbázisok migrálása (séma és az adatbázis is).  </br> – Az adatáttelepítési folyamat során az állásidőt is kielégíti. </br> </br> Támogatott források: </br> -SQL Server (2005 – 2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM | – Az áttelepítési tevékenység elvégzi az adatáthelyezést az adatbázis-objektumok között (a forrástól a cél felé), ezért azt javasoljuk, hogy az adatátvitelt a csúcsidőben. </br> -Data Migration Assistant a Migrálás állapotát egy adatbázis-objektumon jelenti, beleértve az áttelepített sorok számát is.  </br> – A nagyméretű áttelepítésekhez (adatbázisok száma vagy adatbázis mérete) használja a Azure Database Migration Service.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)| – Önálló adatbázisok vagy nagy léptékű Migrálás. </br> – Az áttelepítési folyamat során az állásidőt is kielégíti. </br> </br> Támogatott források: </br> -SQL Server (2005 – 2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM | – A nagy léptékű Migrálás automatizálható [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md)használatával. </br> – Az áttelepítés befejezésének ideje az adatbázis méretétől és az adatbázis objektumainak számától függ. </br> – A forrásadatbázis csak olvashatóként van beállítva. |
| | | |

A következő táblázat összehasonlítja az alternatív áttelepítési lehetőségeket: 

|Metódus vagy technológia |A következő esetekben használja    |Megfontolandó szempontok  |
|---------|---------|---------|
|[Tranzakciós replikáció](../../database/replication-to-sql-database.md)| – Migrálás a forrás-adatbázis tábláiból származó módosítások folyamatos közzétételével SQL Database táblák megcélzásához. </br> – Teljes vagy részleges adatbázis-áttelepítést végez a kiválasztott táblákból (egy adatbázis részhalmaza).  </br> </br> Támogatott források: </br> - [SQL Server (2016 – 2019) néhány korlátozással](/sql/relational-databases/replication/replication-backward-compatibility) </br> – AWS EC2  </br> -GCP számítási SQL Server VM  | – A telepítő viszonylag összetett a többi áttelepítési lehetőséghez képest.   </br> – Folyamatos replikálási lehetőséget biztosít az adatáttelepítéshez (az adatbázisok offline állapotba helyezése nélkül).  </br> – A tranzakciós replikáció korlátozásokat tartalmaz, amikor a közzétevőt a forrás SQL Server példányán állítja be. További információért lásd [a közzétételi objektumok korlátozásait](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) . </br>– Lehetséges a [replikálási tevékenység figyelése](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Importálási exportálási szolgáltatás/BACPAC](../../database/database-import.md)| – Az üzletági alkalmazások különböző adatbázisainak migrálása. </br>-Kisebb adatbázisokhoz is használható.  </br>  – Nincs szükség külön áttelepítési szolgáltatásra vagy eszközre. </br> </br> Támogatott források: </br> -SQL Server (2005 – 2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM  |  – Leállást igényel, mert az adatforrást exportálni kell, és a célhelyen kell importálni.   </br> – Az exportálásban vagy importálásban használt fájlformátumoknak és adattípusoknak konzisztensnek kell lenniük a táblázat sémái között, hogy elkerülhető legyen a csonkítás vagy adattípus-eltérési hibák.  </br> – Az adatbázisok nagy mennyiségű objektummal való exportálásának ideje jelentősen magasabb lehet.       |
|[Tömeges másolás](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Teljes vagy részleges adatáttelepítést hajt végre. </br> -Az állásidőt is képes kezelni. </br> </br> Támogatott források: </br> -SQL Server (2005 – 2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM   | – Az adatok forrásból való exportálásához és a célhelyre való importáláshoz szükséges állásidő. </br> – Az exportálásban vagy importálásban használt fájlformátumoknak és adattípusoknak konzisztensnek kell lenniük a táblázat sémái között. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| – Adatok migrálása és/vagy átalakítása forrás SQL Server adatbázisokból. </br> – Az adatok több adatforrásból való egyesítése Azure SQL Database általában az üzleti intelligencia (BI) munkaterhelések számára.  |  -Adatáthelyezési folyamatokat kell létrehoznia Data Factoryban az adatok forrásról célhelyre való áthelyezéséhez.   </br> - A [költségeket](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) fontos figyelembe venni, és olyan tényezőkön alapulnak, mint például a folyamat-eseményindítók, a tevékenység-futtatások és az adatáthelyezés időtartama. |
|[SQL-adatszinkronizálás](../../database/sql-data-sync-data-sql-server-sql-database.md)| – Az adatokat a forrás-és a cél-adatbázisok között szinkronizálja.</br> -Alkalmas a folyamatos szinkronizálás futtatására a Azure SQL Database és a helyszíni SQL Server között kétirányú folyamatokban. | -Azure SQL Database a központi adatbázisnak kell lennie a helyszíni SQL Server adatbázissal való szinkronizáláshoz.</br> – A tranzakciós replikációhoz képest SQL-adatszinkronizálás támogatja a kétirányú adatszinkronizálást a helyszíni és a Azure SQL Database között. </br> – A munkaterheléstől függően nagyobb teljesítményre lehet hatással.|
| | | |

## <a name="feature-interoperability"></a>Funkciók együttműködési képessége 

A más SQL Server funkciókra támaszkodó munkaterhelések áttelepítésekor több szempontot is figyelembe kell venni.

### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Telepítse át SQL Server Integration Services (SSIS) csomagokat az Azure-ba a csomagok a [Azure Data Factory](../../../data-factory/introduction.md)Azure-SSIS futtatókörnyezetbe való újbóli üzembe helyezésével. Azure Data Factory [támogatja a SSIS-csomagok áttelepítését](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) azáltal, hogy a SSIS-csomagok Azure-ban való futtatására készült futtatókörnyezetet biztosít. Azt is megteheti, hogy átírja a SSIS ETL (kinyerési, átalakítási, betöltési) logikát Azure Data Factory az [adatfolyamatok](../../../data-factory/concepts-data-flow-overview.md)használatával.


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
SQL Server Reporting Services-(SSRS-) jelentések áttelepíthetők a többoldalas jelentésekre Power BIokban. A jelentések előkészítését és áttelepítését az [RDL-áttelepítési eszköz](https://github.com/microsoft/RdlMigration) segítségével végezheti el. A Microsoft kifejlesztette ezt az eszközt az Report Definition Language (RDL-) jelentések SSRS-kiszolgálókról Power BIba való átmigrálása érdekében. A GitHubon elérhető, és a migrálási forgatókönyv összes lépését bemutatja. 

### <a name="high-availability"></a>Magas rendelkezésre állás
A SQL Server magas rendelkezésre állású szolgáltatásainak manuális beállítása, például a feladatátvételi fürt példányai és always on rendelkezésre állási csoportok elavulttá válnak a cél SQL-adatbázison. A magas rendelkezésre állású architektúra már a [általános célú (standard rendelkezésre állási modell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) és a [üzletileg kritikus (prémium rendelkezésre állási modell)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) szolgáltatásra is épül, Azure SQL Database. A üzletileg kritikus/prémium szolgáltatási szint olyan olvasási felskálázást is biztosít, amely lehetővé teszi, hogy csak olvasási célokra lehessen csatlakozni az egyik másodlagos csomóponthoz. 

A Azure SQL Database részét képező, magas rendelkezésre állású architektúrán túl az [automatikus feladatátvételi csoportok](../../database/auto-failover-group-overview.md) funkció lehetővé teszi a felügyelt példányban lévő adatbázisok replikálásának és feladatátvételének kezelését egy másik régióba. 

### <a name="sql-agent-jobs"></a>SQL-ügynök feladatai
Az SQL Agent-feladatok közvetlenül nem támogatottak Azure SQL Databaseban, és a [rugalmas adatbázis-feladatokhoz (előzetes verzió)](../../database/job-automation-overview.md)kell őket telepíteni.

### <a name="logins-and-groups"></a>Bejelentkezések és csoportok
Helyezze át az SQL-bejelentkezéseket a SQL Server forrásból a Azure SQL Database kapcsolat nélküli módban lévő Database Migration Service használatával. Az áttelepítési varázsló **kiválasztott bejelentkezések** paneljének használatával áttelepítheti a bejelentkezéseket a cél SQL-adatbázisba. 

A Windows-felhasználókat és-csoportokat a Database Migration Service keresztül is áttelepítheti a Database Migration Service **konfiguráció** lapján található megfelelő váltógomb engedélyezésével. 

Azt is megteheti, hogy a Microsoft adatáttelepítés-építészek által tervezett [PowerShell-segédprogramot](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) használja. A segédprogram a PowerShell használatával hoz létre egy Transact-SQL (T-SQL) parancsfájlt a bejelentkezések újbóli létrehozásához, majd kiválasztja az adatbázis felhasználóit a forrásból a célhelyre. 

A PowerShell-segédprogram automatikusan leképezi a Windows Server Active Directory fiókokat Azure Active Directory (Azure AD-) fiókokba, és minden bejelentkezéshez UPN-keresést végezhet a forrás Active Directory példányán. A segédprogram az egyéni kiszolgáló-és adatbázis-szerepköröket, valamint a szerepkör tagságát és a felhasználói engedélyeket. A foglalt adatbázisok még nem támogatottak, és csak a lehetséges SQL Server engedélyek egy részhalmaza van parancsfájlban. 

### <a name="system-databases"></a>Rendszeradatbázisok
Azure SQL Database esetében az egyetlen alkalmazható rendszeradatbázis a [fő](/sql/relational-databases/databases/master-database) -és tempdb. További információ: [tempdb in Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="advanced-features"></a>Speciális funkciók 

Ügyeljen arra, hogy kihasználhassa a SQL Database fejlett felhőalapú funkcióit. Például nem kell aggódnia a biztonsági másolatok kezelésével kapcsolatban, mert a szolgáltatás elvégzi Önt. [A megőrzési időtartamon belül bármely időpontra](../../database/recovery-using-backups.md#point-in-time-restore)visszaállíthatja a szolgáltatást. 

A biztonság megerősítése érdekében érdemes lehet az [Azure ad-hitelesítést](../../database/authentication-aad-overview.md), a [naplózást](../../database/auditing-overview.md), a [fenyegetések észlelését](../../database/azure-defender-for-sql.md), a [sor szintű biztonságot](/sql/relational-databases/security/row-level-security)és a [dinamikus adatmaszkolást](/sql/relational-databases/security/dynamic-data-masking)használni.

A speciális felügyeleti és biztonsági funkciók mellett SQL Database olyan eszközöket biztosít, amelyek segítségével [figyelheti és beállíthatja a számítási feladatokat](../../database/monitor-tune-overview.md). A [Azure SQL Analytics (előzetes verzió)](../../../azure-monitor/insights/azure-sql.md) egy fejlett megoldás, amely az összes adatbázis teljesítményének figyelésére használható Azure SQL Database a skálán és több előfizetésben egyetlen nézetben. A Azure SQL Analytics a teljesítménnyel kapcsolatos hibaelhárításhoz beépített intelligenciával rendelkező fő teljesítménymutatókat gyűjti és jeleníti meg.

[Automatikus hangolás](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   folyamatosan figyeli az SQL-végrehajtási terv teljesítményét, és automatikusan kijavítja az azonosított teljesítménnyel kapcsolatos problémákat. 


## <a name="migration-assets"></a>Áttelepítési eszközök 

További segítségért tekintse meg a következő, a valós áttelepítési projektekhez fejlesztett forrásokat.

|Objektum  |Leírás  |
|---------|---------|
|[Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Ez az eszköz javasolt "legmegfelelőbb" célként szolgáló platformot, a felhő készültségét és egy alkalmazás/adatbázis szervizelési szintet biztosít a munkaterheléshez. Egyszerű, egykattintásos számítást és jelentéskészítést kínál, amely segít a nagyméretű ingatlan-értékelések felgyorsításában azáltal, hogy automatizált és egységes döntési eljárást biztosít a megcélzott platformokhoz.|
|[DBLoader segédprogram](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|A DBLoader használatával tagolt szövegfájlokból is betöltheti az adatok SQL Serverba való betöltését. Ez a Windows-konzol segédprogram a SQL Server natív ügyfél tömeges betöltési felületét használja. Az interfész a SQL Server összes verzióján működik, valamint a Azure SQL Database.|
|[Tömeges adatbázis létrehozása a PowerShell-lel](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Használhat három PowerShell-szkriptet, amelyek létrehoznak egy erőforráscsoportot (create_rg.ps1), a [logikai kiszolgálót az Azure-ban](../../database/logical-servers.md) (create_sqlserver.ps1) és egy SQL-adatbázist (create_sqldb.ps1). A szkriptek hurkos képességeket tartalmaznak, így szükség esetén több kiszolgálót és adatbázist is létrehozhat.|
|[Tömeges központi telepítés MSSQL-Scripter és PowerShell használatával](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Ez az eszköz létrehoz egy erőforráscsoportot, létrehoz egy vagy több [logikai kiszolgálót az Azure-ban](../../database/logical-servers.md) a Azure SQL Database üzemeltetéséhez, minden sémát exportál egy helyszíni SQL Server-példányból (vagy több SQL Server 2005 + példányból), és importálja a sémákat a Azure SQL Databaseba.|
|[SQL Server Agent feladatok átalakítása rugalmas adatbázis-feladatokba](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Ez a szkript áttelepíti a forrás SQL Server Agent feladatokat a rugalmas adatbázis-feladatokra.|
|[E-mailek küldése Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Ez a megoldás a SendMail funkció alternatívája, és a helyszíni SQL Server számára is elérhető. A Azure Functions és a SendGrid szolgáltatás használatával küld e-maileket a Azure SQL Databaseból.|
|[A helyszíni SQL Server-bejelentkezések áthelyezésére szolgáló segédprogram Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|A PowerShell-parancsfájlok létrehozhatnak egy T-SQL-parancsfájlt a bejelentkezések újbóli létrehozásához és az adatbázis-felhasználók kiválasztásához a helyszíni SQL Serverból a Azure SQL Database. Az eszköz lehetővé teszi, hogy a Windows Server Active Directory fiókjainak automatikus leképezését Azure AD-fiókokba, valamint a SQL Server natív bejelentkezések igény szerinti áttelepítésére.|
|[Perfmon-adatgyűjtés automatizálása a Logman használatával](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|A Logman eszközt használhatja a perfmon-adatok gyűjtésére (az alapteljesítmény megismerése érdekében) és az áttelepítés céljára vonatkozó javaslatok beszerzésére. Ez az eszköz a logman.exe használatával hozza létre a távoli SQL Server példányon beállított teljesítményszámlálók létrehozására, indítására, leállítására és törlésére szolgáló parancsot.|
|[Adatbázis-áttelepítés Azure SQL Databasere a BACPAC használatával](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Ez a tanulmány útmutatást és lépéseket tartalmaz, amelyekkel felgyorsíthatja az áttelepítést SQL Serverról Azure SQL Databasera BACPAC-fájlok használatával.|

Az adatsql mérnöki csapat fejlesztette ezeket az erőforrásokat. A csapat alapszintű alapokmánya az adatplatform-áttelepítési projektek a Microsoft Azure-beli adatplatformra való feltiltásának és felgyorsításának feloldása.


## <a name="next-steps"></a>Következő lépések

- A SQL Server-adatbázisok Azure SQL Databaseba való áttelepítésének megkezdéséhez tekintse meg az [SQL Server Azure SQL Database áttelepítési útmutatót](sql-server-to-sql-database-guide.md).

- Az adatbázis-és adatáttelepítési forgatókönyvekhez, valamint a speciális feladatokhoz segítséget nyújtó szolgáltatások és eszközök mátrixát az [adatok áttelepítéséhez használható szolgáltatások és eszközök](../../../dms/dms-tools-matrix.md)című szakaszban tekintheti meg.

- A SQL Databaseról további információt a következő témakörben talál:
   - [A Azure SQL Database áttekintése](../../database/sql-database-paas-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 

- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszeréről és bevezetési ciklusáról, tekintse meg a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott költségszámítási és méretezési eljárások az Azure-ba migrált számítási feladatokhoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Az adatelérési réteghez tartozó/B tesztelés végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
