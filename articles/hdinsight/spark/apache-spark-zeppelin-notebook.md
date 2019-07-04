---
title: Zeppelin notebookok használata Azure HDInsight az Apache Spark-fürt
description: Zeppelin notebookok használata Azure HDInsight az Apache Spark-fürtök lépésenkénti útmutatót.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 219cdeea228ae3e334213a0f0654f904592cb09e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448736"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Az Apache Zeppelin notebookok használata Azure HDInsight az Apache Spark-fürt

HDInsight Spark-fürtök tartalmazzák [Apache Zeppelin](https://zeppelin.apache.org/) , amelyek segítségével futtathat jegyzetfüzeteket [Apache Spark](https://spark.apache.org/) feladatok. Ebből a cikkből elsajátíthatja a Zeppelin notebook használata egy HDInsight-fürtön.

**Előfeltételek:**

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* A fürtök elsődleges tároló URI-séma. Ez akkor lehet `wasb://` az Azure Blob Storage `abfs://` az Azure Data Lake Storage Gen2 vagy `adl://` az Azure Data Lake Storage Gen1. Biztonságos átvitel engedélyezve van a Blob Storage vagy a Data Lake Storage Gen2, ha az URI lesz `wasbs://` vagy `abfss://`, illetve.  Lásd még a [az Azure Storage-ban biztonságos átvitel megkövetelése](../../storage/common/storage-require-secure-transfer.md) további információt.

## <a name="launch-an-apache-zeppelin-notebook"></a>Indítsa el az Apache Zeppelin-jegyzetfüzet

1. A Spark-fürtből származó **áttekintése**válassza **Zeppelin-Jegyzetfüzet** a **fürt irányítópultjai**. Adja meg a fürt rendszergazdai hitelesítő adatait.  

   > [!NOTE]  
   > A Zeppelin Notebook használhat saját fürtjében, a böngészőben a következő URL-cím megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Hozzon létre új notebookot. A fejléc panelen keresse meg **Notebook** > **létrehozása új feljegyzés**.

    ![Hozzon létre egy új Zeppelin-Jegyzetfüzet](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "új Zeppelin notebook létrehozása")

    Adja meg a notebook elnevezése, majd válassza ki **Megjegyzés létrehozása**.

3. Győződjön meg arról, a jegyzetfüzet fejléc egy csatlakoztatott állapotát jeleníti meg. A jobb felső sarokban lévő zöld pontot helyén.

    ![Zeppelin notebook állapot](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin notebook állapota")

4. Töltse be a mintaadatokat egy ideiglenes táblába. Spark-fürt létrehozásakor, a HDInsight, a mintaadatfájl `hvac.csv`, másolja a társított storage-fiók alatt `\HdiSamples\SensorSampleData\hvac`.

    Az üres bekezdés, amely az Új jegyzetfüzet alapértelmezés szerint létrejön illessze be az alábbi kódrészletet.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Nyomja meg **SHIFT + ENTER** , vagy kattintson a **lejátszása** a bekezdés a kódrészlet futtatása gombra. A bekezdés a jobb felső sarkának állapota Kész, függő, BEFEJEZETT, FUTÓ, érdemes halad. A kimenetben megjelenik-e stejném paragraph alján. A képernyőfelvételen a következőhöz hasonlóan néz ki:

    ![Hozzon létre egy ideiglenes táblát a nyers adatoktól](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "hozzon létre egy ideiglenes táblát nyers adatokból")

    Minden bekezdéséhez címet is megadhatja. A bekezdés jobb oldali sarokban, válassza ki a **beállítások** (lánckerekek) ikonra, és válassza ki **Zobrazit název**.  

    > [!NOTE]  
    > a(z) % spark2 interpret nem támogatott a Zeppelin-jegyzetfüzetek minden HDInsight-verziók és % sh interpret nem támogatja a HDInsight 4.0 és újabb verziók esetében.

5. Spark SQL-utasítások mostantól futtathatja a `hvac` tábla. Ha új bekezdést illessze be a következő lekérdezést. A lekérdezés lekéri a az épület-Azonosítót és a különbség a cél és minden egyes létrehozásához egy adott időpontban aktuális hőmérséklet. Nyomja meg **SHIFT + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    A **% sql** elején utasítás arra utasítja a notebook használatához a Livy Scala értelmezője számára készült.

6. Válassza ki a **sávdiagram** ikonra kattintva módosíthatja a képernyőt.  **beállítások**, kiválasztása után megjelenő **sávdiagram**, kiválaszthatja **kulcsok**, és **értékek**.  Az alábbi képernyőfelvételen a kimenetet.

    ![A notebook használatával Spark SQL-utasítás futtatásával](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "a notebook használatával Spark SQL-utasítás futtatása")

7. Változók használata a lekérdezés Spark SQL-utasítások is futtathatja. A következő kódrészletet bemutatja, hogyan adhat meg egy változót `Temp`, a lehetséges értékek a lekérdezni kívánt lekérdezés. A lekérdezés első futtatásakor automatikusan megjelenik egy legördülő változóhoz megadott értékekkel.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Illessze be a kódrészletet egy új bekezdés, és nyomja le az **SHIFT + ENTER**. Válassza ki **65** származó a **Temp** legördülő Izraeli normál idő szerint. 

8. Válassza ki a **sávdiagram** ikonra kattintva módosíthatja a képernyőt.  Válassza ki **beállítások** és a következő módosításokat:

   * **Csoportok:**  Adjon hozzá **targettemp**.  
   * **Értékek:** 1. Távolítsa el **dátum**.  2. Adjon hozzá **temp_diff**.  3.  Módosítsa a gyűjtő a **SUM** való **átlagos**.  

     Az alábbi képernyőfelvételen a kimenetet.

     ![A notebook használatával Spark SQL-utasítás futtatásával](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "a notebook használatával Spark SQL-utasítás futtatása")

9. Indítsa újra a Livy értelmezője számára készült kilép belőle. Ehhez nyissa meg a interpret beállítások kiválasztásával a bejelentkezett felhasználó neve, a jobb felső sarokban, és válassza ki **Interpret**.  

    ![Indítsa el a interpret](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-kimenet")

10. Görgessen a **livy**, majd válassza ki **indítsa újra a**.  Válassza ki **OK** a parancssorba.

    ![Indítsa újra a Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "indítsa újra a Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hogyan külső csomagok használata a notebook?
A Zeppelin notebook konfigurálhatja a külső, a Közösség által biztosított csomagok, amelyek nem tartalmaz-a-beépített a fürt használata a HDInsight az Apache Spark-fürt. Kereshet a [Maven tárházból](https://search.maven.org/) a teljes listát az elérhető csomagokat. Elérhető csomagok listáját a más forrásokból is beszerezheti. Például Közösség által biztosított csomagok teljes listája megtalálható [Spark csomagok](https://spark-packages.org/).

Ebben a cikkben bemutatjuk, hogyan használata a [spark-fürt megosztott kötetei szolgáltatás](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) a Jupyter notebookkal csomagot.

1. Interpret beállításainak megnyitásához. A jobb felső sarokban, válassza a bejelentkezett felhasználó nevében, majd válassza ki **Interpret**.

    ![Indítsa el a interpret](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-kimenet")

2. Görgessen a **livy**, majd **szerkesztése**.

    ![Interpret beállításainak módosítása](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "interpret beállításainak módosítása")

3. Adjon hozzá egy új kulcsot nevű `livy.spark.jars.packages`, majd az értékét állítsa a következő formátumban `group:id:version`. Ha használni szeretné a [spark-fürt megosztott kötetei szolgáltatás](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomag, be kell a kulcs értékét `com.databricks:spark-csv_2.10:1.4.0`.

    ![Interpret beállításainak módosítása](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "interpret beállításainak módosítása")

    Válassza ki **mentése** , majd indítsa újra a Livy értelmezője számára készült.

4. Ha szeretné megtudni, hogyan kell érkeznie a kulcsnak az értéke a fent megadott, a következő módját.
   
    a. Keresse meg a csomagot a Maven tárházból. Ebben a cikkben használt [spark-fürt megosztott kötetei szolgáltatás](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Gyűjtse össze a tárházban, a tartozó értékeket **GroupId**, **ArtifactId**, és **verzió**.
   
    ![Külső csomagok használata Jupyter notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "külső csomagok használata Jupyter notebook")
   
    c. Fűz össze adatokat a három, egymástól kettősponttal ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>A Zeppelin notebookok tároló?
A Zeppelin notebookok kerülnek a fürt átjárócsomópontjaihoz. Így ha törli a fürtöt, a notebookok is törlődik. Ha meg szeretné őrizni a notebookok más fürtökön későbbi használatra, exportálnia kell őket a feladatok futtatását követően. Szeretné exportálni a jegyzetfüzet, válassza ki a **exportálása** ikonra az alábbi képen látható módon.

![Töltse le a notebookot](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "töltse le a notebookot")

Ez a jegyzetfüzet egy JSON-fájlt a letöltési helyre menti.

## <a name="livy-session-management"></a>Livy munkamenet-kezelés
A Zeppelin-jegyzetfüzet futtatásakor a kód első bekezdésben új Livy-munkamenetet a HDInsight Spark-fürt jön létre. Ez a munkamenet közösen használja az összes Zeppelin-jegyzetfüzetek ezt követően hozzon létre. Ha valamilyen okból a Livy-munkamenet le állni a (fürt újraindítás, stb.), nem lesz képes feladatok futtatása a Zeppelin-jegyzetfüzet.

Ebben az esetben a következő lépéseket kell elvégeznie, feladatok futtatása a Zeppelin-jegyzetfüzet megkezdése előtt.  

1. Indítsa újra a Zeppelin-jegyzetfüzet a Livy értelmezője számára készült. Ehhez nyissa meg a interpret beállítások kiválasztásával a bejelentkezett felhasználó neve, a jobb felső sarokban, majd válassza ki **Interpret**.

    ![Indítsa el a interpret](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-kimenet")

2. Görgessen a **livy**, majd **indítsa újra a**.

    ![Indítsa újra a Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "indítsa újra a Zeppelin intepreter")

3. A kódcella futtatásához a meglévő Zeppelin-jegyzetfüzeteket. Ez létrehoz egy új Livy-munkamenetet a HDInsight-fürt.

## <a name="seealso"></a>Lásd még:
* [Áttekintés: Az Apache Spark on Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és BI: Spark on HDInsight használatával, BI-eszközökkel interaktív adatelemzés végrehajtása](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A Spark használata a HDInsight HVAC-adatok épület-hőmérséklet elemzésére](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: A HDInsight Spark használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő segítségével létrehozhat, és küldje el az Apache Spark Scala-alkalmazások](apache-spark-intellij-tool-plugin.md)
* [Az Apache Spark-alkalmazások távoli hibakeresése az IntelliJ IDEA HDInsight-eszközei beépülő használata](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
