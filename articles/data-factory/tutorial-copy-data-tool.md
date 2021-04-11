---
title: Adatok másolása az Azure Blob Storage-ból az SQL-be Adatok másolása eszköz használatával
description: Hozzon létre egy Azure Data Factory, majd a Adatok másolása eszköz használatával másolja át az Azure Blob Storage-ból egy SQL Databaseba.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 1540e088565f69ca6d923202ad9b32b8d4ccf0ee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584438"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Adatok másolása az Azure Blob Storage-ból egy SQL Databaseba a Adatok másolása eszközzel

> [!div class="op_single_selector" title1="Válassza ki a használni kívánt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután a Adatok másolása eszközzel létrehozhat egy folyamatot, amely az Azure Blob Storage-ból másol egy SQL Database.

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:
> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure Storage-fiók**: a blob Storage-t használja _forrásként_ szolgáló adattárként. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozása](../storage/common/storage-account-create.md)című témakör utasításait.
* **Azure SQL Database**: használjon egy SQL Database a fogadó _adattárként_ . Ha nem rendelkezik SQL Databaseval, tekintse meg a [SQL Database létrehozása](../azure-sql/database/single-database-create-quickstart.md)című témakör utasításait.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

A következő lépések végrehajtásával készítse elő a blob Storage-t és a SQL Database az oktatóanyaghoz.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a **Jegyzettömböt**. Másolja be a következő szöveget, és mentse a lemezen egy **inputEmp.txt** nevű fájlba:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Hozzon létre egy **adfv2tutorial** nevű tárolót és töltse fel az inputEmp.txt fájlt a tárolóba. A feladatok végrehajtásához használhatja a Azure Portal vagy különféle eszközöket, például a [Azure Storage Explorer](https://storageexplorer.com/) .

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

1. A következő SQL-szkripttel hozzon létre egy **dbo. EMP** nevű táblát a SQL Databaseban:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Ellenőrizze, hogy engedélyezve van **-e az Azure-szolgáltatások és-erőforrások elérésének engedélyezése a kiszolgáló** számára a SQL Database rendszert futtató kiszolgálón. Ezzel a beállítással engedélyezheti, hogy a Data Factory adatokat írjon az adatbázispéldányra. A beállítás ellenőrzéséhez és bekapcsolásához lépjen a logikai SQL Server > biztonsági > tűzfalak és virtuális hálózatok elemre > állítsa be az **Azure-szolgáltatások és-erőforrások engedélyezése a kiszolgálón való elérésre** **lehetőséget.**

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. A bal oldali menüben válassza az **erőforrás**-  >  **integráció** létrehozása  >  **Data Factory**:

    ![Új adat-előállító létrehozása](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

    Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Új adatelőállító-hibaüzenet az ismétlődő névhez.":::

    Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_**ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
1. Válassza ki az Azure-**előfizetést** az új adat-előállító létrehozásához.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét.
    
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket.

1. A **Verzió** résznél válassza a **V2** értéket.
1. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más helyeken/régiókban is lehetnek.
1. Válassza a **Létrehozás** lehetőséget.

1. A létrehozás befejezése után a **Data Factory** kezdőlapja jelenik meg.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="A Azure Data Factory kezdőlapja, a szerző & monitor csempével.":::
1. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő megnyitásához kattintson a **Tartalomkészítés és monitorozás** csempére.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az Adatok másolása eszköz elindításához az **első lépéseket** ismertető oldalon kattintson az **Adatok másolása** csempére.

    ![Az Adatok másolása eszköz csempéje](./media/doc-common-process/get-started-page.png)
1. A **Tulajdonságok** lap **Feladat neve** részénél adja meg a következőt: **CopyFromBlobToSqlPipeline**. Ezután kattintson a **Tovább** gombra. A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven.
    ![Folyamat létrehozása](./media/tutorial-copy-data-tool/create-pipeline.png)

1. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    b. Válassza ki az **Azure Blob Storage** elemet a katalógusból, majd válassza a **Folytatás** lehetőséget.

    c. Az **új társított szolgáltatás** lapon válassza ki az Azure-előfizetését, és válassza ki a Storage-fiók **nevét a Storage-fiók neve** listából. Tesztelje a kapcsolatokat, majd válassza a **Létrehozás** lehetőséget.

    d. Forrásnak válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

    ![Forráshoz társított szolgáltatás kiválasztása](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

    a. A **Tallózás** elemre kattintva lépjen az **adfv2tutorial/input** mappához, válassza az **inputEmp.txt** fájlt, majd kattintson a **Kiválasztás** lehetőségre.

    b. A következő lépéshez kattintson a **Tovább** gombra.

1. A **File Format Settings (fájlformátum beállításai** ) lapon engedélyezze az *első sor fejlécként* való bejelölését. Figyelje meg, hogy az eszköz automatikusan észleli az oszlopok és a sorok határolóit. Kattintson a **Tovább** gombra. Ezen a lapon megtekintheti az adatokat is, és megtekintheti a bemeneti adatok sémáját.

    ![A Fájlformátum beállításai](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. A **Céladattár** oldalon hajtsa végre a következő lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    b. Válassza ki **Azure SQL Database** a katalógusból, majd válassza a **Folytatás** lehetőséget.

    c. Az **új társított szolgáltatás** lapon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, majd adja meg a felhasználónevet és a jelszót, majd kattintson a **Létrehozás** gombra.

    ![Azure SQL-adatbázis konfigurálása](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Válassza ki fogadóként az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

1. A **Tábla hozzárendelése** oldalon válassza ki a **[dbo].[emp]** táblát, majd kattintson a **Tovább** gombra.

1. Figyelje meg, hogy az **oszlop-hozzárendelés** oldalon a bemeneti fájl második és harmadik oszlopa az **EMP** tábla **FirstName** és **LastName** oszlopára van leképezve. Állítsa be úgy a leképezést, hogy ne legyen hiba, majd válassza a **tovább** lehetőséget.

    ![Oszlop-hozzárendelési lap](./media/tutorial-copy-data-tool/column-mapping.png)

1. A **Beállítások** lapon kattintson a **Tovább** gombra.

1. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább** gombra.

1. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.

    ![Folyamat figyelése](./media/tutorial-copy-data-tool/monitor-pipeline.png)
    
1. A folyamat futtatása lapon kattintson a **frissítés** gombra a lista frissítéséhez. Kattintson a **folyamat neve** alatt látható hivatkozásra a tevékenység futtatási részleteinek megtekintéséhez vagy a folyamat újrafuttatásához. 
    ![Folyamat futtatása](./media/tutorial-copy-data-tool/pipeline-run.png)

1. A másolási művelettel kapcsolatos további információkért a tevékenység futtatása lapon válassza a **részletek** hivatkozást (szemüveg ikon) a **tevékenység neve** oszlopban. Ha vissza szeretne térni a folyamat futási nézetéhez, válassza a **minden folyamat futtatása** hivatkozást a navigációs menüben. A nézet frissítéséhez válassza a **Frissítés** parancsot.

    ![Tevékenységfuttatások monitorozása](./media/tutorial-copy-data-tool/activity-monitoring.png)

1. Ellenőrizze, hogy a rendszer beszúrja-e az adatait a SQL Database **dbo. EMP** táblájába.

1. A szerkesztő módra való váltáshoz kattintson a bal oldalon található **Létrehozás** fülre. A szerkesztővel frissítheti a társított szolgáltatásokat, az adatkészleteket és az eszközzel létrehozott folyamatokat. Az entitások Data Factory felhasználói felületen való szerkesztéséről [a jelen oktatóanyag Azure Portal-verziójában](tutorial-copy-data-portal.md) talál további információt.

    ![Szerző lap kiválasztása](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Következő lépések
Az ebben a példában szereplő folyamat átmásolja az adatait a blob Storage-ból egy SQL Databaseba. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

A következő oktatóanyagra lépve megismerheti az adatok helyszíni rendszerből felhőre való másolásának folyamatát:

>[!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-data-tool.md)
