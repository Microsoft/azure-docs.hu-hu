---
title: Spark-programok meghívása Azure Data Factory
description: Ismerje meg, hogyan hívhat meg Spark-programokat egy Azure-beli adatgyárból a MapReduce tevékenység használatával.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 644a0d645abb186731ddb1f408cd36e5b9ba3c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780021"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Spark-programok meghívása Azure Data Factory folyamatokból

> [!div class="op_single_selector" title1="Átalakítási tevékenységek"]
> * [Struktúra tevékenysége](data-factory-hive-activity.md)
> * [Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce tevékenység](data-factory-map-reduce.md)
> * [Hadoop streaming-tevékenység](data-factory-hadoop-streaming-activity.md)
> * [Spark-tevékenység](data-factory-spark.md)
> * [Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Azure Machine Learning Studio (klasszikus) erőforrás-frissítési tevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [U-SQL-Data Lake Analytics tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory általánosan elérhető 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a következő témakört: az [adatátalakítás Apache Spark tevékenység használatával Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Bevezetés
A Spark-tevékenység a Data Factory által támogatott [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) egyike. Ez a tevékenység futtatja a megadott Spark-programot a Spark-fürtön az Azure HDInsight-ben.

> [!IMPORTANT]
> - A Spark-tevékenység nem támogatja a Azure Data Lake Store elsődleges tárolóként használó HDInsight Spark-fürtöket.
> - A Spark-tevékenység csak a meglévő (saját) HDInsight Spark-fürtöket támogatja. Nem támogatja az igény szerinti HDInsight társított szolgáltatást.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Útmutató: folyamat létrehozása Spark-tevékenységgel
Az alábbi általános lépésekkel hozhat létre egy Spark-tevékenységgel rendelkező adatfeldolgozó-folyamatot:

* Adat-előállító létrehozása
* Hozzon létre egy Azure Storage-beli társított szolgáltatást, amely összekapcsolja a HDInsight Spark-fürthöz társított tárhelyet az adatelőállítóval.
* Hozzon létre egy HDInsight társított szolgáltatást, amely összekapcsolja a Spark-fürtöt a HDInsight-ben az adatgyárban.
* Hozzon létre egy adatkészletet, amely a Storage társított szolgáltatásra hivatkozik. Jelenleg egy tevékenység kimeneti adatkészletét kell megadnia, még akkor is, ha nincs kimenet létrehozva.
* Hozzon létre egy olyan folyamatot a Spark-tevékenységgel, amely a létrehozott HDInsight társított szolgáltatásra hivatkozik. A tevékenység az előző lépésben létrehozott adatkészlet kimeneti adatkészletként van konfigurálva. A kimeneti adatkészlet az ütemtervet (óránként, naponta) vezérli. Ezért meg kell adnia a kimeneti adatkészletet annak ellenére, hogy a tevékenység valójában nem eredményez kimenetet.

### <a name="prerequisites"></a>Előfeltételek
1. Hozzon létre egy általános célú Storage-fiókot a Storage- [fiók létrehozása](../../storage/common/storage-account-create.md)című részben leírtak szerint.

1. Hozzon létre egy Spark-fürtöt a HDInsight-ben az oktatóanyagban a [Spark-fürt létrehozása a HDInsight-](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)ben című témakör utasításait követve. Társítsa az 1. lépésben létrehozott Storage-fiókot a fürthöz.

1. Töltse le és tekintse át a Python parancsfájl fájljának **test.py** [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py) .

1. Töltse fel a **test.py** a **pyFiles** mappába a blob Storage-beli **adfspark** -tárolóban. Ha nem léteznek, hozza létre a tárolót és a mappát.

### <a name="create-a-data-factory"></a>Adat-előállító létrehozása
Adat-előállító létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza az **új**  >  **adatok és Analitika**  >  **Data Factory** lehetőséget.

1. Az **új adat-előállító** panelen, a **név** mezőben adja meg a **SparkDF** nevet.

   > [!IMPORTANT]
   > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a "nem érhető el a SparkDF neve" hibaüzenet jelenik meg, módosítsa az adatelőállító nevét. Használja például a yournameSparkDFdate, és hozza létre újra az adatelőállítót. További információ az elnevezési szabályokról: [A Data Factory elnevezési szabályai](data-factory-naming-rules.md).

1. Az **Előfizetés** területen válassza ki azt az Azure-előfizetést, ahol létre kívánja hozni az adat-előállítót.

1. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy Azure-erőforráscsoportot.

1. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet.

1. Válassza a **Létrehozás** lehetőséget.

   > [!IMPORTANT]
   > Data Factory példányok létrehozásához a [Data Factory közreműködő](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.

1. Ekkor megjelenik a Azure Portal-irányítópulton az adatelőállító.

1. Az adat-előállító sikeres létrehozása után megjelenik az **Adat-előállító** oldal, amely megjeleníti az adat-előállító tartalmát. Ha nem látja az **adatelőállító** lapot, válassza ki az adatfeldolgozó csempéjét az irányítópulton.

    ![A Data Factory panel](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben két társított szolgáltatást hoz létre. Az egyik szolgáltatás összeköti a Spark-fürtöt a saját adataival, és a másik szolgáltatás a tárolót a saját adatgyárához kapcsolja.

#### <a name="create-a-storage-linked-service"></a>Storage-beli társított szolgáltatás létrehozása
Ebben a lépésben társítja a tárfiókot az adat-előállítójához. Az útmutató későbbi részében létrehozott adatkészlet erre a társított szolgáltatásra hivatkozik. A következő lépésben definiált HDInsight társított szolgáltatás erre a társított szolgáltatásra is vonatkozik.

1. A **adatfeldolgozó** panelen válassza a **Létrehozás és telepítés** lehetőséget. Megjelenik a Data Factory szerkesztő.

1. Kattintson az **Új adattár** elemre, és válassza az **Azure Storage** lehetőséget.

   ![Új adattár](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. A tárolóhoz társított szolgáltatás létrehozásához használt JSON-szkript megjelenik a szerkesztőben.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Cserélje le a **fiók nevét** és a **fiók kulcsát** a Storage-fiókja nevére és hozzáférési kulcsára. A Storage-hozzáférési kulcs beszerzéséről a Storage- [fiók hozzáférési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md)című témakörben olvashat bővebben.

1. A társított szolgáltatás üzembe helyezéséhez válassza a parancssáv **üzembe helyezés** elemét. A társított szolgáltatás sikeres üzembe helyezése után megjelenik a Draft-1 (Vázlat-1) ablak. Az ablak bal oldalán, fanézetben látható az **AzureStorageLinkedService** szolgáltatás.

#### <a name="create-an-hdinsight-linked-service"></a>HDInsight társított szolgáltatás létrehozása
Ebben a lépésben létrehoz egy HDInsight társított szolgáltatást, amely összekapcsolja a HDInsight Spark-fürtöt az adatelőállítóval. A HDInsight-fürt az ebben a példában szereplő folyamat Spark-tevékenységében megadott Spark-program futtatására szolgál.

1. A Data Factory szerkesztőben válassza a **további**  >  **új számítási**  >  **HDInsight-fürt** lehetőséget.

    ![HDInsight társított szolgáltatás létrehozása](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-szerkesztőben hajtsa végre a következő lépéseket:

    1. Itt adhatja meg a HDInsight Spark-fürt URI-JÁT. Példa: `https://<sparkclustername>.azurehdinsight.net/`.

    1. Adja meg annak a felhasználónak a nevét, aki hozzáfér a Spark-fürthöz.

    1. Adja meg a felhasználó jelszavát.

    1. Itt adhatja meg a HDInsight Spark-fürthöz társított Storage társított szolgáltatást. Ebben a példában ez a AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - A Spark-tevékenység nem támogatja a Azure Data Lake Store elsődleges tárolóként használó HDInsight Spark-fürtöket.
    > - A Spark-tevékenység csak a meglévő (saját) HDInsight Spark-fürtöket támogatja. Nem támogatja az igény szerinti HDInsight társított szolgáltatást.

    A HDInsight társított szolgáltatással kapcsolatos további információkért lásd: [HDInsight társított szolgáltatás](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. A társított szolgáltatás üzembe helyezéséhez válassza a parancssáv **üzembe helyezés** elemét.

### <a name="create-the-output-dataset"></a>A kimeneti adatkészlet létrehozása
A kimeneti adatkészlet az ütemtervet (óránként, naponta) vezérli. Ezért a folyamat során meg kell adnia a Spark-tevékenység kimeneti adatkészletét, annak ellenére, hogy a tevékenység nem hoz létre kimenetet. A tevékenység bemeneti adatkészletének megadása nem kötelező.

1. A Data Factory szerkesztőben **válassza az**  >  **új adatkészlet**  >  **Azure Blob Storage** elemet.

1. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-kódrészlet definiál egy **OutputDataset** nevű adatkészletet. Emellett azt is megadhatja, hogy az eredmények a **adfspark** nevű blob-tárolóban és a **pyFiles/output** nevű mappában legyenek tárolva. Ahogy azt korábban említettük, ez az adatkészlet egy próbabábu-adatkészlet. Ebben a példában a Spark program nem hoz létre kimenetet. A **rendelkezésre állási** szakasz meghatározza, hogy a kimeneti adatkészlet naponta jön létre.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Az adatkészlet üzembe helyezéséhez válassza a parancssáv **üzembe helyezés** elemét.


### <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben egy HDInsightSpark-tevékenységgel rendelkező folyamatot hoz létre. Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. Ezért ebben a példában nincs megadva bemeneti adatkészlet.

1. A Data Factory-szerkesztőben válassza **az**  >  **új folyamat** lehetőséget.

1. Cserélje le a szkriptet a draft-1 ablakban a következő parancsfájlra:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```

    Vegye figyelembe a következő szempontokat:

    1. A **Type** tulajdonság értéke **HDInsightSpark**.

    1. A **rootPath** tulajdonság értéke **adfspark \\ pyFiles** , ahol a adfspark a blob-tároló, a pyFiles pedig a tárolóban lévő fájl mappája. Ebben a példában a blob Storage a Spark-fürthöz társított egyik. A fájlt egy másik Storage-fiókba töltheti fel. Ha így tesz, hozzon létre egy Storage-beli társított szolgáltatást, amely a Storage-fiókot az adatelőállítóhoz kapcsolja. Ezután adja meg a társított szolgáltatás nevét a **sparkJobLinkedService** tulajdonság értékeként. További információt erről a tulajdonságról és a Spark-tevékenység által támogatott egyéb tulajdonságokról a [Spark-tevékenység tulajdonságai](#spark-activity-properties)című témakörben talál.

    1. A **entryFilePath** tulajdonság értéke **test.py**, amely a Python-fájl.

    1. A **getDebugInfo** tulajdonság **mindig** értékre van állítva, ami azt jelenti, hogy a naplófájlok mindig jönnek létre (sikeres vagy sikertelen).

       > [!IMPORTANT]
       > Azt javasoljuk, hogy ne állítson be ezt a tulajdonságot `Always` éles környezetben, kivéve, ha problémát szeretne elhárítani.

    1. A **kimenetek** szakasz egy kimeneti adatkészlettel rendelkezik. A kimeneti adatkészletet akkor is meg kell adnia, ha a Spark-program nem hoz létre kimenetet. A kimeneti adatkészlet a folyamathoz tartozó ütemtervet (óránként, naponta) vezeti.

    A Spark-tevékenység által támogatott tulajdonságokkal kapcsolatos további információkért tekintse meg a [Spark-tevékenység tulajdonságai](#spark-activity-properties)című szakaszt.

1. A folyamat üzembe helyezéséhez válassza a parancssáv **üzembe helyezés** elemét.

### <a name="monitor-a-pipeline"></a>Folyamat monitorozása
1. A **adatfeldolgozó** panelen válassza a **figyelés & kezelés** lehetőséget, hogy elindítsa a figyelési alkalmazást egy másik lapon.

    ![Monitor & Manage csempe](media/data-factory-spark/monitor-and-manage-tile.png)

1. Módosítsa a **kezdő időpontot** a **2/1/2017** értékre, majd kattintson az **alkalmaz** gombra.

1. Csak egy tevékenység ablak jelenik meg, mert a folyamat kezdete (2017-02-01) és a befejezési idő (2017-02-02) között csak egy nap van. Győződjön meg arról, hogy az adatszelet **üzemkész** állapotban van.

    ![A folyamat figyelése](media/data-factory-spark/monitor-and-manage-app.png)

1. A **tevékenység-Windows** listában válassza ki a tevékenység futtatása lehetőséget a részletek megtekintéséhez. Ha hiba merül fel, a jobb oldali ablaktáblában láthatja a részleteket.

### <a name="verify-the-results"></a>Az eredmények ellenőrzése

1. Indítsa el a HDInsight Spark-fürt Jupyter Notebookét a következő címen: `https://CLUSTERNAME.azurehdinsight.net/jupyter` . Emellett megnyithatja a HDInsight Spark-fürthöz tartozó irányítópultot, majd elindíthatja a Jupyter Notebook.

1.   >  Új jegyzetfüzet elindításához válassza az új **PySpark** lehetőséget.

    ![Új jegyzetfüzet Jupyter](media/data-factory-spark/jupyter-new-book.png)

1. A következő parancs futtatásával másolja és illessze be a szöveget, majd nyomja le a SHIFT + ENTER billentyűkombinációt a második utasítás végén:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Ellenőrizze, hogy megjelenik-e az adatok a HVAC-táblából.

    ![Jupyter lekérdezés eredményei](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Részletes útmutatást a [Spark SQL-lekérdezés futtatása](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)című szakaszban talál.

### <a name="troubleshooting"></a>Hibaelhárítás
Mivel úgy állítja be a getDebugInfo, hogy **mindig** megjelenjenek, a blob-tároló pyFiles mappájában megjelenik egy napló almappája. A log mappában található naplófájl további információkat tartalmaz. Ez a naplófájl különösen akkor hasznos, ha hiba történt. Éles környezetben előfordulhat, hogy a **hibát** szeretné beállítani.

További hibaelhárításhoz hajtsa végre a következő lépéseket:


1. Nyissa meg a következőt: `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![A fonal felhasználói felületének alkalmazása](media/data-factory-spark/yarnui-application.png)

1. A futtatási kísérletek egyikéhez válassza a **naplók** lehetőséget.

    ![Alkalmazás lap](media/data-factory-spark/yarn-applications.png)

1. A log (napló) lapon a következő további hibaüzenetek jelennek meg:

    ![Naplózási hiba](media/data-factory-spark/yarnui-application-error.png)

A következő szakaszokban a Spark-fürt és a Spark-tevékenység az adat-előállítóban való használatára vonatkozó információk találhatók a adat-előállító entitásokról.

## <a name="spark-activity-properties"></a>Spark-tevékenység tulajdonságai
Itt látható egy Spark-tevékenységgel rendelkező folyamat JSON-definíciója:

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    },
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

A következő táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti.

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| name | A folyamatban szereplő tevékenység neve. | Yes |
| leírás | A tevékenység működését leíró szöveg | No |
| típus | Ezt a tulajdonságot HDInsightSpark értékre kell beállítani. | Yes |
| linkedServiceName | Azon HDInsight társított szolgáltatás neve, amelyen a Spark-program fut. | Yes |
| rootPath | A Spark-fájlt tartalmazó blob-tároló és-mappa. A fájl neve megkülönbözteti a kis-és nagybetűket. | Yes |
| entryFilePath | A Spark-kód/csomag gyökérkönyvtárának relatív elérési útja. | Yes |
| className | Az alkalmazás Java/Spark fő osztálya. | No |
| argumentumok | A Spark programhoz tartozó parancssori argumentumok listája. | No |
| proxyUser | A Spark-program végrehajtásához megszemélyesíteni kívánt felhasználói fiók. | No |
| sparkConfig | A Spark- [konfiguráció: alkalmazás tulajdonságai](https://spark.apache.org/docs/latest/configuration.html#available-properties)részen megjelenő Spark-konfigurációs tulajdonságok értékeinek megadása. | No |
| getDebugInfo | Megadja, hogy a rendszer mikor másolja a Spark-naplófájlokat a sparkJobLinkedService által megadott HDInsight-fürt (vagy) által használt tárolóba. Az engedélyezett értékek a következők: none, mindig vagy hiba. Az alapértelmezett érték none. | No |
| sparkJobLinkedService | A Spark-munkafájlt, a függőségeket és a naplókat tároló társított szolgáltatás. Ha nem ad meg értéket ehhez a tulajdonsághoz, a rendszer a HDInsight-fürthöz társított tárolót használja. | No |

## <a name="folder-structure"></a>Mappa szerkezete
A Spark-tevékenység nem támogatja az olyan beágyazott parancsfájlokat, mint a Pig és a kaptár tevékenységek. A Spark-feladatok is bővíthetők, mint a Pig/Kas-feladatok. A Spark-feladatok esetében több függőséget is megadhat, például a jar-csomagokat (a Java OSZTÁLYÚTVONAL helyezik el), a Python-fájlokat (a PYTHONPATH) és minden más fájlt.

Hozza létre a következő mappastruktúrát a HDInsight társított szolgáltatás által hivatkozott blob Storage-tárolóban. Ezután töltse fel a függő fájlokat a **entryFilePath** által jelölt gyökérmappa megfelelő almappájába. Töltse fel például a Python-fájlokat a pyFiles almappában és a jar-fájlokba a gyökérmappa tégelyek almappájába. Futásidőben a Data Factory szolgáltatás a következő mappastruktúrát várja a blob Storage-ban:

| Elérési út | Leírás | Kötelező | Típus |
| ---- | ----------- | -------- | ---- |
| . | A Spark-feladatokhoz tartozó gyökér elérési útja a Storage társított szolgáltatásban. | Igen | Mappa |
| &lt;felhasználó által definiált &gt; | Az elérési út, amely a Spark-feladathoz tartozó belépési fájlra mutat. | Yes | Fájl |
| ./jars | A rendszer a mappában található összes fájlt feltölti és elhelyezi a fürt Java-osztályútvonal. | Nem | Mappa |
| ./pyFiles | A rendszer az ebben a mappában található összes fájlt feltölti és elhelyezi a fürt PYTHONPATH. | Nem | Mappa |
| ./files | A rendszer a mappában található összes fájlt feltöltötte és a végrehajtó munkakönyvtárba helyezi. | Nem | Mappa |
| ./archives | A mappa összes fájlja nem tömörített. | Nem | Mappa |
| ./logs | A Spark-fürtből származó naplók tárolására szolgáló mappa.| Nem | Mappa |

Íme egy példa arra a tárolóra, amely a HDInsight társított szolgáltatás által hivatkozott blob Storage-ban két Spark-feladatot tartalmaz:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
