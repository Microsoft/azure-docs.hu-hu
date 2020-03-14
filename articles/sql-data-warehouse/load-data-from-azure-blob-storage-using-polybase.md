---
title: 'Oktatóanyag: a New York-i taxik-adatbázis betöltése'
description: Az oktatóanyag a Azure Portal és a SQL Server Management Studio használatával tölti be a New York-i taxik adatait egy globális Azure-blobból az SQL Analytics szolgáltatásba.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 5e7a4eff57841fdcf3bab87eda4e9771d9742bc5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256678"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Oktatóanyag: a New York taxik-adatkészlet betöltése

Ez az oktatóanyag a Base használatával tölti be a New York-i taxik adatait egy globális Azure Blob Storage-fiókból. Az oktatóanyag az [Azure Portalt](https://portal.azure.com) és az [SQL Server Management Studiót](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) használja a következőkhöz: 

> [!div class="checklist"]
> * SQL-készlet létrehozása a Azure Portalban
> * Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
> * Csatlakozás az adattárházhoz az SSMS használatával
> * Adatok betöltésére kijelölt felhasználó létrehozása
> * Külső táblák létrehozása az Azure Blob Storage-ban található adatokhoz
> * Adatok betöltése az adattárházba a CTAS T-SQL-utasítás használatával
> * Az adatok állapotának megtekintése betöltés közben
> * Statisztikák készítése az újonnan betöltött adatokról

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag megkezdése előtt töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) legújabb verzióját.


## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist

A rendszer létrehoz egy SQL-készletet [számítási erőforrások] memória-Egyidejűség-limits.md) meghatározott készlettel. Az adatbázis egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül egy [Azure SQL logikai kiszolgálón](../sql-database/sql-database-features.md) jön létre. 

Az alábbi lépéseket követve hozzon létre egy üres adatbázist. 

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Az **új oldalon válassza az** **adatbázisok** lehetőséget, majd az **új** oldal **Kiemelt** részén válassza az **Azure szinapszis Analytics** elemet.

    ![adattárház létrehozása](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Adja meg az alábbi adatokat az űrlapon: 

   | Beállítás            | Ajánlott érték       | Leírás                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *Név**            | mySampleDataWarehouse | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers) ismertető cikket. |
   | **Előfizetés**   | Az Ön előfizetése     | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup       | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
   | **Forrás kiválasztása**  | Üres adatbázis        | Megköveteli egy üres adatbázis létrehozását. Megjegyzés: Az adattárház az adatbázisok egy típusa. |

    ![adattárház létrehozása](media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Válassza ki a **kiszolgálót** , és hozzon létre egy új kiszolgálót az új adatbázis létrehozásához és konfigurálásához. Adja meg az alábbi adatokat az **Új kiszolgálóűrlapon**: 

    | Beállítás                | Ajánlott érték          | Leírás                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **Kiszolgálónév**        | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
    | **Kiszolgálói rendszergazdai bejelentkezés** | Bármely érvényes név           | Az érvényes bejelentkezési nevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket. |
    | **Jelszó**           | Bármely érvényes jelszó       | A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek. |
    | **Hely**           | Bármely érvényes hely       | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

    ![adatbázis-kiszolgáló létrehozása](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Válassza a **kiválasztás**lehetőséget.

6. Válassza a **teljesítményszint** lehetőséget annak megadásához, hogy az adatraktár Gen1 vagy Gen2, valamint az adatraktár-egységek számát. 

7. Ebben az oktatóanyagban válassza az SQL Pool **Gen2**elemet. Alapértelmezés szerint a csúszka a **DW1000c** értékre van állítva.  Csúsztassa fel és le, hogy kipróbálja a működését a gyakorlatban. 

    ![teljesítmény konfigurálása](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Kattintson az **Alkalmaz** gombra.
9. A kiépítés panelen válasszon ki egy **rendezést** az üres adatbázishoz. A jelen oktatóanyag esetében használja az alapértelmezett értéket. A rendezésekkel kapcsolatos további információkért lásd: [Rendezések](/sql/t-sql/statements/collations)

11. Most, hogy elvégezte az űrlapot, válassza a **Létrehozás** lehetőséget az adatbázis kiépítéséhez. Az üzembe helyezés eltarthat néhány percig. 

12. Az eszköztáron válassza az **értesítések** lehetőséget a telepítési folyamat figyeléséhez.
  
     ![értesítés](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

A kiszolgáló szintjén található tűzfal, amely megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz. A csatlakozás engedélyezéséhez hozzáadhat tűzfalszabályokat, amelyek adott IP-címekkel engedélyezik a kapcsolódást.  A következő lépéseket követve hozzon létre egy [kiszolgálószintű tűzfalszabályt](../sql-database/sql-database-firewall-configure.md) az ügyfél IP-címéhez. 

> [!NOTE]
> Az SQL Data Warehouse az 1433-as portot használja a kommunikációhoz. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot.

1. Az üzembe helyezés befejezése után válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd válassza a **MySampleDatabase** lehetőséget az **SQL-adatbázisok** lapon. Megnyílik az adatbázis áttekintő lapja, amely megjeleníti a teljes kiszolgálónevet (például **mynewserver-20180430.database.Windows.net**), és további konfigurálási lehetőségeket biztosít. 

2. Másolja le ezt a teljes kiszolgálónevet, mert a későbbi rövid útmutatók során szüksége lesz rá a kiszolgálóhoz és az adatbázisokhoz való csatlakozáshoz. Ezután válassza ki a kiszolgáló nevét a kiszolgáló beállításainak megnyitásához.

    ![kiszolgálónév keresése](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Válassza ki a kiszolgáló nevét a kiszolgáló beállításainak megnyitásához.

    ![kiszolgáló beállításai](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Válassza a **tűzfalbeállítások megjelenítése**lehetőséget. Megnyílik az SQL-adatbáziskiszolgálóhoz tartozó **Tűzfalbeállítások** oldal. 

    ![kiszolgálói tűzfalszabály](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron az aktuális IP-cím új tűzfalszabályként való hozzáadásához. A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

5. Kattintson a **Mentés** gombra. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, és megnyitja az 1433-as portot a logikai kiszolgálón.

6. kattintson **az OK gombra** , majd a **tűzfalbeállítások** oldal bezárásához.

Mostantól csatlakozhat az SQL-kiszolgálóhoz és annak adattárházaihoz erről az IP-címről. A csatlakozás az SQL Server Management Studio vagy más, választott eszköz használatával lehetséges. A csatlakozáskor használja a korábban létrehozott ServerAdmin-fiókot.  

> [!IMPORTANT]
> Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. Válassza **ki** ezt a lapot, majd a **Mentés** gombra kattintva tiltsa le a tűzfalat az összes Azure-szolgáltatáshoz.

## <a name="get-the-fully-qualified-server-name"></a>A teljes kiszolgálónév lekérése

Kérje le az SQL-kiszolgáló teljes kiszolgálónevét az Azure Portalon. Később ezt a teljes nevet fogja majd használni a kiszolgálóhoz való kapcsolódás során.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki az **Azure szinapszis Analytics** elemet a bal oldali menüben, és válassza ki az adatbázist az **Azure szinapszis Analytics** oldalán. 
3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**. Ebben a példában a teljes név mynewserver-20180430.database.windows.net. 

    ![kapcsolatadatok](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Csatlakozás a kiszolgálóhoz kiszolgáló-rendszergazdaként

Ebben a részben az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) használatával építjük fel a kapcsolatot az Azure SQL-kiszolgálóval.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

    | Beállítás        | Ajánlott érték                            | Leírás                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | Kiszolgáló típusa    | Adatbázismotor                            | Kötelezően megadandó érték                                       |
    | Kiszolgálónév    | A teljes kiszolgálónév            | A névnek a következőhöz hasonlónak kell lennie: **mynewserver-20180430.database.Windows.net**. |
    | Authentication | SQL Server-hitelesítés                  | Az SQL-hitelesítés az egyetlen hitelesítési típus, amelyet ebben az oktatóanyagban konfiguráltunk. |
    | Bejelentkezés          | A kiszolgálói rendszergazdai fiók                   | Ez az a fiók, amely a kiszolgáló létrehozásakor lett megadva. |
    | Jelszó       | A kiszolgálói rendszergazdafiók jelszava | Ezt a jelszót adta meg a kiszolgáló létrehozásakor. |

    ![kapcsolódás a kiszolgálóhoz](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Kattintson a **Csatlakozás** gombra. Megnyílik az Object Explorer ablak az SSMS-ben. 

5. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok) elemet. Ezután bontsa ki a **System databases** (Rendszeradatbázisok) és a **master** elemeket az objektumok megtekintéséhez a master adatbázisban.  Bontsa ki a **mySampleDatabase** csomópontot az új adatbázisban található objektumok megtekintéséhez.

    ![adatbázis-objektumok](media/load-data-from-azure-blob-storage-using-polybase/connected.png) 

## <a name="create-a-user-for-loading-data"></a>Felhasználó létrehozása az adatok betöltéséhez

A kiszolgáló rendszergazdai fiókjának célja, hogy felügyeleti műveleteket végezzenek vele, és nem alkalmas a felhasználói adatok lekérdezésére. Az adatok betöltése memóriaigényes művelet. A memória maximális száma az [adatraktár-egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) és a konfigurált [erőforrás-osztály](resource-classes-for-workload-management.md) szerint van meghatározva. 

Érdemes létrehozni egy adatok betöltésére kijelölt felhasználót és fiókot. Ezután adja hozzá a betöltést végző felhasználót egy olyan [erőforrásosztályhoz](resource-classes-for-workload-management.md), amely lehetővé teszi a megfelelő mértékű maximális memórialefoglalást.

Mivel jelenleg a kiszolgálói rendszergazdaként csatlakozik, létrehozhat bejelentkezéseket és felhasználókat. Kövesse ezeket a lépéseket egy **LoaderRC20** nevű fiók és felhasználó létrehozásához. Ezután rendelje hozzá a felhasználót a **staticrc20** erőforrásosztályhoz. 

1.  A SSMS kattintson a jobb gombbal a **Master** elemre a legördülő menü megjelenítéséhez, majd válassza az **Új lekérdezés**lehetőséget. Megnyílik egy új lekérdezési ablak.

    ![Új lekérdezés a master adatbázisban](media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. A lekérdezési ablakban adja meg ezeket a T-SQL-parancsokat egy LoaderRC20 nevű fiók és felhasználó létrehozásához, az „a123STRONGpassword!” helyett pedig adjon meg egy saját jelszót. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Válassza a **Végrehajtás** lehetőséget.

4. Kattintson jobb gombbal a **mySampleDataWarehouse** elemre, majd válassza a **New Query** (Új lekérdezés) elemet. Megnyílik egy új lekérdezési ablak.  

    ![Új lekérdezés futtatása a minta-adattárházon](media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. A következő T-SQL-parancsok begépelésével hozzon létre egy LoaderRC20 nevű felhasználót a LoaderRC20-fiókhoz. A második sor az új adattárházra vonatkozó CONTROL (vezérlési) engedélyeket ad az új felhasználónak.  Ezen engedélyek megadása ahhoz hasonló, mintha az adatbázis tulajdonosává tenné a felhasználót. A harmadik sor a staticrc20 [erőforrásosztály](resource-classes-for-workload-management.md) tagjaként veszi fel az új felhasználót.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Válassza a **Végrehajtás** lehetőséget.

## <a name="connect-to-the-server-as-the-loading-user"></a>Csatlakozás a kiszolgálóhoz a betöltést végző felhasználóként

Az adatok betöltésének első lépése a LoaderRC20-ként való bejelentkezés.  

1. A Object Explorer területen válassza a **kapcsolat** legördülő menüt, és válassza az **adatbázismotor**lehetőséget. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanel jelenik meg.

    ![Csatlakozás az új fiókkal](media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Gépelje be a teljes kiszolgálónevet, és adja meg a **LoaderRC20** felhasználónévként.  Adja meg a LoaderRC20-hoz tartozó jelszót.

3. Kattintson a **Csatlakozás** gombra.

4. Ha a kapcsolat készen áll, az Object Explorerben két kiszolgálói kapcsolat lesz látható. Az egyik kapcsolat ServerAdmin-ként, a másik pedig MedRCLogin-ként jelenik meg.

    ![Sikeres csatlakozás](media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-external-tables-for-the-sample-data"></a>Külső táblák létrehozása a mintaadatokhoz

Készen áll megkezdeni az adatok az új adattárházba való betöltésének folyamatát. Ebből az oktatóanyagból megtudhatja, hogyan használhatja a külső táblákat a New York-i taxi-adatok Azure Storage-blobból való betöltésére. Ha szeretné megtudni, hogyan érheti el adatait az Azure Blob Storage-ba, vagy hogyan töltheti be közvetlenül a forrásból, tekintse meg a [Betöltés áttekintését](sql-data-warehouse-overview-load.md).

Futtassa a következő SQL-parancsfájlokat, és adja meg a betölteni kívánt adatokra vonatkozó információkat. Ezen információk közé tartozik az adatok helye, az adatok tartalmának formátuma és az adatok tábladefiníciója. 

1. Az előző szakaszban LoaderRC20-ként jelentkezett be az adattárházba. Az SSMS-ben kattintson jobb gombbal a LoaderRC20-kapcsolatra, és válassza a **New Query** (Új lekérdezés) elemet.  Megnyílik egy új lekérdezési ablak. 

    ![Új betöltési lekérdezési ablak](media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Hasonlítsa össze a lekérdezési ablakot az előző képpel.  Győződjön meg arról, hogy az új lekérdezési ablak LoaderRC20-ként fut, és a MySampleDataWarehouse adatbázison hajt végre lekérdezéseket. A betöltés összes lépését ebben a lekérdezési ablakban végezze el.

3. Hozzon létre egy főkulcsot a MySampleDataWarehouse adatbázishoz. Adatbázisonként csak egyszer kell főkulcsot létrehoznia. 

    ```sql
    CREATE MASTER KEY;
    ```

4. Futtassa a következő [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) utasítást az Azure blob helyének meghatározásához. Ez a külső taxiadatok helye.  A lekérdezési ablakhoz fűzött parancs futtatásához jelölje ki a futtatni kívánt parancsokat, és válassza a **végrehajtás**lehetőséget.

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytaxiblob.blob.core.windows.net/'
    );
    ```

5. Futtassa a következő [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) T-SQL-utasítást a külső adatfájl formázási jellemzőinek és beállításainak megadásához. Ez az utasítás adja meg, hogy a külső adatok szövegként legyenek tárolva, továbbá azt is, hogy az értékeket függőleges vonal („|”) karakter válassza el egymástól. A külső fájl tömörítése a Gzip használatával történik. 

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( 
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );
    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH ( 
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

6.  Futtassa a következő [CREATE SCHEMA](/sql/t-sql/statements/create-schema-transact-sql) utasítást egy séma létrehozásához a külső fájlformátum számára. A séma lehetővé teszi a létrehozni kívánt külső táblák rendszerezését.

    ```sql
    CREATE SCHEMA ext;
    ```

7. Hozza létre a külső táblákat. A táblázat definíciói az adattárházban tárolódnak, de a táblázatok az Azure Blob Storage-ban tárolt adathivatkozásokat használják. A következő T-SQL parancsok futtatásával hozzon létre több külső táblát, amelyek mind a külső adatforrásban korábban meghatározott Azure-blobra mutatnak.

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    ); 
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0 
    );      
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;  
    CREATE EXTERNAL TABLE [ext].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

8. Az Object Explorerben bontsa ki a mySampleDataWarehouse elemet az imént létrehozott külső táblák listájának megtekintéséhez.

    ![Külső táblák megtekintése](media/load-data-from-azure-blob-storage-using-polybase/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>Az adatok betöltése az adattárházba

Ez a szakasz az imént definiált külső táblákat használja a mintaadatok Azure Storage Blobból való betöltéséhez.  

> [!NOTE]
> Ez az oktatóanyag az adatokat közvetlenül a végső táblázatba tölti be. Éles környezetben általában a CREATE TABLE AS SELECT utasítás használatával végez betöltést egy előkészítési táblába. Amíg az adatok az előkészítési táblában vannak, bármilyen szükséges átalakítás elvégezhető rajtuk. Az előkészítési táblában lévő adatok éles táblához való hozzáfűzéséhez használhatja az INSERT...SELECT utasítást. További információkért lásd: [Adatok beszúrása egy éles táblába](guidance-for-loading-data.md#inserting-data-into-a-production-table).

A szkript a [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL-utasítást használja az adatok betöltéséhez az Azure Storage-blobból az adattárházban található új táblákba. A CTAS egy új táblát hoz létre egy kiválasztási utasítás eredményei alapján. Az új tábla oszlopai és adattípusai megegyeznek a kiválasztási utasítás eredményeivel. Ha a SELECT utasítást kiválasztja egy külső táblából, az adatok az adatraktárban található, egy rokon táblába kerülnek importálásra. 

1. Futtassa a következő szkriptet az adatok betöltéséhez az adattárházban található új táblákba.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. A betöltés közben megtekintheti az adatokat. Több GB-nyi adatot tölt be és tömörít nagy teljesítményű fürtözött oszlopcentrikus indexekbe. Futtassa az alábbi lekérdezést, amely dinamikus felügyeleti nézetekkel (DMV-k) jeleníti meg a töltés állapotát. 

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024.0 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
    WHERE
        r.[label] = 'CTAS : Load [dbo].[Date]' OR
        r.[label] = 'CTAS : Load [dbo].[Geography]' OR
        r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
        r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
        r.[label] = 'CTAS : Load [dbo].[Time]' OR
        r.[label] = 'CTAS : Load [dbo].[Weather]' OR
        r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
        r.command,
        s.request_id,
        r.status
    ORDER BY
        nbr_files desc, 
        gb_processed desc;
    ```

3. Tekintse meg az összes rendszerlekérdezést.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Láthatja, ahogy adatai szépen betöltődnek az adattárházba.

    ![A betöltött táblák megtekintése](media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="authenticate-using-managed-identities-to-load-optional"></a>Hitelesítés felügyelt identitások használatával a betöltéshez (nem kötelező)
A legbiztonságosabb módszer a virtuális hálózati szolgáltatásbeli végpontok kihasználása és a felügyelt identitások használatával történő betöltés. 

### <a name="prerequisites"></a>Előfeltételek
1.  Azure PowerShell telepítése az [útmutató](https://docs.microsoft.com/powershell/azure/install-az-ps)segítségével.
2.  Ha rendelkezik általános célú v1-vagy blob Storage-fiókkal, először az [útmutató](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)használatával kell frissítenie az általános célú v2-re.
3.  Engedélyeznie kell, **hogy a megbízható Microsoft-szolgáltatások hozzáférjenek ehhez a Storage-fiókhoz** az Azure Storage **-fiók tűzfala és a virtuális hálózatok** beállítások menüjében. További információért tekintse meg ezt az [útmutatót](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) .

#### <a name="steps"></a>Lépések
1. A PowerShellben **regisztrálja az SQL servert** Azure Active Directory (HRE) használatával:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
   
   1. Hozzon létre egy **általános célú v2 Storage-fiókot** az [útmutató](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)segítségével.

   > [!NOTE]
   >
   > - Ha rendelkezik általános célú v1-vagy blob Storage-fiókkal, először a **v2-re kell frissítenie** az [útmutató](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)segítségével.
   
1. A Storage-fiók területen navigáljon a **Access Control (iam)** elemre, és válassza a **szerepkör-hozzárendelés hozzáadása**elemet. Rendeljen hozzá **Storage blob-adatközreműködői** RBAC szerepkört a SQL Database-kiszolgálóhoz.

   > [!NOTE] 
   > Ezt a lépést csak a tulajdonosi jogosultsággal rendelkező tagok hajthatják végre. Az Azure-erőforrások különböző beépített szerepköreiért tekintse meg ezt az [útmutatót](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Alapszintű kapcsolat az Azure Storage-fiókkal:**
  
   1. Hozza létre az adatbázishoz tartozó hatókörrel rendelkező hitelesítő adatokat az **Identity = ' Managed Service Identity '** használatával:

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Nincs szükség a titkos kulcs megadására az Azure Storage-hozzáférési kulccsal, mert ez a mechanizmus [felügyelt identitást](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) használ a borítók alatt.
       > - Az identitás nevének **"Managed Service Identity"** kell lennie az Azure Storage-fiókkal való együttműködéshez.
   
   1. Hozza létre a külső adatforrást, amely megadja az adatbázishoz tartozó hatókörrel rendelkező hitelesítő adatokat a Managed Service Identity.
     
   1. Lekérdezés normál módon [külső táblák](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)használatával.

Ha az Azure szinapszis Analytics szolgáltatáshoz virtuális hálózati szolgáltatási végpontokat szeretne beállítani, tekintse meg az alábbi [dokumentációt](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) . 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adattárházába betöltött számítási erőforrások és adatok díjkötelesek. Ezeket külön-külön számlázzuk. 

- Ha szeretné az adatokat megtartani a tárolóban, a számítási erőforrásokat szüneteltetheti, amíg nem használja az adattárházat. A számítás felfüggesztése esetén csak az adattárolásért kell fizetni, és folytathatja a számítást, amikor már készen áll az adatokkal való munkára.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is. 

Kövesse az alábbi lépéseket a fölöslegessé vált erőforrások eltávolítására.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza ki az adattárházat.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **szüneteltetés** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítás folytatásához kattintson a **Start**gombra.

3. Ha el szeretné távolítani az adattárházat, így nem számítja fel a számítás vagy a tárolás díját, válassza a **Törlés**lehetőséget.

4. A létrehozott SQL-kiszolgáló eltávolításához válassza a **mynewserver-20180430.database.Windows.net** lehetőséget az előző képen, majd válassza a **Törlés**lehetőséget.  Ezzel kapcsolatban legyen körültekintő, mert a kiszolgáló törlésével a kiszolgálóhoz rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza a **myResourceGroup**lehetőséget, majd válassza az **erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>Következő lépések 
Ennek az oktatóanyagnak a segítségével megtanulta, hogyan hozhat létre egy adattárházat, illetve egy felhasználót az adatok betöltéséhez. Külső táblákat hozott létre, hogy definiálhassa az Azure Storage-blobban tárolt adatok struktúráját, majd a PolyBase CREATE TABLE AS SELECT utasításával adatokat töltött be az adattárházába. 

A következőket hajtotta végre:
> [!div class="checklist"]
> * Egy adattárház létrehozása az Azure Portalon
> * Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
> * Csatlakozás az adattárházhoz az SSMS használatával
> * Adatok betöltésére kijelölt felhasználó létrehozása
> * Külső táblák létrehozása az Azure Storage-blobban található adatokhoz
> * Adatok betöltése az adattárházba a CTAS T-SQL-utasítás használatával
> * Az adatok állapotának megtekintése betöltés közben
> * Statisztikák készítése az újonnan betöltött adatokról

Folytassa a fejlesztési áttekintéssel, amelyből megtudhatja, hogyan telepíthet át egy meglévő adatbázist az Azure szinapszis Analytics szolgáltatásba.

> [!div class="nextstepaction"]
> [Megtervezheti a meglévő adatbázisok Azure szinapszis Analytics szolgáltatásba való átépítésének döntéseit](sql-data-warehouse-overview-migrate.md)
