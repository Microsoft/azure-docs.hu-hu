---
title: Modellek üzembe helyezése egyéni Docker-lemezképpel
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezhet üzembe a Azure Machine Learning-modelleket egyéni Docker-alaplemezkép használatával. Míg Azure Machine Learning alapértelmezett alapképet biztosít Önnek, a saját alaprendszerképét is használhatja.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 440723167ded15933ba46a3a58f9a873496bd6a7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609362"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Modell üzembe helyezése egyéni Docker-alapú rendszerkép használatával

Megtudhatja, hogyan használhat egyéni Docker-alapképet a betanított modellek Azure Machine Learning használatával történő telepítésekor.

Ha nincs megadva, a Azure Machine Learning alapértelmezett alapszintű Docker-rendszerképet fog használni. Megtalálhatja a által használt Docker-rendszerképet `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . Azure Machine Learning- __környezeteket__ is használhat egy adott alaprendszerkép kiválasztásához, vagy használhat egy Ön által megadott egyéni rendszerképet.

Alapszintű rendszerképet kell használni a központi telepítés rendszerképének létrehozásakor. A mögöttes operációs rendszert és összetevőket tartalmazza. A telepítési folyamat ezután további összetevőket, például a modellt, a Conda-környezetet és más eszközöket is felépít a rendszerképbe.

Általában egyéni alapképet kell létrehoznia, ha a Docker használatával kezeli a függőségeket, megtarthatja az összetevő-verziók szigorúbb szabályozását, vagy időt takaríthat meg az üzembe helyezés során. Előfordulhat, hogy a modellhez szükséges szoftvert is telepítenie kell, ahol a telepítési folyamat hosszú időt vesz igénybe. Ha az alaprendszerkép létrehozásakor telepíti a szoftvert, azt jelenti, hogy nem kell telepítenie az összes központi telepítéshez.

> [!IMPORTANT]
> Modell telepítésekor nem bírálhatja felül a fő összetevőket, például a webkiszolgálót vagy a IoT Edge összetevőket. Ezek az összetevők olyan ismert munkahelyi környezetet biztosítanak, amelyet a Microsoft tesztelt és támogat.

> [!WARNING]
> Előfordulhat, hogy a Microsoft nem tud segíteni az egyéni rendszerkép által okozott problémák elhárításában. Ha problémákba ütközik, előfordulhat, hogy az alapértelmezett lemezképet vagy a Microsoft által biztosított lemezképek valamelyikét kell használnia annak ellenőrzéséhez, hogy a probléma kifejezetten a lemezképre vonatkozik-e.

Ez a dokumentum két részre oszlik:

* Egyéni alaprendszerkép létrehozása: információt nyújt a rendszergazdáknak és a DevOps az egyéni rendszerkép létrehozásával és a hitelesítés konfigurálásával Azure Container Registry az Azure CLI és a Machine Learning parancssori felület használatával.
* Modell üzembe helyezése egyéni alapképpel: az adatszakértők és a DevOps/ML-mérnökök számára biztosítanak egyéni rendszerképeket, amikor a Python SDK-ból vagy a ML CLI-ből helyez üzembe egy betanított modellt.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információt a [Munkaterület létrehozása](how-to-manage-workspace.md) című cikkben talál.
* A [Azure Machine learning SDK](/python/api/overview/azure/ml/install). 
* Az [Azure CLI](/cli/azure/install-azure-cli)-vel.
* A [Azure Machine learning CLI-bővítménye](reference-azure-machine-learning-cli.md).
* Az interneten elérhető [Azure Container Registry](../container-registry/index.yml) vagy más Docker-beállításjegyzék.
* A jelen dokumentum lépései azt feltételezik, hogy a modell üzembe helyezésének részeként már ismeri a __következtetési konfigurációs__ objektum létrehozását és használatát. További információ: [hol kell telepíteni, és hogyan](how-to-deploy-and-where.md).

## <a name="create-a-custom-base-image"></a>Egyéni alaprendszerkép létrehozása

Az ebben a szakaszban található információk azt feltételezik, hogy Azure Container Registry használ a Docker-rendszerképek tárolásához. A következő ellenőrzőlista használható egyéni rendszerképek létrehozásához Azure Machine Learninghoz:

* A Azure Machine Learning munkaterülethez vagy önálló Azure Container Registryhoz létrehozott Azure Container Registry fogja használni?

    Ha a __munkaterülethez tartozó tároló beállításjegyzékében__ tárolt rendszerképeket használ, nem kell hitelesítenie magát a beállításjegyzékben. A hitelesítést a munkaterület kezeli.

    > [!WARNING]
    > A munkaterülethez tartozó Azure Container Registry akkor jön létre, amikor a munkaterületen __először betanít vagy telepít egy modellt__ . Ha létrehozott egy új munkaterületet, de nem képzett vagy nem hozott létre modellt, akkor a munkaterülethez nem tartozik Azure Container Registry.

    __Önálló tároló-beállításjegyzékben__ tárolt rendszerképek használata esetén konfigurálnia kell egy egyszerű szolgáltatásnevet, amely legalább olvasási hozzáféréssel rendelkezik. Ezután megadhatja az egyszerű szolgáltatás AZONOSÍTÓját (felhasználónevét) és jelszavát, amely a beállításjegyzékből származó lemezképeket használ. Ez alól kivételt képez, ha nyilvánosan elérhetővé teszi a tároló-beállításjegyzéket.

    A privát Azure Container Registry létrehozásával kapcsolatos információkért lásd: [saját tároló beállításjegyzékének létrehozása](../container-registry/container-registry-get-started-azure-cli.md).

    További információ az egyszerű szolgáltatások Azure Container Registry használatával történő használatáról: [Azure Container Registry hitelesítés az egyszerű szolgáltatásokkal](../container-registry/container-registry-auth-service-principal.md).

* Azure Container Registry-és képinformációk: adja meg a rendszerkép nevét a használni kívánt felhasználók számára. Például egy `myimage` nevű beállításjegyzékbeli nevű rendszerképet `myregistry` `myregistry.azurecr.io/myimage` a rendszer a rendszerképnek a modell központi telepítésére való használatakor hivatkozik.

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A Azure Machine Learning csak a következő szoftvereket biztosító Docker-rendszerképeket támogatja:
* Ubuntu 16,04 vagy újabb.
* Conda 4.5. # vagy nagyobb.
* Python 3.5 +.

Az adatkészletek használatához telepítse a libfuse-dev csomagot. Győződjön meg arról is, hogy minden szükséges felhasználói tárterület-csomagot telepít.

Az Azure ML a Microsoft Container Registry közzétett CPU-és GPU-lemezképek készletét is fenntarthatja, hogy a saját egyéni rendszerkép létrehozása helyett opcionálisan (vagy hivatkozásként) is kihasználhatja. A képek Dockerfiles megtekintéséhez tekintse meg az [Azure/AzureML-containers GitHub-](https://github.com/Azure/AzureML-Containers) tárházat.

A GPU-lemezképek esetében az Azure ML jelenleg a cuda9 és a cuda10 alaprendszerképét is biztosítja. Az alaplemezképekben telepített fő függőségek a következők:

| Függőségek | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 |
| MPI | intelmpi = = 2018.3.222 |openmpi = = 3.1.2 |intelmpi = = 2018.3.222| openmpi = = 3.1.2 |
| CUDA | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2,4 | 2,4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

A CPU-lemezképek Ubuntu-16.04 épülnek. A cuda9 készült GPU-lemezképek NVIDIA/CUDA: 9.0-cudnn7-devel-Ubuntu 16.04 épülnek. A cuda10 tartozó GPU-lemezképek NVIDIA/CUDA: 10.0-cudnn7-devel-Ubuntu 16.04 épülnek.
<a id="getname"></a>

> [!IMPORTANT]
> Egyéni Docker-rendszerképek használata esetén ajánlott a csomagok verzióinak rögzítése a reprodukálhatóság jobb biztosítása érdekében.

### <a name="get-container-registry-information"></a>Tároló beállításjegyzék-adatainak beolvasása

Ebből a szakaszból megtudhatja, hogyan kérheti le a Azure Machine Learning munkaterülethez tartozó Azure Container Registry nevét.

> [!WARNING]
> A munkaterülethez tartozó Azure Container Registry akkor jön létre, amikor a munkaterületen __először betanít vagy telepít egy modellt__ . Ha létrehozott egy új munkaterületet, de nem képzett vagy nem hozott létre modellt, akkor a munkaterülethez nem tartozik Azure Container Registry.

Ha már betanított vagy telepített modelleket Azure Machine Learning használatával, a rendszer létrehoz egy tároló-beállításjegyzéket a munkaterülethez. A tároló-beállításjegyzék nevének megkereséséhez kövesse az alábbi lépéseket:

1. Nyisson meg egy új rendszerhéjt vagy egy parancssort, és az alábbi paranccsal hitelesítheti magát az Azure-előfizetésében:

    ```azurecli-interactive
    az login
    ```

    Az előfizetések hitelesítéséhez kövesse az utasításokat.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Használja a következő parancsot a munkaterület tároló-beállításjegyzékének listázásához. Cserélje le a helyére a `<myworkspace>` Azure Machine learning munkaterület nevét. Cserélje le a helyére `<resourcegroup>` a munkaterületet tartalmazó Azure-erőforráscsoportot:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    A visszaadott információ az alábbi szöveghez hasonló:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Az `<registry_name>` érték a munkaterület Azure Container Registry neve.

### <a name="build-a-custom-base-image"></a>Egyéni alaprendszerkép létrehozása

Az ebben a szakaszban ismertetett lépések végigvezetik az egyéni Docker-rendszerkép létrehozásán a Azure Container Registry. A minta dockerfiles tekintse meg az [Azure/AzureML-containers GitHub-](https://github.com/Azure/AzureML-Containers) tárházat.

1. Hozzon létre egy nevű új szövegfájlt `Dockerfile` , és használja a következő szöveget a tartalomként:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Egy rendszerhéjból vagy parancssorból a következő paranccsal hitelesítheti magát a Azure Container Registry. A helyére írja be annak a `<registry_name>` tároló-beállításjegyzéknek a nevét, amelyben a rendszerképet tárolni szeretné:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. A Docker feltöltéséhez és a létrehozásához használja a következő parancsot. A helyére írja be annak a `<registry_name>` tároló-beállításjegyzéknek a nevét, amelyben a rendszerképet tárolni szeretné:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > Ebben a példában egy címkét `:v1` alkalmazunk a képre. Ha nincs megadva címke, a rendszer a címkét `:latest` alkalmazza.

    A létrehozási folyamat során a rendszer továbbítja az adatokat a parancssorba. Ha a Build sikeres, az alábbihoz hasonló üzenet jelenik meg:

    ```text
    Run ID: cda was successful after 2m56s
    ```

A rendszerképek Azure Container Registry használatával történő létrehozásával kapcsolatos további információkért lásd: [tároló lemezképének létrehozása és futtatása Azure Container Registry feladatokkal](../container-registry/container-registry-quickstart-task-cli.md)

A meglévő lemezképek Azure Container Registryra való feltöltésével kapcsolatos további információkért lásd: [az első rendszerkép leküldése egy privát Docker-tároló beállításjegyzékbe](../container-registry/container-registry-get-started-docker-cli.md).

## <a name="use-a-custom-base-image"></a>Egyéni alaprendszerkép használata

Egyéni rendszerkép használatához a következő információk szükségesek:

* A __rendszerkép neve__. Például `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` a Microsoft által biztosított egyszerű Docker-rendszerkép elérési útja.

    > [!IMPORTANT]
    > A létrehozott egyéni lemezképek esetében ügyeljen arra, hogy tartalmazza a képhez használt címkéket. Például, ha a rendszerkép egy adott címkével lett létrehozva, például: `:v1` . Ha nem adott meg címkét a rendszerkép létrehozásakor, a rendszer egy címkét `:latest` alkalmazott.

* Ha a rendszerkép egy __privát tárházban__ található, a következő információkra lesz szüksége:

    * A beállításjegyzék __címe__. Például: `myregistry.azureecr.io`.
    * Egy egyszerű szolgáltatásnév __felhasználóneve__ és __jelszava__ , amely olvasási hozzáféréssel rendelkezik a beállításjegyzékhez.

    Ha nem rendelkezik ezekkel az információkkal, beszéljen a rendszerképet tartalmazó Azure Container Registry rendszergazdájához.

### <a name="publicly-available-base-images"></a>Nyilvánosan elérhető alaplemezképek

A Microsoft számos Docker-rendszerképet biztosít egy nyilvánosan elérhető adattáron, amely az ebben a szakaszban ismertetett lépésekkel használható:

| Kép | Description |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning alaprendszerképe |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | A CPU-következtetések ONNX-futtatókörnyezetét tartalmazza |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | A ONNX futtatókörnyezetet és a CUDA-t tartalmazza a GPU-hoz |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | ONNX Runtime és TensorRT for GPU-t tartalmaz |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Az ONNX Runtime és a OpenVINO for Intel <sup></sup> látási gyorssegéd kialakítását tartalmazza a Movidius<sup>TM</sup> MyriadX VPUs alapján |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Az ONNX Runtime és a OpenVINO for Intel <sup></sup> Movidius<sup>TM</sup> USB-Sticks szolgáltatását tartalmazza |

A ONNX Runtime alaplemezképekkel kapcsolatos további információkért lásd a GitHub-tárház [ONNX Runtime Docker szakaszát](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) .

> [!TIP]
> Mivel ezek a lemezképek nyilvánosan elérhetők, nem kell megadnia a szükséges címeket, felhasználónevet vagy jelszót.

További információ: [Azure Machine learning containers](https://github.com/Azure/AzureML-Containers) repository a githubon.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Rendszerkép használata az Azure Machine Learning SDK-val

Ha a **munkaterülethez Azure Container Registry** tárolt képet vagy egy **nyilvánosan elérhető tároló-beállításjegyzéket** szeretne használni, állítsa be a következő [környezeti](/python/api/azureml-core/azureml.core.environment.environment) attribútumokat:

+ `docker.enabled=True`
+ `docker.base_image`: Állítsa a beállításjegyzékre és a rendszerkép elérési útjára.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Ha olyan rendszerképet szeretne használni a __saját tároló-beállításjegyzékből__ , amely nem szerepel a munkaterületén, a használatával `docker.base_image_registry` meg kell adnia a tárház és a Felhasználónév és jelszó nevét:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Pip-függőségként hozzá kell adnia a azureml (>= 1.0.45) alapértelmezett verzióját. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat. A környezet inferencing_stack_version tulajdonságát a "legújabb" értékre kell állítania, ez a webszolgáltatás által igényelt speciális apt-csomagokat fogja telepíteni. 

A környezet meghatározása után használja azt egy [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) objektummal annak a következtetési környezetnek a meghatározásához, amelyben a modell és a webszolgáltatás futni fog.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Ezen a ponton folytathatja a telepítést. A következő kódrészlet például egy webszolgáltatást helyez üzembe helyileg a következtetési konfiguráció és az egyéni rendszerkép használatával:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

A telepítéssel kapcsolatos további információkért lásd: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

A Python-környezet testreszabásával kapcsolatos további információkért lásd: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md). 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Rendszerkép használata a Machine Learning parancssori felülettel

> [!IMPORTANT]
> A Machine Learning CLI jelenleg a munkaterülethez vagy a nyilvánosan elérhető adattárakhoz tartozó Azure Container Registry képeket is használhat. Önálló privát beállításjegyzékből származó lemezképeket nem használhat.

Mielőtt a Machine Learning parancssori felület használatával telepítené a modellt, hozzon létre egy [környezetet](/python/api/azureml-core/azureml.core.environment.environment) , amely az egyéni rendszerképet használja. Ezután hozzon létre egy következtetési konfigurációs fájlt, amely hivatkozik a környezetre. A környezetet közvetlenül is megadhatja a következtetési konfigurációs fájlban. A következő JSON-dokumentum bemutatja, hogyan hivatkozhat egy rendszerképre egy nyilvános tároló beállításjegyzékében. Ebben a példában a környezet beágyazottként van definiálva:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Ezt a fájlt a paranccsal lehet használni `az ml model deploy` . A `--ic` paraméter a következtetési konfigurációs fájl megadására szolgál.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

A modellek ML parancssori felülettel történő üzembe helyezésével kapcsolatos további információkért tekintse meg [Azure Machine learning cikk CLI-bővítményének](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) "modell regisztrálása, profilkészítés és központi telepítése" szakaszát.

## <a name="next-steps"></a>Következő lépések

* További információ a [telepítéséről és a módjáról](how-to-deploy-and-where.md).
* Ismerje meg, hogyan lehet [gépi tanulási modelleket betanítani és üzembe helyezni az Azure-folyamatok használatával](/azure/devops/pipelines/targets/azure-machine-learning).