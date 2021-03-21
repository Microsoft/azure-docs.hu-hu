---
title: SQL Database-referenciák használata Azure Stream Analytics feladatokban
description: Ez a cikk azt ismerteti, hogyan használhatók a SQL Database a Azure Stream Analytics feladatokhoz a Azure Portal és a Visual Studióban.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: e7d16de8a7a5c6f5353d64e25580b19845ce96c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016405"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Azure Stream Analytics feladatokhoz tartozó SQL Database hivatkozási adatainak használata

Azure Stream Analytics támogatja a Azure SQL Database a hivatkozási adatok forrásaként. A Azure Portal és a Visual Studióban Stream Analytics eszközökkel SQL Database is használhatja a Stream Analytics feladatokra vonatkozó hivatkozási adatként. Ez a cikk bemutatja, hogyan végezheti el mindkét módszert.

## <a name="azure-portal"></a>Azure Portal

A következő lépésekkel adhat hozzá Azure SQL Database hivatkozásként megadott bemeneti forrásként a Azure Portal használatával:

### <a name="portal-prerequisites"></a>A portál előfeltételei

1. Stream Analytics-feladat létrehozása.

2. Hozzon létre egy Storage-fiókot, amelyet a Stream Analytics feladatokhoz szeretne használni.

3. Hozza létre a Azure SQL Database egy olyan adatkészlettel, amelyet a Stream Analytics feladatainak hivatkozási adatként kell használni.

### <a name="define-sql-database-reference-data-input"></a>Adja meg SQL Database a hivatkozási adatok bemenetét

1. A Stream Analyticsi feladatban válassza a **feladatok topológia** alatt található **bemenetek** lehetőséget. Kattintson a **hivatkozás hozzáadása** elemre, és válassza a **SQL Database** lehetőséget.

   ![A bal oldali navigációs ablaktáblán a bemenetek elem van kiválasztva. A bemeneteken a + hivatkozás hozzáadása elem be van jelölve, és megjelenít egy legördülő listát, amely megjeleníti a blob Storage és a SQL Database értékeket.](./media/sql-reference-data/stream-analytics-inputs.png)

2. Töltse ki a Stream Analytics bemeneti konfigurációját. Válassza ki az adatbázis nevét, a kiszolgáló nevét, a felhasználónevet és a jelszót. Ha azt szeretné, hogy a rendszer rendszeres időközönként frissítse a hivatkozási adatokat, válassza a "bekapcsolva" lehetőséget a frissítési sebesség megadásához a DD: HH: PP lapon. Ha a nagyméretű adatkészletek rövid frissítési gyakorisággal rendelkeznek, használhat [különbözeti lekérdezést](sql-reference-data.md#delta-query)is.

   ![Ha SQL Database van kiválasztva, megjelenik a SQL Database új bemenet lap. A bal oldali ablaktáblán egy konfigurációs űrlap található, a jobb oldali ablaktáblán pedig egy pillanatkép-lekérdezés található.](./media/sql-reference-data/sql-input-config.png)

3. Tesztelje a pillanatkép-lekérdezést az SQL-lekérdezés szerkesztőjében. További információ: [a Azure Portal SQL-lekérdezési szerkesztőjének használata a kapcsolódáshoz és az adatok lekérdezéséhez](../azure-sql/database/connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Storage-fiók meghatározása a feladatok konfigurációjában

Navigáljon a **Storage-fiók beállításaihoz** a **Konfigurálás** területen, majd válassza a **Storage-fiók hozzáadása** lehetőséget.

   ![A Storage-fiók beállításai a bal oldali ablaktáblán vannak kiválasztva. A jobb oldali ablaktáblán található egy Storage-fiók hozzáadása gomb.](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>A feladat indítása

Ha más bemeneteket, kimeneteket és lekérdezést konfigurált, elindíthatja a Stream Analytics feladatot.

## <a name="tools-for-visual-studio"></a>Visual Studio-eszközök

A következő lépésekkel adhatja hozzá a Azure SQL Databaset a Visual Studio használatával, hivatkozásként szolgáló bemeneti forrásként:

### <a name="visual-studio-prerequisites"></a>A Visual Studio előfeltételei

1. [Telepítse a Visual studióhoz készült stream Analytics-eszközöket](stream-analytics-tools-for-visual-studio-install.md). A Visual Studio következő verziói támogatottak:

   * Visual Studio 2015
   * Visual Studio 2019

2. Ismerkedjen meg a [Visual Studio stream Analytics eszközeivel](stream-analytics-quick-create-vs.md) .

3. Tárfiók létrehozása.

### <a name="create-a-sql-database-table"></a>SQL Database tábla létrehozása

A SQL Server Management Studio használatával hozzon létre egy táblázatot a hivatkozási adatai tárolásához. A részletekért lásd: [az első Azure SQL Database megtervezése a SSMS használatával](../azure-sql/database/design-first-database-tutorial.md) .

Az alábbi példában a következő utasításban használt példában szereplő táblázat jött létre:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Válassza ki az előfizetését

1. A Visual Studio **View** (Nézet) menüjében válassza a **Server Explorer** (Kiszolgálókezelő) lehetőséget.

2. Kattintson a jobb gombbal az **Azure**-ra, válassza a **Kapcsolódás Microsoft Azure előfizetéshez** lehetőséget, majd jelentkezzen be az Azure-fiókjával.

### <a name="create-a-stream-analytics-project"></a>Stream Analytics-projekt létrehozása

1. Válassza a **File > New Project** (Fájl > Új projekt) lehetőséget. 

2. A bal oldali sablonlistában válassza ki a **Stream Analytics**, majd az **Azure Stream Analytics Application** (Azure Stream Analytics-alkalmazás) elemet. 

3. Adja meg a projekt **nevét**, **helyét** és a **megoldás nevét**, majd kattintson **az OK gombra**.

   ![A Stream Analytics sablon ki van választva, Azure Stream Analytics alkalmazás van kiválasztva, és a név, a hely és a megoldás neve mezők ki vannak emelve.](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Adja meg SQL Database a hivatkozási adatok bemenetét

1. Hozzon létre egy új bemenetet.

   ![Az új elem hozzáadása párbeszédpanelen a bemenet lehetőség van kiválasztva.](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Kattintson duplán a **Input.js** elemre a **megoldáskezelő**.

3. Töltse ki a **stream Analytics bemeneti konfigurációját**. Válassza ki az adatbázis nevét, a kiszolgáló nevét, a frissítés típusát és a frissítési gyakoriságot. A frissítési gyakoriságot a formátumban kell megadni `DD:HH:MM` .

   ![Stream Analytics bemeneti konfigurációban az értékek a legördülő listából lesznek megadva vagy kiválasztva.](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Ha a "végrehajtás csak egyszer" vagy a "végrehajtás rendszeresen" lehetőséget választja, a rendszer egy **[bemeneti alias]** nevű SQL Codebehind fájlt hoz létre a projektben a **Input.js** fájl csomóponton.

   ![Az SQL CodeBehind fájl Chemicals. Snapshot. SQL kiemelve.](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Ha a "rendszeres időközönként frissül a Delta szolgáltatással" lehetőséget választja, két SQL-CodeBehind-fájl jön létre: **[bemeneti alias]. Snapshot. SQL** és **[input alias]. Delta. SQL**.

   ![Az SQL CodeBehind Files Chemicals. Delta. SQL és Chemicals. Snapshot. SQL van kiemelve.](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Nyissa meg az SQL-fájlt a szerkesztőben, és írja be az SQL-lekérdezést.

5. Ha a Visual Studio 2019-at használja, és telepítette SQL Server adateszközöket, a **végrehajtás** gombra kattintva tesztelheti a lekérdezést. A varázsló ablakában megjelenik a SQL Databasehoz való kapcsolódás, és a lekérdezés eredménye a lenti ablakban jelenik meg.

### <a name="specify-storage-account"></a>Storage-fiók meghatározása

Nyissa meg **JobConfig.jsa** (z) lehetőséget, hogy megadja a Storage-FIÓKOT az SQL-hivatkozási Pillanatképek tárolására.

   ![Stream Analytics a konfiguráció konfigurálása beállítás alapértelmezett értékekkel jelenik meg. A globális tárolási beállítások ki vannak emelve.](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Helyi tesztelés és üzembe helyezés az Azure-ban

Mielőtt üzembe helyezné a feladatot az Azure-ban, a lekérdezési logikát helyileg is tesztelheti az élő bemeneti adatokkal. A szolgáltatással kapcsolatos további információkért tekintse meg az [élő adatok helyi tesztelése a Visual studióhoz készült Azure stream Analytics Tools (előzetes verzió)](stream-analytics-live-data-local-testing.md)című témakört. Ha végzett a teszteléssel, kattintson **a Küldés az Azure**-ba lehetőségre. A feladatok elindításával kapcsolatos további információkért tekintse meg az [stream Analytics létrehozása a Visual studiohoz készült Azure stream Analytics Tools használatával](stream-analytics-quick-create-vs.md) című útmutatót.

## <a name="delta-query"></a>Különbözeti lekérdezés

A különbözeti lekérdezés használatakor a rendszer [Azure SQL Database ideiglenes táblákat](../azure-sql/temporal-tables.md) ajánlott használni.

1. Hozzon létre egy ideiglenes táblázatot a Azure SQL Databaseban.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. A pillanatkép-lekérdezés szerzője. 

   A **\@ snapshotTime** paraméter használatával utasítsa a stream Analytics futtatókörnyezetet, hogy a rendszeridőben érvényes SQL Database ideiglenes táblából származó hivatkozási adatkészletet szerezze be. Ha nem adja meg ezt a paramétert, akkor egy pontatlan alapszintű hivatkozási adat beszerzésére van szükség az órajel-eltérések miatt. Alább látható egy példa a teljes pillanatkép-lekérdezésre:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. A különbözeti lekérdezés szerzője. 
   
   Ez a lekérdezés lekérdezi a SQL Database összes olyan sorát, amelyeket a rendszer a kezdési időpontban, a **\@ deltaStartTime** és a befejezési idő **\@ deltaEndTime** belül beszúrt vagy törölt. A különbözeti lekérdezésnek a pillanatkép-lekérdezéssel megegyező oszlopokat, valamint az oszlop **_műveletét_** kell visszaadnia. Ez az oszlop határozza meg, hogy a sor be van-e beszúrva vagy törölve a **\@ deltaStartTime** és a **\@ deltaEndTime** között. Az eredményül kapott sorok **1** -ként vannak megjelölve, ha a rekordok be lettek helyezve, vagy **2** Ha törölve lettek. A lekérdezésnek hozzá kell adnia egy **vízjelet** is a SQL Server oldalról, hogy a különbözeti időszak összes frissítése megfelelően rögzítve legyen. A **vízjel** nélküli különbözeti lekérdezés helytelen hivatkozási adatkészletet eredményezhet.  

   A frissített rekordok esetében az időbeli táblázat a beszúrási és törlési művelet rögzítésével végzi a könyvelést. A Stream Analytics futtatókörnyezet Ezután alkalmazza a különbözeti lekérdezés eredményét az előző pillanatképre, hogy a hivatkozási adatok naprakészek maradjanak. A különbözeti lekérdezés példája a következő:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as _watermark_, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as _watermark_, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Vegye figyelembe, hogy Stream Analytics futtatókörnyezet időszakosan futtathatja a pillanatkép-lekérdezést az ellenőrzőpontok tárolására szolgáló különbözeti lekérdezésen kívül is.

## <a name="test-your-query"></a>A lekérdezés tesztelése
   Fontos ellenőrizni, hogy a lekérdezés visszaadja-e a várt adatkészletet, amelyet a Stream Analytics-feladatok hivatkozási adatként fognak használni. A lekérdezés teszteléséhez nyissa meg a bekapcsolást a portálon, a feladatok szakaszban. Ezután kiválaszthatja a mintaadatok a SQL Database hivatkozás bemenetén. A minta elérhetővé válása után letöltheti a fájlt, és megtekintheti, hogy a visszaadott információk a várt módon működnek-e. Ha optimalizálni szeretné a fejlesztési és tesztelési iterációkat, javasoljuk, hogy használja a [Visual Studio stream Analytics eszközeit](./stream-analytics-tools-for-visual-studio-install.md). Azt is megteheti, hogy az előnyben részesített bármely más eszközt, hogy a lekérdezés a megfelelő eredményeket adja vissza Azure SQL Database, majd használja azt a Stream Analytics feladatokban. 

### <a name="test-your-query-with-visual-studio-code"></a>A lekérdezés tesztelése a Visual Studio Code-ban

   Telepítse [Azure stream Analytics eszközöket](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) és [SQL Server (MSSQL)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) a Visual Studio Code-ra, és állítsa be az ASA-projektet. További információ: gyors útmutató [: Azure stream Analytics-feladatok létrehozása a Visual Studio Code-ban](./quick-create-visual-studio-code.md) és a [SQL Server (MSSQL) bővítmény oktatóanyagában](/sql/tools/visual-studio-code/sql-server-develop-use-vscode).

1. Adja meg az SQL-hivatkozási adatok bemenetét.
   
   ![A Visual Studio Code Editor (TAB) megjeleníti a ReferenceSQLDatabase.js.](./media/sql-reference-data/configure-sql-reference-data-input.png)

2. Válassza a SQL Server ikont, majd kattintson a **kapcsolatok hozzáadása** lehetőségre.
   
   ![+ A kapcsolatok hozzáadása elem megjelenik a bal oldali ablaktáblán, és ki van emelve.](./media/sql-reference-data/add-sql-connection.png)

3. Adja meg a kapcsolatok adatait.
   
   ![Az adatbázis és a kiszolgáló adatainak két mezője van kiemelve.](./media/sql-reference-data/fill-connection-information.png)

4. Kattintson a jobb gombbal a Reference SQL elemre, és válassza a **lekérdezés végrehajtása** lehetőséget.
   
   ![A végrehajtási lekérdezés ki van emelve a helyi menüben.](./media/sql-reference-data/execute-query.png)

5. Válassza ki a kívánt kapcsolatokat.
   
   ![A következő párbeszédpanel azt mutatja be, hogy "hozzon létre egy kapcsolatprofil-profilt az alábbi listából", és a lista egy bejegyzést tartalmaz, amely hightlighted.](./media/sql-reference-data/choose-connection.png)

6. Tekintse át és ellenőrizze a lekérdezés eredményét.
   
   ![A lekérdezés keresési eredményei a VS Code Editor lapon találhatók.](./media/sql-reference-data/verify-result.png)


## <a name="faqs"></a>Gyakori kérdések

**Felmerülhetek-e további költségek az SQL-alapú adatbevitel Azure Stream Analyticsban való használatával?**

A Stream Analytics-feladatokban nem használhatók további [díjszabási egységenként](https://azure.microsoft.com/pricing/details/stream-analytics/) . A Stream Analytics feladatokhoz azonban hozzá kell rendelni egy társított Azure Storage-fiókot. A Stream Analytics feladata lekérdezi az SQL DB-t (a feladatok indítási és frissítési időköze alatt) a hivatkozási adathalmaz lekérésére és a pillanatképnek a Storage-fiókban való tárolására. Ezeknek a pillanatképeknek a tárolása az Azure Storage-fiók [díjszabási oldalának](https://azure.microsoft.com/pricing/details/storage/) részletes díját is felmerül.

**Hogyan tudja, hogy a rendszer az SQL DB-ből kérdezi le, és felhasználja a Azure Stream Analytics feladatokban?**

A logikai név alapján két mérőszám van szűrve (a metrikák Azure Portal alatt), amelyek segítségével figyelheti a SQL Database-hivatkozás adatbevitelének állapotát.

   * InputEvents: Ez a metrika a SQL Database hivatkozási adatkészletből betöltött rekordok számát méri.
   * InputEventBytes: Ez a metrika a Stream Analytics feladathoz tartozó memóriában betöltött hivatkozási adatok pillanatképének méretét méri. 

Mindkét metrika kombinációja felhasználható annak a megállapítására, hogy a feladatnak van-e lekérdezése SQL Database a hivatkozási adathalmaz beolvasásához, majd a memóriába való betöltéséhez.

**Szükség van-e a Azure SQL Database speciális típusára?**

Azure Stream Analytics bármilyen típusú Azure SQL Databasevel működni fog. Fontos azonban megérteni, hogy a hivatkozási adatok bemenetére beállított frissítési sebesség hatással lehet a lekérdezés terhelésére. A különbözeti lekérdezési beállítás használatához ajánlott az időbeli táblák használata Azure SQL Databaseban.

**Miért Azure Stream Analytics a pillanatképek tárolása az Azure Storage-fiókban?**

A Stream Analytics garantáltan pontosan egyszer dolgozza fel az eseményeket, és legalább egyszer kézbesíti az eseményeket. Azokban az esetekben, amikor az átmeneti problémák hatással vannak a feladatra, az állapot visszaállításához kis mennyiségű visszajátszás szükséges. A visszajátszás engedélyezéséhez az szükséges, hogy a pillanatképek egy Azure Storage-fiókban legyenek tárolva. Az ellenőrzőpontok újrajátszása szolgáltatással kapcsolatos további információkért lásd: [ellenőrzőpontok és Replay fogalmak Azure stream Analytics feladatokban](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Következő lépések

* [A Stream Analytics-keresések hivatkozási adatainak használata](stream-analytics-use-reference-data.md)
* [Gyors útmutató: Stream Analytics-feladat létrehozása a Visual Studio Azure Stream Analytics eszközeinek használatával](stream-analytics-quick-create-vs.md)
* [Élő adatellenőrzés helyi tesztelése a Visual Studio Azure Stream Analytics eszközeivel (előzetes verzió)](stream-analytics-live-data-local-testing.md)