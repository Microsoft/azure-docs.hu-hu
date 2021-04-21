---
title: TensorFlow-modell betanítás és üzembe helyezése
titleSuffix: Azure Machine Learning
description: Megtudhatja Azure Machine Learning hogyan skálázható fel horizontálisan egy TensorFlow-betanítási feladat rugalmas felhőalapú számítási erőforrásokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 0eac999fa53679ef67c2a322bd8cc7841197d493
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819141"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>TensorFlow-modellek nagy léptékű betanítása Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan futtathat [TensorFlow-betanítási](https://www.tensorflow.org/overview) szkripteket nagy méretekben a Azure Machine Learning.

Ez a példa egy TensorFlow-modellt mutat be és regisztrál a kézzel írt számjegyek mély neurális hálózat (DNN) használatával való besorolásához.

Akár az alapból fejleszt TensorFlow-modellt, akár egy meglévő [](how-to-deploy-existing-model.md) modellt a felhőbe, a Azure Machine Learning használatával horizontálisan felskálázni tudja a nyílt forráskódú betanítási feladatokat az éles környezetben használt modellek fejlesztésére, üzembe helyezésére, verziószámozatára és figyelére.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot az alábbi környezetek egyikében:

 - Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

     - Az [Oktatóanyag: Környezet](tutorial-1st-experiment-sdk-setup.md) és munkaterület beállítása lépésekkel hozzon létre egy dedikált jegyzetfüzet-kiszolgálót, amely előre be van töltve az SDK-val és a mintaadattárba.
    - A jegyzetfüzet-kiszolgáló mintái mélytanulási mappájában keresse meg a befejezett és kibontott jegyzetfüzetet a következő könyvtárban: **how-to-use-azureml > ml-frameworks > tensorflow > train-hyperparameter-tune-deploy-with-tensorflow** mappa. 
 
 - Saját Jupyter Notebook kiszolgálója

    - [Telepítse az Azure Machine Learning SDK-t](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Hozzon létre egy munkaterület-konfigurációs fájlt.](how-to-configure-environment.md#workspace)
    - [A minta szkriptfájlok letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` És `utils.py`
     
    Az útmutató teljes Jupyter Notebook [a](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) GitHub-minták oldalán is megtalálja. A jegyzetfüzet bővített szakaszokat tartalmaz, amelyek az intelligens hiperparaméter-finomhangolást, a modell üzembe helyezését és a jegyzetfüzet-widgeteket ismertetik.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz beállítja a betanítás kísérletét a szükséges Python-csomagok betöltésével, egy munkaterület inicializálása, a számítási cél létrehozása és a betanítás környezetének definiálása által.

### <a name="import-packages"></a>Csomagok importálása

Először importálja a szükséges Python-kódtárakat.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine Learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való munkához. A Python SDK-ban objektum létrehozásával férhet hozzá a [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) munkaterület-összetevőkhez.

Hozzon létre egy munkaterület-objektumot az előfeltételek szakaszban létrehozott `config.json` [fájlból.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Fájladatkészlet létrehozása

Egy `FileDataset` objektum egy vagy több fájlra hivatkozik a munkaterület adattárában vagy a nyilvános URL-címeken. A fájlok bármilyen formátumúak, és a osztály lehetővé teszi a fájlok letöltését vagy a számításhoz való csatlakoztatását. Egy létrehozásával `FileDataset` létrehoz egy hivatkozást az adatforrás helyére. Ha bármilyen átalakítást alkalmazott az adatkészletre, azok is az adatkészletben lesznek tárolva. Az adatok a meglévő helyen maradnak, így nem kell további tárolási költségeket fizetni. További [információért tekintse](./how-to-create-register-datasets.md) meg a csomag `Dataset` útmutatóját.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

A metódussal regisztrálhatja az adatkészletet a munkaterületen, így megoszthatja őket másokkal, újra felhasználhatja őket különböző kísérletekben, és név szerint hivatkozhat rá a betanítási `register()` szkriptben.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a TensorFlow-feladat számára a futtatáshoz. Ebben a példában egy GPU-kompatibilis számítási Azure Machine Learning hozunk létre.

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

A számítási célokkal kapcsolatos további információkért tekintse meg [a mi az a számítási cél.](concept-compute-target.md)

### <a name="define-your-environment"></a>A környezet meghatározása

A betanító [szkript](concept-environments.md) függőségeit beágyazó Azure ML-környezet meghatározásához definiálhat egy egyéni környezetet, vagy használhat azure ML-hez összeszámolt környezetet.

#### <a name="use-a-curated-environment"></a>Összecsomózott környezet használata
Az Azure ML előre összeállított környezeteket biztosít, ha nem szeretné meghatározni a saját környezetét. Az Azure ML számos, a TensorFlow-hoz készült CPU- és GPU-válogatott környezettel rendelkezik, amelyek a TensorFlow különböző verzióinak különböziknek. További információ: [.](resource-curated-environments.md)

Ha egy össze összecsatolandó környezetet szeretne használni, ehelyett futtassa a következő parancsot:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

A összecsomagzott környezetben található csomagok megtekintése érdekében kiírhatja a Conda-függőségeket a lemezre:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Győződjön meg arról, hogy a összeírt környezet tartalmazza a betanítás szkriptjéhez szükséges összes függőséget. Ha nem, módosítania kell a környezetet, hogy tartalmazza a hiányzó függőségeket. Vegye figyelembe, hogy a környezet módosításakor új nevet kell adni neki, mivel az "AzureML" előtag összerendelt környezetekhez van fenntartva. Ha módosította a Conda-függőségek YAML-fájlját, létrehozhat egy új környezetet a fájlból egy új névvel, például:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Ha ehelyett közvetlenül módosította a körülértelt környezeti objektumot, klónozhatja ezt a környezetet egy új névvel:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Egyéni környezet létrehozása

Saját Azure ML-környezetet is létrehozhat, amely beágyazi a betanító szkript függőségeit.

Először határozza meg a Conda-függőségeket egy YAML-fájlban; Ebben a példában a fájl neve `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Hozzon létre egy Azure ML-környezetet ebből a Conda-környezet specifikációból. A környezet futásidőben egy Docker-tárolóba lesz csomagolva.

Ha nincs megadva alapként megadott rendszerkép, az Azure ML alapértelmezés szerint egy CPU-rendszerképet használ `azureml.core.environment.DEFAULT_CPU_IMAGE` alapként. Mivel ez a példa egy GPU-fürtön futtat betanításokat, meg kell adnia egy alap GPU-rendszerképet, amely rendelkezik a szükséges GPU-illesztőkkel és -függőségekkel. Az Azure ML az Microsoft Container Registry (MCR) szolgáltatásban közzétett alapként szolgáló rendszerképeket tart fenn, amelyek használhatók. További információért tekintse meg az [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub-adattárat.

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Igény szerint az összes függőséget rögzítheti közvetlenül egy egyéni Docker-rendszerképben vagy Docker-fájlban, és ebből hozhatja létre a környezetet. További információ: [Betanítás egyéni rendszerképpel.](how-to-train-with-custom-image.md)

A környezetek létrehozásával és használatával kapcsolatos további információkért lásd: Szoftverkörnyezetek létrehozása és használata [a Azure Machine Learning.](how-to-use-environments.md)

## <a name="configure-and-submit-your-training-run"></a>A betanítás futtatásának konfigurálása és elküldése

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig létrehozása

Hozzon létre [egy ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) objektumot a betanítási feladat konfigurációs részleteinek megadásához, beleértve a betanítási szkriptet, a használni kívánt környezetet és a futtatni kívánt számítási célt. A betanítási szkript minden argumentuma a parancssoron keresztül lesz átadva, ha a paraméterben meg van `arguments` adva.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning a teljes forráskönyvtár másolása által futtatja a betanító szkripteket. Ha bizalmas adatokat nem szeretne feltölteni, használjon [.ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) fájlt, vagy ne foglalja bele őket a forráskönyvtárba. Ehelyett egy Azure ML-adatkészlet használatával [férhet hozzá az adataihoz.](how-to-train-with-datasets.md)

A feladatok ScriptRunConfig használatával való konfigurálásával kapcsolatos további információkért lásd: Betanítás futtatás [konfigurálása és elküldése.](how-to-set-up-training-targets.md)

> [!WARNING]
> Ha korábban a TensorFlow becslére használta a TensorFlow betanítási feladatok konfigurálását, vegye figyelembe, hogy a becslők elavultak az 1.19.0 SDK-kiadás óta. Az Azure ML SDK >= 1.15.0 esetén a ScriptRunConfig a betanítás ajánlott módja, beleértve a mély tanulási keretrendszereket használó feladatokat is. A migrálással kapcsolatos gyakori kérdésekért tekintse meg [a Estimator to ScriptRunConfig áttelepítési útmutatót.](how-to-migrate-from-estimators-to-scriptrunconfig.md)

### <a name="submit-a-run"></a>Futtatás elküldése

A [Futtatás objektum](/python/api/azureml-core/azureml.core.run%28class%29) biztosítja a felületet a futtatás előzményeihez a feladat futása közben és befejezése után.

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Mi történik a futtatás végrehajtása során?
A futtatás végrehajtása során a következő szakaszokon megy keresztül:

- **Előkészítés:** A docker-rendszerkép a meghatározott környezetnek megfelelően jön létre. A rendszerképet feltölti a rendszer a munkaterület tárolójegyzékbe, és gyorsítótárazza későbbi futtatásra. A naplók a futtatás előzményeibe is streamelve megtekinthetők a folyamat figyelése érdekében. Ha ehelyett egy összeértett környezetet ad meg, a rendszer az adott környezethez háttérként gyorsítótárazott rendszerképet használja.

- **Skálázás:** A fürt akkor kísérl meg horizontális felskálát végezni, Batch AI fürt több csomópontot igényel a futtatás végrehajtásához, mint amennyit jelenleg elérhető.

- **Futtatva:** A szkriptmappában lévő összes szkript fel lesz töltve a számítási célra, az adattárak csatlakoztatva vagy másolhatók, és a rendszer végrehajtja `script` a parancsot. Az stdout és **a ./logs** mappa kimenetei a futtatás előzményeibe lesznek streamelve, és a futtatás figyelése érdekében használhatók.

- **Utófeldolgozás:** A rendszer átmásolja a futtatás **./outputs** mappáját a futtatás előzményeibe.

## <a name="register-or-download-a-model"></a>Modell regisztrálása vagy letöltése

A modell betanítás után regisztrálható a munkaterületen. A modellregisztrációval a modelleket a munkaterületen tárolhatja és verziószámmal egészheti ki, így leegyszerűsítheti [a modellkezelést és az üzembe helyezést.](concept-model-management-and-deployment.md) Nem kötelező: a , és paraméterek megadásával elérhetővé válik a kód `model_framework` `model_framework_version` nélkül modell üzembe `resource_configuration` helyezése. Ez lehetővé teszi a modell webszolgáltatásként való közvetlen üzembe helyezését a regisztrált modellből, és az objektum határozza meg a webszolgáltatás számítási `ResourceConfiguration` erőforrását.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

A Run objektummal letöltheti a modell helyi példányát is. A betanítási szkriptben egy TensorFlow takarékos objektum egy helyi mappában (a számítási célhelyen helyi) megőrzi `tf_mnist.py` a modellt. A Run (Futtatás) objektummal letölthet egy másolatot.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Elosztott betanítás

Azure Machine Learning támogatja a több csomópontos elosztott TensorFlow-feladatokat is, így skálázhatja a betanítási számítási feladatokat. Könnyedén futtathat elosztott TensorFlow-feladatokat, és az Azure ML kezeli a vezénylést.

Az Azure ML támogatja az elosztott TensorFlow-feladatok futtatását a Horovod és a TensorFlow beépített elosztott betanítási API-jának használatával.

### <a name="horovod"></a>Horovod
[A Horovod](https://github.com/uber/horovod) egy nyílt forráskódú, az Uber által fejlesztett elosztott betanítás csökkentő keretrendszere. Egyszerű útvonalat kínál elosztott TensorFlow-kód írására a betanításhoz.

Az elosztott betanításhoz a betanítás kódját a Horovod eszközzel kell beszerzetni. A Horovod TensorFlow-val való használatával kapcsolatos további információkért tekintse meg a Horovod dokumentációját:

A Horovod TensorFlow-val való használatával kapcsolatos további információkért tekintse meg a Horovod dokumentációját:

* [Horovod a TensorFlow-val](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod a TensorFlow Keras API-jának használatával](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Emellett győződjön meg arról, hogy a betanító környezet tartalmazza a **horovod** csomagot. Ha TensorFlow válogatott környezetet használ, a horovod már szerepel az egyik függőségként. Ha a saját környezetét használja, győződjön meg arról, hogy a horovod függőség is szerepel benne, például:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Ahhoz, hogy egy elosztott feladatot az MPI/Horovod használatával hajtson végre az Azure ML-ben, meg kell adnia egy [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) paramétert a `distributed_job_config` ScriptRunConfig konstruktor paramétere számára. Az alábbi kód egy csomópontonként egy folyamatot futtató kétcsomópontos elosztott feladatot konfigurál. Ha csomópontonként több folyamatot is szeretne futtatni (azaz ha a fürt termékváltozata több GPU-kkal rendelkezik), akkor az MpiConfiguration paramétert is meg kell adnia (az alapértelmezett érték `process_count_per_node` `1` a ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Az elosztott TensorFlow a Horovod használatával az Azure ML-ben való futtatásáról a [Distributed TensorFlow with Horovod (Elosztott TensorFlow a Horovod használatával) cikkből megtudhatja,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod)hogyan futtatja az elosztott TensorFlow-t az Azure ML-ben.

### <a name="tfdistribute"></a>tf.distribute

Ha natív elosztott [TensorFlow-t](https://www.tensorflow.org/guide/distributed_training) használ a betanítási kódban, például a TensorFlow 2.x API-ját, az elosztott feladatot az Azure ML-en keresztül is `tf.distribute.Strategy` elindíthatja. 

Erre a ScriptRunConfig konstruktor paraméterére egy [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) `distributed_job_config` paramétert kell megadni. Ha a értéket használja, adja meg a értéket a TensorflowConfiguration mezőben a betanítási feladat `tf.distribute.experimental.MultiWorkerMirroredStrategy` `worker_count` csomópontjainak számához.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

A TensorFlow-ban a környezeti `TF_CONFIG` változó több gépen való betanításhoz szükséges. Az Azure ML a betanító szkript végrehajtása előtt konfigurálja és beállítja a változót az egyes `TF_CONFIG` feldolgozók számára. A betanító `TF_CONFIG` szkriptből akkor férhet hozzá, ha a használatával `os.environ['TF_CONFIG']` kell.

Példa a fő `TF_CONFIG` feldolgozó csomóponton beállított struktúrára:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Ha a betanítási szkript a paraméterkiszolgálói stratégiát használja az elosztott betanításhoz, például az örökölt TensorFlow 1.x esetén, akkor a feladatban használni kívánt paraméterkiszolgálók számát is meg kell adnia, `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` például: .

## <a name="deploy-a-tensorflow-model"></a>TensorFlow-modell üzembe helyezése

Az üzembe helyezési útmutató tartalmaz egy szakaszt a modellek regisztrálása során, de közvetlenül is átugorhat [egy](how-to-deploy-and-where.md#choose-a-compute-target) számítási cél létrehozásához az üzembe helyezéshez, mivel már rendelkezik regisztrált modellel.

### <a name="preview-no-code-model-deployment"></a>(Előzetes verzió) Kód nélkül modell üzembe helyezése

A hagyományos üzembe helyezési útvonal helyett használhatja a TensorFlow kód nélkül üzembe helyezési funkcióját (előzetes verzió). Ha regisztrálja a modellt a fenti , és paraméterekkel, egyszerűen használhatja a statikus függvényt a `model_framework` `model_framework_version` modell üzembe `resource_configuration` `deploy()` helyezéséhez.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

A részletes [üzembe helyezés részletes](how-to-deploy-and-where.md) Azure Machine Learning részletesen.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben betanított és regisztrált egy TensorFlow-modellt, valamint az üzembe helyezési lehetőségeket. A további tudnivalókért tekintse meg ezeket a Azure Machine Learning.

* [Futtatás metrika nyomon követése a betanítás során](how-to-log-view-metrics.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Referenciaarchitektúra elosztott mélytanulási betanításhoz az Azure-ban](/azure/architecture/reference-architectures/ai/training-deep-learning)
