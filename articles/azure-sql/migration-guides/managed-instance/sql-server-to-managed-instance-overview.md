---
title: 'SQL Server a felügyelt SQL-példányhoz: áttelepítési áttekintés'
description: Ismerje meg a SQL Server-adatbázisok Azure SQL felügyelt példányra való átadásához rendelkezésre álló különböző eszközöket és lehetőségeket.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: ac2b535b2e6b7a6b4169d08dd1768d69e685a216
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562010"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>Áttelepítési Áttekintés: SQL Server a felügyelt SQL-példányhoz
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Ismerje meg a különböző áttelepítési lehetőségeket és szempontokat a SQL Server Azure SQL felügyelt példányra való áttelepítéséhez. 

A helyszíni vagy a szolgáltatásban futó SQL Server áttelepíthetők: 

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon-hoz kapcsolódó adatbázis-szolgáltatás (AWS RDS) 
- Számítási motor (Google Cloud Platform-GCP)  
- Felhőbeli SQL a SQL Serverhoz (Google Cloud Platform – GCP) 

Más forgatókönyvek esetén tekintse meg az [adatbázis áttelepítési Útmutatóját](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Áttekintés

Az [Azure SQL felügyelt példánya](../../managed-instance/sql-managed-instance-paas-overview.md) ajánlott cél olyan SQL Server munkaterhelések esetében, amelyek teljes körűen felügyelt szolgáltatást igényelnek, anélkül, hogy a virtuális gépeket vagy azok operációs rendszerét kellene kezelnie. A felügyelt SQL-példány lehetővé teszi, hogy a helyszíni alkalmazásokat az Azure-ba minimális alkalmazás-vagy adatbázis-módosításokkal emelje, miközben a példányok elkülönítése a natív virtuális hálózat (VNet) támogatásával történik. 

## <a name="considerations"></a>Megfontolandó szempontok 

Az áttelepítési lehetőségek kiértékelése során megfontolandó legfontosabb tényezők a következők: 
- Kiszolgálók és adatbázisok száma
- Adatbázisok mérete
- Elfogadható üzleti állásidő az áttelepítési folyamat során 

Az SQL-kiszolgálók SQL felügyelt példányra való áttelepítésének egyik legfőbb előnye, hogy a teljes példányt vagy csak az egyes adatbázisok egy részhalmazát lehet áttelepíteni. Gondosan tervezze meg, hogy az áttelepítési folyamat tartalmazza a következőket: 
- Minden olyan adatbázis, amelyet közösen kell elhelyezni ugyanahhoz a példányhoz 
- Az alkalmazáshoz szükséges példány-szintű objektumok, beleértve a bejelentkezéseket, a hitelesítő adatokat, az SQL-ügynök feladatait és a kezelőket, valamint a kiszolgálói szintű eseményindítókat. 

> [!NOTE]
> Az Azure SQL felügyelt példánya a 99,99%-os rendelkezésre állást a kritikus helyzetekben is garantálja, így az SQL-ben néhány funkció által okozott terhelés nem tiltható le. További információkért tekintse meg a [SQL Server és az Azure SQL felügyelt példányok blogjának különböző teljesítményét okozó kiváltó okokat](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) . 


## <a name="choose-appropriate-target"></a>A megfelelő cél kiválasztása

Néhány általános útmutató, amely segítséget nyújt az SQL felügyelt példányok megfelelő szolgáltatási szintjének és jellemzőinek kiválasztásához, hogy az megfeleljen a [teljesítmény alaptervének](sql-server-to-managed-instance-performance-baseline.md): 

- A CPU-használat alapkonfigurációjának használatával kiépítheti az SQL Server által használt magok számával megegyező felügyelt példányt. Szükség lehet az erőforrások méretezésére a [hardver generálási jellemzőinek](../../managed-instance/resource-limits.md#hardware-generation-characteristics)megfelelően. 
- Használja a memóriahasználat alapkonfigurációját egy olyan [virtuális mag-beállítás](../../managed-instance/resource-limits.md#service-tier-characteristics) kiválasztásához, amely megfelel a memória foglalásának. 
- Használja a fájlrendszer alapértékének i/o-késleltetését, hogy kiválassza a általános célú (az 5 MS-nál nagyobb késést) és a üzletileg kritikus (a 3 MS-nál kisebb késés). 
- A várt i/o-teljesítmény eléréséhez használja az alapértéket az adatok és naplófájlok méretének előfoglalásához. 

Az üzembe helyezés során kiválaszthatja a számítási és tárolási erőforrásokat, majd a [Azure Portal](../../database/scale-resources.md) használata után megváltoztathatja azokat anélkül, hogy állásidőt kellene használni az alkalmazáshoz. 

> [!IMPORTANT]
> A [felügyelt példányok virtuális hálózati követelményeinek](../../managed-instance/connectivity-architecture-overview.md#network-requirements) bármilyen eltérése megakadályozhatja, hogy új példányokat hozzon létre vagy meglévőket használjon. További információ a meglévő hálózatok [létrehozásáról](../../managed-instance/virtual-network-subnet-create-arm-template.md)   és [konfigurálásáról](../../managed-instance/vnet-existing-add-subnet.md)   . 

Az Azure SQL felügyelt példányaiban (általános célú vs üzletileg kritikus) egy másik fontos szempont, hogy elérhetők-e olyan szolgáltatások, mint például a In-Memory OLTP, amely csak üzletileg kritikus szinten érhető el. 

### <a name="sql-server-vm-alternative"></a>SQL Server VM alternatív megoldás

Előfordulhat, hogy vállalata olyan követelményekkel rendelkezik, amelyek az Azure-beli virtuális gépeken SQL Server az Azure SQL felügyelt példányának megfelelő célként. 

Ha a következők vonatkoznak a vállalatra, érdemes lehet áthelyezni a SQL Server VM helyet: 

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
| [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) | Az Azure SQL Azure Migrate lehetővé teszi, hogy az SQL-adatkészletet a VMware platformon Fedezze fel és mérje fel, így biztosítva az Azure SQL-telepítésre vonatkozó javaslatokat, a cél méretezését és a havi becsléseket. | 
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Az első fél Azure-szolgáltatás, amely támogatja az offline módban való áttelepítést az áttelepítési folyamat során állásidőt biztosító alkalmazások esetében. Az online módban való folyamatos áttelepítéstől eltérően az offline módú áttelepítés a teljes adatbázis biztonsági másolatának egyszeri visszaállítását futtatja a forrásról a célra. | 
|[Natív biztonsági mentés és visszaállítás](../../managed-instance/restore-sample-database-quickstart.md) | A felügyelt SQL-példány támogatja a natív SQL Server adatbázis biztonsági másolatainak (. bak fájlok) VISSZAÁLLÍTÁSát, így a legkönnyebb áttelepítési lehetőség azon ügyfelek számára, akik teljes adatbázis-biztonsági mentést biztosíthatnak az Azure Storage-ba. A teljes és a különbözeti biztonsági másolatok is támogatottak és dokumentálva vannak az [áttelepítési eszközök szakaszban](#migration-assets) , a cikk későbbi részében.| 
|[A log Replay szolgáltatás (LRS)](../../managed-instance/log-replay-service-migrate.md) | Ez egy felhőalapú szolgáltatás, amely a SQL Server naplózási technológia alapján engedélyezve van a felügyelt példányok számára, így áttelepítési lehetőséget biztosít azon ügyfelek számára, akik teljes, differenciált és naplózási adatbázist tudnak készíteni az Azure Storage-ba. A LRS az Azure Blob Storageról az SQL felügyelt példányra történő biztonsági mentési fájlok visszaállítására szolgál.| 
| | |

### <a name="alternative-tools"></a>Alternatív eszközök

Az alábbi táblázat az alternatív áttelepítési eszközöket sorolja fel: 

|Technológia |Leírás  |
|---------|---------|
|[Tranzakciós replikáció](../../managed-instance/replication-transactional-overview.md) | Az adatok replikálása a forrás SQL Server adatbázis-táblájából az SQL felügyelt példányára, a tranzakciós konzisztencia fenntartása mellett biztosítva a közzétevő-előfizető típusú áttelepítési lehetőséget. |  |
|[Tömeges másolás](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| A [tömeges másolási program (BCP) segédprogram](/sql/tools/bcp-utility) SQL Server egy adatfájlba másolja az adatok másolását. A BCP segédprogram használatával exportálja az adatait a forrásból, és importálja az adatfájlt a célként szolgáló SQL felügyelt példányba.</br></br> A nagy sebességű tömeges másolási műveletek esetében az adatok Azure SQL Databaseba való áthelyezéséhez az [intelligens tömeges másolási eszköz](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) használható a párhuzamos másolási feladatok kihasználásával az átviteli sebesség maximalizálása érdekében. | 
|[Exportálási varázsló/BACPAC importálása](../../database/database-import.md?tabs=azure-powershell)| A [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) egy olyan Windows-fájl, `.bacpac` amely egy adatbázis sémájának és adatfájljainak beágyazására szolgáló bővítménnyel rendelkezik. A BACPAC használhatók a forrásokból származó adatok exportálására is SQL Server és a fájlnak az Azure SQL felügyelt példányba való újbóli importálására.  |  
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| A [másolási tevékenység](../../../data-factory/copy-activity-overview.md) Azure Data Factory áttelepíti a forrás SQL Server adatbázis (ok) ról az SQL felügyelt példányra a beépített összekötők és egy [Integration Runtime](../../../data-factory/concepts-integration-runtime.md)használatával.</br> </br> Az ADF számos [összekötőt](../../../data-factory/connector-overview.md) támogat az adatok SQL Server forrásokból az SQL felügyelt példányba való áthelyezéséhez. |
| | |

## <a name="compare-migration-options"></a>Áttelepítési lehetőségek összehasonlítása

Az áttelepítési lehetőségek összehasonlítása az üzleti igényeknek megfelelő elérési út kiválasztásával. 

### <a name="recommended-options"></a>Ajánlott lehetőségek

A következő táblázat összehasonlítja a javasolt áttelepítési lehetőségeket: 

|Áttelepítési lehetőség  |A következő esetekben használja  |Megfontolandó szempontok  |
|---------|---------|---------|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) | – Önálló adatbázisok vagy több adatbázis migrálása nagy léptékben. </br> – Az áttelepítési folyamat során az állásidőt is kielégíti. </br> </br> Támogatott források: </br> -SQL Server (2005-2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM |  – A nagy léptékű Migrálás automatizálható [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)használatával. </br> – Az áttelepítés befejezéséhez szükséges idő az adatbázis méretétől függ, és a biztonsági mentés és a visszaállítás időpontját érinti. </br> – Elegendő állásidőre lehet szükség. |
|[Natív biztonsági mentés és visszaállítás](../../managed-instance/restore-sample-database-quickstart.md) | – Az egyes üzletági alkalmazás-adatbázisok áttelepíthetők.  </br> – Gyors és egyszerű áttelepítés külön áttelepítési szolgáltatás vagy eszköz nélkül.  </br> </br> Támogatott források: </br> -SQL Server (2005-2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM | – Az adatbázis biztonsági mentése több szálat használ az adatok Azure Blob Storage-ba történő átvitelének optimalizálása érdekében, de az ISV sávszélesség és az adatbázis mérete hatással lehet az átvitel sebességére. </br> – Az állásidőnek el kell fogadnia a teljes biztonsági mentéshez és visszaállításhoz szükséges időt (amely az adatműveletek mérete).| 
|[A log Replay szolgáltatás (LRS)](../../managed-instance/log-replay-service-migrate.md) | – Az egyes üzletági alkalmazás-adatbázisok áttelepíthetők.  </br> – Az adatbázisok áttelepítéséhez további szabályozásra van szükség.  </br> </br> Támogatott források: </br> -SQL Server (2008-2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM | – Az áttelepítés a teljes adatbázis biztonsági mentését vonja maga után SQL Server és másolja a biztonságimásolat-fájlokat az Azure Blob Storageba. A LRS az Azure Blob Storageról az SQL felügyelt példányra történő biztonsági mentési fájlok visszaállítására szolgál. </br> – Az áttelepítési folyamat során visszaállított adatbázisok visszaállítási módban lesznek, és nem használhatók olvasásra vagy írásra, amíg a folyamat be nem fejeződik.| 
| | | |

### <a name="alternative-options"></a>Alternatív beállítások

A következő táblázat összehasonlítja az alternatív áttelepítési lehetőségeket: 

|Módszer/technológia |A következő esetekben használja |Megfontolandó szempontok  |
|---------|---------|---------|
|[Tranzakciós replikáció](../../managed-instance/replication-transactional-overview.md) | – Az SQL felügyelt példányok adatbázis-tábláira irányuló módosítások folyamatos közzétételével a forrás-adatbázis tábláiban végezheti el a módosításokat. </br> – A kijelölt táblák (az adatbázis részhalmaza) teljes vagy részleges adatbázis-áttelepítése.  </br> </br> Támogatott források: </br> -SQL Server (2012-2019) néhány korlátozással </br> – AWS EC2  </br> -GCP számítási SQL Server VM | </br> – A telepítő viszonylag összetett a többi áttelepítési lehetőséghez képest.   </br> – Folyamatos replikálási lehetőséget biztosít az adatáttelepítéshez (az adatbázisok offline állapotba helyezése nélkül).</br> – A tranzakciós replikáció számos korlátozást tartalmaz, amelyeket a közzétevőnek a forrás SQL Server való beállításakor figyelembe kell venni. További információért lásd [a közzétételi objektumok korlátozásait](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) .  </br> – A [replikációs tevékenység figyelésének](/sql/relational-databases/replication/monitor/monitoring-replication) képessége elérhető.    |
|[Tömeges másolás](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Teljes vagy részleges adatáttelepítés áttelepítése. </br> -Az állásidőt is képes kezelni. </br> </br> Támogatott források: </br> -SQL Server (2005-2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM   | – Az adatok forrásból való exportálásához és a célhelyre való importáláshoz szükséges állásidő. </br> – Az exportálásban/importálásban használt fájlformátumoknak és adattípusoknak konzisztensnek kell lenniük a táblázat sémái között. |
|[Exportálási varázsló/BACPAC importálása](../../database/database-import.md)| – Az egyes üzletági alkalmazás-adatbázisok áttelepíthetők. </br>-Kisebb adatbázisokhoz is használható.  </br>  Nincs szükség külön áttelepítési szolgáltatásra vagy eszközre. </br> </br> Támogatott források: </br> -SQL Server (2005-2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM  |   </br> -Állásidőt igényel, mivel az adatforráshoz kell exportálni, és a célhelyen kell importálni.   </br> – Az exportálásban/importálásban használt fájlformátumoknak és adattípusoknak konzisztensnek kell lenniük a táblázat sémái között, hogy elkerüljék a csonkítás/adattípus-eltérések hibáit. </br> – Az adatbázisok nagy mennyiségű objektummal való exportálásának ideje jelentősen magasabb lehet. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| – Az adatok áttelepítése és/vagy átalakítása a forrás SQL Server adatbázis (ok) ból.</br> -Több adatforrásból származó adatok egyesítése az Azure SQL felügyelt példányaira jellemzően az üzleti intelligencia (BI) munkaterhelések esetében.   </br> -Adatáthelyezési folyamatokat kell létrehozni az ADF-ben, hogy az adatok a forrásról a célhelyre legyenek áthelyezve.   </br> - A [Cost](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) fontos szempont, és a folyamat-eseményindítók, a tevékenységek futtatása, az adatáthelyezés időtartama stb. alapján történik. |
| | | |

## <a name="feature-interoperability"></a>Funkciók együttműködési képessége 

További szempontokat is figyelembe kell venni a más SQL Server funkciókra támaszkodó munkaterhelések áttelepítése során. 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

SQL Server Integration Services (SSIS) csomagok és projektek migrálása a SSISDB-ben az Azure SQL felügyelt példányára [Azure Database Migration Service (DMS)](../../../dms/how-to-migrate-ssis-packages-managed-instance.md)használatával. 

A SSISDB-től kezdődően csak a SSIS-csomagok telepíthetők át a SQL Server 2012-es verzióra. Örökölt SSIS-csomagok konvertálása az áttelepítés előtt. További információt a [Project Conversion oktatóanyagban](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) talál. 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

A SQL Server Reporting Services-(SSRS-) jelentések áttelepíthetők a többoldalas jelentésekre Power BIokban. Az [RDL áttelepítési eszköz](https://github.com/microsoft/RdlMigration) segítségével előkészítheti és áttelepítheti a jelentéseket. Ezt a Microsoft fejlesztette ki az RDL-jelentések az SSRS-kiszolgálókról a Power BI-ba való migrálásához. A GitHubon érhető el, és az áttelepítési forgatókönyv teljes körű áttekintését dokumentálja. 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server Analysis Services SQL Server 2012-es és újabb verziójú táblázatos modellek áttelepíthetők Azure Analysis Servicesre, amely egy, az Azure-ban Analysis Services táblázatos modellhez tartozó Péter-alapú üzembe helyezési modell. További információ a helyszíni modellek áttelepítéséről Azure Analysis Services ebben a [videó-oktatóanyagban](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Azt is megteheti, hogy áttelepíti a helyszíni Analysis Services táblázatos modelleket [az új XMLA írási/olvasási végpontok használatával történő Power bi Premiumre](/power-bi/admin/service-premium-connect-tools). 
> [!NOTE]
> Power BI XMLA olvasási/írási végpontok funkciója jelenleg nyilvános előzetes verzióban érhető el, és az éles számítási feladatokhoz nem kell figyelembe venni, amíg a funkció általánosan elérhetővé válik.

#### <a name="high-availability"></a>Magas rendelkezésre állás

Az SQL Server magas rendelkezésre állású szolgáltatások mindig a feladatátvevő fürt példányain és az Always On rendelkezésre állási csoportok elavulttá válnak a cél Azure SQL felügyelt példányán, mivel a magas rendelkezésre állási architektúra már be van építve mind a [általános célú (standard rendelkezésre állási modell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) , mind a [üzletileg kritikus (prémium rendelkezésre állási modell)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL A prémium rendelkezésre állási modell olyan olvasási felskálázást is biztosít, amely lehetővé teszi az egyik másodlagos csomóponthoz való csatlakozást csak olvasási célokra.     

Az SQL felügyelt példányában található magas rendelkezésre állású architektúrán túl az [automatikus feladatátvételi csoportok](../../database/auto-failover-group-overview.md) funkciója is lehetővé teszi, hogy a felügyelt példányban lévő adatbázisok replikálását és feladatátvételét egy másik régióba kezelje. 

#### <a name="sql-agent-jobs"></a>SQL-ügynök feladatai

Az [SQL Agent-feladatok](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)áttelepítéséhez használja az offline Azure Database MIGRATION Service (DMS) lehetőséget. Ellenkező esetben a Transact-SQL (T-SQL) feladatok futtatása a SQL Server Management Studio használatával, majd a célként megadott SQL felügyelt példányon manuálisan hozza létre őket. 

> [!IMPORTANT]
> Az Azure DMS jelenleg csak a T-SQL alrendszer lépéseit támogató feladatokat támogatja. A SSIS csomag lépéseivel kapcsolatos feladatokat manuálisan kell áttelepíteni. 

#### <a name="logins-and-groups"></a>Bejelentkezések és csoportok

Az SQL-bejelentkezéseket a forrás SQL Server áthelyezheti az Azure SQL felügyelt példányára Database Migration Service (DMS) használatával offline módban.  Az **áttelepítési varázsló** bejelentkezési adatok **[kiválasztása](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** paneljén áttelepítheti a bejelentkezéseket a cél SQL felügyelt példányára. 

Alapértelmezés szerint a Azure Database Migration Service csak az SQL-bejelentkezések áttelepítését támogatja. Engedélyezheti azonban a Windows-bejelentkezések átmásolásának lehetőségét a következővel:

Annak biztosítása, hogy a célként megadott SQL felügyelt példány Azure AD olvasási hozzáféréssel rendelkezik, amely a **globális rendszergazdai** szerepkörrel rendelkező felhasználó által Azure Portal konfigurálható.
A Azure Database Migration Service-példány konfigurálása a Windows felhasználói/csoportos bejelentkezési áttelepítésének engedélyezéséhez, amely a Azure Portal használatával állítható be a konfiguráció lapon. A beállítás engedélyezése után indítsa újra a szolgáltatást, hogy a módosítások érvénybe lépnek.

A szolgáltatás újraindítása után a Windows felhasználói/csoportos bejelentkezések megjelennek az áttelepítéshez elérhető bejelentkezések listájában. Az áttelepített Windows felhasználói/csoportos bejelentkezések esetében a rendszer felszólítja a társított tartománynév megadására. A szolgáltatás felhasználói fiókjai (tartománynév: NT AUTHORITY) és virtuális felhasználói fiókok (a tartománynév NT SZOLGÁLTATÁSsal) nem támogatottak.

További információ: [Windows-felhasználók és-csoportok migrálása egy SQL Server példányban az Azure SQL felügyelt példányára T-SQL használatával](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Azt is megteheti, hogy a Microsoft adatáttelepítés-építészek által tervezett [PowerShell segédprogram-eszközt](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) használja. A segédprogram a PowerShell használatával hoz létre egy T-SQL-parancsfájlt a bejelentkezések újbóli létrehozásához, majd kiválasztja az adatbázis felhasználóit a forrásból a célhelyre. Az eszköz automatikusan leképezi a Windows AD-fiókokat az Azure AD-fiókokba, és minden bejelentkezéshez UPN-keresést végezhet a forrás Active Directory. Az eszköz parancsfájlok egyéni kiszolgáló-és adatbázis-szerepköröket, valamint szerepkör-tagságot, adatbázis-szerepkört és felhasználói engedélyeket. A foglalt adatbázisok jelenleg nem támogatottak, és csak a lehetséges SQL Server engedélyek egy részhalmaza van parancsfájlban. 

#### <a name="encryption"></a>Titkosítás

Ha transzparens adattitkosítás által védett adatbázisokat  [](../../database/transparent-data-encryption-tde-overview.md)   felügyelt példányra telepít át natív visszaállítási beállítással, a megfelelő tanúsítványt a forrás SQL Server [áttelepítheti](../../managed-instance/tde-certificate-migrate.md) a célként megadott SQL felügyelt példányra az adatbázis visszaállítása *előtt* . 

#### <a name="system-databases"></a>Rendszeradatbázisok

A rendszeradatbázisok visszaállítása nem támogatott. A (Master vagy msdb) adatbázisokban tárolt példány-szintű objektumok áttelepíthetők a Transact-SQL (T-SQL) használatával, majd újból létre kell őket hozni a cél felügyelt példányon. 

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

A memórián belüli technológiákkal kapcsolatos további információkért lásd: [a teljesítmény optimalizálása a memóriában lévő technológiák használatával Azure SQL Database és az Azure SQL felügyelt példányain](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-overview) .

## <a name="leverage-advanced-features"></a>Speciális funkciók kihasználása 

Ügyeljen arra, hogy kihasználja a felügyelt SQL-példány által kínált fejlett felhőalapú szolgáltatásokat. Például többé nem kell aggódnia a biztonsági másolatok kezelésével kapcsolatban, mert a szolgáltatás elvégzi Önt. [A megőrzési időtartamon belül bármely időpontra](../../database/recovery-using-backups.md#point-in-time-restore)visszaállíthatja a szolgáltatást. Emellett nem kell aggódnia a magas rendelkezésre állás beállításával kapcsolatban, mivel a [magas rendelkezésre állás beépített](../../database/high-availability-sla.md). 

A biztonság megerősítése érdekében érdemes lehet [Azure Active Directory hitelesítést](../../database/authentication-aad-overview.md), [naplózást](../../managed-instance/auditing-configure.md), [veszélyforrások észlelését](../../database/azure-defender-for-sql.md), [sor szintű biztonságot](/sql/relational-databases/security/row-level-security)és [dinamikus adatmaszkolást](/sql/relational-databases/security/dynamic-data-masking)használni.

A speciális felügyeleti és biztonsági funkciók mellett az SQL felügyelt példánya olyan speciális eszközöket biztosít, amelyek segítségével [figyelheti és beállíthatja a számítási feladatokat](../../database/monitor-tune-overview.md). [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) lehetővé teszi, hogy központosított módon figyelje a felügyelt példányok nagy készletét.  [Automatikus hangolás](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   a felügyelt példányok folyamatosan figyelik az SQL-terv végrehajtási statisztikáinak teljesítményét, és automatikusan kijavítja az azonosított teljesítménnyel kapcsolatos problémákat. 

Egyes szolgáltatások csak akkor érhetők el, ha az [adatbázis kompatibilitási szintje](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) a legújabb kompatibilitási szintre módosul (150). 

## <a name="migration-assets"></a>Áttelepítési eszközök 

További segítségért tekintse meg az alábbi, a valós migrációs projektekhez fejlesztett forrásokat.

|Objektum  |Description  |
|---------|---------|
|[Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely segít felgyorsítani a nagyméretű ingatlanok értékelését azáltal, hogy lehetővé teszi a és automatizált és egységes célként megadott platform döntési folyamatát.|
|[DBLoader segédprogram](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|A DBLoader felhasználható a tagolt szövegfájlokból származó adatok SQL Serverba való betöltésére. Ez a Windows-konzol segédprogram a SQL Server natív ügyféloldali bulkload felületet használja, amely a SQL Server összes verzióján működik, beleértve az Azure SQL MI-t is.|
|[A helyszíni SQL Server-bejelentkezések áthelyezése az Azure SQL felügyelt példányára](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Egy PowerShell-parancsfájl, amely létrehoz egy T-SQL-parancsfájlt a bejelentkezések újbóli létrehozásához és az adatbázis-felhasználók kiválasztásához a helyszíni SQL Serverból az Azure SQL felügyelt példányára. Az eszköz lehetővé teszi a Windows AD-fiókok automatikus leképezését az Azure AD-fiókokba, valamint SQL Server natív bejelentkezések igény szerinti áttelepítését.|
|[Perfmon-adatgyűjtés automatizálása a Logman használatával](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Egy olyan eszköz, amely összegyűjti az adatokat, hogy megismerje az áttelepítési cél javaslatában támogatott alapkonfigurációk teljesítményét. Ez az eszköz a logman.exe használatával hozza létre a távoli SQL Serveron beállított teljesítményszámlálók létrehozására, indítására, leállítására és törlésére szolgáló parancsot.|
|[Tanulmány – adatbázis-áttelepítés a felügyelt Azure SQL-példányra teljes és differenciált biztonsági mentések visszaállításával](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Ez a tanulmány útmutatást és lépéseket biztosít a SQL Serverról az Azure SQL felügyelt példányra történő Migrálás felgyorsításához, ha csak teljes és különbözeti biztonsági mentések vannak (és nem áll rendelkezésre napló biztonsági mentése).|

Ezek az erőforrások az Azure adatcsoport-mérnöki csapat által szponzorált adatsql ninja program részeként lettek kifejlesztve. Az adatelemzési program alapszintű alapokmánya az, hogy feloldja az összetett modernizációt, és az adatplatform-migrációs lehetőségeket a Microsoft Azure-beli adatplatformján is felgyorsítja. Ha úgy gondolja, hogy a szervezete szeretne részt venni az adatsql ninja programban, forduljon a fiókhoz, és kérje meg, hogy küldje el a jelölést.


## <a name="next-steps"></a>Következő lépések

Az SQL Server Azure SQL felügyelt példányra való áttelepítésének megkezdéséhez tekintse meg a [SQL Server az SQL felügyelt példány áttelepítési Útmutatóját](sql-server-to-managed-instance-guide.md).

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egyik mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Az Azure SQL felügyelt példányával kapcsolatos további tudnivalókért tekintse meg a következőt:
   - [Szolgáltatási szintek az Azure SQL felügyelt példányában](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server és az Azure SQL felügyelt példányai közötti különbségek](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
