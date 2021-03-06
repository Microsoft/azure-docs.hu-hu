---
title: A Triton-t (előzetes verzió) kiszolgáló nagy teljesítményű modell
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti üzembe a modellt az NVIDIA Triton Azure Machine Learning-kiszolgálóval.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 47d2c8865109e8ef43317b3c4a19c36e692aff91
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218842"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Nagy teljesítményű kiszolgálás a Triton inferenc Serverrel (előzetes verzió) 

Ismerje meg, hogyan használhatja az [NVIDIA Triton-alapú kiszolgáló](https://aka.ms/nvidia-triton-docs) szolgáltatást a modell következtetéséhez használt webszolgáltatás teljesítményének növelésére.

A modell üzembe helyezésének egyik módja webszolgáltatásként használható. Például egy üzembe helyezés az Azure Kubernetes Service-ben vagy a Azure Container Instances. Alapértelmezés szerint a Azure Machine Learning egy többszálú, *általános célú* webes keretrendszert használ a webszolgáltatások üzembe helyezéséhez.

A Triton egy *következtetésre optimalizált* keretrendszer. A GPU-k jobb kihasználtságát és költséghatékonyabb következtetéseket biztosít. A kiszolgálóoldali, a beérkező kéréseket kötegbe állítja, és elküldi ezeket a kötegeket a következtetésekhez. A kötegelt feldolgozás jobban kihasználja a GPU-erőforrásokat, és a Triton teljesítményének kulcsfontosságú része.

> [!IMPORTANT]
> A Triton használata a Azure Machine Learning szolgáltatásból való üzembe helyezéshez jelenleg __előzetes__ verzióban érhető el. Előfordulhat, hogy az előzetes verzió funkcióit nem fedi le az ügyfélszolgálat. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> A dokumentumban szereplő kódrészletek szemléltető célokat szolgálnak, és nem mutatnak teljes megoldást. A következő példában a [Triton végpontok közötti mintáit Azure Machine Learningban](https://aka.ms/triton-aml-sample)tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha még nem rendelkezik ilyennel, próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).
* Ismerje meg, [Hogyan és hol helyezheti üzembe a modelleket Azure Machine learning használatával](how-to-deploy-and-where.md) .
* A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/?view=azure-ml-py) **vagy** az [Azure CLI](/cli/azure/) és a [Machine learning bővítmény](reference-azure-machine-learning-cli.md).
* A Docker egy működő telepítése a helyi teszteléshez. A Docker telepítésével és ellenőrzésével kapcsolatos információkért lásd: [Tájolás és beállítás](https://docs.docker.com/get-started/) a Docker dokumentációjában.

## <a name="architectural-overview"></a>Az architektúra áttekintése

Mielőtt a Tritont a saját modelljére próbálja használni, fontos tisztában lennie azzal, hogyan működik együtt a Azure Machine Learning, és hogyan hasonlítja össze az alapértelmezett központi telepítéssel.

**Alapértelmezett üzembe helyezés Triton nélkül**

* Több [Gunicorn](https://gunicorn.org/) -feldolgozó is megkezdi a bejövő kérések egyidejű kezelését.
* Ezek a dolgozók az előfeldolgozást, a modell meghívását és a feldolgozás utáni folyamatokat kezelik. 
* Az ügyfelek az __Azure ml pontozási URI-ját__ használják. Például: `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normál, nem Triton, üzembe helyezési architektúra diagramja":::

**Üzembe helyezés közvetlenül a Triton-ban**

* A kérések közvetlenül a Triton-kiszolgálóra mennek.
* A Triton a GPU-kihasználtság maximalizálása érdekében a kötegekben dolgozza fel a kérelmeket.
* Az ügyfél a __TRITON URI__ -t használja a kérések elvégzéséhez. Például: `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Inferenceconfig üzembe helyezés csak a Triton-mel és a Python middleware-vel":::

**Konfiguráció központi telepítése a Tritontal**

* Több [Gunicorn](https://gunicorn.org/) -feldolgozó is megkezdi a bejövő kérések egyidejű kezelését.
* A rendszer továbbítja a kéréseket a **Triton-kiszolgálónak**. 
* A Triton a GPU-kihasználtság maximalizálása érdekében a kötegekben dolgozza fel a kérelmeket.
* Az ügyfél az __Azure ml pontozási URI__ -t használja a kérések elvégzéséhez. Például: `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Üzembe helyezés a Triton és a Python middleware-vel":::

A Triton a modell üzembe helyezéséhez használt munkafolyamat a következő:

1. A modellt közvetlenül a Tritonból is kiszolgálhatja.
1. Ellenőrizze, hogy küldhet-e kéréseket a Triton által üzembe helyezett modellbe.
1. Választható Python middleware-réteg létrehozása a kiszolgálóoldali előzetes és utólagos feldolgozáshoz

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>A Triton telepítése a Python előtti és utáni feldolgozás nélkül

Először kövesse az alábbi lépéseket annak ellenőrzéséhez, hogy a Triton-következtetési kiszolgáló képes-e a modell kiszolgálására.

### <a name="optional-define-a-model-config-file"></a>Választható Modell konfigurációs fájljának definiálása

A modell konfigurációs fájlja azt mutatja be, hogy a Triton hány bemenetet vár, és hogy milyen dimenziók lesznek a bemenetek. A konfigurációs fájl létrehozásával kapcsolatos további információkért lásd: [modell konfigurálása](https://aka.ms/nvidia-triton-docs) az NVIDIA dokumentációjában.

> [!TIP]
> A `--strict-model-config=false` Triton következtetési kiszolgálójának indításakor a lehetőséget használjuk, ami azt jelenti, hogy nem kell megadnia egy `config.pbtxt` fájlt a ONNX vagy TensorFlow modellekhez.
> 
> További információ erről a lehetőségről: [generált modell konfigurálása](https://aka.ms/nvidia-triton-docs) az NVIDIA dokumentációjában.

### <a name="use-the-correct-directory-structure"></a>A megfelelő címtár-struktúra használata

Azure Machine Learning-alapú modell regisztrálása esetén egyéni fájlokat vagy címtár-struktúrát is regisztrálhat. A Triton használatához a modell regisztrációjának olyan címtár-struktúrára kell mutatnia, amely tartalmazza a nevű könyvtárat `triton` . Ennek a könyvtárnak a általános szerkezete a következő:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Ez a címtár-struktúra a Triton-modell tárháza, és a modell (ek) hoz való együttműködéshez szükséges. További információ: [Triton Model repositorys](https://aka.ms/nvidia-triton-docs) az NVIDIA dokumentációjában.

### <a name="register-your-triton-model"></a>A Triton-modell regisztrálása

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

A szolgáltatással kapcsolatos további információkért `az ml model register` olvassa el a [dokumentációt](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
További információkért tekintse meg a [modell osztály](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py)dokumentációját.

---

### <a name="deploy-your-model"></a>A modell üzembe helyezése

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Ha olyan GPU-kompatibilis Azure Kubernetes Service-fürtöt használ, amelyet az Azure Machine Learning használatával hoztak létre, akkor a következő paranccsal telepítheti a modellt.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

A [modellek üzembe helyezésével kapcsolatos további információkért tekintse meg ezt a dokumentációt](how-to-deploy-and-where.md).

### <a name="call-into-your-deployed-model"></a>Hívás az üzembe helyezett modellbe

Először szerezze be a pontozási URI-t és a tulajdonosi jogkivonatokat.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

Ezt követően győződjön meg arról, hogy a szolgáltatás fut: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Ez a parancs a következőhöz hasonló adatokat ad vissza. Megjegyzés: a `200 OK` ; Ez az állapot azt jelenti, hogy a webkiszolgáló fut.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

Ha elvégezte az állapot-ellenőrzés végrehajtását, létrehozhat egy ügyfelet, amellyel az adatküldés a Triton for következtetésre történik. Az ügyfelek létrehozásával kapcsolatos további információkért tekintse meg az NVIDIA dokumentációjában található [ügyfél-példákat](https://aka.ms/nvidia-client-examples) . [A Triton githubon](https://aka.ms/nvidia-triton-docs)is vannak Python-minták.

Ezen a ponton, ha nem kívánja hozzáadni a Python előtti és utáni feldolgozást a telepített webszolgáltatáshoz, akkor lehet, hogy készen áll. Ha ezt az előkészítő és a feldolgozás utáni logikát szeretné hozzáadni, olvassa el a következőt:.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>Választható Ismételt üzembe helyezés egy Python-bejegyzési parancsfájllal az előzetes és a feldolgozás után

Miután meggyőződött róla, hogy a Triton képes kiszolgálni a modellt, az előzetes és a feldolgozás utáni kódokat a _bejegyzési parancsfájl_ definiálásával adhatja hozzá. A fájl neve `score.py` . A beléptetési parancsfájlokkal kapcsolatos további információkért lásd: [bejegyzési parancsfájl definiálása](how-to-deploy-and-where.md#define-an-entry-script).

A két fő lépés egy Triton HTTP-ügyfél inicializálása a `init()` metódusban, valamint az ügyfélnek a függvényben való meghívása `run()` .

### <a name="initialize-the-triton-client"></a>A Triton-ügyfél inicializálása

Az alábbi példához hasonló kódot adjon meg a `score.py` fájlhoz. A Triton a Azure Machine Learning a localhost, a 8000-es portra vár. Ebben az esetben a localhost az üzemelő példány Docker-rendszerképében található, nem pedig a helyi gépen lévő port:

> [!TIP]
> A `tritonhttpclient` pip-csomag a kurátori környezet részét képezi `AzureML-Triton` , így nem kell megadnia pip-függőségként.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Pontozási parancsfájl módosítása a Tritonba való híváshoz

Az alábbi példa bemutatja, hogyan lehet dinamikusan kérelmezni a modell metaadatait:

> [!TIP]
> A Triton-ügyféllel betöltött modellek metaadatait dinamikusan kérheti le a `.get_model_metadata` Triton-ügyfél metódusának használatával. Tekintse meg a [minta jegyzetfüzetet](https://aka.ms/triton-aml-sample) a használatáról.

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>Újbóli üzembe helyezés a következtetési konfigurációval

A következtetési konfiguráció lehetővé teszi egy belépési parancsfájl használatát, valamint a Azure Machine Learning üzembe helyezési folyamatát a Python SDK vagy az Azure CLI használatával.

> [!IMPORTANT]
> Meg kell adnia a `AzureML-Triton` [kurátori környezetet](./resource-curated-environments.md).
>
> A Python-kód például `AzureML-Triton` egy másik, nevű környezetbe klónozott `My-Triton` . Az Azure CLI-kód ezt a környezetet is használja. A környezetek klónozásával kapcsolatos további információkért tekintse meg a [Environment. Clone ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#clone-new-name-) hivatkozást.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> A következtetési beállítások létrehozásával kapcsolatos további információkért lásd: a [következtetések konfigurációs sémája](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

Az üzembe helyezés befejezése után megjelenik a pontozási URI. Ebben a helyi telepítésben a következő lesz: `http://localhost:6789/score` . Ha a felhőbe telepíti a szolgáltatást, az az [ml Service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) CLI paranccsal kérheti le a pontozási URI-t.

További információ arról, hogyan hozhat létre olyan ügyfelet, amely a pontozási URI-ra vonatkozó következtetéseket küld: [webszolgáltatásként üzembe helyezett modell felhasználása](how-to-consume-web-service.md).

### <a name="setting-the-number-of-workers"></a>A feldolgozók számának beállítása

Az üzemelő példányban lévő feldolgozók számának beállításához állítsa be a környezeti változót `WORKER_COUNT` . Ha van egy nevű [környezeti](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) objektum `env` , a következőket teheti:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Ez azt jelzi, hogy az Azure ML felgyorsítja a megadott munkavégzők számát.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy továbbra is használja a Azure Machine Learning munkaterületet, de szeretné megszabadulni a telepített szolgáltatástól, használja az alábbi lehetőségek egyikét:


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---

## <a name="next-steps"></a>Következő lépések

* [Lásd: a Triton végpontok közötti mintái Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Ismerje meg a [Triton-ügyfelek példáit](https://aka.ms/nvidia-client-examples)
* A Triton-beli [következtetési kiszolgáló dokumentációjának](https://aka.ms/nvidia-triton-docs) beolvasása
* [Sikertelen üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)