---
title: Azure Data Lake Storage Gen1 elemzése a HDInsight Apache Spark
description: Apache Spark feladatok futtatása a Azure Data Lake Storage Gen1 tárolt adatok elemzéséhez
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: ad8239b3403d37a9e4c79de481fb9cb26306e243
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946324"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Az HDInsight Spark-fürt használata Data Lake Storage Gen1ban lévő adatelemzéshez

Ebben a cikkben a HDInsight Spark-fürtökkel elérhető [Jupyter Notebookeket](https://jupyter.org/) használhatja olyan feladatok futtatásához, amelyek beolvasják az adatokat egy Data Lake Storage-fiókból.

## <a name="prerequisites"></a>Előfeltételek

* Azure Data Lake Storage Gen1 fiók. Kövesse a [Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](../../data-lake-store/data-lake-store-get-started-portal.md)című témakör utasításait.

* Azure HDInsight Spark a fürtöt Data Lake Storage Gen1 tárolóként. Kövesse a rövid útmutató [: fürtök beállítása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md)című témakör utasításait.

## <a name="prepare-the-data"></a>Az adatok előkészítése

> [!NOTE]  
> Ezt a lépést nem kell végrehajtania, ha a HDInsight-fürtöt Data Lake Storage alapértelmezett tárolóként hozta létre. A fürt létrehozási folyamata felvesz néhány mintaadatok a fürt létrehozásakor megadott Data Lake Storage fiókba. Ugorjon a HDInsight Spark-fürt használata Data Lake Storage használatával című szakaszra.

Ha olyan HDInsight-fürtöt hozott létre, amely Data Lake Storage további tárterületként, és Azure Storage Blob alapértelmezett tárolóként, akkor először másolja át néhány mintaadatok a Data Lake Storage-fiókba. A mintaadatok a HDInsight-fürthöz társított Azure Storage Blob alapján használhatók. Ehhez a [ADLCopy eszközt](https://www.microsoft.com/download/details.aspx?id=50358) használhatja. Töltse le és telepítse az eszközt a hivatkozásból.

1. Nyisson meg egy parancssort, és navigáljon ahhoz a könyvtárhoz, ahol a AdlCopy telepítve van, jellemzően `%HOMEPATH%\Documents\adlcopy` .

2. Futtassa a következő parancsot egy adott blobnak a forrás tárolójából Data Lake Storageba való másolásához:

    ```scala
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Másolja a **HVAC.csv** Sample adatfájlt a **/HdiSamples/HdiSamples/SensorSampleData/hvac/** címen a Azure Data Lake Storage-fiókba. A kódrészletnek a következőképpen kell kinéznie:

    ```scala
    AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

   > [!WARNING]  
   > Győződjön meg arról, hogy a fájl-és elérésiút-nevek a megfelelő nagybetűket használják.

3. A rendszer felszólítja, hogy adja meg az Azure-előfizetéshez tartozó hitelesítő adatokat, amely alatt a Data Lake Storage fiókja van. Az alábbi kódrészlethez hasonló kimenet jelenik meg:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Copy Completed. 1 file copied.
    ```

    Az adatfájl (**HVAC.csv**) a Data Lake Storage-fiók **/HVAC** mappájában lesz átmásolva.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>HDInsight Spark-fürt használata Data Lake Storage Gen1

1. A [Azure Portal](https://portal.azure.com/)a kezdőpulton kattintson a Apache Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a **Tallózás az összes** HDInsight-fürt területen is megnyithatja  >  .

2. A Spark-fürt panelén kattintson a **Quick Links** (Gyorshivatkozások) lehetőségre, majd a **Cluster Dashboard** (Fürt irányítópultja) panelen a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   > [!NOTE]  
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** nevet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Hozzon létre új notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

    ![Új Jupyter Notebook létrehozása](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Új Jupyter Notebook létrehozása")

4. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Ennek az első lépése a jelen forgatókönyvhöz szükséges típusok importálása. Ehhez illessze be a következő kódrészletet a cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt.

    ```scala
    from pyspark.sql.types import *
    ```

    Minden alkalommal, amikor a Jupyterben feladatot futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezését követően ez a jel üres körre változik.

     ![Jupyter Notebook feladatok állapota](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Jupyter Notebook feladatok állapota")

5. Az Data Lake Storage Gen1-fiókba másolt **HVAC.csv** fájl használatával helyezzen be mintaadatok egy ideiglenes táblába. A Data Lake Storage fiókban lévő adatai a következő URL-minta használatával érhetők el.

   * Ha Data Lake Storage Gen1 alapértelmezett tárolóként, HVAC.csv a következő URL-címhez hasonló elérési úton lesz:

        ```scala
        adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv
        ```

       Vagy rövidített formátumot is használhat, például az alábbiakat:

        ```scala
        adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv
        ```

   * Ha Data Lake Storage további tárterületként, HVAC.csv a másolt helyen lesz, például:

        ```scala
        adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>
        ```

     Illessze be a következő kódot egy üres cellába, és cserélje le az **MYDATALAKESTORE** -t a Data Lake Storage fiók nevére, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. Ez a kódpélda az adatokat a **hvac** nevű ideiglenes táblába regisztrálja.

      ```scala
      # Load the data. The path below assumes Data Lake Storage is   default storage for the Spark cluster
      hvacText = sc.textFile("adl://MYDATALAKESTORazuredatalakestore.  net/cluster/mysparkclusteHdiSamples/HdiSamples/  SensorSampleData/hvac/HVAC.csv")

      # Create the schema
      hvacSchema = StructType([StructField("date", StringTy(), False)  ,StructField("time", StringType(), FalseStructField  ("targettemp", IntegerType(), FalseStructField("actualtemp",   IntegerType(), FalseStructField("buildingID", StringType(),   False)])

      # Parse the data in hvacText
      hvac = hvacText.map(lambda s: s.split(",")).filt(lambda s: s  [0] != "Date").map(lambda s:(str(s[0]), s(s[1]), int(s[2]), int  (s[3]), str(s[6]) ))

      # Create a data frame
      hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

      # Register the data fram as a table to run queries against
      hvacdf.registerTempTable("hvac")
      ```

6. Mivel PySpark kernelt használ, most közvetlenül futtathat SQL-lekérdezést az imént létrehozott **hvac** ideiglenes táblán, a `%%sql` funkció használatával. További információ a `%%sql` magicról, valamint a PySpark kernelben elérhető egyéb varázslatokról: [Jupyter notebookokon elérhető kernelek Apache Spark HDInsight-fürtökkel](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
7. A feladat sikeres végrehajtását követően alapértelmezés szerint az alábbi táblázatos kimenet jelenik meg.

      ![A lekérdezés eredményének táblázatos kimenete](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "A lekérdezés eredményének táblázatos kimenete")

     Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon például az alábbihoz hasonlóan fog kinézni.

     ![A lekérdezés eredményének területgrafikonja](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "A lekérdezés eredményének területgrafikonja")

8. Az alkalmazás futtatását követően állítsa le a notebookot az erőforrások felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.


## <a name="next-steps"></a>Következő lépések

* [Önálló Scala-alkalmazás létrehozása Apache Spark fürtön való futtatáshoz](apache-spark-create-standalone-application.md)
* [HDInsight-eszközök használata a Azure Toolkit for IntelliJban Apache Spark alkalmazások létrehozásához a HDInsight Spark Linux-fürthöz](apache-spark-intellij-tool-plugin.md)
* [HDInsight-eszközök használata a Azure Toolkit for Eclipseban Apache Spark alkalmazások létrehozásához a HDInsight Spark Linux-fürthöz](apache-spark-eclipse-tool-plugin.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)