---
title: 'Oktatóanyag: Spark Machine learning-alkalmazás készítése – Azure HDInsight'
description: Oktatóanyag – részletes útmutató arról, hogyan hozhat létre Apache Spark Machine learning-alkalmazást a HDInsight Spark-fürtökben Jupyter Notebook használatával.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc, devx-track-python
ms.date: 04/07/2020
ms.openlocfilehash: 13e5609c965a28405804b8a993280b13755b2d3c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866067"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-azure-hdinsight"></a>Oktatóanyag: Apache Spark Machine learning-alkalmazás létrehozása az Azure HDInsight

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy [Apache Spark](./apache-spark-overview.md) Machine learning-alkalmazást az Azure HDInsight a [Jupyter notebook](https://jupyter.org/) használatával.

A [MLlib](https://spark.apache.org/docs/latest/ml-guide.html) a Spark adaptív gépi tanulási könyvtára, amely közös tanulási algoritmusokat és segédprogramokat tartalmaz. (Besorolás, regresszió, fürtözés, együttműködési szűrés és dimenzióját csökkentése. Emellett a mögöttes optimalizálási primitívek is.)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Apache Spark Machine learning-alkalmazás fejlesztése

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. Lásd: [Apache Spark-fürt létrehozása](./apache-spark-jupyter-spark-sql-use-portal.md).

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete. További információ: [adatok betöltése és lekérdezések futtatása Apache Spark a HDInsight-on](./apache-spark-load-data-run-query.md).

## <a name="understand-the-data-set"></a>Az adatkészlet értelmezése

Az alkalmazás alapértelmezés szerint az összes fürtön elérhető minta **HVAC.csv** -adatelemzést használja. A fájl a következő helyen található: `\HdiSamples\HdiSamples\SensorSampleData\hvac` . Az adatok néhány HVAC-rendszerrel felszerelt épület célhőmérsékletét és jelenlegi hőmérsékletét mutatják. A **System** (Rendszer) oszlop tartalmazza a rendszer-azonosítót, míg a **SystemAge** (Rendszer kora) oszlop azt mutatja, hogy az épületben hány éve működik a HVAC-rendszer. Megjósolhatja, hogy egy adott épületben melegebb vagy hidegebb állapotú-e a megcélzott hőmérséklet, a rendszerazonosító és a rendszer kora alapján.

:::image type="content" source="./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png " alt-text="A Spark Machine learning-példához használt adatok pillanatképe" border="true":::

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Egy Spark Machine Learning-alkalmazás fejlesztése a Spark MLlib segítségével

Ez az alkalmazás egy Spark [ml](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) -folyamatot használ a dokumentumok besorolásának elvégzéséhez. A ML-folyamatok a DataFrames-re épülő magas szintű API-k egységes készletét biztosítják. A DataFrames segítségével a felhasználók gyakorlati gépi tanulási folyamatokat hozhatnak létre és hangolnak be. A folyamat során a dokumentumokat szavakra osztja fel, a szavakat átalakítja egy numerikus vektorrá, majd végül a vektorok és címkék alapján létrehoz egy előrejelzési modellt. Az alkalmazás létrehozásához hajtsa végre az alábbi lépéseket.

1. Hozzon létre egy Jupyter Notebookt a PySpark kernel használatával. Az utasításokért tekintse meg a [Jupyter notebook fájl létrehozása](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook-file)című témakört.

1. Importálja a forgatókönyvhöz szükséges típusokat. Illessze be a következő kódrészletet egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```

1. Töltse be az adatokat (a hvac.csv fájlból), elemezze őket, és használja fel őket a modellje betanításához.

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    A kódrészletben adjon meg egy függvényt, amely összehasonlítja a jelenlegi hőmérsékletet a célhőmérséklettel. Ha a jelenlegi hőmérséklet a magasabb, akkor az épület meleg, ezt az **1.0** érték jelzi. Ellenkező esetben az épület hideg, amit a **0.0** érték jelez.

1. Konfigurálja a Spark Machine Learning-folyamatot, amely három lépést tartalmaz: tokenizer, hashingTF és lr.

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    További információ a folyamatról és annak működéséről: [Apache Spark Machine learning-folyamat](https://spark.apache.org/docs/latest/ml-pipeline.html).

1. Igazítsa a folyamatot a betanítási dokumentumhoz.

    ```PySpark
    model = pipeline.fit(training)
    ```

1. Ellenőrizze a betanítási dokumentumban az alkalmazás előrehaladási állapotát.

    ```PySpark
    training.show()
    ```

    Az eredmény az alábbihoz hasonlóan fog kinézni:

    ```output
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Hasonlítsa össze a kimenetet a nyers CSV-fájllal. Például a CSV-fájl első sora a következő adatokat tartalmazza:

    :::image type="content" source="./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png " alt-text="Kimeneti adatok pillanatképe a Spark Machine learninghez – példa" border="true":::

    Figyelje meg, hogy a tényleges hőmérséklet alacsonyabb, mint a célhőmérséklet, ami arra utal, hogy az épület hideg. Az első sorban lévő **címke** értéke **0,0**, ami azt jelenti, hogy az épület nem meleg.

1. Készítsen elő egy adathalmazt, amelyen lefuttatja a betanított modellt. Ehhez a rendszerazonosítót és a rendszerszintű (a betanítási kimenetben **SYSTEMINFO** ) rendszerállapotot kell továbbítania. A modell azt jelzi, hogy az adott rendszerazonosítóval és a rendszerszinttel való kiépítés melegebb (1,0) vagy hidegebb (0,0).

    ```PySpark
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF()
    ```

1. Végül végezze el az előrejelzést a tesztadatok alapján.

    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Az eredmény az alábbihoz hasonlóan fog kinézni:

    ```output  
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```

   Figyelje meg az előrejelzés első sorát. Egy 20-as és 25 éves rendszerszintű HVAC rendszer esetén az épület gyors (**előrejelzés = 1.0**). A DenseVector első értéke (0.49999) a 0.0 előrejelzésnek, a második értéke (0.5001) az 1.0 előrejelzésnek felel meg. A kimenetben – annak ellenére, hogy a második érték csak kicsivel nagyobb – a modell által mutatott előrejelzés: **prediction = 1.0**.

1. Állítsa le a notebookot az erőforrások felszabadításához. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ez a művelet leállítja és bezárja a notebookot.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Anaconda scikit-learn kódtár használata a Spark Machine Learninghez

A HDInsight-alapú Apache Spark-fürtök Anaconda-kódtárakat tartalmaznak. Ezek közé tartozik a **scikit-learn** gépi tanulási kódtár. A könyvtár számos olyan adatkészletet is tartalmaz, amelyek használatával közvetlenül egy Jupyter Notebook hozhat létre példákat. Példák a scikit-Learn függvénytár használatára: [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html) .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a létrehozott fürtöt a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső **keresőmezőbe** írja be a **HDInsight** kifejezést.

1. Válassza ki a **HDInsight-fürtök** elemet a **szolgáltatások** területen.

1. A megjelenő HDInsight-fürtök listájában kattintson a **...** elemre az oktatóanyaghoz létrehozott fürt mellett.

1. Válassza a **Törlés** elemet. Válassza az **Igen** lehetőséget.

:::image type="content" source="./media/apache-spark-ipython-notebook-machine-learning/hdinsight-azure-portal-delete-cluster.png " alt-text="Azure Portal töröl egy HDInsight-fürtöt" border="true":::

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtanulta, hogyan hozhat létre egy Apache Spark Machine learning-alkalmazást az Azure HDInsight a Jupyter Notebook használatával. A következő oktatóanyag azt mutatja be, hogyan használhatja az IntelliJ IDEA-t Spark-feladatokhoz.

> [!div class="nextstepaction"]
> [Scala Maven-alkalmazás létrehozása a IntelliJ használatával](./apache-spark-create-standalone-application.md)
