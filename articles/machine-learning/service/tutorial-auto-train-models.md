---
title: '#2. oktatóanyag: Az automatikus machine learning – az Azure Machine Learning szolgáltatás egy regressziós modell betanítása'
description: Ismerje meg, hogyan hozhat létre egy gépi Tanulási modellt az automatizált machine learning használata.  Az Azure Machine Learning képes automatikusan elvégezni az adatokat feldolgozását, valamint az algoritmusok és hiperparaméterek kiválasztását. A kész modell ezután az Azure Machine Learning szolgáltatással lesz üzembe helyezve.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.openlocfilehash: 0c4b9c31c4ae8a6a7a7044887c9af051966c745e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879531"
---
# <a name="tutorial-2-train-a-regression-model-with-automated-machine-learning"></a>#2. oktatóanyag: Az automatikus machine learning egy regressziós modell betanítása

Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az előző oktatóanyagban, [előkészítette a NYC-i taxik adatait regressziós modellezéshez](tutorial-data-prep.md).

Most már készen áll az Azure Machine Learning szolgáltatás a modell használatának megkezdése. Az oktatóanyag ezen része az előkészített adatokat használhatja, és automatikusan létrehozhat egy taxi diszkont árak előrejelzésére regressziós modell. A szolgáltatás automatikus gépi Tanulási funkciói segítségével, meghatározása a gépi tanulási célok és korlátozások, indítsa el az automatikus gépi tanulási folyamat és az algoritmus kiválasztása és a hiperparaméter finomhangolása történjen, majd engedélyezése. Az automatizált gépi tanulási módszer algoritmusok és hiperparaméterek számos kombinációját veszi számba, amíg meg nem találja az Ön feltételeinek legjobban megfelelő modellt.

![folyamatábra](./media/tutorial-auto-train-models/flow2.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy Python-környezet beállítása és az SDK-csomagok importálása
> * Az Azure Machine Learning szolgáltatás munkaterület konfigurálása
> * Automatikus – train egy regressziós modell
> * A modell helyileg történő futtatása egyéni paraméterekkel
> * Az eredmények vizsgálata
> * A legjobb modell regisztrálása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLfree) a virtuális gép létrehozásának megkezdése előtt.

>[!NOTE]
> Ebben a cikkben kód teszteltük az Azure Machine Learning SDK 1.0.0-s verziójának


## <a name="prerequisites"></a>Előfeltételek

> * [Futtassa az adat-előkészítési oktatóanyagot](tutorial-data-prep.md).
> * Machine learning konfigurált környezet automatikus például az Azure-jegyzetfüzetek, helyi Python-környezetet, vagy adatelemzési virtuális gépet. [A telepítő](samples-notebooks.md) automatikus gépi tanulás.

## <a name="get-the-notebook"></a>A notebook beszerzése

Az Ön kényelme érdekében ez az oktatóanyag [Jupyter-notebookként](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part2-automated-ml.ipynb) is elérhető. A `regression-part2-automated-ml.ipynb` notebook az Azure Notebooks szolgáltatásban vagy a saját Jupyter-notebookkiszolgálóján futtatható.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>Csomagok importálása
Importálja azokat a Python-csomagokat, amelyekre ebben az oktatóanyagban lesz szüksége.


```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
from azureml.train.automl.run import AutoMLRun
import time
import logging

```

## <a name="configure-workspace"></a>Munkaterület konfigurálása

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. A `Workspace` egy osztályt, amely fogadja az Azure-előfizetés és erőforrás-adatok, és létrehoz egy felhőalapú erőforrás figyeléséhez és nyomon követéséhez a modell futtatja. A `Workspace.from_config()` beolvassa az **aml_config/config.json** fájlt, és betölti a részleteket a `ws` nevű objektumba.  A `ws` a kód további részében használható ebben az oktatóanyagban.

Miután létrehozott egy munkaterület-objektumot, adja meg a kísérlet nevét, valamint hozzon létre és regisztráljon egy helyi könyvtárat a munkaterülettel. Minden Futtatás előzményeit rögzíti a megadott kísérlet keretében és a [az Azure portal](https://portal.azure.com).


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

import os

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>Adatok megismerése

Az az előző oktatóanyagban létrehozott folyamatot-objektum használatára. Nyissa meg és hajtsa végre az adatfolyamot, és tekintse át az eredményeket.


```python
import azureml.dataprep as dprep
package_saved = dprep.Package.open(".\dflow")
dflow_prepared = package_saved.dataflows[0]
dflow_prepared.get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Típus</th>
      <th>Min</th>
      <th>Max</th>
      <th>Darabszám</th>
      <th>Hiányzó száma</th>
      <th>Nem hiányzó száma</th>
      <th>Hiányzó százalék</th>
      <th>Hibák száma</th>
      <th>Üres száma</th>
      <th>0,1 % ki osztóérték</th>
      <th>1 % ki osztóérték</th>
      <th>5 %-os ki osztóérték</th>
      <th>25 %-os ki osztóérték</th>
      <th>50 %-os ki osztóérték</th>
      <th>75 %-os ki osztóérték</th>
      <th>95 %-os ki osztóérték</th>
      <th>99 %-os ki osztóérték</th>
      <th>99,9 %-os ki osztóérték</th>
      <th>középérték</th>
      <th>Szórás</th>
      <th>Variancia</th>
      <th>Döntés</th>
      <th>Értékek</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Szállító</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>Péntek</td>
      <td>Szerda</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>3.57523</td>
      <td>3</td>
      <td>9.91106</td>
      <td>15.9327</td>
      <td>19</td>
      <td>22.0225</td>
      <td>23</td>
      <td>23</td>
      <td>14.2326</td>
      <td>6.34926</td>
      <td>40.3131</td>
      <td>-0.693335</td>
      <td>-0.459336</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.32313</td>
      <td>4.92308</td>
      <td>14.2214</td>
      <td>29.5244</td>
      <td>44.6436</td>
      <td>56.3767</td>
      <td>58.9798</td>
      <td>59</td>
      <td>29.4635</td>
      <td>17.4396</td>
      <td>304.14</td>
      <td>0.00440324</td>
      <td>-1.20458</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4.99286</td>
      <td>4.91954</td>
      <td>14.6121</td>
      <td>29.9239</td>
      <td>44.5221</td>
      <td>56.6792</td>
      <td>59</td>
      <td>59</td>
      <td>29.6225</td>
      <td>17.3868</td>
      <td>302.302</td>
      <td>-0.0227466</td>
      <td>-1.19409</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Péntek</td>
      <td>Szerda</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>3.23217</td>
      <td>2.93333</td>
      <td>9.92334</td>
      <td>15.9135</td>
      <td>19</td>
      <td>22.2739</td>
      <td>23</td>
      <td>23</td>
      <td>14.1815</td>
      <td>6.45578</td>
      <td>41.677</td>
      <td>-0.691001</td>
      <td>-0.500215</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.1064</td>
      <td>5</td>
      <td>14.2051</td>
      <td>29.079</td>
      <td>44.2937</td>
      <td>56.6338</td>
      <td>58.9984</td>
      <td>59</td>
      <td>29.353</td>
      <td>17.4241</td>
      <td>303.598</td>
      <td>0.0142562</td>
      <td>-1.21531</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.03373</td>
      <td>5</td>
      <td>14.7471</td>
      <td>29.598</td>
      <td>45.3216</td>
      <td>56.1044</td>
      <td>58.9728</td>
      <td>59</td>
      <td>29.7923</td>
      <td>17.481</td>
      <td>305.585</td>
      <td>-0.0281313</td>
      <td>-1.21965</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>I</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0782</td>
      <td>-73.7365</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0763</td>
      <td>-73.9625</td>
      <td>-73.9629</td>
      <td>-73.949</td>
      <td>-73.9279</td>
      <td>-73.8667</td>
      <td>-73.8304</td>
      <td>-73.8232</td>
      <td>-73.7698</td>
      <td>-73.9139</td>
      <td>0.0487111</td>
      <td>0.00237277</td>
      <td>0.402697</td>
      <td>-0.613516</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.6329</td>
      <td>40.7131</td>
      <td>40.7116</td>
      <td>40.7214</td>
      <td>40.7581</td>
      <td>40.8051</td>
      <td>40.8489</td>
      <td>40.8676</td>
      <td>40.8777</td>
      <td>40.7652</td>
      <td>0.0483485</td>
      <td>0.00233758</td>
      <td>0.228088</td>
      <td>-0.598862</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0788</td>
      <td>-73.9856</td>
      <td>-73.9858</td>
      <td>-73.959</td>
      <td>-73.9367</td>
      <td>-73.8848</td>
      <td>-73.8155</td>
      <td>-73.7767</td>
      <td>-73.7335</td>
      <td>-73.9207</td>
      <td>0.055961</td>
      <td>0.00313163</td>
      <td>0.648649</td>
      <td>0.0229141</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.5977</td>
      <td>40.6954</td>
      <td>40.6951</td>
      <td>40.7275</td>
      <td>40.7582</td>
      <td>40.7884</td>
      <td>40.8504</td>
      <td>40.868</td>
      <td>40.8786</td>
      <td>40.7595</td>
      <td>0.0504621</td>
      <td>0.00254642</td>
      <td>0.0484179</td>
      <td>-0.0368799</td>
    </tr>
    <tr>
      <th>az utasok</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2.32979</td>
      <td>1.79978</td>
      <td>3.2392</td>
      <td>0.834099</td>
      <td>-1.11111</td>
    </tr>
    <tr>
      <th>költség</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>444</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>3.01808</td>
      <td>3.0125</td>
      <td>5.91545</td>
      <td>9.49055</td>
      <td>16.5816</td>
      <td>33.5638</td>
      <td>51.9924</td>
      <td>81.1368</td>
      <td>12.9112</td>
      <td>11.6447</td>
      <td>135.599</td>
      <td>8.6842</td>
      <td>269.818</td>
    </tr>
  </tbody>
</table>

Az adatokat a kísérlethez oszlopok hozzáadásával előkészítése `dflow_x` kell funkciók a modell létrehozásához. Megadhat `dflow_y` kell az előrejelzési költség; értéke.

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday', 'dropoff_latitude', 'dropoff_longitude','pickup_hour','pickup_longitude','pickup_latitude','passengers'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-data-into-train-and-test-sets"></a>Adatok felosztása tanítási és csoportok tesztelése

Az adatok felosztása tanítási és tesztelési készletek használatával most a `train_test_split` működni a `sklearn` könyvtár. Ez a függvény elkülöníti az adatok be az x (szolgáltatások) adatkészlet modellek tanítása és az y (előre értékek) adatkészlet teszteléséhez. A `test_size` paraméter határozza meg az adatok tesztelését lefoglalni aránya. A `random_state` paraméter az egyik Kezdőérték a véletlenszerű generátor beállítja, hogy a tanítási és tesztelési elágazást mindig determinisztikus.

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=123)
# flatten y_train to 1d array
y_train.values.flatten()
```

Most már rendelkezésre állnak a modell automatizált betanításához szükséges csomagok és adatok.

## <a name="automatically-train-a-model"></a>Automatikusan a modell tanítása

A modell automatikusan betanításához:
1. A kísérlet futtatásához-beállítások megadása
1. A modell finomhangolása a kísérlet elküldése

### <a name="define-settings-for-autogeneration-and-tuning"></a>A személyfelismerési és a hangolási beállításainak megadása

A kísérlet-paramétereket és a modellek a személyfelismerési és finomhangolási beállítások megadása. Tekintse meg a teljes listáját [beállítások](how-to-configure-auto-train.md).


|Tulajdonság| Az oktatóanyagban szereplő érték |Leírás|
|----|----|---|
|**iteration_timeout_minutes**|10|Minden egyes ismétléskor percben időkorlát|
|**iterations**|30|Iterációk száma. Az egyes iterációkban a modell az adott folyamatban található adatokkal végzi a betanítást|
|**primary_metric**| spearman_correlation | Az optimalizálni kívánt metrika.|
|**preprocess**| True (Igaz) | Igaz lehetővé teszi, hogy a bemeneti adatok az előfeldolgozási végrehajtásához kísérletezhet.|
|**Részletességi**| logging.INFO | A naplózási szint szabályozza.|
|**n_cross_validationss**|5|A keresztellenőrzés felosztásainak száma



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```


```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>Az automatikus regressziós modell betanítása

Indítsa el a kísérlet helyi futtatását. Adja át a definiált `automated_ml_config` objektumot a kísérletvászonra, és a kimenetét állítsa `true` folyamatának megtekintése a kísérlet során.


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

    Parent Run ID: AutoML_83117da4-07e3-473a-b83e-99471bfa9e09
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************
    
     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:21       0.6498    0.6498
             1   MinMaxScaler GradientBoosting                  0:00:22       0.6624    0.6624
             2   StandardScalerWrapper KNN                      0:00:18       0.7267    0.7267
             3   StandardScalerWrapper GradientBoosting         0:00:18       0.5003    0.7267
             4    Ensemble                                      0:00:38       0.6659    0.7267
    

## <a name="explore-the-results"></a>Az eredmények vizsgálata

Fedezze fel az automatikus képzési egy Jupyter widgettel, vagy a kísérlet előzmények megvizsgálásával eredményeit.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>1. lehetőség: Az eredményeinek megtekintéséhez a Jupyter widget hozzáadása

Ha Juypter jegyzetfüzet használ, használja a Jupyter notebook widget grafikon és a egy tábla összes eredmény megtekintéséhez.


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Futtatás részletei Jupyter Widget](./media/tutorial-auto-train-models/jup-widget-auto.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>2. lehetőség: Lekérése, és vizsgálja meg az összes futtatási ismétlését a Pythonban

Másik lehetőségként lekérdezni a előzményeit minden kísérlet, és minden egyes ismétléskor futtatása a önálló metrikák vizsgálata.

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

import pandas as pd
rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```




<div>
<style scoped> .dataframe pedig tbody tr th: csak a-típusú {függőleges igazítás: középre;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0.113810</td>
      <td>0.093514</td>
      <td>-0.010248</td>
      <td>0.005867</td>
      <td>0.108187</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>7.004893</td>
      <td>6.348354</td>
      <td>6.493000</td>
      <td>7.045597</td>
      <td>6.646850</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>4.834063</td>
      <td>3.503244</td>
      <td>3.321553</td>
      <td>4.349547</td>
      <td>4.389995</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0.077832</td>
      <td>0.070537</td>
      <td>0.072144</td>
      <td>0.078284</td>
      <td>0.073854</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0.053712</td>
      <td>0.038925</td>
      <td>0.036906</td>
      <td>0.048328</td>
      <td>0.048778</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0.117819</td>
      <td>0.120518</td>
      <td>0.126141</td>
      <td>0.124289</td>
      <td>0.118340</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0.177689</td>
      <td>0.163360</td>
      <td>0.168101</td>
      <td>0.178250</td>
      <td>0.168685</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0.104661</td>
      <td>0.064075</td>
      <td>-0.036158</td>
      <td>-0.004403</td>
      <td>0.096976</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>10.603744</td>
      <td>10.846632</td>
      <td>11.352731</td>
      <td>11.185972</td>
      <td>10.650593</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0.801531</td>
      <td>0.736896</td>
      <td>0.758279</td>
      <td>0.804062</td>
      <td>0.760913</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0.549825</td>
      <td>0.562435</td>
      <td>0.526702</td>
      <td>0.500302</td>
      <td>0.565857</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0.549825</td>
      <td>0.562435</td>
      <td>0.562435</td>
      <td>0.562435</td>
      <td>0.565857</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="retrieve-the-best-model"></a>A legjobb modellt beolvasása

Válassza ki a legjobb folyamat az ismétlések. A `get_output` metódust `automl_classifier` a legjobb Futtatás és illesztett modell az utolsó hívás szélességhez tartozó adja vissza. A túlterhelések vannak `get_output` , amelyek engedélyezik a legjobb Futtatás és beépíteni modell lekérheti a vonatkozó bármelyik metrika vagy egy adott iterációhoz jelentkezett be.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="register-the-model"></a>Regisztrálja a modellt

Regisztrálja a modellt az Azure Machine Learning szolgáltatás munkaterületen.


```python
description = 'Automated Machine Learning Model'
tags = None
local_run.register_model(description=description, tags=tags)
local_run.model_id # Use this id to deploy the model as a web service in Azure
```

## <a name="test-the-best-model-accuracy"></a>A legpontosabb modell tesztelése

A legjobb modellt használatával előrejelzéseket futtathat a tesztelési adathalmazon. A függvény `predict` a legjobb modellt használja, és megbecsüli a y (utazás költség) értékét a a `x_test` adatkészlet. Nyomtatás az első 10-es előre jelzett költség értékeit `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Hasonlítsa össze az előre jelzett költség értékeket a tényleges költségek értékekkel. Használja a `y_test` adathalmaz, és alakíthatja át egy listát az előre jelzett értékek összehasonlítására. A függvény `mean_absolute_error` két Pole hodnot vesz igénybe, és kiszámítja az átlagos abszolút értéke hiba közöttük. Ebben a példában egy átlagos abszolút hiba 3.5, ez azt jelenti, hogy átlagosan, a modell előrejelzi a költség, plusz vagy mínusz 3.5-ös verzióját a tényleges érték.

```python
from sklearn.metrics import mean_absolute_error

y_actual = y_test.values.flatten().tolist()
mean_absolute_error(y_actual, y_predict)
```

    [ 3.16213051 ]

Futtassa a következő kódot MAPE (átlagos abszolút százalékos hiba) használatával a teljes kiszámításához `y_actual` és `y_predict` adatkészletek. Ez a metrika kiszámítja az egyes előre jelzett, tényleges érték közötti különbségek abszolút eltérésének összegzi a különbségeket az összes és majd kifejezze az összeget az összes tényleges értékek százalékban.

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

    Model MAPE:
    0.22424976634422172

    Model Accuracy:
    0.7757502336557782

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ez az automatikus Machine learning oktatóanyagban akkor:

> [!div class="checklist"]
> * Egy munkaterületet, és a egy kísérletet az előkészített adatok konfigurálása
> * Betanított egy automatizált regressziós modell helyileg használatával egyéni paraméterekkel
> * Felderített és ellenőrzött képzési eredmények
> * A legjobb modellt regisztrálva

[A modell üzembe helyezése](tutorial-deploy-models-with-aml.md) az Azure Machine Learning.
