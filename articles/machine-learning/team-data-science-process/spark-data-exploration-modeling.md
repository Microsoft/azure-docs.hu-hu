---
title: Adatelemzés és-modellezés Spark-Team adatelemzési folyamattal
description: A HDInsight Spark Spark MLlib eszközkészletének adatfeltárási és-modellezési képességeit mutatja be.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: sample
ms.date: 06/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: 531e5cf274d40eae19075514d50aec5a6524aeec
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775340"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Adatáttekintés és modellezés a Spark segítségével

Ismerje meg, hogyan használhatja a HDInsight Sparkot a gépi tanulási modellek betanításához a Spark MLlib használatával a taxi viteldíj-előrejelzéséhez.

Ez a példa a [csoportos adatelemzési folyamat](./index.yml)különböző lépéseit mutatja be. A New York-i City Taxi Trip és a fare 2013 adatkészlet egy részhalmaza az betöltésre, feltárásra és előkészítésre szolgál. Ezt követően a Spark MLlib, a bináris besorolás és a regressziós modellek betanításával előre megjósolható, hogy az utazás után kell-e fizetni a tippnek, és meg kell becsülni a tipp összegét.

## <a name="prerequisites"></a>Előfeltételek

A bemutató elvégzéséhez szüksége lesz egy Azure-fiókra és egy Spark 1,6 (vagy Spark 2,0) HDInsight-fürtre. Tekintse át az [adatelemzés a Spark használatával az Azure HDInsight](spark-overview.md) című témakört, amely útmutatást nyújt a követelmények teljesítéséhez. Ez a témakör az itt használt NYC 2013 taxi-adatok leírását is tartalmazza, valamint útmutatást ad a kód a Spark-fürtön lévő Jupyter-jegyzetfüzetből való végrehajtásához. 

### <a name="spark-clusters-and-notebooks"></a>Spark-fürtök és jegyzetfüzetek

Ebben az útmutatóban a telepítési lépéseket és kódokat a HDInsight Spark 1,6 használatára vonatkozó útmutatóban ismertetjük. A HDInsight Spark 1,6-es és a Spark 2,0-fürtök esetében azonban Jupyter jegyzetfüzetek is elérhetők. A jegyzetfüzetek leírását és azok hivatkozásait az azokat tartalmazó GitHub-adattár [readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) tartalmazza. Emellett a kód itt és a csatolt jegyzetfüzetekben általános, és minden Spark-fürtön működnie kell. Ha nem a HDInsight Sparkot használja, akkor a fürt beállítása és a felügyeleti lépések némileg eltérnek az itt láthatótól. A Jupyter notebookokra mutató hivatkozásokat a Spark 1,6 (a Jupyter Notebook-kiszolgáló pySpark kernelében kell futtatni) és a Spark 2,0 (a Jupyter Notebook-kiszolgáló pySpark3 kernelében kell futtatni):

- [Spark 1,6 notebookok](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/): adja meg az adatok feltárásának, modellezésének és pontozásának több különböző algoritmussal történő végrehajtásával kapcsolatos információkat.
- [Spark 2,0 notebookok](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/): adja meg a regressziós és besorolási feladatok végrehajtásának módját. Az adathalmazok eltérőek lehetnek, de a lépések és fogalmak a különböző adatkészletekre is érvényesek.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!NOTE]
> Az alábbi leírások a Spark 1,6-es verzióval kapcsolatosak. Spark 2,0-verziók esetén használja a fentiekben leírt és csatolt jegyzetfüzeteket. 

## <a name="setup"></a>Beállítás

A Spark képes olvasni és írni Azure Storage Blob (más néven WASB). Így az ott tárolt meglévő adatai feldolgozhatók a Spark használatával és a WASB-ben újra tárolt eredményekkel.

A modellek vagy fájlok a WASB-ben való mentéséhez az elérési utat megfelelően kell megadni. A Spark-fürthöz csatolt alapértelmezett tároló a következővel kezdődő elérési úttal lehet hivatkozni: "wasb:///". A többi helyet a "wasb://" hivatkozik.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>A tárolási helyszínek elérési útjának beállítása a WASB-ben

A következő mintakód határozza meg az olvasni kívánt adatokat, valamint a modell tárolási könyvtárának elérési útját, amely a modell kimenetét menti:

```python
# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

# LOCATION OF TRAINING DATA
taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
```

### <a name="import-libraries"></a>Kódtárak importálása

A beállításhoz a szükséges kódtárak importálására is szükség van. Állítsa be a Spark-környezetet, és importálja a szükséges kódtárakat a következő kóddal:

```python
# IMPORT LIBRARIES
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

A Jupyter-jegyzetfüzetekhez biztosított PySpark-kernelek előre beállított környezettel rendelkeznek. Így nem kell explicit módon beállítania a Spark vagy a kaptár környezetét, mielőtt elkezdi a munkát a fejleszteni kívánt alkalmazással. Ezek a kontextusok alapértelmezés szerint elérhetők. Ezek a kontextusok a következők:

* SC – Spark esetében 
* sqlContext – struktúra esetén

A PySpark kernel tartalmaz néhány előre definiált "varázslatot", amelyek a (z)%% használatával hívható speciális parancsok. A kód mintáinak két ilyen parancsa van használatban.

* **%% helyi** Megadja, hogy a következő sorokban lévő kódot helyileg kell végrehajtani. A kódnak érvényes Python-kódnak kell lennie.
* **%% SQL-o \<variable name>** Struktúra-lekérdezést hajt végre a sqlContext. Ha a-o paraméter át lett adva, a lekérdezés eredménye a (z)%% helyi Python-kontextusban, pandák DataFrame.

További információ a Jupyter notebook-kernelekről és az előre definiált "varázslatokról": a [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark Linux-fürtökkel a HDInsight-on](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="load-the-data"></a>Az adatok betöltése

Az adatelemzési folyamat első lépése az adatok beolvasása a forrásokból, ahol az adatok az adatfeltárási és-modellezési környezetben találhatók. Ebben az útmutatóban a környezet a Spark. Ez a szakasz a feladatok sorozatának elvégzéséhez szükséges kódot tartalmazza:

* az adatminta begyűjtése modellezésre
* olvasás a bemeneti adatkészletben (. TSV fájlként tárolva)
* az adattárolás formázása és tisztítása
* objektumok (RDD vagy adatkeretek) létrehozása és gyorsítótárazása a memóriában
* regisztrálja ideiglenes táblázatként az SQL-környezetben.

Itt látható az adatfeldolgozási kód.

```python
# INGEST DATA

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_train_file = sc.textFile(taxi_train_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_train_file.first()
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

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


# CREATE DATA FRAME
taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 51,72 másodperc

## <a name="explore-the-data"></a>Az adatok feltárása

Miután az adatgyűjtés bekerült a Sparkba, az adatelemzési folyamat következő lépése az adatelemzési és vizualizációs eredmények mélyebb megismerése. Ebben a szakaszban az SQL-lekérdezések használatával vizsgáljuk meg a taxi-adatforrásokat, és kirajzoljuk a vizuális vizsgálathoz használt cél változókat és a jövőbeli funkciókat. Konkrétan az utasok számának gyakoriságát vesszük igénybe a taxiban, a tipp összegének gyakoriságát, és azt, hogy a tippek milyen mértékben változnak a fizetési mennyiség és a típus szerint.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Az utasok számának gyakoriságát ábrázoló hisztogram kirajzolása a taxis kirándulások mintájában

Ez a kód és az azt követő kódrészletek az SQL Magic használatával kérdezik le a mintát és a helyi mágiát az adatábrázoláshoz.

* **SQL Magic ( `%%sql` )** a HDInsight PySpark kernel egyszerűen beágyazott HiveQL-lekérdezéseket támogat a sqlContext. Az (-o VARIABLE_NAME) argumentum megőrzi az SQL-lekérdezés kimenetét a Jupyter-kiszolgálón található pandák DataFrame. Ezzel a beállítással a kimenet elérhetővé válik a helyi módban.
* A **`%%local` Magic** a kód helyi futtatására szolgál a Jupyter-kiszolgálón, amely a HDInsight-fürt átjárócsomóponthoz. Általában `%%local` a Magic `%%sql` és a-o paraméter együttes használata. Az-o paraméter megőrzi az SQL-lekérdezés helyi kimenetét, majd%% local Magic elindítja a következő kódrészletet, hogy helyileg fusson a helyileg megőrzött SQL-lekérdezések kimenetén.

A rendszer automatikusan megjeleníti a kimenetet a kód futtatása után.

A lekérdezés az utasok száma alapján kérdezi le az utakat. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts 
FROM taxi_train 
WHERE passenger_count > 0 and passenger_count < 7 
GROUP BY passenger_count 
```

Ez a kód létrehoz egy helyi adatkeretet a lekérdezés kimenetében, és kirajzolja az adatokat. A `%%local` Magic létrehoz egy helyi adatkeretet, `sqlResults` amely a matplotlib való ábrázoláshoz használható. 

> [!NOTE]
> Ez a PySpark Magic többször is használatban van ebben az útmutatóban. Ha az adatmennyiség nagy, érdemes mintát venni egy olyan adatkeret létrehozásához, amely elfér a helyi memóriában.

```python
#CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Itt látható az a kód, amely az utakat az utasok száma szerint ábrázolja

```python
# PLOT PASSENGER NUMBER VS. TRIP COUNTS
%%local
import matplotlib.pyplot as plt
%matplotlib inline

x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**KIMENETI**

![Utazási gyakoriság utasok száma szerint](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Több különböző típusú vizualizáció (tábla, torta, vonal, terület vagy sáv) közül választhat a notebook **Type (típus** ) gombjának használatával. Itt látható a sáv ábrázolása.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>A tip-összegek hisztogramjának, valamint a tip-mennyiségnek az utasok száma és a viteldíj alapján történő megadását ábrázolja.

SQL-lekérdezés használata az adatmintavételezéshez.

```sql
# PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped 
FROM taxi_train 
WHERE passenger_count > 0 
AND passenger_count < 7 
AND fare_amount > 0 
AND fare_amount < 200 
AND payment_type in ('CSH', 'CRD') 
AND tip_amount > 0 
AND tip_amount < 25
```

A kód cellája az SQL-lekérdezést használja három mintaterület létrehozásához.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 100, -2, 20])
plt.show()
```

**KIMENETI** 

![Tipp mennyiségének eloszlása](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tipp összege utasok száma szerint](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipp összege viteldíj alapján](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="prepare-the-data"></a>Az adatok előkészítése

Ez a szakasz leírja és megadja az adatfeldolgozáshoz használt eljárások kódját az ML-modellezésben. Ez a következő feladatok elvégzését mutatja be:

* Hozzon létre egy új szolgáltatást dobozolási órával a forgalmi idő gyűjtők számára
* A kategorikus funkciók indexelése és kódolása
* Címkézett pont objektumok létrehozása a bemenethez ML függvényekben
* Véletlenszerű almintavételezést hozhat létre az adatból, és kioszthatja azokat képzésbe és tesztelési készletekbe
* Szolgáltatásskálázás
* Objektumok gyorsítótárazása a memóriában

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Hozzon létre egy új szolgáltatást dobozolási órával a forgalmi idő gyűjtők számára

Ez a kód bemutatja, hogyan hozhat létre egy új szolgáltatást a dobozolási órákkal a forgalmi idő gyűjtők számára, majd hogyan gyorsítótárazhatja az eredményül kapott adatkeretet a memóriában. Ha a rugalmasan elosztott adatkészleteket (RDD) és adatkereteket ismételten használják, a gyorsítótárazás a jobb végrehajtási időpontokat eredményezi. Ennek megfelelően a bemutatóban több fázisban gyorsítótárazjuk a RDD és az adatkereteket. 

```python
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train 
"""
taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**KIMENETI**

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>A kategorikus funkciók indexelése és kódolása a modellezési funkciókba való bevitelhez

Ez a szakasz bemutatja, hogyan indexelheti vagy kódolhatja a kategorikus funkciókat a modellezési függvényekbe való bevitelhez. A MLlib modellezési és előrejelzési funkciói olyan funkciókat igényelnek, amelyekben a használat előtt a kategorikus bemeneti adatok indexelve vagy kódolva lesznek. A modelltől függően indexelni vagy kódolni kell őket különböző módokon:  

* A **faalapú modellezéshez** numerikus értékként kell kódolni a kategóriákat (például egy három kategóriába tartozó funkciót 0, 1, 2) kódolva. Ezt az algoritmust a MLlib [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) függvénye írja elő. Ez a függvény a címkék karakterlánc-oszlopát kódolja a címke típusú indexeket tartalmazó oszlopba. Bár a bemeneti és az adatkezelési numerikus értékekkel van indexelve, a rendszer a faalapú algoritmusokat is megadhatja, hogy a kategóriákat megfelelő módon kezeljék. 
* A **logisztikai és a lineáris regressziós modellek** egy-egy gyors kódolást igényelnek, ahol például három kategóriát tartalmazó funkció bővíthető három funkciós oszlopba, amelyek mindegyike 0 vagy 1 értéket tartalmaz a megfigyelés kategóriája alapján. A MLlib [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) függvényt biztosít egy gyors kódoláshoz. Ez a kódoló a Label indexek egy oszlopát a bináris vektorok egy oszlopára képezi le, amely legfeljebb egyetlen egyértékű lehet. Ez a kódolás lehetővé teszi az algoritmusok számára, hogy a kategorikus funkciókra alkalmazni lehessen a számszerű értékű funkciókat, például a logisztikai regressziót.

Itt látható a kategorikus funkciók indexelésére és kódolására szolgáló kód:

```python
# INDEX AND ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES    
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# INDEX AND ENCODE VENDOR_ID
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_train_with_newFeatures)
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

# INDEX AND TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 1,28 másodperc

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Címkézett pont objektumok létrehozása a bemenethez ML függvényekben

Ez a szakasz olyan kódot tartalmaz, amely bemutatja, hogyan indexelheti a kategorikus szöveges adattípusokat címkézett pont adattípusként, és hogyan kódolhatja, hogy a MLlib logisztikai regresszió és más besorolási modellek betanítására és tesztelésére is használható legyen. A címkézett pont objektumok olyan rugalmas elosztott adatkészletek (RDD-EK), amelyek a MLlib-ben a legtöbb ML-algoritmusnak megfelelő bemeneti adatokként vannak formázva. A [címkével ellátott pont](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) egy olyan helyi vektor, amely egy címkéhez vagy válaszhoz van társítva, vagy sűrű vagy ritka.  

Ez a szakasz olyan kódot tartalmaz, amely bemutatja, hogyan indexelheti a kategorikus szöveges adattípusokat [címkézett pont](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) adattípusként, és hogyan kódolhatja, hogy a MLlib logisztikai regresszió és más besorolási modellek betanítására és tesztelésére is használható legyen. A címkézett pont objektumok olyan rugalmas elosztott adatkészletek (RDD-EK), amelyek egy címkéből (cél/válasz változó) és egy szolgáltatás-vektorból állnak. Ez a formátum a MLlib-ben számos ML-algoritmus bemenetéhez szükséges.

Itt látható a bináris besorolású szöveges funkciók indexelésére és kódolására szolgáló kód.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tipped, features)
    return  labPt
```

Az alábbi kód a kategorikus szöveg funkcióinak kódolására és indexelésére szolgál a lineáris regressziós elemzéshez.

```python
# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])

    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt
```

### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Véletlenszerű almintavételezést hozhat létre az adatból, és kioszthatja azokat képzésbe és tesztelési készletekbe

Ez a kód véletlenszerű mintavételt hoz létre az adatmennyiségről (itt 25% használatos). Bár az adathalmaz mérete miatt nem szükséges ehhez a példához, bemutatjuk, hogyan lehet mintát venni, hogy tudja, hogyan használható a saját problémája, ha szükséges. Ha a minták nagy méretűek, a mintavételezés jelentős időt takaríthat meg a modellek betanítása közben. Ezután a mintát egy képzési részre (75%-ra) és egy tesztelési részre (25%-ra) bontottuk a besorolási és regressziós modellezéshez.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.sql.functions import rand

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
indexedTESTbinary = testData.map(parseRowIndexingBinary)
oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
oneHotTESTbinary = testData.map(parseRowOneHotBinary)

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg = trainData.map(parseRowIndexingRegression)
indexedTESTreg = testData.map(parseRowIndexingRegression)
oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
oneHotTESTreg = testData.map(parseRowOneHotRegression)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 0,24 másodperc

### <a name="feature-scaling"></a>Szolgáltatásskálázás

A szolgáltatás skálázása, más néven az adatok normalizálása, nem biztosítja, hogy a széles körben kifizetett értékekkel rendelkező funkciók ne legyenek nagy mértékben mérlegelve az objektív függvényben. A szolgáltatás skálázásának kódja a [StandardScaler](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.classification.LogisticRegressionWithLBFGS.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS
) használatával méretezi a szolgáltatásokat az egység variancia számára. A MLlib a lineáris regressziós és a sztochasztikus gradiens (SGD), egy népszerű algoritmus, amely számos más gépi tanulási modellt, például rendszeres regressziós vagy támogató vektoros gépeket (SVM) biztosít.

> [!NOTE]
> Találtunk a LinearRegressionWithSGD algoritmust, hogy az érzékeny legyen a szolgáltatás skálázására.

Az alábbi kód a reguláris lineáris SGD-algoritmussal használható változók méretezését nyújtja.

```python
# FEATURE SCALING

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils

# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
label = oneHotTRAINreg.map(lambda x: x.label)
features = oneHotTRAINreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

label = oneHotTESTreg.map(lambda x: x.label)
features = oneHotTESTreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 13,17 másodperc

### <a name="cache-objects-in-memory"></a>Objektumok gyorsítótárazása a memóriában

A ML-algoritmusok betanításához és teszteléséhez szükséges idő a besorolási, a regressziós és a méretezett funkciókhoz használt bemeneti adatkeret-objektumok gyorsítótárazásával csökkenthető.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.cache()
indexedTESTbinary.cache()
oneHotTRAINbinary.cache()
oneHotTESTbinary.cache()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.cache()
indexedTESTreg.cache()
oneHotTRAINreg.cache()
oneHotTESTreg.cache()

# SCALED FEATURES
oneHotTRAINregScaled.cache()
oneHotTESTregScaled.cache()

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI** 

A fenti cella végrehajtásához szükséges idő: 0,15 másodperc

## <a name="train-a-binary-classification-model"></a>Bináris besorolási modell betanítása

Ez a szakasz azt mutatja be, hogyan használható három modell a bináris besorolási feladathoz, amely azt jelzi, hogy egy tippet kell-e kifizetni a taxis utazáshoz. A bemutatott modellek a következők:

* Rendszeres logisztikai regresszió 
* Véletlenszerű erdő modell
* Színátmenet-növelő fák

Az egyes modellek felépítési kódok szakasza a következő lépésekre oszlik: 

1. Betanítási adattípusok **modellezése** egyetlen paraméterrel
2. **Modell kiértékelése** mérőszámokkal rendelkező tesztelési adatkészleten
3. **Modell mentése** a blobban a későbbi felhasználás érdekében

### <a name="classification-using-logistic-regression"></a>Besorolás a logisztikai regresszió használatával

Az ebben a szakaszban található kód azt mutatja be, hogyan lehet betanítani, kiértékelni és menteni egy logisztikai regressziós modellt a [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , amely azt jelzi, hogy egy tippet fizettek-e a New York-i taxi Trip és a fare adatkészletben.

**A logisztikai regressziós modell betanítása a CV és a hiperparaméter elsöprő használatával**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics


# CREATE MODEL WITH ONE SET OF PARAMETERS
logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                               tolerance=0.0001, validateData=True, numClasses=2)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitModel.weights))
print("Intercept: " + str(logitModel.intercept))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI** 

Együtthatók: [0.0082065285375,-0.0223675576104,-0.0183812028036,-3.48124578069 e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1,00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Elfogás:-0.0111216486893

A fenti cella végrehajtásához szükséges idő: 14,43 másodperc

**A bináris besorolási modell kiértékelése standard metrikákkal**

```python
#EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

# RECORD START TIME
timestart = datetime.datetime.now()

# PREDICT ON TEST DATA WITH MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

# INSTANTIATE METRICS OBJECT
metrics = BinaryClassificationMetrics(predictionAndLabels)

# AREA UNDER PRECISION-RECALL CURVE
print("Area under PR = %s" % metrics.areaUnderPR)

# AREA UNDER ROC CURVE
print("Area under ROC = %s" % metrics.areaUnderROC)
metrics = MulticlassMetrics(predictionAndLabels)

# OVERALL STATISTICS
precision = metrics.precision()
recall = metrics.recall()
f1Score = metrics.fMeasure()
print("Summary Stats")
print("Precision = %s" % precision)
print("Recall = %s" % recall)
print("F1 Score = %s" % f1Score)


## SAVE MODEL WITH DATE-STAMP
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;
logitModel.save(sc, dirfilename);

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**KIMENETI** 

PR = 0.985297691373 alatti terület

Terület: ROC = 0.983714670256

Összefoglaló statisztika

Precíziós = 0.984304060189

Visszahívás = 0.984304060189

F1 pontszám = 0.984304060189

A fenti cella végrehajtásához szükséges idő: 57,61 másodperc

**A ROC-görbe ábrázolása.**

A *predictionAndLabelsDF* táblaként, *tmp_resultsként* van regisztrálva az előző cellában. a *tmp_results* a lekérdezések és a kimeneti eredmények a sqlResults adatkeretbe való elvégzésére használhatók a rajzoláshoz. Itt látható a kód.

```python
# QUERY RESULTS                              
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Az előrejelzéseket és a ROC-görbét ábrázoló kódot itt találja.

```python
# MAKE PREDICTIONS AND PLOT ROC-CURVE

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

# MAKE PREDICTIONS
predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVE
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**KIMENETI**

![Logisztikai regressziós ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Véletlenszerű erdő besorolása

Az ebben a szakaszban található kód azt mutatja be, hogyan lehet betanítani, kiértékelni és menteni egy véletlenszerű erdő modellt, amely azt jelzi, hogy egy tippet fizettek-e a New York-i taxi Trip és a fare adatkészlet esetében.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# TRAIN RANDOMFOREST MODEL
rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
                                       numTrees=25, featureSubsetStrategy="auto",
                                       impurity='gini', maxDepth=5, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRINT TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp;
dirfilename = modelDir + rfclassificationfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

Terület: ROC = 0.985297691373

A fenti cella végrehajtásához szükséges idő: 31,09 másodperc

### <a name="gradient-boosting-trees-classification"></a>Színátmenet-növelő fák besorolása

Az ebben a szakaszban található kód azt mutatja be, hogyan lehet betanítani, kiértékelni és menteni egy átmenetes növelő fák modellt, amely azt jelzi, hogy egy tipp kifizetése megtörténik-e a New York-i taxi Trip és a fare adatkészletben.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN A BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
dirfilename = modelDir + btclassificationfilename;

gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

Terület: ROC = 0.985297691373

A fenti cella végrehajtásához szükséges idő: 19,76 másodperc

## <a name="train-a-regression-model"></a>Regressziós modell betanítása

Ebből a szakaszból megtudhatja, hogyan használja a három modellt a regressziós feladathoz, amellyel előre megjósolható, hogy a tipp milyen mértékben lett kifizetve a taxik számára más tip-funkciók A bemutatott modellek a következők:

* Szabályos lineáris regresszió
* Véletlenszerű erdő
* Színátmenet-növelő fák

Ezeket a modelleket a bevezetésben ismertetjük. Az egyes modellek felépítési kódok szakasza a következő lépésekre oszlik: 

1. Betanítási adattípusok **modellezése** egyetlen paraméterrel
2. **Modell kiértékelése** mérőszámokkal rendelkező tesztelési adatkészleten
3. **Modell mentése** a blobban a későbbi felhasználás érdekében

### <a name="linear-regression-with-sgd"></a>Lineáris regresszió SGD-val

Az ebben a szakaszban található kód azt mutatja be, hogyan használhatók a méretezhető funkciók olyan lineáris regressziók betanításához, amelyek a sztochasztikus gradienstől (SGD) való optimalizáláshoz, valamint a modell kiértékeléséhez, kiértékeléséhez és mentéséhez szükségesek az Azure Blob Storageban (WASB).

> [!TIP]
> Tapasztalataink szerint a LinearRegressionWithSGD modellek konvergenciáját illetően problémák merülhetnek fel, és a paramétereket egy érvényes modell beszerzéséhez gondosan kell módosítani/optimalizálni. A változók méretezése jelentősen segíti a konvergenciát.

```python
#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
from pyspark.mllib.evaluation import RegressionMetrics
from scipy import stats

# USE SCALED FEATURES TO TRAIN MODEL
linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(linearModel.weights))
print("Intercept: " + str(linearModel.intercept))

# SCORE ON SCALED TEST DATA-SET & EVALUATE
predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
testMetrics = RegressionMetrics(predictionAndLabels)

# PRINT TEST METRICS
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

Együtthatók: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Feltartóztatás: 0.853872718283

GYÖKÁTLAGOS = 1.24190115863

R-sqr = 0.608017146081

A fenti cella végrehajtásához szükséges idő: 58,42 másodperc

### <a name="random-forest-regression"></a>Véletlenszerű erdő regressziója

Az ebben a szakaszban található kód azt mutatja be, hogyan lehet betanítani, kiértékelni és menteni egy véletlenszerű erdő-regressziót, amely előre jelezi a New York-i taxik adatmennyiségét.

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

## PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp;
dirfilename = modelDir + rfregressionfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

GYÖKÁTLAGOS = 0.891209218139

R-sqr = 0.759661334921

A fenti cella végrehajtásához szükséges idő: 49,21 másodperc

### <a name="gradient-boosting-trees-regression"></a>Színátmenet-növelő fák regressziója

Az ebben a szakaszban található kód bemutatja, hogyan lehet betanítani, kiértékelni és menteni a New York-i taxi-adatmennyiséget, amely előre jelezheti a New York-i taxi-adatmennyiséget.

**Betanítás és Értékelés**

```python
#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
from pyspark.mllib.util import MLUtils

## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

## EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
test_predictions = sqlContext.createDataFrame(predictionAndLabels)
test_predictions.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

GYÖKÁTLAGOS = 0.908473148639

R-sqr = 0.753835096681

A fenti cella végrehajtásához szükséges idő: 34,52 másodperc

**Telek**

*tmp_results* az előző cellában struktúra-táblaként van regisztrálva. A tábla eredményei a *sqlResults* adatkeretbe kerülnek a nyomtatáshoz. Itt látható a kód

```python
# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

# SELECT RESULTS
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Itt látható az a kód, amely az Jupyter-kiszolgáló használatával ábrázolja az adatterületet.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
import numpy as np

# PLOT 
ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
plt.axis([-1, 20, -1, 20])
plt.show(ax)
```

**KIMENETI**

![Tényleges – vs-előrejelzett-tipp – összegek](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Objektumok törlése a memóriából

`unpersist()`A használatával törölheti a memóriában gyorsítótárazott objektumokat.

```python
# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.unpersist()
indexedTESTbinary.unpersist()
oneHotTRAINbinary.unpersist()
oneHotTESTbinary.unpersist()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.unpersist()
indexedTESTreg.unpersist()
oneHotTRAINreg.unpersist()
oneHotTESTreg.unpersist()

# SCALED FEATURES
oneHotTRAINregScaled.unpersist()
oneHotTESTregScaled.unpersist()
```

## <a name="save-the-models"></a>A modellek mentése

Ha a [pontszám és a Spark-alapú gépi tanulási modellek kiértékelése](spark-model-consumption.md) című témakörben leírt független adatkészletet szeretne felhasználni, másolja és illessze be az itt létrehozott mentett modelleket tartalmazó fájlneveket a fogyasztási Jupyter jegyzetfüzetbe. Itt látható az a kód, amely kinyomtatja az elérési utakat a szükséges modellekhez.

```python
# MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**KIMENETI**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0317 _03_ 23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 -0317 _05_ 21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0317 _04_ 11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 -0317 _06_ 08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 -0317 _04_ 36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0317 _06_ 51.737282"

## <a name="whats-next"></a>A következő lépések

Most, hogy létrehozta a regressziós és besorolási modelleket a Spark MlLib, készen áll arra, hogy megtudja, hogyan szerzi be és értékelje ki ezeket a modelleket. A speciális adatfeltárási és-modellezési jegyzetfüzet mélyebbre hatol, beleértve a több ellenőrzési, a Hyper-paraméteres és a modell kiértékelését. 

**Modell felhasználása:** A jelen témakörben létrehozott besorolási és regressziós modellek pontszámának és értékelésének megismeréséhez tekintse meg a [Spark által készített gépi tanulási modellek pontszámát és értékelését](spark-model-consumption.md)ismertető szakaszt.

**Több ellenőrzés és hiperparaméter**: a [fejlett adatfeltárás és-modellezés a Spark](spark-advanced-data-exploration-modeling.md) használatával a modellek több ellenőrzéssel és a Hyper-paraméterrel történő elvetésével is betanítható
