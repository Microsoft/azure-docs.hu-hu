---
title: Fejlett adatfeltárás és-modellezés a Spark-Team adatelemzési folyamattal
description: Az HDInsight Spark használatával adatfeltárást végezhet, és a bináris besorolást és a regressziós modelleket az átellenőrzés és a hiperparaméter optimalizálás használatával végezheti el.
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
ms.openlocfilehash: b84b7387411e7df8e092a2e810591697319f9554
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774677"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Speciális adatáttekintés és modellezés a Spark segítségével

Ez az útmutató a HDInsight Spark használatával teszi elérhetővé az adatfeltárást és a bináris besorolási és regressziós modelleket, a New York-i taxi Trip és a fare 2013 adatkészlet mintájára. Végigvezeti az [adatelemzési folyamat](./index.yml)lépésein, teljes egészében a HDInsight Spark-fürt feldolgozásához és az Azure-Blobok tárolásához az adatkezeléshez és a modellekhez. A folyamat felderíti és megjeleníti a Azure Storage Blob beérkező adatait, majd előkészíti a prediktív modellek létrehozásához szükséges adatok előkészítését. A Python a megoldás kódolásához és a kapcsolódó mintaterületek megjelenítéséhez használatos. Ezeket a modelleket a Spark MLlib Toolkit használatával hozhatja létre bináris besorolási és regressziós modellezési feladatok végrehajtásához. 

* A **bináris besorolási** feladattal megjósolhatja, hogy a rendszer kifizet-e egy tippet az utazásért. 
* A **regresszió** feladata, hogy előre megjósolja a tipp mennyiségét más tip-funkciók alapján. 

A modellezési lépések olyan kódot is tartalmaznak, amely bemutatja az egyes modellek betanítását, kiértékelését és mentését. A témakör a [Spark](spark-data-exploration-modeling.md) témakörrel megegyező területtel foglalkozik. Azonban ez a "speciális", hogy a hiperparaméter-leállítást is használja az optimálisan pontos besorolási és regressziós modellek betanításához. 

A **határokon átnyúló hitelesítés (CV)** olyan módszer, amely azt vizsgálja, hogy a modell milyen jól van kiképezve az adathalmazok ismert készletén, hogy megjósolja az adatkészletek azon funkcióit, amelyeken nem lett betanítva.  Az itt használt általános megvalósítás egy adatkészletnek a K-re való osztása, majd a modell betanítása egy ciklikus multiplexelés használatával, de az egyik hajtogatás. A modellnek a modell betanításához nem használt független adatkészletből való tesztelésekor pontosan megbecsülhető a modell képessége.

A **hiperparaméter-optimalizálás** a tanulási algoritmusok hiperparaméterek beállítása kiválasztásának problémája, általában azzal a céllal, hogy az algoritmus teljesítményének mértékét egy független adathalmazon optimalizálja. A **hiperparaméterek beállítása** olyan értékek, amelyeket a modell betanítási eljárásán kívül kell megadni. Az ezekkel az értékekkel kapcsolatos feltételezések befolyásolhatják a modellek rugalmasságát és pontosságát. A döntési fák olyan hiperparaméterek beállítása rendelkeznek, mint például a kívánt mélység és a fában lévő levelek száma. A support Vector Machines (SVMs) a téves besorolási feltételek megadását igényli. 

Az itt használt hiperparaméter optimalizálás egyik gyakori módja a rácsos keresés, vagy egy **paraméter söpör**. Ez a keresés egy tanulási algoritmus hiperparaméter területének egy részhalmazán halad végig. A Cross Validation (több ellenőrzés) teljesítmény-mérőszámot biztosít a rácsos keresési algoritmus által létrehozott optimális eredmények rendezéséhez. A hiperparaméter-megtakarítással használt CV segít korlátozni az olyan problémákat, mint például a modellek kiképzése az adatok betanításához, hogy a modell megőrizze a kapacitást azon általános adathalmazra, amelyről a betanítási adatok kinyerése történik.

Az általunk használt modellek közé tartozik a logisztikai és a lineáris regresszió, a véletlenszerű erdők és a színátmenet-növelő fák:

* A [lineáris regresszió](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.regression.LinearRegressionWithSGD.html#pyspark.mllib.regression.LinearRegressionWithSGD
) és az SGD egy lineáris regressziós modell, amely egy sztochasztikus átmenetes (SGD) módszert használ, valamint az optimalizálás és a szolgáltatás skálázására, hogy előre megjósolja a kifizetett tip-összegeket. 
* A [logisztikai regresszió a LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) vagy a "logit" regresszióval egy regressziós modell, amely akkor használható, ha a függő változó kategorikusan végzi az adatbesorolást. A LBFGS egy kvázi-Newton optimalizálási algoritmus, amely a Broyden – Fletcher – Goldfarb-Shanno (BFGS) algoritmust a korlátozott mennyiségű számítógép memóriájának használatával közelíti meg, és a gépi tanulásban széles körben használatos.
* A [véletlenszerű erdők](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) a döntési fák együttesei.  Számos döntési fát egyesítenek a túlilleszkedés kockázatainak csökkentése érdekében. A véletlenszerű erdők regresszióhoz és besoroláshoz használatosak, és a kategorikus funkciókat kezelhetik, és bővíthetők a többosztályos besorolási beállításokkal. Nem igénylik a szolgáltatások méretezését, és képesek rögzíteni a nem lineáris és a funkciók közötti interakciókat. A véletlenszerű erdők a besorolás és a regresszió egyik legsikeresebb gépi tanulási modellje.
* A [Gradient által növelt fák](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS-EK) a döntési fák együttesei. GBTS betanítása a iteratív a veszteségek csökkentése érdekében. A GBTS a regresszió és a besorolás, valamint a kategorikus funkciók kezelésére szolgál, nem szükséges a funkciók skálázása, és képes rögzíteni a nem lineáris és a funkciók közötti interakciókat. Használhatnak többosztályos besorolási beállításban is.

Az önéletrajz és a Hiperparaméter sweep használatával történő modellezési példák a bináris besorolási problémára mutatnak. A regressziós feladatokhoz tartozó fő témakörben az egyszerűbb példák (paraméterek nélkül) jelennek meg. A függelékben azonban a rugalmas háló használata a lineáris regresszióhoz és az önéletrajzhoz, valamint a véletlenszerű erdő regressziós szolgáltatásával történő ellenőrzés is megtörténik. A **rugalmas háló** egy szabályos regressziós módszer a lineáris regressziós modellek betöltéséhez, amelyek lineárisan ötvözik az L1 és az L2 mérőszámokat a [lasszó](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) és a [gerinc](https://en.wikipedia.org/wiki/Tikhonov_regularization) módszerének kiszabásával.   

<!-- -->

> [!NOTE]
> Bár a Spark MLlib Toolkit nagyméretű adatkészleteken való használatra lett kialakítva, viszonylag kis minta (~ 30 MB 170K-sorok használatával, az eredeti NYC-adatkészlet körülbelül 0,1%-a) használatos. Az itt megadott gyakorlat hatékonyan fut (körülbelül 10 percen belül) egy 2 munkavégző csomóponttal rendelkező HDInsight-fürtön. Ugyanez a kód kisebb módosításokkal is feldolgozható nagyobb adatkészletek feldolgozására, a memóriában tárolt adatgyorsítótárazás megfelelő módosításaival és a fürt méretének megváltoztatásával.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Beállítás: Spark-fürtök és jegyzetfüzetek
Ebben az útmutatóban a telepítési lépéseket és kódokat a HDInsight Spark 1,6 használatára vonatkozó útmutatóban ismertetjük. A HDInsight Spark 1,6-es és a Spark 2,0-fürtök esetében azonban Jupyter jegyzetfüzetek is elérhetők. A jegyzetfüzetek leírását és azok hivatkozásait az azokat tartalmazó GitHub-adattár [readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) tartalmazza. Emellett a kód itt és a csatolt jegyzetfüzetekben általános, és minden Spark-fürtön működnie kell. Ha nem a HDInsight Sparkot használja, akkor a fürt beállítása és a felügyeleti lépések némileg eltérnek az itt láthatótól. Kényelmi okokból a Spark 1,6-es és 2,0-es Jupyter-notebookokra mutató hivatkozásokat a Jupyter Notebook-kiszolgáló pyspark kernelében kell futtatni:

### <a name="spark-16-notebooks"></a>Spark 1,6 notebookok

[pySpark-Machine-learning-adat-tudomány-Spark-Advanced-adat-feltárás-modellezés. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): a notebook-#1 témaköröket tartalmaz, valamint a hiperparaméter finomhangolását és a több érvényesítést használó modellek fejlesztését.

### <a name="spark-20-notebooks"></a>Spark 2,0 notebookok

[Spark 2.0-pySpark3-Machine-learning-adat-tudomány-Spark-Advanced-adatok-Exploration-Modeling. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl információt nyújt arról, hogyan hajtható végre az adatok feltárása, modellezése és pontozása a Spark 2,0-fürtökben.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Beállítás: tárolóhelyek, kódtárak és az előre beállított Spark-környezet
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
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

# PRINT START TIME
import datetime
datetime.datetime.now()
```

**KIMENETI**

DateTime. datetime (2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Kódtárak importálása
Importálja a szükséges kódtárakat a következő kóddal:

```python
# LOAD PYSPARK LIBRARIES
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

A Jupyter-jegyzetfüzetek és az azok által megadott "Magics" kernelekkel kapcsolatos további információkért tekintse meg a [Jupyter notebookok számára elérhető kerneleket HDInsight Spark Linux-fürtökkel a HDInsight-on](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Adatfeldolgozás nyilvános blobból:
Az adatelemzési folyamat első lépése az adatok beolvasása a forrásokból, amelyek az adatfelderítési és-modellezési környezetben találhatók. Ez a környezet ebben az útmutatóban a Spark. Ez a szakasz a feladatok sorozatának elvégzéséhez szükséges kódot tartalmazza:

* az adatminta begyűjtése modellezésre
* olvasás a bemeneti adatkészletben (. TSV fájlként tárolva)
* az adattárolás formázása és tisztítása
* objektumok (RDD vagy adatkeretek) létrehozása és gyorsítótárazása a memóriában
* regisztrálja ideiglenes táblázatként az SQL-környezetben.

Itt látható az adatfeldolgozási kód.

```python
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

# CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
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

A fenti cella végrehajtásához szükséges idő: 276,62 másodperc

## <a name="data-exploration--visualization"></a>Adatfeltárási & vizualizáció
Miután az adatgyűjtés bekerült a Sparkba, az adatelemzési folyamat következő lépése az adatelemzési és vizualizációs eredmények mélyebb megismerése. Ebben a szakaszban az SQL-lekérdezések használatával vizsgáljuk meg a taxi-adatforrásokat, és kirajzoljuk a vizuális vizsgálathoz használt cél változókat és a jövőbeli funkciókat. Konkrétan az utasok számának gyakoriságát vesszük igénybe a taxiban, a tipp összegének gyakoriságát, és azt, hogy a tippek milyen mértékben változnak a fizetési mennyiség és a típus szerint.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Az utasok számának gyakoriságát ábrázoló hisztogram kirajzolása a taxis kirándulások mintájában
Ez a kód és az azt követő kódrészletek az SQL Magic használatával kérdezik le a mintát és a helyi mágiát az adatábrázoláshoz.

* **SQL Magic ( `%%sql` )** a HDInsight PySpark kernel egyszerűen beágyazott HiveQL-lekérdezéseket támogat a sqlContext. Az (-o VARIABLE_NAME) argumentum megőrzi az SQL-lekérdezés kimenetét a Jupyter-kiszolgálón található pandák DataFrame. Ez azt jelenti, hogy a helyi módban érhető el.
* A **`%%local` Magic** a kód helyi futtatására szolgál a Jupyter-kiszolgálón, amely a HDInsight-fürt átjárócsomóponthoz. `%%local`A mágia általában a Magic használatával `%%sql -o` futtatott lekérdezés futtatására szolgál. Az-o paraméter megőrzi az SQL-lekérdezés helyi kimenetét. Ezután a `%%local` Magic elindítja a kódrészletek következő készletét, hogy helyileg fusson a helyileg megőrzött SQL-lekérdezések kimenetén. A rendszer automatikusan megjeleníti a kimenetet a kód futtatása után.

A lekérdezés az utasok száma alapján kérdezi le az utakat. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# SQL QUERY
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count
```

Ez a kód létrehoz egy helyi adatkeretet a lekérdezés kimenetében, és kirajzolja az adatokat. A `%%local` Magic létrehoz egy helyi adatkeretet, `sqlResults` amely a matplotlib való ábrázoláshoz használható. 

<!-- -->

> [!NOTE]
> Ez a PySpark Magic többször is használatban van ebben az útmutatóban. Ha az adatmennyiség nagy, érdemes mintát venni egy olyan adatkeret létrehozásához, amely elfér a helyi memóriában.

<!-- -->

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Itt látható az a kód, amely az utakat az utasok száma szerint ábrázolja

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT PASSENGER NUMBER VS TRIP COUNTS
x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**KIMENETI**

![Utazások gyakorisága utasok száma szerint](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Több különböző típusú vizualizáció (tábla, torta, vonal, terület vagy sáv) közül választhat a notebook **Type (típus** ) gombjának használatával. Itt látható a sáv ábrázolása.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>A tip-összegek hisztogramjának, valamint a tip-mennyiségnek az utasok száma és a viteldíj alapján történő megadását ábrázolja.
SQL-lekérdezés használata az adatmintavételezéshez.

```sql
# SQL SQUERY
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
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline

# TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount ($) by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
ax.set_title('Tip amount by Fare amount ($)')
ax.set_xlabel('Fare Amount')
ax.set_ylabel('Tip Amount')
plt.axis([-2, 120, -2, 30])
plt.show()
```

**KIMENETI** 

![Tipp mennyiségének eloszlása](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tipp összege utasok száma szerint](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipp összege viteldíj alapján](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funkciók mérnöki, átalakítási és adatelőkészítési modellezéshez
Ez a szakasz leírja és megadja az adatfeldolgozáshoz használt eljárások kódját az ML-modellezésben. Ez a következő feladatok elvégzését mutatja be:

* Új szolgáltatás létrehozása az óráknak a forgalmi idő típusú raktárhelyekre particionálásával
* Az index és a on-Hot kódolású kategorikus funkciók
* Címkézett pont objektumok létrehozása a bemenethez ML függvényekben
* Véletlenszerű almintavételezést hozhat létre az adatból, és kioszthatja azokat képzésbe és tesztelési készletekbe
* Szolgáltatásskálázás
* Objektumok gyorsítótárazása a memóriában

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Új szolgáltatás létrehozása a forgalmi idő raktárhelyekre particionálásával
Ez a kód bemutatja, hogyan hozhat létre új szolgáltatást a forgalmi idő raktárhelyekre particionálásával, majd az eredményül kapott adatkeret gyorsítótárazásával a memóriában. A gyorsítótárazás olyan továbbfejlesztett végrehajtási időt eredményez, amelyben a rugalmasan elosztott adatkészleteket (RDD) és az adatkereteket ismételten használják. Ezért a RDD és az adatkereteket az útmutató számos fázisában gyorsítótárazjuk.

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
```

```python
# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**KIMENETI**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Index és egy gyors kódolású kategorikus funkciók
Ez a szakasz bemutatja, hogyan indexelheti vagy kódolhatja a kategorikus funkciókat a modellezési függvényekbe való bevitelhez. A MLlib modellezési és előrejelzési funkciói megkövetelik, hogy a rendszer a használat előtt indexelje vagy kódolja a kategorikus bemeneti adatokat tartalmazó szolgáltatásokat. 

A modelltől függően indexelni vagy kódolni kell őket különböző módokon. Például a logisztikai és a lineáris regressziós modellek egy-egy gyors kódolást igényelnek, ahol például három kategóriát tartalmazó funkció bővíthető három funkciós oszlopba, amelyek mindegyike 0 vagy 1 értéket tartalmaz a megfigyelés kategóriája alapján. A MLlib [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) függvényt biztosít egy gyors kódoláshoz. Ez a kódoló a Label indexek egy oszlopát a bináris vektorok egy oszlopára képezi le, amely legfeljebb egyetlen egyértékű lehet. Ez a kódolás lehetővé teszi az algoritmusok számára, hogy a kategorikus funkciókra alkalmazni lehessen a számszerű értékű funkciókat, például a logisztikai regressziót.

Itt látható a kategorikus funkciók indexelésére és kódolására szolgáló kód:

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

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

A fenti cella végrehajtásához szükséges idő: 3,14 másodperc

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Címkézett pont objektumok létrehozása a bemenethez ML függvényekben
Ez a szakasz olyan kódot tartalmaz, amely bemutatja, hogyan indexelheti a kategorikus szöveges adattípusokat címkézett pont adattípusként, és hogyan kódolja. Ez a transzformáció a MLlib logisztikai regresszió és más besorolási modellek betanítására és tesztelésére szolgáló szöveges adattípusokat készít elő. A címkézett pont objektumok olyan rugalmas elosztott adatkészletek (RDD-EK), amelyek a MLlib-ben a legtöbb ML-algoritmusnak megfelelő bemeneti adatokként vannak formázva. A [címkével ellátott pont](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) egy olyan helyi vektor, amely egy címkéhez vagy válaszhoz van társítva, vagy sűrű vagy ritka.

Itt látható a bináris besorolású szöveges funkciók indexelésére és kódolására szolgáló kód.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Ez a kód véletlenszerű mintavételt hoz létre az adatmennyiségről (itt 25% használatos). Bár az adathalmaz mérete miatt nem szükséges ehhez a példához, bemutatjuk, hogyan lehet az adatokat mintavételezéssel megtekinteni. Ezután megtudhatja, hogyan használhatja azt a saját problémájára, ha szükséges. Ha a minták nagy méretűek, a mintavételezés jelentős időt takaríthat meg a modellek betanítása közben. Ezután a mintát egy képzési részre (75%-ra) és egy tesztelési részre (25%-ra) bontottuk a besorolási és regressziós modellezéshez.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
from pyspark.sql.functions import rand

samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# CACHE TRAIN AND TEST DATA
trainData.cache()
testData.cache()

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

A fenti cella végrehajtásához szükséges idő: 0,31 másodperc

### <a name="feature-scaling"></a>Szolgáltatásskálázás
A szolgáltatás skálázása, más néven az adatok normalizálása, nem biztosítja, hogy a széles körben kifizetett értékekkel rendelkező funkciók ne legyenek nagy mértékben mérlegelve az objektív függvényben. A szolgáltatás skálázásának kódja a [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) használatával méretezi a szolgáltatásokat az egység variancia számára. A MLlib a lineáris regressziós és a sztochasztikus gradiens (SGD) használatával biztosítjuk. Az SGD egy népszerű algoritmus, amely számos más gépi tanulási modellt, például rendszeres regressziós vagy támogató vektoros gépeket (SVM) tanít.   

> [!TIP]
> Találtunk a LinearRegressionWithSGD algoritmust, hogy az érzékeny legyen a szolgáltatás skálázására.   
> 
> 

Az alábbi kód a reguláris lineáris SGD-algoritmussal használható változók méretezését nyújtja.

```python
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

A fenti cella végrehajtásához szükséges idő: 11,67 másodperc

### <a name="cache-objects-in-memory"></a>Objektumok gyorsítótárazása a memóriában
A ML-algoritmusok betanításához és teszteléséhez szükséges idő a besoroláshoz, a regresszióhoz és a méretezett funkciókhoz használt bemeneti adatkeret-objektumok gyorsítótárazásával csökkenthető.

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

A fenti cella végrehajtásához szükséges idő: 0,13 másodperc

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Tippelje meg, hogy a tippet bináris besorolási modellel fizetik-e ki
Ez a szakasz azt mutatja be, hogyan használható három modell a bináris besorolási feladathoz, amely azt jelzi, hogy egy tippet kell-e kifizetni a taxis utazáshoz. A bemutatott modellek a következők:

* Logisztikai regresszió 
* Véletlenszerű erdő
* Színátmenet-növelő fák

Az egyes modellek felépítési kódok szakasza a következő lépésekre oszlik: 

1. Betanítási adattípusok **modellezése** egyetlen paraméterrel
2. **Modell kiértékelése** mérőszámokkal rendelkező tesztelési adatkészleten
3. **Modell mentése** a blobban a későbbi felhasználás érdekében

Azt mutatjuk be, hogyan végezhető el az adatellenőrzés (CV) két módon:

1. Olyan **általános** egyéni kód használata, amely a MLlib bármely algoritmusára alkalmazható, és egy algoritmus bármely paramétere számára. 
2. A **PySpark CrossValidator folyamat függvény** használata. A CrossValidator a Spark 1.5.0 néhány korlátozásával rendelkezik: 
   
   * A folyamat modelljei nem menthetők és nem maradnak meg a későbbi felhasználás céljából.
   * Modell minden paramétere esetében nem használható.
   * Nem használható minden MLlib algoritmushoz.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>A logisztikai regressziós algoritmussal a bináris besoroláshoz használt általános kereszt-ellenőrzési és hiperparaméter
Az ebben a szakaszban található kód azt mutatja be, hogyan lehet betanítani, kiértékelni és menteni egy logisztikai regressziós modellt a [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , amely azt jelzi, hogy egy tippet fizettek-e a New York-i taxi Trip és a fare adatkészletben. A modell több ellenőrzési (CV) és hiperparaméter-elvezetéssel van betanítva, amelyet a MLlib bármely tanulási algoritmusára alkalmazhat.   

<!-- -->

> [!NOTE]
> Az egyéni KtgE kód végrehajtása több percet is igénybe vehet.

<!-- -->

**A logisztikai regressziós modell betanítása a CV és a hiperparaméter elsöprő használatával**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from pyspark.mllib.evaluation import BinaryClassificationMetrics

# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
from sklearn.grid_search import ParameterGrid
grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
paramGrid = list(ParameterGrid(grid))
numModels = len(paramGrid)

# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
nFolds = 3;
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

# BEGIN CV WITH PARAMETER SWEEP
for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create LabeledPoints from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowOneHotBinary)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        regt = paramGrid[j]['regType']
        regp = paramGrid[j]['regParam']
        iters = paramGrid[j]['iterations']
        tol = paramGrid[j]['tolerance']
        # Train logistic regression model with hypermarameter set
        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                  regParam=regp, tolerance = tol, intercept=True)
        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
        # Use ROC-AUC as accuracy metrics
        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
        metric = validMetrics.areaUnderROC
        metricSum[j] += metric

avgAcc = metricSum / nFolds;
bestParam = paramGrid[np.argmax(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                              iterations=bestParam['iterations'], 
                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                              intercept=True)


# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitBest.weights))
print("Intercept: " + str(logitBest.intercept))

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

Az ebben a szakaszban található kód azt mutatja be, hogyan értékelhető ki egy logisztikai regressziós modell egy tesztelési adatkészlettel, beleértve a ROC görbe ábráját is.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT LIBRARIES
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

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

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

PR = 0.985336538462 alatti terület

Terület: ROC = 0.983383274312

Összefoglaló statisztika

Precíziós = 0.984174341679

Visszahívás = 0.984174341679

F1 pontszám = 0.984174341679

A fenti cella végrehajtásához szükséges idő: 2,67 másodperc

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

#PREDICTIONS
predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVES
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

![Logisztikai regressziós ROC-görbe az általános megközelítéshez](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Modell megőrzése a blobban a későbbi felhasználás érdekében**

Az ebben a szakaszban szereplő kód azt mutatja be, hogyan menthető a logisztikai regressziós modell a felhasználáshoz.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

# PERSIST MODEL
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;

logitBest.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 34,57 másodperc

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>A MLlib CrossValidator-folyamat függvényének használata logisztikai regressziós (rugalmas regressziós) modellel
Az ebben a szakaszban található kód azt mutatja be, hogyan lehet betanítani, kiértékelni és menteni egy logisztikai regressziós modellt a [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , amely azt jelzi, hogy egy tippet fizettek-e a New York-i taxi Trip és a fare adatkészletben. A modell a Cross Validation (CV) használatával lett betanítva, és a MLlib CrossValidator-folyamat hiperparaméter való elvezetése az önéletrajzhoz, a következő paraméterrel: sweep.   

<!-- -->

> [!NOTE]
> A MLlib CV-kód végrehajtása több percet is igénybe vehet.

<!-- -->

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.classification import LogisticRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import BinaryClassificationEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
from sklearn.metrics import roc_curve,auc

# DEFINE ALGORITHM / MODEL
lr = LogisticRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build()

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=BinaryClassificationEvaluator(),
                    numFolds=3)

# CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


## PREDICT AND EVALUATE ON TEST DATA-SET

# USE TEST DATASET FOR PREDICTION
testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
test_predictions = cv_model.transform(testDataFrame)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
``` 

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 107,98 másodperc

**A ROC-görbe ábrázolása.**

A *predictionAndLabelsDF* táblaként, *tmp_resultsként* van regisztrálva az előző cellában. a *tmp_results* a lekérdezések és a kimeneti eredmények a sqlResults adatkeretbe való elvégzésére használhatók a rajzoláshoz. Itt látható a kód.

```python
# QUERY RESULTS
%%sql -q -o sqlResults
SELECT label, prediction, probability from tmp_results
```

Itt látható a ROC-görbe ábrázolására szolgáló kód.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
%%local
from sklearn.metrics import roc_curve,auc

# ROC CURVE
prob = [x["values"][1] for x in sqlResults["probability"]]
fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

#PLOT
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

![Logisztikai regressziós ROC görbe a MLlib CrossValidator](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Véletlenszerű erdő besorolása
Az ebben a szakaszban található kód azt mutatja be, hogyan lehet betanítani, kiértékelni és menteni egy véletlenszerű erdő-regressziót, amely azt jelzi, hogy egy tippet fizetnek-e a New York-i taxi Trip és a fare adatkészlet esetében.

```python
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
## UN-COMMENT IF YOU WANT TO PRING TREES
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

Terület: ROC = 0.985336538462

A fenti cella végrehajtásához szükséges idő: 26,72 másodperc

### <a name="gradient-boosting-trees-classification"></a>Színátmenet-növelő fák besorolása
Az ebben a szakaszban található kód azt mutatja be, hogyan lehet betanítani, kiértékelni és menteni egy átmenetes növelő fák modellt, amely azt jelzi, hogy egy tipp kifizetése megtörténik-e a New York-i taxi Trip és a fare adatkészletben.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                numIterations=10)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# Area under ROC curve
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

Terület: ROC = 0.985336538462

A fenti cella végrehajtásához szükséges idő: 28,13 másodperc

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Tip-mennyiség előrejelzése regressziós modellekkel (nem használ CV-t)
Ez a szakasz bemutatja, hogyan használható a regressziós feladat három modellje: megjósolhatja, hogy a tipp milyen összeget fizetett ki egy taxis utazáshoz más tip-funkciók alapján. A bemutatott modellek a következők:

* Szabályos lineáris regresszió
* Véletlenszerű erdő
* Színátmenet-növelő fák

Ezeket a modelleket a bevezetésben ismertetjük. Az egyes modellek felépítési kódok szakasza a következő lépésekre oszlik: 

1. Betanítási adattípusok **modellezése** egyetlen paraméterrel
2. **Modell kiértékelése** mérőszámokkal rendelkező tesztelési adatkészleten
3. **Modell mentése** a blobban a későbbi felhasználás érdekében   

<!-- -->

> [!NOTE] 
> Ez a szakasz a három regressziós modellel nem használja az átellenőrzést, mivel ez a logisztikai regressziós modellekben részletesen szerepelt. Egy példa arra, hogyan használható az önéletrajz a rugalmas hálóval a lineáris regresszióhoz a jelen témakör függelékében.

<!-- -->

<!-- -->

> [!NOTE] 
> Tapasztalataink szerint a LinearRegressionWithSGD-modellek konvergenciájában problémák merülhetnek fel, és a paramétereket egy érvényes modell beszerzéséhez kell módosítani/optimalizálni. A változók méretezése jelentősen segíti a konvergenciát. A rugalmas net-regresszió, amely a jelen témakör függelékében látható, a LinearRegressionWithSGD helyett is használható.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Lineáris regresszió SGD-val
Az ebben a szakaszban található kód azt mutatja be, hogyan használhatók a méretezhető funkciók olyan lineáris regressziók betanításához, amelyek a sztochasztikus gradienstől (SGD) való optimalizáláshoz, valamint a modell kiértékeléséhez, kiértékeléséhez és mentéséhez szükségesek az Azure Blob Storageban (WASB).

> [!TIP]
> Tapasztalataink szerint a LinearRegressionWithSGD modellek konvergenciáját illetően problémák merülhetnek fel, és a paramétereket egy érvényes modell beszerzéséhez gondosan kell módosítani/optimalizálni. A változók méretezése jelentősen segíti a konvergenciát.
> 
> 

```python
# LINEAR REGRESSION WITH SGD 

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

print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
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

Együtthatók: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,-0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Feltartóztatás: 0.854507624459

GYÖKÁTLAGOS = 1.23485131376

R-sqr = 0.597963951127

A fenti cella végrehajtásához szükséges idő: 38,62 másodperc

### <a name="random-forest-regression"></a>Véletlenszerű erdő regressziója
Az ebben a szakaszban található kód azt mutatja be, hogyan lehet betanítani, kiértékelni és menteni egy véletlenszerű erdő-modellt, amely előre jelezi a New York-i taxik adatmennyiségét.   

<!-- -->

> [!NOTE]
> A függelékben megtalálhatók az egyéni kódok használatával történő elvetést megadó paraméterek.

<!-- -->

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
# UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

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

GYÖKÁTLAGOS = 0.931981967875

R-sqr = 0.733445485802

A fenti cella végrehajtásához szükséges idő: 25,98 másodperc

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

# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

# EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
test_predictions= sqlContext.createDataFrame(predictionAndLabels)
test_predictions_pddf = test_predictions.toPandas()

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

GYÖKÁTLAGOS = 0.928172197114

R-sqr = 0.732680354389

A fenti cella végrehajtásához szükséges idő: 20,9 másodperc

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
import numpy as np

# PLOT
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 15])
plt.show(ax)
```

![Tényleges – vs-előrejelzett-tipp – összegek](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Függelék: további regressziós feladatok többértékű hitelesítéssel paraméter-elvetéssel
Ez a függelék olyan kódot tartalmaz, amely bemutatja, hogyan végezhető el az adatküldés a rugalmas háló használatával a lineáris regresszióhoz, és hogyan végezheti el az önéletrajz használatát az egyéni kód használatával a véletlenszerű erdő regressziós

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Több ellenőrzés a rugalmas háló használatával lineáris regresszióhoz
Az ebben a szakaszban található kód bemutatja, hogyan végezhető el a rugalmas háló használata a lineáris regresszióhoz, és hogyan lehet kiértékelni a modellt a tesztelési adataival.

```python
###  CV USING ELASTIC NET FOR LINEAR REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.regression import LinearRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import RegressionEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

# DEFINE ALGORITHM/MODEL
lr = LinearRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build() 

# DEFINE PIPELINE 
# SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
pipeline = Pipeline(stages=[lr])

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=RegressionEvaluator(),
                    numFolds=3)

# CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


# EVALUATE MODEL ON TEST SET
testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

# MAKE PREDICTIONS ON TEST DOCUMENTS
# cvModel uses the best model found (lrModel).
predictionAndLabels = cv_model.transform(testDataFrame)

# CONVERT TO DF AND SAVE REGISTER DF AS TABLE
predictionAndLabels.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

A fenti cella végrehajtásához szükséges idő: 161,21 másodperc

**Értékelés az R-SQR metrikával**

*tmp_results* az előző cellában struktúra-táblaként van regisztrálva. A tábla eredményei a *sqlResults* adatkeretbe kerülnek a nyomtatáshoz. Itt látható a kód

```python
# SELECT RESULTS
%%sql -q -o sqlResults
SELECT label,prediction from tmp_results
```

Itt látható az R-sqr kiszámításához használandó kód.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
from scipy import stats

#R-SQR TEST METRIC
corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
r2 = (corstats[2]*corstats[2])
print("R-sqr = %s" % r2)
```

**KIMENETI**

R-sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Kereszt-ellenőrzés a paraméteres leválasztással a véletlenszerű erdő regressziós egyéni kódjának használatával
Az ebben a szakaszban található kód azt mutatja be, hogyan végezheti el a több érvényesítést a paraméterek használatával a véletlenszerű erdők regressziójának egyéni kódjával, valamint a modellnek a tesztelési adataival való kiértékeléséhez.

```python
# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics
from sklearn.grid_search import ParameterGrid

## CREATE PARAMETER GRID
grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
paramGrid = list(ParameterGrid(grid))

## SPECIFY LEVELS OF CATEGORICAL VARIBLES
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
nFolds = 3;
numModels = len(paramGrid)
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create labeled points from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowIndexingRegression)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        maxD = paramGrid[j]['maxDepth']
        numT = paramGrid[j]['numTrees']
        # Train logistic regression model with hypermarameter set
        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=numT, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=maxD, maxBins=32)
        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
        # Use ROC-AUC as accuracy metrics
        validMetrics = RegressionMetrics(predictionAndLabels)
        metric = validMetrics.rootMeanSquaredError
        metricSum[j] += metric

avgAcc = metricSum/nFolds;
bestParam = paramGrid[np.argmin(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

## TRAIN FINAL MODL WIHT BEST PARAMETERS
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

# EVALUATE MODEL ON TEST DATA
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

#PRINT TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**KIMENETI**

GYÖKÁTLAGOS = 0.906972198262

R-sqr = 0.740751197012

A fenti cella végrehajtásához szükséges idő: 69,17 másodperc

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Objektumok tisztítása a memóriából és a nyomtatási modell helyeiről
`unpersist()`A használatával törölheti a memóriában gyorsítótárazott objektumokat.

```python
# UNPERSIST OBJECTS CACHED IN MEMORY

# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()
trainData.unpersist()
trainData.unpersist()

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

**KIMENETI**

PythonRDD [122] a RDD címen: PythonRDD. Scala: 43

* * A felhasználási jegyzetfüzetben használni kívánt fájlok kimeneti elérési útja. * * Ha egy független adatkészletet szeretne használni, másolja és illessze be ezeket a fájlneveket a "fogyasztási jegyzetfüzet" kifejezésbe.

```python
# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**KIMENETI**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0316 _47_ 30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 -0316 _51_ 28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0316 _50_ 17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 -0316 _51_ 57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 -0316 _50_ 40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0316 _52_ 18.827237"

## <a name="whats-next"></a>A következő lépések
Most, hogy létrehozta a regressziós és besorolási modelleket a Spark MlLib, készen áll arra, hogy megtudja, hogyan szerzi be és értékelje ki ezeket a modelleket.

**Modell felhasználása:** A jelen témakörben létrehozott besorolási és regressziós modellek pontszámának és értékelésének megismeréséhez tekintse meg a [Spark által készített gépi tanulási modellek pontszámát és értékelését](spark-model-consumption.md)ismertető szakaszt.
