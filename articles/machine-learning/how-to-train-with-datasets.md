---
title: Tanítás gépi tanulási adatkészletekkel
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan teheti elérhetővé az adatokat a helyi vagy távoli számítási feladatokhoz a Azure Machine Learning adatkészletekkel rendelkező modellek betanításához.
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
ms.openlocfilehash: 688bec24cbcd88130470634abff0688ead8005ef
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881686"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Modellek betanítása Azure Machine Learning adatkészletekkel 

Ebből a cikkből megtudhatja, hogyan dolgozhat [Azure Machine learning adatkészletekkel](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) a gépi tanulási modellek betanításához.  Az adatkészleteket a helyi vagy távoli számítási célhelyen is használhatja, és nem kell aggódnia a kapcsolatok karakterláncai vagy az adatelérési utak miatt. 

Azure Machine Learning adatkészletek zökkenőmentes integrációt biztosítanak Azure Machine Learning képzési funkciókkal, például a [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py), a [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py) és a [Azure Machine learning folyamatokkal](./how-to-create-machine-learning-pipelines.md).

Ha nem áll készen arra, hogy az adatokat a modell betanításához is elérhetővé tegye, de az adatokat a jegyzetfüzetbe szeretné betölteni az adatfeltáráshoz, olvassa el [az adatkészletben található információk megismerése című részt](how-to-create-register-datasets.md#explore-data). 

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és betanításához a következők szükségesek:

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md).

* A [Azure Machine learning SDK for Python telepítve](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0), amely tartalmazza a `azureml-datasets` csomagot.

> [!Note]
> Egyes adatkészlet-osztályok függőségei vannak a [azureml-adatelőkészítés](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) csomagon. A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak: Red Hat Enterprise Linux, Ubuntu, Fedora és CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Adatkészletek felhasználása a gépi tanulási betanítási parancsfájlokban

Ha strukturált adatokat még nem regisztrált adatkészletként, hozzon létre egy TabularDataset, és közvetlenül a helyi vagy távoli kísérlethez használja a betanítási parancsfájlban.

Ebben a példában létre kell hoznia egy nem regisztrált [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) , és meg kell adnia parancsfájl-argumentumként a ScriptRunConfig objektumban a betanításhoz. Ha a munkaterületen más kísérletekkel szeretné használni ezt a TabularDataset, tekintse meg az [adatkészletek munkaterületre való regisztrálásával](how-to-create-register-datasets.md#register-datasets)foglalkozó témakört.

### <a name="create-a-tabulardataset"></a>TabularDataset létrehozása

A következő kód létrehoz egy nem regisztrált TabularDataset egy webes URL-címről.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

A TabularDataset-objektumok lehetővé teszik a TabularDataset lévő adat betöltését egy pandák vagy Spark-DataFrame, hogy az ismerős adat-előkészítési és-betanítási könyvtárakkal is működjön, anélkül, hogy el kellene hagyni a notebookot.

### <a name="access-dataset-in-training-script"></a>Hozzáférési adatkészlet a betanítási parancsfájlban

A következő kód egy parancsfájl-argumentumot konfigurál `--input-data` , amelyet a betanítási Futtatás konfigurálásakor fog megadni (lásd a következő szakaszt). Ha a táblázatos adatkészletet argumentumként adja át, az Azure ML feloldja az adatkészlet AZONOSÍTÓját, amelyet aztán a betanítási parancsfájlban lévő adatkészlet eléréséhez használhat (anélkül, hogy parancsmagba a parancsfájlban szereplő adatkészlet nevét vagy AZONOSÍTÓját). Ezután a metódus használatával [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) tölti be az adatkészletet egy Panda dataframe, hogy a képzés előtt további adatfeltárást és előkészítést lehessen készíteni.

> [!Note]
> Ha az eredeti adatforrás NaN, üres karakterláncokat vagy üres értékeket tartalmaz, a használatakor `to_pandas_dataframe()` ezek az értékek *Null* értékként lesznek lecserélve.

Ha be kell töltenie az előkészített adatokat egy memóriából származó pandák dataframe egy új adatkészletbe, írja az adatokat egy helyi fájlba, például egy parkettára, és hozzon létre egy új adatkészletet a fájlból. További információ az [adatkészletek létrehozásáról](how-to-create-register-datasets.md).

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

### <a name="configure-the-training-run"></a>A betanítási Futtatás konfigurálása

A [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun?preserve-view=true&view=azure-ml-py) objektum a betanítási Futtatás konfigurálására és elküldésére szolgál.

Ez a kód létrehoz egy ScriptRunConfig objektumot, `src` amely megadja a

* A parancsfájlok parancsfájl-könyvtára. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A betanítási parancsfájl, *train_titanic.*
* A betanítás bemeneti adatkészlete `titanic_ds` parancsfájl-argumentumként. Az Azure ML ezt az adatkészletnek a parancsfájlba való átadásakor az adatkészlet megfelelő AZONOSÍTÓJÁRA fogja feloldani.
* A Futtatás számítási célja.
* A futtatáshoz használt környezet.

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

Ha strukturálatlan adatokkal rendelkezik, hozzon létre egy [FileDataset](/python/api/azureml-core/azureml.data.filedataset?preserve-view=true&view=azure-ml-py) , és csatlakoztassa vagy töltse le az adatfájlokat, hogy azok elérhetők legyenek a távoli számítási cél számára a képzéshez. Ismerje meg, hogy mikor kell használni a [csatlakoztatást és a letöltést](#mount-vs-download) a távoli betanítási kísérletekhez. 

A következő példa létrehoz egy FileDataset, és csatlakoztatja az adatkészletet a számítási célhoz úgy, hogy argumentumként átadja a betanítási parancsfájlnak. 

> [!Note]
> Ha egyéni Docker-alaplemezképet használ, a biztosítékot az `apt-get install -y fuse` adatkészlet-csatlakoztatás működésének függősége alapján kell telepítenie. Ismerje meg, hogyan [hozhat létre egyéni Build-rendszerképet](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>FileDataset létrehozása

A következő példa egy nem regisztrált FileDataset hoz létre a webes URL-címekről. További információ az [adathalmazok](how-to-create-register-datasets.md) más forrásokból való létrehozásáról.

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

### <a name="configure-the-training-run"></a>A betanítási Futtatás konfigurálása

Javasoljuk, hogy az adatkészletet argumentumként adja át, amikor a `arguments` konstruktor paraméterén keresztül csatlakoztatja őket `ScriptRunConfig` . Ezzel az adatelérési utat (csatlakoztatási pont) az oktatóanyagban az argumentumok segítségével érheti el. Így ugyanazt a betanítási szkriptet fogja használni a helyi hibakereséshez és a távoli oktatáshoz bármilyen felhőalapú platformon.

A következő példa egy ScriptRunConfig hoz létre, amely a FileDataset keresztül továbbítja a-t `arguments` . Miután elküldte a futtatást, az adatkészlet által hivatkozott adatfájlok a `mnist_ds` számítási célhoz lesznek csatlakoztatva.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-data-in-your-training-script"></a>Beolvassa az adatait a betanítási parancsfájlba

A következő kód bemutatja, hogyan kérheti le az adatait a parancsfájlban.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>Csatlakoztatás vs Letöltés

Az Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database és Azure Database for PostgreSQL által létrehozott adatkészletek esetében bármilyen formátumú fájlok csatlakoztatása vagy letöltése támogatott. 

Adatkészlet **csatlakoztatásakor** az adatkészlet által hivatkozott fájlokat csatolja egy könyvtárhoz (csatlakoztatási ponthoz), és elérhetővé teszi azt a számítási célra. A csatlakoztatás Linux-alapú számításokhoz, többek között Azure Machine Learning számításokhoz, virtuális gépekhez és HDInsight támogatott. 

Adatkészlet letöltésekor a rendszer az adatkészlet által hivatkozott összes fájlt **letölti** a számítási célra. A letöltés minden számítási típus esetében támogatott. 

Ha a szkript feldolgozza az adatkészlet által hivatkozott összes fájlt, és a számítási lemez elfér a teljes adatkészletben, a letöltés javasolt a tárolási szolgáltatásokból származó adatok átvitelének elkerülése érdekében. Ha az adatok mérete meghaladja a számítási lemez méretét, a letöltés nem lehetséges. Ebben a forgatókönyvben javasoljuk a csatlakoztatást, mivel csak a parancsfájl által használt adatfájlok töltődnek be a feldolgozás során.

A következő kód csatlakoztatja `dataset` a TEMP könyvtárhoz: `mounted_path`

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

## <a name="get-datasets-in-machine-learning-scripts"></a>Adatkészletek lekérése gépi tanulási parancsfájlokban

A regisztrált adatkészletek helyileg és távolról is elérhetők a számítási fürtökön, például az Azure Machine Learning számítási feladatokhoz. A regisztrált adatkészlet kísérletek közötti eléréséhez használja a következő kódot a munkaterület eléréséhez és a korábban elküldött futtatáskor használt adatkészlet lekéréséhez. Alapértelmezés szerint az [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) osztályban lévő metódus a `Dataset` munkaterülethez regisztrált adatkészlet legújabb verzióját adja vissza.

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

## <a name="access-source-code-during-training"></a>Hozzáférési forráskód a betanítás során

Az Azure Blob Storage nagyobb átviteli sebességgel rendelkezik, mint egy Azure-fájlmegosztás, és nagy mennyiségű, párhuzamosan elindított feladatra fog méretezni. Ezért javasoljuk, hogy a futtatások konfigurálásával blob Storage-t használjon a forráskód-fájlok átviteléhez.

A következő kódrészlet a futtatási konfigurációt adja meg, amely a blob-adatforgalomhoz használandó blob-adattár.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Jegyzetfüzet-példák

+ Az [adatkészlet jegyzetfüzetei](https://aka.ms/dataset-tutorial) bemutatják és kibővítik az ebben a cikkben szereplő fogalmakat.
+ Ismerje meg, hogyan [parametrize adatkészleteket az ml-folyamatokban](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="troubleshooting"></a>Hibaelhárítás

* **Az adatkészlet inicializálása nem sikerült: várakozás a csatlakoztatási pont készenléti időkorlátja**: 
  * Ha nem rendelkezik kimenő [hálózati biztonsági csoportra](../virtual-network/network-security-groups-overview.md) vonatkozó szabályokkal, és a-t használja `azureml-sdk>=1.12.0` , a frissítés `azureml-dataset-runtime` és a függőségek a legújabbak lesznek az adott alverziónál, vagy ha fut, akkor hozza létre újra a környezetet, hogy az a javítással rendelkezzen a legújabb javítással. 
  * Ha a `azureml-sdk<1.12.0` -t használja, frissítsen a legújabb verzióra.
  * Ha kimenő NSG-szabályokkal rendelkezik, győződjön meg arról, hogy van egy kimenő szabály, amely engedélyezi a szolgáltatás címkéjének minden forgalmát `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Túlterhelt AzureFile-tároló

Ha hibaüzenetet kap `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , alkalmazza a következő megkerülő megoldásokat.

Ha más számítási feladatokhoz (például adatátvitelhez) használ fájlmegosztást, a javasolt a Blobok használata, hogy a fájlmegosztás díjmentesen használható legyen a futtatások elküldéséhez. A számítási feladatok felosztása két különböző munkaterület között is lehetséges.

### <a name="passing-data-as-input"></a>Adatok továbbítása bemenetként

*  **TypeError: FileNotFound: nincs ilyen fájl vagy könyvtár**: Ez a hiba akkor fordul elő, ha a megadott elérési út nem az a fájl, ahol a fájl található. Meg kell győződnie arról, hogy a fájlra vonatkozó hivatkozás konzisztens, ha az adatkészletet a számítási célra csatlakoztatta. A determinisztikus állapotának biztosítása érdekében javasoljuk, hogy az absztrakt elérési utat használja az adatkészlet számítási célra való csatlakoztatásakor. A következő kódban például az adathalmazt csatlakoztatjuk a számítási cél fájlrendszerének gyökeréhez `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Ha nem tartalmazza a Lead Forward perjelet, a "/" előtagot kell létrehoznia a munkakönyvtárhoz, például a `/mnt/batch/.../tmp/dataset` számítási célra, hogy jelezze, hová szeretné csatlakoztatni az adatkészletet.


## <a name="next-steps"></a>További lépések

* [Gépi tanulási modellek automatikus tanítása](how-to-auto-train-remote.md) a TabularDatasets.

* [Képosztályozási modellek betanítása](https://aka.ms/filedataset-samplenotebook) a FileDatasets.

* [Adatkészletek betanítása a folyamatok használatával](./how-to-create-machine-learning-pipelines.md).