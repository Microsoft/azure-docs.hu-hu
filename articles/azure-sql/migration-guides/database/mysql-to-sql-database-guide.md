---
title: 'MySQL – Azure SQL Database: áttelepítési útmutató'
description: Ebből az útmutatóból megtudhatja, hogyan telepítheti át MySQL-adatbázisait egy Azure SQL Database-adatbázisba a MySQL-hez készült SQL Server Migration Assistant (SSMA for MySQL) használatával.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 1f1692aaa74f56c404a8fae7aa91e94baecbb7e1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626486"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Áttelepítési útmutató: MySQL – Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ebből az útmutatóból megtudhatja, hogyan telepítheti át MySQL-adatbázisát egy Azure SQL Database-adatbázisba a MySQL-hez készült SQL Server Migration Assistant (SSMA for MySQL) használatával. 

Más áttelepítési útmutatókért lásd: az [Azure Database áttelepítési útmutatója](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdené a MySQL-adatbázis áttelepítését egy SQL Database-adatbázisba, tegye a következőket:

- Ellenőrizze, hogy a forrás-környezet támogatott-e. Jelenleg a MySQL 5,6 és a 5,7 támogatott. 
- Töltse le és telepítse [SQL Server Migration Assistant for MySQL](https://www.microsoft.com/download/details.aspx?id=54257)-t.
- Győződjön meg arról, hogy van kapcsolata és megfelelő engedélye a forrás és a cél eléréséhez.

## <a name="pre-migration"></a>A migrálás előtt 

Az előfeltételek teljesítése után készen áll a környezet topológiájának felderítésére és az áttelepítés megvalósíthatóságának értékelésére.

### <a name="assess"></a>Kiértékelés 

A MySQL-hez SQL Server Migration Assistant (SSMA) használatával tekintheti meg az adatbázis-objektumokat és az adatforrásokat, valamint értékelheti az adatbázisok áttelepítését. 

Az értékelés létrehozásához tegye a következőket:

1. Nyissa meg [a SSMA for MySQL](https://www.microsoft.com/download/details.aspx?id=54257)-t. 
1. Válassza a **fájl**, majd az **új projekt** lehetőséget. 
1. Az **új projekt** ablaktáblán adja meg a projekt nevét és helyét, majd az **áttelepítés** legördülő listában válassza a **Azure SQL Database** lehetőséget. 
1. Válassza az **OK** lehetőséget.

   ![Képernyőkép az "új projekt" panelről, amely megadja az áttelepítési projekt nevét, helyét és célját.](./media/mysql-to-sql-database-guide/new-project.png)

1. Válassza a **Csatlakozás a MySQL** -hez lapot, majd adja meg a MySQL-kiszolgáló csatlakoztatásának részleteit. 

   ![Képernyőkép a "Csatlakozás a MySQL-hez" panelről a forráshoz való kapcsolatok megadásához.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. A **MySQL metadata Explorer** ablaktáblán kattintson a jobb gombbal a MySQL-sémára, majd válassza a **jelentés létrehozása** lehetőséget. Azt is megteheti, hogy a jobb felső sarokban a **jelentés létrehozása** lapot választja.

   ![Képernyőkép a "jelentés létrehozása" hivatkozásokról a SSMA for MySQL-ben.](./media/mysql-to-sql-database-guide/create-report.png)

1. A HTML-jelentés áttekintésével megismerheti az átalakítási statisztikákat, a hibákat és a figyelmeztetéseket. Elemezze, hogy megértse az átalakítási problémákat és a megoldásokat. 
   A jelentést az Excelben is megnyithatja a MySQL-objektumok leltárának beszerzéséhez és a séma átalakításához szükséges erőfeszítések megismeréséhez. A jelentés alapértelmezett helye a SSMAProjects belüli jelentési mappában található. Például: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Képernyőkép egy példaként szolgáló konverziós jelentésről a SSMA-ben.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Az adattípusok ellenőrzése

Ellenőrizze az alapértelmezett adattípus-hozzárendeléseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez tegye a következőket: 

1. Válassza az **eszközök**, majd a **projekt beállításai** lehetőséget.  
1. Válassza a **típus-hozzárendelések** lapot. 

   ![Képernyőkép a MySQL-hez készült SSMA "típus leképezése" paneljéről.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Az egyes táblák típus-hozzárendelését úgy módosíthatja, ha a **MySQL metaadat-kezelő** ablaktáblán kiválasztja a tábla nevét. 

### <a name="convert-the-schema"></a>A séma konvertálása 

A séma konvertálásához tegye a következőket: 

1. Választható Dinamikus vagy speciális lekérdezések konvertálásához kattintson a jobb gombbal a csomópontra, majd válassza az **utasítás hozzáadása** parancsot. 

1. Kattintson a **kapcsolódás Azure SQL Database** lapra, majd tegye a következőket:

   a. Adja meg az SQL-adatbázishoz való csatlakozás részleteit.  
   b. A legördülő listában válassza ki a cél SQL-adatbázist. Vagy megadhat egy új nevet is, amely esetben a rendszer létrehoz egy adatbázist a célkiszolgálón.  
   c. Adja meg a hitelesítés részleteit.  
   d. Válassza a **Kapcsolódás** lehetőséget.

   ![Képernyőkép: a SSMA for MySQL "kapcsolódás Azure SQL Databasehoz" panelje.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Kattintson a jobb gombbal arra a sémára, amellyel dolgozik, majd válassza a **séma konvertálása** lehetőséget. Azt is megteheti, hogy a jobb felső sarokban a **séma konvertálása** fület választja.

   ![Képernyőfelvétel: "a séma konvertálása" parancs a "MySQL metadata Explorer" panelen.](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Az átalakítás befejezése után tekintse át és hasonlítsa össze a konvertált objektumokat az eredeti objektumokkal a lehetséges problémák azonosításához és a javaslatok alapján történő kezeléséhez. 

   ![A konvertált objektumok eredeti objektumokhoz való összehasonlítását ábrázoló képernyőkép.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Hasonlítsa össze a konvertált Transact-SQL-szöveget az eredeti kóddal, és tekintse át a javaslatokat.

   ![Az átalakított lekérdezések a forráskódhoz viszonyított összehasonlítását ábrázoló képernyőkép.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. A **kimenet** ablaktáblán válassza az **eredmények áttekintése** lehetőséget, majd tekintse át az esetleges hibákat a hibák **listája** ablaktáblán. 
1. Mentse a projektet helyileg a kapcsolat nélküli séma szervizelési gyakorlatához. Ehhez válassza a **fájl**  >  **Mentés projekt** lehetőséget. Ez lehetőséget biztosít a forrás-és a célként megadott sémák offline állapotba helyezésére és szervizelésre, mielőtt közzéteszi a sémát az SQL Database-ben.

   Hasonlítsa össze az átalakított eljárásokat az eredeti eljárásokkal, ahogy az itt látható: 

   ![Az átalakított eljárások az eredeti eljárásokhoz való összehasonlítását ábrázoló képernyőkép.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Az adatbázisok migrálása 

Miután elvégezte az adatbázisok értékelését és az eltérések megcímzését, futtathatja az áttelepítési folyamatot. Az áttelepítés két lépést tesz szükségessé: a séma közzétételét és az adatáttelepítést. 

A séma közzétételéhez és az adatáttelepítés elvégzéséhez tegye a következőket: 

1. Tegye közzé a sémát. A **Azure SQL Database metadata Explorer** ablaktáblán kattintson a jobb gombbal az adatbázisra, majd válassza az **adatbázissal való szinkronizálás** lehetőséget. Ez a művelet közzéteszi a MySQL-sémát az SQL Database-ben.

   ![Képernyőkép: "szinkronizálás az adatbázissal" ablaktábla az adatbázis-hozzárendelés áttekintéséhez.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Telepítse át az adatátvitelt. A **MySQL metadata Explorer** ablaktáblán kattintson a jobb gombbal az áttelepíteni kívánt MySQL-sémára, majd válassza az **adatok áttelepíteni** lehetőséget. Másik lehetőségként kiválaszthatja az **adatáttelepítés** fület a jobb felső sarokban.

   Ha egy teljes adatbázisra szeretné áttelepíteni az adatátvitelt, jelölje be az adatbázis neve melletti jelölőnégyzetet. Ha az adatok áttelepíthetők az egyes táblákból, bontsa ki az adatbázist, majd a **táblák** csomópontot, és jelölje be a tábla melletti jelölőnégyzetet. Ha az adatok kihagyása az egyes táblákból, törölje a jelölőnégyzet jelölését.

   ![Képernyőkép az "adatok átmigrálása" parancsról a "MySQL metadata Explorer" panelen.](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést**.
   
   ![Képernyőkép az adatáttelepítési jelentésről.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Kapcsolódjon az SQL-adatbázishoz [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) használatával, és ellenőrizze az áttelepítést az adatai és a séma áttekintésével.

   ![Képernyőkép a SQL Server Management Studioról.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>A migrálás után 

Az *áttelepítési* fázis sikeres befejezése után el kell végeznie az áttelepítést követő feladatok sorozatát, hogy a lehető leggördülékenyebb és hatékony működést biztosítsa.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek elvégzése bizonyos esetekben szükségessé teszi az alkalmazások módosítását.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységekből áll:

1. **Ellenőrzési tesztek fejlesztése**: az adatbázis áttelepítésének teszteléséhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket, amelyek a forrás-és a célként megadott adatbázison futnak. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.

1. **Tesztkörnyezet beállítása**: a tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.

1. **Ellenőrzési tesztek futtatása**: ellenőrző tesztek futtatása a forráson és a célon, majd az eredmények elemzése.

1. **Teljesítménytesztek futtatása**: futtasson teljesítményteszteket a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez, a teljesség ellenőrzéséhez és a teljesítménnyel kapcsolatos problémák kezeléséhez.

További információ ezekről a problémákról és az azok enyhítésének lépéseiről: [áttelepítés utáni érvényesítés és optimalizálási útmutató](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Áttelepítési eszközök

Az áttelepítési forgatókönyv végrehajtásával kapcsolatos további segítségért tekintse meg a következő erőforrást. A fejlesztést egy valós idejű migrációs projekt keretében hozták létre.

| Cím | Leírás |
| --- | --- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | A a megadott számítási feladatokhoz javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja. Egyszerű, egykattintásos számítást és jelentéskészítési lehetőséget kínál, amely lehetővé teszi a nagyméretű ingatlanok értékelésének gyorsítását egy automatizált, egységes cél-platform döntési folyamat biztosításával. |

Az adatsql mérnöki csapat fejlesztette ezt az erőforrást. A csapat alapszintű Chartája az adatplatform-áttelepítési projektek a Microsoft Azure-beli adatplatformra való feltiltásának és felgyorsításának feloldása.

## <a name="next-steps"></a>Következő lépések 

- A számítási feladatok Azure-ba való áttelepítésével felhasználható költségmegtakarítások becsléséhez tekintse meg az [Azure teljes tulajdonlási díjas számológép](https://aka.ms/azure-tco)című témakört.

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egy olyan mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek és speciális feladatok ellátásához nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Más áttelepítési útmutatókért lásd: az [Azure Database áttelepítési útmutatója](https://datamigration.microsoft.com/). 

- Áttelepítési videók: az áttelepítési [út áttekintése és az ajánlott áttelepítési és értékelési eszközök és szolgáltatások](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).
