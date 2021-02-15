---
title: SQL Server tárolt eljárási tevékenység
description: Ebből a témakörből megtudhatja, hogyan hívhat meg egy Azure SQL Database vagy Azure szinapszis Analyticsben tárolt eljárást egy Data Factory folyamatból az SQL Server tárolt eljárási tevékenység használatával.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 05717352936bed888e108277d0163e43bc5a37af
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368761"
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server tárolt eljárási tevékenység
> [!div class="op_single_selector" title1="Átalakítási tevékenységek"]
> * [Struktúra tevékenysége](data-factory-hive-activity.md)
> * [Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce tevékenység](data-factory-map-reduce.md)
> * [Hadoop streaming-tevékenység](data-factory-hadoop-streaming-activity.md)
> * [Spark-tevékenység](data-factory-spark.md)
> * [Az Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenysége](data-factory-azure-ml-batch-execution-activity.md)
> * [Az Azure Machine Learning Studio (klasszikus) erőforrás-frissítési tevékenysége](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az adatátalakítás a Data Factory tárolt eljárással tevékenységgel](../transform-data-using-stored-procedure.md)című témakört.

## <a name="overview"></a>Áttekintés
Az Adatátalakítási tevékenységek egy Data Factory folyamaton keresztül alakíthatók át és feldolgozhatók az előrejelzések [és az](data-factory-create-pipelines.md) elemzések során. A tárolt eljárási tevékenység az Data Factory által támogatott átalakítási tevékenységek egyike. Ez a cikk az [Adatátalakítási tevékenységekről](data-factory-data-transformation-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatátalakításról és a Data Factory támogatott átalakítási tevékenységekről.

A tárolt eljárási tevékenységgel egy tárolt eljárást hívhat meg a vállalat vagy egy Azure-beli virtuális gép (VM) alábbi adattárainak egyikében:

- Azure SQL Database
- Azure Synapse Analytics
- SQL Server adatbázis. Ha SQL Server használ, telepítse adatkezelés átjárót ugyanarra a gépre, amely az adatbázist üzemelteti, vagy egy különálló gépen, amely hozzáfér az adatbázishoz. Adatkezelés átjáró egy olyan összetevő, amely biztonságos és felügyelt módon csatlakoztatja a helyszíni/Azure-beli virtuális gépen lévő adatforrásokat a Cloud Services szolgáltatással. További részletekért tekintse meg [adatkezelés Gateway](data-factory-data-management-gateway.md) -cikket.

> [!IMPORTANT]
> Az adatok Azure SQL Database vagy SQL Serverba való másolása során beállíthatja, hogy a **SqlSink** a másolási tevékenységben egy tárolt eljárást hívjanak a **sqlWriterStoredProcedureName** tulajdonság használatával. További információkért lásd: [tárolt eljárás hívása másolási tevékenységből](data-factory-invoke-stored-procedure-from-copy-activity.md). A tulajdonsággal kapcsolatos részletekért tekintse meg a következő összekötő-cikkeket: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). A tárolt eljárás meghívása az adatok Azure szinapszis-elemzésbe való másolása során a másolási tevékenység használata nem támogatott. Azonban a tárolt eljárási tevékenység használatával meghívhat egy tárolt eljárást az Azure szinapszis Analytics szolgáltatásban.
>
> Azure SQL Database vagy SQL Server vagy az Azure szinapszis Analytics adatainak másolása esetén a másolási tevékenység **SqlSource** konfigurálhatja a tárolt eljárás meghívását a forrásadatbázis adatainak a **sqlReaderStoredProcedureName** tulajdonság használatával történő olvasásához. További információkért tekintse meg a következő összekötő cikkeit: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure szinapszis Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

A következő útmutató egy folyamat tárolt eljárási tevékenységét használja egy Azure SQL Database tárolt eljárás meghívásához.

## <a name="walkthrough"></a>Útmutatás
### <a name="sample-table-and-stored-procedure"></a>Minta tábla és tárolt eljárás
1. Hozza létre a következő **táblázatot** a Azure SQL Databaseban SQL Server Management Studio vagy bármely olyan eszköz használatával, amellyel Ön is kényelmesen használható. A datetimestamp oszlop a megfelelő azonosító generálásának dátuma és időpontja.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    Az azonosító az egyedi azonosított, a datetimestamp oszlop pedig a megfelelő azonosító generálásának dátuma és időpontja.
    
    ![Mintaadatok](./media/data-factory-stored-proc-activity/sample-data.png)

    Ebben a példában a tárolt eljárás egy Azure SQL Database található. Ha a tárolt eljárás az Azure szinapszis Analyticsben és SQL Server adatbázisban található, a módszer hasonló. SQL Server-adatbázisok esetében telepítenie kell egy [adatkezelés-átjárót](data-factory-data-management-gateway.md).
2. Hozza létre a következő **tárolt eljárást** , amely beszúrja az adatkészletet a **sampletable**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > A paraméter **nevének** és **burkolatának** (ebben a példában a DateTime) meg kell egyeznie a folyamat/tevékenység JSON-ban megadott paraméterrel. A tárolt eljárás definíciójában ügyeljen arra, hogy **\@** a paraméter előtagjaként legyen használva.

### <a name="create-a-data-factory"></a>Adat-előállító létrehozása
1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/).
2. Kattintson az **új** elemre a bal oldali menüben, majd kattintson az **intelligencia és elemzés** lehetőségre, majd a **Data Factory** elemre.

    ![Új adatfeldolgozó 1](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Az **új adatgyár** panelen írja be a **SProcDF** nevet. A Azure Data Factory nevek **globálisan egyediek**. A gyár sikeres létrehozásához előtaggal kell elvégeznie az adatgyár nevét.

   ![Új adatfeldolgozó 2](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Válassza ki az **Azure-előfizetését**.
5. Az **Erőforráscsoportban** hajtsa végre a következő lépések egyikét:
   1. Kattintson az **új létrehozása** elemre, és adja meg az erőforráscsoport nevét.
   2. Kattintson a **meglévő használata** elemre, és válasszon ki egy meglévő erőforráscsoportot.
6. Válassza ki a Data Factory **helyét**.
7. Válassza a **rögzítés az irányítópulton** lehetőséget, hogy a következő bejelentkezéskor megtekintheti az irányítópulton látható adatgyárat.
8. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.
9. Ekkor megjelenik a Azure Portal **irányítópultján** létrehozott adatelőállító. A data factory sikeres létrehozása után megjelenik a data factory oldal, amely megjeleníti a data factory tartalmát.

   ![Data Factory Kezdőlap](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Azure SQL társított szolgáltatás létrehozása
Az adatelőállító létrehozása után létrehoz egy Azure SQL társított szolgáltatást, amely összekapcsolja az adatbázisát Azure SQL Databaseban, amely tartalmazza a sampletable táblát és a usp_sample tárolt eljárást a saját adatelőállítójának.

1. A **SProcDF** **Data Factory** paneljén kattintson a **Szerző és üzembe helyezés** elemre, hogy elindítsa a Data Factory szerkesztőt.
2. Kattintson a parancssáv **új adattár** elemére, és válassza a **Azure SQL Database** lehetőséget. Az Azure SQL társított szolgáltatás létrehozásához a szerkesztőben megjelenik a JSON-szkript.

   ![Új adattár 1](media/data-factory-stored-proc-activity/new-data-store.png)
3. A JSON-parancsfájlban hajtsa végre a következő módosításokat:

   1. Cserélje le a `<servername>` nevet a kiszolgáló nevére.
   2. Cserélje le `<databasename>` a helyére azt az adatbázist, amelyben létrehozta a táblát és a tárolt eljárást.
   3. Cserélje le az-t arra `<username@servername>` a felhasználói fiókra, amely hozzáféréssel rendelkezik az adatbázishoz.
   4. Cserélje le a `<password>` jelszót a felhasználói fiók jelszavára.

      ![Új adattár 2](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy (üzembe helyezés** ) elemére. Győződjön meg arról, hogy a bal oldali fanézetben megjelenik a AzureSqlLinkedService.

    ![faszerkezetes nézet a társított szolgáltatás 1](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
A tárolt eljárási tevékenység kimeneti adatkészletét akkor is meg kell adnia, ha a tárolt eljárás nem hoz létre adatokat. Ennek oka, hogy ez a kimeneti adatkészlet, amely a tevékenység ütemtervét vezeti (milyen gyakran fut a tevékenység óránként, naponta stb.). A kimeneti adatkészletnek olyan **társított szolgáltatást** kell használnia, amely egy Azure SQL Database vagy Azure szinapszis Analytics vagy egy SQL Server-adatbázisra hivatkozik, amelyben a tárolt eljárást futtatni kívánja. A kimeneti adatkészlet képes arra, hogy átadja a tárolt eljárás eredményét egy másik tevékenység által végzett későbbi feldolgozás céljából (a folyamaton belüli[láncolt tevékenységek](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) . A Data Factory azonban nem ír automatikusan egy tárolt eljárás kimenetét erre az adatkészletre. Ez a tárolt eljárás, amely egy SQL-táblába ír, amelyre a kimeneti adatkészlet mutat. Bizonyos esetekben a kimeneti adatkészlet lehet egy **próbabábu-adatkészlet** (egy olyan adathalmaz, amely egy olyan táblára mutat, amely nem igazán tartja meg a tárolt eljárás kimenetét). Ez a próbabábu-adatkészlet csak a tárolt eljárási tevékenység futtatási ütemtervének megadására szolgál.

1. Kattintson a... **További információ** az eszköztáron, kattintson az **új adatkészlet** elemre, majd az **Azure SQL** elemre. **Új adatkészlet** a parancssorban, majd válassza az **Azure SQL** lehetőséget.

    ![faszerkezetes nézet a társított szolgáltatás 2](media/data-factory-stored-proc-activity/new-dataset.png)
2. Másolja/illessze be a következő JSON-szkriptet a JSON-szerkesztőbe.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Az adatkészlet üzembe helyezéséhez kattintson a parancssáv **Deploy (üzembe helyezés** ) elemére. Győződjön meg róla, hogy az adatkészlet megjelenik a fanézetben.

    ![Fanézet a társított szolgáltatásokkal](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Folyamat létrehozása SqlServerStoredProcedure-tevékenységgel
Most hozzon létre egy folyamatot egy tárolt eljárási tevékenységgel.

Figyelje meg a következő tulajdonságokat:

- A **Type** tulajdonság értéke **SqlServerStoredProcedure**.
- A típus tulajdonságok **storedProcedureName** értéke **usp_sample** (a tárolt eljárás neve).
- A **storedProcedureParameters** szakasz egy **datetime** nevű paramétert tartalmaz. A JSON-paraméter nevének és burkolatának meg kell egyeznie a tárolt eljárás definíciójában szereplő paraméter nevével és házával. Ha a paraméternek null értéket kell adnia, használja a következő szintaxist: `"param1": null` (minden kisbetűs).

1. Kattintson a... **Továbbiak** a parancssorban, majd kattintson az **új folyamat** elemre.
2. Másolja/illessze be a következő JSON-kódrészletet:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. A folyamat üzembe helyezéséhez kattintson a **telepítés** elemre az eszköztáron.

### <a name="monitor-the-pipeline"></a>A folyamat figyelése
1. A Data Factory Editor paneljeinek a bezárásához és a Data Factory panelre való visszatéréshez kattintson az **X**, majd a **Diagram** elemre.

    ![diagram csempe 1](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. A **diagram nézetben** az oktatóanyagban használt folyamatok és adatkészletek áttekintése látható.

    ![diagram csempe 2](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. A diagram nézetben kattintson duplán az adatkészletre `sprocsampleout` . A szeletek kész állapotban jelennek meg. Öt szeletnek kell lennie, mivel a JSON-ből a kezdési idő és a befejezési idő között minden órában létrejön egy szelet.

    ![diagram csempe 3](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Ha egy szelet **üzemkész** állapotban van, futtasson egy `select * from sampletable` lekérdezést az adatbázison annak ellenőrzéséhez, hogy a tárolt eljárás beszúrta-e az adott táblát a táblába.

   ![Kimeneti adatok](./media/data-factory-stored-proc-activity/output.png)

   A Azure Data Factory-folyamatok figyelésével kapcsolatos részletes információkért lásd: [a folyamat figyelése](data-factory-monitor-manage-pipelines.md) .

## <a name="specify-an-input-dataset"></a>Adja meg a bemeneti adatkészletet
A forgatókönyvben a tárolt eljárás tevékenysége nem tartalmaz bemeneti adatkészleteket. Ha bemeneti adatkészletet ad meg, a tárolt eljárási tevékenység nem fut addig, amíg a bemeneti adatkészlet szelete nem érhető el (üzemkész állapotban). Az adatkészlet lehet külső adatkészlet (ez nem egy másik tevékenység, amely ugyanabban a folyamatban van) vagy egy felsőbb rétegbeli tevékenység által létrehozott belső adatkészlet (a tevékenység előtt futó tevékenység). Több bemeneti adatkészletet is megadhat a tárolt eljárási tevékenységhez. Ha így tesz, a tárolt eljárási tevékenység csak akkor fut le, ha a bemeneti adatkészlet összes szelete elérhető (kész állapotban). A bemeneti adatkészlet nem használható paraméterként a tárolt eljárásban. A rendszer csak a tárolt eljárási tevékenység megkezdése előtt használja a függőség ellenőrzését.

## <a name="chaining-with-other-activities"></a>Láncolás más tevékenységekkel
Ha egy felsőbb rétegbeli tevékenységet szeretne felvenni ezzel a tevékenységgel, adja meg a felsőbb rétegbeli tevékenység kimenetét a tevékenység bemenetként. Ha így tesz, a tárolt eljárási tevékenység addig nem fut le, amíg a felsőbb rétegbeli tevékenység befejeződik, és a felsőbb rétegbeli tevékenység kimeneti adatkészlete elérhető (kész állapotban). Több felsőbb rétegbeli tevékenység kimeneti adatkészleteit is megadhatja a tárolt eljárási tevékenység bemeneti adatkészletei. Ha így tesz, a tárolt eljárási tevékenység csak akkor fut le, ha a bemeneti adatkészlet összes szelete elérhető.

Az alábbi példában a másolási tevékenység kimenete a következő: OutputDataset, amely a tárolt eljárási tevékenység bemenete. Ezért a tárolt eljárási tevékenység addig nem fut le, amíg a másolási tevékenység be nem fejeződik, és az OutputDataset szelet elérhető (üzemkész állapotban). Ha több bemeneti adatkészletet ad meg, akkor a tárolt eljárási tevékenység addig nem fut le, amíg az összes bemeneti adatkészlet-szelet elérhetővé nem válik (üzemkész állapotban). A bemeneti adatkészletek nem használhatók közvetlenül paraméterként a tárolt eljárási tevékenységhez.

A tevékenységek láncolásával kapcsolatos további információkért tekintse meg a [folyamatok több tevékenységét](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline) ismertető témakört.

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Hasonlóképpen, az adattárolási eljárás tevékenységének **alárendelt tevékenységekkel** való összekapcsolásához (a tárolt eljárási tevékenység befejezése után futó tevékenységek) adja meg a tárolt eljárási tevékenység kimeneti adatkészletét a folyamat alsóbb rétegbeli tevékenységének bemenetében.

> [!IMPORTANT]
> Az adatok Azure SQL Database vagy SQL Serverba való másolása során beállíthatja, hogy a **SqlSink** a másolási tevékenységben egy tárolt eljárást hívjanak a **sqlWriterStoredProcedureName** tulajdonság használatával. További információkért lásd: [tárolt eljárás hívása másolási tevékenységből](data-factory-invoke-stored-procedure-from-copy-activity.md). A tulajdonsággal kapcsolatos részletekért tekintse meg a következő összekötő cikkeit: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Azure SQL Database vagy SQL Server vagy az Azure szinapszis Analytics adatainak másolása esetén a másolási tevékenység **SqlSource** konfigurálhatja a tárolt eljárás meghívását a forrásadatbázis adatainak a **sqlReaderStoredProcedureName** tulajdonság használatával történő olvasásához. További információkért tekintse meg a következő összekötő cikkeit: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure szinapszis Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>JSON formátum
Itt látható a tárolt eljárási tevékenység definiálásának JSON-formátuma:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

A következő táblázat ismerteti ezeket a JSON-tulajdonságokat:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| name | A tevékenység neve |Yes |
| leírás |A tevékenység által használt szöveg leírása |No |
| típus | A következőre kell beállítani: **SqlServerStoredProcedure** | Yes |
| bemenetek | Választható. Ha megad egy bemeneti adatkészletet, a tárolt eljárási tevékenység futtatásához elérhetőnek kell lennie ("Ready" (kész) állapotban). A bemeneti adatkészlet nem használható paraméterként a tárolt eljárásban. A rendszer csak a tárolt eljárási tevékenység megkezdése előtt használja a függőség ellenőrzését. |No |
| kimenetek | Meg kell adnia egy kimeneti adatkészletet egy tárolt eljárási tevékenységhez. A kimeneti adatkészlet meghatározza a tárolt eljárási tevékenység **ütemtervét** (óránként, hetente, havonta stb.). <br/><br/>A kimeneti adatkészletnek olyan **társított szolgáltatást** kell használnia, amely egy Azure SQL Database vagy Azure szinapszis Analytics vagy egy SQL Server-adatbázisra hivatkozik, amelyben a tárolt eljárást futtatni kívánja. <br/><br/>A kimeneti adatkészlet képes arra, hogy átadja a tárolt eljárás eredményét egy másik tevékenység által végzett későbbi feldolgozás céljából (a folyamaton belüli[láncolt tevékenységek](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) . A Data Factory azonban nem ír automatikusan egy tárolt eljárás kimenetét erre az adatkészletre. Ez a tárolt eljárás, amely egy SQL-táblába ír, amelyre a kimeneti adatkészlet mutat. <br/><br/>Bizonyos esetekben a kimeneti adatkészlet lehet egy **próbabábu-adatkészlet** is, amely kizárólag a tárolt eljárási tevékenység futtatási ütemtervének megadására szolgál. |Yes |
| storedProcedureName |Adja meg a tárolt eljárás nevét a Azure SQL Databaseban, az Azure szinapszis Analyticsben vagy SQL Server, amelyet a kimeneti tábla által használt társított szolgáltatás képvisel. |Yes |
| storedProcedureParameters |A tárolt eljárás paramétereinek értékeinek megadása. Ha egy paraméternél null értéket kell átadnia, használja a következő szintaxist: "param1": null (az összes kisbetű). Tekintse meg a következő mintát, amelyből megtudhatja, hogyan használhatja ezt a tulajdonságot. |No |

## <a name="passing-a-static-value"></a>Statikus érték átadása
Most vegyünk fel egy "forgatókönyv" nevű oszlopot egy olyan táblában, amely a "Document Sample" nevű statikus értéket tartalmazza.

![Mintaadatok 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tábla**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Tárolt eljárás:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Most adja át a **forgatókönyv** paramétert és a tárolt eljárási tevékenység értékét. Az előző minta **typeProperties** szakasza a következő kódrészlethez hasonlít:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory adatkészlet:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Data Factory folyamat**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
