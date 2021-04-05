---
title: TensorFlow-modell betanítása és üzembe helyezése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy a Azure Machine Learning lehetővé teszi, hogy rugalmas felhőalapú számítási erőforrásokkal bővítse a TensorFlow-betanítási feladatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 583f588004f41fc07037e7f5e4ce75538a581c70
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518330"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>TensorFlow-modellek betanítása méretekben Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan futtathat [TensorFlow](https://www.tensorflow.org/overview) -betanítási szkripteket Azure Machine learning használatával.

Ez a példa egy TensorFlow-modellt vezet be, és regisztrálja a kézzel írt számjegyeket egy mély neurális hálózat (DNN) használatával.

Függetlenül attól, hogy a TensorFlow-modellt fejleszti az alapoktól, vagy egy [meglévő modellt](how-to-deploy-existing-model.md) hoz létre a felhőbe, a Azure Machine learning használatával kibővítheti a nyílt forráskódú képzési feladatokat az éles modellek létrehozásához, üzembe helyezéséhez, verzióhoz és figyeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:

 - Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

     - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló minták Deep learning mappájában keresse meg a befejezett és kibontott jegyzetfüzetet a következő könyvtárra való navigálással: **útmutató – használat-azureml > ml-keretrendszerek > tensorflow > Train-hiperparaméter-Tune-Deploy-with-tensorflow** mappa. 
 
 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse az Azure Machine learning SDK](/python/api/overview/azure/ml/install) -t (>= 1.15.0).
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - [A minta parancsfájl fájljainak letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` és `utils.py`
     
    Az útmutató egy befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) is megtalálhatja a GitHub-minták lapon. A jegyzetfüzet tartalmaz kibővített szakaszt az intelligens hiperparaméter hangolás, a modell üzembe helyezése és a notebook widgetek számára.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a betanítási kísérletet a szükséges Python-csomagok betöltésével, a munkaterület inicializálásával, a számítási cél létrehozásával és a képzési környezet definiálásával állítja be.

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

A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy objektum létrehozásával érheti el a munkaterület összetevőit [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) .

Hozzon létre egy munkaterület-objektumot az `config.json` [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Fájl adatkészletének létrehozása

Egy `FileDataset` objektum egy vagy több fájlra hivatkozik a munkaterület adattárában vagy a nyilvános URL-címekben. A fájlok bármilyen formátumúak lehetnek, és a osztály lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számítási feladatokhoz. A létrehozásával `FileDataset` létrehoz egy hivatkozást az adatforrás helyére. Ha az adatkészletbe átalakításokat alkalmazott, azokat az adatkészletben is tárolja a rendszer. Az adattárolók a meglévő helyükön maradnak, így nem merülnek fel extra tárolási költségek. További információért tekintse [meg a csomag útmutatóját](./how-to-create-register-datasets.md) `Dataset` .

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

A `register()` módszer használatával regisztrálja az adatkészletet a munkaterületen, hogy másokkal is megoszthatók legyenek, újra felhasználható a különböző kísérletekben, és nevük szerint a képzési szkriptben.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a TensorFlow-feladatokhoz a futtatásához. Ebben a példában hozzon létre egy GPU-kompatibilis Azure Machine Learning számítási fürtöt.

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

A számítási célokkal kapcsolatos további információkért tekintse meg a [Mi az a számítási cél](concept-compute-target.md) című cikket.

### <a name="define-your-environment"></a>A környezet meghatározása

Ha meg szeretné határozni, hogy melyik Azure ML- [környezet](concept-environments.md) tartalmazza a betanítási parancsfájl függőségeit, definiálhat egy egyéni környezetet, vagy használhat egy Azure ml-alapú kurátori környezetet.

#### <a name="use-a-curated-environment"></a>Kurátori környezet használata
Az Azure ML-ben előre elkészített, kiépített környezetek is elérhetők, ha nem szeretné saját környezetét meghatározni. Az Azure ML több PROCESSZORral és GPU-val rendelkező környezettel rendelkezik a TensorFlow különböző verzióihoz tartozó TensorFlow. További információ: [itt](resource-curated-environments.md).

Ha egy kurátori környezetet szeretne használni, a következő parancsot futtathatja inkább:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

A kurátori környezetben található csomagok megtekintéséhez a Conda-függőségeket a következő lemezre írhatja:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Győződjön meg arról, hogy a betanított környezet tartalmazza a betanítási szkript által igényelt összes függőséget. Ha nem, akkor módosítania kell a környezetet a hiányzó függőségek belefoglalásához. Vegye figyelembe, hogy ha a környezet módosítva van, meg kell adnia egy új nevet, mivel a "AzureML" előtag a kurátori környezetek számára van fenntartva. Ha módosította a Conda-függőségek YAML-fájlját, új néven létrehozhat egy új környezetet, például:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Ha ehelyett közvetlenül módosította a kurátori környezet objektumát, akkor a környezet új néven is klónozott:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Egyéni környezet létrehozása

Létrehozhatja saját Azure ML-környezetét is, amely magában foglalja a betanítási parancsfájl függőségeit.

Először határozza meg a Conda függőségeit egy YAML-fájlban; Ebben a példában a fájl neve `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Hozzon létre egy Azure ML-környezetet ebből a Conda-környezeti specifikációból. A környezet egy Docker-tárolóba lesz csomagolva futásidőben.

Alapértelmezés szerint, ha nincs megadva alaprendszerkép, az Azure ML egy CPU-rendszerképet fog használni `azureml.core.environment.DEFAULT_CPU_IMAGE` az alaprendszerképként. Mivel ez a példa egy GPU-fürtön futtatja a képzést, meg kell adnia egy GPU-alapú alapképet, amely rendelkezik a szükséges GPU-illesztővel és-függőségekkel. Az Azure ML a Microsoft Container Registry (MCR) szolgáltatásban közzétett alaplemezképek készletét tartja fenn. További információért tekintse meg az [Azure/AzureML-containers GitHub-](https://github.com/Azure/AzureML-Containers) tárházat.

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Igény szerint egyszerűen rögzítheti az összes függőségét egy egyéni Docker-rendszerképben vagy Docker, és létrehozhatja a környezetét. További információ: [a betanítás egyéni rendszerképpel](how-to-train-with-custom-image.md).

További információ a környezetek létrehozásáról és használatáról: [szoftverek környezetének létrehozása és használata Azure Machine Learningban](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>A betanítási Futtatás konfigurálása és elküldése

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig létrehozása

Hozzon létre egy [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) objektumot a betanítási feladatok konfigurációs adatainak megadásához, beleértve a betanítási parancsfájlt, a használni kívánt környezetet és a futtatáshoz szükséges számítási célt. A program a (z `arguments` ) paraméterben megadott paraméterekkel adja át a betanítási parancsfájl összes argumentumát.

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
> Azure Machine Learning a teljes forrás könyvtár másolásával futtatja a betanítási parancsfájlokat. Ha olyan bizalmas adatokkal rendelkezik, amelyeket nem szeretne felvenni, használja a [. ignore fájlt](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) , vagy ne adja meg a forrás könyvtárában. Ehelyett egy Azure ML- [adathalmaz](how-to-train-with-datasets.md)használatával férhet hozzá az adataihoz.

A feladatok ScriptRunConfig-vel való konfigurálásával kapcsolatos további információkért lásd: a [betanítási futtatások konfigurálása és elküldése](how-to-set-up-training-targets.md).

> [!WARNING]
> Ha korábban már használta a TensorFlow-kalkulátort a TensorFlow-betanítási feladatok konfigurálásához, vegye figyelembe, hogy a becslések a 1.19.0 SDK kiadása óta elavulttá vált. Az Azure ML SDK >= 1.15.0 használatával a betanítási feladatok, köztük a Deep learning-keretrendszerek használatát ajánlott beállítani a ScriptRunConfig. Gyakori áttelepítési kérdésekben tekintse [meg a ScriptRunConfig áttelepítési útmutatót a kalkulátorban](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-a-run"></a>Futtatás küldése

A [Run objektum](/python/api/azureml-core/azureml.core.run%28class%29) biztosítja a felületet a futtatási előzményekhez, miközben a feladatot futtatja, és a művelet befejeződött.

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Mi történik a Futtatás végrehajtásakor
A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Előkészítés**: a Docker-rendszerkép a definiált környezetnek megfelelően jön létre. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők. Ha helyette egy kurátori környezet van megadva, akkor a rendszer a kiválasztott gyorsítótárazott rendszerképet fogja használni.

- **Skálázás**: a fürt akkor kísérli meg a skálázást, ha a Batch AI fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: a rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és `script` végrehajtja a szolgáltatást. Az stdout és a **./logs** mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Utómunka**: a Futtatás **./outputs** mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="register-or-download-a-model"></a>Modell regisztrálása vagy letöltése

A modell kiképzése után regisztrálhatja azt a munkaterületre. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében. Nem kötelező: a paraméterek, a `model_framework` `model_framework_version` és a, a `resource_configuration` kód nélküli üzembe helyezés megadásával elérhetővé válik. Ez lehetővé teszi a modell közvetlen üzembe helyezését webszolgáltatásként a regisztrált modellből, és az `ResourceConfiguration` objektum meghatározza a webszolgáltatás számítási erőforrását.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

A modell helyi másolatát a Run objektum használatával is letöltheti. A betanítási parancsfájlban `tf_mnist.py` egy TensorFlow-megtakarító objektum megőrzi a modellt egy helyi mappába (helyi a számítási célra). A Futtatás objektum használatával letöltheti a másolatokat.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Elosztott betanítás

A Azure Machine Learning támogatja a többcsomópontos elosztott TensorFlow feladatok használatát is, így méretezhetővé teheti a betanítási számítási feladatokat. Az elosztott TensorFlow feladatok egyszerűen futtathatók, az Azure ML pedig felügyeli az Ön számára.

Az Azure ML támogatja az elosztott TensorFlow-feladatok futtatását mind a Horovod, mind a TensorFlow beépített, elosztott betanítási API-val.

### <a name="horovod"></a>Horovod
A [Horovod](https://github.com/uber/horovod) egy nyílt forráskódú, amely az Über által fejlesztett elosztott képzések összes csökkentési keretrendszerét csökkenti. Egyszerű elérési utat biztosít az elosztott TensorFlow-kódok írásához a betanításhoz.

A betanítási kódot az elosztott képzés Horovod kell kiépíteni. A Horovod és a TensorFlow használatával kapcsolatos további információkért tekintse meg a Horovod dokumentációját:

A Horovod és a TensorFlow használatával kapcsolatos további információkért tekintse meg a Horovod dokumentációját:

* [Horovod a TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod a TensorFlow kerasz API-val](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Továbbá győződjön meg arról, hogy a képzési környezet tartalmazza a **horovod** csomagot. Ha TensorFlow kurátori környezetet használ, a horovod már szerepel a függőségek egyikében is. Ha saját környezetet használ, győződjön meg róla, hogy a horovod függőség szerepel, például:

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

Elosztott feladatok az Azure ML-ben használt MPI/Horovod használatával történő végrehajtásához meg kell adnia egy [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) a `distributed_job_config` ScriptRunConfig konstruktor paramétereként. Az alábbi kód egy 2 csomópontos elosztott feladatot konfigurál, amely egy folyamatot futtat egy csomóponton. Ha egy csomóponton belül több folyamatot is futtatni kíván (például ha a fürt SKU-jának több GPU-val rendelkezik), adja meg a `process_count_per_node` paramétert a MpiConfiguration (ez az alapértelmezett érték `1` ).

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

Az elosztott TensorFlow az Azure ML-Horovod való futtatásával kapcsolatos teljes oktatóanyagért lásd: [elosztott TensorFlow és Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod).

### <a name="tfdistribute"></a>TF. Distribute

Ha [natív elosztott TensorFlow](https://www.tensorflow.org/guide/distributed_training) használ a betanítási kódban, például a TensorFlow 2. x `tf.distribute.Strategy` API-ját, akkor az elosztott FELADATOT az Azure ml-n keresztül is elindíthatja. 

Ehhez meg kell adnia egy [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) a `distributed_job_config` ScriptRunConfig konstruktor paramétereként. Ha a `tf.distribute.experimental.MultiWorkerMirroredStrategy` -t használja, adja meg a `worker_count` betanítási feladatokhoz tartozó csomópontok számának megfelelő TensorflowConfiguration.

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

A TensorFlow-ben a `TF_CONFIG` környezeti változónak több gépen történő képzésre van szüksége. Az Azure ML a `TF_CONFIG` betanítási szkript végrehajtása előtt konfigurálja és beállítja a megfelelő változót az egyes feldolgozókhoz. Ha a-t kell használnia, hozzáférhet `TF_CONFIG` a képzési parancsfájlhoz `os.environ['TF_CONFIG']` .

Példa a `TF_CONFIG` fő feldolgozó csomóponton beállított szerkezetre:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Ha a betanítási szkript a paraméter kiszolgálói stratégiát használja az elosztott képzéshez, azaz az örökölt TensorFlow 1. x esetében, meg kell adnia a feladatokban használandó paraméter-kiszolgálók számát is, például: `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>TensorFlow-modell üzembe helyezése

Az üzembe helyezési útmutató egy szakaszt tartalmaz a modellek regisztrálásához, de közvetlenül kihagyhatja a központi telepítéshez szükséges [számítási cél létrehozását](how-to-deploy-and-where.md#choose-a-compute-target) , mivel már rendelkezik regisztrált modellel.

### <a name="preview-no-code-model-deployment"></a>Előnézet Nem kód modell telepítése

A hagyományos üzembe helyezési útvonal helyett a kód nélküli üzembe helyezési funkciót (előzetes verzió) is használhatja a TensorFlow. Ha a fentiekben látható módon regisztrálja a modellt `model_framework` , `model_framework_version` és a `resource_configuration` paramétereket is használja, egyszerűen használhatja a `deploy()` statikus függvényt a modell üzembe helyezéséhez.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

A teljes körű [útmutató](how-to-deploy-and-where.md) a Azure Machine learning nagyobb részletességgel történő üzembe helyezését ismerteti.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy TensorFlow-modellt oktatott és regisztrált, és megismerte az üzembe helyezési lehetőségeket. Ezekről a cikkekről további tudnivalókat talál a Azure Machine Learningról.

* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Az Azure-ban elosztott Deep learning-képzés hivatkozási architektúrája](/azure/architecture/reference-architectures/ai/training-deep-learning)
