---
title: 'SQL Server a felügyelt SQL-példányhoz: áttelepítési áttekintés'
description: Ismerje meg a SQL Server-adatbázisok Azure SQL felügyelt példányra történő áttelepíthető eszközeit és lehetőségeit.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 0a3fd1b492d19e241d89cc5477891c7c836e4640
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078978"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Áttelepítési Áttekintés: SQL Server az Azure SQL felügyelt példányához
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Ismerje meg a SQL Server-adatbázisok Azure SQL felügyelt példányra való áttelepítésének lehetőségeit és szempontjait. 

A helyszínen vagy a rendszeren futó SQL Server-adatbázisokat áttelepítheti: 

- SQL Server az Azure Virtual Machines.  
- Amazon Web Services (AWS) rugalmas számítási felhő (EC2). 
- AWS-hez kapcsolódó adatbázis-szolgáltatás (RDS). 
- Számítási motor a Google Cloud Platformban (GCP).  
- Felhőalapú SQL SQL Server a GCP-ben. 

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Áttekintés

Az [Azure SQL felügyelt példánya](../../managed-instance/sql-managed-instance-paas-overview.md) ajánlott cél olyan SQL Server munkaterhelések esetében, amelyek teljes körűen felügyelt szolgáltatást igényelnek, anélkül, hogy a virtuális gépeket vagy azok operációs rendszerét kellene kezelnie. Az SQL felügyelt példány lehetővé teszi a helyszíni alkalmazások Azure-ba való áthelyezését minimális alkalmazás-vagy adatbázis-módosításokkal. A példányok teljes elkülönítését a natív virtuális hálózatok támogatásával biztosítja. 

## <a name="considerations"></a>Megfontolandó szempontok 

Az áttelepítési lehetőségek kiértékelése során megfontolandó legfontosabb tényezők a következők: 
- Kiszolgálók és adatbázisok száma
- Adatbázisok mérete
- Elfogadható üzleti állásidő az áttelepítési folyamat során 

A SQL Server-adatbázisok SQL felügyelt példányra való áttelepítésének egyik legfőbb előnye, hogy a teljes példányt vagy csak az egyes adatbázisok egy részhalmazát lehet áttelepíteni. Gondosan tervezze meg, hogy az áttelepítési folyamat tartalmazza a következőket: 
- Minden olyan adatbázis, amelyet közösen kell elhelyezni ugyanahhoz a példányhoz 
- Az alkalmazáshoz szükséges példány-szintű objektumok, beleértve a bejelentkezéseket, a hitelesítő adatokat, az SQL-ügynök feladatait és a kezelőket, valamint a kiszolgálói szintű eseményindítókat 

> [!NOTE]
> Az Azure SQL felügyelt példánya 99,99%-os rendelkezésre állást garantál, még a kritikus helyzetekben is. A felügyelt SQL-példány egyes funkciói által okozott terhelés nem tiltható le. További információ: az [SQL felügyelt példánya és a SQL Server blogbejegyzés közötti teljesítménybeli különbségek fő okai](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) . 


## <a name="choose-an-appropriate-target"></a>Válasszon ki egy megfelelő célt

Az alábbi általános irányelvek segítségével kiválaszthatja a megfelelő szolgáltatási szintet és az SQL felügyelt példányának jellemzőit, hogy az megfeleljen a [teljesítmény alaptervének](sql-server-to-managed-instance-performance-baseline.md): 

- A CPU-használat alapkonfigurációjának használatával olyan felügyelt példányokat építhet ki, amelyek megfelelnek a SQL Server által használt magok számának. Szükség lehet az erőforrások méretezésére a [hardver generálási jellemzőinek](../../managed-instance/resource-limits.md#hardware-generation-characteristics)megfelelően. 
- Használja a memóriahasználat alapkonfigurációját egy olyan [virtuális mag-beállítás](../../managed-instance/resource-limits.md#service-tier-characteristics) kiválasztásához, amely megfelel a memória foglalásának. 
- A fájlrendszer alapértékének I/O-késésével választhatja ki a általános célú (az 5 MS-nál nagyobb késést), és üzletileg kritikus (kevesebb, mint 3 MS) szolgáltatási szintet. 
- A várt I/O-teljesítmény eléréséhez használja az alapértéket az adatok és naplófájlok méretének előfoglalásához. 

Az üzembe helyezés során kiválaszthatja a számítási és tárolási erőforrásokat, majd [később is megváltoztathatja azokat a Azure Portal használatával](../../database/scale-resources.md), az alkalmazáshoz való állásidő nélkül. 

> [!IMPORTANT]
> A [felügyelt példányok virtuális hálózati követelményeinek](../../managed-instance/connectivity-architecture-overview.md#network-requirements) bármilyen eltérése megakadályozhatja, hogy új példányokat hozzon létre vagy meglévőket használjon. További információ a meglévő hálózatok [létrehozásáról](../../managed-instance/virtual-network-subnet-create-arm-template.md)   és [konfigurálásáról](../../managed-instance/vnet-existing-add-subnet.md)   . 

Az Azure SQL felügyelt példányaiban (általános célú és üzletileg kritikus) a cél szolgáltatási rétegek kiválasztásakor egy másik fontos szempont, hogy rendelkezésre áll bizonyos szolgáltatások, például a In-Memory OLTP, amelyek csak a üzletileg kritikus szinten érhetők el. 

### <a name="sql-server-vm-alternative"></a>SQL Server VM alternatív megoldás

Előfordulhat, hogy vállalata olyan követelményekkel rendelkezik, amelyek az Azure-ban [Virtual Machines SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) az Azure SQL felügyelt példányának megfelelőbb célját teszik lehetővé. 

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
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Ez az Azure-szolgáltatás támogatja az offline módban való áttelepítést olyan alkalmazások esetében, amelyek az áttelepítési folyamat során leállást biztosítanak. Az online módban való folyamatos áttelepítéstől eltérően az offline módú áttelepítés a teljes adatbázis biztonsági másolatának egyszeri visszaállítását futtatja a forrásról a célra. | 
|[Natív biztonsági mentés és visszaállítás](../../managed-instance/restore-sample-database-quickstart.md) | Az SQL felügyelt példánya támogatja a natív SQL Server adatbázis biztonsági másolatainak (. bak fájlok) visszaállítását. Ez a legegyszerűbb áttelepítési lehetőség azon ügyfelek számára, akik teljes adatbázis-biztonsági mentést biztosíthatnak az Azure Storage-ba. A teljes és a különbözeti biztonsági mentést a cikk későbbi [részében található áttelepítési eszközök című szakasza](#migration-assets) is támogatja és dokumentálja.| 
|[A log Replay szolgáltatás](../../managed-instance/log-replay-service-migrate.md) | Ez a felhőalapú szolgáltatás a SQL Server naplózási technológia alapján engedélyezhető az SQL felügyelt példányain. Ez egy áttelepítési lehetőség azon ügyfelek számára, akik teljes, differenciált és naplózási adatbázist tudnak készíteni az Azure Storage-ba. A log Replay szolgáltatás az Azure Blob Storageról az SQL felügyelt példányra történő biztonsági mentési fájlok visszaállítására szolgál.| 
| | |

Az alábbi táblázat az alternatív áttelepítési eszközöket sorolja fel: 

|**Technológia** |**Leírás**  |
|---------|---------|
|[Tranzakciós replikáció](../../managed-instance/replication-transactional-overview.md) | Adatok replikálása a forrás SQL Server az adatbázis-táblákból az SQL felügyelt példányba egy kiadói előfizetői típus áttelepítési lehetőségének megadásával a tranzakciós konzisztencia fenntartása mellett. | 
|[Tömeges másolás](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| A [tömeges másolási program (BCP) eszköz](/sql/tools/bcp-utility) SQL Server egy példányának adatait másolja egy adatfájlba. Az eszköz használatával exportálja az adatait a forrásból, és importálja az adatfájlt a célként megadott SQL felügyelt példányba. </br></br> A nagy sebességű tömeges másolási műveletek esetében az adatok Azure SQL felügyelt példányba való áthelyezéséhez használja az [intelligens tömeges másolási eszközt](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) az átviteli sebesség maximalizálása érdekében a párhuzamos másolási feladatok előnyeit kihasználva. | 
|[Exportálási varázsló/BACPAC importálása](../../database/database-import.md?tabs=azure-powershell)| A [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) egy. BACPAC kiterjesztésű Windows-fájl, amely egy adatbázis sémáját és az adatkészletet ágyazza be. Használhatja a BACPAC-t az adatok SQL Server forrásból való exportálására és az adatok az Azure SQL felügyelt példányba való visszaimportálására. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  A [másolási tevékenység](../../../data-factory/copy-activity-overview.md) a Azure Data Factory áttelepíti a forrás SQL Server adatbázisainak adatait az SQL felügyelt példányára beépített összekötők és egy [integrációs](../../../data-factory/concepts-integration-runtime.md)modul használatával.</br> </br> A Data Factory számos [összekötőt](../../../data-factory/connector-overview.md) támogat az adatok SQL Server forrásokból az SQL felügyelt példányba való áthelyezéséhez. |

## <a name="compare-migration-options"></a>Áttelepítési lehetőségek összehasonlítása

Az áttelepítési lehetőségek összehasonlításával kiválaszthatja az üzleti igényeinek megfelelő elérési utat. 

A következő táblázat összehasonlítja a javasolt áttelepítési lehetőségeket: 

|Áttelepítési lehetőség  |A következő esetekben használja  |Megfontolandó szempontok  |
|---------|---------|---------|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md) | – Önálló adatbázisok vagy több adatbázis migrálása nagy léptékben. </br> – Az áttelepítési folyamat során az állásidőt is kielégíti. </br> </br> Támogatott források: </br> -SQL Server (2005 – 2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM |  – A nagy léptékű Migrálás automatizálható [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)használatával. </br> – Az áttelepítés befejezésének ideje az adatbázis méretétől függ, és a biztonsági mentési és visszaállítási idő is befolyásolja. </br> -Elegendő állásidőre lehet szükség. |
|[Natív biztonsági mentés és visszaállítás](../../managed-instance/restore-sample-database-quickstart.md) | – Az üzletági alkalmazások különböző adatbázisainak migrálása.  </br> – Gyors és egyszerű áttelepítés külön áttelepítési szolgáltatás vagy eszköz nélkül.  </br> </br> Támogatott források: </br> -SQL Server (2005 – 2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM | – Az adatbázis biztonsági mentése több szálat használ az Azure-Blob Storageba való adatátvitel optimalizálása érdekében, a partneri sávszélesség és az adatbázis mérete azonban hatással lehet az átviteli sebességre. </br> – Az állásidőnek el kell fogadnia a teljes biztonsági mentéshez és visszaállításhoz szükséges időt (amely az adatműveletek mérete).| 
|[A log Replay szolgáltatás](../../managed-instance/log-replay-service-migrate.md) | – Az üzletági alkalmazások különböző adatbázisainak migrálása.  </br> – Az adatbázisok áttelepítéséhez további szabályozásra van szükség.  </br> </br> Támogatott források: </br> -SQL Server (2008 – 2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM | – Az áttelepítés a teljes adatbázis biztonsági mentését vonja maga után SQL Server és másolja a biztonságimásolat-fájlokat az Azure Blob Storageba. A log Replay szolgáltatás az Azure Blob Storageról az SQL felügyelt példányra történő biztonsági mentési fájlok visszaállítására szolgál. </br> – Az áttelepítési folyamat során visszaállított adatbázisok visszaállítási módban lesznek, és nem használhatók olvasásra vagy írásra, amíg a folyamat be nem fejeződik.| 
| | | |

A következő táblázat összehasonlítja az alternatív áttelepítési lehetőségeket: 

|Metódus vagy technológia |A következő esetekben használja |Megfontolandó szempontok  |
|---------|---------|---------|
|[Tranzakciós replikáció](../../managed-instance/replication-transactional-overview.md) | – Az SQL felügyelt példányok adatbázis-tábláira irányuló módosítások folyamatos közzétételével a forrás-adatbázis tábláiban végezheti el a módosításokat. </br> – Teljes vagy részleges adatbázis-áttelepítést végez a kiválasztott táblákból (egy adatbázis részhalmaza).  </br> </br> Támogatott források: </br> -SQL Server (2012 – 2019) bizonyos korlátozásokkal </br> – AWS EC2  </br> -GCP számítási SQL Server VM | </br> – A telepítő viszonylag összetett a többi áttelepítési lehetőséghez képest.   </br> – Folyamatos replikálási lehetőséget biztosít az adatáttelepítéshez (az adatbázisok offline állapotba helyezése nélkül).</br> – A tranzakciós replikáció korlátozásokat tartalmaz, amikor a közzétevőt a forrás SQL Server példányán állítja be. További információért lásd [a közzétételi objektumok korlátozásait](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) .  </br> – A [replikációs tevékenység figyelésének](/sql/relational-databases/replication/monitor/monitoring-replication) képessége elérhető.    |
|[Tömeges másolás](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Teljes vagy részleges adatáttelepítést hajt végre. </br> -Az állásidőt is képes kezelni. </br> </br> Támogatott források: </br> -SQL Server (2005 – 2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM   | – Az adatok forrásból való exportálásához és a célhelyre való importáláshoz szükséges állásidő. </br> – Az exportálásban vagy importálásban használt fájlformátumoknak és adattípusoknak konzisztensnek kell lenniük a táblázat sémái között. |
|[Exportálási varázsló/BACPAC importálása](../../database/database-import.md)| – Az üzletági alkalmazások különböző adatbázisainak migrálása. </br>-Kisebb adatbázisokhoz is használható.  </br>  Nincs szükség külön áttelepítési szolgáltatásra vagy eszközre. </br> </br> Támogatott források: </br> -SQL Server (2005 – 2019) helyszíni vagy Azure-beli virtuális gép </br> – AWS EC2 </br> -AWS RDS </br> -GCP számítási SQL Server VM  |   </br> – Leállást igényel, mert az adatforrást exportálni kell, és a célhelyen kell importálni.   </br> – Az exportálásban vagy importálásban használt fájlformátumoknak és adattípusoknak konzisztensnek kell lenniük a táblázat sémái között, hogy elkerülhető legyen a csonkítás vagy adattípus-eltérési hibák. </br> – Az adatbázisok nagy mennyiségű objektummal való exportálásának ideje jelentősen magasabb lehet. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| – Adatok migrálása és/vagy átalakítása forrás SQL Server adatbázisokból.</br> – A több forrásból származó adatoknak az Azure SQL felügyelt példányba való egyesítése általában az üzleti intelligencia (BI) munkaterhelések esetében.   </br> -Adatáthelyezési folyamatokat kell létrehoznia Data Factoryban az adatok forrásról célhelyre való áthelyezéséhez.   </br> - A [költségeket](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) fontos figyelembe venni, és olyan tényezőkön alapulnak, mint például a folyamat-eseményindítók, a tevékenység-futtatások és az adatáthelyezés időtartama. |
| | | |

## <a name="feature-interoperability"></a>Funkciók együttműködési képessége 

A más SQL Server funkciókra támaszkodó munkaterhelések áttelepítésekor több szempontot is figyelembe kell venni. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

SQL Server Integration Services (SSIS) csomagok és projektek migrálása a SSISDB-ben az Azure SQL felügyelt példányára [Azure Database Migration Service](../../../dms/how-to-migrate-ssis-packages-managed-instance.md)használatával. 

A SSISDB-től kezdődően csak a SSIS-csomagok telepíthetők át a SQL Server 2012-es verzióra. A régebbi SSIS-csomagok konvertálása az áttelepítés előtt. További információt a [Project Conversion oktatóanyagban](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) talál. 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

SQL Server Reporting Services (SSRS) jelentéseket áttelepíthet Power BI többoldalas jelentésekre. A jelentések előkészítését és áttelepítését az [RDL-áttelepítési eszköz](https://github.com/microsoft/RdlMigration) segítségével végezheti el. A Microsoft kifejlesztette ezt az eszközt az Report Definition Language (RDL-) jelentések SSRS-kiszolgálókról Power BIba való átmigrálása érdekében. A GitHubon elérhető, és a migrálási forgatókönyv összes lépését bemutatja. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server Analysis Services SQL Server 2012-es és újabb verziójú táblázatos modellek áttelepíthetők a Azure Analysis Servicesre, amely az Azure-beli Analysis Services táblázatos modellhez tartozó szolgáltatásként szolgáló platformként szolgáló telepítési modell. További információ a helyszíni modellek áttelepítéséről Azure Analysis Services ebben a [videó-oktatóanyagban](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Azt is megteheti, hogy áttelepíti a helyszíni Analysis Services táblázatos modelleket [az új XMLA írási/olvasási végpontok használatával Power bi Premiumre](/power-bi/admin/service-premium-connect-tools). 

### <a name="high-availability"></a>Magas rendelkezésre állás

A SQL Server magas rendelkezésre állású szolgáltatások mindig a feladatátvevő fürt példányain, és az Always On rendelkezésre állási csoportok elavulttá válnak a célként megadott SQL felügyelt példányon. A magas rendelkezésre állású architektúra már beépített [általános célú (standard rendelkezésre állási modell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) és [üzletileg kritikus (prémium rendelkezésre állási modell)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) szolgáltatási rétegekre az SQL felügyelt példányához. A prémium rendelkezésre állási modell olyan olvasási felskálázást is biztosít, amely lehetővé teszi az egyik másodlagos csomóponthoz való csatlakozást csak olvasási célokra.     

Az SQL felügyelt példányában található magas rendelkezésre állású architektúrán túl az [automatikus feladatátvételi csoportok](../../database/auto-failover-group-overview.md) funkció lehetővé teszi a felügyelt példányban lévő adatbázisok replikálásának és feladatátvételének kezelését egy másik régióba. 

### <a name="sql-agent-jobs"></a>SQL-ügynök feladatai

Az [SQL Agent-feladatok](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)áttelepítéséhez használja az offline Azure Database Migration Service lehetőséget. Egyéb esetben a Transact-SQL (T-SQL) által használt feladatok a SQL Server Management Studio használatával, majd manuálisan újra létrehozhatók a célként megadott SQL felügyelt példányon. 

> [!IMPORTANT]
> A Azure Database Migration Service jelenleg csak a T-SQL alrendszer lépéseit támogató feladatokat támogatja. A SSIS csomag lépéseivel rendelkező feladatokat manuálisan kell migrálni. 

### <a name="logins-and-groups"></a>Bejelentkezések és csoportok

Helyezze át az SQL-bejelentkezéseket a SQL Server forrásból az Azure SQL felügyelt példányára offline módban Database Migration Service használatával.  Az áttelepítési varázsló [bejelentkezések kiválasztása](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) paneljén áttelepítheti a bejelentkezéseket a CÉLKÉNT használt SQL-példányra. 

Alapértelmezés szerint a Azure Database Migration Service csak az SQL-bejelentkezések áttelepítését támogatja. Engedélyezheti azonban a Windows-bejelentkezések áttelepítését:

- Győződjön meg arról, hogy a cél SQL felügyelt példányának Azure Active Directory (Azure AD) olvasási hozzáférése van. A globális rendszergazdai szerepkörrel rendelkező felhasználók a Azure Portal használatával konfigurálhatják ezt a hozzáférést.
- Azure Database Migration Service konfigurálása a Windows felhasználói vagy csoportos bejelentkezési áttelepítésének engedélyezéséhez. Ezt a beállítást a Azure Portal, a **konfiguráció** lapon állíthatja be. A beállítás engedélyezése után indítsa újra a szolgáltatást, hogy a módosítások érvénybe lépnek.

A szolgáltatás újraindítása után a Windows-felhasználó vagy-csoport bejelentkezési adatai megjelennek az áttelepítéshez elérhető bejelentkezések listájában. Az áttelepített Windows-felhasználók vagy-csoportok számára a rendszer felszólítja a társított tartománynév megadására. A szolgáltatás felhasználói fiókjai (a tartománynevet az NT AUTHORITY) és a virtuális felhasználói fiókok (a tartománynév NT SZOLGÁLTATÁSsal rendelkező fiókok) nem támogatottak. További információ: [Windows-felhasználók és-csoportok migrálása egy SQL Server példányban az Azure SQL felügyelt példányára T-SQL használatával](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Azt is megteheti, hogy a Microsoft adatáttelepítés-építészek által tervezett [PowerShell-segédprogramot](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) használja. A segédprogram a PowerShell használatával hoz létre egy T-SQL-parancsfájlt a bejelentkezések újbóli létrehozásához, majd kiválasztja az adatbázis felhasználóit a forrásból a célhelyre. 

A PowerShell-segédprogram automatikusan leképezi a Windows Server Active Directory fiókokat az Azure AD-fiókokba, és minden bejelentkezéshez UPN-keresést végezhet a forrás Active Directory példányon. A segédprogram az egyéni kiszolgáló-és adatbázis-szerepköröket, valamint a szerepkör tagságát és a felhasználói engedélyeket. A foglalt adatbázisok még nem támogatottak, és csak a lehetséges SQL Server engedélyek egy részhalmaza van parancsfájlban. 

### <a name="encryption"></a>Titkosítás

Ha transzparens adattitkosítás által védett adatbázisokat telepít át  [](../../database/transparent-data-encryption-tde-overview.md)   egy felügyelt példányra a natív visszaállítási lehetőséggel, [telepítse át a megfelelő tanúsítványt](../../managed-instance/tde-certificate-migrate.md) a forrás SQL Server példányról a cél SQL felügyelt példányra az adatbázis visszaállítása *előtt* . 

### <a name="system-databases"></a>Rendszeradatbázisok

A rendszeradatbázisok visszaállítása nem támogatott. Ha a példány-szintű objektumokat (a Master és a msdb adatbázisokban tárolja) át szeretné telepíteni, a T-SQL használatával parancsfájlokat kell létrehoznia, majd újra létre kell hoznia őket a cél felügyelt példányon. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (memóriára optimalizált táblák)

A SQL Server In-Memory OLTP képességet biztosít. Lehetővé teszi a memóriára optimalizált táblázatok, a memóriára optimalizált táblák és a natív módon lefordított SQL-modulok használatát a nagy átviteli sebességű és kis késésű, tranzakciós feldolgozást igénylő munkaterhelések futtatásához. 

> [!IMPORTANT]
> In-Memory OLTP csak az Azure SQL felügyelt példányának üzletileg kritikus szintjein támogatott. A általános célú szinten nem támogatott.

Ha a helyszíni SQL Server-példányban memóriára optimalizált táblákat vagy memóriára optimalizált táblázatokat használ, és az Azure SQL felügyelt példányára szeretne áttérni, akkor a következők egyikét kell tennie:

- Válassza ki a célként megadott SQL felügyelt példány üzletileg kritikusi szintjét, amely támogatja a In-Memory OLTP.
- Ha az Azure SQL felügyelt példányának általános célú szintjére kíván áttérni, távolítsa el a memóriára optimalizált táblákat, a memóriára optimalizált táblázatos típusokat és a natív módon lefordított SQL-modulokat, amelyek a memóriára optimalizált objektumokat használják az adatbázisok áttelepítése előtt. A következő T-SQL-lekérdezéssel azonosíthatja azokat az objektumokat, amelyeket el kell távolítani az általános célú szintjére való áttelepítés előtt:

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

A memórián belüli technológiákkal kapcsolatos további információkért lásd: [a teljesítmény optimalizálása a memóriában lévő technológiák használatával Azure SQL Database és az Azure SQL felügyelt példányain](../../in-memory-oltp-overview.md).

## <a name="advanced-features"></a>Speciális funkciók 

Ügyeljen arra, hogy kihasználja az SQL felügyelt példányának fejlett felhőalapú funkcióit. Például nem kell aggódnia a biztonsági másolatok kezelésével kapcsolatban, mert a szolgáltatás elvégzi Önt. [A megőrzési időtartamon belül bármely időpontra](../../database/recovery-using-backups.md#point-in-time-restore)visszaállíthatja a szolgáltatást. Emellett nem kell aggódnia a magas rendelkezésre állás beállításával kapcsolatban, mert [a magas rendelkezésre állás beépített](../../database/high-availability-sla.md). 

A biztonság megerősítése érdekében érdemes lehet az [Azure ad-hitelesítést](../../database/authentication-aad-overview.md), a [naplózást](../../managed-instance/auditing-configure.md), a [fenyegetések észlelését](../../database/azure-defender-for-sql.md), a [sor szintű biztonságot](/sql/relational-databases/security/row-level-security)és a [dinamikus adatmaszkolást](/sql/relational-databases/security/dynamic-data-masking)használni.

A speciális felügyeleti és biztonsági funkciók mellett az SQL felügyelt példánya olyan speciális eszközöket biztosít, amelyek segítségével [figyelheti és beállíthatja a számítási feladatokat](../../database/monitor-tune-overview.md). [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) lehetővé teszi, hogy központosított módon figyelje a felügyelt példányok nagy készletét.  [Automatikus hangolás](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   a felügyelt példányok folyamatosan figyelik az SQL-terv végrehajtásának teljesítményét, és automatikusan kijavítja az azonosított teljesítménnyel kapcsolatos problémákat. 

Néhány szolgáltatás csak akkor érhető el, ha az [adatbázis kompatibilitási szintje](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) a legújabb kompatibilitási szintre módosul (150). 

## <a name="migration-assets"></a>Áttelepítési eszközök 

További segítségért tekintse meg a következő, a valós áttelepítési projektekhez fejlesztett forrásokat.

|Objektum  |Leírás  |
|---------|---------|
|[Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Ez az eszköz javasolt "legmegfelelőbb" célként szolgáló platformot, a felhő készültségét és egy alkalmazás/adatbázis szervizelési szintet biztosít a munkaterheléshez. Egyszerű, egykattintásos számítást és jelentéskészítést kínál, amely segít a nagyméretű ingatlan-értékelések felgyorsításában azáltal, hogy automatizált és egységes döntési eljárást biztosít a megcélzott platformokhoz.|
|[DBLoader segédprogram](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|A DBLoader használatával tagolt szövegfájlokból is betöltheti az adatok SQL Serverba való betöltését. Ez a Windows-konzol segédprogram a SQL Server natív ügyfél tömeges betöltési felületét használja. Az interfész a SQL Server összes verzióján működik, valamint az Azure SQL felügyelt példányával együtt.|
|[A helyszíni SQL Server-bejelentkezések áthelyezése az Azure SQL felügyelt példányára](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|A PowerShell-parancsfájlok létrehozhatnak egy T-SQL-parancsfájlt a bejelentkezések újbóli létrehozásához és az adatbázis-felhasználók kiválasztásához a helyszíni SQL Serverból az Azure SQL felügyelt példányára. Az eszköz lehetővé teszi, hogy a Windows Server Active Directory fiókjainak automatikus leképezését Azure AD-fiókokba, valamint a SQL Server natív bejelentkezések igény szerinti áttelepítésére.|
|[Perfmon-adatgyűjtés automatizálása a Logman használatával](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|A Logman eszközt használhatja a perfmon-adatok gyűjtésére (az alapteljesítmény megismerése érdekében) és az áttelepítés céljára vonatkozó javaslatok beszerzésére. Ez az eszköz a logman.exe használatával hozza létre a távoli SQL Server példányon beállított teljesítményszámlálók létrehozására, indítására, leállítására és törlésére szolgáló parancsot.|
|[Adatbázis áttelepítése az Azure SQL felügyelt példányára teljes és különbözeti biztonsági másolatok visszaállításával](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Ez a tanulmány útmutatást és lépéseket biztosít a SQL Serverról az Azure SQL felügyelt példányra történő Migrálás felgyorsításához, ha csak a teljes és különbözeti biztonsági mentések (és a naplók biztonsági mentése nem áll rendelkezésre).|

Az adatsql mérnöki csapat fejlesztette ezeket az erőforrásokat. A csapat alapszintű alapokmánya az adatplatform-áttelepítési projektek a Microsoft Azure-beli adatplatformra való feltiltásának és felgyorsításának feloldása.


## <a name="next-steps"></a>Következő lépések

- Az SQL Server-adatbázisok Azure SQL felügyelt példányra való áttelepítésének megkezdéséhez tekintse meg az [SQL Server az Azure SQL felügyelt példányok áttelepítési útmutatójában](sql-server-to-managed-instance-guide.md).

- Az adatbázis-és adatáttelepítési forgatókönyvekhez, valamint a speciális feladatokhoz segítséget nyújtó szolgáltatások és eszközök mátrixát az [adatok áttelepítéséhez használható szolgáltatások és eszközök](../../../dms/dms-tools-matrix.md)című szakaszban tekintheti meg.

- Az Azure SQL felügyelt példányával kapcsolatos további tudnivalókért tekintse meg a következőt:
   - [Szolgáltatási szintek az Azure SQL felügyelt példányában](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server és az Azure SQL felügyelt példányai közötti különbségek](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 

- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszeréről és bevezetési ciklusáról, tekintse meg a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott költségszámítási és méretezési eljárások az Azure-ba migrált számítási feladatokhoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- A/B teszteléséhez az adatelérési rétegen a következő témakörben talál további információt: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
