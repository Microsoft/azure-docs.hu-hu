---
title: Az adatmozgatás ML-folyamatokban
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan végezheti el az adatfeldolgozást Azure Machine Learning folyamatokat, és hogyan kezelheti és helyezheti át az adatátviteli lépéseket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: 0c4f1fbc0ed27dbf3b7347e0e4c12f1926ac1377
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566251"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Adatok áthelyezése gép tanulási folyamatok lépéseibe és azok között (Python)

Ez a cikk egy Azure Machine Learning folyamat lépései közötti adatimportálási,-átalakítási és-áthelyezési kódot tartalmaz. Az adatAzure Machine Learningi működésének áttekintését lásd: az [Azure Storage-szolgáltatásokban tárolt adathozzáférés](how-to-access-data.md). Azure Machine Learning folyamatok előnyeiről és struktúrájáról a [Mi a Azure Machine learning-folyamat?](concept-ml-pipelines.md)című témakörben olvashat.

Ez a cikk bemutatja, hogyan végezheti el a következőket:

- `Dataset`Objektumok használata már meglévő adatértékekhez
- A lépésein belüli hozzáférés adatai
- Az `Dataset` adat felosztása részhalmazokra, például betanítási és érvényesítési részhalmazokra
- Objektumok létrehozása az `OutputFileDatasetConfig` adatok átviteléhez a következő folyamat lépéséhez
- `OutputFileDatasetConfig`Objektumok használata bemenetként a folyamat lépéseihez
- Hozzon létre új `Dataset` objektumokat a `OutputFileDatasetConfig` wisƒh, hogy megmaradjon

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro), vagy a [Azure Machine learning studióhoz](https://ml.azure.com/)való hozzáférés.

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy meglévőt a Python SDK-n keresztül. Importálja a `Workspace` és az `Datastore` osztályt, és töltse be az előfizetési adatokat a fájlból `config.json` a függvény használatával `from_config()` . Ez a függvény alapértelmezés szerint az aktuális könyvtárban keresi a JSON-fájlt, de megadhat egy Path paramétert is, amely a fájlra mutat `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Néhány már meglévő adattal. Ez a cikk röviden bemutatja az [Azure Blob-tárolók](../storage/blobs/storage-blobs-overview.md)használatát.

- Nem kötelező: egy meglévő gépi tanulási folyamat, például egy, a [Machine learning-folyamatok létrehozása és futtatása Azure Machine learning SDK-val](./how-to-create-machine-learning-pipelines.md)című témakörben leírtak szerint.

## <a name="use-dataset-objects-for-pre-existing-data"></a>`Dataset`Objektumok használata már meglévő adatértékekhez 

Az adatfolyamatok adatfeldolgozásának előnyben részesített módja egy [adatkészlet](/python/api/azureml-core/azureml.core.dataset%28class%29) -objektum használata. `Dataset` az objektumok az egész munkaterületen elérhető állandó adatértékeket jelölik.

Számos módon hozhatók létre és regisztrálhatók `Dataset` objektumok. A táblázatos adatkészletek egy vagy több fájlban elérhető tagolt adatokat foglalnak magukban. A fájl adatkészletei bináris adatokat (például képeket) vagy az elemezni kívánt adatokat használják. Az objektumok létrehozásának legegyszerűbb programozási módjai a `Dataset` meglévő Blobok használata a munkaterület-tárolóban vagy a nyilvános URL-címekben:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

datastore_path = [
    DataPath(datastore, 'animals/dog/1.jpg'),
    DataPath(datastore, 'animals/dog/2.jpg'),
    DataPath(datastore, 'animals/cat/*.jpg')
]
cats_dogs_dataset = Dataset.File.from_files(path=datastore_path)
```

Az adatkészletek különböző lehetőségekkel és különböző forrásokból való létrehozásával, a regisztrálással és a Azure Machine Learning felhasználói felületen való áttekintésével, valamint az adatméretnek a számítási kapacitással való interakciójának és verziószámozásának megismerésével kapcsolatban lásd: [Azure Machine learning adatkészletek létrehozása](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Adatkészletek továbbítása a parancsfájlba

Ha át szeretné adni az adatkészlet elérési útját a parancsfájlhoz, használja az `Dataset` objektum `as_named_input()` metódusát. Átadhatja az eredményül kapott `DatasetConsumptionConfig` objektumot a parancsfájlnak argumentumként, vagy a `inputs` folyamat parancsfájljának argumentumának használatával lekérheti az adatkészletet a használatával `Run.get_context().input_datasets[]` .

Miután létrehozott egy elnevezett bemenetet, kiválaszthatja a hozzáférési módját: `as_mount()` vagy `as_download()` . Ha a szkript feldolgozza az adatkészlet összes fájlját, és a számítási erőforrás lemeze elég nagy az adatkészlet számára, a letöltési hozzáférési mód a jobb választás. A letöltési hozzáférési mód elkerüli az adatátviteli terhelést a futásidőben. Ha a parancsfájl az adatkészlet egy részhalmazára fér hozzá, vagy túl nagy a számítási feladatokhoz, használja a csatlakoztatási hozzáférési módot. További információért olvassa el a [Csatlakoztatás és letöltés](./how-to-train-with-datasets.md#mount-vs-download) című témakört.

Adatkészlet átadása a folyamat lépéseire:

1. `TabularDataset.as_named_input()` `FileDataset.as_named_input()` Objektum létrehozásához használja a (z) vagy a (nem) lehetőséget. `DatasetConsumptionConfig`
1. `as_mount()` `as_download()` Hozzáférési mód beállítása vagy használata
1. Adja át az adatkészleteket a folyamat lépéseire a `arguments` vagy a `inputs` argumentum használatával.

A következő kódrészlet a fenti lépések a konstruktorban való összekapcsolásának általános mintáját mutatja be `PythonScriptStep` : 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> Az összes argumentum (azaz,,, és) értékének a `"train_data"` `"train.py"` `cluster` `iris_dataset` saját adataival kell helyettesítenie. A fenti kódrészlet csak a hívás formáját mutatja, és nem része a Microsoft-mintának. 

Olyan metódusokat is használhat, mint `random_split()` például `take_sample()` a és a több bemenet létrehozása vagy a folyamat lépéséhez átadott adatok mennyiségének csökkentése:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>A parancsfájlban lévő adatkészletek elérése

A folyamat lépéseit tartalmazó parancsfájl elnevezett bemenetei szótárként érhetők el az `Run` objektumon belül. Kérje le az aktív `Run` objektumot a használatával `Run.get_context()` , majd kérje le az elnevezett bemenetek szótárát a használatával `input_datasets` . Ha az argumentum helyett az argumentumot adta meg, akkor a `DatasetConsumptionConfig` `arguments` `inputs` kód használatával férhet hozzá az adataihoz `ArgParser` . Mindkét módszert az alábbi kódrészlet mutatja be.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()]
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Az átadott érték lesz az adatkészlet-fájl (ok) elérési útja.

Közvetlenül is elérhető a regisztráció `Dataset` . Mivel a regisztrált adatkészletek állandók és megosztottak a munkaterületen, közvetlenül is lekérheti őket:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> Az előző kódrészletek a hívások formáját mutatják, és nem részei a Microsoft-mintának. A különböző argumentumokat a saját projekt értékeivel kell helyettesítenie.

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>`OutputFileDatasetConfig`A köztes adatkezeléshez használatos

Míg `Dataset` az objektumok csak állandó adatokat képviselnek, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) az objektum (ok) a folyamat lépései **és** az állandó kimeneti adatok ideiglenes adatkimenetéhez használható. `OutputFileDatasetConfig` támogatja az adatírást a blob Storage-ba, a fájlmegosztás, a adlsgen1 vagy a adlsgen2. Támogatja a csatlakoztatási módot és a feltöltési módot is. A csatlakoztatási módban a csatlakoztatott könyvtárba írt fájlok véglegesen tárolódnak a fájl bezárásakor. Feltöltési módban a kimeneti könyvtárba írt fájlok a feladatok végén lesznek feltöltve. Ha a feladat meghiúsul vagy meg lett szakítva, a kimeneti könyvtár nem lesz feltöltve.

 `OutputFileDatasetConfig` az objektum alapértelmezett viselkedése a munkaterület alapértelmezett adattárára való írás. Adja át az `OutputFileDatasetConfig` objektumait a `PythonScriptStep` `arguments` paraméterrel.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

Dönthet úgy, hogy a Futtatás végén feltölti az `OutputFileDatasetConfig` objektum tartalmát. Ebben az esetben használja a `as_upload()` függvényt az `OutputFileDatasetConfig` objektummal együtt, és adja meg, hogy felülírja-e a meglévő fájlokat a célhelyen. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=(blob_store, 'outputdataset')).as_upload(overwrite=False)
```

> [!NOTE]
> Az egyidejű írások `OutputFileDatasetConfig` sikertelenek lesznek. Ne kísérelje meg egyszerre használni a használatát `OutputFileDatasetConfig` . Ne ossza meg egyetlen `OutputFileDatasetConfig` többprocesszoros szituációban, például az elosztott képzések használatakor. 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Használat `OutputFileDatasetConfig` egy képzési lépés kimenete

A folyamat `PythonScriptStep` részében lekérheti az elérhető kimeneti útvonalakat a program argumentumaival. Ha ez a lépés az első, és inicializálja a kimeneti adatokat, létre kell hoznia a könyvtárat a megadott útvonalon. Ezután megírhatja, hogy milyen fájlokat szeretne foglalni a alkalmazásban `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Olvasási `OutputFileDatasetConfig` bemenetként a nem kezdeti lépésekhez

Miután a kezdeti folyamat lépése adatokat ír az `OutputFileDatasetConfig` elérési útra, és ennek a kezdeti lépésnek a kimenete lesz, egy későbbi lépéshez bemenetként is használható. 

A következő kódban: 

* `step1_output_data` azt jelzi, hogy a PythonScriptStep kimenete `step1` a ADLS Gen 2 adattárba íródik a `my_adlsgen2` feltöltési hozzáférési módban. További információ a szerepkör- [engedélyek beállításáról](how-to-access-data.md#azure-data-lake-storage-generation-2) : az adat visszaírása ADLS Gen 2 adattárba. 

* `step1`A befejezést követően a kimenet a által jelzett célhelyre íródik `step1_output_data` , majd a 2. lépés készen áll a `step1_output_data` bemenet használatára. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=(datastore, "mypath/{run-id}/{output-name}")).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input()]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Objektumok regisztrálása `OutputFileDatasetConfig` újbóli használatra

Ha a `OutputFileDatasetConfig` kísérlet időtartama alatt továbbra is elérhetővé szeretné tenni a rendelkezésre állást, regisztrálja azt a munkaterületen a kísérletek közötti megosztáshoz és újrafelhasználáshoz.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="delete-outputfiledatasetconfig-contents-when-no-longer-needed"></a>Tartalom törlése, `OutputFileDatasetConfig` Ha már nincs rá szükség

Az Azure nem törli automatikusan a-val írt köztes adatkészleteket `OutputFileDatasetConfig` . Ha el szeretné kerülni a nagy mennyiségű szükségtelen adatok tárolási díját, akkor a következők valamelyikét kell megadnia:

* Programozott módon törölheti a közbenső adatmennyiséget a folyamat futásának végén, ha már nincs rá szükség
* Használjon rövid távú tárolási házirenddel rendelkező blob Storage-t a köztes adattároláshoz (lásd: a [költségek optimalizálása az Azure Blob Storage hozzáférési szintjeinek automatizálásával](../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)) 
* A már nem szükséges adatfeldolgozások rendszeres felülvizsgálata és törlése

További információ: [Azure Machine learning költségeinek tervezése és kezelése](concept-plan-manage-cost.md).

## <a name="next-steps"></a>Következő lépések

* [Azure Machine learning-adatkészlet létrehozása](how-to-create-register-datasets.md)
* [Gépi tanulási folyamatokat hozhat létre és futtathat Azure Machine Learning SDK-val](./how-to-create-machine-learning-pipelines.md)