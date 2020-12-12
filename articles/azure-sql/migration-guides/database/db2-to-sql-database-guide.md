---
title: 'DB2 – SQL Database: áttelepítési útmutató'
description: Kövesse ezt az útmutatót a DB2-adatbázisok Azure SQL Databaseba való átirányításához.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 3e216c4aeb8d1206011ff9cca2d80900c23b2399
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356344"
---
# <a name="migration-guide-db2-to-sql-database"></a>Áttelepítési útmutató: DB2 – SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ez az útmutató azt ismerteti, hogy a DB2-adatbázisokat a DB2-SQL Server Migration Assistant használatával Azure SQL Database áttelepíteni. 

Más forgatókönyvek esetén tekintse meg az [adatbázis áttelepítési Útmutatóját](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Előfeltételek 

A DB2-adatbázis SQL Databasera való áttelepíthető a következőkre van szüksége:

- annak ellenőrzéséhez, hogy a forrás-környezet támogatott-e.
- [SQL Server Migration Assistant (SSMA) letöltése a DB2-hez](https://www.microsoft.com/download/details.aspx?id=54254).
- a cél [Azure SQL Database](../../database/single-database-create-quickstart.md).


## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesítése után készen áll a környezet topológiájának felderítésére és az áttelepítés megvalósíthatóságának értékelésére. 

### <a name="assess-and-convert"></a>Értékelés és átalakítás

Értékelés létrehozása SQL Server Migration Assistant használatával (SSMA). 

Az értékelés létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg SQL Server Migration Assistant (SSMA) a DB2-hez. 
1. Válassza a **fájl** , majd az **új projekt** lehetőséget. 
1. Adja meg a projekt nevét, a kívánt helyet a projekt mentéséhez, majd válassza a Azure SQL Database az áttelepítési célként lehetőséget a legördülő menüből. Válassza az **OK** lehetőséget.  

   :::image type="content" source="media/db2-to-sql-database-guide/new-project.png" alt-text="Adja meg a projekt részleteit, és kattintson az OK gombra a mentéshez.":::


1. Adja meg az értékeket a DB2-kapcsolat részleteihez a **Kapcsolódás DB2-** hez párbeszédpanelen. 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-db2.png" alt-text="Kapcsolódás a DB2-példányhoz":::


1. Kattintson a jobb gombbal az áttelepíteni kívánt DB2-sémára, majd válassza a **jelentés létrehozása** parancsot. Ekkor létrejön egy HTML-jelentés. Azt is megteheti, hogy kiválasztja a **jelentés létrehozása** elemet a navigációs sávon a séma kiválasztása után. 

   :::image type="content" source="media/db2-to-sql-database-guide/create-report.png" alt-text="Kattintson a jobb gombbal a sémára, és válassza a jelentés létrehozása lehetőséget.":::

1. A HTML-jelentés áttekintésével megismerheti a konverziós statisztikákat és az esetleges hibákat vagy figyelmeztetéseket. A jelentés az Excelben is megnyitható a DB2-objektumok leltárának beszerzéséhez, valamint a séma átalakításához szükséges erőfeszítésekhez. A jelentés alapértelmezett helye a SSMAProjects belüli jelentési mappában található.

   Például: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-database-guide/report.png" alt-text="A hibák vagy figyelmeztetések azonosítására szolgáló jelentés áttekintése":::


### <a name="validate-data-types"></a>Adattípusok ellenőrzése

Érvényesítse az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez kövesse az alábbi lépéseket: 

1. Válassza az **eszközök** lehetőséget a menüből. 
1. Válassza a **projekt beállításai** lehetőséget. 
1. Válassza a **típus-hozzárendelések** lapot. 

   :::image type="content" source="media/db2-to-sql-database-guide/type-mapping.png" alt-text="Válassza ki a sémát, majd írja be a leképezést":::

1. Az egyes táblák típus-hozzárendelését a **DB2 metadata Explorerben** található táblázat kiválasztásával módosíthatja. 

### <a name="schema-conversion"></a>Séma átalakítása 

A séma konvertálásához kövesse az alábbi lépéseket:

1. Választható Dinamikus vagy alkalmi lekérdezéseket adhat az utasításokhoz. Kattintson a jobb gombbal a csomópontra, majd válassza az **utasítások hozzáadása** parancsot. 
1. Válassza **a kapcsolódás Azure SQL Database** lehetőséget. 
    1. Adja meg a kapcsolat adatait az adatbázis Azure SQL Database-ben való összekapcsolásához. 
    1. Válassza ki a cél SQL Database a legördülő menüből. 
    1. Válassza a **Kapcsolódás** lehetőséget. 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-sql-database.png" alt-text="Az Azure-beli logikai kiszolgálóhoz való kapcsolódáshoz adja meg a részleteket.":::


1. Kattintson a jobb gombbal a sémára, majd válassza a **séma konvertálása** parancsot. Azt is megteheti, hogy a séma kiválasztása után kiválasztja a **séma konvertálása** lehetőséget a felső navigációs sávon. 

   :::image type="content" source="media/db2-to-sql-database-guide/convert-schema.png" alt-text="Kattintson a jobb gombbal a sémára, és válassza a séma konvertálása parancsot.":::

1. Az átalakítás befejezése után hasonlítsa össze és tekintse át a séma struktúráját a lehetséges problémák azonosításához és a javaslatok alapján történő kezeléséhez. 

   :::image type="content" source="media/db2-to-sql-database-guide/compare-review-schema-structure.png" alt-text="Hasonlítsa össze és tekintse át a séma struktúráját a lehetséges problémák azonosításához és az ajánlások alapján történő kezeléséhez.":::

1. Mentse a projektet helyileg a kapcsolat nélküli séma szervizelési gyakorlatához. Válassza a **projekt mentése** lehetőséget a **fájl** menüből. 


## <a name="migrate"></a>Migrate

Miután elvégezte az adatbázisok értékelését és az eltérések kezelését, a következő lépés az áttelepítési folyamat végrehajtása.

A séma közzétételéhez és az adatáttelepítés elvégzéséhez kövesse az alábbi lépéseket:

1. Tegye közzé a sémát: kattintson a jobb gombbal az adatbázisra az **Azure SQL Database metadata Explorer** **adatbázisok** csomópontjában, majd válassza az **adatbázissal való szinkronizálás** lehetőséget.

   :::image type="content" source="media/db2-to-sql-database-guide/synchronize-with-database.png" alt-text="Kattintson a jobb gombbal az adatbázisra, és válassza az adatbázissal való szinkronizálás lehetőséget.":::

1. Telepítse át az adatokat: kattintson a jobb gombbal a sémára a **DB2 metadata Explorerben** , és válassza az **adatok áttelepíteni** lehetőséget. 

   :::image type="content" source="media/db2-to-sql-database-guide/migrate-data.png" alt-text="Kattintson a jobb gombbal a sémára, és válassza az adatáttelepítés lehetőséget.":::

1. Adja meg a DB2 és a Azure SQL Database kapcsolati adatait. 
1. Tekintse meg az **adatáttelepítési jelentést**. 

   :::image type="content" source="media/db2-to-sql-database-guide/data-migration-report.png" alt-text="Az adatáttelepítési jelentés áttekintése":::

1. Kapcsolódjon a Azure SQL Databasehoz a SQL Server Management Studio használatával, és ellenőrizze az áttelepítést az adatelemzés és a séma áttekintésével. 

   :::image type="content" source="media/db2-to-sql-database-guide/compare-schema-in-ssms.png" alt-text="A séma összehasonlítása a SSMS":::

## <a name="post-migration"></a>A migrálás után 

Miután sikeresen elvégezte az áttelepítési szakaszt, át kell haladnia az áttelepítés utáni feladatok sorozatán, hogy minden a lehető legzökkenőmentesen és hatékonyan működjön.

### <a name="remediate-applications"></a>Alkalmazások szervizelése 

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek elvégzése bizonyos esetekben szükségessé teszi az alkalmazások módosítását.


### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységekből áll:

1. **Ellenőrzési tesztek fejlesztése**: az adatbázisok áttelepítésének TESZTELÉSéhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.
1. **Tesztkörnyezet beállítása**: a tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.
1. **Ellenőrzési tesztek futtatása**: futtassa az ellenőrző teszteket a forráson és a célhelyen, majd elemezze az eredményeket.
1. **Teljesítménytesztek futtatása**: futtasson teljesítménytesztet a forráson és a célhelyen, majd elemezze és hasonlítsa össze az eredményeket.

   > [!NOTE]
   > A Migrálás utáni ellenőrzési tesztek fejlesztésére és futtatására vonatkozó segítségért vegye figyelembe a partner [QuerySurge](https://www.querysurge.com/company/partners/microsoft)elérhető adatminőségi megoldást. 


## <a name="leverage-advanced-features"></a>Speciális funkciók kihasználása 

Ügyeljen arra, hogy kihasználja az SQL Database által kínált fejlett felhőalapú szolgáltatásokat, például a [beépített magas rendelkezésre állást](../../database/high-availability-sla.md), a [fenyegetések észlelését](../../database/azure-defender-for-sql.md), valamint [a számítási feladatok monitorozását és finomhangolását](../../database/monitor-tune-overview.md). 


Néhány SQL Server funkció csak akkor érhető el, ha az [adatbázis kompatibilitási szintje](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) a legújabb kompatibilitási szintre módosul (150). 

## <a name="migration-assets"></a>Áttelepítési eszközök 

További segítségért tekintse meg a következő erőforrásokat, amelyeket a valós idejű migrációs projektek támogatásához fejlesztettek ki:

|Objektum  |Leírás  |
|---------|---------|
|[Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely segít felgyorsítani a nagyméretű ingatlanok értékelését azáltal, hogy lehetővé teszi a és automatizált és egységes célként megadott platform döntési folyamatát.|
|[DB2 zOS adategységek felderítési és értékelési csomagja](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Ha az SQL-szkriptet egy adatbázison futtatja, az eredményeket a fájlrendszer egyik fájljába exportálhatja. Számos fájlformátum támogatott, beleértve a *. csv fájlt is, így az eredményeket külső eszközökön, például táblázatokban rögzítheti. Ez a módszer akkor lehet hasznos, ha egyszerűen meg szeretné osztani az eredményeket olyan csapatokkal, amelyeken nincs telepítve a Workbench.|
|[IBM DB2 LUW-leltári parancsfájlok és összetevők](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Ez az objektum olyan SQL-lekérdezést tartalmaz, amely az IBM DB2 LUW 11,1 rendszertábláit tartalmazza, és az objektumok számát séma és objektumtípus alapján, az egyes sémákban található nyers adatmennyiséget, valamint az egyes sémákban lévő táblák méretezését, valamint CSV-formátumban tárolt eredményeket tartalmaz.|
|[DB2 LUW – tiszta méretezés az Azure-ban – telepítési útmutató](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Ez az útmutató egy DB2-implementációs csomag kiindulási pontként szolgál. Míg az üzleti követelmények eltérőek lesznek, ugyanez az alapszintű minta is érvényes. Ez az építészeti minta az Azure-beli OLAP-alkalmazásokhoz is használható.|

Ezek az erőforrások az Azure adatcsoport-mérnöki csapat által szponzorált adatsql ninja program részeként lettek kifejlesztve. Az adatelemzési program alapszintű alapokmánya az, hogy feloldja az összetett modernizációt, és az adatplatform-migrációs lehetőségeket a Microsoft Azure-beli adatplatformján is felgyorsítja. Ha úgy gondolja, hogy a szervezete szeretne részt venni az adatsql ninja programban, forduljon a fiókhoz, és kérje meg, hogy küldje el a jelölést.



## <a name="next-steps"></a>Következő lépések

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egyik mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Ha többet szeretne megtudni Azure SQL Database lásd:
   - [A SQL Database áttekintése](../../database/sql-database-paas-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).