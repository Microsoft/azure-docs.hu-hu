---
title: Működővé tenni Spark – beépített gépi tanulási modellek – csoportos adatelemzési folyamat
description: Az Azure Blob Storage (WASB) szolgáltatásban tárolt tanulási modellek betöltése és pontszáma Python használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bb38a76de41885b6f39a1c6dce7c44bcb52a4d60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86027443"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Működővé tenni Spark – beépített gépi tanulási modellek

Ez a témakör bemutatja, hogyan működővé tenni egy mentett gépi tanulási modellt (ML) a Python használatával a HDInsight Spark-fürtökön. Ismerteti, hogyan tölthetők be a Spark MLlib használatával létrehozott és az Azure Blob Storageban (WASB) tárolt gépi tanulási modellek, valamint a WASB-ben tárolt adatkészletekkel való kiértékelésük. Azt mutatja be, hogyan lehet előre feldolgozni a bemeneti adatokat, átalakíthatja a szolgáltatásokat az indexelési és kódolási függvények használatával a MLlib Toolkit-ben, és megtudhatja, hogyan hozhat létre olyan címkézett pont típusú adatobjektumot, amely a ML-modellekkel való pontozáshoz bemenetként használható. A pontozáshoz használt modellek közé tartozik a lineáris regresszió, a logisztikai regresszió, a véletlenszerű erdő modelljei és a színátmenetes növelő famodellek.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Spark-fürtök és Jupyter notebookok
A telepítési lépések és a működővé tenni tartozó kód a HDInsight Spark 1,6-fürt, valamint a Spark 2,0-fürt használata esetén ebben az útmutatóban van megadva. Az eljárások kódja a Jupyter-jegyzetfüzetekben is elérhető.

### <a name="notebook-for-spark-16"></a>Jegyzetfüzet a Spark 1,6-hoz
A [pySpark-Machine-learning-adat-tudomány-Spark-Model-fogyasztás. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter notebook bemutatja, hogyan működővé tenni egy mentett modellt a Python használatával HDInsight-fürtökön. 

### <a name="notebook-for-spark-20"></a>Jegyzetfüzet a Spark 2,0-hoz
Ha módosítani szeretné a Spark 1,6-hoz készült Jupyter-jegyzetfüzetet egy HDInsight Spark 2,0-fürttel való használatra, cserélje le a Python-programkódot [erre a fájlra](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Ez a kód a Spark 2,0-ben létrehozott modellek felhasználását mutatja be.


## <a name="prerequisites"></a>Előfeltételek

1. A bemutató elvégzéséhez szüksége lesz egy Azure-fiókra és egy Spark 1,6 (vagy Spark 2,0) HDInsight-fürtre. Tekintse át az [adatelemzés a Spark használatával az Azure HDInsight](spark-overview.md) című témakört, amely útmutatást nyújt a követelmények teljesítéséhez. Ez a témakör az itt használt NYC 2013 taxi-adatok leírását is tartalmazza, valamint útmutatást ad a kód a Spark-fürtön lévő Jupyter-jegyzetfüzetből való végrehajtásához. 
2. Hozza létre az itt látható gépi tanulási modelleket a Spark 1,6-fürthöz vagy a Spark 2,0 notebookokhoz készült Spark témakörrel végzett [adatelemzési és-modellezési](spark-data-exploration-modeling.md) műveletek során. 
3. A Spark 2,0 jegyzetfüzetek egy további adatkészletet használnak a besorolási feladathoz, a jól ismert légitársaságok időpontját a 2011 és 2012-ból. A jegyzetfüzetek leírását és azok hivatkozásait az azokat tartalmazó GitHub-adattár [readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) tartalmazza. Emellett a kód itt és a csatolt jegyzetfüzetekben általános, és minden Spark-fürtön működnie kell. Ha nem a HDInsight Sparkot használja, akkor a fürt beállítása és a felügyeleti lépések némileg eltérnek az itt láthatótól. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Beállítás: tárolóhelyek, kódtárak és az előre beállított Spark-környezet
A Spark képes olvasni és írni egy Azure Storage Blobt (WASB). Így az ott tárolt meglévő adatai feldolgozhatók a Spark használatával és a WASB-ben újra tárolt eredményekkel.

A modellek vagy fájlok a WASB-ben való mentéséhez az elérési utat megfelelően kell megadni. A Spark-fürthöz csatolt alapértelmezett tároló a következővel kezdődő elérési úttal lehet hivatkozni: *"wasb///"*. A következő mintakód meghatározza az olvasni kívánt adatokat, valamint annak a modell-tárolási könyvtárnak az elérési útját, amelyre a modell kimenete mentve lesz. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>A tárolási helyszínek elérési útjának beállítása a WASB-ben
A modellek a következő helyre lesznek mentve: "wasb:///user/remoteuser/NYCTaxi/Models". Ha az elérési út nincs megfelelően beállítva, a modellek nincsenek betöltve a pontozáshoz.

Az eredményül kapott eredményeket a rendszer a következő helyre mentette: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Ha a mappa elérési útja helytelen, a rendszer nem menti az eredményeket az adott mappába.   

> [!NOTE]
> A fájl elérési útjának helye átmásolható és beilleszthető a kód helyőrzőbe a **Machine-learning-rescience-Spark-adat-Exploration-Modeling. ipynb** notebook utolsó cellájának kimenetében.   
> 
> 

Itt látható a könyvtár elérési útjának beállítására szolgáló kód: 

```python
# LOCATION OF DATA TO BE SCORED (TEST DATA)
taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

# SET SCORDED RESULT DIRECTORY PATH
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

# FILE LOCATIONS FOR THE MODELS TO BE SCORED
logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

# RECORD START TIME
import datetime
datetime.datetime.now()
```

**KIMENETI**

DateTime. datetime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Kódtárak importálása
A Spark-környezet beállítása és a szükséges kódtárak importálása a következő kóddal

```python
#IMPORT LIBRARIES
import pyspark
from pyspark import SparkConf
from pyspark import SparkContext
from pyspark.sql import SQLContext
import matplotlib
import matplotlib.pyplot as plt
from pyspark.sql import Row
from pyspark.sql.functions import UserDefinedFunction
from pyspark.sql.types import *
import atexit
from numpy import array
import numpy as np
import datetime
```

### <a name="preset-spark-context-and-pyspark-magics"></a>Előre beállított Spark-környezet és PySpark Magics
A Jupyter-jegyzetfüzetekhez biztosított PySpark-kernelek előre beállított környezettel rendelkeznek. Ezért nem kell explicit módon beállítania a Spark vagy a kaptár környezetét, mielőtt elkezdi a munkát a fejleszteni kívánt alkalmazással. Ezek a kontextusok alapértelmezés szerint elérhetők:

* SC – Spark esetében 
* sqlContext – struktúra esetén

A PySpark kernel tartalmaz néhány előre definiált "varázslatot", amelyek a (z)%% használatával hívható speciális parancsok. A kód mintáinak két ilyen parancsa van használatban.

* **%% helyi** Azt adta meg, hogy a következő sorokban található kód helyileg legyen végrehajtva. A kódnak érvényes Python-kódnak kell lennie.
* **%% SQL-o \<variable name>** 
* Struktúra-lekérdezést hajt végre a sqlContext. Ha a-o paraméter át lett adva, a lekérdezés eredménye a (z)%% helyi Python-kontextusban, pandák dataframe.

A Jupyter-jegyzetfüzetek és az azok által megadott "Magics" kernelekkel kapcsolatos további információkért tekintse meg a [Jupyter notebookok számára elérhető kerneleket HDInsight Spark Linux-fürtökkel a HDInsight-on](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Adatbevitel és megtisztított adatkeret létrehozása
Ez a szakasz a pontszámok betöltéséhez szükséges feladatok sorozatának kódját tartalmazza. Olvassa el a taxi Trip és a fare fájl (. TSV fájlként tárolt) 0,1%-os mintáját, formázza az adatkeretet, majd hozzon létre egy tiszta adatkeretet.

A taxi Trip-és viteldíj-fájljai a következő eljárásban megadott eljárás alapján lettek összekapcsolva: [a csoportos adatelemzési folyamat: a HDInsight Hadoop-fürtök használata](hive-walkthrough.md) témakör.

```python
# INGEST DATA AND CREATE A CLEANED DATA FRAME

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_test_file = sc.textFile(taxi_test_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_test_file.first()
fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
fields[7].dataType = IntegerType() #Pickup hour
fields[8].dataType = IntegerType() # Pickup week
fields[9].dataType = IntegerType() # Weekday
fields[10].dataType = IntegerType() # Passenger count
fields[11].dataType = FloatType() # Trip time in secs
fields[12].dataType = FloatType() # Trip distance
fields[19].dataType = FloatType() # Fare amount
fields[20].dataType = FloatType() # Surcharge
fields[21].dataType = FloatType() # Mta_tax
fields[22].dataType = FloatType() # Tip amount
fields[23].dataType = FloatType() # Tolls amount
fields[24].dataType = FloatType() # Total amount
fields[25].dataType = IntegerType() # Tipped or not
fields[26].dataType = IntegerType() # Tip class
taxi_schema = StructType(fields)

# CREATE DATA FRAME
taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_cleaned.cache()
taxi_df_test_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_test_cleaned.registerTempTable("taxi_test")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 46,37 másodperc

## <a name="prepare-data-for-scoring-in-spark"></a>Az adatfeldolgozás előkészítése a Sparkban
Ez a szakasz bemutatja, hogyan indexelheti, kódolhatja és méretezheti a kategorikus funkciókat, hogy előkészítse őket a MLlib felügyelt tanulási algoritmusokban való használatra a besorolás és a regresszió érdekében.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Szolgáltatások átalakítása: kategorikus funkciók indexelése és kódolása a modellekbe való bevitelhez a pontozáshoz
Ebből a szakaszból megtudhatja, hogyan indexelheti a kategorikus adatokat a `StringIndexer` és `OneHotEncoder` a modellbe bevitt funkciók kódolásával.

A [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) a címkék sztring oszlopát kódolja a Label indexek oszlopaiba. Az indexeket a feliratok gyakorisága alapján rendezi a rendszer. 

A [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) leképezi a Label indexek oszlopát bináris vektorok egy oszlopára, amely legfeljebb egyetlen egyértékű lehet. Ez a kódolás lehetővé teszi, hogy az algoritmusok a kategorikus funkciókra alkalmazható folyamatos értékű funkciókat, például a logisztikai regressziót.

```python
#INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_test 
"""
taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_with_newFeatures.cache()
taxi_df_test_with_newFeatures.count()

# INDEX AND ONE-HOT ENCODING
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_test_with_newFeatures)
encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
model = stringIndexer.fit(encoded1)
indexed = model.transform(encoded1)
encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
model = stringIndexer.fit(encoded2)
indexed = model.transform(encoded2)
encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
encoded3 = encoder.transform(indexed)

# INDEX AND ENCODE TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 5,37 másodperc

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>RDD objektumok létrehozása a modellbe való bevitelhez
Ez a szakasz olyan kódot tartalmaz, amely bemutatja, hogyan indexelheti a kategorikus szöveges adatok RDD-objektumként való indexelését, és egy-egy gyors kódolást, hogy a MLlib logisztikai regressziós és faalapú modellek betanítására és tesztelésére is használható legyen. Az indexelt adatokat a rendszer [rugalmas elosztott adatkészlet-(RDD-)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) objektumokban tárolja. A RDD a Spark alapszintű absztrakciója. A RDD objektum a Sparktal párhuzamosan üzemeltethető elemek nem módosítható, particionált gyűjteményét jelöli.

Emellett olyan kódot is tartalmaz, amely bemutatja, hogyan méretezhetők az adatkészletek a `StandardScalar` MLlib által biztosított lineáris regressziós és sztochasztikus színátmenet-leereszkedés (SGD) használatával, amely egy népszerű algoritmus, amely számos gépi tanulási modellt tanít. A [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) a szolgáltatások egységbeli variancia szerinti skálázására szolgál. A szolgáltatás skálázása, más néven az adatok normalizálása, nem biztosítja, hogy a széles körben kifizetett értékekkel rendelkező funkciók ne legyenek nagy mértékben mérlegelve az objektív függvényben. 

```python
# CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

# FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

# SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

# CACHE RDDS IN MEMORY
indexedTESTbinary.cache();
oneHotTESTbinary.cache();
indexedTESTreg.cache();
oneHotTESTreg.cache();
oneHotTESTregScaled.cache();

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 11,72 másodperc

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Pontszám a logisztikai regressziós modellel, és a kimenet mentése a blobba
Az ebben a szakaszban található kód azt mutatja be, hogyan tölthető be egy, az Azure Blob Storage-ba mentett logisztikai regressziós modell, amely azt jelzi, hogy a tippet egy taxis útra fizetik-e ki, majd a standard besorolási metrikákkal, majd az eredményeket a blob Storage-ba menti és ábrázolja. A pontszámok eredményei a RDD-objektumokban vannak tárolva. 

```python
# SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

## LOAD SAVED MODEL
savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

## SAVE SCORED RESULTS (RDD) TO BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
dirfilename = scoredResultDir + logisticregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 19,22 másodperc

## <a name="score-a-linear-regression-model"></a>Lineáris regressziós modell pontszáma
A [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) -t egy lineáris regressziós modell betanítására használták a sztochasztikus GRADIENS (SGD) használatával, amellyel előre jelezhető a kifizetett tip mennyisége. 

Az ebben a szakaszban található kód azt mutatja be, hogyan tölthető be egy lineáris regressziós modell az Azure Blob Storage-ból, és hogyan méretezhető változókat használ, majd visszamenti az eredményeket a blobba.

```python
#SCORE LINEAR REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

#LOAD LIBRARIES
from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

# LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = scoredResultDir + linearregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 16,63 másodperc

## <a name="score-classification-and-regression-random-forest-models"></a>Pontszám besorolása és regressziós véletlenszerű erdő modelljei
Az ebben a szakaszban található kód azt mutatja be, hogyan tölthetők be az Azure Blob Storage-ban mentett mentett besorolási és regressziós véletlenszerű erdős modellek, a teljesítményük a szabványos osztályozó és regressziós mértékekkel történik, majd az eredmények visszamenthetők a blob Storage-ba.

A [véletlenszerű erdők](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) a döntési fák együttesei.  Számos döntési fát egyesítenek a túlilleszkedés kockázatainak csökkentése érdekében. A véletlenszerű erdők képesek kezelni a kategorikus funkciókat, kiterjeszteni a többosztályos besorolási beállításra, nem szükségesek a funkciók skálázása, és képesek rögzíteni a nem lineáris és a funkciók közötti interakciókat. A véletlenszerű erdők a besorolás és a regresszió egyik legsikeresebb gépi tanulási modellje.

a [Spark. mllib](https://spark.apache.org/mllib/) a véletlenszerű erdőket támogatja a bináris és a többosztályos besoroláshoz, valamint a folyamatos és kategorikus funkciók használatával történő regresszióhoz. 

```python
# SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES    
from pyspark.mllib.tree import RandomForest, RandomForestModel


# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 31,07 másodperc

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Pontszám besorolása és regressziós átmenet fokozása famodellek
Az ebben a szakaszban található kód azt mutatja be, hogyan tölthető be a besorolási és a regressziós színátmenet az Azure Blob Storage-ból származó famodellek betöltésére, a szabványos osztályozó és regressziós mértékek teljesítményére, majd a blob Storage-ba való visszaállítására. 

a **Spark. mllib** támogatja a GBTS a bináris besoroláshoz és a regresszióhoz a folyamatos és kategorikus funkciók használatával. 

A [Gradient növelő fák](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS-EK) a döntési fák együttesei. GBTS betanítása a iteratív a veszteségek csökkentése érdekében. A GBTS képes kezelni a kategorikus funkciókat, nem szükséges a funkciók skálázása, és képes rögzíteni a nem lineáris és a funkciók közötti interakciókat. Ez az algoritmus többosztályos besorolású beállításban is használható.

```python
# SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

#LOAD AND SCORE THE MODEL
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + btclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

# LOAD AND SCORE MODEL 
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + btregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 14,6 másodperc

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Objektumok törlése a memóriából és a kinyomtatott fájlok helyeinek nyomtatása

```python
# UNPERSIST OBJECTS CACHED IN MEMORY
taxi_df_test_cleaned.unpersist()
indexedTESTbinary.unpersist();
oneHotTESTbinary.unpersist();
indexedTESTreg.unpersist();
oneHotTESTreg.unpersist();
oneHotTESTregScaled.unpersist();


# PRINT OUT PATH TO SCORED OUTPUT FILES
print "logisticRegFileLoc: " + logisticregressionfilename;
print "linearRegFileLoc: " + linearregressionfilename;
print "randomForestClassificationFileLoc: " + rfclassificationfilename;
print "randomForestRegFileLoc: " + rfregressionfilename;
print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
print "BoostedTreeRegressionFileLoc: " + btregressionfilename;
```

**KIMENETI**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05 -0317 _22_ 58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Spark-modellek felhasználása webes felületen keresztül
A Spark lehetővé teszi a Batch-feladatok vagy interaktív lekérdezések távoli beküldését egy Livy nevű összetevővel rendelkező REST-felületen keresztül. A Livy alapértelmezés szerint engedélyezve van a HDInsight Spark-fürtön. A Livy kapcsolatos további információkért lásd: [Spark-feladatok távoli elküldése a Livy használatával](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

A Livy használatával távolról is elküldhet egy olyan feladatot, amely egy Azure-blobban tárolt fájlt szerzi be, majd az eredményeket egy másik blobba írja. Ehhez töltse fel a Python-szkriptet a következőből:  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) a Spark-fürt blobján. A szkript a fürt blobba másolásához használhatja a **Microsoft Azure Storage Explorer** vagy a **AzCopy** eszközt. Ebben az esetben feltöltöttük a szkriptet a **_wasb:///example/Python/ConsumeGBNYCReg.py_**.   

> [!NOTE]
> A szükséges hozzáférési kulcsok a-portálon találhatók a Spark-fürthöz társított Storage-fiókhoz. 
> 
> 

Az erre a helyre történő feltöltés után ez a szkript egy elosztott környezetben fut a Spark-fürtön belül. Betölti a modellt, és a modell alapján a bemeneti fájlokra vonatkozó előrejelzéseket futtat.  

Ezt a szkriptet távolról is meghívhatja, ha egy egyszerű HTTPS/REST-kérelmet készít a Livy-on.  Itt található egy curl parancs a Python-szkript távoli meghívására szolgáló HTTP-kérelem létrehozásához. Cserélje le a CLUSTERLOGIN, a CLUSTERPASSWORD és a CLUSTERNAME értéket a Spark-fürt megfelelő értékeire.

```console
# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches
```

A távoli rendszer bármilyen nyelvét használhatja a Spark-feladat a Livy-n keresztüli meghívásához azáltal, hogy egyszerű HTTPS-hívást végez az alapszintű hitelesítéssel.   

> [!NOTE]
> Érdemes használni a Python-kérelmek függvénytárát a HTTP-hívás során, de az alapértelmezés szerint nincs telepítve Azure Functionsban. Így a régebbi HTTP-kódtárak használata is megtörténik.   
> 
> 

Itt látható a HTTP-hívás Python-kódja:

```python
#MAKE AN HTTPS CALL ON LIVY. 

import os

# OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
import httplib, urllib, base64

# REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
host = '<spark cluster name>.azurehdinsight.net:443'
username='<username>'
password='<password>'

#AUTHORIZATION
conn = httplib.HTTPSConnection(host)
auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

# SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
# IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
response = conn.getresponse().read()
print(response)
conn.close()
```

Azt is megteheti, hogy ezt a Python-kódot [Azure functions](https://azure.microsoft.com/documentation/services/functions/) a Spark-feladatok beküldésének elindításához, amely különböző események (például időzítő, létrehozás vagy frissítés) alapján szerzi be a blobot. 

Ha a kód ingyenes felhasználói felületét szeretné használni, használja a [Azure Logic apps](https://azure.microsoft.com/documentation/services/app-service/logic/) a Spark batch pontozás meghívásához egy HTTP-művelet definiálásával a **Logic apps Designerben** , és állítsa be a paramétereit. 

* Azure Portal hozzon létre egy új logikai alkalmazást az **+ új**  ->  **web és mobil**  ->  **Logic app** lehetőség kiválasztásával. 
* A **Logic apps Designer** kiépítéséhez adja meg a logikai alkalmazás nevét és a app Service tervet.
* Válasszon ki egy HTTP-műveletet, és adja meg a következő ábrán látható paramétereket:

![Logic Apps Designer](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>A következő lépések
**Több ellenőrzés és hiperparaméter**: a [fejlett adatfeltárás és-modellezés a Spark](spark-advanced-data-exploration-modeling.md) segítségével a modellek több ellenőrzéssel és a Hyper-paraméterekkel történő használatával is betanítható.

