---
title: Mély tanulási kerasz-modellek betanítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan lehet betanítani és regisztrálni a TensorFlow-on futó kerasz Deep neurális hálózati besorolási modellt Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2b4af9dec2bf397ad2766c68d547eeac85a9a9a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518364"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Kerasz-modellek betanítása méretekben Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan futtathat kerasz-betanítási parancsfájlokat Azure Machine Learning használatával.

A cikkben szereplő példa azt mutatja be, hogyan lehet betanítani és regisztrálni egy kerasz-besorolási modellt, amely a TensorFlow-háttér használatával készült Azure Machine Learning segítségével. A szolgáltatás a népszerű [MNIST-adatkészletet](http://yann.lecun.com/exdb/mnist/) használja a kézzel írt számjegyek besorolására egy olyan Deep neurális hálózat (DNN) használatával, amely a [TensorFlow](https://www.tensorflow.org/overview)-on futó [kerasz Python-kódtár](https://keras.io) használatával készült.

A kerasz egy magas szintű neurális hálózati API, amely a fejlesztés egyszerűsítése érdekében más népszerű DNN-keretrendszerek felett is futtatható. A Azure Machine Learning segítségével rugalmas felhőalapú számítási erőforrásokkal gyorsan kibővítheti a betanítási feladatokat. Nyomon követheti a képzések futtatását, a verziók modelljeit, a modellek üzembe helyezését és még sok mást is.

Függetlenül attól, hogy a kerasz-modellt fejleszti az alapoktól, vagy egy meglévő modellt hoz létre a felhőben, Azure Machine Learning segíthet a termelésre kész modellek létrehozásában.

> [!NOTE]
> Ha a kerasz API **TF. kerasz** beépített TensorFlow, és nem az önálló kerasz csomagot használja, tekintse át a [TensorFlow-modellek betanítása](how-to-train-tensorflow.md)helyet.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:

- Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

     - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló Samples (minták) mappájában keresse meg a befejezett és kibontott jegyzetfüzetet, ehhez a következő könyvtárra navigálva: **How-to-use-azureml > ml-keretrendszerek > kerasz > Train-hiperparaméter-Tune-Deploy-with-kerasz** mappa.

 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse az Azure Machine learning SDK](/python/api/overview/azure/ml/install) -t (>= 1.15.0).
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - [A minta parancsfájl fájljainak letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` és `utils.py`

    Az útmutató egy befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) is megtalálhatja a GitHub-minták lapon. A jegyzetfüzet tartalmaz kibővített szakaszt az intelligens hiperparaméter hangolás, a modell üzembe helyezése és a notebook widgetek számára.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a betanítási kísérletet a szükséges Python-csomagok betöltésével, a munkaterület inicializálásával, a bemeneti betanítási adatok FileDataset létrehozásával, a számítási cél létrehozásával és a képzési környezet definiálásával állítja be.

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

A `register()` metódussal regisztrálhatja az adatkészletet a munkaterületre, hogy másokkal is megoszthatók legyenek, újra felhasználva a különböző kísérletekben, és nevük alapján a betanítási szkriptben.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a betanítási feladatokhoz a futtatáshoz. Ebben a példában hozzon létre egy GPU-kompatibilis Azure Machine Learning számítási fürtöt.

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

Adja meg azt az Azure ML- [környezetet](concept-environments.md) , amely magában foglalja a betanítási parancsfájl függőségeit.

Először határozza meg a Conda függőségeit egy YAML-fájlban; Ebben a példában a fájl neve `conda_dependencies.yml` .

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

Hozzon létre egy Azure ML-környezetet ebből a Conda-környezeti specifikációból. A környezet egy Docker-tárolóba lesz csomagolva futásidőben.

Alapértelmezés szerint, ha nincs megadva alaprendszerkép, az Azure ML egy CPU-rendszerképet fog használni `azureml.core.environment.DEFAULT_CPU_IMAGE` az alaprendszerképként. Mivel ez a példa egy GPU-fürtön futtatja a képzést, meg kell adnia egy GPU-alapú alapképet, amely rendelkezik a szükséges GPU-illesztővel és-függőségekkel. Az Azure ML a Microsoft Container Registry (MCR) szolgáltatásban közzétett alaplemezképek készletét tartja fenn. További információért tekintse meg az [Azure/AzureML-containers GitHub-](https://github.com/Azure/AzureML-Containers) tárházat.

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

További információ a környezetek létrehozásáról és használatáról: [szoftverek környezetének létrehozása és használata Azure Machine Learningban](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>A betanítási Futtatás konfigurálása és elküldése

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig létrehozása
Először kérje le a munkaterület-adattár adatait a `Dataset` osztály használatával.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Hozzon létre egy ScriptRunConfig objektumot a betanítási feladatok konfigurációs adatainak megadásához, beleértve a betanítási parancsfájlt, a használni kívánt környezetet és a futtatáshoz szükséges számítási célt.

A program a (z `arguments` ) paraméterben megadott paraméterekkel adja át a betanítási parancsfájl összes argumentumát. A FileDataset DatasetConsumptionConfig a betanítási szkript argumentumként adja át az `--data-folder` argumentumhoz. Az Azure ML ezt a DatasetConsumptionConfig a mögöttes adattár csatlakoztatási pontjára oldja fel, amely ezután elérhető lesz a betanítási szkriptből.

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

A feladatok ScriptRunConfig-vel való konfigurálásával kapcsolatos további információkért lásd: a [betanítási futtatások konfigurálása és elküldése](how-to-set-up-training-targets.md).

> [!WARNING]
> Ha korábban már használta a TensorFlow-kalkulátort a kerasz-betanítási feladatok konfigurálásához, vegye figyelembe, hogy a becslések a 1.19.0 SDK kiadása óta elavulttá vált. Az Azure ML SDK >= 1.15.0 használatával a betanítási feladatok, köztük a Deep learning-keretrendszerek használatát ajánlott beállítani a ScriptRunConfig. Gyakori áttelepítési kérdésekben tekintse [meg a ScriptRunConfig áttelepítési útmutatót a kalkulátorban](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-your-run"></a>A Futtatás beküldése

A [Run objektum](/python/api/azureml-core/azureml.core.run%28class%29) biztosítja a felületet a futtatási előzményekhez, miközben a feladatot futtatja, és a művelet befejeződött.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Mi történik a Futtatás végrehajtásakor
A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Előkészítés**: a Docker-rendszerkép a definiált környezetnek megfelelően jön létre. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők. Ha helyette egy kurátori környezet van megadva, akkor a rendszer a kiválasztott gyorsítótárazott rendszerképet fogja használni.

- **Skálázás**: a fürt akkor kísérli meg a skálázást, ha a Batch AI fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: a rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és `script` végrehajtja a szolgáltatást. Az stdout és a **./logs** mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Utómunka**: a Futtatás **./outputs** mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="register-the-model"></a>A modell regisztrálása

A modell kiképzése után regisztrálhatja azt a munkaterületre. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> Az üzembe helyezési útmutató egy szakaszt tartalmaz a modellek regisztrálásához, de közvetlenül kihagyhatja a központi telepítéshez szükséges [számítási cél létrehozását](how-to-deploy-and-where.md#choose-a-compute-target) , mivel már rendelkezik regisztrált modellel.

Letöltheti a modell helyi példányát is. Ez akkor lehet hasznos, ha a modell további ellenőrzése helyileg működik. A betanítási parancsfájlban `keras_mnist.py` egy TensorFlow-megtakarítási objektum a modellt egy helyi mappába (a számítási cél számára) őrzi meg. A Run (Futtatás) objektum használatával letöltheti a másolatokat a futtatási előzményekből.

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

Ebben a cikkben egy kerasz-modellt képzett és regisztrált Azure Machine Learningon. A modellek üzembe helyezésének megismeréséhez folytassa a modell üzembe helyezésével kapcsolatos cikket.

* [Modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)
* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
* [Az Azure-ban elosztott Deep learning-képzés hivatkozási architektúrája](/azure/architecture/reference-architectures/ai/training-deep-learning)
