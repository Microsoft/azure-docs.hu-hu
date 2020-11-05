---
title: Az adatmozgatás ML-folyamatokban
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan végezheti el az adatfeldolgozást Azure Machine Learning folyamatokat, és hogyan kezelheti és helyezheti át az adatátviteli lépéseket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python, data4ml
ms.openlocfilehash: bf5bfd8c2047764f9a03889c8fdd5012dc38ab65
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359765"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Adatok áthelyezése gép tanulási folyamatok lépéseibe és azok között (Python)



Ez a cikk egy Azure Machine Learning folyamat lépései közötti adatimportálási,-átalakítási és-áthelyezési kódot tartalmaz. Az adatAzure Machine Learningi működésének áttekintését lásd: az [Azure Storage-szolgáltatásokban tárolt adathozzáférés](how-to-access-data.md). Azure Machine Learning folyamatok előnyeiről és struktúrájáról a [Mi a Azure Machine learning-folyamat?](concept-ml-pipelines.md)című témakörben olvashat.

Ez a cikk bemutatja, hogyan végezheti el a következőket:

- `Dataset`Objektumok használata már meglévő adatértékekhez
- A lépésein belüli hozzáférés adatai
- Az `Dataset` adat felosztása részhalmazokra, például betanítási és érvényesítési részhalmazokra
- Objektumok létrehozása az `PipelineData` adatok átviteléhez a következő folyamat lépéséhez
- `PipelineData`Objektumok használata bemenetként a folyamat lépéseihez
- Hozzon létre új `Dataset` objektumokat, `PipelineData` amelyeket meg szeretne őrizni

> [!TIP]
> A folyamat lépései közötti ideiglenes adatátvitelt és az adattárolást követő adatmegőrzési folyamat a nyilvános előzetes osztályokban és a szolgáltatásban is elérhető  [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?preserve-view=true&view=azure-ml-py) .  Ezek az osztályok [kísérleti](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) előzetes funkciók, és bármikor változhatnak.


## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py), vagy a [Azure Machine learning studióhoz](https://ml.azure.com/)való hozzáférés.

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy meglévőt a Python SDK-n keresztül. Importálja a `Workspace` és az `Datastore` osztályt, és töltse be az előfizetési adatokat a fájlból `config.json` a függvény használatával `from_config()` . Ez a függvény alapértelmezés szerint az aktuális könyvtárban keresi a JSON-fájlt, de megadhat egy Path paramétert is, amely a fájlra mutat `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Néhány már meglévő adattal. Ez a cikk röviden bemutatja az [Azure Blob-tárolók](../storage/blobs/storage-blobs-overview.md)használatát.

- Nem kötelező: egy meglévő gépi tanulási folyamat, például egy, a [Machine learning-folyamatok létrehozása és futtatása Azure Machine learning SDK-val](how-to-create-your-first-pipeline.md)című témakörben leírtak szerint.

## <a name="use-dataset-objects-for-pre-existing-data"></a>`Dataset`Objektumok használata már meglévő adatértékekhez 

Az adatfolyamatok adatfeldolgozásának előnyben részesített módja egy [adatkészlet](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) -objektum használata. `Dataset` az objektumok az egész munkaterületen elérhető állandó adatértékeket jelölik.

Számos módon hozhatók létre és regisztrálhatók `Dataset` objektumok. A táblázatos adatkészletek egy vagy több fájlban elérhető tagolt adatokat foglalnak magukban. A fájl adatkészletei bináris adatokat (például képeket) vagy az elemezni kívánt adatokat használják. Az objektumok létrehozásának legegyszerűbb programozási módjai a `Dataset` meglévő Blobok használata a munkaterület-tárolóban vagy a nyilvános URL-címekben:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
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

## <a name="use-pipelinedata-for-intermediate-data"></a>`PipelineData`A köztes adatkezeléshez használatos

Míg `Dataset` az objektumok állandó adatokat képviselnek, a [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) objektumok a folyamat lépéseiből származó ideiglenes adatokhoz használatosak. Mivel egy objektum élettartama `PipelineData` hosszabb, mint egyetlen folyamat, a folyamat definíciós parancsfájljában definiálja őket. Objektum létrehozásakor meg `PipelineData` kell adnia egy nevet és egy adattárolót, amelyen az adat található. Adja át az `PipelineData` objektum (oka) t a (z) `PythonScriptStep` _both_ `arguments` és az `outputs` argumentumok használatával:

```python

default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)

```

Dönthet úgy, hogy az `PipelineData` objektumot egy azonnali feltöltést biztosító hozzáférési móddal hozza létre. Ebben az esetben a létrehozásakor állítsa be a (z) és a (z) `PipelineData` `upload_mode` `"upload"` argumentumot a következőre `output_path_on_compute` :

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

> [!NOTE]
> Az előző kódrészletek a hívások formáját mutatják, és nem részei a Microsoft-mintának. A különböző argumentumokat a saját projekt értékeivel kell helyettesítenie.

> [!TIP]
> A köztes adatátviteli folyamat lépéseinek átadásával kapcsolatos továbbfejlesztett élmény a nyilvános előzetes verzióban érhető el [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Példa a használatával történő használatra `OutputFileDatasetConfig` : [két lépésből álló ml-folyamat](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)létrehozása.


### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Használat `PipelineData` egy képzési lépés kimenete
A folyamaton belül a `PythonScriptStep` program argumentumai segítségével kérheti le a rendelkezésre álló kimeneti útvonalakat. Ha ez a lépés az első, és inicializálja a kimeneti adatokat, létre kell hoznia a könyvtárat a megadott elérési úton. Ezután megírhatja, hogy milyen fájlokat szeretne foglalni a alkalmazásban `PipelineData` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Ha létrehozta `PipelineData` az `is_directory` argumentumot a értékre `True` , akkor elég lenne ahhoz, hogy csak a hívást hajtsa végre, `os.makedirs()` és az elérési út minden fájlját meg kellene írnia. További részletekért tekintse meg a [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) dokumentációját.


### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Olvasási `PipelineData` bemenetként a nem kezdeti lépésekhez

Miután a kezdeti folyamat lépése beírja az adatokat az `PipelineData` elérési útra, és ennek a kezdeti lépésnek a kimenete lesz, egy későbbi lépés bemenetként is használható:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)
pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

A `PipelineData` bemenetek értéke az előző kimenet elérési útja. 

> [!NOTE]
> Az előző kódrészletek a hívások formáját mutatják, és nem részei a Microsoft-mintának. A különböző argumentumokat a saját projekt értékeivel kell helyettesítenie.

> [!TIP]
> A köztes adatátviteli folyamat lépéseinek átadásával kapcsolatos továbbfejlesztett élmény a nyilvános előzetes verzióban érhető el [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Példa a használatával történő használatra `OutputFileDatasetConfig` : [két lépésből álló ml-folyamat](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)létrehozása.

Ha az előzőekben leírtak szerint az első lépés egyetlen fájlt írt, a következőhöz hasonló lehet: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()
with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>`PipelineData`Objektumok átalakítása s-re `Dataset`

Ha `PipelineData` a futtatási időtartamnál hosszabb ideig szeretné elérhetővé tenni a rendelkezésre állást, a `as_dataset()` függvény használatával alakítsa át `Dataset` . Ezután regisztrálhatja a-t `Dataset` , így a munkaterület első osztályú polgára lehet. Mivel az `PipelineData` objektum a folyamat futása során minden alkalommal eltérő elérési úttal rendelkezik, erősen ajánlott, hogy a rendszer egy `create_new_version` `True` objektumból hozzon `Dataset` létre egy `PipelineData` objektumot.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)

```
> [!TIP]
> A köztes adatátviteli folyamatokon kívüli adatmegőrzési élmény a nyilvános előzetes verzióban érhető el [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Példa a használatával történő használatra `OutputFileDatasetConfig` : [két lépésből álló ml-folyamat](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)létrehozása.

## <a name="next-steps"></a>Következő lépések

* [Azure Machine learning-adatkészlet létrehozása](how-to-create-register-datasets.md)
* [Gépi tanulási folyamatokat hozhat létre és futtathat Azure Machine Learning SDK-val](how-to-create-your-first-pipeline.md)