---
title: A Tritonnal kiszolgált nagy teljesítményű modell (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti üzembe a modellt az NVIDIA Triton Inference Serverrel a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 8775696a35bfccc363aa2c6ec06c6c44115916b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479270"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Nagy teljesítményű kiszolgálás a Triton következtetési kiszolgálóval (előzetes verzió) 

Megtudhatja, hogyan használhatja az [NVIDIA Triton dedukenciakiszolgálót](https://aka.ms/nvidia-triton-docs) a modelldedukenciához használt webszolgáltatás teljesítményének javítására.

A modellek következtetéshez való üzembe helyezésének egyik módja a webszolgáltatás. Például egy központi telepítést Azure Kubernetes Service vagy Azure Container Instances. Alapértelmezés szerint a Azure Machine Learning egyszálas, általános  célú webes keretrendszert használ a webszolgáltatás üzembe helyezéséhez.

A Triton egy következtetésre optimalizált *keretrendszer.* Jobb GPU-kihasználtságot és költséghatékonyabb következtetést biztosít. A kiszolgálóoldalon kötege a bejövő kéréseket, és dedoklánsan elküldi ezeket a kötegeket. A kötegek jobban kihasználják a GPU-erőforrásokat, és a Triton teljesítményének kulcsfontosságú részét képezi.

> [!IMPORTANT]
> A Triton használata a Azure Machine Learning jelenleg előzetes verzióban __érhető el.__ Előfordulhat, hogy az előzetes verziójú funkciókra nem vonatkozik az ügyfélszolgálat. További információ: Kiegészítő használati feltételek a Microsoft Azure [előzetes verziókhoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> A dokumentumban a kódrészletek szemléltető célokat szolgálnak, és előfordulhat, hogy nem mutatnak teljes megoldást. Példakódokért tekintse meg a [Triton](https://aka.ms/triton-aml-sample)teljes mintáit a Azure Machine Learning.

> [!NOTE]
> [Az NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) egy nyílt forráskódú, harmadik féltől származó szoftver, amely integrálva van a Azure Machine Learning.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha még nincs ilyen verziója, próbálja ki az ingyenes vagy fizetős [Azure Machine Learning.](https://aka.ms/AMLFree)
* Jártasság a modell üzembe [helyezésének és helyének Azure Machine Learning.](how-to-deploy-and-where.md)
* A [Azure Machine Learning SDK for Python vagy](/python/api/overview/azure/ml/) **az** [Azure CLI](/cli/azure/) és a Machine [Learning bővítmény.](reference-azure-machine-learning-cli.md)
* A Docker működő telepítése helyi teszteléshez. A Docker telepítésével és érvényesítésével kapcsolatos információkért lásd a Docker dokumentációjának [Tájolás](https://docs.docker.com/get-started/) és beállítás lépését.

## <a name="architectural-overview"></a>Az architektúra áttekintése

Mielőtt megpróbálja használni a Tritont a saját modelljéhez, fontos megértenie, hogyan működik együtt a Azure Machine Learning és hogyan viszonyul az alapértelmezett üzemelő példányhoz.

**Alapértelmezett üzembe helyezés Triton nélkül**

* Több [Gunicorn-dolgozó](https://gunicorn.org/) is egyidejűleg kezeli a bejövő kéréseket.
* Ezek a feldolgozók kezelik az előfeldolgozást, a modell hívását és az utófeldolgozást. 
* Az ügyfelek az __Azure ML pontozási URI-ját használják.__ Például: `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normál, nem triton, üzembe helyezési architektúra ábrája":::

**Üzembe helyezés a Tritonnal közvetlenül**

* A kérések közvetlenül a Triton-kiszolgálóhoz mennek.
* A Triton kötegben dolgozza fel a kérelmeket a GPU-kihasználtság maximalizálása érdekében.
* Az ügyfél a __Triton URI-t használja__ a kérések igényléshez. Például: `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Inferenceconfig üzemelő példány csak Tritonnal, pythonos middleware nélkül":::

**Dedúenciakonfiguráció üzembe helyezése a Tritonnal**

* Több [Gunicorn-dolgozó](https://gunicorn.org/) is egyidejűleg kezeli a bejövő kéréseket.
* A kéréseket a rendszer továbbítja a **Triton-kiszolgálónak.** 
* A Triton kötegben dolgozza fel a kérelmeket a GPU-kihasználtság maximalizálása érdekében.
* Az ügyfél az __Azure ML pontozási URI-ját használja__ a kérések igényléshez. Például: `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Üzembe helyezés a Triton és a Python middleware használatával":::

A Triton a következő munkafolyamatot használja a modell üzembe helyezéséhez:

1. A modell közvetlen kiszolgálása a Tritonnal.
1. Ellenőrizze, hogy küldhet-e kéréseket a Triton által üzembe helyezett modellnek.
1. (Nem kötelező) Python-középső szoftverréteg létrehozása kiszolgálóoldali elő- és utófeldolgozáshoz

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>A Triton üzembe helyezése Python elő- és utófeldolgozás nélkül

Először kövesse az alábbi lépéseket annak ellenőrzéséhez, hogy a Triton-következtetési kiszolgáló képes-e a modell kiszolgálására.

### <a name="optional-define-a-model-config-file"></a>(Nem kötelező) Modell konfigurációs fájlja definiálva

A modellkonfigurációs fájl adja meg a Tritonnak, hogy hány bemenet várható, és hogy ezek milyen dimenziókban lesznek. További információ a konfigurációs fájl létrehozásáról: [Modellkonfiguráció](https://aka.ms/nvidia-triton-docs) az NVIDIA dokumentációjában.

> [!TIP]
> A Triton Inference Server indításakor ezt a lehetőséget használjuk, ami azt jelenti, hogy nem kell fájlt adnia az `--strict-model-config=false` `config.pbtxt` ONNX- vagy TensorFlow-modellekhez.
> 
> Erről a beállításról az NVIDIA-dokumentáció [Generated model configuration (Létrehozott modellkonfiguráció)](https://aka.ms/nvidia-triton-docs) dokumentumában talál további információt.

### <a name="use-the-correct-directory-structure"></a>A megfelelő könyvtárstruktúra használata

Amikor regisztrál egy modellt a Azure Machine Learning, egyéni fájlokat vagy könyvtárstruktúrát regisztrálhat. A Triton használata érdekében a modellregisztrációnak olyan címtárstruktúrához kell lennie, amely tartalmaz egy nevű `triton` könyvtárat. A könyvtár általános szerkezete a következő:

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
> Ez a könyvtárstruktúra egy Triton Model-adattár, és ahhoz szükséges, hogy a modell(ek) együtt dolgozva dolgoz(nak) a Tritonnal. További információt az [NVIDIA-dokumentáció Triton Model Repositories (Triton-modelltárak)](https://aka.ms/nvidia-triton-docs) dokumentumában talál.

### <a name="register-your-triton-model"></a>A Triton-modell regisztrálása

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

A ről a `az ml model register` referenciadokumentációban [talál további információt.](/cli/azure/ext/azure-cli-ml/ml/model)

Amikor regisztrálja a modellt a Azure Machine Learning, a paraméter értékének a Triton szülőmappa nevének `--model-path  -p` kell lennie.  
A fenti példában  `--model-path` a "models" (modellek) lesz.

A példában `--name  -n` a à€ ̃ my_triton_modelâ™ paraméter értéke lesz a modell neve, Azure Machine Learning-munkaterület. 

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
További információért tekintse meg a [Model osztály dokumentációját.](/python/api/azureml-core/azureml.core.model.model)

---

### <a name="deploy-your-model"></a>A modell üzembe helyezése

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Ha az "aks-gpu" nevű GPU Azure Kubernetes Service-kompatibilis fürtöt Azure Machine Learning, a következő paranccsal helyezheti üzembe a modellt.

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

A [modellek üzembe helyezésével kapcsolatos további részletekért tekintse meg ezt a dokumentációt.](how-to-deploy-and-where.md)

### <a name="call-into-your-deployed-model"></a>Az üzembe helyezett modell hívása

Először szerezze be a pontozási URI-t és a bearer tokeneket.

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

Ezután a következővel győződjön meg arról, hogy a szolgáltatás fut: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Ez a parancs az alábbihoz hasonló információkat ad vissza. Figyelje meg `200 OK` a ; ez az állapot azt jelenti, hogy a webkiszolgáló fut.

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

Miután végrehajtott egy állapotellenőrzést, létrehozhat egy ügyfelet, amely adatokat küld a Tritonnak dedüklációra. Az ügyfelek létrehozásával kapcsolatos további [](https://aka.ms/nvidia-client-examples) információkért tekintse meg az NVIDIA dokumentációjában található ügyfélpémentációkat. [Python-minták is elérhetők a Triton GitHubon.](https://aka.ms/nvidia-triton-docs)

Ha ezen a ponton nem szeretne python elő- és utófeldolgozást hozzáadni az üzembe helyezett webszolgáltatáshoz, akkor lehet, hogy már kész is van. Ha hozzá szeretné adni ezt az elő- és utófeldolgozási logikát, olvasson tovább.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>(Nem kötelező) Újra üzembe helyezés Python-bejegyzési szkript használatával elő- és utófeldolgozáshoz

Miután ellenőrizte, hogy a Triton képes-e a modell kiszolgálására, hozzáadhat egy elő- és utófeldolgozási kódot egy bejegyzési _szkript definiálása után._ Ennek a fájlnak a neve `score.py` . A bejegyzési szkriptekkel kapcsolatos további információkért lásd: [Bejegyzési parancsfájl definiálás.](how-to-deploy-and-where.md#define-an-entry-script)

A két fő lépés egy Triton HTTP-ügyfél inicializálása a metódusban, valamint az ügyfél hívása a `init()` `run()` függvényben.

### <a name="initialize-the-triton-client"></a>A Triton-ügyfél inicializálása

A fájlba foglalja bele az alábbi példához hasonló `score.py` kódot. A triton Azure Machine Learning azt várja, hogy a localhoston, a 8000-es porton lesz címzve. Ebben az esetben a localhost az üzemelő példány Docker-rendszerképében található, nem pedig a helyi gépen lévő porton:

> [!TIP]
> A pip csomag megtalálható a összecsomagzott környezetben, ezért nem szükséges `tritonhttpclient` `AzureML-Triton` pip-függőségként megadni.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>A pontozó szkript módosítása a Tritonba való híváshoz

Az alábbi példa bemutatja, hogyan kérhet dinamikusan metaadatokat a modellhez:

> [!TIP]
> A Triton-ügyfél metódusával dinamikusan kérheti le a Tritonnal betöltött `.get_model_metadata` modellek metaadatait. A [használatra a mintajegyzetfüzetben](https://aka.ms/triton-aml-sample) láthat példát.

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

### <a name="redeploy-with-an-inference-configuration"></a>Ismételt üzembe ásás következtetési konfigurációval

A következtetési konfiguráció lehetővé teszi egy bejegyzési szkript használatát, valamint a Azure Machine Learning üzembe helyezési folyamatot a Python SDK vagy az Azure CLI használatával.

> [!IMPORTANT]
> Meg kell adnia a `AzureML-Triton` [összecsukott környezetet.](./resource-curated-environments.md)
>
> A Python-példakód klónja `AzureML-Triton` egy másik, nevű környezetbe `My-Triton` lesz klónozva. Az Azure CLI-kód is ezt a környezetet használja. A környezetek klónozásának további információiért tekintse meg az [Environment.Clone()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) referenciáját.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> A következtetési konfigurációk létrehozásával kapcsolatos további információkért lásd a következtetési konfigurációs [sémát.](./reference-azure-machine-learning-cli.md#inference-configuration-schema)

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

Az üzembe helyezés befejezése után megjelenik a pontozási URI. Ebben a helyi üzemelő példányban a következő lesz: `http://localhost:6789/score` . Ha a felhőben helyez üzembe, az [az ml service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) CLI-paranccsal le tudja szerezni a pontozási URI-t.

A következtetési kérelmeket a pontozási URI-nak ügyfélszámítógépek létrehozásáról lásd: Webszolgáltatásként üzembe helyezett [modell igénybe helyezése.](how-to-consume-web-service.md)

### <a name="setting-the-number-of-workers"></a>A dolgozók számának beállítása

Az üzemelő példányban a munkamunkások számának beállítását a környezeti változóval állíthatja `WORKER_COUNT` be. Ha van egy [nevű környezeti](/python/api/azureml-core/azureml.core.environment.environment) objektuma, a `env` következőket teheti:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Ez arra fogja szólni az Azure ML-t, hogy adja meg a megadott számú dolgozókat.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy továbbra is az Azure Machine Learning munkaterületet használja, de el szeretné szabadulni az üzembe helyezett szolgáltatástól, használja az alábbi lehetőségek egyikét:


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---
## <a name="troubleshoot"></a>Hibaelhárítás

* [Sikertelen üzembe helyezés hibaelhárítása,](how-to-troubleshoot-deployment.md)megtudhatja, hogyan háríthatja el és háríthatja el a modellek üzembe helyezése során előforduló gyakori hibákat.

* Ha az üzembe helyezési naplók azt mutatják, hogy **a TritonServer** nem tudott elindulni, tekintse meg az Nvidia® ™ nyílt forráskódú [dokumentációját.](https://github.com/triton-inference-server/server)

## <a name="next-steps"></a>Következő lépések

* [A Triton végpontok között mintái a Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Tekintse meg [a Triton-ügyfelek példái között](https://aka.ms/nvidia-client-examples)
* Olvassa el [a Triton Inference Server dokumentációját](https://aka.ms/nvidia-triton-docs)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [Adatok gyűjtése éles modellekhez](how-to-enable-data-collection.md)
