---
title: 'SQL Server a Azure SQL Databasehoz: áttelepítési útmutató'
description: Ezt az útmutatót követve áttelepítheti SQL Server adatbázisait Azure SQL Databasere.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 03/19/2021
ms.openlocfilehash: e2de694a153276dcace1070d35af44dec1056e03
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564925"
---
# <a name="migration-guide-sql-server-to-azure-sql-database"></a>Áttelepítési útmutató: SQL Server Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Ez az útmutató segítséget nyújt a SQL Server-példány átirányításához Azure SQL Databasere. 

A helyszíni vagy a szolgáltatásban futó SQL Server áttelepíthetők: 

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon-hoz kapcsolódó adatbázis-szolgáltatás (AWS RDS) 
- Számítási motor (Google Cloud Platform-GCP)  
- Felhőbeli SQL a SQL Serverhoz (Google Cloud Platform – GCP) 

További áttelepítési információkért tekintse meg az [áttelepítés áttekintését](sql-server-to-sql-database-overview.md). Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://docs.microsoft.com/data-migration). 

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="Áttelepítési folyamat folyamatábrája":::

## <a name="prerequisites"></a>Előfeltételek 

A SQL Server Azure SQL Databasere való áttelepítéséhez ellenőrizze, hogy rendelkezik-e az alábbi előfeltételekkel: 

- A kiválasztott [áttelepítési módszer](sql-server-to-sql-database-overview.md#compare-migration-options) és a hozzá tartozó eszközök.
- [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) olyan gépre van telepítve, amely képes csatlakozni a forrás SQL Serverhoz.
- A cél [Azure SQL Database](../../database/single-database-create-quickstart.md). 
- Kapcsolat és megfelelő engedélyek a forrás és a cél eléréséhez. 



## <a name="pre-migration"></a>A migrálás előtt

Miután ellenőrizte, hogy a forrás-környezet támogatott-e, kezdje az áttelepítés előtti fázissal. Fedezze fel az összes meglévő adatforrást, mérje fel az áttelepítés megvalósíthatóságát, és azonosítsa az áttelepítést megakadályozó blokkolási problémákat. 

### <a name="discover"></a>Felderítés

A felderítési fázisban vizsgálja meg a hálózatot a szervezet által használt összes SQL Server példány és funkció azonosításához. 

Használja [Azure Migrate](../../../migrate/migrate-services-overview.md) a helyszíni kiszolgálók áttelepítési alkalmasságának értékeléséhez, a teljesítmény-alapú méretezés elvégzéséhez, valamint az Azure-ban való futtatásához szükséges költségbecslés biztosításához. 

Azt is megteheti, hogy a [Microsoft Assessment and Planning Toolkit (a "Map Toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) segítségével értékeli az aktuális informatikai infrastruktúrát. Az eszközkészlet hatékony leltározási, értékelési és jelentéskészítési eszközt biztosít az áttelepítési tervezési folyamat egyszerűsítéséhez. 

További információ a felderítési fázishoz használható eszközökről: az [adatáttelepítési forgatókönyvekhez elérhető szolgáltatások és eszközök](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Kiértékelés 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Miután felderített egy adatforrást, mérje fel azokat a helyszíni SQL Server-adatbázisokat, amelyek áttelepíthetők Azure SQL Database az áttelepítési blokkolók vagy kompatibilitási problémák azonosításához. 

A Data Migration Assistant (4,1-es és újabb verzió) használatával felhasználhatja az adatbázisok kiértékelését: 

- [Azure Target-javaslatok](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU-javaslatok](/sql/dma/dma-sku-recommend-sql-db)

Az alábbi lépéseket követve értékelheti a környezetét az adatbázis-áttelepítési felmérés használatával: 

1. Nyissa meg a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Válassza a **fájl** , majd az **új értékelés** lehetőséget. 
1. Adja meg a projekt nevét, válassza a SQL Server lehetőséget a forráskiszolgáló típusaként, majd válassza a Azure SQL Database lehetőséget a célkiszolgáló típusaként. 
1. Válassza ki a létrehozáshoz használni kívánt értékelési jelentések típusát (ke) t. Például az adatbázis-kompatibilitás és a szolgáltatás paritása. Az értékelés típusa alapján a forrás SQL Serverhoz szükséges engedélyek eltérőek lehetnek.  A DMA a felmérés futtatása előtt kiemeli a kiválasztott tanácsadóhoz szükséges engedélyeket.
    - A **szolgáltatás paritásának** kategóriája a javaslatok széles körét, az Azure-ban elérhető alternatívákat és az áttelepítési projekt megtervezéséhez szükséges lépéseket tartalmazza. (sysadmin engedély szükséges)
    - A **kompatibilitási problémák** kategóriája a részben támogatott vagy nem támogatott kompatibilitási problémákat azonosítja, amelyek letilthatják az áttelepítést, valamint ajánlásokat is tartalmaznak ( `CONNECT SQL` , `VIEW SERVER STATE` és a `VIEW ANY DEFINITION` szükséges engedélyekkel).
1. Adja meg a SQL Server forrás-kapcsolati adatait, és kapcsolódjon a forrás-adatbázishoz.
1. Válassza az **értékelés indítása** lehetőséget. 
1. A folyamat befejezése után válassza ki és tekintse át az áttelepítési blokkolással és a szolgáltatás paritásával kapcsolatos problémákra vonatkozó értékelési jelentéseket. Az értékelési jelentés exportálható egy fájlba is, amely megosztható a szervezet más csoportjaival vagy munkatársaival. 
1. Határozza meg az adatbázis kompatibilitási szintjét, amely az áttelepítés utáni erőfeszítéseket lekicsinyíti.  
1. Azonosítsa a helyszíni számítási feladatok legjobb Azure SQL Database SKU-t. 

További információ: [SQL Server áttelepítési felmérés végrehajtása Data Migration Assistant](/sql/dma/dma-assesssqlonprem)használatával.

Ha az értékelés több blokkoló észlel, hogy megbizonyosodjon róla, hogy az adatbázis nem áll készen a Azure SQL Database áttelepítésre, akkor vegye figyelembe a következőket:

- [Felügyelt Azure SQL-példány](../managed-instance/sql-server-to-managed-instance-overview.md) , ha több példányra kiterjedő függőség van
- [SQL Server az Azure Virtual Machines](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) , ha a felügyelt SQL Database és az SQL-példányok egyike sem felel meg a megfelelő célnak. 



#### <a name="scaled-assessments-and-analysis"></a>Méretezett értékelések és elemzések
Data Migration Assistant támogatja az elemzésre szolgáló értékelési jelentések méretezett értékelésének és összevonásának végrehajtását. 

Ha több olyan kiszolgálóval és adatbázissal rendelkezik, amelyeket méretezéssel kell értékelni és elemezni, hogy az adatközpont szélesebb körű képet nyújtson, tekintse meg a következő hivatkozásokat, ahol további információt talál:

- [Méretezett értékelések végrehajtása a PowerShell használatával](/sql/dma/dma-consolidatereports)
- [Értékelési jelentések elemzése Power BI használatával](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> Ha több adatbázisra, különösen nagyra értékeli az értékeléseket, akkor a [DMA parancssori segédprogrammal](/sql/dma/dma-commandline) is automatizálható, és a további elemzéshez és a cél készültséghez [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) feltölthető.

## <a name="migrate"></a>Migrate

Az áttelepítés előtti fázishoz kapcsolódó feladatok elvégzése után készen áll a séma és az adatok áttelepítésére. 

Migrálja adatait a kiválasztott [áttelepítési módszer](sql-server-to-sql-database-overview.md#compare-migration-options)használatával. 

Ez az útmutató a két legnépszerűbb lehetőséget ismerteti – Data Migration Assistant és Azure Database Migration Service. 

### <a name="data-migration-assistant-dma"></a>Data Migration Assistant (DMA)

Ha a DMA használatával szeretne áttelepíteni egy adatbázist SQL Serverról Azure SQL Databasera, kövesse az alábbi lépéseket: 

1. Töltse le és telepítse a [Migration Assistant adatbázist](https://www.microsoft.com/download/details.aspx?id=53595).
1. Hozzon létre egy új projektet, és válassza az **áttelepítés** lehetőséget a projekt típusaként.
1. Állítsa a forráskiszolgáló típusát **SQL Serverre** , a célkiszolgáló típusát pedig **Azure SQL Databasera**, válassza ki az áttelepítési hatókört **sémaként és adatként** , majd válassza a **Létrehozás** lehetőséget.
1. Az áttelepítési projektben adja meg a forráskiszolgáló adatait, például a kiszolgáló nevét, a kiszolgálóhoz való kapcsolódáshoz szükséges hitelesítő adatokat, valamint az áttelepítendő forrás-adatbázist.
1. A célkiszolgálón adja meg az Azure SQL Database kiszolgáló nevét, a kiszolgálóhoz való kapcsolódáshoz szükséges hitelesítő adatokat, valamint a cél-adatbázist, amelyre át kíván térni.
1. Válassza ki a séma objektumait, és telepítse azokat a cél Azure SQL Database.
1. Végül válassza az **adatáttelepítés indítása** lehetőséget, és figyelje az áttelepítés előrehaladását.

Részletes oktatóanyagért lásd: helyszíni [SQL Server vagy SQL Server Azure-beli virtuális gépeken való Áttelepítésének Azure SQL Database a Data Migration Assistant használatával](/sql/dma/dma-migrateonpremsqltosqldb). 


> [!NOTE]
> - Az importálási sebesség maximalizálása érdekében az adatbázist magasabb szolgáltatási szinten és számítási méretekben méretezheti, így több erőforrást biztosít. A sikeres importálás után vertikálisan leskálázhatja őket.</br>
> - Az importált adatbázis kompatibilitási szintje a forrásadatbázis kompatibilitási szintjétől függ. 


### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

Az adatbázisok SQL Serverról Azure SQL Database DMS használatával történő átmigrálása a következő lépésekkel végezhető el:

1. Ha még nem tette meg, regisztrálja a **Microsoft. DataMigration** erőforrás-szolgáltatót az előfizetésében. 
1. Hozzon létre egy Azure Database Migration Service példányt az Ön által választott kívánt helyen (lehetőleg ugyanabban a régióban, mint a célként megadott Azure SQL Database). Válasszon ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat a DMS-példány üzemeltetéséhez.
1. A DMS-példány létrehozása után hozzon létre egy új áttelepítési projektet, és adja meg a forráskiszolgáló típusát **SQL Server** és a célkiszolgáló típusaként **Azure SQL Databaseként**. Válassza az **Offline adatáttelepítés** lehetőséget a tevékenység típusaként az áttelepítési projekt létrehozási paneljén.
1. Válassza ki a forrás SQL Server részleteit az **áttelepítési forrás** részletei lapon, valamint a cél Azure SQL Database részleteit az **áttelepítési cél** részletei lapon.
1. Képezze le a forrás-és cél-adatbázisokat az áttelepítéshez, majd válassza ki az áttelepíteni kívánt táblákat.
1. Tekintse át az áttelepítés összegzését, és válassza az **áttelepítés futtatása** lehetőséget. Ezután figyelheti az áttelepítési tevékenységet, és ellenőrizheti az adatbázis áttelepítésének folyamatát.

Részletes oktatóanyagért lásd: [SQL Server migrálása Azure SQL Databasera a DMS használatával](../../../dms/tutorial-sql-server-to-azure-sql.md). 

## <a name="data-sync-and-cutover"></a>Adatszinkronizálás és átváltás

Ha olyan áttelepítési beállításokat használ, amelyek folyamatosan replikálják vagy szinkronizálják az adatváltozásokat a forrásról a célra, a forrásadatok és a séma megváltoztathatja és áttérhet a célhelyről. Az adatszinkronizálás során győződjön meg arról, hogy a rendszer rögzíti a forrás összes módosítását, és az áttelepítési folyamat során alkalmazza a célt. 

Miután meggyőződött róla, hogy az adatok megegyeznek a forráson és a célon is, a forrástól a átváltás. Fontos, hogy megtervezze a átváltás folyamatot üzleti vagy alkalmazási csapatokkal, hogy a átváltás során a minimális megszakítás ne legyen hatással az üzletmenet folytonosságára. 

> [!IMPORTANT]
> A átváltás a DMS használatával történő áttelepítések keretében történő végrehajtásával kapcsolatos konkrét lépésekről további információt az [áttelepítési átváltás végrehajtása](../../../dms/tutorial-sql-server-to-azure-sql.md)című témakörben talál.

## <a name="migration-recommendations"></a>Áttelepítési javaslatok

A Azure SQL Databasera való Migrálás felgyorsításához a következő ajánlásokat kell figyelembe vennie:

|  | Erőforrás-tartalom | Ajánlás |
|--|--|--|
| **Forrás (jellemzően a helyszínen)** |Az elsődleges szűk keresztmetszet a forráson belüli áttelepítés során az adatfájlok adatfájljainak, valamint a körültekintően figyelni kívánt adatfájlok késése.  |Az adatio és az adatfájl késése alapján, valamint attól függően, hogy a virtuális gép vagy a fizikai kiszolgáló van-e, a tárolási rendszergazdának kell bejelentkeznie, és meg kell vizsgálnia a lehetőségeket a szűk keresztmetszet csökkentése érdekében. |
|**Cél (Azure SQL Database)**|A legnagyobb korlátozási tényező a log generálási arány és a késés a naplófájlban. A Azure SQL Database legfeljebb 96 MB/s log generálási sebességet érhet el. | A Migrálás felgyorsításához a cél SQL-adatbázis vertikális felskálázása üzletileg kritikus Gen5 8 virtuális mag, hogy a maximális log generálási sebesség 96 MB/s legyen, és a naplófájlhoz is kis késést érjen el. A [nagy kapacitású](../../database/service-tier-hyperscale.md) szolgáltatási szint 100 MB/s naplózási sebességet biztosít a választott szolgáltatási szinttől függetlenül. |
|**Hálózat** |A hálózati sávszélességnek meg kell egyeznie a napló maximális betöltési sebességével (96 MB/s) (768 MB/s) |A helyszíni adatközpontból az Azure-ba irányuló hálózati kapcsolattól függően ellenőrizheti a hálózati sávszélességet (általában az [Azure-ExpressRoute](../../../expressroute/expressroute-introduction.md#bandwidth-options)), hogy megfeleljen a naplók maximális betöltési arányának. |
|**Data Migration Assistant használt virtuális gép (DMA)** |A CPU a DMA-t futtató virtuális gép elsődleges szűk keresztmetszete |Az adatáttelepítés felgyorsításának szempontja a használatával </br>– Azure-beli nagy számítási igényű virtuális gépek </br>-Használjon legalább F8s_v2 (8 virtuális mag) virtuális gépet a DMA futtatásához </br>– Győződjön meg arról, hogy a virtuális gép ugyanabban az Azure-régióban fut, mint a cél |
|**Azure Database Migration Service (DMS)** |Számítási erőforrás-tartalom és adatbázis-objektumok figyelembe vétele a DMS-hez |Használja a prémium 4 virtuális mag. A DMS automatikusan gondoskodik az adatbázis-objektumokról, például a külső kulcsokról, a triggerekről, a megkötésekről és a nem fürtözött indexekről, és nem igényel manuális beavatkozást.  |


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

Ügyeljen arra, hogy kihasználja az SQL Database által kínált fejlett felhőalapú szolgáltatásokat, például a [beépített magas rendelkezésre állást](../../database/high-availability-sla.md), a [fenyegetések észlelését](../../database/azure-defender-for-sql.md), valamint [a számítási feladatok monitorozását és finomhangolását](../../database/monitor-tune-overview.md). 

Néhány SQL Server funkció csak akkor érhető el, ha az [adatbázis kompatibilitási szintje](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) a legújabb kompatibilitási szintre módosul (150). 

További információ: [Azure SQL Database kezelése az áttelepítés után](../../database/manage-data-after-migrating-to-database.md)


## <a name="next-steps"></a>Következő lépések

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egyik mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Ha többet szeretne megtudni SQL Database lásd:
    - [A Azure SQL Database áttekintése](../../database/sql-database-paas-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
