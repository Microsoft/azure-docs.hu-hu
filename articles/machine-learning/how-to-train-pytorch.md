---
title: Mély tanulási PyTorch-modellek betanítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan futtathatja nagyvállalati szintű PyTorch-betanítási parancsfájljait Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b1cb14e07f6c0e402510abad6f1cb160f5215c63
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518381"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>PyTorch-modellek betanítása méretekben Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan futtathat nagyvállalati szintű [PyTorch](https://pytorch.org/) -betanítási szkripteket Azure Machine learning használatával.

Az ebben a cikkben szereplő szkriptek a csirkék és a Törökország képeinek osztályozására szolgálnak a Deep learning neurális hálózat (DNN) létrehozásához, amely a PyTorch adatátviteli [oktatóanyagán](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)alapul. Az átvitel a tanulás olyan technika, amely az egyik probléma megoldását egy másik, de kapcsolódó problémára is kiszolgálja. Ez a tanítási folyamat lerövidítésével kevesebb adatmennyiséget, időt és számítási erőforrást igényel, mint a teljesen új képzés. A tanulással kapcsolatos további információkért tekintse meg a [Deep learning vs Machine learning](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) című cikket.

Legyen szó akár egy mély tanulási PyTorch modellről az alapoktól, akár egy meglévő modellt hoz létre a felhőben, a Azure Machine Learning segítségével rugalmas Felhőbeli számítási erőforrásokkal bővítheti a nyílt forráskódú képzési feladatokat. A Azure Machine Learning használatával előkészítheti, üzembe helyezheti, telepítheti és figyelheti a termelési szintű modelleket. 

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:

- Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

    - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló minták Deep learning mappájában keresse meg a befejezett és kibontott jegyzetfüzetet a következő könyvtárra való navigálással: **útmutató – használat-azureml > ml-keretrendszerek > pytorch > Train-hiperparaméter-Tune-Deploy-with-pytorch** mappa. 
 
 - Saját Jupyter Notebook-kiszolgáló
    - [Telepítse az Azure Machine learning SDK](/python/api/overview/azure/ml/install) -t (>= 1.15.0).
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - [A minta parancsfájl fájljainak letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Az útmutató egy befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) is megtalálhatja a GitHub-minták lapon. A jegyzetfüzet tartalmaz kibővített szakaszt az intelligens hiperparaméter hangolás, a modell üzembe helyezése és a notebook widgetek számára.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a betanítási kísérletet a szükséges Python-csomagok betöltésével, a munkaterület inicializálásával, a számítási cél létrehozásával és a képzési környezet definiálásával állítja be.

### <a name="import-packages"></a>Csomagok importálása

Először importálja a szükséges Python-kódtárakat.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
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

### <a name="get-the-data"></a>Az adatok lekérése

Az adatkészlet körülbelül 120 betanítási képet tartalmaz a pulykák és csirkék számára, az egyes osztályokhoz 100 érvényesítési lemezképekkel. Az adathalmazt a betanítási szkript részeként fogjuk letölteni és kinyerni `pytorch_train.py` . A képek a [megnyitott lemezképek V5-adatkészlet](https://storage.googleapis.com/openimages/web/index.html)részhalmazai.

### <a name="prepare-training-script"></a>Betanítási szkript előkészítése

Ebben az oktatóanyagban már meg van biztosítva a betanítási szkript `pytorch_train.py` . A gyakorlatban bármilyen egyéni tanítási szkriptet igénybe vehet, és futtathatja a Azure Machine Learning használatával.

Hozzon létre egy mappát a betanítási parancsfájl (ok) hoz.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a PyTorch-feladatokhoz a futtatásához. Ebben a példában hozzon létre egy GPU-kompatibilis Azure Machine Learning számítási fürtöt.

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
Az Azure ML-ben előre elkészített, kiépített környezetek is elérhetők, ha nem szeretné saját környezetét meghatározni. Az Azure ML több PROCESSZORral és GPU-val rendelkező környezettel rendelkezik a PyTorch különböző verzióihoz tartozó PyTorch. További információ: [itt](resource-curated-environments.md).

Ha egy kurátori környezetet szeretne használni, a következő parancsot futtathatja inkább:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

A kurátori környezetben található csomagok megtekintéséhez a Conda-függőségeket a következő lemezre írhatja:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Győződjön meg arról, hogy a betanított környezet tartalmazza a betanítási szkript által igényelt összes függőséget. Ha nem, akkor módosítania kell a környezetet a hiányzó függőségek belefoglalásához. Vegye figyelembe, hogy ha a környezet módosítva van, meg kell adnia egy új nevet, mivel a "AzureML" előtag a kurátori környezetek számára van fenntartva. Ha módosította a Conda-függőségek YAML-fájlját, új néven létrehozhat egy új környezetet, például:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Ha ehelyett közvetlenül módosította a kurátori környezet objektumát, akkor a környezet új néven is klónozott:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
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
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Hozzon létre egy Azure ML-környezetet ebből a Conda-környezeti specifikációból. A környezet egy Docker-tárolóba lesz csomagolva futásidőben.

Alapértelmezés szerint, ha nincs megadva alaprendszerkép, az Azure ML egy CPU-rendszerképet fog használni `azureml.core.environment.DEFAULT_CPU_IMAGE` az alaprendszerképként. Mivel ez a példa egy GPU-fürtön futtatja a képzést, meg kell adnia egy GPU-alapú alapképet, amely rendelkezik a szükséges GPU-illesztővel és-függőségekkel. Az Azure ML a Microsoft Container Registry (MCR) szolgáltatásban közzétett alaplemezképek készletét tartja fenn. További információért tekintse meg az [Azure/AzureML-containers GitHub-](https://github.com/Azure/AzureML-Containers) tárházat.

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Igény szerint egyszerűen rögzítheti az összes függőségét egy egyéni Docker-rendszerképben vagy Docker, és létrehozhatja a környezetét. További információ: [a betanítás egyéni rendszerképpel](how-to-train-with-custom-image.md).

További információ a környezetek létrehozásáról és használatáról: [szoftverek környezetének létrehozása és használata Azure Machine Learningban](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>A betanítási Futtatás konfigurálása és elküldése

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig létrehozása

Hozzon létre egy [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) objektumot a betanítási feladatok konfigurációs adatainak megadásához, beleértve a betanítási parancsfájlt, a használni kívánt környezetet és a futtatáshoz szükséges számítási célt. A program a (z `arguments` ) paraméterben megadott paraméterekkel adja át a betanítási parancsfájl összes argumentumát. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning a teljes forrás könyvtár másolásával futtatja a betanítási parancsfájlokat. Ha olyan bizalmas adatokkal rendelkezik, amelyeket nem szeretne felvenni, használja a [. ignore fájlt](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) , vagy ne adja meg a forrás könyvtárában. Ehelyett egy Azure ML- [adathalmaz](how-to-train-with-datasets.md)használatával férhet hozzá az adataihoz.

A feladatok ScriptRunConfig-vel való konfigurálásával kapcsolatos további információkért lásd: a [betanítási futtatások konfigurálása és elküldése](how-to-set-up-training-targets.md).

> [!WARNING]
> Ha korábban már használta a PyTorch-kalkulátort a PyTorch-betanítási feladatok konfigurálásához, vegye figyelembe, hogy a becslések a 1.19.0 SDK kiadása óta elavulttá vált. Az Azure ML SDK >= 1.15.0 használatával a betanítási feladatok, köztük a Deep learning-keretrendszerek használatát ajánlott beállítani a ScriptRunConfig. Gyakori áttelepítési kérdésekben tekintse [meg a ScriptRunConfig áttelepítési útmutatót a kalkulátorban](how-to-migrate-from-estimators-to-scriptrunconfig.md).

## <a name="submit-your-run"></a>A Futtatás beküldése

A [Run objektum](/python/api/azureml-core/azureml.core.run%28class%29) biztosítja a felületet a futtatási előzményekhez, miközben a feladatot futtatja, és a művelet befejeződött.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Mi történik a Futtatás végrehajtásakor
A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Előkészítés**: a Docker-rendszerkép a definiált környezetnek megfelelően jön létre. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők. Ha helyette egy kurátori környezet van megadva, akkor a rendszer a kiválasztott gyorsítótárazott rendszerképet fogja használni.

- **Skálázás**: a fürt akkor kísérli meg a skálázást, ha a Batch AI fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: a rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és `script` végrehajtja a szolgáltatást. Az stdout és a **./logs** mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Utómunka**: a Futtatás **./outputs** mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="register-or-download-a-model"></a>Modell regisztrálása vagy letöltése

A modell kiképzése után regisztrálhatja azt a munkaterületre. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> Az üzembe helyezési útmutató egy szakaszt tartalmaz a modellek regisztrálásához, de közvetlenül kihagyhatja a központi telepítéshez szükséges [számítási cél létrehozását](how-to-deploy-and-where.md#choose-a-compute-target) , mivel már rendelkezik regisztrált modellel.

A modell helyi másolatát a Run objektum használatával is letöltheti. A betanítási parancsfájlban a `pytorch_train.py` PyTorch mentése objektum megőrzi a modellt egy helyi mappába (helyi a számítási célra). A Futtatás objektum használatával letöltheti a másolatokat.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Elosztott betanítás

A Azure Machine Learning támogatja a többcsomópontos elosztott PyTorch feladatok használatát is, így méretezhetővé teheti a betanítási számítási feladatokat. Az elosztott PyTorch feladatok egyszerűen futtathatók, az Azure ML pedig felügyeli az Ön számára.

Az Azure ML támogatja az elosztott PyTorch-feladatok futtatását a Horovod és a PyTorch beépített DistributedDataParallel modullal.

### <a name="horovod"></a>Horovod
A [Horovod](https://github.com/uber/horovod) egy nyílt forráskódú, amely az Über által fejlesztett elosztott képzések összes csökkentési keretrendszerét csökkenti. Egyszerű elérési utat biztosít az elosztott PyTorch-kódok írásához a betanításhoz.

A betanítási kódot az elosztott képzés Horovod kell kiépíteni. A Horovod és a PyTorch használatával kapcsolatos további információkért tekintse meg a [Horovod dokumentációját](https://horovod.readthedocs.io/en/stable/pytorch.html).

Továbbá győződjön meg arról, hogy a képzési környezet tartalmazza a **horovod** csomagot. Ha PyTorch kurátori környezetet használ, a horovod már szerepel a függőségek egyikében is. Ha saját környezetet használ, győződjön meg róla, hogy a horovod függőség szerepel, például:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

Elosztott feladatok az Azure ML-ben használt MPI/Horovod használatával történő végrehajtásához meg kell adnia egy [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) a `distributed_job_config` ScriptRunConfig konstruktor paramétereként. Az alábbi kód egy 2 csomópontos elosztott feladatot konfigurál, amely egy folyamatot futtat egy csomóponton. Ha egy csomóponton belül több folyamatot is futtatni kíván (például ha a fürt SKU-jának több GPU-val rendelkezik), adja meg a `process_count_per_node` paramétert a MpiConfiguration (ez az alapértelmezett érték `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Az elosztott PyTorch az Azure ML-Horovod való futtatásával kapcsolatos teljes oktatóanyagért lásd: [elosztott PyTorch és Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod).

### <a name="distributeddataparallel"></a>DistributedDataParallel
Ha a PyTorch beépített [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) modulját használja, amely a betanítási kódban a **Torch. Distributed** Package használatával készült, akkor az ELOSZTOTT feladatot az Azure ml-n keresztül is elindíthatja.

Az Azure ML-ben elosztott PyTorch-feladatok elindításához két lehetőség közül választhat:
1. Folyamaton belüli indítás: adja meg a futtatni kívánt munkavégző folyamatok teljes számát, és az Azure ML az egyes folyamatok indítását fogja kezelni.
2. Felhasználónkénti indítás a következővel `torch.distributed.launch` : adja `torch.distributed.launch` meg az egyes csomópontokon futtatni kívánt parancsot. A Torch Launch segédprogram kezeli a munkavégző folyamatok indítását az egyes csomópontokon.

Ezek az indítási lehetőségek nem rendelkeznek alapvető különbségekkel; nagyrészt a felhasználó preferenciájának vagy a vanília-PyTorch (például villám vagy átfedő arc) épülő keretrendszerek/könyvtárak konvenciói.

#### <a name="per-process-launch"></a>Folyamaton belüli indítás
Ha ezt a lehetőséget szeretné használni az elosztott PyTorch-feladatok futtatásához, tegye a következőket:
1. Adja meg a betanítási szkriptet és argumentumokat
2. Hozzon létre egy [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) , és határozza meg a, valamint a `process_count` `node_count` . Az `process_count` megfelel a feladatokhoz futtatni kívánt folyamatok teljes számának. Ez általában egyenlő a GPU-k számával, szorozva a csomópontok számával. Ha `process_count` nincs megadva, az Azure ml alapértelmezés szerint egy folyamatot indít el egy csomóponton.

Az Azure ML a következő környezeti változókat fogja beállítani:
* `MASTER_ADDR` – Az a számítógép IP-címe, amely a 0. rangsorban fogja üzemeltetni a folyamatot.
* `MASTER_PORT` – A gép egy ingyenes portja, amely a 0. rangsorban fogja üzemeltetni a folyamatot.
* `NODE_RANK` – A csomópont rangja a többcsomópontos képzéshez. A lehetséges értékek a következők: 0 – (csomópontok száma összesen – 1).
* `WORLD_SIZE` – A folyamatok teljes száma. Ennek egyenlőnek kell lennie az elosztott képzéshez használt összes eszköz (GPU) számával.
* `RANK` – Az aktuális folyamat (globális) rangsora. A lehetséges értékek: 0 – (globális méret – 1).
* `LOCAL_RANK` – A folyamat helyi (relatív) rangsora a csomóponton belül. A lehetséges értékek a következők: 0 – (az 1. csomóponton futó folyamatok száma).

Mivel a szükséges környezeti változók az Azure ML-ben lesznek beállítva, [az alapértelmezett környezeti változó inicializálási módszerével](https://pytorch.org/docs/stable/distributed.html#environment-variable-initialization) inicializálhatja a folyamat csoportját a betanítási kódban.

A következő kódrészlet egy 2 csomópontos, 2 folyamat/csomópontos PyTorch-feladatot konfigurál:
```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=4, node_count=2)

src = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 25],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

> [!WARNING]
> Ha ezt a lehetőséget szeretné használni a többfolyamatos felhasználónkénti képzéshez, az Azure ML Python SDK >= 1.22.0-t kell használnia, ahogy `process_count` az a 1.22.0-ben jelent meg.

> [!TIP]
> Ha a betanítási szkript olyan információkat ad át, mint a helyi rangsor vagy a rangsor parancsfájl-argumentumként, akkor a környezeti változóra hivatkozhat a következő argumentumokban: `arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]` .

#### <a name="per-node-launch-with-torchdistributedlaunch"></a>Csomóponton belüli indítás a `torch.distributed.launch`
A PyTorch a [Torch. Distributed. Launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) segédprogramot nyújtja, amely lehetővé teszi, hogy a felhasználók több folyamat indítását is elindítsák egy csomóponton. A `torch.distributed.launch` modul minden egyes csomóponton több betanítási folyamatot indít el.

A következő lépések bemutatják, hogyan konfigurálhat egy PyTorch-feladatot az Azure ML-ben egy csomópont-indítóval, amely a következő parancs futtatásával egyenértékűnek fogja elérni:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Adja `torch.distributed.launch` meg a parancsot a `command` konstruktor paraméteréhez `ScriptRunConfig` . Az Azure ML a betanítási fürt minden csomópontján futtatja ezt a parancsot. `--nproc_per_node` kisebbnek vagy egyenlőnek kell lennie az egyes csomópontokon elérhető GPU-k számával. `MASTER_ADDR`, `MASTER_PORT` és az `NODE_RANK` összes Azure ml-vel van beállítva, így a parancsban egyszerűen hivatkozhat a környezeti változókra. Az Azure ML `MASTER_PORT` -re 6105-re van szüksége, de ha kívánja, más értéket is megadhat a `--master_port` parancs argumentumához `torch.distributed.launch` . (A rendszerindító segédprogram alaphelyzetbe állítja a környezeti változókat.)
2. Hozzon létre egy `PyTorchConfiguration` -t, és határozza meg a `node_count` . Nem kell beállítania, hogy az `process_count` Azure ml alapértelmezetten egy folyamat indítását hajtja végre egy csomóponton, amely a megadott indítási parancsot fogja futtatni.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 2 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

src = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

Az elosztott PyTorch Azure ML-re való futtatásával kapcsolatos teljes oktatóanyagért tekintse meg az [elosztott PyTorch a DistributedDataParallel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-distributeddataparallel)-mel című témakört.

### <a name="troubleshooting"></a>Hibaelhárítás

* A **Horovod le lett** állítva: a legtöbb esetben, ha a "AbortedError: Horovod leállítása" művelettel találkozik, a rendszer egy mögöttes kivételt észlelt az egyik olyan folyamatnál, amely a Horovod leállítását okozta. Az MPI-feladat mindegyik rangsora saját dedikált naplófájllal fog rendelkezni az Azure ML-ben. Ezen naplók neve `70_driver_logs`. Elosztott betanítás esetén a naplók neve a `_rank` utótaggal egészül ki, hogy könnyebben meg tudja őket különböztetni. A Horovod leállítását okozó pontos hiba megtalálásához folytassa az összes naplófájlt, és keresse meg a `Traceback` driver_log fájlok végén található fájlt. Ezen fájlok egyike megadja a tényleges mögöttes kivételt. 

## <a name="export-to-onnx"></a>Exportálás ONNX

Ha optimalizálni szeretné a következtetést a [ONNX futtatókörnyezettel](concept-onnx.md), alakítsa át a betanított PyTorch modelljét a ONNX formátumba. A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modellt az előrejelzéshez használják, leggyakrabban a termelési adatforgalomban. Példaként tekintse meg az [oktatóanyagot](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) .

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a PyTorch-on Azure Machine Learning-on keresztül tanult és regisztrált egy mély tanulási és neurális hálózatot. A modellek üzembe helyezésének megismeréséhez folytassa a modell üzembe helyezésével kapcsolatos cikket.

* [Modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)
* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
* [Az Azure-ban elosztott Deep learning-képzés hivatkozási architektúrája](/azure/architecture/reference-architectures/ai/training-deep-learning)
