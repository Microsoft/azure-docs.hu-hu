---
title: Mélytanulásos PyTorch-modellek betanítása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan futtathat PyTorch-betanító szkripteket nagyvállalati méretekben a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5a955424f3fb91a38e9061966ed742922913f1c4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819159"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>PyTorch-modellek nagy léptékű betanítása Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan futtathat [PyTorch-betanító](https://pytorch.org/) szkripteket vállalati méretekben a Azure Machine Learning.

A cikkben a példaszk szkriptek segítségével osztályozhatók a képek és a képek, így egy mélytanulásos neurális hálózatot (DNN) hoznak létre a PyTorch transzfertanuló [oktatóanyaga alapján.](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) A transzfertanulás egy olyan technika, amely az egyik probléma megoldásával megszerzett tudást egy másik, de kapcsolódó problémára alkalmazza. Ez úgy csökkenti a betanítási folyamatot, hogy kevesebb adatra, időre és számítási erőforrásra van szükség, mint az új betanítás. A mély [tanulás és a gépi tanulás elsajátításról](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) a mély tanulásról és a gépi tanulásról olvashat bővebben.

Függetlenül attól, hogy egy mélytanulásos PyTorch-modellt az alapból betanít, vagy egy meglévő modellt hoz létre a felhőbe, az Azure Machine Learning használatával horizontálisan felskálolhatja a nyílt forráskódú betanításokat rugalmas felhőalapú számítási erőforrásokkal. Az éles környezetben használható modelleket a modellekkel buildelheti, üzembe helyezheti, verziószámozhatja és Azure Machine Learning. 

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot az alábbi környezetek egyikében:

- Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

    - Az [Oktatóanyag: Környezet](tutorial-1st-experiment-sdk-setup.md) és munkaterület beállítása lépésekkel hozzon létre egy dedikált jegyzetfüzet-kiszolgálót, amely előre be van töltve az SDK-val és a mintaadattárba.
    - A notebookkiszolgálón található mélytanulásminták mappában keresse meg a befejezett és kibontott jegyzetfüzetet a következő könyvtárban: **how-to-use-azureml > ml-frameworks > pytorch > train-hyperparameter-tune-deploy-with-pytorch** mappa. 
 
 - Saját Jupyter Notebook kiszolgálója
    - [Telepítse az Azure Machine Learning SDK-t](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Hozzon létre egy munkaterület-konfigurációs fájlt.](how-to-configure-environment.md#workspace)
    - [A minta szkriptfájlok letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Az útmutató teljes Jupyter Notebook [a](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) GitHub-minták oldalán is megtalálja. A jegyzetfüzet bővített szakaszokat tartalmaz, amelyek az intelligens hiperparaméter-finomhangolást, a modell üzembe helyezését és a jegyzetfüzet-widgeteket ismertetik.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a szükséges Python-csomagok betöltésével, egy munkaterület inicializálása, a számítási cél létrehozása és a betanítési környezet definiálása által állítja be a betanítás kísérletét.

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

A [Azure Machine Learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való munkához. A Python SDK-ban objektum létrehozásával férhet hozzá a [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) munkaterület-összetevőkhez.

Hozzon létre egy munkaterület-objektumot az előfeltételek szakaszban létrehozott `config.json` [fájlból.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Az adatok lekérése

Az adatkészlet körülbelül 120 betanító képből áll az egyes osztályokhoz, és mindegyik osztályhoz 100 ellenőrző képet tartalmaz. Az adatkészletet a betanító szkript részeként fogjuk letölteni és `pytorch_train.py` kinyerni. A rendszerképek az Open [Images v5 adatkészlet részkészletei.](https://storage.googleapis.com/openimages/web/index.html)

### <a name="prepare-training-script"></a>Betanító szkript előkészítése

Ebben az oktatóanyagban a betanító szkript már `pytorch_train.py` rendelkezésre áll. A gyakorlatban bármilyen egyéni betanító szkriptet saját ként is futtathat a Azure Machine Learning.

Hozzon létre egy mappát a betanító szkript(ék)hez.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a PyTorch-feladat számára a futtatáshoz. Ebben a példában egy GPU-kompatibilis számítási Azure Machine Learning hozunk létre.

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

A betanító [szkript](concept-environments.md) függőségeit beágyazó Azure ML-környezet meghatározásához definiálhat egy egyéni környezetet, vagy használhat Azure ML-hez összeszámolt környezetet.

#### <a name="use-a-curated-environment"></a>Összeforrott környezet használata
Az Azure ML előre összeállított környezeteket biztosít, ha nem szeretné meghatározni a saját környezetét. Az Azure ML több, a PyTorch különböző verzióinak megfelelő, CPU- és GPU-alapú környezetben is elérhető. További információ: [.](resource-curated-environments.md)

Ha egy össze összecsatolandó környezetet szeretne használni, ehelyett futtassa a következő parancsot:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

A összecsomagzott környezetben található csomagok megtekintése érdekében kiírhatja a Conda-függőségeket a lemezre:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Győződjön meg arról, hogy a összeírt környezet tartalmazza a betanítás szkriptjéhez szükséges összes függőséget. Ha nem, módosítania kell a környezetet, hogy tartalmazza a hiányzó függőségeket. Vegye figyelembe, hogy a környezet módosításakor új nevet kell adni neki, mivel az "AzureML" előtag összerendelt környezetekhez van fenntartva. Ha módosította a Conda-függőségek YAML-fájlját, létrehozhat egy új környezetet a fájlból egy új névvel, például:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Ha ehelyett közvetlenül módosította a körülértelt környezeti objektumot, klónozhatja ezt a környezetet egy új névvel:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
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
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Hozzon létre egy Azure ML-környezetet ebből a Conda-környezet specifikációból. A környezet futásidőben egy Docker-tárolóba lesz csomagolva.

Ha nincs megadva alapként megadott rendszerkép, az Azure ML alapértelmezés szerint egy CPU-rendszerképet használ `azureml.core.environment.DEFAULT_CPU_IMAGE` alapként. Mivel ez a példa egy GPU-fürtön futtat betanításokat, meg kell adnia egy alap GPU-rendszerképet, amely rendelkezik a szükséges GPU-illesztőkkel és -függőségekkel. Az Azure ML egy használható alapként szolgáló rendszerképkészletet tart fenn az Microsoft Container Registry(MCR) szolgáltatásban, további információért tekintse meg az [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub-adattárat.

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Igény szerint az összes függőséget rögzítheti közvetlenül egy egyéni Docker-rendszerképben vagy Docker-fájlban, és létrehozhatja ebből a környezetet. További információ: [Betanítás egyéni rendszerképpel.](how-to-train-with-custom-image.md)

A környezetek létrehozásával és használatával kapcsolatos további információkért lásd: Szoftverkörnyezetek létrehozása és használata [a Azure Machine Learning.](how-to-use-environments.md)

## <a name="configure-and-submit-your-training-run"></a>A betanítás futtatásának konfigurálása és elküldése

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig létrehozása

Hozzon létre [egy ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) objektumot a betanítási feladat konfigurációs részleteinek megadásához, beleértve a betanítási szkriptet, a használni kívánt környezetet és a futtatni kívánt számítási célt. A betanítási szkript minden argumentuma a parancssoron keresztül lesz átadva, ha a paraméterben meg van `arguments` adva. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning a teljes forráskönyvtár másolása által futtatja a betanító szkripteket. Ha bizalmas adatokat nem szeretne feltölteni, használjon [.ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) fájlt, vagy ne foglalja bele őket a forráskönyvtárba. Ehelyett egy Azure ML-adatkészlet használatával [férhet hozzá az adataihoz.](how-to-train-with-datasets.md)

A feladatok ScriptRunConfig használatával való konfigurálásával kapcsolatos további információkért lásd: Betanítás futtatás [konfigurálása és elküldése.](how-to-set-up-training-targets.md)

> [!WARNING]
> Ha korábban a PyTorch becslővel konfigurálta a PyTorch betanító feladatokat, vegye figyelembe, hogy a becslők elavultak az 1.19.0 SDK-kiadás óta. Az Azure ML SDK >= 1.15.0 esetén a ScriptRunConfig a betanítás ajánlott módja, beleértve a mély tanulási keretrendszereket használó feladatokat is. A migrálással kapcsolatos gyakori kérdésekért tekintse meg [a Estimator to ScriptRunConfig áttelepítési útmutatót.](how-to-migrate-from-estimators-to-scriptrunconfig.md)

## <a name="submit-your-run"></a>A futtatás elküldése

A [Futtatás objektum](/python/api/azureml-core/azureml.core.run%28class%29) biztosítja a felületet a futtatás előzményeihez a feladat futása közben és befejezése után.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Mi történik a futtatás végrehajtása során?
A futtatás végrehajtása során a következő szakaszokon megy keresztül:

- **Előkészítés:** A docker-rendszerkép a meghatározott környezetnek megfelelően jön létre. A rendszerképet feltölti a rendszer a munkaterület tárolójegyzékbe, és gyorsítótárazza későbbi futtatásra. A naplók a futtatás előzményeibe is streamelve megtekinthetők a folyamat figyelése érdekében. Ha ehelyett egy összeképezett környezetet ad meg, a rendszer az adott környezethez háttérként gyorsítótárazott rendszerképet használja.

- **Skálázás:** A fürt akkor kísérl meg horizontális felskálát végezni, Batch AI fürt több csomópontot igényel a futtatás végrehajtásához, mint amennyit jelenleg elérhető.

- **Futtatva:** A szkriptmappában lévő összes szkript fel lesz töltve a számítási célra, az adattárak csatlakoztatva vagy másolhatók, és a rendszer végrehajtja `script` a parancsot. Az stdout és **a ./logs** mappa kimenetei a futtatás előzményeibe lesznek streamelve, és a futtatás figyelése érdekében használhatók.

- **Utófeldolgozás:** A rendszer átmásolja a futtatás **./outputs** mappáját a futtatás előzményeibe.

## <a name="register-or-download-a-model"></a>Modell regisztrálása vagy letöltése

A modell betanítás után regisztrálható a munkaterületen. A modellregisztrációval a modelleket a munkaterületen tárolhatja és verziószámmal egészheti ki, így leegyszerűsítheti [a modellkezelést és az üzembe helyezést.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> Az üzembe helyezési útmutató tartalmaz egy szakaszt a modellek regisztrálása során, de közvetlenül is átugorhat [egy](how-to-deploy-and-where.md#choose-a-compute-target) számítási cél létrehozásához az üzembe helyezéshez, mivel már rendelkezik regisztrált modellel.

A Run objektummal letöltheti a modell helyi példányát is. A betanító szkriptben egy PyTorch-mentés objektum egy helyi mappában (a számítási célon helyi szinten) menti `pytorch_train.py` a modellt. A Run (Futtatás) objektummal letölthet egy másolatot.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Elosztott betanítás

Azure Machine Learning támogatja a több csomópontos elosztott PyTorch-feladatokat is, így skálázhatja a betanítás számítási feladatait. Könnyedén futtathat elosztott PyTorch-feladatokat, és az Azure ML kezeli a vezénylést.

Az Azure ML támogatja az elosztott PyTorch-feladatok futtatását a Horovod és a PyTorch beépített DistributedDataParallel modulja segítségével.

### <a name="horovod"></a>Horovod
[A Horovod](https://github.com/uber/horovod) egy nyílt forráskódú, az Uber által fejlesztett elosztott betanítás csökkentő keretrendszere. Egyszerű útvonalat kínál elosztott PyTorch-kód írására a betanításhoz.

A betanítás kódját a Horovod eszközzel kell beszerzetni az elosztott betanításhoz. A Horovod PyTorch alkalmazással való használatával kapcsolatos további információkért tekintse meg a [Horovod dokumentációját.](https://horovod.readthedocs.io/en/stable/pytorch.html)

Emellett győződjön meg arról, hogy a betanító környezet tartalmazza a **horovod** csomagot. Ha PyTorch-összecsomókörnyezetet használ, a horovod már szerepel a függőségek egyikeként. Ha a saját környezetét használja, győződjön meg arról, hogy a horovod függőség is szerepel benne, például:

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

Ahhoz, hogy egy elosztott feladatot az MPI/Horovod használatával hajtson végre az Azure ML-ben, meg kell adnia egy [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) paramétert a `distributed_job_config` ScriptRunConfig konstruktor paramétere számára. Az alábbi kód egy csomópontonként egy folyamatot futtató kétcsomópontos elosztott feladatot konfigurál. Ha csomópontonként több folyamatot is szeretne futtatni (azaz ha a fürt termékváltozata több GPU-kkal rendelkezik), akkor az MpiConfiguration paramétert is meg kell adnia (az alapértelmezett érték `process_count_per_node` `1` a ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Az elosztott PyTorch És a Horovod használatával az Azure ML-ben való futtatásáról a [Distributed PyTorch with Horovod (Elosztott PyTorch a Horovod használatával) cikk tartalmaz teljes körű oktatóanyagot.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod)

### <a name="distributeddataparallel"></a>DistributedDataParallel
Ha a pytorch beépített [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) modulját használja, amely a betanítás kódjában található **torch.distributed** csomaggal készült, akkor az elosztott feladatot az Azure ML-en keresztül is elindíthatja.

Az elosztott PyTorch-feladat Azure ML-ben való indításához két lehetőség áll rendelkezésre:
1. Folyamatonkénti indítás: adja meg a futtatni kívánt munkavégző folyamatok teljes számát, és az Azure ML kezeli az egyes folyamatok elindítását.
2. Futtassa a csomópontonkénti indítást a paranccsal, és adja meg az egyes csomóponton `torch.distributed.launch` `torch.distributed.launch` futtatni kívánt parancsot. A torch launch segédprogram kezeli a munkavégző folyamatok elindítását minden csomóponton.

Ezek között az indítási lehetőségek között nincs alapvető különbség; ez nagyrészt a felhasználó igényeinek vagy a vanilla PyTorchra (például Villám vagy Hugging Face) épült keretrendszerek/kódtárak konvencióinak megfelelő.

#### <a name="per-process-launch"></a>Folyamatonkénti indítás
Ha ezt a lehetőséget egy elosztott PyTorch-feladat futtatására használja, tegye a következőket:
1. A betanító szkript és az argumentumok megadása
2. Hozzon létre [egy PyTorchConfiguration konfigurációt,](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) és adja meg a `process_count` és a `node_count` értékeket. A a feladathoz futtatni kívánt folyamatok `process_count` teljes számának felel meg. Ez általában megegyezik a GPU-k csomópontonkénti számával szorozva a csomópontok számával. Ha nincs megadva, az Azure ML alapértelmezés szerint csomópontonként `process_count` egy folyamatot indít el.

Az Azure ML a következő környezeti változókat fogja beállítani:
* `MASTER_ADDR` – Annak a gépnek az IP-címe, amely a folyamatot a 0. ranggal fogja gazdagépként rangsorolva.
* `MASTER_PORT` – Egy ingyenes port a gépen, amely a 0. rangsorban fogja a folyamatot.
* `NODE_RANK` – A csomópont rangja a több csomópontos betanításnál. A lehetséges értékek 0 és között (csomópontok száma összesen – 1).
* `WORLD_SIZE` – A folyamatok teljes száma. Ennek egyenlőnek kell lennie az elosztott betanításhoz használt eszközök (GPU-k) teljes számével.
* `RANK` – Az aktuális folyamat (globális) rangja. A lehetséges értékek 0 és között (világméret – 1).
* `LOCAL_RANK` – A folyamat helyi (relatív) rangja a csomóponton belül. A lehetséges értékek 0 és (a csomóponton futó folyamatok száma – 1).

Mivel az Azure ML be fogja állítani a szükséges [](https://pytorch.org/docs/stable/distributed.html#environment-variable-initialization) környezeti változókat, az alapértelmezett környezeti változó inicializálási módszerével inicializálhatja a folyamatcsoportot a betanítási kódban.

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
> Ahhoz, hogy ezt a lehetőséget csomópontonként több folyamatra is használni tudja, az Azure ML Python SDK >= 1.22.0-s verzióját kell használnia az `process_count` 1.22.0-s verziójában bemutatottak szerint.

> [!TIP]
> Ha a betanítási szkript olyan információkat ad át szkript argumentumként, mint a local rank vagy a rank, hivatkozhat a környezeti változó(k)ban az argumentumban: `arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]` .

#### <a name="per-node-launch-with-torchdistributedlaunch"></a>Csomópontonkénti indítás a(z) `torch.distributed.launch`
A PyTorch egy indítási segédprogramot biztosít a [torch.distributed.launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) fájlban, amely segítségével a felhasználók csomópontonként több folyamatot is elindíthat. A `torch.distributed.launch` modul több betanítási folyamatot is elindít az egyes csomópontokon.

A következő lépések bemutatják, hogyan konfigurálható egy PyTorch-feladat csomópont-indítóval az Azure ML-ben, amely a következő parancs futtatásával egyenértékű eredményt ad:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Adja meg `torch.distributed.launch` a parancsot `command` a `ScriptRunConfig` konstruktor paraméterének. Az Azure ML ezt a parancsot a betanító fürt minden csomópontján futtatni fogja. `--nproc_per_node` az egyes csomópontok gpu-inak számával kisebbnek vagy egyenlőnek kell lennie. `MASTER_ADDR`A , a és a mindegyiket az Azure ML adja meg, így egyszerűen hivatkozhat a környezeti `MASTER_PORT` `NODE_RANK` változókra a parancsban. Az Azure ML a 6105 értéket állítja be, de ha szeretné, más értéket is átadhat a parancs `MASTER_PORT` `--master_port` `torch.distributed.launch` argumentumának. (Az indítási segédprogram alaphelyzetbe állítja a környezeti változókat.)
2. Hozzon létre egy `PyTorchConfiguration` et, és adja meg a következőt: `node_count` . Nem kell beállítania a beállítást, mivel az Azure ML alapértelmezés szerint csomópontonként egy folyamatot indít el, amely a megadott `process_count` indítási parancsot fogja futtatni.

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

Az elosztott PyTorch Azure ML-ben való futtatásának teljes oktatóanyagát lásd: [Distributed PyTorch with DistributedDataParallel (Elosztott PyTorch a DistributedDataParallel használatával).](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-distributeddataparallel)

### <a name="troubleshooting"></a>Hibaelhárítás

* **A Horovod** le lett állított: Ha a legtöbb esetben az "AbortedError: Horovod le lett állított" hibával találkozik, akkor az egyik folyamatnál egy mögöttes kivétel történt, amely miatt a Horovod leállt. Az MPI-feladat mindegyik rangsora saját dedikált naplófájllal fog rendelkezni az Azure ML-ben. Ezen naplók neve `70_driver_logs`. Elosztott betanítás esetén a naplók neve a `_rank` utótaggal egészül ki, hogy könnyebben meg tudja őket különböztetni. A Horovod leálltát okozó pontos hiba megkeresése érdekében nézze át az összes naplófájlt, és keresse meg a driver_log `Traceback` végén. Ezen fájlok egyike megadja a tényleges mögöttes kivételt. 

## <a name="export-to-onnx"></a>Exportálás ONNX-be

Az [ONNX Runtime-ban](concept-onnx.md)a dedukencia optimalizálásához konvertálja a betanított PyTorch-modellt ONNX formátumra. A következtetés, vagyis a modell pontozása az a fázis, amelyben az üzembe helyezett modellt előrejelzésre használják, leggyakrabban az éles adatok esetében. Erre az [oktatóanyagban](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) láthat példát.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy mélytanulásos neurális hálózatot tanított be és regisztrált a PyTorch használatával a Azure Machine Learning. Ha meg szeretne ismerkedni a modellek üzembe helyezési folyamatának mikéntjére, folytassa a modell üzembe helyezési cikkel.

* [Modellek üzembe helyezése](how-to-deploy-and-where.md)
* [Futtatás metrika nyomon követése a betanítás során](how-to-log-view-metrics.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
* [Referenciaarchitektúra elosztott mélytanulási betanításhoz az Azure-ban](/azure/architecture/reference-architectures/ai/training-deep-learning)
