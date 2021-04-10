---
title: 'Oktatóanyag: modell betanítása Python-ban automatizált gépi tanulással'
description: Útmutató a gépi tanulási modellek a Pythonban való betanításához Apache Spark és automatizált gépi tanulás használatával.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 89309cfe427183d594a5cc2f76332ae150d4f803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102498676"
---
# <a name="tutorial-train-a-model-in-python-with-automated-machine-learning"></a>Oktatóanyag: modell betanítása Python-ban automatizált gépi tanulással

A Azure Machine Learning egy felhőalapú környezet, amely lehetővé teszi a gépi tanulási modellek betanítását, üzembe helyezését, automatizálását, kezelését és nyomon követését. 

Ebben az oktatóanyagban a Azure Machine Learning [automatizált gépi tanulást](../../machine-learning/concept-automated-ml.md) használ egy regressziós modell létrehozásához a taxi díjszabási árának előrejelzéséhez. Ez a folyamat a legjobb modellt fogadja el az oktatási és konfigurációs beállítások elfogadásával, valamint a különböző metódusok, modellek és hiperparaméter-beállítások kombinációinak automatikus megismétlésével.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
- Az adatokat a Apache Spark és az Azure Open adatkészletek használatával töltheti le.
- Az adatátalakítás és-tisztítás Apache Spark DataFrames használatával.
- Regressziós modell betanítása automatizált gépi tanulásban.
- Modell pontosságának kiszámítása

## <a name="before-you-begin"></a>Előkészületek

- Hozzon létre egy kiszolgáló nélküli Apache Spark készletet a [kiszolgáló nélküli Apache Spark készlet](../quickstart-create-apache-spark-pool-studio.md) gyors üzembe helyezési útmutatójának követésével.
- Ha nem rendelkezik meglévő Azure Machine Learning-munkaterülettel, fejezze be a [Azure Machine learning munkaterület telepítési](../../machine-learning/tutorial-1st-experiment-sdk-setup.md) oktatóanyagát. 

## <a name="understand-regression-models"></a>A regressziós modellek ismertetése

A *regressziós modellek* előre jelezik a numerikus kimeneti értékeket a független előrejelzők alapján. A regresszió során a cél az, hogy segítsen a független prediktív változók közötti kapcsolat kialakításában azzal, hogy megbecsüli, hogy az egyik változó hogyan befolyásolja a többiet.  

### <a name="example-based-on-new-york-city-taxi-data"></a>Példa a New York-i taxi-adathalmazok alapján

Ebben a példában a Spark használatával végez némi elemzést a New York Cityből (NYC) érkező, a taxival kapcsolatos tip-adatokról. Az adatokat az [Azure Open-adatkészletek](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)segítségével érheti el. Az adatkészlet ezen részhalmaza információt tartalmaz a sárga taxis utakról, beleértve az egyes utazásokkal, a kezdő és a befejező időponttal és a hellyel kapcsolatos információkat, valamint a költségeket.

> [!IMPORTANT]
> Az adatok tárolási helyétől való kihúzása további költségekkel is jár. A következő lépésekben egy modellt fejlesztünk a New York-i taxi díjszabásának előrejelzéséhez. 

##  <a name="download-and-prepare-the-data"></a>Az adatgyűjtés letöltése és előkészítése

Ezt a következőképpen teheti meg:

1. Hozzon létre egy jegyzetfüzetet a PySpark kernel használatával. Útmutatásért lásd: [Jegyzetfüzet létrehozása](../quickstart-apache-spark-notebook.md#create-a-notebook).
   
    > [!Note]
    > A PySpark kernel miatt nem kell explicit módon létrehoznia a környezeteket. A Spark-környezet automatikusan létrejön az első kód cellájának futtatásakor.
  
2. Mivel a nyers adatmennyiség parkettás formátumú, a Spark-környezettel a fájl közvetlenül a memóriába is lehívható DataFrame. Hozzon létre egy Spark-DataFrame az adatoknak a nyílt adatkészletek API-n keresztüli beolvasásával. Itt a Spark DataFrame `schema on read` tulajdonságainak használatával következtetheti ki az adattípusokat és a sémát. 
   
    ```python
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow Spark to read from the blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # Spark read parquet; note that it won't load any data yet
    df = spark.read.parquet(wasbs_path)

    ```

3. A Spark-készlet méretétől függően előfordulhat, hogy a nyers adatmennyiség túl nagy, vagy túl sok időbe telik a működés. Ezeket az adatmennyiségeket leszűkítheti a és a szűrők használatával, például egy hónapnál kisebb értékre ```start_date``` ```end_date``` . A DataFrame szűrése után a ```describe()``` függvényt az új DataFrame is futtathatja az egyes mezők összegző statisztikáinak megtekintéséhez. 

   Az összegző statisztika alapján láthatja, hogy vannak-e szabálytalanságok az adatokban. A statisztikák például azt mutatják, hogy a minimális utazási távolság nullánál kisebb. Ezeket a szabálytalan adatpontokat ki kell szűrnie.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime< "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Hozzon létre szolgáltatásokat az adatkészletből egy adott oszlop kiválasztásával, és hozzon létre különböző időalapú szolgáltatásokat a pickup `datetime` mezőből. Szűrje ki a korábbi lépésekben azonosított kiugró értékeket, majd távolítsa el az utolsó néhány oszlopot, mert azok nem szükségesek a betanításhoz.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                   , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                   , column('puMonth').alias('month_num') \
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                   ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                           .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                   & (sampled_taxi_df.tipAmount >= 0)\
                                   & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                   & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                   & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                   & (sampled_taxi_df.rateCodeId <= 5)\
                                   & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
   Amint láthatja, ez egy új DataFrame hoz létre, amely további oszlopokkal fog szolgálni a hónap napjához, a pickup óra, a hétköznap és a teljes menetidő. 

   ![A taxi DataFrame képe.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Tesztelési és érvényesítési adatkészletek előállítása

A végső adatkészlet elvégzése után az adatokat kioszthatja a betanítási és tesztelési készletekbe a ```random_ split ``` Spark-függvény használatával. A megadott súlyok használatával a függvény véletlenszerűen felosztja az adatokat a modell betanítása és a teszteléshez használt ellenőrzési adatkészlet betanítási adatkészletbe.

```python
# Random split dataset using Spark; convert Spark to pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Ez a lépés biztosítja, hogy a kész modell tesztelésére szolgáló adatpontok ne használják a modell betanítását. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Kapcsolódás Azure Machine Learning munkaterülethez
Azure Machine Learning a munkaterület egy olyan osztály, amely elfogadja az Azure-előfizetést és az erőforrás-információkat. Létrehoz egy felhőalapú erőforrást is a modell futtatásának figyelésére és nyomon követésére. Ebben a lépésben egy munkaterület-objektumot hoz létre a meglévő Azure Machine Learning munkaterületről.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>DataFrame átalakítása Azure Machine Learning adatkészletbe
Távoli kísérlet elküldéséhez alakítsa át az adatkészletet egy Azure Machine Learning- ```TabularDatset``` példányba. A [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) táblázatos formátumban jeleníti meg az adatokat a megadott fájlok elemzésével.

A következő kód beolvassa a meglévő munkaterületet és az alapértelmezett Azure Machine Learning adattárt. Ezután átadja az adattár és a fájl helyét a Path paraméternek egy új példány létrehozásához ```TabularDataset``` . 

```python
import pandas 
from azureml.core import Dataset

# Get the Azure Machine Learning default datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into an Azure Machine Learning tabular dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![A feltöltött adatkészlet képe.](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automated-experiment"></a>Automatikus kísérlet küldése

A következő szakasz végigvezeti egy automatizált gépi tanulási kísérlet elküldésének folyamatán.

### <a name="define-training-settings"></a>Képzési beállítások megadása
1. Kísérlet elküldéséhez meg kell határoznia a kísérlet paramétert és a modell beállításait a betanításhoz. A beállítások teljes listájáért lásd: [automatizált gépi tanulási kísérletek konfigurálása a Pythonban](../../machine-learning/how-to-configure-auto-train.md).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

1. Adja át a definiált tanítási beállításokat `kwargs` paraméterként egy `AutoMLConfig` objektumnak. Mivel a Sparkot használja, meg kell adnia a Spark-környezetet is, amelyet a változó automatikusan elérhet ```sc``` . Emellett megadhatja a betanítási és a modell típusát is, amely ebben az esetben regressziós.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                                debug_log='automated_ml_errors.log',
                                training_data = dataset_training,
                                spark_context = sc,
                                model_explainability = False, 
                                label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
>Az automatizált gépi tanulás előfeldolgozási lépései az alapul szolgáló modell részévé válnak. Ezek a lépések közé tartoznak a funkciók normalizálása, a hiányzó adatkezelés és a szöveg numerikusra konvertálása. Ha az előrejelzések modelljét használja, a betanítás során alkalmazott előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

### <a name="train-the-automatic-regression-model"></a>Az automatikus regressziós modell betanítása 
Ezután létre kell hoznia egy kísérlet objektumot a Azure Machine Learning munkaterületen. Egy kísérlet tárolóként működik az egyes futtatásokhoz. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
A kísérlet befejezésekor a kimenet a befejezett iterációk részleteit adja vissza. Minden egyes iterációnál megjelenik a modell típusa, a Futtatás időtartama és a képzés pontossága. A `BEST` mező a mérőszám típusa alapján követi nyomon a legjobb futó betanítási pontszámot.

![Képernyőkép a modell kimenetéről.](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> Az automatizált gépi tanulási kísérlet elküldése után különböző iterációkat és modell-típusokat futtat. A Futtatás általában 60 – 90 percet vesz igénybe. 

### <a name="retrieve-the-best-model"></a>A legjobb modell lekérése
Az iterációk közül a legjobb modell kiválasztásához használja a ```get_output``` függvényt a legjobb Futtatás és a felszerelt modell visszaadásához. A következő kód lekéri az összes naplózott metrika vagy egy adott iteráció legjobb futtatási és beszerelt modelljét.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

### <a name="test-model-accuracy"></a>Modell pontosságának tesztelése
1. A modell pontosságának teszteléséhez a legjobb modell használatával futtasson taxi viteldíj-előrejelzéseket a tesztelési adatkészleten. A ```predict``` függvény a legjobb modellt használja, és előre jelzi a `y` (viteldíj összege) értékeit az ellenőrzési adatkészletből. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

1. A legfelső szintű Mean-Square típusú hiba a modell által előre jelzett és a megfigyelt értékek közötti különbségeket gyakran használt mérték. A `y_test` DataFrame és a modell által előre jelzett értékekkel összehasonlítva kiszámíthatja az eredmények gyökerének középértékes hibáját. 

   A függvény ```mean_squared_error``` két tömböt vesz igénybe, és kiszámítja a közöttük lévő átlagos négyzetes hibát. Ezután az eredmény négyzet gyökerét kell megadnia. Ez a metrika nagyjából azt jelzi, hogy a taxi díjszabása milyen mértékben származik a tényleges viteldíj-értékekkel.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate root-mean-square error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   A legfelső szintű Mean-Square típusú hiba jól szemlélteti, hogy a modell pontosan milyen mértékben Jósolja meg a választ. Az eredményekből láthatja, hogy a modell elég jó, ha az adatkészlethez tartozó szolgáltatások, jellemzően a $2,00-es, a taxi viteldíjait szeretné előre jelezni.

1. Futtassa az alábbi kódot az Mean-Absolute-százalék hiba kiszámításához. Ez a metrika a hiba százalékában kifejezi a pontosságot. Ezt úgy hajtja végre, hogy kiszámítja az összes előre jelzett és tényleges érték közötti abszolút különbséget, majd összesíti az összes különbséget. Ezt követően az összeg a tényleges értékek összesített értékének százalékában fejezhető ki.

   ```python
   # Calculate mean-absolute-percent error and model accuracy 
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

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   A két előrejelzési pontossági mérőszámból láthatja, hogy a modell elég jó a taxi viteldíjak előrejelzéséhez az adatkészlet funkciói alapján. 

1. A lineáris regressziós modell illesztése után meg kell határoznia, hogy a modell milyen jól illeszkedik az adattípushoz. Ehhez ki kell rajzolni a tényleges viteldíj-értékeket az előre jelzett kimenetre. Emellett kiszámítja az R-négyzetes mértéket, hogy megtudja, hogyan zárhatja be az adatok a beépített regressziós sorba.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score by using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the actual versus predicted fare amount values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amount R^2={}".format(r2))
   plt.show()

   ```
   ![Képernyőkép egy regressziós mintaterületről.](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Az eredményekből láthatja, hogy az R-négyzetes mérték a variancia 95%-ában van elkönyvelve. Ezt a tényleges mintaterület és a megfigyelt terület is ellenőrzi. Minél több variancia van a regressziós modellnél, annál közelebb esik az adatpontok a beépített regressziós vonalához.  

## <a name="register-the-model-to-azure-machine-learning"></a>A modell regisztrálása Azure Machine Learning
A legjobb modell ellenőrzése után regisztrálhatja Azure Machine Learning. Ezután letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

```python
description = 'My automated ML model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Eredmények megtekintése Azure Machine Learning
Az iterációk eredményeit úgy is elérheti, ha a Azure Machine Learning munkaterületen található kísérletre kattint. Itt további részleteket is megtudhat a Futtatás, a megkísérelt modellek és az egyéb modell-metrikák állapotáról. 

![Képernyőkép egy Azure Machine Learning munkaterületről.](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Következő lépések
- [Azure Synapse Analytics](../index.yml)
- [Oktatóanyag: gépi tanulási alkalmazás létrehozása Apache Spark MLlib és az Azure szinapszis Analytics használatával](./apache-spark-machine-learning-mllib-notebook.md)