---
title: 'Oktatóanyag: gépi tanulási alkalmazás létrehozása Apache Spark MLlib'
description: Útmutató arról, hogyan használható a Apache Spark MLlib egy olyan Machine learning-alkalmazás létrehozásához, amely a logisztikai regresszió segítségével osztályozza az adatkészleteket.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 5caa41b852bf55a11489db6c0bab871b20720e05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670655"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Oktatóanyag: gépi tanulási alkalmazás létrehozása Apache Spark MLlib és az Azure szinapszis Analytics használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre Apache Spark [MLlib](https://spark.apache.org/mllib/) olyan gépi tanulási alkalmazás létrehozásához, amely egyszerű prediktív elemzést végez egy Azure Open-adatkészleten. A Spark beépített gépi tanulási kódtárakat biztosít. Ez a példa a *besorolást* a logisztikai regresszión keresztül használja.

A SparkML és a MLlib olyan alapvető Spark-kódtárak, amelyek számos, a gépi tanulási feladatokhoz hasznos segédprogramot biztosítanak, beleértve a következőkre alkalmas segédprogramokat:

- Osztályozás
- Regresszió
- Fürtözés
- Témakör modellezése
- Egyrészes értékek elbomlása (SVD) és a fő összetevők elemzése (PEM)
- A hipotézis tesztelése és a minta statisztikáinak kiszámítása

## <a name="understand-classification-and-logistic-regression"></a>A besorolás és a logisztikai regresszió ismertetése

Az *osztályozás*, amely egy népszerű gépi tanulási feladat, a bemeneti adatok kategóriákba rendezésének folyamata. A besorolási algoritmus feladata, hogy kiderítse, hogyan rendeljen hozzá *címkéket* a megadott bemeneti adatokhoz. Tegyük fel például, hogy egy gépi tanulási algoritmus, amely adatokat fogad el bemenetként, és két kategóriába osztja el a készletet: a készleteket, amelyeket érdemes eladni, és a készleteket kell megőrizni.

A *logisztikai regresszió* egy algoritmus, amelyet besoroláshoz használhat. A Spark logisztikai regressziós API-ját *bináris besoroláshoz* vagy a bemeneti adatok két csoportba való besorolásához lehet hasznos. További információ a logisztikai regresszióról: [wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Összefoglalva, a logisztikai regressziós folyamat olyan *logisztikai függvényt* hoz létre, amellyel előre jelezhető, hogy egy bemeneti vektor egy csoportba vagy a másikba tartozik-e.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Prediktív elemzés – példa a New York-i taxi adataira

Ebben a példában a Spark használatával némi prediktív elemzést végezünk a New York-i taxival kapcsolatos tip-adatokról. Az adatokat az [Azure Open-adatkészletek](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)segítségével érheti el. Az adatkészlet ezen részhalmaza a Yellow taxi-utakról tartalmaz információkat, beleértve az egyes utazásokkal, a kezdési és befejezési időponttal és a hellyel, a díjszabással és egyéb érdekes attribútumokkal kapcsolatos információkat.

> [!IMPORTANT]
> Az adatok tárolási helyétől való kihúzása további költségekkel is jár.

A következő lépésekben olyan modellt fejlesztünk ki, amely azt jelzi, hogy egy adott utazáshoz tartozik-e tipp vagy sem.

## <a name="create-an-apache-spark-machine-learning-model"></a>Apache Spark Machine learning-modell létrehozása

1. Hozzon létre egy jegyzetfüzetet a PySpark kernel használatával. Útmutatásért lásd: [Jegyzetfüzet létrehozása](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importálja az alkalmazáshoz szükséges típusokat. Másolja és illessze be a következő kódot egy üres cellába, majd nyomja le a SHIFT + ENTER billentyűkombinációt. Vagy futtassa a cellát a kód bal oldalán található kék lejátszás ikon használatával.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    A PySpark kernel miatt nem kell explicit módon létrehoznia a környezeteket. A Spark-környezet automatikusan létrejön az első kód cellájának futtatásakor.

## <a name="construct-the-input-dataframe"></a>A bemeneti DataFrame felépítése

Mivel a nyers adatmennyiség parkettás formátumú, a Spark-környezettel közvetlenül is lehívhatja a fájlt a memóriába DataFrame. Bár az alábbi lépésekben szereplő kód az alapértelmezett beállításokat használja, az adattípusok és egyéb sémák attribútumainak leképezése is kényszeríthető, ha szükséges.

1. A következő sorok futtatásával hozzon létre egy Spark-DataFrame a kód egy új cellába való beillesztésével. Ez a lépés a megnyitott adatkészletek API-n keresztül kéri le az adatokat. Az összes ilyen adatmennyiség körülbelül 1 500 000 000 sort hoz létre. 

   A kiszolgáló nélküli Apache Spark készlet méretétől függően előfordulhat, hogy a nyers adatmennyiség túl nagy, vagy túl sok időt vesz igénybe. Ezt az adatmennyiséget lejjebb is szűrheti. Az alábbi kódrészlet egy olyan `start_date` `end_date` szűrőt alkalmaz, amely egy adott havi adatmennyiséget ad vissza.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Az egyszerű szűrés hátránya, hogy statisztikai szempontból az adatokra vonatkozó elfogultság is bevezethető. Egy másik módszer a Spark-ba épített mintavétel használata. 

   A következő kód csökkenti az adatkészletet körülbelül 2 000 sorra, ha azt az előző kód után alkalmazza a rendszer. Ezt a mintavételi lépést az egyszerű szűrő helyett, vagy az egyszerű szűrővel együtt használhatja.

    ```python
    # To make development easier, faster, and less expensive, downsample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Most már megtekintheti az itt olvasható információt. Általában jobban áttekintheti az adatokat a teljes készlet helyett egy részhalmazsal az adatkészlet méretétől függően. 

   Az alábbi kód két módszert kínál az adatmegtekintésre. Az első módszer az alapszintű. A második módszer sokkal gazdagabb hálózati élményt nyújt, valamint az adatábrázolási képességet is.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. A generált adatkészlet méretétől függően, ha többször kell kísérletezni vagy futtatni a jegyzetfüzetet, érdemes lehet helyileg gyorsítótárazni az adatkészletet a munkaterületen. Az explicit gyorsítótárazás három módon hajtható végre:

   - Mentse a DataFrame helyileg a fájlként.
   - Mentse a DataFrame ideiglenes táblába vagy nézetbe.
   - Mentse a DataFrame állandó táblaként.

A módszerek közül az első kettő a következő példákban szerepel.

Egy ideiglenes tábla vagy nézet létrehozása különböző hozzáférési útvonalakat biztosít az adateléréshez, de csak a Spark-példány munkamenetének idejére tart.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Az adatok előkészítése

A nyers formában lévő adatforrások gyakran nem alkalmasak arra, hogy közvetlenül a modellbe továbbítsák őket. Végre kell hajtania egy sor műveletet az adatokon, hogy azok olyan állapotba kerüljön, amelyben a modell fel tudja használni azt.

A következő kódban négy műveleti osztályt hajt végre:

- Kiugró adatok vagy helytelen értékek eltávolítása szűréssel.
- Az oszlopok eltávolítása nem szükséges.
- A nyers adatokból származtatott új oszlopok létrehozása, hogy a modell hatékonyabban működjön. Ezt a műveletet néha featurization nevezik.
- Címkézés. Mivel a bináris besorolást végzi (egy adott útvonalon nem lesz tipp, vagy nem az adott utazáson), a tipp értékét 0 vagy 1 értékre kell konvertálnia.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Ezután elvégezheti a második továbbítást az adatátvitelhez a végső funkciók hozzáadásához.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Logisztikai regressziós modell létrehozása

A végső feladat a címkével ellátott adatok átalakítása a logisztikai regresszió használatával elemezhető formátumba. A logisztikai regressziós algoritmus bemenetének a *címke/szolgáltatás vektoros párok* készletének kell lennie, ahol a *funkció vektor* a bemeneti pontot jelölő számok vektora. 

Ezért a kategorikus oszlopokat számmá kell alakítani. Konkrétan a `trafficTimeBins` és az `weekdayString` oszlopokat egész szám típusú ábrázolásra kell konvertálnia. Az átalakítás végrehajtásához több módszer is rendelkezésre áll. A következő példa az `OneHotEncoder` általános megközelítést alkalmazza.

```python
# Because the sample uses an algorithm that works only with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new DataFrame that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Ez a művelet egy új DataFrame eredményez, amely a megfelelő formátumban lévő összes oszloppal betanítja a modellt.

## <a name="train-a-logistic-regression-model"></a>Logisztikai regressziós modell betanítása

Az első feladata, hogy az adatkészletet egy betanítási csoportba, egy tesztelési vagy érvényesítési csoportba ossza fel. A felosztás tetszőleges. Kísérletezzen a különböző felosztási beállításokkal, és ellenőrizze, hogy érintik-e a modellt.

```python
# Decide on the split between training and testing data from the DataFrame
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the DataFrame into test and training DataFrames
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Most, hogy két DataFrames van, a következő feladat a modell képletének létrehozása és futtatása a betanítási DataFrame. Ezután ellenőrizheti a tesztelési DataFrame. Kísérletezzen a modell képletének különböző verzióival a különböző kombinációk hatásának megtekintéséhez.

> [!Note]
> A modell mentéséhez szüksége lesz a *Storage blob-adatközreműködői* Azure-szerepkörre. A Storage-fiók területen lépjen a **Access Control (iam)** elemre, és válassza a **szerepkör-hozzárendelés hozzáadása** elemet. Rendelje hozzá a Storage blob adatközreműködői szerepkört a Azure SQL Database-kiszolgálóhoz. Ez a lépés csak tulajdonosi jogosultságokkal rendelkező tagok számára hajtható végre. 
>
>A különböző Azure-beli beépített szerepkörökhöz tekintse meg [ezt az útmutatót](../../role-based-access-control/built-in-roles.md).

```python
## Create a new logistic regression object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create a logistic regression model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional, but it's another form of inter-session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset; evaluation using area under ROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

A cella kimenete:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Az előrejelzés vizuális ábrázolásának létrehozása

Most létrehozhat egy végső vizualizációt, amely segít a teszt eredményeinek indoklásában. A [Roc görbe](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) egy módszer az eredmény áttekintésére.

```python
## Plot the ROC curve; no need for pandas, because this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![A tip-modellben a logisztikai regresszióhoz tartozó ROC-görbét bemutató gráf.](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>A Spark-példány leállítása

Miután befejezte az alkalmazás futtatását, állítsa le a notebookot az erőforrások felszabadításához a TAB bezárásával. Vagy válassza a **munkamenet befejezése** elemet a jegyzetfüzet alján található állapot paneljén.

## <a name="see-also"></a>Lásd még

- [Áttekintés: Apache Spark az Azure szinapszis Analytics szolgáltatásban](apache-spark-overview.md)

## <a name="next-steps"></a>Következő lépések

- [.NET Apache Spark dokumentációhoz](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
- [Apache Spark hivatalos dokumentáció](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> A hivatalos Apache Spark dokumentációja a Spark-konzollal való használatra támaszkodik, amely nem érhető el Apache Spark az Azure szinapszis Analyticsben. Ehelyett használjon [jegyzetfüzetet](../quickstart-apache-spark-notebook.md) vagy [IntelliJ](../spark/intellij-tool-synapse.md) -élményt.