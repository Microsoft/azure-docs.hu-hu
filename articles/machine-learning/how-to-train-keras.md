---
title: Mélytanulásos Keras-modellek betanítása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan betaníthat és regisztrálhat egy TensorFlow-ban futó Mély neurális hálózati besorolási modellt a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 555ec90bbd73cee401f6f35aa04598792d2f24f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817145"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Keras-modellek nagy léptékű betanítása Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan futtathat Keras-betanító szkripteket a Azure Machine Learning.

A cikkben látható példakód bemutatja, hogyan betaníthat és regisztrálhat egy TensorFlow-háttér használatával készült Keras-besorolási modellt a Azure Machine Learning. A népszerű [kódtárat MNIST-adathalmaz](http://yann.lecun.com/exdb/mnist/) a kézzel írt számjegyek besorolására a [TensorFlow-on](https://www.tensorflow.org/overview)futó [Keras](https://keras.io) Python-kódtár használatával készült mély neurális hálózat (DNN) használatával.

A Keras egy magas szintű neurális hálózati API, amely képes más népszerű DNN-keretrendszerek futtatására a fejlesztés egyszerűsítése érdekében. A Azure Machine Learning rugalmas felhőalapú számítási erőforrások használatával gyorsan horizontálisan felskálaszthatja a betanító feladatokat. Emellett nyomon követheti a betanítás futtatásokat, verziómodelleket, modelleket helyezhet üzembe és sok más mellett.

Akár az alapból fejleszt Keras-modellt, akár egy meglévő modellt a felhőbe, az Azure Machine Learning az éles használatra kész modelleket.

> [!NOTE]
> Ha a TensorFlow-ba épített Keras API **tf.keras-t** használja, és nem a különálló Keras-csomagot, tekintse meg a [TensorFlow-modellek betanítását.](how-to-train-tensorflow.md)

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot az alábbi környezetek egyikében:

- Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

     - Az [Oktatóanyag: Környezet](tutorial-1st-experiment-sdk-setup.md) és munkaterület beállítása lépésekkel hozzon létre egy, az SDK-val és a mintaadattáraval előre feltöltött dedikált notebookkiszolgálót.
    - A notebookkiszolgáló minták mappájában keresse meg a befejezett és kibontott jegyzetfüzetet a következő könyvtárban: **how-to-use-azureml > ml-frameworks > keras > train-hyperparameter-tune-deploy-with-keras** mappa.

 - Saját Jupyter Notebook kiszolgálója

    - [Telepítse az Azure Machine Learning SDK-t](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Hozzon létre egy munkaterület-konfigurációs fájlt.](how-to-configure-environment.md#workspace)
    - [A minta szkriptfájlok letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` És `utils.py`

    Az útmutató teljes Jupyter Notebook [a](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) GitHub-minták oldalán is megtalálja. A jegyzetfüzet bővített szakaszokat tartalmaz, amelyek az intelligens hiperparaméter-finomhangolást, a modell üzembe helyezését és a jegyzetfüzet-widgeteket ismertetik.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz beállítja a betanítás kísérletét a szükséges Python-csomagok betöltésével, egy munkaterület inicializálása, a FileDataset létrehozása a bemeneti betanítás adataihoz, a számítási cél létrehozásához és a betanítési környezet meghatározásához.

### <a name="import-packages"></a>Csomagok importálása

Először importálja a szükséges Python-kódtárakat.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine Learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központosított helyet biztosít az összes létrehozott összetevővel való munkához. A Python SDK-ban objektum létrehozásával férhet hozzá a [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) munkaterület-összetevőkhez.

Hozzon létre egy munkaterület-objektumot `config.json` az előfeltételek szakaszban létrehozott [fájlból.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Fájladatkészlet létrehozása

Egy `FileDataset` objektum egy vagy több fájlra hivatkozik a munkaterület adattárában vagy a nyilvános URL-címeken. A fájlok bármilyen formátumúak, és a osztály lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számításhoz. Egy létrehozásával `FileDataset` létrehoz egy hivatkozást az adatforrás helyére. Ha bármilyen átalakítást alkalmazott az adatkészletre, azok is az adatkészletben lesznek tárolva. Az adatok a meglévő helyen maradnak, így nem kell további tárolási költségeket fizetni. További [információért tekintse](./how-to-create-register-datasets.md) meg a csomag `Dataset` útmutatóját.

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
```

### <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a betanítás feladatának futtatásához. Ebben a példában egy GPU-kompatibilis számítási Azure Machine Learning hozunk létre.

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

Definiálja a betanító [szkript](concept-environments.md) függőségeit beágyazó Azure ML-környezetet.

Először határozza meg a Conda-függőségeket egy YAML-fájlban; Ebben a példában a fájl neve `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Hozzon létre egy Azure ML-környezetet ebből a Conda-környezet specifikációból. A környezet futásidőben egy Docker-tárolóba lesz csomagolva.

Ha nincs megadva alapként megadott rendszerkép, az Azure ML alapértelmezés szerint egy CPU-rendszerképet használ `azureml.core.environment.DEFAULT_CPU_IMAGE` alapként. Mivel ez a példa EGY GPU-fürtön futtat betanításokat, meg kell adnia egy gpu-alapképet, amely rendelkezik a szükséges GPU-illesztőkkel és -függőségekkel. Az Azure ML egy használható alapként szolgáló rendszerképkészletet tart fenn az Microsoft Container Registry(MCR) szolgáltatásban, további információért tekintse meg az [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub-adattárat.

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

A környezetek létrehozásával és használatával kapcsolatos további információkért lásd: Szoftverkörnyezetek létrehozása és használata [a Azure Machine Learning.](how-to-use-environments.md)

## <a name="configure-and-submit-your-training-run"></a>A betanítás futtatásának konfigurálása és elküldése

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig létrehozása
Először szerezze be az adatokat a munkaterület adattárából a osztály `Dataset` használatával.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Hozzon létre egy ScriptRunConfig objektumot a betanítási feladat konfigurációs részleteinek megadásához, beleértve a betanítási szkriptet, a használni kívánt környezetet és a futtatni kívánt számítási célt.

A betanítási szkript minden argumentuma a parancssoron keresztül lesz átadva, ha a paraméterben meg van `arguments` adva. A FileDataset adatkészlet datasetConsumptionConfig tulajdonsága argumentumként van átkényzve a betanítási szkriptnek a `--data-folder` argumentumhoz. Az Azure ML ezt a DatasetConsumptionConfig adatokat a háttéradattár csatlakoztatási pontjára oldja fel, amely ezután elérhető a betanítási szkriptből.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

A feladatok ScriptRunConfig használatával való konfigurálásával kapcsolatos további információkért lásd: Betanítás futtatás [konfigurálása és elküldése.](how-to-set-up-training-targets.md)

> [!WARNING]
> Ha korábban a TensorFlow becslátort használt a Keras betanítási feladatok konfigurálához, vegye figyelembe, hogy a becslők elavultak az 1.19.0 SDK-kiadás óta. Az Azure ML SDK >= 1.15.0 esetén a ScriptRunConfig a betanítás ajánlott módja, beleértve a mély tanulási keretrendszereket használó feladatokat is. A migrálással kapcsolatos gyakori kérdésekért lásd: [Estimator to ScriptRunConfig áttelepítési útmutató.](how-to-migrate-from-estimators-to-scriptrunconfig.md)

### <a name="submit-your-run"></a>A futtatás elküldése

A [Futtatás objektum](/python/api/azureml-core/azureml.core.run%28class%29) biztosítja a felületet a futtatás előzményeihez a feladat futása közben és befejezése után.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Mi történik a futtatás végrehajtása során?
A futtatás végrehajtása során a következő szakaszokon halad végig:

- **Előkészítés:** A docker-rendszerkép a megadott környezetnek megfelelően jön létre. A rendszerképet feltölti a rendszer a munkaterület tárolójegyzékbe, és gyorsítótárazza későbbi futtatásra. A naplók a futtatás előzményeibe is streamelve megtekinthetők a folyamat figyelése érdekében. Ha ehelyett egy összeképezett környezetet ad meg, a rendszer az adott környezethez háttérként gyorsítótárazott rendszerképet használja.

- **Skálázás:** A fürt akkor kísérl meg horizontális felskálát végezni, Batch AI fürt több csomópontot igényel a futtatás végrehajtásához, mint amennyit jelenleg elérhető.

- **Futtatva:** A szkriptmappában lévő összes szkript fel lesz töltve a számítási célra, az adattárak csatlakoztatva vagy másolhatók, és a rendszer végrehajtja `script` a parancsot. Az stdout és **a ./logs** mappa kimenetei a futtatás előzményeibe lesznek streamelve, és a futtatás figyelése érdekében használhatók.

- **Utófeldolgozás:** A rendszer átmásolja a futtatás **./outputs** mappáját a futtatás előzményeibe.

## <a name="register-the-model"></a>A modell regisztrálása

A modell betanítás után regisztrálható a munkaterületen. A modellregisztrációval a modelleket a munkaterületen tárolhatja és verziószámmal egészheti ki, így leegyszerűsítheti [a modellkezelést és az üzembe helyezést.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> Az üzembe helyezési útmutató tartalmaz egy szakaszt a modellek regisztrálása során, de közvetlenül is átugorhat [egy](how-to-deploy-and-where.md#choose-a-compute-target) számítási cél létrehozásához az üzembe helyezéshez, mivel már rendelkezik regisztrált modellel.

A modell helyi példányát is letöltheti. Ez hasznos lehet további modellellenőrzési munka helyi munkához. A betanítási szkriptben egy TensorFlow takarékos objektum egy helyi mappában (a számítási célon helyi szinten) marad meg `keras_mnist.py` a modell. A Futtatás objektummal letölthet egy másolatot a futtatás előzményeiből.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben betanított és regisztrált egy Keras-modellt a Azure Machine Learning. Ha meg szeretne ismerkedni a modellek üzembe helyezési folyamatának mikéntjére, folytassa a modell üzembe helyezéssel kapcsolatos cikkel.

* [Modellek üzembe helyezése](how-to-deploy-and-where.md)
* [Futtatás metrika nyomon követése a betanítás során](how-to-log-view-metrics.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
* [Referenciaarchitektúra elosztott mélytanulási betanításhoz az Azure-ban](/azure/architecture/reference-architectures/ai/training-deep-learning)
