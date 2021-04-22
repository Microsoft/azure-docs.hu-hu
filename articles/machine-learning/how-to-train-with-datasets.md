---
title: Betanítás gépi tanulási adatkészletekkel
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan teszi elérhetővé az adatokat a helyi vagy távoli számítás számára a modell betanítása érdekében Azure Machine Learning adatkészletekkel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 0b2bb49863e07e6f06512e868ed12ecf00cc11c2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872388"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Modellek betanítása Azure Machine Learning adatkészletekkel 

Ebből a cikkből megtudhatja, hogyan használhatók Azure Machine Learning [gépi](/python/api/azureml-core/azureml.core.dataset%28class%29) tanulási modellek betanítása érdekében.  A helyi vagy távoli számítási cél adatkészletei anélkül is használhatók, hogy aggódnia kellene a kapcsolati sztringek vagy az adatútvonalak miatt. 

Azure Machine Learning adatkészletek zökkenőmentes integrációt biztosítanak az olyan Azure Machine Learning betanító funkciókkal, mint a [ScriptRunConfig,](/python/api/azureml-core/azureml.core.scriptrunconfig)a [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive)és Azure Machine Learning [folyamatok.](./how-to-create-machine-learning-pipelines.md)

Ha még nem áll készen arra, hogy az adatokat elérhetővé tegye a modell betanítására, de adatfeltárásra szeretné betölteni az adatokat a jegyzetfüzetbe, tekintse meg, hogyan tárhatja fel az adatkészlet [adatait.](how-to-create-register-datasets.md#explore-data) 

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és betanításhoz a következőre van szükség:

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a Azure Machine Learning [ingyenes vagy Azure Machine Learning](https://aka.ms/AMLFree) verzióját.

* Egy [Azure Machine Learning munkaterület.](how-to-manage-workspace.md)

* A [Azure Machine Learning pythonhoz készült](/python/api/overview/azure/ml/install) >(1.13.0), amely tartalmazza a `azureml-datasets` csomagot.

> [!Note]
> Egyes Adatkészletosztályok az [azureml-dataprep csomagtól függnek.](https://pypi.org/project/azureml-dataprep/) Linux-felhasználók esetén ezek az osztályok csak a következő disztribúciókban támogatottak: Red Hat Enterprise Linux, Ubuntu, Fedora és CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Adatkészletek használata gépi tanulási betanítás szkriptjében

Ha még nem regisztrált strukturált adatokat adatkészletként, hozzon létre egy TabularDataset adatkészletet, és használja közvetlenül a betanítási szkriptben a helyi vagy távoli kísérlethez.

Ebben a példában egy nem regisztrált [TabularDatasetet](/python/api/azureml-core/azureml.data.tabulardataset) hoz létre, és megadja szkriptar argumentumként a [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) objektumban a betanításhoz. Ha újra fel szeretné használni ezt a TabularDataset adatkészletet a munkaterületen található más kísérletekkel, tekintse meg, hogyan regisztrálhat adatkészleteket [a munkaterületen.](how-to-create-register-datasets.md#register-datasets)

### <a name="create-a-tabulardataset"></a>TabularDataset létrehozása

Az alábbi kód egy nem regisztrált TabularDataset adatokat hoz létre egy webes URL-címből.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

A TabularDataset objektumokkal betöltheti a TabularDatasetben lévő adatokat egy pandas- vagy Spark DataFrame-keretbe, így a jegyzetfüzet elhagyása nélkül dolgozhat a már ismert adat-előkészítési és -betanítási kódtárakkal.

### <a name="access-dataset-in-training-script"></a>Adatkészlet elérése a betanító szkriptben

A következő kód konfigurál egy szkript-argumentumot, amely a betanítás futtatásakor lesz megadhatja (lásd a `--input-data` következő szakaszt). Amikor a táblázatos adatkészletet argumentumértékként adja át, az Azure ML ezt az adatkészlet azonosítójára oldja fel, amellyel hozzáférhet a betanítási szkriptben található adatkészlethez (anélkül, hogy szoftveres kódot kellene írnia a szkriptben található adatkészlet nevéhez vagy azonosítójához). Ezután a metódussal betölti az adatkészletet egy pandas-adatkeretbe további adatfeltárás és előkészítés érdekében [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) a betanítás előtt.

> [!Note]
> Ha az eredeti adatforrás NaN-t, üres sztringeket vagy üres értékeket tartalmaz, az használata esetén ezek az értékek Null értékként vannak `to_pandas_dataframe()` *lecserélve.*

Ha az előkészített adatokat egy memóriabeli pandas-adatkeretből kell betöltenie egy új adatkészletbe, írja az adatokat egy helyi fájlba, például egy parquetbe, és hozzon létre egy új adatkészletet ebből a fájlból. További információ [az adatkészletek létrehozásáról.](how-to-create-register-datasets.md)

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>A betanítás futtatásának konfigurálása

A betanítás futtatásának konfigurálása és elküldése egy [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) objektum használatával.

Ez a kód egy ScriptRunConfig objektumot hoz `src` létre, amely a

* A szkriptek szkriptkönyvtára. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A betanító szkript *train_titanic.py.*
* A betanítás bemeneti adatkészlete, `titanic_ds` szkriptar argumentumként. Az Azure ML ezt az adatkészlet megfelelő azonosítójára oldja fel, amikor az a szkriptnek lesz továbbadva.
* A futtatás számítási célja.
* A futtatás környezete.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Fájlok csatlakoztatása távoli számítási célokhoz

Strukturálatlan adatok esetén hozzon létre egy [FileDataset adatkészletet,](/python/api/azureml-core/azureml.data.filedataset) és csatlakoztassa vagy töltse le az adatfájlokat, hogy elérhetővé tegye őket a távoli számítási cél számára a betanításhoz. Megtudhatja, mikor használja a [csatlakoztatást és](#mount-vs-download) a letöltést a távoli betanítás kísérleteihez. 

A következő példa: 

* Létrehoz egy bemeneti FileDataset () `mnist_ds` adatkészletet a betanítás adataihoz.
* Megadja a betanítás eredményeinek írási helyét, és az eredmények FileDataset-ként való megléptetése.
* Csatlakoztatja a bemeneti adatkészletet a számítási célhoz.

> [!Note]
> Ha egyéni Docker-alapként használt rendszerképet használ, a fuse-t a használatával kell telepítenie függőségként ahhoz, hogy az adatkészlet `apt-get install -y fuse` csatlakoztatása működjön. Megtudhatja, hogyan [hozhat létre egyéni összeállítási rendszerképet.](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)

A jegyzetfüzet-példáért lásd: Betanítás futtatása adatbemenettel [és -kimenettel.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb)

### <a name="create-a-filedataset"></a>FileDataset létrehozása

Az alábbi példa egy nem regisztrált FileDataset adatokat hoz létre `mnist_data` a webes URL-címekből. Ez a FileDataset a betanítás futtatásának bemeneti adatai.

További információ [az adatkészletek](how-to-create-register-datasets.md) más forrásokból való létrehozásáról.

```Python

from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]

mnist_ds = Dataset.File.from_files(path = web_paths)

```
### <a name="where-to-write-training-output"></a>A betanítás kimenetének írási hely

Az [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)objektummal megadhatja a betanítás eredményeinek írási helyét. 

Az OutputFileDatasetConfig objektumok a következőt teszik lehetővé: 

* Csatlakoztassa vagy töltse fel egy futtatás kimenetét a megadott felhőalapú tárolóba.
* Mentse a kimenetet FileDataset néven az alábbi támogatott tárolótípusokba:
    * Azure Blob
    * Azure-fájlmegosztás
    * Azure Data Lake Storage 1. és 2. generáció
* Nyomon követheti a betanítás futtatásai közötti adat leszúkódást.

A következő kód megadja, hogy a betanítás eredményeit FileDataset adatkészletként kell menteni az alapértelmezett `outputdataset` blob-adattár () `def_blob_store` mappájába. 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>A betanítás futtatásának konfigurálása

Javasoljuk, hogy az adatkészletet argumentumként továbbadva a konstruktor paraméterének használatával `arguments` történő `ScriptRunConfig` csatlakoztatáskor. Ezzel lekért adatútvonalat (csatlakoztatási pontot) kap a betanító szkriptben argumentumok használatával. Így ugyanazt a betanító szkriptet használhatja helyi hibakereséshez és távoli betanításhoz bármely felhőplatformon.

Az alábbi példa egy ScriptRunConfig fájlt hoz létre, amely a fájladatkészletben a használatával halad `arguments` át. A futtatás elküldése után az adatkészlet által hivatkozott adatfájlok csatlakoztatva vannak a számítási célhoz, a betanítás eredményeit pedig az alapértelmezett adattár megadott `mnist_ds` `outputdataset` mappájába menti a rendszer.

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>Egyszerű betanító szkript

A következő szkript a ScriptRunConfig parancsprogramon keresztül lesz elküldve. Beolvassa az adatkészletet bemenetként, és az alapértelmezett blob-adattár mappájába írja `mnist_ds ` `outputdataset` a `def_blob_store` fájlt.

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>Csatlakoztatás és letöltés

Bármilyen formátumú fájl csatlakoztatása vagy letöltése támogatott az Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database és Azure Database for PostgreSQL. 

Adatkészlet **csatlakoztatásakor** az adatkészlet által hivatkozott fájlokat egy könyvtárhoz (csatlakoztatási ponthoz) csatolja, és elérhetővé teszi a számítási célon. A csatlakoztatás Linux-alapú számítási feladatokhoz támogatott, beleértve a Azure Machine Learning Compute, a virtuális gépek és a HDInsight esetén. 

Amikor letölt **egy** adatkészletet, az adatkészlet által hivatkozott összes fájl letöltve lesz a számítási célhoz. A letöltés minden számítási típus esetében támogatott. 

Ha a szkript feldolgozza az adatkészlet által hivatkozott összes fájlt, és a számítási lemez megfelel a teljes adatkészletnek, a letöltés ajánlott, hogy elkerülje a tárolási szolgáltatásokból származó streamelési adatokkal kapcsolatos többletterhelést. Ha az adatméret meghaladja a számítási lemez méretét, a letöltés nem lehetséges. Ebben a forgatókönyvben a csatlakoztatást javasoljuk, mivel csak a szkript által használt adatfájlok töltődnek be a feldolgozáskor.

A következő kód a temp `dataset` könyvtárhoz csatlakoztatja a következőt: `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Adatkészletek lekért használata gépi tanulási szkriptek használatával

A regisztrált adathalmazok helyileg és távolról is elérhetők olyan számítási fürtökön, mint a Azure Machine Learning számítás. A regisztrált adatkészlet kísérletek közötti eléréséhez használja a következő kódot a munkaterület eléréséhez és a korábban elküldött futtatás során használt adatkészlet lekért létrehozásához. Alapértelmezés szerint a osztály metódusa az adatkészletnek a munkaterületen regisztrált [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) `Dataset` legújabb verzióját adja vissza.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>Forráskód elérése a betanítás során

Az Azure Blob Storage nagyobb átviteli sebességgel rendelkezik, mint az Azure-fájlmegosztások, és nagy számú, párhuzamosan elindított feladatra skálázható. Ezért javasoljuk, hogy konfigurálja a futtatásokat úgy, hogy a Blob Storage-tárolót használják a forráskódfájlok átviteléhez.

A következő példakód határozza meg a futtatási konfigurációban, hogy melyik blobadattárat használja a forráskódátvitelhez.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Jegyzetfüzet-példák

+ További példákat és fogalmakat az [adatkészlet-jegyzetfüzetek között talál.](https://aka.ms/dataset-tutorial)
+ Tekintse meg, [hogyan lehet parametizálni az adatkészleteket a gépi tanulási folyamatokban.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)

## <a name="troubleshooting"></a>Hibaelhárítás

* **Az adatkészlet inicializálása sikertelen volt: A csatlakoztatási pontra való várakozás időkorrelált:** 
  * Ha nincsenek kimenő hálózati biztonsági [](../virtual-network/network-security-groups-overview.md) csoportra vonatkozó szabályai, és a (frissítés) és annak függőségeit az adott alverzió legfrissebb verziójára használja, vagy ha futtatáskor használja, hozza létre újra a környezetet, hogy a javítással együtt a legújabb javítást tudja `azureml-sdk>=1.12.0` `azureml-dataset-runtime` használni. 
  * Ha a verziót `azureml-sdk<1.12.0` használja, frissítsen a legújabb verzióra.
  * Ha kimenő NSG-szabályok vannak, győződjön meg arról, hogy van olyan kimenő szabály, amely engedélyezi a szolgáltatáscímke összes `AzureResourceMonitor` forgalmát.

### <a name="overloaded-azurefile-storage"></a>Túlterhelt AzureFile-tároló

Ha hibaüzenetet `Unable to upload project files to working directory in AzureFile because the storage is overloaded` kap, alkalmazza a következő megkerülő megoldásokat.

Ha más számítási feladatokhoz, például adatátvitelhez használ fájlmegosztást, javasoljuk, hogy blobokat használjon, hogy a fájlmegosztás ingyenesen használható legyen futtatási feladatok beküldéséhez. A számítási feladatot két különböző munkaterület között is feloszthatja.

### <a name="passing-data-as-input"></a>Adatok átadása bemenetként

*  **TypeError: FileNotFound: Nincs** ilyen fájl vagy könyvtár: Ez a hiba akkor fordul elő, ha a fájl elérési útja nem a fájl helyének megfelelő. Meg kell győződni arról, hogy a fájlra való hivatkozás módja összhangban van azzal, ahol az adatkészletet csatlakoztatta a számítási célhoz. A determinisztikus állapot biztosítása érdekében azt javasoljuk, hogy használja az absztrakt útvonalat, amikor egy adatkészletet egy számítási célhoz rögzít. Az alábbi kódban például csatlakoztatjuk az adatkészletet a számítási cél fájlrendszerének `/tmp` gyökérkönyvtárában. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Ha nem foglalja bele a kezdő perjelet (/) a munkakönyvtár előtagjaként, például a számítási célon, hogy jelezze, hová szeretné csatlakoztatni az `/mnt/batch/.../tmp/dataset` adatkészletet.


## <a name="next-steps"></a>Következő lépések

* [Gépi tanulási modellek automatikus betanítása](how-to-configure-auto-train.md#data-source-and-format) a TabularDatasets használatával.

* [Képbesorolási modellek betanítása](https://aka.ms/filedataset-samplenotebook) FileDatasets használatával.

* [Betanítás adatkészletekkel folyamatok használatával.](./how-to-create-machine-learning-pipelines.md)
