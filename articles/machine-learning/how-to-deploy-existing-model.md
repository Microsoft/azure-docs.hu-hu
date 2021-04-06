---
title: Meglévő modellek használata és üzembe helyezése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja ki az Azure-on kívül az Azure-ba betanított ML-modelleket Azure Machine Learning. Ezután telepítse a modellt webszolgáltatásként vagy IoT-modulként.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 8076e3f6b2198abe46ae49cd8e0fd2b02f000231
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519350"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>A meglévő modell üzembe helyezése Azure Machine Learning


Ebből a cikkből megtudhatja, hogyan regisztrálhatja és helyezheti üzembe a Azure Machine Learningon kívül betanított gépi tanulási modellt. Webszolgáltatásként vagy IoT Edge eszközként is üzembe helyezhető.  A telepítés után nyomon követheti a modellt, és felderítheti az adateltolódást Azure Machine Learningban. 

A cikkben szereplő fogalmakkal és kifejezésekkel kapcsolatos további információkért lásd: [gépi tanulási modellek kezelése, üzembe helyezése és figyelése](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Előfeltételek

* [Egy Azure Machine Learning-munkaterület](how-to-manage-workspace.md)
  + A Python-példák azt feltételezik, hogy a `ws` változó a Azure Machine learning munkaterületre van beállítva. A munkaterülethez való kapcsolódással kapcsolatos további információkért tekintse meg a [Pythonhoz készült Azure Machine learning SDK dokumentációját](/python/api/overview/azure/ml/#workspace).
  
  + A CLI-példák a és a helyőrzőit használják `myworkspace` `myresourcegroup` , amelyeket a munkaterület nevével és az azt tartalmazó erőforráscsoporthoz kell cserélni.

* A [Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/install)-t.  

* Az [Azure CLI](/cli/azure/install-azure-cli) és a [Machine learning CLI bővítmény](reference-azure-machine-learning-cli.md).

* Egy betanított modell. A modellt a fejlesztői környezet egy vagy több fájljába kell megőrizni. <br><br>A betanított modell regisztrálásának bemutatásához a cikkben szereplő példa kód a [Paolo Ripamonti Twitter hangulat-elemzési projekt](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)modelljeit használja.

## <a name="register-the-models"></a>A modell (ek) regisztrálása

A modell regisztrálása lehetővé teszi a munkaterületen lévő modellekhez tartozó metaadatok tárolását, verzióját és nyomon követését. A következő Python-és CLI-példákban a `models` könyvtár tartalmazza a `model.h5` , a és a `model.w2v` `encoder.pkl` `tokenizer.pkl` fájlokat. Ez a példa a címtárban található fájlokat a következő `models` nevű új modell-regisztrációval tölti fel `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

További információ: [modell. Register ()](/python/api/azureml-core/azureml.core.model%28class%29#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) – hivatkozás.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> `tags` `properties` A regisztrált modellhez a Hozzáadás és a szótár objektumok is megadhatók. Ezek az értékek később is használhatók egy adott modell azonosításához. Például a használt keretrendszer, a betanítási paraméterek stb.

További információ: az [ml Model Register](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-register) Reference.


A modell-regisztrációval kapcsolatos további információkért lásd: [gépi tanulási modellek kezelése, üzembe helyezése és figyelése](concept-model-management-and-deployment.md).

## <a name="define-inference-configuration"></a>Következtetési konfiguráció megadása

A következtetési konfiguráció határozza meg az üzembe helyezett modell futtatásához használt környezetet. A következtetési konfiguráció a következő entitásokra hivatkozik, amelyek a modellnek a telepítésekor történő futtatására szolgálnak:

* Egy nevű bejegyzési parancsfájl `score.py` betölti a modellt a központilag telepített szolgáltatás indításakor. Ez a szkript az adatfogadásra, a modellbe való átadásra, majd a válasz visszaküldésére is felelős.
* Azure Machine Learning [környezet](how-to-use-environments.md). A környezet a modell és a bejegyzési parancsfájl futtatásához szükséges szoftver-függőségeket határozza meg.

Az alábbi példa azt szemlélteti, hogyan használható az SDK egy környezet létrehozásához, majd egy következtetési konfiguráció használatával:

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

+ [Környezetek használata](how-to-use-environments.md).
+ [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) -hivatkozás.


A CLI betölti a következtetési konfigurációt egy YAML-fájlból:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

A CLI-vel a Conda-környezet a `myenv.yml` következtetési konfiguráció által hivatkozott fájlban van definiálva. A következő YAML a fájl tartalma:

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

További információ a konfigurációval kapcsolatban: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

### <a name="entry-script-scorepy"></a>Bejegyzési parancsfájl (score.py)

A bejegyzési parancsfájlnak csak két kötelező funkciója van, `init()` és `run(data)` . Ezekkel a függvényekkel inicializálhatja a szolgáltatást indításkor, és futtathatja a modellt az ügyfél által átadott kérelem-adatok használatával. A parancsfájl többi része kezeli a modell (ek) betöltését és futtatását.

> [!IMPORTANT]
> Nincs olyan általános bejegyzési parancsfájl, amely minden modell esetében működik. Mindig a használt modellre jellemző. Meg kell ismernie, hogyan tölthető be a modell, a modell által várt adatformátum, valamint az adatok a modell használatával történő pontozása.

A következő Python-kód egy példaként szolgáló bejegyzési parancsfájl ( `score.py` ):

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

A beléptetési parancsfájlokkal kapcsolatos további információkért lásd: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Központi telepítés meghatározása

A [webszolgáltatási](/python/api/azureml-core/azureml.core.webservice) csomag az üzembe helyezéshez használt osztályokat tartalmazza. A használt osztály meghatározza a modell központi telepítését. Ha például webszolgáltatásként szeretne üzembe helyezni az Azure Kubernetes szolgáltatásban, használja a [AksWebService.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) -t a telepítési konfiguráció létrehozásához.

A következő Python-kód egy helyi telepítés központi telepítési konfigurációját határozza meg. Ezzel a konfigurációval a modellt webszolgáltatásként helyezi üzembe a helyi számítógépen.

> [!IMPORTANT]
> A helyi telepítéshez a [Docker](https://www.docker.com/) működéséhez a helyi számítógépen kell telepíteni:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

További információ: [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.localwebservice#deploy-configuration-port-none-) referenciája.

A CLI betölti a telepítési konfigurációt egy YAML-fájlból:

```YAML
{
    "computeType": "LOCAL"
}
```

Egy másik számítási célra való üzembe helyezés, például az Azure Kubernetes szolgáltatás az Azure-felhőben, olyan egyszerűen, mint a telepítési konfiguráció módosítása. További információt a [modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)című témakörben talál.

## <a name="deploy-the-model"></a>A modell üzembe helyezése

A következő példa a nevű regisztrált modell adatait tölti be `sentiment` , majd telepíti a nevű szolgáltatásként `sentiment` . Az üzembe helyezés során a rendszer a következő viszonyítási konfigurációt és telepítési konfigurációt használja a szolgáltatási környezet létrehozásához és konfigurálásához:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

További információ: [modell. Deploy ()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) hivatkozás.

A modell parancssori felületről történő üzembe helyezéséhez használja a következő parancsot. Ezzel a paranccsal a (z) és a (z `sentiment:1` ) és a (z) fájlban tárolt következtetési és telepítési konfiguráció alapján telepítheti a regisztrált modell () 1 `inferenceConfig.json` . verzióját `deploymentConfig.json` :

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

További információ: az [ml Model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) Reference.

A telepítéssel kapcsolatos további információkért lásd a [modellek üzembe helyezésének módját és helyét](how-to-deploy-and-where.md)ismertető témakört.

## <a name="request-response-consumption"></a>Kérelem – válasz felhasználás

Az üzembe helyezés után megjelenik a pontozási URI. Ezt az URI-t használhatja az ügyfelek a szolgáltatásnak küldött kérések küldésére. Az alábbi példa egy egyszerű Python-ügyfél, amely elküldi az adatait a szolgáltatásnak, és megjeleníti a választ:

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

Az üzembe helyezett szolgáltatás használatáról további információt az [ügyfél létrehozása](how-to-consume-web-service.md)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Ügyfél létrehozása központilag telepített modellhez](how-to-consume-web-service.md)