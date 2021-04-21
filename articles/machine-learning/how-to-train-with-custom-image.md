---
title: Modell betanítása egyéni Docker-rendszerkép használatával
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a saját Docker-rendszerképeket vagy a Microsofttól származó összecsukott rendszerképeket modellek betanítása Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 953d43f93635e25da008515afd9baf9a9e9b7afa
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817070"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Modell betanítása egyéni Docker-rendszerkép használatával

Ebből a cikkből megtudhatja, hogyan használhatja az egyéni Docker-rendszerképeket, amikor modelleket Azure Machine Learning. Ebben a cikkben a példaszk szkriptek használatával osztályozhatja a kisállatok képeit egy konvolúciós neurális hálózat létrehozásával. 

Azure Machine Learning alapértelmezett Alap Docker-rendszerképet biztosít. Az alapként Azure Machine Learning is megadhat egy másik alapként megadott rendszerképet, például az egyik karbantartott Azure Machine Learning rendszerképet vagy a saját egyéni [](https://github.com/Azure/AzureML-Containers) rendszerképét. [](how-to-deploy-custom-docker-image.md#create-a-custom-base-image) Az egyéni alapként használható rendszerképek lehetővé teszik a függőségek szoros kezelését és az összetevők verzióinak szigorúbb szabályozását a betanító feladatok futtatásakor.

## <a name="prerequisites"></a>Előfeltételek

Futtassa a kódot az alábbi környezetek egyikében:

* Azure Machine Learning számítási példány (nincs szükség letöltésre vagy telepítésre):
  * A Környezet [és munkaterület beállítása](tutorial-1st-experiment-sdk-setup.md) oktatóanyagban egy, az SDK-val és a mintaadattáraval előre feltöltött dedikált notebookkiszolgálót hozhat létre.
  * A Azure Machine Learning adattárban keresse meg a kész jegyzetfüzetet a [](https://github.com/Azure/azureml-examples)   >  **fastai**  >  **train-pets-resnet34.ipynb** könyvtárban. 
* Saját Jupyter Notebook kiszolgáló:
  * Hozzon létre egy [munkaterület-konfigurációs fájlt.](how-to-configure-environment.md#workspace)
  * Telepítse az [Azure Machine Learning SDK-t.](/python/api/overview/azure/ml/install) 
  * Hozzon létre [egy Azure-beli](../container-registry/index.yml) tároló-beállításjegyzéket vagy más, az interneten elérhető Docker-beállításjegyzéket.

## <a name="set-up-a-training-experiment"></a>Betanításos kísérlet beállítása

Ebben a szakaszban egy munkaterület inicializálásával, a környezet meghatározásával és egy számítási cél konfigurálásával állíthatja be a betanítás kísérletét.

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine Learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központosított helyet biztosít az összes létrehozott összetevővel való munkához. A Python SDK-ban objektum létrehozásával férhet hozzá a [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) munkaterület-összetevőkhez.

Hozzon létre egy objektumot a config.jsaz előfeltételként létrehozott `Workspace` fájlban. [](#prerequisites)

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>A környezet meghatározása

Hozzon létre `Environment` egy objektumot, és engedélyezze a Docker-t.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

A következő kódban megadott alapként megadott rendszerkép támogatja fast.ai kódtárat, amely elosztott mély tanulási képességeket tesz lehetővé. További információ: fast.ai Docker Hub [adattár.](https://hub.docker.com/u/fastdotai) 

Az egyéni Docker-rendszerkép használata esetén előfordulhat, hogy már megfelelően beállította a Python-környezetet. Ebben az esetben állítsa a jelzőt a következőre: az egyéni rendszerkép beépített `user_managed_dependencies` `True` Python-környezetének használatához. Alapértelmezés szerint a Azure Machine Learning Conda-környezetet hoz létre a megadott függőségekkel. A szolgáltatás az alapként használt rendszerképre telepített Python-kódtárak használata helyett ebben a környezetben futtatja a szkriptet.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Privát tároló-beállításjegyzék használata (nem kötelező)

Ha a munkaterületen nem található privát tároló-beállításjegyzékből származó rendszerképet használ, a használatával adja meg az adattár címét, valamint egy felhasználónevet és `docker.base_image_registry` jelszót:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Egyéni Docker-fájl használata (nem kötelező)

Egyéni Docker-fájl is használható. Akkor használja ezt a módszert, ha nem Python-csomagokat kell függőségként telepítenie. Ne felejtse el az alapként beállított rendszerképet a következőre állítani: `None` .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> Azure Machine Learning csak azokat a Docker-lemezképeket támogatja, amelyek a következő szoftvereket biztosítják:
> * Ubuntu 16.04 vagy nagyobb.
> * Conda 4.5.# vagy nagyobb.
> * Python 3.6+.

További információ az új környezetek létrehozásáról és Azure Machine Learning: [Szoftverkörnyezetek létrehozása és használata.](how-to-use-environments.md) 

### <a name="create-or-attach-a-compute-target"></a>Számítási cél létrehozása vagy csatolása

Létre kell hoznia egy [számítási célt](concept-azure-machine-learning-architecture.md#compute-targets) a modell betanításhoz. Ebben az oktatóanyagban betanítás `AmlCompute` számítási erőforrásként hoz létre.

A létrehozása `AmlCompute` eltarthat néhány percig. Ha az `AmlCompute` erőforrás már a munkaterületen van, ez a kód kihagyja a létrehozási folyamatot.

Ahogy más Azure-szolgáltatások esetében, itt is bizonyos erőforrásokra (például ) korlátozások vonatkoznak, `AmlCompute` amelyek a Azure Machine Learning service. További információ: Alapértelmezett [korlátok és magasabb kvóta kérése.](how-to-manage-quotas.md)

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>A betanítás feladatának konfigurálása

Ebben az oktatóanyagban használja a *githubon train.py* [szkriptet.](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py) A gyakorlatban bármilyen egyéni betanító szkriptet futtathat és futtathat a Azure Machine Learning.

Hozzon létre egy erőforrást, amely konfigurálja a feladatot a kívánt számítási `ScriptRunConfig` [célon való futtatásra.](how-to-set-up-training-targets.md)

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>A betanítás feladatának elküldése

Amikor egy objektummal küld el egy betanításfuttat, a `ScriptRunConfig` metódus egy típusú objektumot `submit` ad `ScriptRun` vissza. A `ScriptRun` visszaadott objektum programozott hozzáférést biztosít a betanítás futtatásával kapcsolatos információkhoz. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning a teljes forráskönyvtár másolása által futtatja a betanító szkripteket. Ha bizalmas adatokat nem szeretne feltölteni, használjon [.ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) fájlt, vagy ne foglalja bele őket a forráskönyvtárba. Ehelyett egy adattár használatával férhet hozzá az [adataihoz.](/python/api/azureml-core/azureml.data)

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben betanított egy modellt egy egyéni Docker-rendszerkép használatával. Az alábbi cikkekben további információt talál a Azure Machine Learning:
* [Futtatásmetrikák nyomon követése](how-to-log-view-metrics.md) a betanítás során.
* [Modell üzembe helyezése](how-to-deploy-custom-docker-image.md) egyéni Docker-rendszerkép használatával.
