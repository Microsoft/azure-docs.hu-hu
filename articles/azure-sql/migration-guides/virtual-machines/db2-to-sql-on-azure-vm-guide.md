---
title: 'DB2 – SQL Server Azure-beli virtuális gépeken: áttelepítési útmutató'
description: Ez az útmutató a DB2-adatbázis áttelepítését mutatja be SQL Server Azure-beli virtuális gépeken a DB2-SQL Server Migration Assistant használatával.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: c461c7c5b25d3972cf5f870a13e1d618dc57e159
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026053"
---
# <a name="migration-guide-db2-to-sql-server-on-azure-vms"></a>Áttelepítési útmutató: DB2 – SQL Server Azure-beli virtuális gépeken
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Ez az áttelepítési útmutató azt ismerteti, hogyan telepítheti át a felhasználói adatbázisokat a DB2-ből SQL Server Azure-beli virtuális gépeken a DB2-SQL Server Migration Assistant használatával. 

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://docs.microsoft.com/data-migration). 


## <a name="prerequisites"></a>Előfeltételek

A DB2-adatbázis SQL Serverra való áttelepíthető a következőkre van szüksége:

- annak ellenőrzéséhez, hogy a [forrás-környezet támogatott](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites)-e.
- [SQL Server Migration Assistant (SSMA) a DB2-hez](https://www.microsoft.com/download/details.aspx?id=54254).
- [Kapcsolat](../../virtual-machines/windows/ways-to-connect-to-sql.md) a forrás-és a SQL Server VM között az Azure-ban. 
- Egy cél [SQL Server az Azure-beli virtuális gépen](../../virtual-machines/windows/create-sql-vm-portal.md). 



## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesítése után készen áll a környezet topológiájának felderítésére és az áttelepítés megvalósíthatóságának értékelésére. 


### <a name="assess"></a>Kiértékelés 

A DB2-hez SQL Server Migration Assistant (SSMA) használatával tekintse át az adatbázis-objektumokat és az adatforrásokat, és mérje fel az adatbázisok áttelepítését. 

Az értékelés létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg [SQL Server Migration Assistant (SSMA) a DB2-hez](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Válassza a **fájl** , majd az **új projekt** lehetőséget. 
1. Adja meg a projekt nevét, egy helyet a projekt mentéséhez, majd válasszon egy SQL Server áttelepítési célt a legördülő menüből. Válassza **az OK gombot**:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Adja meg a projekt részleteit, és kattintson az OK gombra a mentéshez.":::


1. Adja meg az értékeket a DB2-kapcsolat részleteihez a **Kapcsolódás DB2-** hez párbeszédpanelen:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Kapcsolódás a DB2-példányhoz":::


1. Kattintson a jobb gombbal az áttelepíteni kívánt DB2-sémára, majd válassza a **jelentés létrehozása** parancsot. Ekkor létrejön egy HTML-jelentés. Azt is megteheti, hogy kiválasztja a **jelentés létrehozása** lehetőséget a navigációs sávon a séma kiválasztása után:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Kattintson a jobb gombbal a sémára, és válassza a jelentés létrehozása lehetőséget.":::

1. A HTML-jelentés áttekintésével megismerheti a konverziós statisztikákat és az esetleges hibákat vagy figyelmeztetéseket. A jelentés az Excelben is megnyitható a DB2-objektumok leltárának beszerzéséhez, valamint a séma átalakításához szükséges erőfeszítésekhez. A jelentés alapértelmezett helye a SSMAProjects belüli jelentési mappában található.

   Példa: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="A hibák vagy figyelmeztetések azonosítására szolgáló jelentés áttekintése":::


### <a name="validate-data-types"></a>Adattípusok ellenőrzése

Érvényesítse az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez kövesse az alábbi lépéseket: 

1. Válassza az **eszközök** lehetőséget a menüből. 
1. Válassza a **projekt beállításai** lehetőséget. 
1. Válassza a **típus-hozzárendelések** fület:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Válassza ki a sémát, majd írja be a leképezést":::

1. Az egyes táblák típus-hozzárendelését a **DB2 metadata Explorerben** található táblázat kiválasztásával módosíthatja. 

### <a name="convert-schema"></a>Séma konvertálása 

A séma konvertálásához kövesse az alábbi lépéseket:

1. Választható Dinamikus vagy alkalmi lekérdezéseket adhat az utasításokhoz. Kattintson a jobb gombbal a csomópontra, majd válassza az **utasítások hozzáadása** parancsot. 
1. Válassza **a kapcsolódás SQL Server** lehetőséget. 
    1. Adja meg a kapcsolat adatait az Azure-beli virtuális gépen SQL Server példányához való kapcsolódáshoz. 
    1. Válasszon egy meglévő adatbázishoz a célkiszolgálón, vagy adjon meg egy új nevet egy új adatbázis létrehozásához a célkiszolgálón. 
    1. Adja meg a hitelesítés részleteit. 
    1. Válassza a **kapcsolat** elemet:

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Kapcsolódás a SQL Server Azure-beli virtuális gépen":::

1. Kattintson a jobb gombbal a sémára, majd válassza a **séma konvertálása** parancsot. Választhatja a **séma konvertálása** lehetőséget is a felső navigációs sávon a séma kiválasztása után:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Kattintson a jobb gombbal a sémára, és válassza a séma konvertálása parancsot.":::

1. Az átalakítás befejezése után hasonlítsa össze és tekintse át a séma struktúráját a lehetséges problémák azonosításához és a javaslatok alapján történő kezeléséhez: 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Hasonlítsa össze és tekintse át a séma struktúráját a lehetséges problémák azonosításához és az ajánlások alapján történő kezeléséhez.":::

1. Válassza az **eredmények áttekintése** lehetőséget a kimenet ablaktáblán, és tekintse át a hibákat a hibák **listája** ablaktáblán. 
1. Mentse a projektet helyileg a kapcsolat nélküli séma szervizelési gyakorlatához. Válassza a **projekt mentése** lehetőséget a **fájl** menüből. Ez lehetőséget biztosít a forrás-és a célként megadott sémák offline állapotba helyezésére és szervizelésre, mielőtt közzé tudja tenni a sémát az Azure-beli virtuális gépen SQL Server.


## <a name="migrate"></a>Migrate

Miután elvégezte az adatbázisok értékelését és az eltérések kezelését, a következő lépés az áttelepítési folyamat végrehajtása.

A séma közzétételéhez és az adatáttelepítés elvégzéséhez kövesse az alábbi lépéseket:

1. Tegye közzé a sémát: kattintson a jobb gombbal az adatbázisra az **SQL Server metadata Explorer** **adatbázisok** csomópontjában, majd válassza a **szinkronizálás az adatbázissal** lehetőséget:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Kattintson a jobb gombbal az adatbázisra, és válassza az adatbázissal való szinkronizálás lehetőséget.":::

1. Migrálja az adatokat: kattintson a jobb gombbal az áttelepíteni kívánt adatbázisra vagy objektumra a **DB2 metaadat-kezelőben**, majd válassza az **adatok áttelepíteni** lehetőséget. Választhatja azt is, hogy az **adatok áttelepíthetők** a legfelső szintű navigációs sávon. Ha egy teljes adatbázisra szeretné áttelepíteni az adatátvitelt, jelölje be az adatbázis neve melletti jelölőnégyzetet. Ha az adatok áttelepíthetők az egyes táblákból, bontsa ki az adatbázist, majd a táblák csomópontot, és jelölje be a tábla melletti jelölőnégyzetet. Ha az adatok kihagyása az egyes táblákból, törölje a jelet a jelölőnégyzetből:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Kattintson a jobb gombbal a sémára, és válassza az adatáttelepítés lehetőséget.":::

1. Adja meg a kapcsolat részleteit mind a DB2-, mind a SQL Server-példányokhoz. 
1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést**:  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Az adatáttelepítési jelentés áttekintése":::

1.  Kapcsolódjon a SQL Serverhoz az Azure VM-példányon [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) használatával, és ellenőrizze az áttelepítést az adatelemzés és a séma áttekintésével:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="A séma összehasonlítása a SSMS":::

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


## <a name="migration-assets"></a>Áttelepítési eszközök 

További segítségért tekintse meg a következő erőforrásokat, amelyeket a valós idejű migrációs projektek támogatásához fejlesztettek ki:

|Objektum  |Leírás  |
|---------|---------|
|[Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely segít felgyorsítani a nagyméretű ingatlanok értékelését azáltal, hogy lehetővé teszi a és automatizált és egységes célként megadott platform döntési folyamatát.|
|[DB2 zOS adategységek felderítési és értékelési csomagja](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Ha az SQL-szkriptet egy adatbázison futtatja, az eredményeket a fájlrendszer egyik fájljába exportálhatja. Számos fájlformátum támogatott, beleértve a *. csv fájlt is, így az eredményeket külső eszközökön, például táblázatokban rögzítheti. Ez a módszer akkor lehet hasznos, ha egyszerűen meg szeretné osztani az eredményeket olyan csapatokkal, amelyeken nincs telepítve a Workbench.|
|[IBM DB2 LUW-leltári parancsfájlok és összetevők](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Ez az objektum olyan SQL-lekérdezést tartalmaz, amely az IBM DB2 LUW 11,1 rendszertábláit tartalmazza, és az objektumok számát séma és objektumtípus alapján, az egyes sémákban található nyers adatmennyiséget, valamint az egyes sémákban lévő táblák méretezését, valamint CSV-formátumban tárolt eredményeket tartalmaz.|
|[DB2 LUW – tiszta méretezés az Azure-ban – telepítési útmutató](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/db2%20PureScale%20on%20Azure.pdf)|Ez az útmutató egy DB2-implementációs csomag kiindulási pontként szolgál. Míg az üzleti követelmények eltérőek lesznek, ugyanez az alapszintű minta is érvényes. Ez az építészeti minta az Azure-beli OLAP-alkalmazásokhoz is használható.|

Ezek az erőforrások az Azure adatcsoport-mérnöki csapat által szponzorált adatsql ninja program részeként lettek kifejlesztve. Az adatelemzési program alapszintű alapokmánya az, hogy feloldja az összetett modernizációt, és az adatplatform-migrációs lehetőségeket a Microsoft Azure-beli adatplatformján is felgyorsítja. Ha úgy gondolja, hogy a szervezete szeretne részt venni az adatsql ninja programban, forduljon a fiókhoz, és kérje meg, hogy küldje el a jelölést.

## <a name="next-steps"></a>Következő lépések

Az áttelepítés után tekintse át az [áttelepítés utáni érvényesítési és optimalizálási útmutatót](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egyik mátrixa, amely segítséget nyújt a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok számára, lásd: [adatáttelepítési szolgáltatások és eszközök](../../../dms/dms-tools-matrix.md).

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://datamigration.microsoft.com/). 

A videó tartalma:
- [Az áttelepítési út áttekintése](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)