---
title: Automatikus ML használata ML-folyamatokban
titleSuffix: Azure Machine Learning
description: A AutoMLStep lehetővé teszi az automatizált gépi tanulás használatát a folyamatokban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 02/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl
ms.openlocfilehash: 0de3c9a7cf464f38a1a12d8bc19451fb1158a5ad
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520506"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Automatizált ML használata Azure Machine Learning-folyamatokban a Pythonban


A Azure Machine Learning automatizált ML-funkciói lehetővé teszik a nagy teljesítményű modellek felderítését anélkül, hogy minden lehetséges megközelítést újra kellene implementálni. Azure Machine Learning folyamatokkal kombinálva olyan telepíthető munkafolyamatokat hozhat létre, amelyek gyorsan felfedezhetik az adataihoz legjobban illő algoritmust. Ez a cikk bemutatja, hogyan lehet hatékonyan csatlakoztatni egy adatelőkészítési lépést egy automatizált ML lépéshez. Az automatizált ML gyorsan képes felderíteni az adataihoz legjobban illő algoritmust, miközben a MLOps és-modellezési életciklus-operacionalizálási a folyamatokkal.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy Azure Machine Learning-munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).  

* Ismerje meg az Azure [automatizált gépi tanulási](concept-automated-ml.md) és [gépi tanulási folyamatait](concept-ml-pipelines.md) és az SDK-t.

## <a name="review-automated-mls-central-classes"></a>Az automatikus ML központi osztályainak áttekintése

A folyamatokban az automatikus ML-t egy `AutoMLStep` objektum jelképezi. Az `AutoMLStep` osztály a alosztálya `PipelineStep` . Az objektumok gráfja a értéket `PipelineStep` definiálja `Pipeline` .

A több alosztálya is létezik `PipelineStep` . A on kívül `AutoMLStep` Ez a cikk az `PythonScriptStep` adatok előkészítését és egy másikat is megjeleníti a modell regisztrálásához.

Az első lépés az, _Ha az adatátvitelt egy ml_ -folyamatba kívánja használni, az `Dataset` objektumokkal. Ha át szeretné helyezni az adatokat a lépések _között_ , és lehetséges az adatok kimenetének mentése a futtatásokból, az előnyben részesített mód [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) és az [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig) objektumok. A szolgáltatással való használathoz `AutoMLStep` az `PipelineData` objektumot át kell alakítani egy `PipelineOutputTabularDataset` objektumba. További információ: [bemeneti és kimeneti adatok a ml-folyamatokból](how-to-move-data-in-out-of-pipelines.md).

A `AutoMLStep` konfigurálása egy `AutoMLConfig` objektumon keresztül történik. `AutoMLConfig` a egy rugalmas osztály, amelyet az [automatikus ml-kísérletek konfigurálása a Pythonban](./how-to-configure-auto-train.md#configure-your-experiment-settings)című cikkben talál. 

Egy `Pipeline` fut az a-ben `Experiment` . A folyamat `Run` minden egyes lépéshez gyermeket tartalmaz `StepRun` . Az automatizált ML kimenetei `StepRun` a betanítási mérőszámok és a legmagasabb teljesítményű modellek.

A dolgok konkrét elvégzéséhez ez a cikk egy egyszerű folyamatot hoz létre egy besorolási feladathoz. A feladat a Titanic túlélésének előrejelzése, de az adatok vagy a feladat nem kerül megvitatásra, kivéve az átadást.

## <a name="get-started"></a>Bevezetés

### <a name="retrieve-initial-dataset"></a>Kezdeti adatkészlet beolvasása

A ML-munkafolyamatok gyakran már meglévő alapértékekkel kezdődnek. Ez jó példa a regisztrált adatkészletek használatára. Az adatkészletek a munkaterületen, a verziószámozás támogatásán keresztül láthatók, és interaktív módon is megtekinthetők. Számos módon hozhat létre és tölthet fel egy adatkészletet a [Azure Machine learning adatkészletek létrehozása](how-to-create-register-datasets.md)című cikkben leírtak szerint. Mivel a Python SDK-t fogjuk használni a folyamat létrehozásához, az SDK használatával töltse le az alapkonfigurációt, és regisztrálja azt a "titanic_ds" névvel.

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

A kód először bejelentkezik a **config.json** Azure Machine learning munkaterületre (magyarázatért lásd: munkaterület- [konfigurációs fájl létrehozása](how-to-configure-environment.md#workspace). Ha még nincs regisztrálva nevű adatkészlet `'titanic_ds'` , akkor létrejön egy. A kód letölti a CSV-adatokat a webről, felhasználja őket az a létrehozásához, `TabularDataset` majd regisztrálja az adatkészletet a munkaterületen. Végül a függvény `Dataset.get_by_name()` hozzárendeli a `Dataset` -t `titanic_ds` . 

### <a name="configure-your-storage-and-compute-target"></a>Tárolási és számítási cél konfigurálása

További erőforrások, amelyeknek a folyamatnak szüksége van a tárolóra, és általában Azure Machine Learning számítási erőforrásokra. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Az adatelőkészítés és az automatikus ML-lépés közötti köztes adatfeldolgozás a munkaterület alapértelmezett adattárában tárolható, így nem kell többet megtennie, mint `get_default_datastore()` a hívás az `Workspace` objektumon. 

Ezt követően a kód ellenőrzi, hogy már létezik-e a pénzmosás-számítási cél `'cpu-cluster'` . Ha nem, azt adjuk meg, hogy egy kis CPU-alapú számítási célt szeretnénk használni. Ha az automatizált ML mély tanulási funkcióit szeretné használni (például DNN-támogatással rendelkező szöveges featurization), akkor a GPU-ra [optimalizált virtuálisgép-méretek](../virtual-machines/sizes-gpu.md)című cikkben leírtak szerint válasszon ki egy erős GPU-támogatással rendelkező számítást. 

A kód blokkolja a cél üzembe helyezését, majd kinyomtatja az imént létrehozott számítási cél részleteit. Végül a rendszer lekéri a nevesített számítási célt a munkaterületről, és hozzárendeli a következőhöz: `compute_target` . 

### <a name="configure-the-training-run"></a>A betanítási Futtatás konfigurálása

A AutoMLStep automatikusan konfigurálja a függőségeket a feladatok beküldésekor. A futásidejű környezet egy objektum létrehozásával és konfigurálásával állítható be `RunConfiguration` . Itt beállítjuk a számítási célt.

```python
from azureml.core.runconfig import RunConfiguration

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target
```

## <a name="prepare-data-for-automated-machine-learning"></a>Az automatizált gépi tanulásra vonatkozó adatelőkészítés

### <a name="write-the-data-preparation-code"></a>Az adatelőkészítési kód írása

A Titanic alapkészlete vegyes numerikus és szöveges adatokat tartalmaz, és néhány érték hiányzik. Az automatizált gépi tanulásra való felkészüléshez az adatelőkészítési folyamat lépése a következő lesz:

- A hiányzó adat kitöltése véletlenszerű vagy "ismeretlen" értékű kategóriával
- Kategorikus érték átalakítása egész számmá
- A használni nem kívánt oszlopok eldobása
- Az adat felosztása képzési és tesztelési csoportokra
- Az átalakított adatokat a `OutputFileDatasetConfig` kimeneti elérési utakra írja

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

df.to_csv(os.path.join(args.output_path,"prepped_data.csv"))

print(f"Wrote prepped data to {args.output_path}/prepped_data.csv")
```

A fenti kódrészlet egy teljes, de minimális példa a Titanic-adatfeldolgozásra. A kódrészlet egy Jupyter "Magic paranccsal" kezdődik a kód fájlba való exportálásához. Ha nem használ Jupyter-jegyzetfüzetet, távolítsa el a sort, és manuálisan hozza létre a fájlt.

A `prepare_` fenti kódrészletben szereplő különféle függvények módosítják a bemeneti adatkészlet megfelelő oszlopát. Ezek a függvények az adatokon működnek, ha egy Panda `DataFrame` objektumba módosították. Minden esetben a hiányzó adat a reprezentatív véletlenszerű adatmennyiséggel vagy az "ismeretlen" jelzéssel ellátott kategorikus adattal van kitöltve. A szöveg-alapú kategorikus adathalmazok egész számokra vannak leképezve. A már nem szükséges oszlopok felülírása vagy eldobása nem történik meg. 

Miután a kód definiálta az adat-előkészítési függvényeket, a kód elemzi a bemeneti argumentumot, amely az az elérési út, ahová az adatokat írni szeretnénk.  (Ezek `OutputFileDatasetConfig` az értékek a következő lépésben tárgyalt objektumok alapján lesznek meghatározva.) A kód lekéri a regisztrált `'titanic_cs'` `Dataset` , átalakítja egy pandare `DataFrame` , és meghívja a különböző adat-előkészítési funkciókat. 

Mivel a a `output_path` könyvtár, a hívás a `to_csv()` fájlnevet adja meg `prepped_data.csv` .

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>Az adatelőkészítési folyamat lépésének () megírása `PythonScriptStep`

A fent ismertetett adatelőkészítési kódnak társítania kell egy, a `PythonScripStep` folyamathoz használandó objektumot. A CSV-kimenet írásának elérési útját egy objektum hozza létre `OutputFileDatasetConfig` . A korábban előkészített erőforrások, például a, `ComputeTarget` a `RunConfig` és a, a `'titanic_ds' Dataset` specifikáció végrehajtásához használatosak.

```python
from azureml.data import OutputFileDatasetConfig
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input('titanic_ds')],
    allow_reuse=True
)
```

Az `prepped_data_path` objektum olyan típusú, `OutputFileDatasetConfig` amely egy könyvtárra mutat.  Figyelje meg, hogy a paraméterben meg van adva `arguments` . Ha áttekinti az előző lépést, látni fogja, hogy az adatelőkészítési kódban az argumentum értéke annak `'--output_path'` a könyvtárnak az elérési útja, amelyen a CSV-fájl íródott. 

## <a name="train-with-automlstep"></a>Tanítás AutoMLStep

Az automatikus ML-folyamat konfigurálása lépés az `AutoMLConfig` osztálysal történik. Ez a rugalmas osztály az [automatikus ml-kísérletek konfigurálása a Pythonban](./how-to-configure-auto-train.md)című témakörben található. Az adatok bevitele és kimenete a konfiguráció egyetlen olyan eleme, amely különleges figyelmet igényel egy ML-folyamatban. A folyamatokban a bemeneti és kimeneti adatokat az `AutoMLConfig` alábbiakban részletesen tárgyaljuk. Az adatokon túl a ML-folyamatok előnye, hogy különböző számítási célokat is használhat a különböző lépésekhez. Dönthet úgy is, hogy a hatékonyabban `ComputeTarget` csak az automatikus ml-t használja. Ez olyan egyszerű, mint `RunConfiguration` az objektum paramétereinek kiosztása `AutoMLConfig` `run_configuration` .

### <a name="send-data-to-automlstep"></a>Az adatküldés `AutoMLStep`

ML-folyamatokban a bemeneti adatoknak `Dataset` objektumnak kell lenniük. A legnagyobb teljesítményű mód az, hogy a bemeneti adatokat objektumok formájában adja meg `OutputTabularDatasetConfig` . Ehhez a típushoz a (z), például a (z) és a (z), például a (z) objektumot kell létrehoznia  `read_delimited_files()` `OutputFileDatasetConfig` `prepped_data_path` `prepped_data_path` .

```python
# type(prepped_data) == OutputTabularDatasetConfig
prepped_data = prepped_data_path.read_delimited_files()
```

Egy másik lehetőség a `Dataset` munkaterületen regisztrált objektumok használata:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

A két módszer összehasonlítása:

| Módszer | Előnyök és hátrányok | 
|-|-|
|`OutputTabularDatasetConfig`| Nagyobb teljesítmény | 
|| Természetes útvonal innen: `OutputFileDatasetConfig` | 
|| A folyamat futtatása után nem őrzi meg az adattárolást |
|| [A `OutputTabularDatasetConfig` technikát bemutató jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Regisztrált `Dataset` | Alacsonyabb teljesítmény |
| | Számos módon hozható létre | 
| | Az adatmegőrzés és a munkaterület teljes egészében látható |
| | [Regisztrált `Dataset` technikát mutató jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)


### <a name="specify-automated-ml-outputs"></a>Automatikus ML kimenetek meghatározása

A kimenetei a `AutoMLStep` magasabb teljesítményű modell és a modell végső mérőszámának eredményei. Ha ezeket a kimeneteket a további folyamat lépéseiben szeretné használni, készítse elő az `OutputFileDatasetConfig` objektumokat a fogadásához.

```python
from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                            datastore=datastore,
                            pipeline_output_name='metrics_output',
                            training_output=TrainingOutput(type='Metrics'))

model_data = PipelineData(name='best_model_data',
                          datastore=datastore,
                          pipeline_output_name='model_output',
                          training_output=TrainingOutput(type='Model'))
```

A fenti kódrészlet létrehozza a két `PipelineData` objektumot a metrikák és a modell kimenete számára. Mindegyik neve a korábban lekért alapértelmezett adattárhoz van rendelve, és az adott helyhez társítva van `type` `TrainingOutput` `AutoMLStep` . Mivel ezeket az `pipeline_output_name` objektumokat hozzárendeljük `PipelineData` , azok értékei nem csupán az egyes folyamatokból, hanem a teljes folyamatból lesznek elérhetők, a "folyamat eredményeinek vizsgálata" szakaszban ismertetett módon. 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>Az automatikus ML-folyamat lépésének konfigurálása és létrehozása

A bemenetek és kimenetek meghatározása után itt az ideje, hogy létrehozza a `AutoMLConfig` és a értéket `AutoMLStep` . A konfiguráció részletei a feladattól függenek, az [automatikus ml-kísérletek konfigurálása a Pythonban](./how-to-configure-auto-train.md)című témakörben leírtak szerint. A Titanic túlélési besorolása feladathoz az alábbi kódrészlet egy egyszerű konfigurációt mutat be.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    passthru_automl_config=False,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
A kódrészlet egy gyakran használt kifejezést mutat be `AutoMLConfig` . A több folyadékot (hiperparaméter-ish) tartalmazó argumentumok külön szótárban vannak megadva, míg a kisebb valószínűséggel módosuló értékek közvetlenül a `AutoMLConfig` konstruktorban vannak megadva. Ebben az esetben az `automl_settings` adja meg a rövid futtatást: a Futtatás csak 2 iteráció vagy 15 perc elteltével áll le, attól függően, hogy melyik következik be először.

A `automl_settings` szótárt a rendszer a kwargs adja át a `AutoMLConfig` konstruktornak. A többi paraméter nem összetett:

- `task``classification`erre a példára van beállítva. A többi érvényes érték a `regression` és a `forecasting`
- `path` és `debug_log` írja le a projekt elérési útját és egy helyi fájlt, amelybe a rendszer a hibakeresési adatokat írni fogja 
- `compute_target` a korábban definiált, `compute_target` amely ebben a példában egy olcsó CPU-alapú gép. Ha a AutoML mély tanulási szolgáltatásait használja, a számítási célt GPU-alapúra szeretné módosítani
- `featurization` értékre van állítva `auto` . További részletek az automatikus ML konfigurációs dokumentum [adat Featurization](./how-to-configure-auto-train.md#data-featurization) szakaszában találhatók. 
- `label_column_name` azt jelzi, hogy melyik oszlopot érdekli az előrejelzés 
- `training_data` az `OutputTabularDatasetConfig` adatelőkészítési lépés eredményeiből származó objektumokra van beállítva 

`AutoMLStep`Maga a és a `AutoMLConfig` kimenete is a `PipelineData` mérőszámok és a modell adatainak tárolására létrehozott objektumok. 

>[!Important]
> A `enable_default_model_output` és `enable_default_metrics_output`  a értékeit `True` csak a használata esetén kell beállítania  `AutoMLStepRun` .

Ebben a példában az automatikus ML folyamat kereszt-érvényesítést hajt végre a on `training_data` . Az argumentummal megadhatja a kereszthivatkozások számát `n_cross_validations` . Ha már kiosztotta a betanítási adatait az adatelőkészítési lépések részeként, beállíthatja a `validation_data` sajátját `Dataset` .

Időnként előfordulhat, hogy az `X` adatszolgáltatások és `y` az Adatfeliratok használatában is látható. Ez a technika elavult, és `training_data` a bemenethez kell használnia. 

## <a name="register-the-model-generated-by-automated-ml"></a>Az automatizált ML által generált modell regisztrálása 

Egy egyszerű ML-folyamat utolsó lépése a létrehozott modell regisztrálása. A modellnek a munkaterület modell-Hivatalához való hozzáadásával elérhető lesz a portálon, és telepíthető. A modell regisztrálásához írjon egy másikat, `PythonScriptStep` amely a `model_data` kimenetét veszi át `AutoMLStep` .

### <a name="write-the-code-to-register-the-model"></a>A kód megírása a modell regisztrálásához

A modell regisztrálva van a-ben `Workspace` . Valószínűleg ismeri a használatát a `Workspace.from_config()` munkaterületre való bejelentkezéshez a helyi gépen, de van egy másik módja, hogy a munkaterületet egy futó ml-folyamaton belülről is beolvassa. A `Run.get_context()` lekérdezi az aktívt `Run` . Ez az `run` objektum számos fontos objektumhoz biztosít hozzáférést, beleértve az `Workspace` itt használtkat is.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>A PythonScriptStep kód írása

A modell – a regisztrálás az `PythonScriptStep` `PipelineParameter` egyik argumentumát használja. A folyamat paraméterei olyan folyamatok argumentumai, amelyek könnyen állíthatók be a futtatási beküldési időben. A deklarációt követően a rendszer normál argumentumként adja át őket. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>Automatikus ML-folyamat létrehozása és futtatása

Egy-t tartalmazó folyamat létrehozása és futtatása `AutoMLStep` nem különbözik a normál folyamattól. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

A fenti kód kombinálja az adatelőkészítést, az automatikus ML-t és a modell-regisztrálási lépéseket egy `Pipeline` objektumba. Ezután létrehoz egy `Experiment` objektumot. A `Experiment` konstruktor lekéri az elnevezett kísérletet, ha az létezik, vagy szükség esetén létrehozza azt. Beküldi a- `Pipeline` t a `Experiment` rendszerbe, és létrehoz egy `Run` objektumot, amely aszinkron módon futtatja a folyamatot. A `wait_for_completion()` függvény blokkolja, amíg a Futtatás be nem fejeződik.

### <a name="examine-pipeline-results"></a>A folyamat eredményeinek vizsgálata 

A befejezést követően lekérheti azokat `run` `PipelineData` az objektumokat, amelyek hozzá lettek rendelve `pipeline_output_name` . Letöltheti az eredményeket, és betöltheti őket további feldolgozás céljából.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

A letöltött fájlok az alkönyvtárba íródnak `azureml/{run.id}/` . A metrikai fájl JSON formátumú, és a rendszer átalakíthatja a vizsgálathoz a pandák dataframe.

Helyi feldolgozás esetén előfordulhat, hogy telepítenie kell a megfelelő csomagokat, például a pandák, a savanyúság, a AzureML SDK és így tovább. Ebben a példában valószínű, hogy az automatikus ML által talált legjobb modell a XGBoost függ.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

A fenti kódrészlet megjeleníti az Azure-adattárban lévő helyről betöltött metrikai fájlt. Azt is betöltheti a letöltött fájlból, ahogy azt a Megjegyzés is mutatja. Miután deszerializálta, és átalakította egy Panda DataFrame, megtekintheti az automatikus ML lépés minden egyes ismétlésének részletes mérőszámait.

A termékmodell deszerializálható egy olyan `Model` objektumba, amelyet következtetni, további mérőszámok elemzésére és így tovább. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

A meglévő modellek betöltésével és használatával kapcsolatos további információkért lásd: [meglévő modell használata Azure Machine Learningsal](how-to-deploy-existing-model.md).

### <a name="download-the-results-of-an-automated-ml-run"></a>Automatikus ML-Futtatás eredményeinek letöltése

Ha már követte a cikket, a rendszer létrehoz egy példányban lévő `run` objektumot. `Run`A befejezett objektumokat azonban `Workspace` egy objektumból is lekérheti `Experiment` .

A munkaterület az összes kísérlet és Futtatás teljes rekordját tartalmazza. A portál használatával megkeresheti és letöltheti a kísérletek eredményét, vagy használhat programkódot. Ha egy korábbi futtatásból szeretne hozzáférni a rekordokhoz, a Azure Machine Learning használatával megkeresheti annak a futtatásnak az AZONOSÍTÓját, amelyben érdekli. Ezzel az AZONOSÍTÓval kiválaszthatja a `run` és a konkrét módszerét `Workspace` `Experiment` .

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

A fenti kódban szereplő karakterláncokat a korábbi futtatások jellemzőire kell módosítania. A fenti kódrészlet feltételezi, hogy hozzárendelte a `ws` megfelelőt a `Workspace` normálishoz `from_config()` . A rendszer közvetlenül lekéri a érdeklődési kísérletet, majd a kód megkeresi a `Run` kamatot az érték egyeztetésével `run.id` .

Ha rendelkezik egy `Run` objektummal, letöltheti a metrikákat és a modellt. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Minden `Run` objektum olyan `StepRun` objektumokat tartalmaz, amelyek az egyes folyamatokra vonatkozó lépésekkel kapcsolatos információkat tartalmazzák. A a következő `run` `StepRun` objektumra keres rá: `AutoMLStep` . A metrikák és a modellek az alapértelmezett nevük alapján kérhetők le, amelyek akkor is elérhetők, ha nem adnak át `PipelineData` objektumokat a `outputs` paraméterének `AutoMLStep` . 

Végül a rendszer letölti a tényleges mérőszámokat és modelleket a helyi gépre, ahogy azt a fenti "a folyamat eredményeinek vizsgálata" szakaszban tárgyalták.

## <a name="next-steps"></a>Következő lépések

- Futtassa ezt a Jupyter-jegyzetfüzetet egy olyan folyamaton, amely egy regressziót használó [folyamatban lévő AUTOMATIZÁLT ml-](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) t mutat a taxi viteldíjak előrejelzéséhez
- [Automatizált ML-kísérletek létrehozása kód írása nélkül](how-to-use-automated-ml-for-ml-models.md)
- Ismerkedjen meg az [automatikus ml-t bemutató különböző Jupyter notebookokkal](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
- Olvassa el a folyamat integrálása a teljes [MLOps](./concept-model-management-and-deployment.md#automate-the-ml-lifecycle) vagy a [MLOps GitHub-tárház](https://github.com/Microsoft/MLOpspython) vizsgálatával foglalkozó részt
