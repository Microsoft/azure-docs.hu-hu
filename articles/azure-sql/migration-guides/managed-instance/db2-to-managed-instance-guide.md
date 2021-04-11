---
title: 'DB2 – Azure SQL felügyelt példány: áttelepítési útmutató'
description: Ez az útmutató bemutatja, hogyan telepítheti át IBM DB2-adatbázisait az Azure SQL felügyelt példányaira a DB2 SQL Server Migration Assistant használatával.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 15d94e17bea6a49bb5bf6fdb6c48252365676042
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228163"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-managed-instance"></a>Áttelepítési útmutató: az IBM DB2 és az Azure SQL felügyelt példánya
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Ez az útmutató bemutatja, hogyan telepítheti át IBM DB2-adatbázisait az Azure SQL felügyelt példányaira a DB2-SQL Server Migration Assistant használatával. 

Más áttelepítési útmutatókért lásd: az [Azure Database áttelepítési útmutatói](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Előfeltételek 

A DB2-adatbázis felügyelt SQL-példányra való átirányításához a következők szükségesek:

- Annak ellenőrzéséhez, hogy a [forrás-környezet támogatott](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites)-e.
- [SQL Server Migration Assistant (SSMA) letöltése a DB2-hez](https://www.microsoft.com/download/details.aspx?id=54254).
- Az [Azure SQL felügyelt példányának](../../managed-instance/instance-create-quickstart.md)célként megadott példánya.
- Kapcsolat és megfelelő engedélyek a forrás és a cél eléréséhez. 

## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesítése után készen áll a környezet topológiájának felderítésére és az áttelepítés megvalósíthatóságának értékelésére. 

### <a name="assess-and-convert"></a>Értékelés és átalakítás

Értékelés létrehozása SQL Server Migration Assistant használatával. 

Az értékelés létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg [a SSMA a DB2-hez](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Válassza a **fájl**  >  **új projekt** lehetőséget. 
1. Adja meg a projekt nevét és helyét a projekt mentéséhez. Ezután válassza az Azure SQL felügyelt példány áttelepítési célként lehetőséget a legördülő listából, majd kattintson az **OK gombra**.

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="A projekt részleteinek megadását bemutató képernyőkép.":::


1. A **Kapcsolódás a DB2**-hez mezőben adja meg a DB2-kapcsolat részleteinek értékeit. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="A DB2-példányhoz való kapcsolódás beállításait bemutató képernyőkép.":::


1. Kattintson a jobb gombbal az áttelepíteni kívánt DB2-sémára, majd válassza a **jelentés létrehozása** parancsot. Ekkor létrejön egy HTML-jelentés. Azt is megteheti, hogy kiválasztja a **jelentés létrehozása** elemet a navigációs sávon a séma kiválasztása után.

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Képernyőkép, amely bemutatja, hogyan hozhat létre egy jelentést.":::

1. A HTML-jelentés áttekintésével megismerheti a konverziós statisztikákat és az esetleges hibákat vagy figyelmeztetéseket. A jelentés az Excelben is megnyitható a DB2-objektumok leltárának beszerzéséhez, valamint a séma átalakításához szükséges erőfeszítésekhez. A jelentés alapértelmezett helye a *SSMAProjects* belüli jelentési mappában található.

   Példa: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Képernyőkép a jelentésről, amelyet a rendszer a hibák vagy figyelmeztetések azonosítására áttekint":::


### <a name="validate-data-types"></a>Adattípusok ellenőrzése

Érvényesítse az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez kövesse az alábbi lépéseket: 

1. Válassza az **eszközök** lehetőséget a menüből. 
1. Válassza a **projekt beállításai** lehetőséget. 
1. Válassza a **típus-hozzárendelések** lapot.

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="A séma és típus leképezésének kijelölését bemutató képernyőkép.":::

1. Az egyes táblák típus-hozzárendelését a **DB2 metadata Explorerben** található táblázat kiválasztásával módosíthatja. 

### <a name="convert-schema"></a>Séma konvertálása 

A séma konvertálásához kövesse az alábbi lépéseket:

1. Választható Dinamikus vagy alkalmi lekérdezéseket adhat az utasításokhoz. Kattintson a jobb gombbal a csomópontra, majd válassza az **utasítások hozzáadása** parancsot. 
1. Válassza **a Kapcsolódás az Azure SQL felügyelt példányhoz** lehetőséget. 
    1. Adja meg a kapcsolat adatait az Azure SQL felügyelt példányhoz való kapcsolódáshoz. 
    1. Válassza ki a célként szolgáló adatbázist a legördülő listából, vagy adjon meg egy új nevet, amely esetben a rendszer létrehoz egy adatbázist a célkiszolgálón. 
    1. Adja meg a hitelesítés részleteit. 
    1. Válassza a **Kapcsolódás** lehetőséget.

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="A SQL Serverhoz való kapcsolódáshoz szükséges adatokat bemutató képernyőkép.":::


1. Kattintson a jobb gombbal a sémára, majd válassza a **séma konvertálása** parancsot. Azt is megteheti, hogy a séma kiválasztása után kiválasztja a **séma konvertálása** lehetőséget a felső navigációs sávon.

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Képernyőkép, amely a séma kijelölését és átalakítását mutatja.":::

1. Az átalakítás befejezése után hasonlítsa össze és tekintse át a séma struktúráját a lehetséges problémák azonosításához. A problémák megoldása a javaslatok alapján.

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Képernyőkép, amely a séma szerkezetének összehasonlítását és áttekintését mutatja a lehetséges problémák azonosításához.":::

1. A **kimenet** ablaktáblán válassza az **eredmények áttekintése** lehetőséget. A hibák **listája** ablaktáblán tekintse át a hibákat. 
1. Mentse a projektet helyileg a kapcsolat nélküli séma szervizelési gyakorlatához. A **fájl** menüben válassza a **projekt mentése** elemet. Ez lehetőséget biztosít a forrás-és a célként megadott sémák offline kiértékelésére, és szervizelést hajt végre, mielőtt közzé tudja tenni a sémát az SQL felügyelt példányon.

## <a name="migrate"></a>Migrate

Miután elvégezte az adatbázisok értékelését és az eltérések kezelését, a következő lépés az áttelepítési folyamat végrehajtása.

A séma közzétételéhez és az adatáttelepítés elvégzéséhez kövesse az alábbi lépéseket:

1. Tegye közzé a sémát. Az **Azure SQL felügyelt példány metaadatainak Explorer** **adatbázis** csomópontjában kattintson a jobb gombbal az adatbázisra. Ezután válassza **az adatbázissal való szinkronizálás** lehetőséget.

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Képernyőkép, amely az adatbázissal való szinkronizálás lehetőségét mutatja.":::

1. Telepítse át az adatátvitelt. Kattintson a jobb gombbal az áttelepíteni kívánt adatbázisra vagy objektumra a **DB2 metaadat-kezelőben**, majd válassza az **adatok áttelepíteni** lehetőséget. Azt is megteheti, hogy az **adatok áttelepíthetők** a navigációs sávon. Ha egy teljes adatbázisra szeretné áttelepíteni az adatátvitelt, jelölje be az adatbázis neve melletti jelölőnégyzetet. Ha az adatok áttelepíthetők az egyes táblákból, bontsa ki az adatbázist, majd a **táblák** csomópontot, és jelölje be a tábla melletti jelölőnégyzetet. Ha az adatok kihagyása az egyes táblákból, törölje a jelölőnégyzet jelölését.

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Képernyőkép, amely a séma kiválasztását és az adatáttelepítés kiválasztását mutatja.":::

1. Adja meg a kapcsolati adatokat a DB2 és az SQL felügyelt példányhoz. 
1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést**.  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Képernyőkép, amely bemutatja, hol tekintheti át az adatáttelepítési jelentést.":::

1. Kapcsolódjon az Azure SQL felügyelt példányának példányához [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)használatával. Ellenőrizze az áttelepítést az adatelemzés és a séma áttekintésével:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="A sémának a SQL Server Management Studioban való összehasonlítását bemutató képernyőkép.":::

## <a name="post-migration"></a>A migrálás után 

Az áttelepítés befejezése után át kell haladnia az áttelepítés utáni feladatok sorozatán, hogy minden a lehető leggördülékenyen és hatékonyan működjön.

### <a name="remediate-applications"></a>Alkalmazások szervizelése 

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek elvégzése bizonyos esetekben szükségessé teszi az alkalmazások módosítását.


### <a name="perform-tests"></a>Tesztek végrehajtása

A tesztelés a következő tevékenységekből áll:

1. **Ellenőrzési tesztek fejlesztése**: az adatbázisok áttelepítésének TESZTELÉSéhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.
1. **A tesztkörnyezet beállítása**: a tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.
1. **Ellenőrzési tesztek futtatása**: futtassa az ellenőrző teszteket a forráson és a célhelyen, majd elemezze az eredményeket.
1. **Teljesítménytesztek futtatása**: futtasson teljesítményteszteket a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

## <a name="advanced-features"></a>Speciális funkciók 

Ügyeljen arra, hogy kihasználja az Azure SQL felügyelt példányai által kínált fejlett felhőalapú funkciókat, például a [beépített magas rendelkezésre állást](../../database/high-availability-sla.md), a [fenyegetések észlelését](../../database/azure-defender-for-sql.md), valamint [a számítási feladatok monitorozását és finomhangolását](../../database/monitor-tune-overview.md). 

Egyes SQL Server szolgáltatások csak akkor érhetők el, ha az [adatbázis kompatibilitási szintje](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) a legújabb kompatibilitási szintre módosul. 

## <a name="migration-assets"></a>Áttelepítési eszközök 

További segítségért tekintse meg a következő erőforrásokat, amelyeket a valós idejű migrációs projektek támogatásához fejlesztettek ki:

|Objektum  |Description  |
|---------|---------|
|[Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely segít felgyorsítani a nagyméretű ingatlanok értékelését azáltal, hogy lehetővé teszi a és automatizált és egységes célként megadott platform döntési folyamatát.|
|[DB2 zOS adategységek felderítési és értékelési csomagja](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Ha az SQL-szkriptet egy adatbázison futtatja, az eredményeket a fájlrendszer egyik fájljába exportálhatja. Számos fájlformátum támogatott, beleértve a *. csv fájlt is, így az eredményeket külső eszközökön, például táblázatokban rögzítheti. Ez a módszer akkor lehet hasznos, ha egyszerűen meg szeretné osztani az eredményeket olyan csapatokkal, amelyeken nincs telepítve a Workbench.|
|[IBM DB2 LUW-leltári parancsfájlok és összetevők](https://github.com/microsoft/DataMigrationTeam/blob/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Ez az objektum olyan SQL-lekérdezést tartalmaz, amely az IBM DB2 LUW 11,1 rendszertábláit tartalmazza, és az objektumok számát séma és objektumtípus alapján, az egyes sémákban a "nyers adat" durva becslésével, valamint az egyes sémákban lévő táblák méretezésével, valamint CSV-formátumban tárolt eredményekkel biztosítja.|
|[DB2 LUW – tiszta méretezés az Azure-ban – telepítési útmutató](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Ez az útmutató egy DB2-implementációs csomag kiindulási pontként szolgál. Bár az üzleti követelmények eltérőek lesznek, ugyanez az alapszintű minta is érvényes. Ezt az építészeti mintát az Azure-beli OLAP-alkalmazásokhoz is használhatja.|

Az adatsql mérnöki csapat fejlesztette ezeket az erőforrásokat. A csapat alapszintű alapokmánya az adatplatform-áttelepítési projektek a Microsoft Azure-beli adatplatformra való feltiltásának és felgyorsításának feloldása.

## <a name="next-steps"></a>Következő lépések

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök számára a különböző adatbázis-és adatáttelepítési forgatókönyvek támogatásához lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Az Azure SQL felügyelt példányával kapcsolatos további tudnivalókért tekintse meg a következőt:
   - [Az SQL felügyelt példányának áttekintése](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszeréről és bevezetési ciklusáról, tekintse meg a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott költségszámítási és méretezési eljárások az Azure-ba migrált számítási feladatokhoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
