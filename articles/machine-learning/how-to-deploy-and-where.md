---
title: Gépi tanulási modellek üzembe helyezése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan és hol helyezhet üzembe gépi tanulási modelleket. Üzembe helyezés Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge és FPGA környezetben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2
adobe-target: true
ms.openlocfilehash: f2128949090ce0ec2aa4ed66eb476384d662953a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872640"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Gépi tanulási modellek üzembe helyezése az Azure-ban

Megtudhatja, hogyan helyezheti üzembe webszolgáltatásként a gépi tanulási vagy mély tanulási modelljét az Azure-felhőben. A központi telepítést az Azure IoT Edge is.

A munkafolyamat a modell telepítésének helyétől függetlenül hasonló:

1. Regisztrálja a modellt (nem kötelező, lásd alább).
1. A következtetési konfiguráció előkészítése (kivéve, ha [kód nélkül való üzembe helyezést használ).](./how-to-deploy-no-code-deployment.md)
1. Készítsen elő egy bejegyzési szkriptet (kivéve, ha [kód nélkül történő üzembe helyezést használ).](./how-to-deploy-no-code-deployment.md)
1. Válasszon ki egy számítási célt.
1. A modell üzembe helyezése a számítási célon.
1. Tesztelje az eredményül kapott webszolgáltatást.

A gépi tanulás üzembe helyezési munkafolyamatával kapcsolatos fogalmakkal kapcsolatos további információkért lásd: [Modellek kezelése,](concept-model-management-and-deployment.md)üzembe helyezése és figyelése a Azure Machine Learning.

## <a name="prerequisites"></a>Előfeltételek

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md)(Munkaterület Azure Machine Learning létrehozása).
- Egy modell. Ha nem áll rendelkezésre betanított modell, használhatja az oktatóanyagban megadott modell- és [függőségfájlokat.](https://aka.ms/azml-deploy-cloud)
- Az [Azure parancssori felület (CLI) bővítménye a Machine Learning szolgáltatáshoz.](reference-azure-machine-learning-cli.md)

# <a name="python"></a>[Python](#tab/python)

- Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md)(Munkaterület Azure Machine Learning létrehozása).
- Egy modell. Ha nem áll rendelkezésre betanított modell, használhatja az oktatóanyagban megadott modell- és [függőségfájlokat.](https://aka.ms/azml-deploy-cloud)
- A [Azure Machine Learning szoftverfejlesztői készlet (SDK) a Pythonhoz.](/python/api/overview/azure/ml/intro)

---

## <a name="connect-to-your-workspace"></a>Csatlakozás a munkaterülethez

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Kövesse az Azure CLI dokumentációjában található utasításokat az [előfizetési környezet beállításához.](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)

Ezután tegye a következőt:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

a munkaterületek, amelyekhez hozzáféréssel rendelkezik.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

A munkaterülethez való csatlakozás SDK-val való használatával kapcsolatos további információkért tekintse meg az [Azure Machine Learning SDK for Python dokumentációját.](/python/api/overview/azure/ml/intro#workspace)


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> A modell regisztrálása (nem kötelező)

A regisztrált modell a modellt tartalmazó egy vagy több fájl logikai tárolója. Ha például egy modell több fájlban van tárolva, regisztrálhatja őket egyetlen modellként a munkaterületen. A fájlok regisztrálása után letöltheti vagy üzembe helyezheti a regisztrált modellt, és fogadhatja az összes regisztrált fájlt.

> [!TIP] 
> A modell verziókövetésre való regisztrálása ajánlott, de nem kötelező. Ha inkább modell regisztrálása nélkül folytatná, meg kell adnia egy forráskönyvtárat az [](./reference-azure-machine-learning-cli.md#inference-configuration-schema) [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) vagyinferenceconfig.jsalatt, és meg kell győződni arról, hogy a modell abban a forráskönyvtárban található.

> [!TIP]
> A modell regisztrálásakor meg kell adnia egy felhőbeli hely elérési útját (egy betanítás futtatásából) vagy egy helyi könyvtárat. Ez az elérési út csak a regisztrációs folyamat részeként feltölthet fájlokat keresve. Nem kell megegyezni a bejegyzési szkriptben használt elérési útokkal. További információ: [Modellfájlok keresése a bejegyzési szkriptben.](./how-to-deploy-advanced-entry-script.md#load-registered-models)

> [!IMPORTANT]
> Ha a Filter by (Szűrés) lehetőséget használja a Azure Machine Learning Studio Models (Modellek) lapján, akkor az ügyfelek helyett a következőt kell `Tags` `TagName : TagValue` használniuk: `TagName=TagValue` (szóköz nélkül)

Az alábbi példák bemutatják, hogyan regisztrálhat egy modellt.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Modell regisztrálása Azure ML betanítás futtatásából

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

A `--asset-path` paraméter a modell felhőbeli helyét jelenti. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt is fel akar foglalni a modellregisztrációba, állítsa be egy olyan mappa elérési útját, `--asset-path` amely a fájlokat tartalmazza.

### <a name="register-a-model-from-a-local-file"></a>Modell regisztrálása helyi fájlból

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Ha több fájlt is fel akar foglalni a modellregisztrációba, állítsa a adatokat tartalmazó mappa `-p` elérési útjára.

A ről a `az ml model register` referenciadokumentációban [talál további információt.](/cli/azure/ml/model)

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Modell regisztrálása Azure ML betanítás futtatásából

  Ha az SDK-val tanít be egy modellt, a modell betanításától függően kaphat egy Futtatás objektumot vagy egy [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) objektumot. [](/python/api/azureml-core/azureml.core.run.run) Minden objektum felhasználható egy kísérlet futtatásával létrehozott modell regisztrálásához.

  + Modell regisztrálása `azureml.core.Run` objektumból:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    A `model_path` paraméter a modell felhőbeli helyét jelenti. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt is fel akar foglalni a modellregisztrációba, állítsa a adatokat tartalmazó mappa `model_path` elérési útjára. További információért tekintse [](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) meg a Run.register_model dokumentációját.

  + Modell regisztrálása `azureml.train.automl.run.AutoMLRun` objektumból:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Ebben a példában a és a paraméter nincs megadva, így a legjobb elsődleges metrikával való `metric` `iteration` iteráció lesz regisztrálva. A `model_id` futtatásból visszaadott érték lesz használva a modell neve helyett.

    További információért tekintse [](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) meg a AutoMLRun.register_model dokumentációját.

    Regisztrált modell egy alkalmazásból való üzembe helyezéséhez javasoljuk, hogy ezt az Azure Machine Learning Studióban, az egykattintásos üzembe helyezés `AutoMLRun` [gombbal telepítse.](how-to-use-automated-ml-for-ml-models.md#deploy-your-model) 
### <a name="register-a-model-from-a-local-file"></a>Modell regisztrálása helyi fájlból

A modellek regisztrálása a modell helyi elérési útjának meg kell megszabad ássa. Meg lehet adni egy mappa vagy egyetlen fájl elérési útját. Ezzel a módszerrel regisztrálhatja a Azure Machine Learning betanított modelleket. Ezzel a módszerrel a modellen kívül betanított modelleket is Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Az SDK és az ONNX használata**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Ha több fájlt is fel akar foglalni a modellregisztrációba, állítsa be egy olyan mappa elérési útját, `model_path` amely a fájlokat tartalmazza.

További információért tekintse meg a [Model osztály dokumentációját.](/python/api/azureml-core/azureml.core.model.model)

A meglévő modelleken kívül betanított Azure Machine Learning [lásd: How to deploy an existing model](how-to-deploy-existing-model.md)(Meglévő modell üzembe helyezése).

---

## <a name="define-an-entry-script"></a>Bejegyzési szkript meghatározása

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Következtetési konfiguráció meghatározása


A következtetési konfiguráció azt ismerteti, hogyan állíthatja be a modellt tartalmazó webszolgáltatást. Ezt később, a modell üzembe helyezésekor használjuk majd.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

A minimális következtetési konfiguráció a következő ként írható meg:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir",
    "environment": {
    "docker": {
        "arguments": [],
        "baseDockerfile": null,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
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
                "conda-forge",
                "pytorch"
            ],
            "dependencies": [
                "python=3.6.2",
                "torchvision"
                {
                    "pip": [
                        "azureml-defaults",
                        "azureml-telemetry",
                        "scikit-learn==0.22.1",
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
```

Ez azt határozza meg, hogy a Machine Learning üzembe helyezése a könyvtárban lévő fájlt fogja használni a bejövő kérések feldolgozásához, és hogy a Docker-rendszerképet fogja használni a környezetben megadott `score.py` `./working_dir` Python-csomagokkal. `project_environment`

[A következtetési](./reference-azure-machine-learning-cli.md#inference-configuration-schema) konfigurációk alaposabb megvitatásához tekintse meg ezt a cikket. 

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa a következőket mutatja be:

1. [összecsatott környezet betöltése a](resource-curated-environments.md) munkaterületről
1. A környezet klónozása
1. Függőségként `scikit-learn` való megadása.
1. A környezet használata egy InferenceConfig létrehozásához

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

A környezetekkel kapcsolatos további információkért lásd: [Környezetek létrehozása és kezelése a betanításhoz és az üzembe helyezéshez.](how-to-use-environments.md)

A dedundáns konfigurációval kapcsolatos további információkért tekintse meg az [InferenceConfig osztály dokumentációját.](/python/api/azureml-core/azureml.core.model.inferenceconfig)

---

> [!TIP] 
> Az egyéni Docker-rendszerkép következtetési konfigurációval való használatával kapcsolatos információkért lásd: Modell üzembe helyezése egyéni [Docker-rendszerkép használatával.](how-to-deploy-custom-docker-image.md)

## <a name="choose-a-compute-target"></a>Számítási cél kiválasztása

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Üzembe helyezési konfiguráció meghatározása

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Az üzembe helyezési konfigurációkhoz elérhető beállítások a választott számítási céltól függően eltérőek lehetnek.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

További információért tekintse meg ezt [a hivatkozást.](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)

# <a name="python"></a>[Python](#tab/python)

A modell üzembe helyezése előtt meg kell határoznia az üzembe helyezési konfigurációt. *Az üzembe helyezési konfiguráció a webszolgáltatást tároló számítási célra vonatkozik.* Amikor például helyileg helyez üzembe egy modellt, meg kell adnia azt a portot, ahol a szolgáltatás fogadja a kéréseket. Az üzembe helyezési konfiguráció nem része a belépési szkriptnek. A modell és a belépési szkriptet tároló számítási cél jellemzőinek meghatározására használatos.

Előfordulhat, hogy a számítási erőforrást is létre kell hoznia, ha például még nincs a munkaterülethez társított Azure Kubernetes Service-példány (AKS).

Az alábbi táblázat egy példát mutat be az egyes számítási célhoz való üzembe helyezési konfiguráció létrehozására:

| Számítási cél | Példa üzembe helyezési konfigurációra |
| ----- | ----- |
| Helyi | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

A helyi, Azure Container Instances és AKS-webszolgáltatások osztályai a alkalmazásból `azureml.core.webservice` importálhatók:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>A gépi tanulási modell üzembe helyezése

Most már készen áll a modell üzembe helyezésére. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>Regisztrált modell használata

Ha regisztrálta a modellt a Azure Machine Learning munkaterületén, cserélje le a "mymodel:1" helyére a modell nevét és verziószámát.

```azurecli-interactive
az ml model deploy -n tutorial -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Helyi modell használata

Ha nem szeretné regisztrálni a modellt, átadhatja a "sourceDirectory" paramétert a inferenceconfig.js-ban, hogy megadjon egy helyi könyvtárat, amelyből a modellt kiszolgálni szeretné.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json --name my_deploy
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy helyi üzembe helyezést mutat be. A szintaxis az előző lépésben kiválasztott számítási céltól függően változik.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

További információkért tekintse meg a [LocalWebservice,](/python/api/azureml-core/azureml.core.webservice.local.localwebservice) [a Model.deploy() és](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)a [Webservice dokumentációját.](/python/api/azureml-core/azureml.core.webservice.webservice)

---

### <a name="understanding-service-state"></a>A szolgáltatás állapotának ismertetése

A modell üzembe helyezése során előfordulhat, hogy a szolgáltatás állapota megváltozik a teljes üzembe helyezés során.

Az alábbi táblázat a különböző szolgáltatás-államokat ismerteti:

| Webszolgáltatás állapota | Description | Végső állapot?
| ----- | ----- | ----- |
| Átállás | A szolgáltatás üzembe helyezése folyamatban van. | No |
| Nem kifogástalan | A szolgáltatás telepítve van, de jelenleg nem érhető el.  | No |
| Nem ütemezhető | A szolgáltatás jelenleg nem helyezhető üzembe az erőforrások hiánya miatt. | No |
| Sikertelen | A szolgáltatás üzembe helyezése hiba vagy összeomlás miatt meghiúsult. | Yes |
| Kifogástalan | A szolgáltatás kifogástalan állapotú, és a végpont elérhető. | Yes |

> [!TIP]
> Az üzembe helyezéskor a számítási célokhoz készült Docker-rendszerképeket a rendszer kiépíti és betölti az Azure Container Registry (ACR) szolgáltatásból. Alapértelmezés szerint a Azure Machine Learning alapszintű szolgáltatási szintet használó *ACR-t* hoz létre. Ha a munkaterület ACR-ét standard vagy prémium szintre módosítja, az csökkentheti a rendszerképek számítási célokon való létrehozásához és üzembe helyezéséhez szükséges időt. További információkért lásd a [Azure Container Registry rétegeket.](../container-registry/container-registry-skus.md)

> [!NOTE]
> Ha AKS-Azure Kubernetes Service helyez üzembe egy modellt, javasoljuk, [](../azure-monitor/containers/container-insights-enable-existing-clusters.md) hogy engedélyezze Azure Monitor fürtön. Ez segít megérteni a fürt általános állapotát és erőforrás-használatát. A következő erőforrásokat is hasznosnak találhatja:
>
> * [Az AKS-Resource Health érintett események ellenőrzése](../aks/aks-resource-health.md)
> * [Azure Kubernetes Service diagnosztika](../aks/concepts-diagnostics.md)
>
> Ha egy modellt egy nem megfelelő vagy túlterhelt fürtön próbál üzembe helyezni, problémákat fog tapasztalni. Ha segítségre van szüksége az AKS-fürtproblémák elhárításához, forduljon az AKS ügyfélszolgálatához.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Batch-következtetés
Azure Machine Learning számítási célok létrehozása és kezelése a Azure Machine Learning. A folyamatokból származó kötegelt előrejelzésekhez Azure Machine Learning használhatók.

Az Azure Machine Learning Compute kötegelt következtetésének bemutatójáért lásd: Kötegelt [előrejelzések futtatása.](tutorial-pipeline-batch-scoring-classification.md)

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge következtetés
A peremhálózaton való üzembe helyezés támogatása előzetes verzióban érhető el. További információ: [Deploy Azure Machine Learning as an IoT Edge module](../iot-edge/tutorial-deploy-machine-learning.md)..

## <a name="delete-resources"></a>Erőforrások törlése

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Egy üzembe helyezett webszolgáltatás törléséhez használja a `az ml service <name of webservice>` parancsot.

Regisztrált modell munkaterületről való törléséhez használja a `az ml model delete <model id>`

További információ [a webszolgáltatások törléséről](/cli/azure/ml/service#az_ml_service_delete) és [a modell törléséről.](/cli/azure/ml/model#az_ml_model_delete)

# <a name="python"></a>[Python](#tab/python)

Az üzembe helyezett webszolgáltatás törléséhez használja a `service.delete()` parancsot.
Regisztrált modell törléséhez használja a `model.delete()` parancsot.

További információt a [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) és [a Model.delete() dokumentációjában talál.](/python/api/azureml-core/azureml.core.model.model#delete--)

---

## <a name="next-steps"></a>Következő lépések

* [Sikertelen üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Ügyfélalkalmazások létrehozása webszolgáltatásokhoz](how-to-consume-web-service.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Egykattintásos üzembe helyezés az automatizált gépi tanulási Azure Machine Learning stúdió](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [Monitor a Azure Machine Learning modelleket a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése éles modellekhez](how-to-enable-data-collection.md)
* [Eseményriasztás és eseményindítók létrehozása a modell üzembe helyezéséhez](how-to-use-event-grid.md)