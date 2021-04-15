---
title: Modellek üzembe helyezése egyéni Docker-rendszerképekkel
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti üzembe az egyéni Docker-alapként használt rendszerképet a Azure Machine Learning létrehozásához. Bár Azure Machine Learning alapértelmezett alapként használt rendszerképet biztosít, saját alapként használt rendszerképet is használhat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: f621bb2a7d4543620d22ab85fb8b44752c9989ac
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376256"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Modell üzembe helyezése egyéni Docker-alapként használt rendszerkép használatával

Megtudhatja, hogyan használhatja az egyéni Docker-alapként használt rendszerképet a betanított modellek üzembe helyezésekor a Azure Machine Learning.

Azure Machine Learning alapértelmezett alap Docker-rendszerképet fog használni, ha nincs megadva. A hez használt Docker-rendszerképet itt `azureml.core.runconfig.DEFAULT_CPU_IMAGE` találja: . A saját Azure Machine Learning __is__ használhat egy adott alapként megadott rendszerképet, vagy használhat egy ön által megadott egyéni rendszerképet.

Az alapként használt rendszerkép kiindulási pontként használható, amikor rendszerképet hoz létre az üzembe helyezéshez. Ez biztosítja a mögöttes operációs rendszert és összetevőket. Az üzembe helyezési folyamat ezután további összetevőket, például a modellt, a Conda-környezetet és más eszközöket ad hozzá a rendszerképhez.

Az egyéni alapként használt rendszerképet általában akkor kell létrehozni, ha a Docker használatával szeretné kezelni a függőségeket, szigorúbban szabályozni az összetevők verzióit, vagy időt takarít meg az üzembe helyezés során. Érdemes lehet a modellhez szükséges szoftvereket is telepíteni, ahol a telepítési folyamat hosszú ideig tart. Az alapként használt rendszerkép létrehozásakor a szoftver telepítése azt jelenti, hogy nem kell minden üzemelő példányhoz telepítenie.

> [!IMPORTANT]
> Modell üzembe helyezésekor nem bírálhatja felül az olyan alapvető összetevőket, mint a webkiszolgáló vagy IoT Edge összetevők. Ezek az összetevők egy ismert munkakörnyezetet biztosítanak, amelyet a Microsoft tesztelt és támogat.

> [!WARNING]
> Előfordulhat, hogy a Microsoft nem tud segíteni az egyéni rendszerkép által okozott problémák elhárításában. Ha problémákba ütközik, előfordulhat, hogy a rendszer arra kéri, hogy használja az alapértelmezett rendszerképet vagy a Microsoft által megadott rendszerképeket, hogy lássa, a probléma csak az Ön rendszerképére jellemző-e.

Ez a dokumentum két szakaszra van osztva:

* Egyéni alapként használt rendszerkép létrehozása: Információt nyújt a rendszergazdáknak és a DevOpsnak az egyéni rendszerkép létrehozásáról és a hitelesítés Azure Container Registry azure CLI-ről és Machine Learning konfigurálásról.
* Modell üzembe helyezése egyéni alapként használt rendszerkép használatával: Információt nyújt az adatszakértőknek és DevOps-/ML-mérnököknek az egyéni rendszerképek használatával kapcsolatban, amikor betanított modellt helyez üzembe a Python SDK-ból vagy az ML CLI-ről.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információt a Munkaterület létrehozása [cikkben](how-to-manage-workspace.md) talál.
* A [Azure Machine Learning SDK.](/python/api/overview/azure/ml/install) 
* Az [Azure CLI.](/cli/azure/install-azure-cli)
* A [cli-bővítmény a következő Azure Machine Learning:](reference-azure-machine-learning-cli.md).
* Egy [Azure Container Registry](../container-registry/index.yml) vagy más Docker-beállításjegyzék, amely elérhető az interneten.
* A dokumentumban található lépések feltételezik, hogy Ön már ismeri __egy__ következtetési konfigurációs objektum létrehozását és a modell üzembe helyezésének részeként való használatának tapasztalatát. További információ: [A telepítés helyének és a telepítésének mikénte.](how-to-deploy-and-where.md)

## <a name="create-a-custom-base-image"></a>Egyéni alapként használt rendszerkép létrehozása

Az ebben a szakaszban található információk feltételezik, hogy docker Azure Container Registry tároló rendszerképet használ. Használja a következő ellenőrzőlistát, amikor egyéni rendszerképeket tervez létrehozni a Azure Machine Learning:

* Használni fogja a Azure Container Registry munkaterülethez létrehozott Azure Machine Learning vagy egy önálló Azure Container Registry?

    Ha a munkaterület tárolójegyzékében tárolt rendszerképeket __használ,__ nem kell hitelesítenie magát a beállításjegyzékben. A hitelesítést a munkaterület kezeli.

    > [!WARNING]
    > A Azure Container Registry első betanításkor vagy üzembe helyezéskor létrejön __a__ munkaterülethez használt modell. Ha létrehozott egy új munkaterületet, de nem képezte be vagy hozta létre a modellt, a Azure Container Registry nem lesz új munkaterülethez.

    Ha önálló tároló-beállításjegyzékben tárolt rendszerképeket __használ,__ konfigurálnia kell egy olyan szolgáltatásnévvel, amely legalább olvasási hozzáféréssel rendelkezik. Ezután meg kell adnia a szolgáltatásnév azonosítóját (felhasználónevét) és jelszavát minden olyan felhasználónak, aki rendszerképeket használ a beállításjegyzékből. Ez alól kivételt képez, ha nyilvánosan elérhetővé teszi a tároló-beállításjegyzéket.

    A privát tárolójegyzékek létrehozásával kapcsolatos Azure Container Registry [lásd: Privát tároló-beállításjegyzék létrehozása.](../container-registry/container-registry-get-started-azure-cli.md)

    További információ a szolgáltatásnév szolgáltatásnévvel való Azure Container Registry: Azure Container Registry [szolgáltatásnévvel való hitelesítés.](../container-registry/container-registry-auth-service-principal.md)

* Azure Container Registry és képinformációk: Adja meg a rendszerkép nevét bárkinek, akinek használnia kell. A nevű beállításjegyzékben tárolt rendszerképre például a modell üzembe helyezéséhez használt rendszerképre `myimage` `myregistry` `myregistry.azurecr.io/myimage` hivatkozik.

### <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

Azure Machine Learning csak azokat a Docker-rendszerképeket támogatja, amelyek a következő szoftvereket biztosítják:
* Ubuntu 16.04 vagy nagyobb.
* Conda 4.5.# vagy nagyobb.
* Python 3.6+.

Az Adatkészletek használata érdekében telepítse a libfuse-dev csomagot. Ügyeljen arra is, hogy telepítse az esetlegesen szükséges felhasználóitér-csomagokat.

Az Azure ML olyan processzor- és GPU-alapként beállított rendszerképeket tart fenn, Microsoft Container Registry egyéni rendszerképek létrehozása helyett igény szerint kihasználhatja (vagy hivatkozhat rá). A lemezképek Docker-fájlját az [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub-adattárban találja.

GPU-rendszerképek esetén az Azure ML jelenleg cuda9- és cuda10-alapképeket is kínál. Az alapként telepített rendszerképek fő függőségei a következőek:

| Függőségek | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | ==4.5.11 | ==4.5.11 | ==4.5.11 | ==4.5.11 |
| mpi | intelmpi==2018.3.222 |openmpi==3.1.2 |intelmpi==2018.3.222| openmpi==3.1.2 |
| Cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2,4 | 2,4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

A CPU-lemezképek ubuntu16.04-ről vannak felépítve. A cuda9 GPU-lemezképek nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04-ről készültek. A cuda10 GPU-lemezképek nvidia/cuda:10.0-cudnnn7-devel-ubuntu16.04-ről készültek.
<a id="getname"></a>

> [!IMPORTANT]
> Egyéni Docker-rendszerképek használata esetén ajánlott a csomagverziók kitűzése a reprodukálhatóság jobb biztosítása érdekében.

### <a name="get-container-registry-information"></a>Tároló-beállításjegyzék-információk lekérte

Ebben a szakaszban megtudhatja, hogyan olvashatja be a Azure Container Registry munkaterülethez Azure Machine Learning nevét.

> [!WARNING]
> A Azure Container Registry létrehozása akkor jön létre, amikor először betanít vagy helyez üzembe __egy modellt a__ munkaterület használatával. Ha létrehozott egy új munkaterületet, de nem képezte be vagy nem hozott létre modellt, a Azure Container Registry nem fog létezni új munkaterülethez.

Ha már betanított vagy üzembe helyezett modelleket a Azure Machine Learning, létrejött egy tároló-beállításjegyzék a munkaterülethez. A tároló-beállításjegyzék nevének megkereséhez kövesse az alábbi lépéseket:

1. Nyisson meg egy új rendszerhéjat vagy parancssort, és használja a következő parancsot az Azure-előfizetésben való hitelesítéshez:

    ```azurecli-interactive
    az login
    ```

    Kövesse az utasításokat az előfizetésben való hitelesítéshez.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. A következő paranccsal listába sorolja a munkaterület tároló-beállításjegyzékét. Cserélje `<myworkspace>` le a helyére Azure Machine Learning munkaterület nevét. Cserélje `<resourcegroup>` le a helyére a munkaterületet tartalmazó Azure-erőforráscsoportot:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    A visszaadott információk a következő szöveghez hasonlók:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Az `<registry_name>` érték a munkaterülethez Azure Container Registry név.

### <a name="build-a-custom-base-image"></a>Egyéni alapként létrehozott rendszerkép létrehozása

Az ebben a szakaszban található lépések egy egyéni Docker-rendszerkép létrehozását ják Azure Container Registry. Minta dockerfile-okat az [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub-adattárban találja.

1. Hozzon létre egy nevű új szövegfájlt, és használja a `Dockerfile` következő szöveget tartalomként:

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

2. Egy rendszerhéjból vagy parancssorból a következővel hitelesítheti magát a Azure Container Registry. Cserélje le a helyére annak a tárolójegyzéknek a `<registry_name>` nevét, amelyben a lemezképet tárolni szeretné:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. A Dockerfile feltöltéshez és buildhez használja a következő parancsot. Cserélje le a helyére annak a tárolójegyzéknek a `<registry_name>` nevét, amelyben a lemezképet tárolni szeretné:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > Ebben a példában egy `:v1` címkét alkalmazunk a képre. Ha nem ad meg címkét, a rendszer a `:latest` címkét alkalmazza.

    A buildelési folyamat során az információk visszastreamelnek a parancssorba. Ha a build sikeres, a következő szöveghez hasonló üzenet jelenik meg:

    ```text
    Run ID: cda was successful after 2m56s
    ```

További információ a lemezképek Azure Container Registry használatával való összeállításával és futtatásával kapcsolatos [Azure Container Registry-feladatok](../container-registry/container-registry-quickstart-task-cli.md)

További információ a meglévő rendszerképek feltöltésével kapcsolatban egy Azure Container Registry: Az első rendszerkép leküldése egy privát [Docker-tárolójegyzékbe.](../container-registry/container-registry-get-started-docker-cli.md)

## <a name="use-a-custom-base-image"></a>Egyéni alapként használt rendszerkép használata

Egyéni rendszerkép használatának a következő információkra van szüksége:

* A __rendszerkép neve.__ A például `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` a Microsoft által biztosított egyszerű Docker-rendszerkép elérési útja.

    > [!IMPORTANT]
    > A létrehozott egyéni rendszerképekhez mindenképpen foglalja bele a képhez használt címkéket. Ha például a rendszerképet egy adott címkével hozták létre, `:v1` például: . Ha nem adott címkét használt a rendszerkép létrehozásakor, a rendszer a `:latest` címkét alkalmazta.

* Ha a rendszerkép egy privát __adattárban__ található, a következő információkra lesz szüksége:

    * A __beállításjegyzékbeli cím.__ Például: `myregistry.azureecr.io`.
    * Egy __szolgáltatásnévhez tartozó felhasználónév__ és __jelszó,__ amely olvasási hozzáféréssel rendelkezik a beállításjegyzékhez.

    Ha nincs ilyen információja, forduljon a rendszergazdához a Azure Container Registry-fájlért.

### <a name="publicly-available-base-images"></a>Nyilvánosan elérhető alapként használható rendszerképek

A Microsoft számos Docker-rendszerképet biztosít egy nyilvánosan elérhető adattárban, amelyek az ebben a szakaszban található lépésekben használhatók:

| Kép | Leírás |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Alapként Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | AZ ONNX Runtime-t tartalmazza a CPU-következtetéshez |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | A GPU-hez készült ONNX-runtime-t és CUDA-t tartalmazza |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | GPU-val készült ONNX Runtime-et és TensorRT-t tartalmaz |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | <sup></sup>Movidius<sup>TM</sup> MyriadX VPN-re épülő ONNX Runtime-t és OpenVINO for Intel Vision Accelerator-kialakítást tartalmaz |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Tartalmazza az ONNX Runtime-t és az OpenVINO for Intel <sup></sup> Movidius<sup>TM</sup> USB-csatlakozókat |

Az ONNX Runtime alapként megadott rendszerképekkel kapcsolatos további információkért lásd a GitHub-adattár [ONNX Runtime dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) szakaszát.

> [!TIP]
> Mivel ezek a rendszerképek nyilvánosan elérhetők, nem kell megadnia címet, felhasználónevet vagy jelszót a használatukkor.

További információ: [](https://github.com/Azure/AzureML-Containers) Azure Machine Learning GitHubon.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Rendszerkép használata a Azure Machine Learning SDK-val

Ha egy, a munkaterülethez Azure Container Registry tárolójegyzékben tárolt rendszerképet vagy egy nyilvánosan elérhető tároló-beállításjegyzéket használ, **állítsa** be a következő [környezeti attribútumokat:](/python/api/azureml-core/azureml.core.environment.environment) 

+ `docker.enabled=True`
+ `docker.base_image`: Állítsa be a beállításjegyzéket és a rendszerkép elérési útját.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Ha a munkaterületen nem __található__ privát tároló-beállításjegyzékből származó rendszerképet használ, a használatával meg kell adnia az adattár címét, valamint egy felhasználónevet és `docker.base_image_registry` jelszót:

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

Az azureml-defaults paramétert a >1.0.45 verzióval kell hozzáadnia pip-függőségként. Ez a csomag tartalmazza a modell webszolgáltatásként való használatához szükséges funkciókat. A környezetben a inferencing_stack_version tulajdonságot a "latest" (legújabb) beállításra kell állítania. Ez telepíti a webszolgáltatáshoz szükséges speciális apt-csomagokat. 

A környezet definiálása után használja azt egy [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) objektummal annak a következtetési környezetnek a meghatározásához, amelyben a modell és a webszolgáltatás futni fog.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Ezen a ponton folytathatja az üzembe helyezést. A következő kódrészlet például helyileg helyez üzembe egy webszolgáltatást a következtetési konfiguráció és az egyéni rendszerkép használatával:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

További információ az üzembe helyezésről: [Modellek üzembe helyezése a Azure Machine Learning.](how-to-deploy-and-where.md)

A Python-környezet testreszabásával kapcsolatos további információkért lásd: Környezetek létrehozása és kezelése [betanítás és üzembe helyezéshez.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Rendszerkép használata a Machine Learning CLI-val

> [!IMPORTANT]
> A Machine Learning CLI jelenleg a munkaterülethez Azure Container Registry vagy nyilvánosan elérhető adattárakhoz használhatja a rendszerképeket. Nem használhat önálló privát beállításregisztrálókból származó rendszerképeket.

Mielőtt üzembe helyez egy modellt a Machine Learning CLI használatával, hozzon létre egy [környezetet,](/python/api/azureml-core/azureml.core.environment.environment) amely az egyéni rendszerképet használja. Ezután hozzon létre egy következtetési konfigurációs fájlt, amely a környezetre hivatkozik. A környezetet közvetlenül a következtetési konfigurációs fájlban is meghatározhatja. A következő JSON-dokumentum bemutatja, hogyan hivatkozhat egy nyilvános tárolójegyzékben található rendszerképre. Ebben a példában a környezet beágyazottan van definiálva:

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

Ezt a fájlt a parancs `az ml model deploy` használja. A paraméter a következtetési konfigurációs fájl `--ic` megadására használható.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

A modellek ML CLI használatával való üzembe helyezéséről további információt a cli-bővítmény "modellregisztráció, profilkészítés és üzembe helyezés" című szakaszában talál, [Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) cikkben.

## <a name="next-steps"></a>Következő lépések

* További információ a [telepítés helyének és a telepítésének mikéntjről.](how-to-deploy-and-where.md)
* Ismerje meg, hogyan képezhet és helyezhet [üzembe gépi tanulási modelleket az Azure Pipelines használatával.](/azure/devops/pipelines/targets/azure-machine-learning)