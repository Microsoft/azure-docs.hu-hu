---
title: Meglévő modellek használata és üzembe helyezése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja az Azure-ban betanított gépi tanulási modelleket az Azure-felhőbe a Azure Machine Learning. Ezután üzembe helyezheti a modellt webszolgáltatásként vagy IoT-modulként.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 46dc9e48ba68189253885ae823457a62c3c4426e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877803"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Meglévő modell üzembe helyezése a Azure Machine Learning


Ebből a cikkből megtudhatja, hogyan regisztrálhat és helyezhet üzembe egy gépi tanulási modellt, amely a Azure Machine Learning. Üzembe helyezheti webszolgáltatásként vagy egy IoT Edge eszközre.  Az üzembe helyezés után monitorozhatja a modellt, és észlelheti a sodródásokat a Azure Machine Learning. 

A cikkben található fogalmakkal és kifejezésekkel kapcsolatos további információkért lásd: Gépi tanulási modellek kezelése, üzembe helyezése [és figyelése.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Előfeltételek

* [Egy Azure Machine Learning-munkaterület](how-to-manage-workspace.md)
  + A Python-példák feltételezik, hogy a változó a saját Azure Machine Learning `ws` van beállítva. A munkaterülethez való csatlakozásról a Pythonhoz készült [Azure Machine Learning SDK dokumentációjában talál további információt.](/python/api/overview/azure/ml/#workspace)
  
  + A CLI-példák a és a helyőrzőit használják, amelyeket le kell cserélni a munkaterület nevére és az azt tartalmazó `myworkspace` `myresourcegroup` erőforráscsoportra.

* A [Azure Machine Learning Python SDK.](/python/api/overview/azure/ml/install)  

* Az [Azure CLI és](/cli/azure/install-azure-cli) a Machine Learning [CLI-bővítmény.](reference-azure-machine-learning-cli.md)

* Egy betanított modell. A modellt egy vagy több fájlban kell megőrznie a fejlesztési környezetben. <br><br>A betanított modell regisztrálásának szemléltetéséhez a cikkben található példakód [Paolo Pagamonti](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)Twitter-hangulatelemző projektjéhez () használt modelleket használja.

## <a name="register-the-models"></a>A modell(nek) regisztrálása

A modell regisztrálása lehetővé teszi a modellek metaadatainak tárolására, verziószámozására és nyomon követésére a munkaterületen. A következő Python- és CLI-példákban a könyvtár `models` a , , és `model.h5` `model.w2v` `encoder.pkl` `tokenizer.pkl` fájlokat tartalmazza. Ez a példa a könyvtárban található fájlokat tölti fel új, nevű `models` modellregisztrációként: `sentiment`

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

További információért tekintse meg a [Model.register()](/python/api/azureml-core/azureml.core.model%28class%29#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) referenciáját.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> A regisztrált modellhez hozzáadhat és `tags` `properties` szótárobjektumokat is. Ezek az értékek később egy adott modell azonosításához használhatók. Például a használt keretrendszer, a betanítás paraméterei stb.

További információért tekintse meg az [az ml model register referenciáját.](/cli/azure/ml/model#az_ml_model_register)


A modellregisztrációval kapcsolatos általános információkért lásd: Gépi tanulási modellek kezelése, üzembe helyezése [és figyelése.](concept-model-management-and-deployment.md)

## <a name="define-inference-configuration"></a>Következtetési konfiguráció meghatározása

A következtetési konfiguráció határozza meg az üzembe helyezett modell futtatásához használt környezetet. A következtetési konfiguráció a következő entitásra hivatkozik, amelyek a modell üzembe helyezésekor való futtatására használhatók:

* Egy nevű bejegyzési szkript betölti a `score.py` modellt az üzembe helyezett szolgáltatás indításakor. Ez a szkript felelős az adatok fogadásáért, a modellnek való átadásért, majd a válasz visszaküldéséért is.
* Egy Azure Machine Learning [környezet.](how-to-use-environments.md) A környezet határozza meg a modell és a bejegyzés szkript futtatásához szükséges szoftverfüggőségeket.

Az alábbi példa bemutatja, hogyan hozhat létre környezetet az SDK használatával, majd hogyan használhatja azt következtetési konfigurációval:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

További információért tekintse át a következő cikkeket:

+ [Környezetek használata.](how-to-use-environments.md)
+ [InferenceConfig referencia.](/python/api/azureml-core/azureml.core.model.inferenceconfig)


A CLI betölti a következtetési konfigurációt egy YAML-fájlból:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

A CLI-val a Conda-környezet a következtetési konfiguráció által hivatkozott fájlban `myenv.yml` van definiálva. A fájl tartalma a következő:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

A következtetési konfigurációval kapcsolatos további információkért lásd: Modellek üzembe helyezése [a Azure Machine Learning.](how-to-deploy-and-where.md)

### <a name="entry-script-scorepy"></a>Bejegyzési szkript (score.py)

A bejegyzési szkript csak két kötelező függvényt tartalmaz: `init()` és `run(data)` . Ezekkel a függvényekkel inicializálhatja a szolgáltatást indításkor, és futtathatja a modellt az ügyfél által átadott kérelemadatok használatával. A szkript többi része kezeli a modell(ök) betöltését és futtatását.

> [!IMPORTANT]
> Nincs olyan általános bejegyzési szkript, amely minden modell esetében működik. Mindig a használt modellre jellemző. Tisztában kell lennie a modell betöltésének, a modell által várt adatformátumnak és az adatok modell használatával való pontozásának módszerének használatával.

Az alábbi Python-kód egy bejegyzési szkript ( `score.py` ) példája:

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

A bejegyzési szkriptekkel kapcsolatos további információkért lásd: [Modellek üzembe helyezése a Azure Machine Learning.](how-to-deploy-and-where.md)

## <a name="define-deployment"></a>Üzembe helyezés meghatározása

A [Webservice](/python/api/azureml-core/azureml.core.webservice) csomag tartalmazza az üzembe helyezéshez használt osztályokat. A használt osztály határozza meg a modell üzembe helyezésének a helyét. Ha például webszolgáltatásként való üzembe helyezést Azure Kubernetes Service a [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) használatával hozza létre az üzembe helyezési konfigurációt.

A következő Python-kód egy helyi üzemelő példány üzembe helyezési konfigurációját határozza meg. Ez a konfiguráció webszolgáltatásként telepíti a modellt a helyi számítógépen.

> [!IMPORTANT]
> A helyi üzemelő példányhoz a Docker működő [telepítésére](https://www.docker.com/) van szükség a helyi számítógépen:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

További információért tekintse meg a [LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.localwebservice#deploy-configuration-port-none-) referenciáját.

A CLI betölti az üzembe helyezési konfigurációt egy YAML-fájlból:

```YAML
{
    "computeType": "LOCAL"
}
```

Az üzembe helyezés egy másik számítási célon, például az Azure-Azure Kubernetes Service való üzembe helyezés egyszerűen meg is változtatható az üzembe helyezési konfiguráción. További információ: [Modellek üzembe helyezése:](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Az alábbi példa betölti a nevű regisztrált modell adatait, majd üzembe helyez `sentiment` egy nevű `sentiment` szolgáltatásként. Az üzembe helyezés során a dedokenciakonfiguráció és a központi telepítési konfiguráció segítségével hozható létre és konfigurálható a szolgáltatási környezet:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

További információért tekintse meg a [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) referenciáját.

A modell parancssori felületről való üzembe helyezéséhez használja a következő parancsot. Ez a parancs a regisztrált modell () 1. verzióját telepíti a és a fájlokban tárolt következtetési és üzembe `sentiment:1` helyezési `inferenceConfig.json` `deploymentConfig.json` konfigurációval:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

További információért tekintse meg az [az ml model deploy reference (az az ml model deploy referencia)](/cli/azure/ml/model#az_ml_model_deploy) referenciát.

Az üzembe helyezéssel kapcsolatos további információkért lásd: Modellek üzembe [helyezése.](how-to-deploy-and-where.md)

## <a name="request-response-consumption"></a>Kérés-válasz használat

Az üzembe helyezés után megjelenik a pontozási URI. Ezt az URI-t az ügyfelek arra használják, hogy kéréseket küldjenek a szolgáltatásnak. Az alábbi példa egy egyszerű Python-ügyfél, amely adatokat küld a szolgáltatásnak, és megjeleníti a választ:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Az üzembe helyezett szolgáltatás létrehozására vonatkozó további információkért lásd: [Ügyfél létrehozása.](how-to-consume-web-service.md)

## <a name="next-steps"></a>Következő lépések

* [Monitor a Azure Machine Learning modelleket a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése éles modellekhez](how-to-enable-data-collection.md)
* [Ügyfél létrehozása üzembe helyezett modellhez](how-to-consume-web-service.md)