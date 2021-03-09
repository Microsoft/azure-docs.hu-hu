---
title: Modell üzembe helyezése a Cognitive Searchcsel történő használatra
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning-t egy modell üzembe helyezéséhez Cognitive Search használatával. A modell egyéni képességként használható a keresési élmény gazdagítása érdekében.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 06/11/2020
ms.custom: deploy
ms.openlocfilehash: 9336dbd4d5615a93bbc029ba51b561b18f5a1c15
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521985"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Modell üzembe helyezése a Cognitive Searchcsel történő használatra


Ebből a cikkből megtudhatja, hogyan használhatja a Azure Machine Learningt modell üzembe helyezéséhez az [Azure Cognitive Search](../search/search-what-is-azure-search.md)használatával.

Cognitive Search a tartalom feldolgozását különböző-tartalomon keresztül végzi el, hogy az emberek vagy az alkalmazások lekérdezhető legyen. Ez a folyamat a Azure Machine Learning-ból üzembe helyezett modell használatával javítható.

Azure Machine Learning a betanított modellt webszolgáltatásként helyezheti üzembe. A webszolgáltatás ezután beágyazva van egy Cognitive Search- _képességbe_, amely a feldolgozási folyamat részévé válik.

> [!IMPORTANT]
> A cikkben található információk a modell üzembe helyezésére vonatkoznak. Információt nyújt a támogatott központi telepítési konfigurációkról, amelyek lehetővé teszik a modell használatát Cognitive Search.
>
> A Cognitive Search az üzembe helyezett modell használatára való konfigurálásával kapcsolatos információkért tekintse meg az [egyéni képességek létrehozása és üzembe helyezése Azure Machine learning](../search/cognitive-search-tutorial-aml-custom-skill.md) oktatóanyagban című témakört.
>
> Az oktatóanyag alapjául szolgáló minta: [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

Az Azure Cognitive Search-hoz való használatra szolgáló modell telepítésekor a központi telepítésnek meg kell felelnie az alábbi követelményeknek:

* Használja az Azure Kubernetes Service-t az következtetési modell üzemeltetéséhez.
* Az Azure Kubernetes szolgáltatás TLS protokolljának engedélyezése. A TLS a Cognitive Search és a központilag telepített modell közötti HTTPS-kommunikáció biztonságossá tételére szolgál.
* A bejegyzési parancsfájlnak a csomagot kell használnia a `inference_schema` szolgáltatáshoz tartozó OpenAPI-(hencegő) séma létrehozásához.
* A bejegyzési parancsfájlnak a JSON-adatokat is el kell fogadnia bemenetként, és a JSON-t kimenetként kell előállítania


## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

* A Azure Machine Learning SDK-val telepített Python-fejlesztési környezet. További információ: [Azure Machine learning SDK](/python/api/overview/azure/ml/install).  

* Egy regisztrált modell. Ha nem rendelkezik modellel, használja a példán látható jegyzetfüzetet a következő helyen: [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

* Általános ismeretek a [modellek üzembe helyezésének módjáról és helyéről](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Csatlakozás a munkaterülethez

Egy Azure Machine Learning munkaterület központosított helyet biztosít a Azure Machine Learning használatakor létrehozott összes összetevővel való együttműködéshez. A munkaterület megőrzi az összes betanítási Futtatás előzményeit, beleértve a naplókat, a metrikákat, a kimenetet és a parancsfájlok pillanatképét.

Egy meglévő munkaterülethez való kapcsolódáshoz használja a következő kódot:

> [!IMPORTANT]
> Ez a kódrészlet arra vár, hogy a munkaterület-konfiguráció az aktuális könyvtárba vagy a szülőbe legyen mentve. További információ: [Azure Machine learning-munkaterületek létrehozása és kezelése](how-to-manage-workspace.md). A konfiguráció fájlra való mentésével kapcsolatos további információkért lásd: [munkaterület konfigurációs fájljának létrehozása](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

**Becsült idő**: körülbelül 20 perc.

A Kubernetes-fürt virtuálisgép-példányok (más néven csomópontok) készlete, amely a tároló alkalmazások futtatására szolgál.

Ha Azure Machine Learningról az Azure Kubernetes szolgáltatásba helyez üzembe modellt, a modell és a webszolgáltatásként való üzemeltetéshez szükséges összes eszköz egy Docker-tárolóba van csomagolva. Ezt a tárolót ezután telepíti a rendszer a fürtön.

A következő kód bemutatja, hogyan hozhat létre új Azure Kubernetes Service-(ak-) fürtöt a munkaterülethez:

> [!TIP]
> Meglévő Azure Kubernetes-szolgáltatást is csatolhat a Azure Machine Learning munkaterülethez. További információ: [modellek üzembe helyezése az Azure Kubernetes szolgáltatásban](how-to-deploy-azure-kubernetes-service.md).

> [!IMPORTANT]
> Figyelje meg, hogy a kód a következő módszert használja a `enable_ssl()` Transport Layer Security (TLS) engedélyezésére a fürtön. Erre akkor van szükség, ha az üzembe helyezett modellt Cognitive Servicesról tervezi használni.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Az Azure számlázni fogja, amíg az AK-fürt létezik. Ha végzett, mindenképp törölje az AK-fürtöt.

További információ a Azure Machine Learning-vel való használatáról: [üzembe helyezés az Azure Kubernetes szolgáltatásban](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>A bejegyzés parancsfájljának írása

A bejegyzési parancsfájl fogadja a webszolgáltatásnak küldött adatok fogadását, átadja a modellnek, és visszaadja a pontozás eredményét. A következő szkript betölti a modellt indításkor, majd a modell használatával szerzi be az adattípust. Ezt a fájlt néha nevezik `score.py` .

> [!TIP]
> A bejegyzés parancsfájlja egyedi a modellnél. A szkriptnek például tudnia kell, hogy milyen keretrendszert kell használni a modellhez, az adatformátumokhoz stb.

> [!IMPORTANT]
> Ha az Azure Cognitive Servicesról tervezi az üzembe helyezett modell használatát, akkor a `inference_schema` csomag használatával engedélyeznie kell a séma létrehozását a központi telepítéshez. Ez a csomag olyan dekorációkat biztosít, amelyekkel meghatározható a webszolgáltatáshoz tartozó bemeneti és kimeneti adatformátum, amely a modell használatával következtetéseket hajt végre.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

A beléptetési parancsfájlokkal kapcsolatos további információkért lásd: [how and hol üzembe helyezés](how-to-deploy-and-where.md).

## <a name="define-the-software-environment"></a>A Szoftverkörnyezet meghatározása

A környezeti osztály a szolgáltatás Python-függőségeinek definiálására szolgál. Magában foglalja a modell és a beléptetési parancsfájl által igényelt függőségeket is. Ebben a példában a csomagokat a normál PyPI indexből, valamint egy GitHub-tárházból telepítik. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

További információ a környezetekről: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md).

## <a name="define-the-deployment-configuration"></a>A központi telepítési konfiguráció megadása

A telepítési konfiguráció határozza meg a webszolgáltatás futtatásához használt Azure Kubernetes szolgáltatás üzemeltetési környezetét.

> [!TIP]
> Ha nem biztos abban, hogy az üzemelő példány memória-, CPU-vagy GPU-szükséglete nem megfelelő, a profilkészítés használatával megismerheti ezeket. További információt a [modell üzembe helyezésének módját és helyét](how-to-deploy-and-where.md)ismertető témakörben talál.

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

További információt a [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)dokumentációjában talál.

## <a name="define-the-inference-configuration"></a>A következtetési konfiguráció megadása

A következtetési konfiguráció a bejegyzési parancsfájlra és a környezeti objektumra mutat:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

További információkért tekintse meg a [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig)dokumentációját.

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Telepítse a modellt az AK-fürtre, és várja meg, amíg létrehozza a szolgáltatást. Ebben a példában két regisztrált modell van betöltve a beállításjegyzékből, és az AK-ban van üzembe helyezve. Az üzembe helyezés után a `score.py` központi telepítésben lévő fájl betölti ezeket a modelleket, és a következtetések elvégzéséhez használja őket.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

További információ: a [modell](/python/api/azureml-core/azureml.core.model.model)dokumentációja.

## <a name="issue-a-sample-query-to-your-service"></a>Lekérdezés kiadása a szolgáltatásnak

A következő példa a változóban tárolt központi telepítési információkat használja az `aks_service` előző kód szakaszban. Ezt a változót használja a szolgáltatással való kommunikációhoz szükséges pontozási URL és hitelesítési jogkivonat lekéréséhez:

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

A szolgáltatás által visszaadott eredmény a következő JSON-hoz hasonló:

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Kapcsolódás Cognitive Searchhoz

További információ a modell Cognitive Searchból történő használatáról: [Egyéni szakértelem létrehozása és üzembe helyezése Azure Machine learning](../search/cognitive-search-tutorial-aml-custom-skill.md) oktatóanyagban.

## <a name="clean-up-the-resources"></a>Az erőforrások eltávolítása

Ha kifejezetten ehhez a példához hozta létre az AK-fürtöt, törölje az erőforrásokat a Cognitive Search való tesztelés után.

> [!IMPORTANT]
> Az Azure-számlázás az AK-fürt üzembe helyezésének időtartamán alapul. Ha elkészült, ügyeljen rá, hogy megtisztítsa.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Következő lépések

* [Egyéni képesség létrehozása és üzembe helyezése Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md)