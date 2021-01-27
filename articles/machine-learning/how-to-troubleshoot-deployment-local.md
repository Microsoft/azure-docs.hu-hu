---
title: Helyi modell telepítésének hibaelhárítása
titleSuffix: Azure Machine Learning
description: Próbálja ki a modell üzembe helyezésével kapcsolatos hibák elhárításának első lépéseként a helyi modell központi telepítését.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 473b2e4ac3443d204b39331d9cefaa6c71122439
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881669"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>Hibaelhárítás helyi modell-telepítéssel

Próbálja ki a helyi modell központi telepítését az Azure Container Instances (ACI) vagy az Azure Kubernetes Service (ak) üzembe helyezésének hibaelhárítása során.  A helyi webszolgáltatás használatával egyszerűbbé válik a gyakori Azure Machine Learning Docker-webszolgáltatások telepítési hibáinak kijavítása és javítása.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).
* A [Azure Machine learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* Az [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)-vel.
* A [Azure Machine learning CLI-bővítménye](reference-azure-machine-learning-cli.md).
* A helyileg történő hibakereséshez modelleket telepíthet [Azure Machine learning számítási példányra](./how-to-deploy-local-container-notebook-vm.md) , vagy használhat egy működő Docker-telepítést a helyi rendszeren.

    A Docker-telepítés ellenőrzéséhez használja a parancsot `docker run hello-world` egy terminálról vagy parancssorból. A Docker telepítésével vagy a Docker-hibák elhárításával kapcsolatos információkért tekintse meg a [Docker dokumentációját](https://docs.docker.com/).

## <a name="debug-locally"></a>Helyi hibakeresés

A [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) -tárházban talál egy [helyi telepítési jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) egy futtatható-példa megismeréséhez.

> [!WARNING]
> A helyi webszolgáltatások üzembe helyezése éles környezetben nem támogatott.

A helyileg történő üzembe helyezéshez módosítsa a kód használatát a `LocalWebservice.deploy_configuration()` telepítési konfiguráció létrehozásához. Ezután `Model.deploy()` a használatával telepítheti a szolgáltatást. A következő példa egy modellt helyez üzembe a modell változójában helyi webszolgáltatásként:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Ha saját Conda-specifikációs YAML határoz meg, sorolja fel a azureml alapértelmezett verzióját (>= 1.0.45) pip-függőségként. Ez a csomag a modell webszolgáltatásként való üzemeltetéséhez szükséges.

Ezen a ponton a megszokott módon dolgozhat a szolgáltatással. A következő kód az adatok szolgáltatásba küldését mutatja be:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

A Python-környezet testreszabásával kapcsolatos további információkért lásd: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md). 

### <a name="update-the-service"></a>A szolgáltatás frissítése

A helyi tesztelés során előfordulhat, hogy frissítenie kell a `score.py` fájlt a naplózás hozzáadásához, vagy a felderített problémák megoldására tett kísérletet. A fájl módosításainak újratöltéséhez használja a következőt: `score.py` `reload()` . A következő kód például újratölti a szolgáltatáshoz tartozó parancsfájlt, majd adatokat küld neki. Az adatgyűjtés a frissített `score.py` fájllal történik:

> [!IMPORTANT]
> A `reload` metódus csak helyi központi telepítések esetén érhető el. A központi telepítés másik számítási célra való frissítésével kapcsolatos információkért lásd: [a webszolgáltatások frissítése](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> A parancsfájl a `InferenceConfig` szolgáltatás által használt objektum által megadott helyről lesz újratöltve.

A modell, a Conda-függőségek vagy a telepítési konfiguráció módosításához használja az [Update ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-)t. A következő példa frissíti a szolgáltatás által használt modellt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>A szolgáltatás törlése

A szolgáltatás törléséhez használja a [delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--)t.

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> A Docker-napló ellenőrzése

A Service objektumból kinyomtathatja a Docker-motor részletes naplófájljait. Megtekintheti az ACI-, AK-és helyi központi telepítések naplóját. Az alábbi példa bemutatja, hogyan lehet kinyomtatni a naplókat.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Ha úgy látja, hogy a sor többször is `Booting worker with pid: <pid>` előfordul a naplókban, az azt jelenti, hogy nincs elég memória a feldolgozó elindításához.
A hibát a következő értékének növelésével kezelheti: `memory_gb``deployment_config`

## <a name="next-steps"></a>További lépések

További információk az üzembe helyezésről:

* [Távoli központi telepítések hibáinak megoldása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés és hol](how-to-deploy-and-where.md)
* [Oktatóanyag: a betanítási & modellek üzembe helyezése](tutorial-train-models-with-aml.md)
* [Kísérletek helyi futtatása és hibakeresése](./how-to-debug-visual-studio-code.md)