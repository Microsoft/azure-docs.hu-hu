---
title: 'Oktatóanyag: regresszió automatikus gépi tanulással'
titleSuffix: Azure Machine Learning
description: Kódot írhat a Python SDK-val egy automatizált gépi tanulási kísérlet létrehozásához, amely egy regressziós modellt hoz létre.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 08/14/2020
ms.custom: devx-track-python, automl
ms.openlocfilehash: 85129cf282e39b4f4932cc5e9f7cfd72d1e445b0
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210635"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Oktatóanyag: Automatizált gépi tanulás használata a taxiutak árának előrejelzéséhez

Ebben az oktatóanyagban a Azure Machine Learning SDK-ban található automatizált gépi tanulás használatával [regressziós modellt](concept-automated-ml.md#regression) hozhat létre a New York-i taxi díjszabásának előrejelzéséhez. Ez a folyamat fogadja az oktatási és konfigurációs beállításokat, és automatikusan megismétli a különböző funkció-normalizálás/szabványosítási módszerek, modellek és hiperparaméter-beállítások kombinációit, hogy a legjobb modellt lehessen megérkezni.

![Folyamatábra](./media/tutorial-auto-train-models/flow2.png)

Ebben az oktatóanyagban kódot fog írni a Python SDK használatával.  A következő feladatokat fogja megismerni:

> [!div class="checklist"]
> * Az Azure Open-adatkészletek használatával letöltheti, átalakíthatja és megtisztíthatja az adatokat
> * Automatizált gépi tanulási regressziós modell betanítása
> * Modell pontosságának kiszámítása

Próbálja ki az automatizált gépi tanulást is a következő típusú modellek esetében: 

* [Oktatóanyag: hozzon létre egy besorolási modellt automatikus ml-vel Azure Machine learning](tutorial-first-experiment-automated-ml.md) -egy kód nélküli példa.
* [Oktatóanyag: a kereslet automatikus gépi tanulással való előrejelzése](tutorial-automated-ml-forecast.md) – a kód nélküli példa.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a Azure Machine Learning [ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Ha még nem rendelkezik Azure Machine Learning munkaterülettel vagy notebook virtuális géppel, fejezze be a [telepítési útmutatót](tutorial-1st-experiment-sdk-setup.md) .
* A telepítési oktatóanyag befejezése után nyissa meg az *oktatóanyagok/Regression-automl-NYC-taxi-Data/Regression-Automated-ml. ipynb* notebookot ugyanazzal a notebook-kiszolgálóval.

Ez az oktatóanyag a [githubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha saját [helyi környezetben](how-to-configure-environment.md#local)szeretné futtatni. A szükséges csomagok beszerzéséhez 
* [Telepítse a teljes `automl` ügyfelet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment).
* Futtassa `pip install azureml-opendatasets azureml-widgets` a parancsot a szükséges csomagok beszerzéséhez.

## <a name="download-and-prepare-data"></a>Az adatgyűjtés letöltése és előkészítése

Importálja a szükséges csomagokat. A nyílt adatkészletek csomag az egyes adatforrásokat jelképező osztályt tartalmaz ( `NycTlcGreen` például) a letöltés előtt egyszerűen szűrheti a dátum-paramétereket.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Először hozzon létre egy dataframe a taxi-adattároláshoz. Ha nem Spark-környezetben dolgozik, a nyílt adatkészletek csak egy hónapos adat letöltését teszik lehetővé bizonyos osztályokkal, hogy elkerülje `MemoryError` a nagyméretű adatkészleteket.

A taxi-adatok letöltéséhez egyszerre egy hónapot iteratív le, és mielőtt hozzáfűzi, hogy `green_taxi_df` véletlenszerűen mintát 2 000 rekordokat minden hónapból, hogy elkerülje a dataframe felduzzadása. Ezután tekintse meg az adatmegjelenítést.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   paymentType |fareAmount |extra| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|    tripType|
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4,84|Nincsenek|Nincsenek|– 73,88|40,84|– 73,94|...|2|15,00|0,50|0,50|0.3|0,00|0,00|Nan|16,30|
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0,69|Nincsenek|Nincsenek|– 73,96|40,81|– 73,96|...|2|4,50|1,00|0,50|0.3|0,00|0,00|Nan|6,30|
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|Nincsenek|Nincsenek|– 73,92|40,76|– 73,91|...|2|4,00|0,00|0,50|0.3|0,00|0,00|Nan|4,80|
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0,00|Nincsenek|Nincsenek|– 73,81|40,70|– 73,82|...|2|12,50|0,50|0,50|0.3|0,00|0,00|Nan|13,80|
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0,50|Nincsenek|Nincsenek|– 73,92|40,76|– 73,92|...|2|4,00|0,50|0,50|0|0,00|0,00|Nan|5,00|
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Nincsenek|Nincsenek|– 73,96|40,72|– 73,95|...|2|6,50|0,50|0,50|0.3|0,00|0,00|Nan|7,80|
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0,90|Nincsenek|Nincsenek|– 73,88|40,76|– 73,87|...|2|6,00|0,00|0,50|0.3|0,00|0,00|Nan|6,80|
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3,30|Nincsenek|Nincsenek|– 73,96|40,72|– 73,91|...|2|12,50|0,50|0,50|0.3|0,00|0,00|Nan|13,80|
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1,19|Nincsenek|Nincsenek|– 73,94|40,71|– 73,95|...|1|7,00|0,00|0,50|0.3|1,75|0,00|Nan|9,55|
|432136|2|2015-01-22 23:16:33 2015-01-22 23:20:13 1 0,65|Nincsenek|Nincsenek|– 73,94|40,71|– 73,94|...|2|5,00|0,50|0,50|0.3|0,00|0,00|Nan|6,30|

Most, hogy a kezdeti adatok betöltődik, Definiáljon egy függvényt, amely különböző időalapú szolgáltatásokat hoz létre a pickup datetime mezőből. Ez új mezőket hoz létre a hónap, a hónap napja, a hét napja és a nap órájában, és lehetővé teszi a modell időalapú szezonális felszámítását. Használja a `apply()` függvényt a dataframe, hogy a iteratív alkalmazza a `build_time_features()` függvényt a taxi összes sorára.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   paymentType|fareAmount  |extra| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|tripType|month_num|day_of_month|day_of_week|hour_of_day
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|----|----|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4,84|Nincsenek|Nincsenek|– 73,88|40,84|– 73,94|...|2|15,00|0,50|0,50|0.3|0,00|0,00|Nan|16,30|1,00|1|11|6|
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0,69|Nincsenek|Nincsenek|– 73,96|40,81|– 73,96|...|2|4,50|1,00|0,50|0.3|0,00|0,00|Nan|6,30|1,00|1|20|1|
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|Nincsenek|Nincsenek|– 73,92|40,76|– 73,91|...|2|4,00|0,00|0,50|0.3|0,00|0,00|Nan|4,80|1,00|1|1|3|
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0,00|Nincsenek|Nincsenek|– 73,81|40,70|– 73,82|...|2|12,50|0,50|0,50|0.3|0,00|0,00|Nan|13,80|1,00|1|17|5|
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0,50|Nincsenek|Nincsenek|– 73,92|40,76|– 73,92|...|2|4,00|0,50|0,50|0|0,00|0,00|Nan|5,00|1,00|1|1|3|
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Nincsenek|Nincsenek|– 73,96|40,72|– 73,95|...|2|6,50|0,50|0,50|0.3|0,00|0,00|Nan|7,80|1,00|1|4|6|
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0,90|Nincsenek|Nincsenek|– 73,88|40,76|– 73,87|...|2|6,00|0,00|0,50|0.3|0,00|0,00|Nan|6,80|1,00|1|3|5|
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3,30|Nincsenek|Nincsenek|– 73,96|40,72|– 73,91|...|2|12,50|0,50|0,50|0.3|0,00|0,00|Nan|13,80|1,00|1|9|4|
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1,19|Nincsenek|Nincsenek|– 73,94|40,71|– 73,95|...|1|7,00|0,00|0,50|0.3|1,75|0,00|Nan|9,55|1,00|1|11|6|
|432136|2|2015-01-22 23:16:33 2015-01-22 23:20:13 1 0,65|Nincsenek|Nincsenek|– 73,94|40,71|– 73,94|...|2|5,00|0,50|0,50|0.3|0,00|0,00|Nan|6,30|1,00|1|22|3|

Távolítson el néhány olyan oszlopot, amelyre nem lesz szüksége a betanításhoz vagy a további funkciók létrehozásához.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Az adattisztítás

Futtassa a `describe()` függvényt az új dataframe, és tekintse meg az egyes mezők összegző statisztikáit.

```python
green_taxi_df.describe()
```

|vendorID|passengerCount|tripDistance|pickupLongitude|pickupLatitude|dropoffLongitude|dropoffLatitude|  totalAmount|month_num day_of_month|day_of_week|hour_of_day
|----|----|---|---|----|---|---|---|---|---|---|
|count|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|
|középérték|1,78|1,37|2,87|– 73,83|40,69|– 73,84|40,70|14,75|6,50|15,13|
|szórás|0.41|1.04|2.93|2,76|1.52|2,61|1,44|12,08|3,45|8,45|
|p|1,00|0,00|0,00|– 74,66|0,00|– 74,66|0,00|– 300,00|1,00|1,00|
|25%|2.00|1,00|1.06|– 73,96|40,70|– 73,97|40,70|7,80|3,75|8,00|
|50%|2.00|1,00|1,90|– 73,94|40,75|– 73,94|40,75|11,30|6,50|15,00|
|75%|2.00|1,00|3,60|– 73,92|40,80|– 73,91|40,79|17,80|9,25|22,00|
|max.|2.00|9,00|97,57|0,00|41,93|0,00|41,94|450,00|12,00|30,00|


Az összegző statisztikában láthatja, hogy több olyan mező van, amely kiugró vagy értékkel rendelkezik, ami csökkenti a modell pontosságát. Először szűrje a Lat/Long mezőket a Manhattan terület határain belülre. Ezzel kiszűri a hosszú taxi-utakat vagy-utazásokat, amelyek a más funkciókkal való kapcsolatuk tekintetében kiugró módon jelennek meg.

Emellett szűrheti a `tripDistance` mezőt úgy, hogy nullánál nagyobb legyen, de kevesebb, mint 31 mérföld (a haversine távolság a két lat/hosszú pár között). Ezzel kiküszöbölheti a hosszú kiugró utakat, amelyekben inkonzisztens utazási díj szerepel.

Végül a `totalAmount` mező negatív értékeket tartalmaz a taxi viteldíjak esetében, amelyek nem ésszerűek a modell kontextusában, és a `passengerCount` mező hibás adatokkal rendelkezik, és a minimális érték nulla.

Szűrje ki ezeket a rendellenességeket a Query functions használatával, majd távolítsa el az utolsó néhány oszlopot, amely nem szükséges a betanításhoz.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Hívja `describe()` meg újra az adatvesztést, hogy a takarítás a várt módon működött. Most már készen áll a gépi tanulási modell betanításához használt, a taxi, a nyaralás és az időjárási idő előkészítésére.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Munkaterület konfigurálása

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. A [munkaterület](/python/api/azureml-core/azureml.core.workspace.workspace) egy olyan osztály, amely elfogadja az Azure-előfizetést és az erőforrás-információkat. Létrehoz egy felhőalapú erőforrást is a modell futtatásának figyelésére és nyomon követésére. `Workspace.from_config()` beolvassa a (z) **config.js** fájlt, és betölti a hitelesítési adatokat egy nevű objektumba `ws` . A `ws` a kód további részében használható ebben az oktatóanyagban.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Az adat felosztása a vonatra és a tesztelési csoportokra

Ossza fel az adatgyűjtést a betanítási és tesztelési készletekbe a `train_test_split` könyvtárban található függvény használatával `scikit-learn` . Ez a függvény elkülöníti az adatait az x (**szolgáltatások**) adatkészletbe a modell betanításához, valamint az y (**előre jelzett értékek**) adatkészletet a teszteléshez.

A `test_size` paraméter határozza meg a teszteléshez lefoglalható adatmennyiség százalékos arányát. A `random_state` paraméter állítja be a magot a véletlenszerű generátorba, hogy a determinisztikus legyenek kiosztva.

```python
from sklearn.model_selection import train_test_split

x_train, x_test = train_test_split(final_df, test_size=0.2, random_state=223)
```

Ennek a lépésnek a célja, hogy az adatpontokkal tesztelje a modell betanítására nem használt befejezett modellt a valódi pontosság mérése érdekében.

Más szóval a jól betanított modellnek képesnek kell lennie arra, hogy a már nem látott adatok alapján pontosan elvégezze az előrejelzések készítését. Most már készen áll a gépi tanulási modell automatikus betanítására.

## <a name="automatically-train-a-model"></a>Modell automatikus betanítása

A modellek automatikus betanításához hajtsa végre a következő lépéseket:
1. Adja meg a kísérlet futtatásának beállításait. Csatolja a betanítási adatait a konfigurációhoz, és módosítsa a betanítási folyamatot szabályozó beállításokat.
1. A kísérlet beküldése a modell finomhangolására. A kísérlet elküldése után a folyamat különböző gépi tanulási algoritmusokon és hiperparaméter-beállításokon keresztül megismétli a megadott megkötéseket. A pontosság mérőszámának optimalizálásával kiválasztja a legjobban illeszkedő modellt.

### <a name="define-training-settings"></a>Képzési beállítások megadása

A kísérlet paraméter és a modell beállításainak megadása a betanításhoz. Tekintse meg a [Beállítások](how-to-configure-auto-train.md)teljes listáját. Ha a kísérletet ezekkel az alapértelmezett beállításokkal küldi el, a rendszer körülbelül 5-20 percig tart, de ha rövidebb futási időt szeretne, csökkentse a `experiment_timeout_hours` paramétert.

|Tulajdonság| Az oktatóanyagban szereplő érték |Description|
|----|----|---|
|**iteration_timeout_minutes**|10|Az egyes iterációk időkorlátja percben kifejezve. Növelje ezt az értéket olyan nagyobb adatkészletek esetében, amelyeknek több időre van szükségük az egyes iterációk esetében.|
|**experiment_timeout_hours**|0.3|A kísérlet megszakítása előtt az összes iteráció együttes elvégzése ennyi idő alatt elvégezhető.|
|**enable_early_stopping**|Igaz|Annak a jelzője, amely lehetővé teszi a korai megszakítást, ha a pontszám rövid távon nem javul.|
|**primary_metric**| spearman_correlation | Az optimalizálni kívánt metrika. A legjobban illeszkedő modell a metrika alapján lesz kiválasztva.|
|**featurization**| automatikus | Az **automatikus** használatával a kísérlet elődolgozhatja a bemeneti adatokat (a hiányzó adatok kezelésével, a szöveg és a numerikus szöveggé való átalakítással stb.).|
|**részletességi**| logging.INFO | A naplózási szint szabályozása.|
|**n_cross_validations**|5|Az ellenőrzési adatok megadásakor végrehajtandó, több ellenőrzési felosztások száma.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 10,
    "experiment_timeout_hours": 0.3,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

A megadott tanítási beállításokat `**kwargs` paraméterként használhatja egy `AutoMLConfig` objektumhoz. Továbbá adja meg a betanítási adatait és a modell típusát, amely `regression` ebben az esetben.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data=x_train,
                             label_column_name="totalAmount",
                             **automl_settings)
```

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozásának lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

### <a name="train-the-automatic-regression-model"></a>Az automatikus regressziós modell betanítása

Hozzon létre egy kísérlet objektumot a munkaterületen. Egy kísérlet tárolóként működik az egyes futtatásokhoz. Adja át a definiált `automl_config` objektumot a kísérletnek, és állítsa be a kimenetet, `True` hogy megtekintse az előrehaladást a Futtatás során.

A kísérlet elindítása után a kimenet a kísérlet futtatásakor élőben jelenik meg. Minden egyes iterációnál megjelenik a modell típusa, a Futtatás időtartama és a képzés pontossága. A mező a `BEST` metrikák típusától függően a legjobb futó tanítási pontszámot követi nyomon.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "Tutorial-NYCTaxi")
local_run = experiment.submit(automl_config, show_output=True)
```

```output
Running on local machine
Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
Current status: DatasetFeaturization. Beginning to featurize the dataset.
Current status: DatasetEvaluation. Gathering dataset statistics.
Current status: FeaturesGeneration. Generating features for the dataset.
Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
Current status: ModelSelection. Beginning model selection.

****************************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE: A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
****************************************************************************************************

 ITERATION   PIPELINE                                       DURATION      METRIC      BEST
         0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
         1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
         2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
         3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
         4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
         5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
         6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
         7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
         8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
         9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
        10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
        11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
        12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
        13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
        14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
        15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
        16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
        17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
        18   VotingEnsemble                                 0:00:23       0.9471    0.9471
        19   StackEnsemble                                  0:00:27       0.9463    0.9471
```

## <a name="explore-the-results"></a>Az eredmények vizsgálata

Ismerje meg az automatikus képzés eredményeit egy [Jupyter widgettel](/python/api/azureml-widgets/azureml.widgets). A widget lehetővé teszi, hogy megtekintse az egyes futtatási ismétlések gráfját és táblázatát, valamint a betanítási pontossági mérőszámokat és metaadatokat. Emellett a legördülő választóval szűrheti az elsődleges metrikától eltérő pontossági mérőszámokat is.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter widget-Futtatás részletei ](./media/tutorial-auto-train-models/automl-dash-output.png)
 ![ Jupyter widget Plot](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>A legjobb modell lekérése

Válassza ki az iterációk legjobb modelljét. A `get_output` függvény a legjobb futtatást adja vissza, az utolsó illeszkedési meghíváshoz tartozó modellt. A túlterhelések bekapcsolásával lekérheti `get_output` az összes naplózott metrika és egy adott iteráció esetében a legjobb futtatási és a beépített modellt.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>A legjobb modell pontosságának tesztelése

A legjobb modell segítségével előrejelzéseket futtathat a tesztelési adatkészleten a taxi viteldíjak előrejelzéséhez. A függvény `predict` a legjobb modellt használja, és az adatkészletből megbecsüli az y és az **utazás költségeit** `x_test` . Nyomtassa ki az első 10 előre jelzett költségadatok értékét a következőből: `y_predict` .

```python
y_test = x_test.pop("totalAmount")

y_predict = fitted_model.predict(x_test)
print(y_predict[:10])
```

Az `root mean squared error` eredmények kiszámítása. Alakítsa át a `y_test` dataframe egy listára, hogy összehasonlítsa az előre jelzett értékeket. A függvény `mean_squared_error` két tömb értéket vesz igénybe, és kiszámítja a közöttük lévő átlagos négyzetes hibát. Az eredmény négyzet gyökerének megadásával az y változóval megegyező egységekben talál **hibát.** Nagyjából azt jelzi, hogy a taxi díjszabása milyen messzire kerül a tényleges díjszabás alapján.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

A teljes `y_actual` és az adatkészletek használatával a következő kód alapján számíthatja ki az átlagos abszolút százalékos hibát (mape) `y_predict` . Ez a mérőszám az összes előre jelzett és tényleges érték közötti abszolút különbséget számítja ki, és összegzi az összes különbséget. Ezt követően a tényleges értékek összegének százalékában kifejezi az összeget.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

```output
Model MAPE:
0.14353867606052823

Model Accuracy:
0.8564613239394718
```


A két előrejelzési pontossági mérőszámból láthatja, hogy a modell elég jó, ha az adatkészlet szolgáltatásaiból, jellemzően a +-$4,00-es és körülbelül 15%-os hiba miatt megjósolja a taxi viteldíjait.

A hagyományos gépi tanulási modell fejlesztési folyamata nagy erőforrás-igényes, és jelentős tartományi ismereteket és időigényes befektetéseket igényel a több tucat modell eredményeinek összehasonlításához. Az automatizált gépi tanulás nagyszerű módja annak, hogy gyorsan tesztelje a forgatókönyv számos különböző modelljét.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ne hajtsa végre ezt a szakaszt, ha más Azure Machine Learning oktatóanyagok futtatását tervezi.

### <a name="stop-the-compute-instance"></a>A számítási példány leállítása

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Mindent törölni

Ha nem tervezi a létrehozott erőforrások használatát, törölje őket, így nem számítunk fel díjat.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.
1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Írja be az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet.

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az automatizált gépi tanulási oktatóanyagban a következő feladatokat hajtotta végre:

> [!div class="checklist"]
> * Egy kísérlethez konfigurált egy munkaterületet és egy előkészített adatkészletet.
> * Automatikus regressziós modell használatával, helyi egyéni paraméterekkel.
> * A betanítási eredmények megismerése és felülvizsgálata.

[A modell üzembe helyezése](tutorial-deploy-models-with-aml.md) Azure Machine Learningsal.
