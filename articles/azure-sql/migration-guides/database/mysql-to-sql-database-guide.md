---
title: 'MySQL to Azure SQL Database: Migrálási útmutató'
description: Ebből az útmutatóból megtudhatja, hogyan telepítheti át MySQL-adatbázisát egy Azure SQL-adatbázisba a SQL Server Migration Assistant for MySQL (SSMA for MySQL) használatával.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: d4510aa5cda61dac88102c89b3e03da231380bd6
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389451"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Migrálási útmutató: MySQL Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ebből az útmutatóból [](https://azure.microsoft.com/migration/migration-journey) megtudhatja, hogyan migrálható a MySQL-adatbázis egy Azure SQL-adatbázisba a SQL Server Migration Assistant for MySQL (SSMA for [MySQL)](https://azure.microsoft.com/en-us/migration/sql-server/) használatával. 

További migrálási útmutatókért lásd az [Azure Database migrálási útmutatóját.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdi a MySQL-adatbázis SQL-adatbázisba való áttelepítését, tegye a következőket:

- Ellenőrizze, hogy a forráskörnyezet támogatott-e. A MySQL 5.6 és 5.7 jelenleg támogatott. 
- Töltse le és telepítse [SQL Server Migration Assistant MySQL-hez.](https://www.microsoft.com/download/details.aspx?id=54257)
- Győződjön meg arról, hogy rendelkezik kapcsolattal és megfelelő engedélyekkel a forrás és a cél eléréséhez.

## <a name="pre-migration"></a>A migrálás előtt 

Az előfeltételek teljesültével készen áll arra, hogy felfedezze a környezet topológiáját, és felmérje az Azure-felhőbe való [migrálás megvalósíthatóságát.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Kiértékelés 

Az SQL Server Migration Assistant (SSMA) for MySQL használatával áttekintheti az adatbázis-objektumokat és adatokat, és kiértékelheti az adatbázisokat a migráláshoz. 

Értékelés létrehozásához tegye a következőket:

1. Nyissa meg [az SSMA for MySQL-t.](https://www.microsoft.com/download/details.aspx?id=54257) 
1. Válassza **a File (Fájl)** lehetőséget, majd a **New Project (Új projekt) lehetőséget.** 
1. A **New Project (Új projekt)** panelen adja meg a projekt nevét és **helyét,** majd a **Migrate To** (Áttelepítés ide) legördülő listában válassza a Azure SQL Database. 
1. Válassza az **OK** lehetőséget.

   ![A migrálási projekt nevének, helyének és célhelyének bevitelére vonatkozó "Új projekt" panel képernyőképe.](./media/mysql-to-sql-database-guide/new-project.png)

1. Válassza a **Csatlakozás a MySQL-hez** lapot, majd adja meg a MySQL-kiszolgáló csatlakoztatásának részleteit. 

   ![Képernyőkép a "Csatlakozás a MySQL-hez" panelről a forráshoz való csatlakozás megadásához.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. A **MySQL Metadata Explorer panelen** kattintson a jobb gombbal a MySQL-sémára, majd válassza a **Jelentés létrehozása lehetőséget.** Másik lehetőségként válassza a jelentés létrehozása **lapot** a jobb felső sarokban.

   ![A MySQL-hez készült SSMA "Jelentés létrehozása" hivatkozásának képernyőképe.](./media/mysql-to-sql-database-guide/create-report.png)

1. Tekintse át a HTML-jelentést az átalakítási statisztikák, hibák és figyelmeztetések áttekintéséhez. Elemezze a átalakítási problémák és a megoldásuk érdekében. 
   A jelentést az Excelben is megnyithatja, hogy leltárt kap a MySQL-objektumokról, és megértse a sémakonverziók végrehajtásához szükséges erőfeszítéseket. A jelentés alapértelmezett helye az SSMAProjects jelentésmappában található. Például: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Képernyőkép az SSMA-ban egy példa konverziós jelentésről.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Az adattípusok ellenőrzése

Ellenőrizze az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa őket a követelmények alapján. Ehhez tegye a következőket: 

1. Válassza **a Tools (Eszközök)** lehetőséget, majd a **Project Settings (Projektbeállítások) lehetőséget.**  
1. Válassza a **Típusleképezések** lapot. 

   ![Képernyőkép az SSMA for MySQL "Típusleképezés" panelről.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Az egyes táblák típusleképezését úgy módosíthatja, hogy kiválasztja a tábla nevét a **MySQL Metadata Explorer panelen.** 

### <a name="convert-the-schema"></a>A séma konvertálása 

A séma átalakításához tegye a következőket: 

1. (Nem kötelező) Dinamikus vagy specializált lekérdezések átalakításához kattintson a jobb gombbal a csomópontra, majd válassza az **Utasítás hozzáadása lehetőséget.** 

1. Válassza a **Csatlakozás Azure SQL Database** lapot, majd tegye a következőket:

   a. Adja meg az SQL-adatbázishoz való csatlakozás részleteit.  
   b. A legördülő listában válassza ki a célKÉNT kiválasztott SQL-adatbázist. Új nevet is meg lehet adni, amely esetben létrejön egy adatbázis a célkiszolgálón.  
   c. Adja meg a hitelesítési adatokat.  
   d. Válassza a **Kapcsolódás** lehetőséget.

   ![Képernyőkép az SSMA for MySQL Azure SQL Database " panelről.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Kattintson a jobb gombbal arra a sémára, amelyvel dolgozik, majd válassza a **Séma konvertálása lehetőséget.** Másik lehetőségként válassza a séma **konvertálása** lapot a jobb felső sarokban.

   ![A "MySQL Metadata Explorer" panel "Séma konvertálása" parancsának képernyőképe.](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Az átalakítás befejezése után tekintse át és hasonlítsa össze a konvertált objektumokat az eredeti objektumokkal a lehetséges problémák azonosítása és a javaslatok alapján történő megoldása érdekében. 

   ![A konvertált objektumok és az eredeti objektumok összehasonlítását bemutató képernyőkép.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Hasonlítsa össze a konvertált Transact-SQL szöveget az eredeti kóddal, és tekintse át a javaslatokat.

   ![A konvertált lekérdezések és a forráskód összehasonlítását bemutató képernyőkép.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. A Kimenet **panelen** válassza az **Eredmények** áttekintése lehetőséget, majd tekintse át az esetleges hibákat a **Hibalista panelen.** 
1. Mentse helyileg a projektet egy offline séma-szervizelési gyakorlathoz. Válassza a Fájl **Projekt**  >  **mentése lehetőséget.** Ez lehetővé teszi a forrás- és célsémák offline kiértékelését és szervizelését, mielőtt közzétenné a sémát az SQL-adatbázisban.

   Hasonlítsa össze a konvertált eljárásokat az eredeti eljárásokkal az itt látható módon: 

   ![A konvertált eljárások és az eredeti eljárások összehasonlítását bemutató képernyőkép.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Adatbázisok áttelepítése 

Az adatbázisok felmérése és az eltérések elhárítása után futtathatja a migrálási folyamatot. A migrálás két lépésből áll: a séma közzétételéből és az adatok migrálásból. 

A séma közzétételéhez és az adatok áttelepítéséhez tegye a következőket: 

1. Tegye közzé a sémát. A Azure SQL Database **Metaadat-kezelő panelen** kattintson a jobb gombbal az adatbázisra, majd válassza a **Szinkronizálás adatbázissal lehetőséget.** Ez a művelet közzéteszi a MySQL-sémát az SQL-adatbázisban.

   ![Képernyőkép a "Szinkronizálás az adatbázissal" panelről az adatbázis-leképezés áttekintésére.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Az adatok áttelepítése. A **MySQL Metadata Explorer panelen** kattintson a jobb gombbal az át telepíteni kívánt MySQL-sémára, majd válassza az **Adatok áttelepítése lehetőséget.** Másik lehetőségként választhatja az Adatok **áttelepítése lapot** a jobb felső sarokban.

   Egy teljes adatbázis adatainak áttelepítéséhez jelölje be az adatbázis neve melletti jelölőnégyzetet. Az adatok egyes táblákból való áttelepítéséhez bontsa ki az adatbázist, bontsa ki a **Táblák** gombra, majd jelölje be a tábla melletti jelölőnégyzetet. Az egyes táblák adatainak kihagyása érdekében törölje a jelölőnégyzet jelölését.

   ![A "MySQL Metadata Explorer" panel "Adatok áttelepítése" parancsának képernyőképe.](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést.**
   
   ![Képernyőkép az adatáttelepítési jelentésről.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Csatlakozzon az SQL-adatbázishoz a SQL Server Management Studio [és](/sql/ssms/download-sql-server-management-studio-ssms) ellenőrizze a migrálást az adatok és a séma áttekintésének segítségével.

   ![Képernyőkép a SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>A migrálás után 

Miután sikeresen befejezte a migrálási szakaszt, a migrálás utáni feladatok sorát kell elvégeznie, hogy minden a lehető zökkenőmentesen és leghatékonyabban működjön. 

### <a name="remediate-applications"></a>Alkalmazások szervize

Az adatok célkörnyezetbe való migrálása után az összes olyan alkalmazásnak, amely korábban a forrást felhasználta, hozzá kell kezdenie a cél fogyasztásának. Ennek megvalósítása bizonyos esetekben az alkalmazások módosításait igényli.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-migrálás tesztelési megközelítése a következő tevékenységekből áll:

1. **Ellenőrző tesztek fejlesztése:** Az adatbázis-migrálás tesztelése SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket, hogy a forrás- és a céladatbázison is fusson. Az érvényesítési lekérdezések a megadott hatókört fedik le.

1. **Tesztkörnyezet beállítása: A** tesztkörnyezetnek tartalmaznia kell a forrásadatbázis és a céladatbázis másolatát. Mindenképpen különítse el a tesztkörnyezetet.

1. **Ellenőrző tesztek futtatása:** Érvényesítési tesztek futtatása a forráson és a célon, majd az eredmények elemzése.

1. **Teljesítményttesztek futtatása:** Futtatassa a teljesítményteszteket a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

### <a name="optimize"></a>Optimalizálás

A migrálás utáni fázis elengedhetetlen az adatpontossági problémák egyeztetéséhez, a teljesség ellenőrzéséhez és a számítási feladat teljesítményével kapcsolatos problémák megoldásához.

Ezekről a problémákról és a megoldásukhoz szükséges lépésekről a migrálás utáni ellenőrzési és optimalizálási [útmutatóban található további információ.](/sql/relational-databases/post-migration-validation-and-optimization-guide)

## <a name="migration-assets"></a>Migrálási eszközök

A migrálási forgatókönyv elvégzésével kapcsolatos további segítségért tekintse meg a következő forrást. Egy valós migrálási projekt támogatásaként lett kifejlesztve.

| Cím | Leírás |
| --- | --- |
| [Adat-számítási feladat értékelési modellje és eszköze](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Javasolt "legmegfelelőbb" célplatformokat, felhőalapú készenlétet és alkalmazás-/adatbázis-szervizelési szinteket biztosít a megadott számítási feladatokhoz. Egyszerű, egykattintásos számítást és jelentés-készítést kínál, amely automatizált, egységes célplatform-döntési folyamat biztosításával segít felgyorsítani a nagy tulajdonfelméréseket. |

Ezeket az erőforrásokat az SQL mérnöki csapata fejlesztette ki. Ennek a csapatnak az alapvető csomagja a Microsoft Azure-adatplatformjára irányuló adatplatform-migrálási projektek összetett modernizálásának feloldására és felgyorsítására szolgál.

## <a name="next-steps"></a>Következő lépések 

- Ha meg tudja becsülni a számítási feladatok Azure-ba való mibrálásának költségmegtakarításait, tekintse meg az Azure teljes tulajdonlási költség [kalkulátorát.](https://aka.ms/azure-tco)

- A Különböző adatbázis- és adatáttelepítési forgatókönyvekben és speciális feladatokban segítő Microsoft- és külső szolgáltatások és eszközök mátrixát lásd: Szolgáltatások és eszközök [adatáttelepítéshez.](../../../dms/dms-tools-matrix.md)

- További migrálási útmutatókért lásd az [Azure Database migrálási útmutatóját.](https://datamigration.microsoft.com/) 

- A migrálási videókért lásd: A migrálási folyamat áttekintése, valamint az ajánlott migrálási és értékelési [eszközök és szolgáltatások.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)

- További felhőbe [való migrálási forrásokkal kapcsolatban lásd](https://azure.microsoft.com/migration/resources/)a [felhőbe való migrálási megoldásokat.](https://azure.microsoft.com/migration)

