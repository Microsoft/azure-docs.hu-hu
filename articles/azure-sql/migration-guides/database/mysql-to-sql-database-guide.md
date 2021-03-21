---
title: 'MySQL – Azure SQL Database: áttelepítési útmutató'
description: Ez az útmutató bemutatja, hogyan telepíthet át MySQL-adatbázisokat Azure SQL Database a MySQL-hez készült SQL Server Migration Assistant (SSMA for MySQL) használatával.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: ff7b2115b396bf42cdeffa9c58bffb1802e980d1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721885"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Áttelepítési útmutató: MySQL – Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ez az útmutató a MySQL-adatbázis migrálása Azure SQL Database a MySQL-hez készült SQL Server Migration Assistant (SSMA for MySQL) használatával. 

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Előfeltételek

Ha át szeretné telepíteni a MySQL-adatbázist Azure SQL Databasere, a következőkre lesz szüksége:

- annak ellenőrzéséhez, hogy a forrás-környezet támogatott-e. Jelenleg a MySQL 5,6 és a 5,7 támogatott. 
- [MySQL-SQL Server Migration Assistant](https://www.microsoft.com/download/confirmation.aspx?id=54257)


## <a name="pre-migration"></a>A migrálás előtt 

Az előfeltételek teljesítése után készen áll a környezet topológiájának felderítésére és az áttelepítés megvalósíthatóságának értékelésére.

### <a name="assess"></a>Kiértékelés 

A [MySQL SQL Server Migration Assistant](https://www.microsoft.com/download/confirmation.aspx?id=54257)használatával áttekintheti az adatbázis-objektumokat és-adatforrásokat, és kiértékelheti az áttelepítéshez szükséges adatbázisokat.

Értékelés létrehozásához hajtsa végre az alábbi lépéseket.

1. Nyissa meg SQL Server Migration Assistant a MySQL-hez. 
1. Válassza a menüben a **fájl** lehetőséget, majd válassza az **új projekt** lehetőséget. Adja meg a projekt nevét, a projekt mentéséhez szükséges helyet. Az áttelepítési célként válassza a **Azure SQL Database** lehetőséget. 

   ![Új projekt](./media/mysql-to-sql-database-guide/new-project.png)

1. Válassza a **Kapcsolódás a MySQL** -hez lehetőséget, és adja meg a kapcsolati adatokat a MySQL-kiszolgáló csatlakoztatásához. 

   ![Kapcsolódás a MySQL-hez](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Kattintson a jobb gombbal a MySQL-sémára a **MySQL metadata Explorerben** , és válassza a **jelentés létrehozása** lehetőséget. Azt is megteheti, hogy a felső vonal navigációs sávján kiválasztja a **jelentés létrehozása** lehetőséget. 

   ![Jelentés létrehozása](./media/mysql-to-sql-database-guide/create-report.png)

1. Tekintse át az átalakítási statisztikák HTML-jelentését, valamint a hibákat és a figyelmeztetéseket. Elemezze, hogy megértse az átalakítási problémákat és a megoldásokat. 

   Ez a jelentés a SSMA projects mappából is elérhető az első képernyőn kiválasztott módon. A fenti példában keresse meg a report.xml fájlt a következő helyről:
 
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   és nyissa meg az Excelben a MySQL-objektumok leltárának beszerzéséhez és a séma átalakításának végrehajtásához szükséges erőfeszítésekhez.

    ![Átalakítási jelentés](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>Adattípusok ellenőrzése

Érvényesítse az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez kövesse az alábbi lépéseket: 

1. Válassza az **eszközök** lehetőséget a menüből. 
1. Válassza a **projekt beállításai** lehetőséget. 
1. Válassza a **típus-hozzárendelések** lapot. 

   ![Típus-hozzárendelések](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Az egyes táblák típus-hozzárendelését úgy módosíthatja, hogy kijelöli a táblázatot a **MySQL metadata Explorerben**. 

### <a name="convert-schema"></a>Séma konvertálása 

A séma konvertálásához kövesse az alábbi lépéseket: 

1. Választható Dinamikus vagy alkalmi lekérdezések konvertálásához kattintson a jobb gombbal a csomópontra, majd válassza az **utasítás hozzáadása** parancsot. 
1. A legfelső szintű navigációs sávon válassza a **kapcsolódás Azure SQL Database** lehetőséget, és adja meg a kapcsolat részleteit. Választhat, hogy meglévő adatbázishoz csatlakozik, vagy új nevet ad meg, ebben az esetben a rendszer létrehoz egy adatbázist a célkiszolgálón.

   ![Kapcsolódás az SQL-hez](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Kattintson a jobb gombbal a sémára, és válassza a **séma konvertálása** lehetőséget. 

   ![Séma konvertálása](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Miután a séma elkészült, hasonlítsa össze az átalakított kódot az eredeti kóddal a lehetséges problémák azonosításához. 

   Konvertált objektumok összehasonlítása az eredeti objektumokkal: 

   ![ Objektum összehasonlítása és áttekintése ](./media/mysql-to-sql-database-guide/table-comparison.png)

   Átalakított eljárások összehasonlítása az eredeti eljárásokkal: 

   ![Objektum kódjának összehasonlítása és áttekintése](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate"></a>Migrate 

Miután elvégezte az adatbázisok értékelését és az eltérések kezelését, a következő lépés az áttelepítési folyamat végrehajtása. Az áttelepítés két lépést tesz szükségessé – a séma közzétételét és az adatáttelepítést. 

A séma közzétételéhez és az adatáttelepítés végrehajtásához kövesse az alábbi lépéseket: 

1. Kattintson a jobb gombbal az adatbázisra a **Azure SQL Database metadata Explorerben** , majd válassza az **adatbázissal való szinkronizálás** lehetőséget. Ez a művelet közzéteszi a MySQL-sémát a Azure SQL Database.

   ![Szinkronizálás adatbázissal](./media/mysql-to-sql-database-guide/synchronize-database.png)

   Tekintse át a forrás projekt és a cél közötti leképezést:

   ![Szinkronizálás adatbázis-ellenőrzéssel](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Kattintson a jobb gombbal a MySQL-sémára a **MySQL metadata Explorerben** , és válassza az **adatok áttelepíteni** lehetőséget. Azt is megteheti, hogy az **adatok áttelepíthetők** a legfelső szintű navigációs sávon. 

   ![Adatok áttelepítése](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési** jelentést: 

   ![Adatáttelepítési jelentés](./media/mysql-to-sql-database-guide/data-migration-report.png)

1.  Ellenőrizze az áttelepítést úgy, hogy az SQL Server Management Studio (SSMS) használatával áttekinti Azure SQL Database az adatai és sémáját.

    ![Érvényesítés a SSMA](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



## <a name="post-migration"></a>A migrálás után 

Miután sikeresen elvégezte az **áttelepítési** szakaszt, át kell haladnia az áttelepítés utáni feladatok sorozatán, hogy minden a lehető legzökkenőmentesen és hatékonyan működjön.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek elvégzése bizonyos esetekben szükségessé teszi az alkalmazások módosítását.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységek elvégzését tartalmazza:

1. **Ellenőrzési tesztek fejlesztése**. Az adatbázis-áttelepítés teszteléséhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.

2. **Tesztkörnyezet beállítása**. A tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.

3. **Ellenőrzési tesztek futtatása**. Futtassa az ellenőrző teszteket a forráson és a célon, majd elemezze az eredményeket.

4. **Teljesítménytesztek futtatása**. Futtasson teljesítménytesztet a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez és a teljesség ellenőrzéséhez, valamint a számítási feladatok teljesítményével kapcsolatos problémák kezeléséhez.

További információ ezekről a problémákról és az azok enyhítésére szolgáló konkrét lépésekről: [áttelepítés utáni érvényesítés és optimalizálási útmutató](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Áttelepítési eszközök

Ha további segítségre van az áttelepítési forgatókönyv végrehajtásával kapcsolatban, tekintse meg a következő forrásokat, amelyek a valós idejű migrációs projektek támogatásában lettek kifejlesztve.

| Cím/hivatkozás     | Description    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely nagy mértékben segíti a nagyméretű ingatlanok értékelését azáltal, hogy biztosítja és automatizálja a célzott platformra vonatkozó döntési folyamatokat. |

Ezek az erőforrások az Azure adatcsoport-mérnöki csapat által szponzorált adatsql ninja program részeként lettek kifejlesztve. Az adatelemzési program alapszintű alapokmánya az, hogy feloldja az összetett modernizációt, és az adatplatform-migrációs lehetőségeket a Microsoft Azure-beli adatplatformján is felgyorsítja. Ha úgy gondolja, hogy a szervezete szeretne részt venni az adatsql ninja programban, forduljon a fiókhoz, és kérje meg, hogy küldje el a jelölést.

## <a name="next-steps"></a>Következő lépések 

- A számítási feladatok Azure-ba való áttelepítésével megbecsülheti az [Azure teljes tulajdonlási (TCO) számológépét](https://aka.ms/azure-tco) .

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egy olyan mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, tekintse meg a cikk [szolgáltatás és eszközök az adatok áttelepítéséhez](https://docs.microsoft.com/azure/dms/dms-tools-matrix)című témakört.

- Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://datamigration.microsoft.com/). 

Videók esetében lásd: 
- [Az áttelepítési út és a szükséges eszközök/szolgáltatások áttekintése az értékelés és a Migrálás elvégzéséhez](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
