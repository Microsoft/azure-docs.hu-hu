---
title: Megerősítő tanulási modell (előzetes verzió) betanítása és üzembe helyezése.
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning-megerősítő tanulást (előzetes verzió) egy RL-ügynök betanításához a pong játékhoz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 4c03016d003978b3c56361595bec7c559205574b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520880"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Megerősítő tanulás (előzetes verzió) Azure Machine Learning



> [!NOTE]
> Azure Machine Learning a megerősítő tanulási funkció jelenleg előzetes verzióként érhető el. Jelenleg csak a Ray és a RLlib keretrendszerek támogatottak.

Ebből a cikkből megtudhatja, hogyan taníthat meg egy megerősítő tanulási (RL) ügynököt a videojáték-pong lejátszásához. A nyílt forráskódú Python Library [Ray RLlib](https://ray.readthedocs.io/en/master/rllib.html) és a Azure Machine learning segítségével kezelheti az elosztott RL összetettségét.

Ebben a cikkben az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Kísérlet beállítása
> * Fő-és munkavégző csomópontok meghatározása
> * RL-kalkulátor létrehozása
> * Kísérlet elküldése a Futtatás indításához
> * Eredmények megtekintése

Ez a cikk a [RLlib pong példán](https://aka.ms/azureml-rl-pong) alapul, amely a Azure Machine learning notebook [GitHub-tárházban](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)található.

## <a name="prerequisites"></a>Előfeltételek

Ezt a kódot ezen környezetek bármelyikében futtathatja. Javasoljuk, hogy próbálja meg Azure Machine Learning számítási példányt a leggyorsabb indítási élményhez. A megerősítő minta notebookok gyors klónozását és futtatását Azure Machine Learning számítási példányon végezheti el.

 - Azure Machine Learning számítási példány

     - Megtudhatja, hogyan klónozott minta-jegyzetfüzeteket a következő [oktatóanyagban: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md).
         - A **útmutató használata – azureml** mappa klónozása az **oktatóanyagok** helyett
     - Futtassa a virtuális hálózati telepítő notebookját a (z) helyen az `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb` elosztott megerősítési tanuláshoz használt hálózati portok megnyitásához.
     - A minta jegyzetfüzet futtatása `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb`
 
 - Saját Jupyter Notebook-kiszolgáló

    - Telepítse a [Azure Machine learning SDK](/python/api/overview/azure/ml/install)-t.
    - Telepítse a [Azure Machine learning RL SDK](/python/api/azureml-contrib-reinforcementlearning/)-t: `pip install --upgrade azureml-contrib-reinforcementlearning`
    - Hozzon létre egy [munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - Futtassa a virtuális hálózatot az elosztott megerősítési tanuláshoz használt hálózati portok megnyitásához.


## <a name="how-to-train-a-pong-playing-agent"></a>A pong lejátszási ügynök betanítása

A megerősítő tanulás (RL) a gépi tanulás olyan megközelítése, amellyel megtanulható. Míg más gépi tanulási technikák a bemeneti adatok passzív bevitelével és az abban belüli minták megtalálásával tanulnak, az RL a **képzési ügynökökkel** aktívan hozza döntéseket és tanulja meg az eredményeket.

A képzési ügynökök egy **szimulált környezetben** tanulják meg a pongot. A betanítási ügynökök a játék minden keretén megdöntik, hogy a lapát fel-vagy leállt, vagy a helyükön maradjon. A döntés végrehajtásához a játék állapotát (a képernyő RGB-képét) tekinti át.

Az RL a **jutalmak** használatával közli az ügynököt, ha döntése sikeres. Ebben a példában az ügynök pozitív jutalomban részesül, amikor egy pontot és negatív jutalmat mutat be, amikor egy pontot kilőttek. A képzési ügynök számos iteráción keresztül megtanulja, hogy a jelenlegi állapot alapján kiválassza a műveletet, amely a várható jövőbeli jutalmak összegét optimalizálja. Gyakori, hogy a **Deep neurális hálózatokat** (DNN) használja az RL-ben való optimalizáláshoz. 

A képzés akkor ér véget, amikor az ügynök elérte a 18. számú jutalom pontszámát egy képzési korszakban. Ez azt jelenti, hogy az ügynök az ellenfelet legalább 18 ponttal megszakította.

A szimuláció és a DNN átképzésének folyamata számítási feltételt igényel, és nagy mennyiségű adattal kell rendelkeznie. Az RL-feladatok teljesítményének növelésének egyik módja a **tetszés munkája** , így egyszerre több képzési ügynök is működhet és tanulhat. Az elosztott RL-környezetek kezelése azonban összetett vállalkozás lehet.

Azure Machine Learning biztosítja az ilyen bonyolultságok kezelésére szolgáló keretrendszert az RL számítási feladatainak méretezésére.

## <a name="set-up-the-environment"></a>A környezet beállítása

A helyi RL-környezet beállítása:
1. A szükséges Python-csomagok betöltése
1. A munkaterület inicializálása
1. Kísérlet létrehozása
1. Konfigurált virtuális hálózat megadására.

### <a name="import-libraries"></a>Kódtárak importálása

Importálja a szükséges Python-csomagokat a példa további részeinek futtatásához.

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

Inicializáljon egy [munkaterület](concept-workspace.md) -objektumot az `config.json` [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból. Ha Azure Machine Learning számítási példányban hajtja végre ezt a kódot, a konfigurációs fájl már létre lett hozva.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>Megerősítési tanulási kísérlet létrehozása

Hozzon létre egy [kísérletet](/python/api/azureml-core/azureml.core.experiment.experiment) a megerősítő tanulás futtatásának nyomon követésére. Azure Machine Learning a kísérletek a kapcsolódó kísérletek logikai gyűjteményei a futtatási naplók, előzmények, kimenetek és egyebek rendezéséhez.

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>Virtuális hálózat meghatározása

A több számítási célt használó RL-feladatok esetében olyan nyitott portokkal rendelkező virtuális hálózatot kell megadnia, amelyek lehetővé teszik a munkavégző csomópontok és a fő csomópontok közötti kommunikációt.

A virtuális hálózat bármely erőforráscsoporthoz tartozhat, de a munkaterület munkaterületének azonos régiójában kell lennie. A virtuális hálózat beállításával kapcsolatos további információkért tekintse meg a munkaterület telepítő notebookját az előfeltételek szakaszban. Itt adhatja meg az erőforráscsoport virtuális hálózatának nevét.

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>A fő-és munkavégző számítási célok meghatározása

Ez a példa külön számítási célokat használ a Ray Head és a Worker csomópontjaihoz. Ezek a beállítások lehetővé teszik a számítási erőforrások felfelé és lefelé méretezését a munkaterheléstől függően. Állítsa be a csomópontok számát, valamint az egyes csomópontok méretét az igények alapján.

### <a name="head-computing-target"></a>Fő számítástechnikai cél

A mély tanulási teljesítmény javítása érdekében egy GPU-vel felszerelt főfürtet használhat. A főcsomópont betanítja azt az neurális hálózatot, amelyet az ügynök a döntések elvégzéséhez használ. A fő csomópont a munkavégző csomópontokból származó adatpontokat is gyűjti a neurális hálózat betanításához.

A Head számítás egyetlen [ `STANDARD_NC6` virtuális gépet](../virtual-machines/nc-series.md) (VM) használ. 6 virtuális processzorral rendelkezik a munka elosztásához.


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>Feldolgozói számítástechnikai fürt

Ez a példa négy [ `STANDARD_D2_V2` virtuális](../virtual-machines/nc-series.md) gépet használ a munkavégző számítási célra. Minden munkavégző csomópont 2 rendelkezésre álló processzorral rendelkezik, összesen 8 rendelkezésre álló CPU-hoz.

A GPU-k nem szükségesek a munkavégző csomópontok számára, mivel nem rendelkeznek mély tanulással. A feldolgozók futtatják a játék szimulációit, és adatokat gyűjtenek.

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>Megerősítő tanulási kalkulátor létrehozása
A [ReinforcementLearningEstimator](/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator) használatával betanítási feladatot küldhet Azure Machine Learningnak.

Azure Machine Learning a kalkulátor osztályokat használja a futtatási konfigurációs adatok beágyazásához. Ez lehetővé teszi a parancsfájlok végrehajtásának konfigurálását. 

### <a name="define-a-worker-configuration"></a>Munkavégző konfiguráció megadása

A WorkerConfiguration objektum közli Azure Machine Learning a beléptetési parancsfájlt futtató munkavégző fürt inicializálását.

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>Parancsfájl paramétereinek megadása

A bejegyzési parancsfájl `pong_rllib.py` elfogadja a betanítási feladatok végrehajtásának módját meghatározó paraméterek listáját. Ha ezeket a paramétereket a kalkulátoron keresztül adja át, a beágyazás rétege megkönnyíti a parancsfájlok paramétereinek módosítását és a konfigurációk egymástól független futtatását.

A helyes beállítás megadásával `num_workers` a lehető legtöbbet hozhatja ki a párhuzamos. Állítsa be a feldolgozók számát a rendelkezésre álló processzorok számával megegyező értékre. Ehhez a példához a következő számítást használhatja:

A fő csomópont egy 6 vCPU rendelkező [Standard_NC6](../virtual-machines/nc-series.md) . A munkavégző fürt 4 [Standard_D2_V2 virtuális gép](../cloud-services/cloud-services-sizes-specs.md#dv2-series) 2 processzorral, összesen 8 processzorral. Azonban ki kell vonnia az 1 PROCESSZORt a munkavégzők számáról, mivel az 1 elemet a fő csomóponti szerepkörhöz kell hozzárendelni.

6 processzor + 8 processzor – 1 fő CPU = 13 egyidejű feldolgozók. Azure Machine Learning a fő-és feldolgozói fürtöket használja a számítási erőforrások megkülönböztetéséhez. A Ray azonban nem különbözteti meg a fej és a feldolgozók között, és az összes CPU munkavégző szálként érhető el.


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>A megerősítési tanulási kalkulátor meghatározása

A kalkulátor létrehozásához használja a paraméterek listáját és a Worker konfigurációs objektumot.

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>Bejegyzési parancsfájl

A [bejegyzési parancsfájl](https://aka.ms/azure-rl-pong-script) `pong_rllib.py` egy neurális hálózatot hajt a [OpenAI Gym-környezettel](https://github.com/openai/gym/) `PongNoFrameSkip-v4` . A OpenAI-tornatermek szabványosított felületek a megerősítő tanulási algoritmusok tesztelésére a klasszikus Atari-játékoknál.

Ez a példa [Impala](https://arxiv.org/abs/1802.01561) -ként ismert képzési algoritmust használ (fontosság súlyozott Actor-Learner architektúra). A IMPALA parallelizes az egyes tanulók számos számítási csomópontra kiterjedő skáláját a sebesség vagy a stabilitás feláldozása nélkül.

A [Ray Tune összehangolja](https://ray.readthedocs.io/en/latest/tune.html) az Impala Worker feladatait.

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>Naplózási visszahívás funkció


A bejegyzési parancsfájl egy segédprogram-függvényt használ egy [Egyéni RLlib visszahívási függvény](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics) definiálásához a metrikák a Azure Machine learning munkaterületre történő naplózásához. Megtudhatja, hogyan tekintheti meg ezeket a mérőszámokat a [figyelés és az eredmények megtekintése](#monitor-and-view-results) szakaszban.

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>Futtatás küldése

A [Run](/python/api/azureml-core/azureml.core.run%28class%29) a folyamatban lévő vagy a Befejezett feladatok futtatási előzményeit kezeli. 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> A Futtatás akár 30 – 45 percet is igénybe vehet.

## <a name="monitor-and-view-results"></a>Eredmények monitorozása és megtekintése

A futtatások állapotának valós idejű megjelenítéséhez használja a Azure Machine Learning Jupyter widgetet. A widget két alárendelt futtatást mutat be: egyet a Head és egy for Worker számára. 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. Várjon, amíg betöltődik a widget.
1. A futtatások listájában válassza ki a futtatni kívánt Head parancsot.

Válassza **a kattintson ide a futtatás Azure Machine learning Studióban** a további futtatási információk a Studióban című témakört. Ezeket az információkat a Futtatás folyamatban vagy a befejezést követően is elérheti.

![A Run details widgetet bemutató line Graph](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

Az **episode_reward_mean** ábrán látható, hogy milyen számú pontot kell kiszámítani a képzési korszakban. Láthatja, hogy az oktatási ügynök kezdetben rosszul lett elvégezve, és a egyezésük elvesztése nélkül megszakadt (a-21 reward_mean). Az 100-es ismétléseken belül a képzési ügynök megtanulta, hogy átlagosan 18 ponttal megverte a számítógépes ellenfelet.

Ha a gyermek által futtatott naplókat böngészi, láthatja driver_log.txt fájlban rögzített kiértékelési eredményeket. Előfordulhat, hogy néhány percet várnia kell, amíg a metrikák elérhetővé válnak a Futtatás oldalon.

Rövid idő alatt több számítási erőforrást is megtanult, hogy betanítsa a megerősítő tanulási ügynököt, hogy nagyon jól játszhasson a oppponent a számítógépeken.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan taníthat meg egy megerősítéses oktatási ügynököt egy IMPALA learning-ügynök használatával. Ha további példákat szeretne látni, ugorjon a [Azure Machine learning megerősítése learning GitHub-tárházra](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md).