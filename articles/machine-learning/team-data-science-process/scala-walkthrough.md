---
title: Adatelemzés a Scala és a Spark használatával az Azure-ban – csoportos adatelemzési folyamat
description: A Scala használata felügyelt gépi tanulási feladatokhoz a Spark skálázható MLlib és a Spark ML-csomagok egy Azure HDInsight Spark-fürtön.
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
ms.openlocfilehash: 9ae4549fe343422bbf60275a97768ca407f2dc7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321377"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Adatelemzés a Scala és a Spark használatával az Azure rendszerben
Ez a cikk bemutatja, hogyan használható a Scala a felügyelt gépi tanulási feladatokhoz a Spark skálázható MLlib és a Spark ML-csomagok egy Azure HDInsight Spark-fürtön. Végigvezeti az [adatelemzési folyamatot](./index.yml)alkotó feladatokon: az adatok betöltését és feltárását, a vizualizációt, a szolgáltatások mérnöki használatát, a modellezést és a modell felhasználását. A cikkben szereplő modellek közé tartozik a logisztika és a lineáris regresszió, a véletlenszerű erdők és a gradiens által növelt fák (GBTs), valamint két közös felügyelt gépi tanulási feladat:

* Regressziós probléma: a tipp összegének ($) előrejelzése egy taxis útra
* Bináris besorolás: tipp vagy nincs tipp (1/0) a taxis utazáshoz

A modellezési folyamathoz meg kell adni egy tesztelési adatkészletet és a megfelelő pontossági mérőszámokat. Ebből a cikkből megtudhatja, hogyan tárolhatja ezeket a modelleket az Azure Blob Storage-ban, és hogyan adhatja meg és értékelheti a prediktív teljesítményt. Ez a cikk azt is ismerteti, hogyan optimalizálhatja a modelleket a több ellenőrzési és a Hyper-paraméteres működés használatával. A felhasznált adatmennyiség a 2013-es, a GitHubon elérhető, a New York-i és a viteldíj-adatkészletet tartalmazó minta.

A [Scala](https://www.scala-lang.org/), a Java virtuális gépen alapuló nyelv, integrálja az objektumorientált és funkcionális nyelvi fogalmakat. Ez egy méretezhető nyelv, amely jól illeszkedik a Felhőbeli elosztott feldolgozáshoz, és az Azure Spark-fürtökön fut.

A [Spark](https://spark.apache.org/) egy nyílt forráskódú, párhuzamos feldolgozást végző keretrendszer, amely támogatja a memórián belüli feldolgozást Big Data elemzési alkalmazások teljesítményének növelése érdekében. A Spark-feldolgozó motor a sebességre, a könnyű használatra és a kifinomult elemzésekre épül. A Spark memóriában elosztott számítási képességei jó választást biztosítanak a gépi tanulásban és a Graph-számításokban az ismétlődő algoritmusokhoz. A [Spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) csomag olyan, az adatkockákra épülő magas szintű API-k egységes készletét biztosítja, amelyek segítségével gyakorlati gépi tanulási folyamatokat hozhat létre és hangolhat össze. A [MLlib](https://spark.apache.org/mllib/) a Spark skálázható gépi tanulási könyvtára, amely modellezési képességeket biztosít az elosztott környezet számára.

A [HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) a nyílt forráskódú Spark Azure által üzemeltetett ajánlata. Emellett támogatja a Jupyter Scala notebookok használatát a Spark-fürtön, és a Spark SQL interaktív lekérdezések futtatásával átalakíthatja, szűrheti és megjelenítheti az Azure Blob Storage-ban tárolt adatforrásokat. A cikkben található Scala-kódrészletek biztosítják a megoldásokat, és megjelenítik a kapcsolódó mintaterületeket, amelyek a Spark-fürtökön telepített Jupyter-jegyzetfüzetekben lévő adatokat jelenítik meg. Az ezekben a témakörökben található modellezési lépések olyan kódot mutatnak be, amely bemutatja, hogyan kell betanítani, kiértékelni, menteni és felhasználni az egyes modelleket.

A telepítési lépések és kódok ebben a cikkben az Azure HDInsight 3,4 Spark 1,6. Azonban a jelen cikkben és a [Scala Jupyter Notebookban](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) szereplő kód általános, és minden Spark-fürtön működnie kell. Ha nem használja a HDInsight Sparkot, a fürt beállítási és felügyeleti lépései némileg eltérhetnek a jelen cikkben láthatótól.

> [!NOTE]
> Egy olyan témakörben, amely bemutatja, hogyan használhatja a Pythont a Scala helyett a teljes adatelemzési folyamat feladatainak elvégzéséhez, lásd: [adatelemzés a Spark on Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
* Rendelkeznie kell egy Azure-előfizetéssel. Ha még nem rendelkezik ilyennel, szerezze be [az ingyenes Azure-próbaverziót](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Az alábbi eljárások elvégzéséhez szüksége lesz egy Azure HDInsight 3,4 Spark 1,6-fürtre. Fürt létrehozásához tekintse [meg az első lépések: Apache Spark létrehozása az Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)című témakör utasításait. Állítsa be a fürt típusát és verzióját a **fürt típusának kiválasztása** menüben.

![HDInsight-fürt típusának konfigurálása](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

A New York-i utazási adatokról és a Spark-fürtön található Jupyter-jegyzetfüzetből származó kódok végrehajtásával kapcsolatos útmutatásért tekintse meg a megfelelő részeket az [adatelemzés az Azure HDInsight-on való használatának áttekintése](spark-overview.md)című témakörben.  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Scala-kód végrehajtása Jupyter-jegyzetfüzetből a Spark-fürtön
Jupyter notebookot a Azure Portal is indíthat. Keresse meg az irányítópulton a Spark-fürtöt, majd kattintson rá a fürt felügyeleti lapjának megadásához. Ezután kattintson a **fürt irányítópultok** elemre, majd kattintson a **Jupyter notebook** elemre a Spark-fürthöz társított jegyzetfüzet megnyitásához.

![A fürt irányítópultja és a Jupyter notebookok](./media/scala-walkthrough/spark-jupyter-on-portal.png)

A Jupyter-jegyzetfüzeteket a https:// &lt; clustername. azurehdinsight.net/jupyter webhelyen is elérheti &gt; . Cserélje le a *clustername* nevet a fürt nevére. A Jupyter-jegyzetfüzetek eléréséhez szüksége lesz a rendszergazdai fiók jelszavára.

![Ugrás a Jupyter notebookokra a fürt nevének használatával](./media/scala-walkthrough/spark-jupyter-notebook.png)

A **Scala** lehetőség kiválasztásával megtekintheti a PySpark API-t használó előre csomagolt jegyzetfüzetek néhány példáját tartalmazó könyvtárat. A "a" ipynb notebook, amely tartalmazza a következő Spark-csomagokhoz tartozó kódrészleteket, a a [githubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)érhető el: a felderítési modellezés és pontozás.

A jegyzetfüzetet közvetlenül a GitHubról a Spark-fürt Jupyter Notebook kiszolgálójára töltheti fel. A Jupyter kezdőlapján kattintson a **feltöltés** gombra. A Fájlkezelőben illessze be a Scala notebookhoz tartozó GitHub (nyers tartalom) URL-címét, majd kattintson a **Megnyitás** gombra. A Scala notebook a következő URL-címen érhető el:

[Feltárás – modellezés és pontozás – a-Scala. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Beállítás: előre beállított Spark-és kaptár-környezetek, Spark-varázslatok és Spark-kódtárak
### <a name="preset-spark-and-hive-contexts"></a>Előre definiált Spark-és struktúra-környezetek

```scala
# SET THE START TIME
import java.util.Calendar
val beginningTime = Calendar.getInstance().getTime()
```

A Jupyter-jegyzetfüzetekhez biztosított Spark-kernelek előre beállított környezettel rendelkeznek. Nem kell explicit módon beállítania a Spark vagy a kaptár környezetét, mielőtt elkezdi a munkát a fejleszteni kívánt alkalmazással. Az előre beállított környezetek a következők:

* `sc` SparkContext
* `sqlContext` HiveContext

### <a name="spark-magics"></a>Spark-varázslatok
A Spark kernel néhány előre definiált "varázslatot" tartalmaz, amelyek olyan speciális parancsok, amelyekkel meghívható `%%` . A következő kódrészletek közül kettőt használunk.

* `%%local` Megadja, hogy a következő sorokban a kód helyileg fog futni. A kódnak érvényes Scala-kódnak kell lennie.
* `%%sql -o <variable name>` Struktúra-lekérdezést hajt végre a szolgáltatáson `sqlContext` . Ha a `-o` paramétert átadja, a rendszer a lekérdezés eredményét `%%local` Spark-adatkeretként őrzi meg a Scala környezetben.

A Jupyter-jegyzetfüzetek és az előre definiált "Magics" kernelekkel kapcsolatos további információkért `%%` (például `%%local` :) tekintse [meg a Jupyter notebookok számára elérhető kerneleket a HDInsight Spark Linux-fürtökkel a HDInsight-on](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Kódtárak importálása
Importálja a Spark, a MLlib és más könyvtárakat, amelyekre szüksége lesz a következő kód használatával.

```scala
# IMPORT SPARK AND JAVA LIBRARIES
import org.apache.spark.sql.SQLContext
import org.apache.spark.sql.functions._
import java.text.SimpleDateFormat
import java.util.Calendar
import sqlContext.implicits._
import org.apache.spark.sql.Row

# IMPORT SPARK SQL FUNCTIONS
import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
import org.apache.spark.sql.functions.rand

# IMPORT SPARK ML FUNCTIONS
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

# IMPORT SPARK MLLIB FUNCTIONS
import org.apache.spark.mllib.linalg.{Vector, Vectors}
import org.apache.spark.mllib.util.MLUtils
import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
import org.apache.spark.mllib.tree.configuration.BoostingStrategy
import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

# SPECIFY SQLCONTEXT
val sqlContext = new SQLContext(sc)
```

## <a name="data-ingestion"></a>Adatfeldolgozás
Az adatelemzési folyamat első lépése az elemezni kívánt adatmennyiség beolvasása. Az adatok külső forrásokból vagy rendszerekből származnak, ahol az adatfelderítési és-modellezési környezetben található. Ebben a cikkben a betöltött adatmennyiség a taxi Trip és a fare fájl (. TSV-fájlként tárolt) 0,1%-os mintája. Az adatfeltárási és-modellezési környezet a Spark. Ez a szakasz a következő feladatok elvégzéséhez szükséges kódot tartalmazza:

1. Adja meg a könyvtár elérési útját az adattárolóhoz és a modellhez.
2. Olvassa el a bemeneti adatkészletben (. TSV fájlként tárolva).
3. Definiáljon egy sémát az adattípushoz, és törölje az adathalmazt.
4. Hozzon létre egy megtisztított adatkeretet, és gyorsítótárazza a memóriában.
5. Regisztrálja az adatmennyiséget ideiglenes táblázatként a SQLContext-ben.
6. A tábla lekérdezése és az eredmények importálása egy adatkeretbe.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>A tárolási helyszínek elérési útjának megadása az Azure Blob Storage-ban
A Spark képes olvasni és írni az Azure Blob Storage-ba. A Spark használatával feldolgozhatja a meglévő adatait, majd a blob Storage-ban is tárolhatja az eredményeket.

Ha modelleket vagy fájlokat szeretne menteni a blob Storage-ban, pontosan meg kell adnia az elérési utat. Hivatkozzon a Spark-fürthöz csatolt alapértelmezett tárolóra egy olyan útvonal használatával, amely a következővel kezdődik: `wasb:///` . Hivatkozhat más helyszínekre a használatával `wasb://` .

A következő mintakód az olvasni kívánt bemeneti adatok helyét adja meg, valamint a blob Storage elérési útját, amely ahhoz a Spark-fürthöz van csatolva, ahol a modell el lesz mentve.

```scala
# SET PATHS TO DATA AND MODEL FILE LOCATIONS
# INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
val header = taxi_train_file.first;

# SET THE MODEL STORAGE DIRECTORY PATH
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";
```

### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Adatimportálás, RDD létrehozása és adatkeret definiálása a séma alapján

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
val sqlContext = new SQLContext(sc)
val taxi_schema = StructType(
    Array(
        StructField("medallion", StringType, true),
        StructField("hack_license", StringType, true),
        StructField("vendor_id", StringType, true),
        StructField("rate_code", DoubleType, true),
        StructField("store_and_fwd_flag", StringType, true),
        StructField("pickup_datetime", StringType, true),
        StructField("dropoff_datetime", StringType, true),
        StructField("pickup_hour", DoubleType, true),
        StructField("pickup_week", DoubleType, true),
        StructField("weekday", DoubleType, true),
        StructField("passenger_count", DoubleType, true),
        StructField("trip_time_in_secs", DoubleType, true),
        StructField("trip_distance", DoubleType, true),
        StructField("pickup_longitude", DoubleType, true),
        StructField("pickup_latitude", DoubleType, true),
        StructField("dropoff_longitude", DoubleType, true),
        StructField("dropoff_latitude", DoubleType, true),
        StructField("direct_distance", StringType, true),
        StructField("payment_type", StringType, true),
        StructField("fare_amount", DoubleType, true),
        StructField("surcharge", DoubleType, true),
        StructField("mta_tax", DoubleType, true),
        StructField("tip_amount", DoubleType, true),
        StructField("tolls_amount", DoubleType, true),
        StructField("total_amount", DoubleType, true),
        StructField("tipped", DoubleType, true),
        StructField("tip_class", DoubleType, true)
        )
    )

# CAST VARIABLES ACCORDING TO THE SCHEMA
val taxi_temp = (taxi_train_file.map(_.split("\t"))
                        .filter((r) => r(0) != "medallion")
                         .map(p => Row(p(0), p(1), p(2),
                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


# CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

# CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Kimeneti**

A cella futtatásának ideje: 8 másodperc.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>A tábla lekérdezése és az eredmények importálása egy adatkeretbe
Ezután kérdezze le a táblázatot a viteldíjak, az utasok és a tippek számára; kiszűri a sérült és a függőben lévő adatértékeket; és nyomtasson ki több sort.

```scala
# QUERY THE DATA
val sqlStatement = """
    SELECT fare_amount, passenger_count, tip_amount, tipped
    FROM taxi_train
    WHERE passenger_count > 0 AND passenger_count < 7
    AND fare_amount > 0 AND fare_amount < 200
    AND payment_type in ('CSH', 'CRD')
    AND tip_amount > 0 AND tip_amount < 25
"""
val sqlResultsDF = sqlContext.sql(sqlStatement)

# SHOW ONLY THE TOP THREE ROWS
sqlResultsDF.show(3)
```

**Kimeneti**

| fare_amount | passenger_count | tip_amount | végű |
| --- | --- | --- | --- |
|        13,5 |1.0 |2.9 |1.0 |
|        16,0 |2.0 |3.4 |1.0 |
|        10,5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Adatfelderítés és-vizualizáció
Miután a Sparkba helyezte az információt, az adatelemzési folyamat következő lépése az adatelemzési és vizualizációs eredmények mélyebb megismerése. Ebben a szakaszban az SQL-lekérdezések használatával vizsgálja meg a taxi-adatforrásokat. Ezt követően importálja az eredményeket egy adatkeretbe, hogy az automatikus vizualizáció Jupyter funkció használatával kirajzolja a célzott változókat és a vizuális ellenőrzéshez szükséges jövőbeli funkciókat.

### <a name="use-local-and-sql-magic-to-plot-data"></a>A helyi és az SQL Magic használata az adatábrázoláshoz
Alapértelmezés szerint a Jupyter-jegyzetfüzetből futtatott kódrészletek kimenete a munkavégző csomópontokon megőrzött munkamenet kontextusában érhető el. Ha minden számításhoz el szeretné menteni a munkavégző csomópontokat, és ha a számításhoz szükséges összes adat helyileg elérhető a Jupyter-kiszolgáló csomópontján (amely a fő csomópont), a Magic használatával futtathatja a kódrészletet a `%%local` Jupyter-kiszolgálón.

* **SQL Magic** ( `%%sql` ). A HDInsight Spark kernel egyszerűen beágyazott HiveQL-lekérdezéseket támogat a SQLContext. A ( `-o VARIABLE_NAME` ) argumentum megőrzi az SQL-lekérdezés kimenetét a Jupyter-kiszolgálón található pandák adatkeretként. Ez a beállítás azt jelenti, hogy a kimenet a helyi módban lesz elérhető.
* `%%local`**Magic**. A `%%local` Magic helyileg futtatja a kódot a Jupyter-kiszolgálón, amely a HDInsight-fürt fő csomópontja. A magict általában a (z `%%local` `%%sql` ) paraméterrel együtt a Magic szolgáltatással együtt kell használni `-o` . A `-o` paraméter megőrzi az SQL-lekérdezés helyi kimenetét, majd a `%%local` Magic elindítja a következő kódrészletet, hogy helyileg fusson a helyileg MEGőrzött SQL-lekérdezések kimenetén.

### <a name="query-the-data-by-using-sql"></a>Az adatlekérdezés SQL használatával
Ez a lekérdezés beolvassa a taxis utakat a viteldíjak összege, az utasok száma és a tip-összeg alapján.

```scala
# RUN THE SQL QUERY
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25
```

A következő kódban a `%%local` Magic létrehoz egy helyi adatkeretet, a sqlResults. A sqlResults használatával a matplotlib segítségével ábrázolhatja a nyomtatást.

> [!TIP]
> A helyi Magic többször is használatban van ebben a cikkben. Ha az adathalmaz nagy méretű, kóstolja meg a helyi memóriában elférő adatkeret létrehozását.
> 
> 

### <a name="plot-the-data"></a>Az adatábrázolás
A Python-kód használatával ábrázolhatja az adatkeretet, miután a helyi kontextusban Panda-adatkeretként van kiválasztva.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
sqlResults
```

 A Spark kernel automatikusan megjeleníti az SQL-(HiveQL-) lekérdezések kimenetét a kód futtatása után. Több típusú vizualizáció közül választhat:

* Tábla
* Torta
* Vonal
* Terület
* Sáv

A kód az alábbi kódot ábrázolja:

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# PLOT TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.show()
```

**Kimeneti**

![Tipp összegének hisztogramja](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tipp összege utasok száma szerint](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tipp összege viteldíj alapján](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Szolgáltatások és átalakítási funkciók létrehozása, majd az adatok előkészítője a modellezési függvényekbe való bevitelhez
A Spark ML-ből és a MLlib-ből származó famodelles modellezési függvények esetében különféle technikákat kell előkészítenie, mint például a dobozolási, az indexelés, az egy gyors kódolás és a vektorizációt. A jelen szakaszban ismertetett eljárások az alábbiak:

1. Hozzon létre egy új szolgáltatást **dobozolási** órával a forgalmi idő gyűjtőbe.
2. Alkalmazzon **indexelést és egy gyors kódolást** a kategorikus funkciókra.
3. **Kóstolja meg és ossza szét az adathalmazt** képzésbe és tesztelési frakcióba.
4. **Adja meg a betanítási változót és a funkciókat**, majd hozzon létre indexelt vagy egy gyors kódolású betanítást, és tesztelje a bemeneti feliratú pont rugalmas elosztott adatkészleteit (RDD) vagy adatkereteket.
5. A gépi tanulási modellekhez bemenetként használandó **funkciók és célok automatikus kategorizálása és vektorizálhatja** .

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Hozzon létre egy új szolgáltatást dobozolási órával a forgalmi idő gyűjtők számára
Ez a kód bemutatja, hogyan hozhat létre egy új szolgáltatást a dobozolási órákkal a forgalmi idő gyűjtők számára, és hogyan gyorsítótárazhatja az eredményül kapott adatkeretet a memóriában. Ahol a RDD és az adatkereteket ismételten használják, a gyorsítótárazás a jobb végrehajtási időpontokat eredményezi. Ennek megfelelően az alábbi eljárásokban több fázisban fogja gyorsítótárazni a RDD és az adatkereteket.

```scala
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
val sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train
"""
val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>A kategorikus funkciók indexelése és egy gyors kódolása
A MLlib modellezési és előrejelzési funkciói olyan funkciókat igényelnek, amelyekben a használat előtt a kategorikus bemeneti adatok indexelve vagy kódolva lesznek. Ez a szakasz bemutatja, hogyan indexelheti vagy kódolhatja a kategorikus funkciókat a modellezési függvényekbe való bevitelhez.

A modelltől függően különböző módokon kell indexelni vagy kódolni a modelleket. A logisztikai és a lineáris regressziós modellekhez például egy gyors kódolásra van szükség. Egy három kategóriát tartalmazó funkció például három funkció oszlopba bővíthető. Az egyes oszlopok a megfigyelési kategóriától függően 0 vagy 1 értéket tartalmaznak. Az MLlib biztosítja az [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) függvényt egy gyors kódoláshoz. Ez a kódoló a Label indexek egy oszlopát a bináris vektorok egy oszlopára képezi le, amely legfeljebb egyetlen egyértékű lehet. Ebben a kódolásban az algoritmusok, amelyek a numerikus értékekkel rendelkező funkciókat (például a logisztikai regressziót) várnak, a kategorikus funkciókra alkalmazhatók.

Itt csak négy változót alakít át a példák megjelenítéséhez, amelyek karakterláncok. Más változókat (például a hétköznapokat) is indexelheti a numerikus értékek szerint, kategorikus változókként.

Az indexeléshez, a `StringIndexer()` és az egy gyors kódoláshoz használja a `OneHotEncoder()` functions elemet a MLlib. Itt látható a kategorikus funkciók indexelésére és kódolására szolgáló kód:

```scala
# CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# INDEX AND ENCODE VENDOR_ID
val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
val encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
val indexed = stringIndexer.transform(encoded1)
val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
val encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
val indexed = stringIndexer.transform(encoded2)
val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
val encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
val indexed = stringIndexer.transform(encoded3)
val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
val encodedFinal = encoder.transform(indexed)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Kimeneti**

A cella futtatásához szükséges idő: 4 másodperc.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Az adathalmaz mintavételezése és felosztása képzésbe és tesztelési hányadokra
Ez a kód véletlenszerű mintavételt hoz létre az adatmennyiségről (ebben a példában 25%-ot). Bár ehhez a példához nem szükséges mintavételezés az adathalmaz mérete miatt, a cikk bemutatja, hogyan lehet mintát venni, hogy tudja, hogyan használhatja azt a saját problémáira, ha szükséges. Ha a minták nagy méretűek, ez jelentős időt takaríthat meg a modellek betanítása közben. Ezután Ossza szét a mintát egy képzési részbe (ebben a példában a 75%-ban) és egy tesztelési részt (ebben a példában 25%-ot) a besorolási és regressziós modellezéshez.

Adjon hozzá egy véletlenszerű számot (0 és 1 között) az egyes sorokhoz (egy "Rand" oszlopban), amellyel kiválaszthatja az átellenőrzési munkákat a betanítás során.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
val samplingFraction = 0.25;
val trainingFraction = 0.75;
val testingFraction = (1-trainingFraction);
val seed = 1234;
val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
val sampledDFcount = encodedFinalSampledTmp.count().toInt

val generateRandomDouble = udf(() => {
    scala.util.Random.nextDouble
})

# ADD A RANDOM NUMBER FOR CROSS-VALIDATION
val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

# SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
# INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
val trainData = splits(0)
val testData = splits(1)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Kimeneti**

A cella futtatásának ideje: 2 másodperc.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Adja meg a betanítási változót és a funkciókat, majd hozzon létre indexelt vagy egy gyors kódolású betanítást, és tesztelje a bemeneti feliratú RDD vagy adatkereteket
Ez a szakasz olyan kódot tartalmaz, amely bemutatja, hogyan indexelheti a kategorikus szöveges adattípusokat egy címkézett pont adattípusként, és hogyan kódolhatja, hogy a MLlib logisztikai regresszió és más besorolási modellek betanítására és tesztelésére is használható legyen. A címkézett pont objektumok olyan RDD, amelyek a MLlib-ben a gépi tanulási algoritmusok többsége által szükséges bemeneti adatokként vannak formázva. A [címkével ellátott pont](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) egy olyan helyi vektor, amely egy címkéhez vagy válaszhoz van társítva, vagy sűrű vagy ritka.

Ebben a kódban meg kell adnia a cél (függő) változót és a modellek betanításához használni kívánt szolgáltatásokat. Ezután létrehozhatja az indexelt vagy egy gyors kódolású betanítást, és tesztelheti a bemeneti feliratú pont RDD vagy adatkereteit.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

# CREATE INDEXED LABELED POINT RDD OBJECTS
val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

# CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
val indexedTESTbinaryDF = indexedTESTbinary.toDF()
val indexedTRAINregDF = indexedTRAINreg.toDF()
val indexedTESTregDF = indexedTESTreg.toDF()

# CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
val OneHotTRAIN = assemblerOneHot.transform(trainData)
val OneHotTEST = assemblerOneHot.transform(testData)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Kimeneti**

A cella futtatásához szükséges idő: 4 másodperc.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>A gépi tanulási modellekhez bemenetként használandó funkciók és célok automatikus kategorizálása és vektorizálhatja
A Spark ML használatával kategorizálhatja a cél és a szolgáltatások faalapú modellezési függvényekbe való használatát. A kód két feladatot hajt végre:

* Létrehoz egy bináris célt a besoroláshoz, ha 0 vagy 1 értéket rendel az egyes adatpontokhoz 0 és 1 között, a 0,5 küszöbérték használatával.
* Funkciók automatikus kategorizálása. Ha az egyes funkciókhoz tartozó eltérő numerikus értékek száma kevesebb, mint 32, akkor ez a szolgáltatás kategorizálva van.

Itt látható a két feladathoz tartozó kód.

```scala
# CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
# CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINregDF)
val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)
```


## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Bináris besorolási modell: Tippelje meg, hogy díjköteles-e a tipp
Ebben a szakaszban háromféle bináris besorolási modellt hoz létre annak megbecsléséhez, hogy ki kell-e fizetni a tippet:

* **Logisztikai regressziós modell** a Spark ml `LogisticRegression()` függvény használatával
* **Véletlenszerű erdő besorolási modell** a Spark ml függvény használatával `RandomForestClassifier()`
* **Színátmenetet növelő faosztályozási modell** a MLlib `GradientBoostedTrees()` függvény használatával

### <a name="create-a-logistic-regression-model"></a>Logisztikai regressziós modell létrehozása
Ezután hozzon létre egy logisztikai regressziós modellt a Spark ML `LogisticRegression()` függvény használatával. A modell-létrehozási kódot a következő lépések sorozatában hozza létre:

1. **A modellre** vonatkozó adatkészletek betanítása egyetlen paraméterrel.
2. **Értékelje ki a modellt** egy olyan tesztelési adatkészleten, amely metrikákkal rendelkezik.
3. **Mentse a modellt** a blob Storage-ban a későbbi felhasználás érdekében.
4. **A modell kiértékelése a** tesztelési adatként.
5. **Az eredményeket** a fogadó működési jellemző (Roc) görbékkel ábrázolhatja.

Az alábbi eljárás a következő kódokat ismerteti:

```scala
# CREATE A LOGISTIC REGRESSION MODEL
val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
val lrModel = lr.fit(OneHotTRAIN)

# PREDICT ON THE TEST DATA SET
val predictions = lrModel.transform(OneHotTEST)

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)

# SAVE THE MODEL
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LogisticRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);
```

Az eredmények betöltése, pontszáma és mentése.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON THE TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
predictions.registerTempTable("testResults")

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC RESULTS
println("ROC on test data = " + ROC)
```

**Kimeneti**

ROC on test Reporting = 0.9827381497557599

A Python használata a helyi pandák adatkereteken a ROC-görbe ábrázolásához.

```scala
# QUERY THE RESULTS
%%sql -q -o sqlResults
SELECT tipped, probability from testResults


# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
predictions_pddf = sqlResults[["tipped","probFloat"]]

# PREDICT THE ROC CURVE
# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
prob = predictions_pddf["probFloat"]
fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT THE ROC CURVE
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

**Kimeneti**

![Tipp vagy nincs tipp ROC-görbe](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Véletlenszerű erdő besorolási modell létrehozása
Ezután hozzon létre egy véletlenszerű erdő besorolási modellt a Spark ML `RandomForestClassifier()` függvény használatával, majd értékelje ki a modellt a tesztelési adatain.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE THE RANDOM FOREST CLASSIFIER MODEL
val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

# FIT THE MODEL
val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

# EVALUATE THE MODEL
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(predictions)
println("F1 score on test data: " + Test_f1Score);

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)
```

**Kimeneti**

ROC on test Reporting = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>GBT-besorolási modell létrehozása
Ezután hozzon létre egy GBT besorolási modellt a MLlib `GradientBoostedTrees()` függvény használatával, majd értékelje ki a modellt a tesztelési adatain.

```scala
# TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE GBT CLASSIFICATION MODEL
val boostingStrategy = BoostingStrategy.defaultParams("Classification")
boostingStrategy.numIterations = 20
boostingStrategy.treeStrategy.numClasses = 2
boostingStrategy.treeStrategy.maxDepth = 5
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

# TRAIN THE MODEL
val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

# SAVE THE MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "GBT_Classification__"
val filename = modelDir.concat(modelName).concat(datestamp)
gbtModel.save(sc, filename);

# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
val labelAndPreds = indexedTESTbinary.map { point =>
  val prediction = gbtModel.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
println("Test Error = " + testErr)

# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
val metrics = new MulticlassMetrics(labelAndPreds)
println(s"Precision: ${metrics.precision}")
println(s"Recall: ${metrics.recall}")
println(s"F1 Score: ${metrics.fMeasure}")

val metrics = new BinaryClassificationMetrics(labelAndPreds)
println(s"Area under PR curve: ${metrics.areaUnderPR}")
println(s"Area under ROC curve: ${metrics.areaUnderROC}")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC METRIC
println(s"Area under ROC curve: ${metrics.areaUnderROC}")
```

**Kimeneti**

ROC-görbe alatti terület: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regressziós modell: tipp előrejelzésének összege
Ebben a szakaszban két regressziós modellt hoz létre a tipp összegének előrejelzéséhez:

* Egy **szabályos lineáris regressziós modell** a Spark ml függvény használatával `LinearRegression()` . Mentse a modellt, és értékelje ki a modellt a tesztelési adatként.
* **Színátmenet-növelő fa regressziós modell** a Spark ml `GBTRegressor()` függvény használatával.

### <a name="create-a-regularized-linear-regression-model"></a>Szabályos lineáris regressziós modell létrehozása

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

# FIT THE MODEL BY USING DATA FRAMES
val lrModel = lr.fit(OneHotTRAIN)
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
val trainingSummary = lrModel.summary
println(s"numIterations: ${trainingSummary.totalIterations}")
println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
trainingSummary.residuals.show()
println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
println(s"r2: ${trainingSummary.r2}")

# SAVE THE MODEL IN AZURE BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LinearRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);

# PRINT THE COEFFICIENTS
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = lrModel.transform(OneHotTEST)

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```


**Kimeneti**

A cella futtatásának ideje: 13 másodperc.

```scala
# LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
predictions.registerTempTable("testResults")

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("R-sqr on test data = " + r2)
```

**Kimeneti**

R-sqr = 0.5960320470835743

Ezután lekérdezheti a teszt eredményeit adatkeretként, és a AutoVizWidget és a matplotlib használatával jelenítheti meg.

```sql
# RUN A SQL QUERY
%%sql -q -o sqlResults
select * from testResults

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
sqlResults
```

A kód létrehoz egy helyi adatkeretet a lekérdezés kimenetében, és kirajzolja az adatokat. A `%%local` Magic egy helyi adatkeretet hoz létre, `sqlResults` amely a matplotlib való ábrázolásra használható.

> [!NOTE]
> Ez a Spark Magic többször is használatban van ebben a cikkben. Ha az adatmennyiség nagy, érdemes mintát venni egy olyan adatkeret létrehozásához, amely elfér a helyi memóriában.
> 
> 

Ábrázolások létrehozása a Python matplotlib használatával.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
sqlResults
%matplotlib inline
import numpy as np

# PLOT THE RESULTS
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 8])
plt.show(ax)
```

**Kimeneti**

![Tipp összege: tényleges és előre jelzett](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>GBT regressziós modell létrehozása
Hozzon létre egy GBT regressziós modellt a Spark ML `GBTRegressor()` függvény használatával, majd értékelje ki a modellt a teszt adatain.

A [színátmenet által növelt fák](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS-EK) a döntési fák együttesei. A GBTS vonatok döntési feladatait iteratív a veszteségek csökkentése érdekében. A GBTS a regresszió és a besorolás használatára használhatja. Képesek a kategorikus funkciók kezelésére, nem igénylik a funkciók skálázását, és rögzíthetik a nem lineáris és a funkciók közötti interakciókat. Ezeket használhatja többosztályos besorolási beállításban is.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# TRAIN A GBT REGRESSION MODEL
val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

# MAKE PREDICTIONS
val predictions = gbtModel.transform(indexedTESTwithCatFeat)

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(predictions)


# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("Test R-sqr is: " + Test_R2);
```

**Kimeneti**

Az R-sqr tesztelése: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Speciális modellezési segédeszközök az optimalizáláshoz
Ebben a szakaszban a fejlesztők által gyakran használt gépi tanulási segédprogramokat használhatja a modell optimalizálásához. Pontosabban optimalizálhatja a gépi tanulási modelleket, ha a paraméter-megtakarítást és a több érvényesítést is használja:

* Az adat felosztása a betanítási és az ellenőrzési készletekbe, a modell optimalizálása a betanítási csoporton belüli Hyper-paraméteres leválasztással, és értékelés egy ellenőrző készleten (lineáris regresszió)
* A modell optimalizálása a Spark ML CrossValidator funkciójának (bináris besorolás) használatával történő átállítási és a Hyper-paraméteres Elvetés használatával
* Optimalizálja a modellt egyéni, átellenőrzési és paraméter-elsöprő kód használatával bármely Machine learning-függvény és-paraméter használatára (lineáris regresszió)

A többszörös **Érvényesítés** egy olyan technika, amely kiértékeli, hogy az ismert adathalmazon alapuló modellek mennyire jól általánosítják az olyan adatkészletek funkcióinak előrejelzését, amelyeken még nincs betanítva. Ennek a technikának az általános ötlete, hogy a modell az ismert adatok adathalmazára van betanítva, és az előrejelzések pontossága egy független adatkészleten van tesztelve. A közös megvalósítás egy adatkészlet felosztása a *k*-redők közé, majd a modell betanítása egy ciklikus multiplexelés, de az egyik hajtogatási módszer.

A **Hyper-paraméter optimalizálása** olyan Hyper-paraméterek kiválasztásának a problémája, amely egy tanulási algoritmushoz szükséges, általában azzal a céllal, hogy az algoritmus teljesítményének mértékét egy független adathalmazon optimalizálja. A Hyper-paraméter olyan érték, amelyet a modell betanítási eljárásán kívül kell megadnia. A Hyper-paraméterek értékeivel kapcsolatos feltételezések befolyásolhatják a modell rugalmasságát és pontosságát. A döntési fák olyan Hyper-paraméterekkel rendelkeznek, mint például a kívánt mélység és a fában lévő levelek száma. A támogatási vektoros gép (SVM) esetében meg kell adni egy téves besorolási szankciót.

A Hyper-paraméterek optimalizálásának gyakori módja, ha egy rácsos keresést használ, más néven a **paramétert** is. A rácsos keresés során a rendszer részletes keresést hajt végre egy tanulási algoritmus Hyper-paraméterének egy adott részhalmazának értékein. A kereszthivatkozások teljesítmény-mérőszámot biztosíthatnak a rácsos keresési algoritmus által előállított optimális eredmények rendezéséhez. Ha a több-ellenőrzéses Hyper-paramétert használja, akkor a problémák korlátozásához, például a modell túlillesztéséhez használhatja az adatképzést. Így a modell megőrzi a kapacitást arra az általános adathalmazra, amelyből a betanítási adatok kinyerése megtörténik.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Lineáris regressziós modell optimalizálása Hyper-paraméter-megtakarítással
Ezután bontsa ki az adat a betanítási és ellenőrzési készletekbe lehetőséget, és a modell optimalizálásához használja a Hyper-paramétert, és értékelje ki az ellenőrzési készletet (lineáris regresszió).

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# RENAME `tip_amount` AS A LABEL
val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
OneHotTRAINLabeled.cache()
OneHotTESTLabeled.cache()

# DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

# DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
val trainPct = 0.75
val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = trainValidationSplit.fit(OneHotTRAINLabeled)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

println("Test R-sqr is: " + Test_R2);
```

**Kimeneti**

Az R-sqr tesztelése: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>A bináris besorolási modell optimalizálása a kereszt-ellenőrzés és a Hyper-paraméter-áthúzás használatával
Ebből a szakaszból megtudhatja, hogyan optimalizálhat egy bináris besorolási modellt a több-ellenőrzési és a Hyper-paraméteres elsöprő megoldás használatával. Ez a Spark ML `CrossValidator` függvényt használja.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
indexedTRAINwithCatFeatBinTargetRF.cache()
indexedTESTwithCatFeatBinTargetRF.cache()

# DEFINE THE ESTIMATOR FUNCTION
val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

# SPECIFY THE NUMBER OF FOLDS
val numFolds = 3

# DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

# COMPUTE THE TEST F1 SCORE
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Kimeneti**

A cella futtatásának ideje: 33 másodperc.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>A lineáris regressziós modell optimalizálása egyéni kereszt-ellenőrzési és paraméter-elsöprő kód használatával
Ezután optimalizálja a modellt egyéni kód használatával, és azonosítsa a legjobb modell-paramétereket a legmagasabb pontossági feltétel használatával. Ezután hozza létre a végső modellt, értékelje ki a modellt a tesztelési adatként, és mentse a modellt a blob Storage-ban. Végül töltse be a modellt, a pontszám tesztelési adatok és a pontosság kiértékelése.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

val nFolds = 3
val numModels = paramGrid.size
val numParamsinGrid = 2

# SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

var maxDepth = -1
var numTrees = -1
var param = ""
var paramval = -1
var validateLB = -1.0
var validateUB = -1.0
val h = 1.0 / nFolds;
val RMSE  = Array.fill(numModels)(0.0)

# CREATE K-FOLDS
val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
for (i <- 0 to (nFolds-1)) {
    validateLB = i * h
    validateUB = (i + 1) * h
    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    validationLabPt.cache()
    trainCVLabPt.cache()

    for (nParamSets <- 0 to (numModels-1)) {
        for (nParams <- 0 to (numParamsinGrid-1)) {
            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
            if (param == "maxDepth") {maxDepth = paramval}
            if (param == "numTrees") {numTrees = paramval}
        }
        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=numTrees, maxDepth=maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
        val labelAndPreds = validationLabPt.map { point =>
                                                 val prediction = rfModel.predict(point.features)
                                                  ( prediction, point.label )
                                                }
        val validMetrics = new RegressionMetrics(labelAndPreds)
        val rmse = validMetrics.rootMeanSquaredError
        RMSE(nParamSets) += rmse
    }
    validationLabPt.unpersist();
    trainCVLabPt.unpersist();
}
val minRMSEindex = RMSE.indexOf(RMSE.min)

# GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
var best_maxDepth = -1
var best_numTrees = -1
for (nParams <- 0 to (numParamsinGrid-1)) {
    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
    if (param == "maxDepth") {best_maxDepth = paramval}
    if (param == "numTrees") {best_numTrees = paramval}
}

# CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)

# SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "BestCV_RF_Regression__"
val filename = modelDir.concat(modelName).concat(datestamp)
best_rfModel.save(sc, filename);

# PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = best_rfModel.predict(point.features)
                                        ( prediction, point.label )
                                       }

val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");


# LOAD THE MODEL
val savedRFModel = RandomForestModel.load(sc, filename)

val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = savedRFModel.predict(point.features)
                                        ( prediction, point.label )
                                       }
# TEST THE MODEL
val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2
```

**Kimeneti**

A cella futtatásának ideje: 61 másodperc.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Spark által készített gépi tanulási modellek automatikus használata a Scala-vel
Az Azure adatelemzési folyamatát alkotó feladatokkal kapcsolatos témakörök áttekintését itt találja: [csoportos adatelemzési folyamat](./index.yml).

A [csoportos adatelemzési folyamat](walkthroughs.md) bemutatói ismertetik azokat a végpontok közötti bemutatókat, amelyek bemutatják a csoportos adatelemzési folyamat lépéseit adott forgatókönyvek esetén. A forgatókönyvek azt is bemutatják, hogyan kombinálhatja a Felhőbeli és a helyszíni eszközöket és szolgáltatásokat munkafolyamat vagy folyamatba egy intelligens alkalmazás létrehozásához.

A [Spark-alapú gépi tanulási modellek](spark-model-consumption.md) azt mutatják be, hogyan használható a Scala Code az új adatkészletek automatikus betöltéséhez és az Azure Blob Storage-ban mentett gépi tanulási modellekkel való összegyűjtéséhez. Követheti az itt megadott utasításokat, és egyszerűen lecserélheti a Python-kódot a Scala Code-ra ebben a cikkben az automatikus felhasználás érdekében.