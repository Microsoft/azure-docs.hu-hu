---
title: 'Oktatóanyag: az adatbetöltések & lekérdezések futtatása Apache Spark-Azure HDInsight'
description: Oktatóanyag – Ismerje meg, hogyan tölthetők be az adathalmazok, és hogyan futtathat interaktív lekérdezéseket a Spark-fürtökön az Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: d3c8a08a14b23492c4bf032dd2b722c59bdf80ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930092"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Oktatóanyag: Adatok betöltése és lekérdezések futtatása egy Apache Spark-fürtön az Azure HDInsightban

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre dataframe egy csv-fájlból, és hogyan futtathat interaktív Spark SQL-lekérdezéseket egy [Apache Spark](https://spark.apache.org/) -fürtön az Azure HDInsight. A Sparkban az adathalmazok olyan elosztott adatgyűjtemények, amelyek megnevezett oszlopokba vannak rendezve. Az adathalmazok elméleti szinten azonosak a relációs adatbázisokban található táblákkal vagy R/Python-adathalmazokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adathalmaz létrehozása egy CSV-fájlból
> * Lekérdezések futtatása az adathalmazon

## <a name="prerequisites"></a>Előfeltételek

Apache Spark-fürt megléte a HDInsightban. Lásd: [Apache Spark-fürt létrehozása](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter-notebook létrehozása

A Jupyter Notebook egy interaktív notebook-környezet, amely számos programozási nyelvet támogat. A notebook lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű vizualizációk elvégzését.

1. Szerkessze az URL-címet a `https://SPARKCLUSTER.azurehdinsight.net/jupyter` Spark-fürt nevére való lecseréléssel `SPARKCLUSTER` . Ezután írja be a szerkesztett URL-címet egy webböngészőbe. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

2. A Jupyter weblapon válassza az **új**  >  **PySpark** lehetőséget a jegyzetfüzet létrehozásához.

   ![Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához")

   A rendszer létrehoz egy új jegyzetfüzetet, és a névtelen név () nevet nyitja meg `Untitled.ipynb` .

    > [!NOTE]  
    > Ha a notebookot a PySpark-kernellel hozza létre, a `spark`-munkamenet az első kódcella futtatásakor automatikusan jön létre. A munkamenetet nem szükséges manuálisan létrehoznia.

## <a name="create-a-dataframe-from-a-csv-file"></a>Adathalmaz létrehozása egy CSV-fájlból

Az alkalmazások közvetlenül a távoli tárolóban található fájlokból vagy mappákból hozhatnak létre dataframes, például az Azure Storage vagy a Azure Data Lake Storage; egy struktúra táblából; vagy a Spark által támogatott egyéb adatforrásokból, például az Cosmos DB, az Azure SQL DB, a DW stb. A következő képernyőképen az oktatóanyaghoz használt HVAC.csv fájl pillanatfelvétele látható. Ez a csv-fájl minden HDInsight Spark-fürtön megtalálható. Az adatok néhány épület hőmérséklet-változását rögzítik.

![Az interaktív Spark SQL-lekérdezésre vonatkozó adatok pillanatképe](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Az interaktív Spark SQL-lekérdezésre vonatkozó adatok pillanatképe")

1. Illessze be a következő kódot a Jupyter Notebook egy üres cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához. A kód importálja az alábbi forgatókönyvhöz szükséges típusokat:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Amikor a Jupyterben interaktív lekérdezést futtatunk, a böngésző ablakának vagy lapjának címsorában a **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezése után ez a jel üres körre változik.

    ![Az interaktív Spark SQL-lekérdezés állapota](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Az interaktív Spark SQL-lekérdezés állapota")

1. Megjegyzés: a visszaadott munkamenet-azonosító. A fenti képen a munkamenet-azonosító 0. Ha kívánja, lekérheti a munkamenet részleteit, ha a `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` CLUSTERNAME a Spark-fürt neve, az azonosító pedig a munkamenet-azonosító száma.

1. Hozzon létre egy adathalmazt és egy ideiglenes táblát (**hvac**) a következő kód futtatásával.

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Lekérdezések futtatása az adathalmazon

A tábla létrehozása után az adatokon interaktív lekérdezéseket futtathat.

1. Futtassa a következő kódot a notebook egy üres cellájában:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Az alábbi táblázatos kimenet jelenik meg.

     ![Interaktív Spark-lekérdezés eredményének táblázatos kimenete](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Interaktív Spark-lekérdezés eredményének táblázatos kimenete")

2. Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon megjelenítéséhez válassza az **Area** (Terület) lehetőséget, majd állítsa be a további értékeket az ábra szerint.

    ![Az interaktív Spark-lekérdezés eredményének terület gráfja](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Az interaktív Spark-lekérdezés eredményének terület gráfja")

3. A notebook menüsávban navigáljon a **fájl**  >  **Mentés és ellenőrzőpont** elemre.

4. Ha most kezd bele a [következő oktatóanyagba](apache-spark-use-bi-tools.md), hagyja nyitva a notebookot. Ha nem, állítsa le a jegyzetfüzetet a fürterőforrások felszabadításához: a jegyzetfüzet menüsorában navigáljon a **fájl**  >   **bezárásához és leállításához**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight az adatait és Jupyter jegyzetfüzeteit az Azure Storage vagy a Azure Data Lake Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban. Ha azt tervezi, hogy rögtön elvégzi a következő oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Nyissa meg az Azure Portalon a fürtöt, és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésekor a rendszer a HDInsight Spark-fürtöt és az alapértelmezett tárfiókot is törli.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre dataframe egy csv-fájlból, és hogyan futtathat interaktív Spark SQL-lekérdezéseket egy Apache Spark-fürtön az Azure HDInsight. A következő cikkből megtudhatja, hogy a Apache Sparkban regisztrált információk Hogyan tölthetők be egy BI Analytics-eszközbe, például Power BIba.

> [!div class="nextstepaction"]
> [Adatok elemzése BI-eszközökkel](apache-spark-use-bi-tools.md)
