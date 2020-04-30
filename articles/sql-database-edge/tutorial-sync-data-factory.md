---
title: Adatok szinkronizálása Azure SQL Database Edge-ből Azure Data Factory használatával | Microsoft Docs
description: Tudnivalók az Azure SQL Database Edge és az Azure Blob Storage közötti adatszinkronizálásról
keywords: SQL Database Edge, adatok szinkronizálása az SQL Database Edge-ből, az SQL Database Edge-adatgyárból
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: e6fd9e6431137708ba93328a8ed1359b93b4ee1f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74851689"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Oktatóanyag: adatok szinkronizálása SQL Database Edge-ből az Azure Blob Storage-ba Azure Data Factory használatával

Ebben az oktatóanyagban a Azure Data Factory használatával fokozatosan szinkronizálja az Azure Blob Storage-ba az adatok egy Azure SQL Database Edge-példányban lévő táblából.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem hozott létre adatbázist vagy táblát a Azure SQL Database Edge-telepítésben, akkor az alábbi módszerek egyikével hozzon létre egyet:

* Az SQL Database Edge-hez való kapcsolódáshoz használjon [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) vagy [Azure Data Studio](/sql/azure-data-studio/download/) . Futtasson egy SQL-szkriptet az adatbázis és a tábla létrehozásához.
* Hozzon létre egy SQL-adatbázist és-táblázatot a [Sqlcmd](/sql/tools/sqlcmd-utility/) használatával az SQL Database Edge-modulhoz való közvetlen csatlakozással. További információ: [Kapcsolódás az adatbázismotor számára a Sqlcmd használatával](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* A SQLPackage. exe használatával helyezzen üzembe egy DAC-csomagfájl SQL Database Edge-tárolón. Ezt a folyamatot automatizálhatja a SqlPackage fájl URI azonosítójának megadásával a modul kívánt tulajdonságainak konfigurációjának részeként. Közvetlenül is használhatja a SqlPackage. exe-ügyfél eszközt egy DAC-csomag SQL Database Edge-re való telepítéséhez.

    További információ a SqlPackage. exe fájl letöltéséről: [SqlPackage letöltése és telepítése](/sql/tools/sqlpackage-download/). A következő néhány példa a SqlPackage. exe parancsra. További információt a SqlPackage. exe dokumentációjában talál.

    **DAC-csomag létrehozása**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **DAC-csomag alkalmazása**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>SQL-tábla és-eljárás létrehozása a vízjel szintjeinek tárolására és frissítésére

A rendszer egy vízjel táblát használ az utolsó időbélyeg tárolására, amely az adatokat már szinkronizálta az Azure Storage szolgáltatással. A rendszer Transact-SQL (T-SQL) tárolt eljárást használ a vízjelek táblázatának minden szinkronizálás után történő frissítéséhez.

Futtassa ezeket a parancsokat az SQL Database Edge-példányon:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Data Factory folyamat létrehozása

Ebben a szakaszban egy Azure Data Factory folyamatot fog létrehozni az adatok Azure Blob Storage-ba való szinkronizálásához Azure SQL Database Edge-táblázatból.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Adatelőállító létrehozása a Data Factory felhasználói felületének használatával

Hozzon létre egy adatelőállítót az [oktatóanyag](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)utasításait követve.

### <a name="create-a-data-factory-pipeline"></a>Data Factory folyamat létrehozása

1. A Data Factory felhasználói felületének első **lépések** oldalán válassza a **folyamat létrehozása**lehetőséget.

    ![Data Factory folyamat létrehozása](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. A folyamat **Tulajdonságok** ablakának **általános** lapján adja meg a **PeriodicSync** nevet.

3. Adja hozzá a keresési tevékenységet a régi vízjel értékének lekéréséhez. A **tevékenységek** ablaktáblán bontsa ki az **általános** elemet, és húzza a **keresési** tevékenységet a folyamat tervező felületére. Módosítsa a tevékenység nevét a **OldWatermark**értékre.

    ![A régi vízjel-keresés hozzáadása](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Váltson a **Beállítások** lapra, és válassza az **új** lehetőséget a **forrás adatkészlethez**. Most létrehoz egy adatkészletet, amely az adatokat jelöli a vízjel táblában. Ez a tábla tartalmazza az előző másolási művelet során használt régi küszöbértéket.

5. Az **új adatkészlet** ablakban válassza az **Azure SQL Server**lehetőséget, majd kattintson a **Folytatás**gombra.  

6. Az adatkészlet **tulajdonságok beállítása** ablakában, a név mezőben adja meg a **WatermarkDataset** **nevet**.

7. A **társított szolgáltatás**esetében válassza az **új**lehetőséget, majd hajtsa végre a következő lépéseket:

    1. A **név**mezőben adja meg a **SQLDBEdgeLinkedService**.

    2. A **kiszolgáló neve**alatt adja meg az SQL Database Edge-kiszolgáló adatait.

    3. Válassza ki az **adatbázis nevét** a listából.

    4. Adja meg a **felhasználónevét** és a **jelszavát**.

    5. Az SQL Database Edge-példányhoz való kapcsolódás teszteléséhez válassza a **Kapcsolódás tesztelése**lehetőséget.

    6. Kattintson a **Létrehozás** gombra.

    ![Társított szolgáltatás létrehozása](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Kattintson az **OK** gombra.

8. A **Beállítások** lapon válassza a **Szerkesztés**lehetőséget.

9. A **kapcsolatok** lapon válassza a **[dbo] lehetőséget. [ watermarktable]** a **táblához**. Ha meg szeretné tekinteni az adatmegjelenítést a táblázatban, válassza az **előnézet**-adatelemet.

10. Váltson a folyamat-szerkesztőre a felső részen található folyamat lapon, vagy a bal oldali fanézetben a folyamat nevének kiválasztásával. A keresési tevékenység tulajdonságok ablakában ellenőrizze, hogy a **WatermarkDataset** van-e kiválasztva a **forrás adatkészlet** listájában.

11. A **tevékenységek** ablaktáblán bontsa ki az **általános** elemet, és húzzon egy másik **keresési** tevékenységet a folyamat tervező felületére. Állítsa a **NewWatermark** nevet a Tulajdonságok ablak **általános** lapján. Ez a keresési tevékenység beolvassa a forrás adatait tartalmazó táblázat új küszöbértékét, hogy a rendszer átmásolja a célhelyre.

12. A második keresési tevékenység tulajdonságok ablakában váltson a **Beállítások** lapra, és az **új** elemre kattintva hozzon létre egy adatkészletet, amely az új küszöbértéket tartalmazó táblára mutat.

13. Az **új adatkészlet** ablakban válassza ki **SQL Database Edge-példányt**, majd válassza a **Folytatás**lehetőséget.

    1. A **készlet tulajdonságai** ablakban, a név mezőben adja meg a **SourceDataset** **nevet**. A **társított szolgáltatás**területen válassza a **SQLDBEdgeLinkedService**lehetőséget.

    2. A **táblázat**alatt válassza ki a szinkronizálni kívánt táblát. Ehhez az adatkészlethez is megadhat egy lekérdezést az oktatóanyag későbbi részében leírtak szerint. A lekérdezés elsőbbséget élvez az ebben a lépésben megadott táblázatban.

    3. Kattintson az **OK** gombra.

14. Váltson a folyamat-szerkesztőre a felső részen található folyamat lapon, vagy a bal oldali fanézetben a folyamat nevének kiválasztásával. A keresési tevékenység tulajdonságok ablakában ellenőrizze, hogy a **SourceDataset** van-e kiválasztva a **forrás adatkészlet** listájában.

15. A lekérdezés **használata**területen válassza a **lekérdezés** lehetőséget. Frissítse a tábla nevét a következő lekérdezésben, majd írja be a lekérdezést. Csak a tábla maximális értékét `timestamp` választja ki. Ügyeljen arra, hogy **csak az első sor**legyen kiválasztva.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![lekérdezés kiválasztása](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. A **tevékenységek** ablaktáblán bontsa ki az **Áthelyezés & átalakítás** elemet, és húzza a **Másolás** tevékenységet a **tevékenységek** ablaktábláról a tervező felületére. Állítsa a tevékenység nevét **IncrementalCopy**értékre.

17. A keresési tevékenységhez csatolt zöld gombot a másolási tevékenységhez húzva kapcsolja mindkét keresési tevékenységet a másolási tevékenységhez. Ha a másolási tevékenység szegélyének színe kékre változik, engedje el az egérgombot.

18. Válassza ki a másolási tevékenységet, és győződjön meg arról, hogy a **tulajdonságok** ablakában megjelennek a tevékenység tulajdonságai.

19. Váltson a **forrás** lapra a **Tulajdonságok** ablakában, és hajtsa végre a következő lépéseket:

    1. A **forrás adatkészlete** mezőben válassza a **SourceDataset**lehetőséget.

    2. A **lekérdezés használata**alatt válassza a **lekérdezés**lehetőséget.

    3. Adja meg az SQL-lekérdezést a **lekérdezési** mezőben. Példa egy lekérdezésre:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. A fogadó **lapon válassza** az **új** a fogadó **adatkészlet**lehetőséget.

21. Ebben az oktatóanyagban a fogadó adattár egy Azure Blob Storage-adattár. Válassza az **Azure Blob Storage**lehetőséget, majd válassza a **Folytatás** lehetőséget az **új adatkészlet** ablakban.

22. A **formátum kiválasztása** ablakban válassza ki az adatformátumot, majd kattintson a Continue ( **Folytatás**) elemre.

23. A **készlet tulajdonságai** ablakban, a név mezőben adja meg a **SinkDataset** **nevet**. A **társított szolgáltatás**területen válassza az **új**lehetőséget. Most létrehoz egy kapcsolatot (egy társított szolgáltatást) az Azure Blob Storage-hoz.

24. Az **új társított szolgáltatás (Azure Blob Storage)** ablakban végezze el a következő lépéseket:

    1. A név mezőbe írja be a **AzureStorageLinkedService** **nevet** .

    2. A **Storage-fiók neve**területen válassza ki az Azure-előfizetéshez tartozó Azure Storage-fiókot.

    3. Tesztelje a kapcsolatokat, majd válassza a **Befejezés**lehetőséget.

25. A **készlet tulajdonságai** ablakban ellenőrizze, hogy a **AzureStorageLinkedService** van-e kiválasztva a **társított szolgáltatás**területen. Válassza a **Létrehozás** és **az OK gombot**.

26. A **fogadó lapon válassza** a **Szerkesztés**lehetőséget.

27. Nyissa meg a SinkDataset **Kapcsolódás** lapját, és hajtsa végre a következő lépéseket:

    1. A **fájl elérési útja**területen adja meg a *asdedatasync/incrementalcopy*nevet, ahol a *asdedatasync* a blob-tároló neve, a *incrementalcopy* pedig a mappa neve. Ha nem létezik, hozza létre a tárolót, vagy használjon egy meglévőt. Azure Data Factory automatikusan létrehozza a kimeneti mappa *incrementalcopy* , ha az nem létezik. A **fájl elérési útjánál** a **Tallózás** gombot is használhatja a blobtárolóban található mappák megkereséséhez.

    2. A **fájl elérési útjának** **fájljának** részeként válassza a **dinamikus tartalom hozzáadása [ALT + P]** lehetőséget, majd írja be ** @CONCATa következőt: (növekményes, folyamat (). RunId, '. txt ')** a megnyíló ablakban. Válassza a **Finish** (Befejezés) elemet. A fájl nevét a kifejezés dinamikusan hozza létre. A folyamat minden futtatásához tartozik egy egyedi azonosító. A másolási tevékenység a futtatási azonosítót használja a fájlnév létrehozásához.

28. Váltson a folyamat-szerkesztőre a felső részen található folyamat lapon, vagy a bal oldali fanézetben a folyamat nevének kiválasztásával.

29. A **tevékenységek** ablaktáblán bontsa ki az **általános** elemet, és húzza a **tárolt eljárás** tevékenységet a **tevékenységek** ablaktábláról a folyamat-tervező felületre. A másolási tevékenység zöld (sikeres) kimenetének összekapcsolása a tárolt eljárási tevékenységgel.

30. Válassza a **tárolt eljárási tevékenység** lehetőséget a folyamat-tervezőben, és módosítsa a nevét a **SPtoUpdateWatermarkActivity**értékre.

31. Váltson az **SQL-fiók** lapra, és válassza a ***QLDBEdgeLinkedService** elemet a **társított szolgáltatás**területen.

32. Váltson a **tárolt eljárás** lapra, és hajtsa végre a következő lépéseket:

    1. A **tárolt eljárás neve**alatt válassza a **[dbo] lehetőséget. [ usp_write_watermark]**.

    2. A tárolt eljárás paraméterei értékének megadásához válassza a **paraméter importálása** lehetőséget, majd adja meg az alábbi értékeket a paraméterekhez:

    |Name (Név)|Típus|Érték|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {Activity ("NewWaterMark"). output. firstRow. NewWatermarkvalue}|
    |TableName|Sztring|@ {Activity ("OldWaterMark"). output. firstRow. Táblanév}|

33. A folyamat beállításainak érvényesítéséhez kattintson az **Érvényesítés** elemre az eszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A folyamat- **ellenőrzési jelentés** ablakának bezárásához **>>** válassza a elemet.

34. Tegye közzé az entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatokat) a Azure Data Factory szolgáltatáshoz az **összes közzététele** gomb kiválasztásával. Várjon, amíg megjelenik egy üzenet, amely megerősíti, hogy a közzétételi művelet sikeres volt.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Folyamat elindítása ütemterv alapján

1. A folyamat eszköztárán válassza az **trigger hozzáadása**lehetőséget, válassza az **új/szerkesztés**lehetőséget, majd válassza az **új**lehetőséget.

2. Nevezze el az trigger **HourlySync**. A **típus**területen válassza az **ütemterv**lehetőséget. Állítsa be az **ismétlődést** 1 óránként.

3. Kattintson az **OK** gombra.

4. Kattintson **Az összes közzététele** gombra.

5. Válassza az **aktiválás most**lehetőséget.

6. Váltson a bal oldali **Monitorozás** lapra. Láthatja a manuális eseményindítás által elindított folyamatfuttatás állapotát. A lista frissítéséhez kattintson a **Frissítés** gombra.

## <a name="next-steps"></a>További lépések

Az oktatóanyagban szereplő Azure Data Factory folyamat egy SQL Database Edge-példány táblájának adatait egy óránként egyszer másolja át az Azure Blob Storage-ba. Ha többet szeretne megtudni a Data Factory használatáról más forgatókönyvekben, tekintse meg ezeket az [oktatóanyagokat](../data-factory/tutorial-copy-data-portal.md).
