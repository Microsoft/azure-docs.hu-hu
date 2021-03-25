---
title: 'Az Azure SQL felügyelt példányának SQL Servere: áttelepítési útmutató'
description: Ez az útmutató bemutatja, hogyan telepítheti át SQL Server adatbázisait az Azure SQL felügyelt példányaira.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: a1dcb72c30268dd82052e29232e79a485d86f72d
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025305"
---
# <a name="migration-guide-sql-server-to-azure-sql-managed-instance"></a>Áttelepítési útmutató: SQL Server a felügyelt Azure SQL-példányhoz
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Ez az útmutató segítséget nyújt SQL Server példányának az Azure SQL felügyelt példányra való átirányításához. 

A helyszíni vagy a szolgáltatásban futó SQL Server áttelepíthetők: 

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon-hoz kapcsolódó adatbázis-szolgáltatás (AWS RDS) 
- Számítási motor (Google Cloud Platform-GCP)  
- Felhőbeli SQL a SQL Serverhoz (Google Cloud Platform – GCP) 

További áttelepítési információkért tekintse meg az [áttelepítés áttekintését](sql-server-to-managed-instance-overview.md). Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://docs.microsoft.com/data-migration). 

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Áttelepítési folyamat folyamatábrája":::

## <a name="prerequisites"></a>Előfeltételek 

A SQL Server Azure SQL felügyelt példányra való áttelepítéséhez mindenképpen hajtsa végre a következő előfeltételeket: 

- Válassza ki az [áttelepítési módszert](sql-server-to-managed-instance-overview.md#compare-migration-options) , valamint a kiválasztott módszerhez szükséges megfelelő eszközöket.
- Telepítse a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) szolgáltatást egy olyan gépre, amely képes csatlakozni a forráshoz SQL Server
- Kapcsolat és megfelelő engedélyek a forrás és a cél eléréséhez. 



## <a name="pre-migration"></a>A migrálás előtt

Miután ellenőrizte, hogy a forrás-környezet támogatott-e, kezdje az áttelepítés előtti fázissal. Fedezze fel az összes meglévő adatforrást, mérje fel az áttelepítés megvalósíthatóságát, és azonosítsa az áttelepítést megakadályozó blokkolási problémákat.  

### <a name="discover"></a>Felderítés

A felderítési fázisban vizsgálja meg a hálózatot a szervezet által használt összes SQL Server példány és funkció azonosításához. 

A [Azure Migrate](../../../migrate/migrate-services-overview.md) használatával felméri a helyszíni kiszolgálók áttelepítési alkalmasságát, teljesítmény-alapú méretezést hajthat végre, és megbecsülheti az Azure-ban való futtatáshoz szükséges költségbecslést. 

Azt is megteheti, hogy a [Microsoft Assessment and Planning Toolkit (a "Map Toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) segítségével értékeli az aktuális informatikai infrastruktúrát. Az eszközkészlet hatékony leltározási, értékelési és jelentéskészítési eszközt biztosít az áttelepítési tervezési folyamat egyszerűsítéséhez. 

További információ a felderítési fázishoz használható eszközökről: az [adatáttelepítési forgatókönyvekhez elérhető szolgáltatások és eszközök](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Kiértékelés 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Az adatforrások felderítése után mérje fel azokat a helyszíni SQL Server-példányokat, amelyek áttelepíthetők az Azure SQL felügyelt példányaira az áttelepítési blokkolók vagy kompatibilitási problémák azonosításához. 

A Data Migration Assistant (4,1-es és újabb verzió) használatával felhasználhatja az adatbázisok kiértékelését: 

- [Azure Target-javaslatok](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU-javaslatok](/sql/dma/dma-sku-recommend-sql-db)

Az alábbi lépéseket követve értékelheti a környezetét az adatbázis-áttelepítési felmérés használatával: 

1. Nyissa meg a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Válassza a **fájl** , majd az **új értékelés** lehetőséget. 
1. Adja meg a projekt nevét, válassza a SQL Server lehetőséget a forráskiszolgáló típusaként, majd válassza az Azure SQL felügyelt példány célként kiszolgáló típusa lehetőséget. 
1. Válassza ki a létrehozáshoz használni kívánt értékelési jelentések típusát (ke) t. Például az adatbázis-kompatibilitás és a szolgáltatás paritása. Az értékelés típusa alapján a forrás SQL Serverhoz szükséges engedélyek eltérőek lehetnek.  A DMA a felmérés futtatása előtt kiemeli a kiválasztott tanácsadóhoz szükséges engedélyeket.
    - A **szolgáltatás paritásának** kategóriája a javaslatok széles körét, az Azure-ban elérhető alternatívákat és az áttelepítési projekt megtervezéséhez szükséges lépéseket tartalmazza. (sysadmin engedély szükséges)
    - A **kompatibilitási problémák** kategóriája a részben támogatott vagy nem támogatott kompatibilitási problémákat azonosítja, amelyek letilthatják az áttelepítést, valamint ajánlásokat is tartalmaznak ( `CONNECT SQL` , `VIEW SERVER STATE` és a `VIEW ANY DEFINITION` szükséges engedélyekkel).
1. Adja meg a SQL Server forrás-kapcsolati adatait, és kapcsolódjon a forrás-adatbázishoz.
1. Válassza az **értékelés indítása** lehetőséget. 
1. Ha a folyamat befejeződött, válassza ki és tekintse át az áttelepítési blokkolás és a szolgáltatás paritásával kapcsolatos problémákra vonatkozó értékelési jelentéseket. Az értékelési jelentés exportálható egy fájlba is, amely megosztható a szervezet más csoportjaival vagy munkatársaival. 
1. Határozza meg az adatbázis kompatibilitási szintjét, amely az áttelepítés utáni erőfeszítéseket lekicsinyíti.  
1. Azonosítsa a helyszíni számítási feladathoz tartozó legjobb Azure SQL felügyelt példány SKU-t. 

További információ: [SQL Server áttelepítési felmérés végrehajtása Data Migration Assistant](/sql/dma/dma-assesssqlonprem)használatával.

Ha az SQL felügyelt példánya nem megfelelő cél a számítási feladatokhoz, az Azure-beli virtuális gépeken való SQL Server a vállalat számára életképes alternatív cél lehet. 

#### <a name="scaled-assessments-and-analysis"></a>Méretezett értékelések és elemzések

Data Migration Assistant támogatja az elemzésre szolgáló értékelési jelentések méretezett értékelésének és összevonásának végrehajtását. Ha több olyan kiszolgálóval és adatbázissal rendelkezik, amelyeket méretezéssel kell értékelni és elemezni, hogy az adatközpont szélesebb körű képet nyújtson, kattintson az alábbi hivatkozásokra további információért.

- [Méretezett értékelések végrehajtása a PowerShell használatával](/sql/dma/dma-consolidatereports)
- [Értékelési jelentések elemzése Power BI használatával](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>Ha több adatbázisra vonatkozó értékeléseket futtat, a [DMA parancssori segédprogramjának](/sql/dma/dma-commandline) használatával is automatizálható, ami lehetővé teszi az eredmények feltöltését az [Azure Migrateba](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) további elemzés és megcélzott készültség érdekében.

### <a name="create-a-performance-baseline"></a>Teljesítmény-alapterv létrehozása

Ha össze kell hasonlítani a számítási feladatok teljesítményét egy SQL felügyelt példányon a SQL Serveron futó eredeti számítási feladattal, hozzon létre egy, az összehasonlításhoz használandó alapkonfigurációt. További információért lásd a [teljesítmény alaptervét](sql-server-to-managed-instance-performance-baseline.md) . 

### <a name="create-sql-managed-instance"></a>Felügyelt SQL-példány létrehozása 

A felderítési és felmérési fázisban található információk alapján hozzon létre egy megfelelő méretű SQL felügyelt példányt. Ezt a [Azure Portal](../../managed-instance/instance-create-quickstart.md), a [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)vagy egy [Azure Resource Manager (ARM) sablon](../../managed-instance/create-template-quickstart.md)használatával teheti meg. 


## <a name="migrate"></a>Migrate

Az áttelepítés előtti fázishoz kapcsolódó feladatok elvégzése után készen áll a séma és az adatok áttelepítésére. 

Migrálja adatait a kiválasztott [áttelepítési módszer](sql-server-to-managed-instance-overview.md#compare-migration-options)használatával. 

Ez az útmutató ismerteti a két legnépszerűbb lehetőséget – Azure Database Migration Service (DMS) és a natív biztonsági mentést és visszaállítást. 

### <a name="database-migration-service"></a>Database Migration Service

Az áttelepítés a DMS használatával történő végrehajtásához kövesse az alábbi lépéseket:

1. Regisztrálja a **Microsoft. DataMigration** erőforrás-szolgáltatót az előfizetésben, ha első alkalommal végezi el ezt.
1. Hozzon létre egy Azure Database Migration Service-példányt az Ön által választott kívánt helyen (lehetőleg ugyanabban a régióban, mint a cél Azure SQL felügyelt példánya), majd válasszon ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat a DMS-példány üzemeltetéséhez.
1. A DMS-példány létrehozása után hozzon létre egy új áttelepítési projektet, és adja meg a forráskiszolgáló típusát **SQL Server** és a célkiszolgáló típusát **Azure SQL Database felügyelt példányként**. Válassza ki a tevékenység típusát a projekt létrehozása panelen – online vagy offline adatáttelepítés. 
1.  Az áttelepítési **cél** részletei lapon válassza ki a forrás SQL Server részleteit az **áttelepítési forrás** részletei lapon, valamint a cél Azure SQL felügyelt példányának részleteit. Kattintson a **Tovább** gombra.
1. Válassza ki az áttelepíteni kívánt adatbázist. 
1. Adja meg a konfigurációs beállításokat az adatbázis biztonságimásolat-fájljait tartalmazó **SMB hálózati megosztás** megadásához. Használja a Windows felhasználói hitelesítő adatait a DMS használatával, amely hozzáfér a hálózati megosztáshoz. Adja meg az **Azure Storage-fiók adatait**. 
1. Tekintse át az áttelepítési összegzést, majd kattintson az **áttelepítés futtatása** elemre. Ezután figyelheti az áttelepítési tevékenységet, és ellenőrizheti az adatbázis áttelepítésének folyamatát.
1. Az adatbázis visszaállítása után válassza a **Start átváltás** elemet. Az áttelepítési folyamat átmásolja a farok-napló biztonsági mentését, miután elérhetővé tette az SMB hálózati megosztásban, és visszaállítja azt a célhelyen. 
1. Állítsa le az összes bejövő forgalmat a forrás-adatbázisba, és frissítse a kapcsolódási karakterláncot az új Azure SQL felügyelt példány-adatbázisra. 

Az áttelepítési lehetőség részletes ismertetését lásd: [SQL Server áttelepítése egy Azure SQL felügyelt példányra online a DMS használatával](../../../dms/tutorial-sql-server-managed-instance-online.md). 
   


### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás 

Az Azure SQL felügyelt példányainak egyik fő funkciója a gyors és egyszerű adatbázis-áttelepítés lehetővé tétele az `.bak` [Azure Storage](https://azure.microsoft.com/services/storage/)-on tárolt adatbázis-biztonsági mentési () fájlok natív visszaállítása. A biztonsági mentés és visszaállítás egy aszinkron művelet, amely az adatbázis méretétől függ. 

Az alábbi ábra a folyamat magas szintű áttekintését tartalmazza:

![A diagramon SQL Server egy, az Azure Storage-ba irányuló URL-cím és az Azure Storage-ba irányuló URL-cím átadására szolgáló nyíl.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> A biztonsági mentés ideje, feltöltése az Azure Storage-ba, és natív visszaállítási művelet végrehajtása az Azure SQL felügyelt példányain az adatbázis méretétől függ. A nagy adatbázisokhoz való működéshez elegendő állásidőt kell figyelembe venni. 


A biztonsági mentés és a visszaállítás használatával történő Migrálás lépései: 

1. Készítse elő az adatbázis biztonsági mentését az Azure Blob Storage-ba. Használja például a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) [URL-címére történő biztonsági mentést](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . A [Microsoft Azure eszköz](https://go.microsoft.com/fwlink/?LinkID=324399) használatával SQL Server 2012 SP1 CU2 korábbi verziókban is támogathatja az adatbázisokat. 
1. Kapcsolódjon az Azure SQL felügyelt példányához SQL Server Management Studio használatával. 
1. Hozzon létre egy hitelesítő adatot egy közös hozzáférési aláírás használatával, hogy hozzáférjen az Azure Blob Storage-fiókhoz az adatbázis biztonsági másolatai segítségével. Például:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Állítsa vissza a biztonsági mentést az Azure Storage blob-tárolóból. Például: 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Ha a visszaállítás befejeződött, tekintse meg az adatbázist a **Object Explorer** SQL Server Management Studioon belül. 

További információ az áttelepítési lehetőségről: [adatbázis visszaállítása az Azure SQL felügyelt példányára SSMS használatával](../../managed-instance/restore-sample-database-quickstart.md).

> [!NOTE]
> Az adatbázis-visszaállítási művelet aszinkron és újrapróbálható. Előfordulhat, hogy a rendszer hibát jelez SQL Server Management Studio, ha a kapcsolatok megszakadnak, vagy lejár az időtúllépés. Azure SQL Database továbbra is megkísérli visszaállítani az adatbázist a háttérben, és a [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) és [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézetek használatával nyomon követheti a visszaállítás előrehaladását.



## <a name="data-sync-and-cutover"></a>Adatszinkronizálás és átváltás

Ha olyan áttelepítési beállításokat használ, amelyek folyamatosan replikálják vagy szinkronizálják az adatváltozásokat a forrásról a célra, a forrásadatok és a séma megváltoztathatja és áttérhet a célhelyről. Az adatszinkronizálás során győződjön meg arról, hogy a rendszer rögzíti a forrás összes módosítását, és az áttelepítési folyamat során alkalmazza a célt. 

Miután meggyőződött arról, hogy az adatok azonosak a forrás és a cél esetében is, a forrástól a átváltás. Fontos, hogy megtervezze a átváltás folyamatot üzleti vagy alkalmazási csapatokkal, hogy a átváltás során a minimális megszakítás ne legyen hatással az üzletmenet folytonosságára. 

> [!IMPORTANT]
> A átváltás a DMS használatával történő áttelepítések keretében történő végrehajtásával kapcsolatos konkrét lépésekről további információt az [áttelepítési átváltás végrehajtása](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover)című témakörben talál.


## <a name="post-migration"></a>A migrálás után

Miután sikeresen elvégezte az áttelepítési szakaszt, ugorjon át az áttelepítés utáni feladatok sorozatán, és győződjön meg arról, hogy minden zökkenőmentes és hatékony működést biztosít. 

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez és a teljesség ellenőrzéséhez, valamint a számítási feladatok teljesítményével kapcsolatos problémák kezeléséhez. 

### <a name="remediate-applications"></a>Alkalmazások szervizelése 

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek végrehajtása bizonyos esetekben szükségessé teszi az alkalmazások módosítását.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységekből áll:

1. **Ellenőrzési tesztek fejlesztése**: az adatbázisok áttelepítésének TESZTELÉSéhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.
1. **Tesztkörnyezet beállítása**: a tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.
1. **Ellenőrzési tesztek futtatása**: futtassa az ellenőrző teszteket a forráson és a célhelyen, majd elemezze az eredményeket.
1. **Teljesítménytesztek futtatása**: futtasson teljesítménytesztet a forráson és a célhelyen, majd elemezze és hasonlítsa össze az eredményeket.


## <a name="leverage-advanced-features"></a>Speciális funkciók kihasználása 

Ügyeljen arra, hogy kihasználja a felügyelt SQL-példányok által kínált fejlett felhőalapú funkciókat, például a [beépített magas rendelkezésre állást](../../database/high-availability-sla.md), a [fenyegetések észlelését](../../database/azure-defender-for-sql.md), valamint [a számítási feladatok monitorozását és finomhangolását](../../database/monitor-tune-overview.md). 

[Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) lehetővé teszi, hogy központosított módon figyelje a felügyelt példányok nagy készletét.

Néhány SQL Server funkció csak akkor érhető el, ha az [adatbázis kompatibilitási szintje](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) a legújabb kompatibilitási szintre módosul (150). 


## <a name="next-steps"></a>Következő lépések

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