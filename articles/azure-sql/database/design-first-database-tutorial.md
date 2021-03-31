---
title: 'Oktatóanyag: az első kapcsolódó adatbázis megtervezése a SSMS használatával'
description: Megtudhatja, hogyan tervezheti meg első, Azure SQL Database a SQL Server Management Studio használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: ae7baeac6cee2a692928642e3e38ce0adad17d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674891"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>Oktatóanyag: a SSMS-t használó, Azure SQL Databaseban található viszonyítási adatbázis tervezése
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


A Azure SQL Database a Microsoft Cloud (Azure) szolgáltatásban a DBaaS (). Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket az Azure Portal és az [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával:

> [!div class="checklist"]
>
> - Adatbázis létrehozása a Azure Portal * használatával
> - Kiszolgálói szintű IP-tűzfalszabály beállítása a Azure Portal használatával
> - Kapcsolódás az adatbázishoz SSMS segítségével
> - Táblázatok létrehozása az SSMS használatával
> - Adatok kötegelt betöltése a BCP használatával
> - Adatlekérdezés a SSMS

* Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

> [!TIP]
> A következő Microsoft Learn modul segít megtanulni az ingyenes ASP.NET- [alkalmazások fejlesztését és konfigurálását, amely lekérdezi a Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/), beleértve egy egyszerű adatbázis létrehozását is.
> [!NOTE]
> Ebben az oktatóanyagban a Azure SQL Database használjuk. Egy készletezett adatbázist rugalmas készletben vagy SQL felügyelt példányban is használhat. SQL felügyelt példányhoz való csatlakozáshoz tekintse meg a következő SQL felügyelt példányok rövid útmutatóját: gyors útmutató [: az Azure virtuális gép konfigurálása Azure SQL felügyelt példányhoz való csatlakozáshoz](../managed-instance/connect-vm-instance-configure.md) és rövid útmutató [: pont – hely kapcsolat konfigurálása egy Azure SQL felügyelt példányhoz a helyszínen](../managed-instance/point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette a következőt:

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (legújabb verzió)
- [BCP és Sqlcmd](https://www.microsoft.com/download/details.aspx?id=36433) (legújabb verzió)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-blank-database-in-azure-sql-database"></a>Üres adatbázis létrehozása Azure SQL Database

A Azure SQL Databaseban található adatbázis számítási és tárolási erőforrások egy meghatározott készletével jön létre. Az adatbázis egy [Azure-erőforráscsoport](../../active-directory-b2c/overview.md) keretein belül jön létre, és egy [logikai SQL Server](logical-servers.md)használatával van kezelve.

Az alábbi lépéseket követve hozzon létre egy üres adatbázist.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
2. Az **Új** oldalon válassza az **Adatbázisok** elemet az Azure Marketplace szakaszban, majd kattintson az **SQL Database** elemre a **Kiemelt** szakaszban.

   ![üres adatbázis létrehozása](./media/design-first-database-tutorial/create-empty-database.png)

3. Töltse ki a **SQL Database** űrlapot a következő információkkal az előző képen látható módon:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Adatbázis neve** | *yourDatabase* | Az érvényes adatbázis-nevekkel kapcsolatban lásd: [adatbázis-azonosítók](/sql/relational-databases/databases/database-identifiers). |
    | **Előfizetés** | *yourSubscription*  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
    | **Erőforráscsoport** | *yourResourceGroup* | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
    | **Forrás kiválasztása** | Üres adatbázis | Meghatározza, hogy egy üres adatbázist kell létrehozni. |

4. Kattintson a **kiszolgáló** elemre egy meglévő kiszolgáló használatához, vagy hozzon létre és konfiguráljon egy új kiszolgálót. Válasszon ki egy meglévő kiszolgálót, vagy kattintson az **új kiszolgáló létrehozása** lehetőségre, és töltse ki az **új kiszolgáló** űrlapot a következő információkkal:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Kiszolgáló neve** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
    | **Kiszolgáló rendszergazdájának felhasználóneve** | Bármely érvényes név | Az érvényes bejelentkezési nevekért lásd: [adatbázis-azonosítók](/sql/relational-databases/databases/database-identifiers). |
    | **Jelszó** | Bármely érvényes jelszó | A jelszónak legalább nyolc karakterből kell állnia, és tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűk, számok és nem alfanumerikus karakterek. |
    | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

    ![adatbázis-kiszolgáló létrehozása](./media/design-first-database-tutorial/create-database-server.png)

5. Kattintson a **Kiválasztás** elemre.
6. Kattintson a **Tarifacsomag** parancsra a szolgáltatási szint, a DTU-k vagy virtuális magok száma és a tárterületméret megadásához. Megvizsgálhatja az egyes szolgáltatási szintek számára elérhető DTU/virtuális mag és tárhelyek számát.

    Miután kiválasztotta a szolgáltatási szintet, a DTU vagy a virtuális mag számát, valamint a tárterület méretét, kattintson az **alkalmaz** gombra.

7. Adja meg az üres adatbázis **rendezését** (ebben az oktatóanyagban használja az alapértelmezett értéket). A rendezésekkel kapcsolatos további információkért lásd: [Rendezések](/sql/t-sql/statements/collations)

8. Most, hogy végrehajtotta a **SQL Database** űrlapot, kattintson a **Létrehozás** gombra az adatbázis kiépítéséhez. Ez a lépés néhány percet is igénybe vehet.

9. Az eszköztáron kattintson az **Értesítések** elemre az üzembehelyezési folyamat monitorozásához.

   ![Képernyőfelvétel: az értesítések menü, amelyen a telepítés folyamatban van.](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Kiszolgálói szintű IP-tűzfalszabály létrehozása

A Azure SQL Database egy IP-tűzfalat hoz létre a kiszolgáló szintjén. Ez a tűzfal megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz és a kiszolgálón lévő adatbázisokhoz, kivéve, ha egy tűzfalszabály engedélyezi az IP-címet a tűzfalon keresztül. Az adatbázis külső kapcsolatának engedélyezéséhez először hozzá kell adnia egy IP-tűzfalszabály-szabályt az IP-címhez (vagy IP-címtartomány). A [kiszolgálói szintű IP-tűzfalszabály](firewall-configure.md)létrehozásához kövesse az alábbi lépéseket.

> [!IMPORTANT]
> Az Azure SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni a szolgáltatáshoz, előfordulhat, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni az adatbázishoz, ha a rendszergazda megnyitja a 1433-es portot.

1. Az üzembe helyezés befejezése után válassza az **SQL-adatbázisok** lehetőséget a Azure Portal menüben, vagy keresse meg és válassza ki az *SQL-adatbázisok* elemet bármely oldalon.  

1. Az **SQL-adatbázisok** lapon válassza a *yourDatabase* lehetőséget. Megnyílik az adatbázis áttekintő lapja, amely megjeleníti a teljes **kiszolgálónevet** (például `contosodatabaseserver01.database.windows.net` ), és további konfigurálási lehetőségeket biztosít.

   ![kiszolgáló neve](./media/design-first-database-tutorial/server-name.png)

1. Másolja ezt a teljes kiszolgálónevet, hogy a kiszolgálóhoz és az adatbázisokhoz való kapcsolódáshoz SQL Server Management Studio.

1. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az eszköztáron. Ekkor megnyílik a kiszolgáló **tűzfalbeállítások** lapja.

   ![kiszolgálói szintű IP-tűzfalszabály](./media/design-first-database-tutorial/server-firewall-rule.png)

1. Kattintson az **ügyfél IP-** címének hozzáadása elemre az eszköztáron, és adja hozzá az aktuális IP-címet egy új IP-tűzfalszabály-szabályhoz. Az IP-tűzfalszabály az 1433-as portot egyetlen IP-cím vagy IP-címtartomány számára is megnyithatja.

1. Kattintson a **Mentés** gombra. A rendszer a kiszolgálón a 1433-as portot nyitja meg a jelenlegi IP-címhez.

1. Kattintson az **OK** gombra, majd zárja be a **Tűzfalbeállítások** lapot.

Az IP-cím mostantól átadható az IP-tűzfalon. Most már csatlakozhat az adatbázishoz SQL Server Management Studio vagy egy tetszőleges eszköz használatával. Ügyeljen arra, hogy a korábban létrehozott kiszolgálói rendszergazdai fiókot használja.

> [!IMPORTANT]
> Alapértelmezés szerint a SQL Database IP-tűzfalon keresztüli hozzáférés engedélyezve van az összes Azure-szolgáltatáshoz. Kattintson a **KI** gombra ezen az oldalon az összes Azure-szolgáltatás hozzáférésének letiltásához.

## <a name="connect-to-the-database"></a>Csatlakozás az adatbázishoz

Kapcsolat létesítése az adatbázissal a [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) használatával.

1. Nyissa meg az SQL Server Management Studiót.
2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Server type (Kiszolgáló típusa)** | Adatbázismotor | Kötelezően megadandó érték. |
   | **Kiszolgáló neve** | A teljes kiszolgálónév | Például: *YourServer.database.Windows.net*. |
   | **Hitelesítés** | SQL Server-hitelesítés | Az SQL-hitelesítés az egyetlen hitelesítési típus, amelyet ebben az oktatóanyagban konfiguráltunk. |
   | **Bejelentkezés** | A kiszolgálói rendszergazdafiók | Az a fiók, amely a kiszolgáló létrehozásakor lett megadva. |
   | **Jelszó** | A kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásakor megadott jelszó. |

   ![kapcsolódás a kiszolgálóhoz](./media/design-first-database-tutorial/connect.png)

3. A **Connect to server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen kattintson az **Options** (Beállítások) elemre. A **Kapcsolódás az adatbázishoz** szakaszban adja meg a *yourDatabase* az adatbázishoz való kapcsolódáshoz.

    ![csatlakozás kiszolgálón található adatbázishoz](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. Kattintson a **Csatlakozás** gombra. Megnyílik a **Object Explorer** ablak a SSMS.

5. Az **Object Explorerban** bontsa ki az **adatbázisok** csomópontot, majd bontsa ki a *yourDatabase* elemet, és tekintse meg a mintaadatbázis objektumait.

   ![adatbázis-objektumok](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>Táblák létrehozása az adatbázisban

A [Transact-SQL](/sql/t-sql/language-reference) használatával hozzon létre egy adatbázis-sémát négy táblázattal, amelyek az egyetemek hallgatókezelési rendszerét modellezik:

- Személy
- Tanfolyam
- Tanuló
- Kredit

Az alábbi ábrán látható, hogyan kapcsolódnak ezek a táblázatok egymáshoz. Ezen táblázatok némelyike más táblák oszlopaira hivatkozik. A *tanulói* tábla például a *személy* tábla *számú personid* oszlopára hivatkozik. Tanulmányozza a diagramot az ebben az oktatóanyagban szereplő táblák kapcsolatainak megértéséhez. A hatékony adatbázistáblák létrehozásának részleteiért lásd: [Hatékony adatbázistáblák létrehozása](/previous-versions/tn-archive/cc505842(v=technet.10)). Az adattípusok kiválasztására vonatkozó további információkért lásd: [Adattípusok](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Használhatja az [SQL Server Management Studio táblatervezőjét](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) is a táblák létrehozásához és tervezéséhez.

![Táblák közötti kapcsolatok](./media/design-first-database-tutorial/tutorial-database-tables.png)

1. A **Object Explorer** kattintson a jobb gombbal a *yourDatabase* elemre, és válassza az **Új lekérdezés** elemet. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.

2. A lekérdezési ablakban hozzon létre négy táblát az adatbázisban a következő lekérdezés futtatásával:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Táblák létrehozása](./media/design-first-database-tutorial/create-tables.png)

3. A létrehozott táblák megtekintéséhez bontsa ki a **táblák** csomópontot a **Object Explorer** *yourDatabase* alatt.

   ![létrehozott ssms-táblák](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba

1. Hozzon létre egy *sampleData* nevű mappát a letöltések mappában az adatbázishoz tartozó mintaadatok tárolásához.

2. Kattintson a jobb gombbal a következő hivatkozásokra, és mentse őket a *sampleData* mappába.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Nyisson meg egy parancssori ablakot, és navigáljon a *sampleData* mappára.

4. A következő parancsok végrehajtásával szúrhat be mintaadatok a táblázatba, és cserélje le a *kiszolgáló*, az *adatbázis*, a *felhasználó* és a *jelszó* értékeit a környezetéhez tartozó értékekre.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Ezzel mintaadatokat töltött be a korábban létrehozott táblákba.

## <a name="query-data"></a>Adatok lekérdezése

Hajtsa végre a következő lekérdezéseket az adatbázistáblákban lévő információk lekéréséhez. További információ az SQL-lekérdezések írásához: [SQL-lekérdezések írása](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)) . Az első lekérdezés összekapcsolja mind a négy táblázatot, hogy megkeresse a "Dominika Pope" által tanított tanulókat, akik 75%-nál magasabb szintűek. A második lekérdezés mind a négy táblát összekapcsolja, és megkeresi azokat a tanfolyamokat, amelyekben a "Noe Coleman" már regisztrálva volt.

1. Az SQL Server Management Studio lekérdezési ablakában hajtsa végre a következő lekérdezést:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. A lekérdezési ablakban hajtsa végre a következő lekérdezést:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban számos alapvető adatbázis-feladatot tanult meg. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
>
> - Adatbázis létrehozása a Azure Portal * használatával
> - Kiszolgálói szintű IP-tűzfalszabály beállítása a Azure Portal használatával
> - Kapcsolódás az adatbázishoz SSMS segítségével
> - Táblázatok létrehozása az SSMS használatával
> - Adatok kötegelt betöltése a BCP használatával
> - Adatlekérdezés a SSMS

Folytassa a következő oktatóanyaggal, amely az adatbázisok Visual Studio és C# használatával történő tervezését ismerteti.

> [!div class="nextstepaction"]
> [A Azure SQL Database C# és a ADO.NET közti viszonyítási adatbázis megtervezése](design-first-database-csharp-tutorial.md)