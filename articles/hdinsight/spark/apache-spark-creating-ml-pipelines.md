---
title: Apache Spark Machine learning-folyamat létrehozása – Azure HDInsight
description: Adatfolyamatok létrehozása az Azure HDInsight a Apache Spark Machine learning-kódtár használatával.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 07/22/2019
ms.openlocfilehash: e9897c7ba14e6190698cf10792a94acc759699d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98940186"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Apache Spark Machine Learning-folyamat létrehozása

Apache Spark méretezhető gépi tanulási könyvtára (MLlib) a modellezési képességeket elosztott környezetbe hozza. A Spark-csomag a [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) DataFrames-re épülő, magas szintű API-k készlete. Ezek az API-k segítenek a gyakorlati gépi tanulási folyamatok létrehozásában és finomhangolásában.  A *Spark Machine learning* erre a MLlib-alapú DataFrame API-ra hivatkozik, nem a régebbi RDD-alapú folyamat API-ra.

A Machine learning (ML) folyamat olyan teljes munkafolyamat, amely több gépi tanulási algoritmust egyesít. Több lépésre van szükség az adatok feldolgozásához és megismeréséhez, az algoritmusok sorrendjének megköveteléséhez. A folyamatok meghatározzák a gépi tanulási folyamat szakaszait és sorrendjét. A MLlib-ben a folyamat egyes szakaszait egy adott PipelineStages-sorozatot jelképezik, ahol a transzformátorok és az egyes feladatok elvégzésére szolgáló kalkulátorok.

A transzformátorok olyan algoritmusok, amelyek az egyik DataFrame egy másikra alakítják át a `transform()` metódus használatával. Egy szolgáltatás-átalakító például elolvashatja egy DataFrame egy oszlopát, leképezheti azt egy másik oszlopba, és egy új DataFrame is kipróbálhatja a hozzárendelt oszlophoz hozzáfűzve.

A kalkulátor a tanulási algoritmusok absztrakciója, és az adatkészletek összevonása és betanítása egy transzformátor létrehozásához. A kalkulátor egy nevű metódust valósít meg `fit()` , amely elfogad egy DataFrame, és létrehoz egy DataFrame, amely egy átalakító.

A transzformátorok és a kalkulátorok minden állapot nélküli példánya saját egyedi azonosítóval rendelkezik, amelyet a paraméterek megadásakor használ a rendszer. Mindkettő egységes API-t használ ezeknek a paramétereknek a megadásához.

## <a name="pipeline-example"></a>Példa a folyamatra

Egy ML-folyamat gyakorlati használatának bemutatása érdekében ebben a példában a `HVAC.csv` HDInsight-fürt alapértelmezett tárolójában előre betöltött minta adatfájlt használunk, vagy az Azure Storage vagy a Data Lake Storage. A fájl tartalmának megtekintéséhez navigáljon a `/HdiSamples/HdiSamples/SensorSampleData/hvac` címtárhoz. `HVAC.csv` több időpontot tartalmaz, és a cél és a tényleges hőmérséklet is megadható a különböző épületekben található HVAC (*fűtő, szellőzés és légkondicionáló*) rendszerek esetében. A cél a modell betanítása az adatra, és egy adott épület előrejelzési hőmérsékletének előállítása.

A következő kód:

1. Meghatározza a, `LabeledDocument` a (a `BuildingID` `SystemInfo` rendszer azonosítóját és korát) és a `label` (1,0, ha az épület túl gyors, 0,0 egyébként) tárolja.
2. Egy egyéni elemzési függvényt hoz létre `parseDocument` , amely egy sor (sor) adatokat vesz igénybe, és meghatározza, hogy az épület "forró"-e, ha a célként megadott hőmérsékletet a tényleges hőmérsékletgel hasonlítja össze.
3. A forrásadatok kibontásakor alkalmazza az elemzőt.
4. Betanítási adatkészletet hoz létre.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

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
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

A példában szereplő folyamat három szakaszból áll: `Tokenizer` és `HashingTF` (mindkettő transzformátor) és `Logistic Regression` (egy kalkulátor).  A DataFrame kinyert és elemzett adatait a `training` rendszer a folyamaton keresztül a `pipeline.fit(training)` hívásakor átfolyik.

1. Első lépésként a a `Tokenizer` `SystemInfo` bemeneti oszlopot (amely a rendszerazonosító és a kor értékeiből áll) egy `words` kimeneti oszlopba osztja fel. Ezt az új `words` oszlopot a rendszer hozzáadja a DataFrame. 
2. A második szakasza `HashingTF` átalakítja az új `words` oszlopot a szolgáltatás-vektorokra. Ezt az új  `features` oszlopot a rendszer hozzáadja a DataFrame. Ezek az első két szakasz a transzformátorok. 
3. A harmadik szakasz `LogisticRegression` egy kalkulátor, így a folyamat meghívja a metódust a létrehozásához `LogisticRegression.fit()` `LogisticRegressionModel` . 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

A `words` `features` és a Transformers által hozzáadott új és oszlopok megtekintéséhez, valamint a `Tokenizer` `HashingTF` kalkulátor mintájának megjelenítéséhez `LogisticRegression` futtasson egy `PipelineModel.transform()` metódust az eredeti DataFrame. Az éles kódban a következő lépés egy tesztelési DataFrame átadása a képzés érvényesítéséhez.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

Az `model` objektum mostantól az előrejelzések készítésére is használható. A Machine learning-alkalmazás teljes mintája, valamint a futtatására vonatkozó részletes útmutatás: [Apache Spark Machine learning-alkalmazások létrehozása az Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Lásd még

* [Adatelemzés a Scala és a Apache Spark használatával az Azure-ban](../../machine-learning/team-data-science-process/scala-walkthrough.md)
