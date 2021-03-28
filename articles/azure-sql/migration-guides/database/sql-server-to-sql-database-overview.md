---
title: 'SQL Server a Azure SQL Databasehoz: áttelepítési áttekintés'
description: Ismerje meg a SQL Server-adatbázisok Azure SQL Databaseba való átadásához rendelkezésre álló különböző eszközöket és lehetőségeket.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 5ef69b8cf0e51c6248862f4cabd4f4ce3598a811
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640361"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Áttelepítési Áttekintés: SQL Server Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Ismerkedjen meg a különböző áttelepítési lehetőségekkel és szempontokkal a SQL Server Azure SQL Databasere való áttelepítéséhez. 

A helyszíni vagy a szolgáltatásban futó SQL Server áttelepíthetők: 

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon-hoz kapcsolódó adatbázis-szolgáltatás (AWS RDS) 
- Számítási motor (Google Cloud Platform-GCP)  
- Felhőbeli SQL a SQL Serverhoz (Google Cloud Platform – GCP) 

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Áttekintés

[Azure SQL Database](../../database/sql-database-paas-overview.md) a teljes körűen felügyelt platformként (Pásti) megkövetelt SQL Server munkaterhelések esetében ajánlott célként megadott beállítás. SQL Database kezeli a legtöbb adatbázis-kezelési funkciót, valamint a magas rendelkezésre állást, az intelligens lekérdezés-feldolgozást, a méretezhetőséget és a számos különböző típusú alkalmazáshoz beépített teljesítménnyel kapcsolatos képességeket. 

SQL Database rugalmasságot biztosít több [üzembe helyezési modellel](../../database/sql-database-paas-overview.md#deployment-models) és [szolgáltatási szinttel](../../database/service-tiers-vcore.md#service-tiers) , amelyek különböző típusú alkalmazásokhoz vagy munkaterhelésekhez használhatók.

A SQL Database-re való Migrálás egyik legfőbb előnye, hogy az alkalmazás modernizálása a Pásti-képességek kihasználásával, valamint a példányok szintjén, például az SQL-ügynök feladataira hatókörrel rendelkező technikai összetevőktől való függőség kiiktatásával végezhető el.

Azt is megteheti, hogy a SQL Server helyszíni licencek áttelepítésével Azure SQL Database a [virtuális mag-alapú vásárlási modellt](../../database/service-tiers-vcore.md)a SQL Server [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) használatával.

Ez az útmutató az áttelepítési lehetőségek és szempontok tisztázását célozza meg SQL Server-adatbázisok Azure SQL Databaseba való áttelepítésének előkészítésekor.  

## <a name="considerations"></a>Megfontolandó szempontok 

Az áttelepítési lehetőségek kiértékelése során megfontolandó legfontosabb tényezők a következők: 

- Kiszolgálók és adatbázisok száma
- Adatbázisok mérete
- Elfogadható üzleti állásidő az áttelepítési folyamat során 

Az útmutatóban felsorolt áttelepítési lehetőségek figyelembe veszik ezeket a tényezőket. A Azure SQL Databaseba való logikai adatáttelepítés esetén az áttelepítési idő az adatbázis objektumainak számától és az adatbázis méretétől függ. 

Különböző eszközök érhetők el különböző számítási feladatokhoz és felhasználói beállításokhoz. Egyes eszközök segítségével egyetlen adatbázis gyors áttelepítését végezheti el felhasználói felületen alapuló eszköz használatával, míg más eszközök több adatbázist is áttelepíthetnek, amelyek automatizáltan képesek a nagy léptékű áttelepítések kezelésére. 

## <a name="choose-appropriate-target"></a>A megfelelő cél kiválasztása

Tekintse át az általános irányelveket, amelyekkel kiválaszthatja a Azure SQL Database megfelelő telepítési modelljét és szolgáltatási szintjét. Az üzembe helyezés során kiválaszthatja a számítási és tárolási erőforrásokat, majd [később is megváltoztathatja őket a Azure Portal használatával](../../database/scale-resources.md) anélkül, hogy állásidőt kellene kihasználnia az alkalmazáshoz.


**Üzembe helyezési modellek**: megismerheti az alkalmazás számítási feladatait és a használati mintát, hogy az egyetlen adatbázis vagy egy rugalmas készlet között döntsön. 

- [Egyetlen adatbázis](../../database/single-database-overview.md) egy teljes körűen felügyelt adatbázis, amely a legtöbb modern Felhőbeli alkalmazáshoz és szolgáltatáshoz alkalmas.
- A [rugalmas készlet](../../database/elastic-pool-overview.md) olyan önálló adatbázisok gyűjteménye, amelyek az erőforrások, például a processzor vagy a memória közös készletével rendelkeznek, és alkalmasak arra, hogy a készletben lévő adatbázisokat kiszámítható használati mintákkal kombinálják, amelyek hatékonyan megoszthatják ugyanazt az erőforrás-készletet.

**Vásárlási modellek**: válasszon a virtuális mag, a DTU vagy a kiszolgáló nélküli vásárlási modell közül. 

- A [virtuális mag modell](../../database/service-tiers-vcore.md) segítségével kiválaszthatja a Azure SQL Database virtuális mag számát, így a legegyszerűbb választás a helyszíni SQL Serverból való fordítás során. Ez az egyetlen lehetőség, amely támogatja a licencek megtakarítását a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- A [DTU modell](../../database/service-tiers-dtu.md) elvonta a mögöttes számítási, memória-és IO-erőforrásokat, hogy a rendszer kevert DTU biztosítson. 
- A [kiszolgáló nélküli modell](../../database/serverless-tier-overview.md) olyan számítási feladatokhoz készült, amelyeknek automatikus igény szerinti skálázásra van szükségük a használati díjak alapján. A kiszolgáló nélküli számítási rétegek automatikusan szüneteltetik az adatbázisokat az inaktív időszakokban (ahol csak a tárterületet számlázzák), és automatikusan folytatja az adatbázisokat, amikor a tevékenység visszaadja. 

**Szolgáltatási szintek**: választhat három szolgáltatási szintet, amelyek különböző típusú alkalmazásokhoz lettek kialakítva.

- A [általános célú/standard szintű szolgáltatási szint](../../database/service-tier-general-purpose.md) kiegyensúlyozott, költségvetésre épülő megoldást kínál, amely a közepes szintű alkalmazások kiszolgálására alkalmas számítási és tárolási kapacitással rendelkezik, és a tárolási rétegben beépített redundanciával helyreállítható a hibákból. A legtöbb adatbázis-számítási feladathoz lett tervezve. 
- A [üzletileg kritikus/prémium szolgáltatási szint](../../database/service-tier-business-critical.md) olyan magas szintű alkalmazások számára készült, amelyek nagy tranzakciós sebességet igényelnek, alacsony késésű IO-t és magas fokú rugalmasságot biztosítanak a feladatátvételhez és az olvasási munkaterhelések kiszervezéséhez egyaránt elérhető másodlagos replikákkal.
- Az [nagy kapacitású szolgáltatási réteg](../../database/service-tier-hyperscale.md) olyan adatbázisokhoz használható, amelyek növekvő adatmennyiséggel rendelkeznek, és a 100 TB-os adatbázis méretének automatikus skálázására van szükség. Nagyon nagy méretű adatbázisokhoz tervezve. 

> [!IMPORTANT]
> A [tranzakciós napló arányának szabályozása](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) Azure SQL Database a nagy mennyiségű betöltési arány korlátozása érdekében. Így az áttelepítés során szükség lehet a célként megadott adatbázis-erőforrások (virtuális mag/DTU) méretezésére a CPU vagy az átviteli sebesség megkönnyítése érdekében. Válassza ki a megfelelő méretű céladatbázis-adatbázist, de szükség esetén tervezze meg az erőforrások méretezését az áttelepítéshez. 


### <a name="sql-server-on-azure-vm-alternative"></a>SQL Server Azure-beli VM-alternatíva esetén

Előfordulhat, hogy vállalata olyan követelményekkel rendelkezik, amelyek az Azure-ban [Virtual Machines SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) megfelelőbb célt tesznek lehetővé, mint Azure SQL Database. 

Ha az alábbiak vonatkoznak a vállalatra, érdemes lehet áthelyezni az Azure-beli virtuális gépekre SQL Server: 

- Ha közvetlen hozzáférésre van szüksége az operációs rendszerhez vagy a fájlrendszerhez, például a harmadik féltől származó vagy az egyéni ügynökök telepítéséhez a SQL Servert tartalmazó virtuális gépen. 
- Ha olyan funkciókkal rendelkezik, amelyek még nem támogatottak, például a FileStream/leválasztható, a bázisterület és a több példány tranzakciója. 
- Ha mindenképpen a SQL Server adott verziójára van szüksége (például 2012). 
- Ha a számítási követelmények sokkal alacsonyabbak, mint a felügyelt példányok ajánlatai (egy virtuális mag, például), és az adatbázis-összevonás nem elfogadható megoldás. 


## <a name="migration-tools"></a>Migrálási eszközök 

A Migrálás ajánlott eszközei a Data Migration Assistant és a Azure Database Migration Service. Más alternatív áttelepítési lehetőségek is elérhetők. 

### <a name="recommended-tools"></a>Ajánlott eszközök

A következő táblázat a javasolt áttelepítési eszközöket sorolja fel: 

|Technológia | Leírás|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Az Azure SQL Azure Migrate lehetővé teszi, hogy az SQL-adatkészletet a VMware platformon Fedezze fel és mérje fel, így biztosítva az Azure SQL-telepítésre vonatkozó javaslatokat, a cél méretezését és a havi becsléseket. | 
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|A Data Migration Assistant egy asztali eszköz, amely zökkenőmentesen értékeli a SQL Server és a Migrálás Azure SQL Database (a séma és az adatértékek esetében egyaránt). Az eszközt telepítheti a helyszíni kiszolgálóra vagy a helyi gépre, amely a forrás-adatbázisokhoz kapcsolódik. Az áttelepítési folyamat a forrás-és a céladatbázis objektumai közötti logikai adatáthelyezés. </br> – Önálló adatbázisok migrálása (a séma és az adatszolgáltatások esetében is)|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|Egy első féltől származó Azure-szolgáltatás, amely áttelepítheti a SQL Server-adatbázisokat Azure SQL Database a Azure Portal vagy a PowerShell-lel automatizált használatával. Az Azure DMS használatához ki kell választania egy előnyben részesített Azure-Virtual Network (VNet) a kiépítés során, hogy biztosítsa a forrás-SQL Server adatbázisaihoz való kapcsolódást. </br> – Önálló adatbázisok vagy nagy léptékű Migrálás. |
| | |


### <a name="alternative-tools"></a>Alternatív eszközök

Az alábbi táblázat az alternatív áttelepítési eszközöket sorolja fel: 

|Technológia |Leírás  |
|---------|---------|
|[Tranzakciós replikáció](../../database/replication-to-sql-database.md)|A forrás SQL Server adatbázis-táblá (k) adatait replikálja a SQL Database egy kiadói előfizetői típus áttelepítési lehetőségének megadásával a tranzakciós konzisztencia fenntartása mellett. A növekményes adatváltozásokat a közzétevők számára elérhetővé kell tennie az előfizetők számára.|
|[Importálási exportálási szolgáltatás/BACPAC](../../database/database-import.md)|A [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) egy olyan Windows-fájl, `.bacpac` amely egy adatbázis sémájának és adatfájljainak beágyazására szolgáló bővítménnyel rendelkezik. A BACPAC használható a forrás-SQL Server adatok exportálására és az adatok Azure SQL Databaseba való importálására is. A BACPAC-fájl egy új Azure SQL Databaseba importálható a Azure Portal használatával. </br></br> A nagyméretű adatbázisok méretével vagy nagy számú adatbázissal való méretezéshez és teljesítményhez érdemes a [SqlPackage](../../database/database-import.md#using-sqlpackage) parancssori segédprogramot használni az adatbázisok exportálásához és importálásához.|
|[Tömeges másolás](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|A [tömeges másolási program (BCP) segédprogram](/sql/tools/bcp-utility) SQL Server egy adatfájlba másolja az adatok másolását. A BCP segédprogram használatával exportálja az adatait a forrásból, és importálja az adatfájlt a cél SQL Databaseba. </br></br> A nagy sebességű tömeges másolási műveletek esetében az adatok Azure SQL Databaseba való áthelyezéséhez az [intelligens tömeges másolási eszköz](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) használható az átviteli sebesség maximalizálására a párhuzamos másolási feladatok kihasználásával.|
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)|A [másolási tevékenység](../../../data-factory/copy-activity-overview.md) Azure Data Factory áttelepíti a forrás SQL Server adatbázis (ok) adatait, hogy SQL Database beépített összekötők és egy [Integration Runtime](../../../data-factory/concepts-integration-runtime.md)használatával.</br> </br> Az ADF számos [összekötőt](../../../data-factory/connector-overview.md) támogat az adatok SQL Server forrásokból SQL Databaseba való áthelyezéséhez.|
|[SQL-adatszinkronizálás](../../database/sql-data-sync-data-sql-server-sql-database.md)|A SQL-adatszinkronizálás Azure SQL Databasere épülő szolgáltatás, amely lehetővé teszi, hogy a helyileg és a felhőben egyaránt több adatbázison keresztül szinkronizálja az adatokat.</br>Az adatszinkronizálás olyan esetekben hasznos, amikor a Azure SQL Database vagy SQL Server több adatbázisában frissíteni kell az információkat.|
| | |

## <a name="compare-migration-options"></a>Áttelepítési lehetőségek összehasonlítása

Az áttelepítési lehetőségek összehasonlítása az üzleti igényeknek megfelelő elérési út kiválasztásával. 

### <a name="recommended-options"></a>Ajánlott lehetőségek

A következő táblázat összehasonlítja a javasolt áttelepítési lehetőségeket: 

|Áttelepítési lehetőség  |A következő esetekben használja  |Megfontolandó szempontok  |
|---------|---------|---------|
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | – Önálló adatbázisok migrálása (séma és az adatbázis is).  </br> – Az adatáttelepítési folyamat során az állásidőt is kielégíti. </br> </br> Támogatott források: </br> -SQL Server (2005-2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM | – Az áttelepítési tevékenység elvégzi az adatáthelyezést az adatbázis-objektumok között (a forrástól a cél felé), és ezért javasolt a lejárati idő alatt futtatni. </br> -A DMA jelentést készít az adatbázis-objektumok áttelepítésének állapotáról, beleértve az áttelepített sorok számát is.  </br> – Nagyméretű áttelepítésekhez (adatbázisok száma/adatbázis mérete) használja az alább felsorolt Azure Database Migration Service.|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| – Önálló adatbázisok vagy nagy léptékű Migrálás. </br> – Az áttelepítési folyamat során az állásidőt is kielégíti. </br> </br> Támogatott források: </br> -SQL Server (2005-2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM | – A nagy léptékű Migrálás automatizálható [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md)használatával. </br> – Az áttelepítés befejezéséhez szükséges idő az adatbázis méretétől és az adatbázis objektumainak számától függ. </br> – A forrásadatbázis csak olvashatóként van beállítva. |
| | | |

### <a name="alternative-options"></a>Alternatív beállítások

A következő táblázat összehasonlítja az alternatív áttelepítési lehetőségeket: 

|Módszer/technológia |A következő esetekben használja    |Megfontolandó szempontok  |
|---------|---------|---------|
|[Tranzakciós replikáció](../../database/replication-to-sql-database.md)| – Migrálás a forrás-adatbázis tábláiból származó módosítások folyamatos közzétételével SQL Database táblák megcélzásához. </br> – A kijelölt táblák (az adatbázis részhalmaza) teljes vagy részleges adatbázis-áttelepítése.  </br> </br> Támogatott források: </br> - [SQL Server (2016-2019) néhány korlátozással](/sql/relational-databases/replication/replication-backward-compatibility) </br> – AWS EC2  </br> -GCP számítási SQL Server VM  | – A telepítő viszonylag összetett a többi áttelepítési lehetőséghez képest.   </br> – Folyamatos replikálási lehetőséget biztosít az adatáttelepítéshez (az adatbázisok offline állapotba helyezése nélkül).  </br> – A tranzakciós replikáció számos korlátozást tartalmaz, amelyeket a közzétevőnek a forrás SQL Server való beállításakor figyelembe kell venni. További információért lásd [a közzétételi objektumok korlátozásait](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) . </br>– Lehetséges a [replikálási tevékenység figyelése](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Importálási exportálási szolgáltatás/BACPAC](../../database/database-import.md)| – Az üzletági alkalmazások különböző adatbázisainak migrálása. </br>-Kisebb adatbázisokhoz is használható.  </br>  – Nincs szükség külön áttelepítési szolgáltatásra vagy eszközre. </br> </br> Támogatott források: </br> -SQL Server (2005-2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM  |  -Állásidőt igényel, mivel az adatforráshoz kell exportálni, és a célhelyen kell importálni.   </br> – Az exportálásban/importálásban használt fájlformátumoknak és adattípusoknak konzisztensnek kell lenniük a táblázat sémái között, hogy elkerüljék a csonkítás/adattípus-eltérések hibáit.  </br> – Az adatbázisok nagy mennyiségű objektummal való exportálásának ideje jelentősen magasabb lehet.       |
|[Tömeges másolás](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Teljes vagy részleges adatáttelepítés áttelepítése. </br> -Az állásidőt is képes kezelni. </br> </br> Támogatott források: </br> -SQL Server (2005-2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM   | – Az adatok forrásból való exportálásához és a célhelyre való importáláshoz szükséges állásidő. </br> – Az exportálásban/importálásban használt fájlformátumoknak és adattípusoknak konzisztensnek kell lenniük a táblázat sémái között. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)| -A forrás SQL Server adatbázisaiból származó adatok migrálása és/vagy átalakítása. </br> -Több adatforrásból származó adatok egyesítése Azure SQL Database általában az üzleti intelligencia (BI) munkaterhelések számára.  |  -Adatáthelyezési folyamatokat kell létrehozni az ADF-ben, hogy az adatok a forrásról a célhelyre legyenek áthelyezve.   </br> - A [Cost](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) fontos szempont, és a folyamat-eseményindítók, a tevékenységek futtatása, az adatáthelyezés időtartama stb. alapján történik. |
|[SQL-adatszinkronizálás](../../database/sql-data-sync-data-sql-server-sql-database.md)| – Az adatokat a forrás-és a cél-adatbázisok között szinkronizálja.</br> -Alkalmas a folyamatos szinkronizálás futtatására a Azure SQL Database és a helyszíni SQL Server között egy kétirányú folyamat során. | -Azure SQL Database a központi adatbázisnak kell lennie a helyszíni SQL Server-adatbázissal való szinkronizáláshoz tag-adatbázisként.</br> – A tranzakciós replikációhoz képest SQL-adatszinkronizálás támogatja a kétirányú adatszinkronizálást a helyszíni és a Azure SQL Database között. </br> -A munkaterheléstől függően nagyobb teljesítményre lehet hatással.|
| | | |

## <a name="feature-interoperability"></a>Funkciók együttműködési képessége 

További szempontokat is figyelembe kell venni a más SQL Server funkciókra támaszkodó munkaterhelések áttelepítése során.

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Telepítse át SQL Server Integration Services (SSIS) csomagokat az Azure-ba a csomagok Azure-SSIS futtatókörnyezetbe való újbóli üzembe helyezésével [Azure Data Factory](../../../data-factory/introduction.md)-ban. Azure Data Factory [támogatja a SSIS-csomagok áttelepítését](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) azáltal, hogy a SSIS-csomagok Azure-ban való végrehajtásához készült futtatókörnyezetet biztosít. Azt is megteheti, hogy a SSIS ETL-logikát natív módon írja át az ADF-ben a [adatfolyamok](../../../data-factory/concepts-data-flow-overview.md)használatával.


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
SQL Server Reporting Services-(SSRS-) jelentések áttelepíthetők a többoldalas jelentésekre Power BIokban. A jelentések előkészítését és áttelepítését az [RDL-áttelepítési eszköz](https://github.com/microsoft/RdlMigration) segítségével végezheti el. Ezt a Microsoft fejlesztette ki az RDL-jelentések az SSRS-kiszolgálókról a Power BI-ba való migrálásához. A GitHubon érhető el, és az áttelepítési forgatókönyv teljes körű áttekintését dokumentálja. 

#### <a name="high-availability"></a>Magas rendelkezésre állás
SQL Server magas rendelkezésre állású szolgáltatások (például az Always On Feladatátvevőfürt-példányok és always on rendelkezésre állási csoportok) manuális beállítása elavulttá válik a cél Azure SQL Database, mivel a magas rendelkezésre állású architektúra már be van építve mind a [általános célú (standard rendelkezésre állási modell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) , mind [üzletileg kritikus (prémium rendelkezésre állási modell SQL Database)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) A üzletileg kritikus/prémium szolgáltatási szint olyan olvasási felskálázást is biztosít, amely lehetővé teszi, hogy csak olvasási célokra lehessen csatlakozni az egyik másodlagos csomóponthoz. 

A SQL Database részét képező, magas rendelkezésre állású architektúrán kívül az [automatikus feladatátvételi csoportok](../../database/auto-failover-group-overview.md) funkciója is lehetővé teszi a felügyelt példányban lévő adatbázisok replikálásának és feladatátvételének kezelését egy másik régióba. 

#### <a name="sql-agent-jobs"></a>SQL-ügynök feladatai
Az SQL Agent-feladatok nem támogatottak közvetlenül a Azure SQL Databaseban, és telepíteni kell őket [Elastic Database feladatokhoz (előzetes verzió)](../../database/job-automation-overview.md).

#### <a name="logins-and-groups"></a>Bejelentkezések és csoportok
Helyezze át az SQL-bejelentkezéseket a forrás SQL Serverról Azure SQL Database kapcsolat nélküli módban Database Migration Service (DMS) használatával.  Az **áttelepítési varázsló** **kiválasztott bejelentkezések** paneljének használatával áttelepítheti a bejelentkezéseket a cél SQL Databaseba. 

A Windows-felhasználók és-csoportok a DMS használatával is áttelepíthetők, ha engedélyezi a megfelelő váltógomb beállítást a DMS konfigurációs lapján. 

Azt is megteheti, hogy a Microsoft adatáttelepítés-építészek által tervezett [PowerShell segédprogram-eszközt](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) használja. A segédprogram a PowerShell használatával hoz létre egy Transact-SQL (T-SQL) parancsfájlt a bejelentkezések újbóli létrehozásához, majd kiválasztja az adatbázis felhasználóit a forrásból a célhelyre. Az eszköz automatikusan leképezi a Windows AD-fiókokat az Azure AD-fiókokba, és minden bejelentkezéshez UPN-keresést végezhet a forrás Active Directory. Az eszköz parancsfájlok egyéni kiszolgáló-és adatbázis-szerepköröket, valamint szerepkör-tagságot, adatbázis-szerepkört és felhasználói engedélyeket. A foglalt adatbázisok még nem támogatottak, és csak a lehetséges SQL Server engedélyek egy részhalmaza van parancsfájlban. 


#### <a name="system-databases"></a>Rendszeradatbázisok
Azure SQL Database esetében az egyetlen alkalmazható rendszeradatbázis a [fő](/sql/relational-databases/databases/master-database) -és tempdb. További információ: [tempdb in Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="leverage-advanced-features"></a>Speciális funkciók kihasználása 

Ügyeljen arra, hogy kihasználhassa a SQL Database által kínált fejlett felhőalapú szolgáltatásokat. Például többé nem kell aggódnia a biztonsági másolatok kezelésével kapcsolatban, mert a szolgáltatás elvégzi Önt. [A megőrzési időtartamon belül bármely időpontra](../../database/recovery-using-backups.md#point-in-time-restore)visszaállíthatja a szolgáltatást. 

A biztonság megerősítése érdekében érdemes lehet [Azure Active Directory hitelesítést](../../database/authentication-aad-overview.md), [naplózást](../../database/auditing-overview.md), [veszélyforrások észlelését](../../database/azure-defender-for-sql.md), [sor szintű biztonságot](/sql/relational-databases/security/row-level-security)és [dinamikus adatmaszkolást](/sql/relational-databases/security/dynamic-data-masking)használni.

A speciális felügyeleti és biztonsági funkciók mellett a SQL Database olyan speciális eszközöket biztosít, amelyek segítségével [figyelheti és beállíthatja a számítási feladatokat](../../database/monitor-tune-overview.md). A [Azure SQL Analytics (előzetes verzió)](../../../azure-monitor/insights/azure-sql.md) egy fejlett felhőalapú figyelési megoldás, amely az összes adatbázis teljesítményének figyelésére használható Azure SQL Database és több előfizetés között egyetlen nézetben. A Azure SQL Analytics a teljesítménnyel kapcsolatos hibaelhárításhoz beépített intelligenciával rendelkező fő teljesítménymutatókat gyűjti és jeleníti meg.

[Automatikus hangolás](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   folyamatosan figyeli az SQL-végrehajtási terv statisztikáinak teljesítményét, és automatikusan javítja az azonosított teljesítménnyel kapcsolatos problémákat. 


## <a name="migration-assets"></a>Áttelepítési eszközök 

További segítségért tekintse meg az alábbi, a valós migrációs projektekhez fejlesztett forrásokat.

|Objektum  |Description  |
|---------|---------|
|[Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely lehetővé teszi a nagyméretű ingatlan-értékelések felgyorsítását azáltal, hogy automatizált és egységes célzott platformra vonatkozó döntési folyamatot biztosít.|
|[DBLoader segédprogram](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|A DBLoader felhasználható a tagolt szövegfájlokból származó adatok SQL Serverba való betöltésére. Ez a Windows-konzol segédprogram a SQL Server natív ügyféloldali bulkload felületet használja, amely a SQL Server összes verzióján működik, beleértve a Azure SQL Databaset is.|
|[Tömeges adatbázis létrehozása a PowerShell-lel](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Ez három PowerShell-parancsfájlt tartalmaz, amelyek létrehoznak egy erőforráscsoportot (create_rg.ps1), a [logikai kiszolgálót az Azure-ban](../../database/logical-servers.md) (create_sqlserver.ps1), és Azure SQL Database (create_sqldb.ps1). A szkriptek hurkos képességeket tartalmaznak, így szükség esetén több kiszolgálót és adatbázist is létrehozhat.|
|[Tömeges telepítés MSSQL-Scripter & PowerShell-lel](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Ez az eszköz létrehoz egy erőforráscsoportot, egy vagy több [Azure-beli logikai kiszolgálót](../../database/logical-servers.md) a Azure SQL Database üzemeltetéséhez, minden sémát exportál a helyszíni SQL Server (vagy több SQL Server-kiszolgálóról (2005 +), és importálja azt a Azure SQL Databaseba.|
|[SQL Server Agent feladatok átalakítása Elastic Database feladatokba](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Ez a szkript áttelepíti a forrás SQL Server Agent feladatokat Elastic Database feladatokra.|
|[Levelek küldése Azure SQL Databaseról](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Ez megoldást kínál a helyi SQL Serverban elérhető SendMail-képesség alternatívájaként. A megoldás a Azure Functions és az Azure SendGrid szolgáltatás használatával küld e-maileket a Azure SQL Databaseból.|
|[A helyszíni SQL Server-bejelentkezések áthelyezésére szolgáló segédprogram Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Egy PowerShell-parancsfájl, amely létrehoz egy T-SQL-parancsfájlt a bejelentkezések újbóli létrehozásához és a helyszíni SQL Server az adatbázis-felhasználók kiválasztásához a Azure SQL Database. Az eszköz lehetővé teszi a Windows AD-fiókok automatikus leképezését az Azure AD-fiókokba, valamint SQL Server natív bejelentkezések igény szerinti áttelepítését.|
|[PerfMon-adatgyűjtés automatizálása a Logman használatával](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Olyan eszköz, amely Teljesítményfigyelő adatokat gyűjt az alapkonfigurációk teljesítményének megismeréséhez és az áttelepítési cél javaslatainak támogatásához. Ez az eszköz a logman.exe használatával hozza létre a távoli SQL Serverban beállított teljesítményszámlálók létrehozására, indítására, leállítására és törlésére szolgáló parancsot.|
|[Tanulmány – adatbázis migrálása az Azure SQL Database-be az BACPAC használatával](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Ez a tanulmány útmutatást és lépéseket biztosít a SQL Serverról Azure SQL Database BACPAC-fájlok használatával történő áttelepítésének felgyorsításához.|

Az adatsql mérnöki csapat fejlesztette ezeket az erőforrásokat. A csapat alapszintű alapokmánya az adatplatform-áttelepítési projektek a Microsoft Azure-beli adatplatformra való feltiltásának és felgyorsításának feloldása.


## <a name="next-steps"></a>Következő lépések

A SQL Server SQL Databasere való áttelepítésének megkezdéséhez tekintse meg az [SQL Server Azure SQL Database áttelepítési útmutatót](sql-server-to-sql-database-guide.md).

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egyik mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Ha többet szeretne megtudni SQL Database lásd:
   - [A Azure SQL Database áttekintése](../../database/sql-database-paas-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 

- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).