---
title: Helyi Futtatás és központi telepítés
titleSuffix: Azure Machine Learning
description: Ez a cikk azt ismerteti, hogyan használható a helyi számítógép a Azure Machine Learning-ben létrehozott modellek betanításához, hibakereséséhez vagy üzembe helyezéséhez célként.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 8f22f1af04d59b4c1fb7f19a3d6815b61cac5fbd
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211227"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Helyi gépeken Azure Machine Learning betanított modellek üzembe helyezése 

Ez a cikk azt ismerteti, hogyan használható a helyi számítógép a Azure Machine Learning-ben létrehozott modellek betanításához vagy üzembe helyezéséhez célként. A Azure Machine Learning a legtöbb Python Machine learning-keretrendszerrel való együttműködéshez elég rugalmas. A gépi tanulási megoldások általában olyan összetett függőségekkel rendelkeznek, amelyeket nehéz lehet duplikálni. Ebből a cikkből megtudhatja, hogyan kiegyensúlyozhatja a teljes irányítást egyszerű használattal.

A helyi telepítés forgatókönyvei a következők:

* Egy projekt elején gyorsan megismételheti az adat, parancsfájlokat és modelleket.
* Hibakeresés és hibaelhárítás a későbbi fázisokban.
* Végső telepítés a felhasználó által felügyelt hardveren.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).
- Modell és környezet. Ha nem rendelkezik betanított modellel, az [oktatóanyagban](tutorial-train-models-with-aml.md)szereplő modell-és függőségi fájlokat is használhatja.
- A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
- Conda-kezelő, például anaconda vagy Miniconda, ha meg szeretné jeleníteni Azure Machine Learning csomag függőségeinek tükrözését.
- Docker, ha a Azure Machine Learning környezet egy tárolós verzióját kívánja használni.

## <a name="prepare-your-local-machine"></a>A helyi gép előkészítése

Azure Machine Learning modell helyi futtatásának legmegbízhatóbb módja egy Docker-rendszerkép. A Docker-rendszerkép egy elkülönített, tárolóban lévő, duplikált, a hardverrel kapcsolatos problémákat, az Azure végrehajtási környezetét is lehetővé teszi. A Docker fejlesztési forgatókönyvek telepítésével és konfigurálásával kapcsolatos további információkért lásd: a [Windowson a Docker Remote Development áttekintése](/windows/dev-environment/docker/overview).

Egy hibakeresőt csatlakoztathat a Docker-ben futó folyamathoz. (Lásd: [csatolás egy futó tárolóhoz](https://code.visualstudio.com/docs/remote/attach-container).) Azonban érdemes lehet hibakeresést végezni, és megismételni a Python-kódot a Docker bevonása nélkül. Ebben az esetben fontos, hogy a helyi gép ugyanazokat a kódtárakat használja, mint amelyeket a kísérlet Azure Machine Learning való futtatásakor használ. A Python-függőségek kezeléséhez az Azure a [Conda](https://docs.conda.io/)-t használja. A környezetet más csomagkezelő használatával újra létrehozhatja, de a helyi gépen a Conda telepítése és konfigurálása a legegyszerűbb módja a szinkronizálásnak. 

## <a name="prepare-your-entry-script"></a>A nevezési parancsfájl előkészítése

Akkor is, ha a Docker használatával kezeli a modellt és a függőségeket, a Python pontozási parancsfájlnak helyinek kell lennie. A szkriptnek két módszerrel kell rendelkeznie:

- Olyan `init()` metódus, amely nem tartalmaz argumentumot, és nem ad vissza semmit 
- Egy `run()` JSON-formázott karakterláncot tartalmazó metódus, amely egy JSON-szerializálható objektumot ad vissza.

A metódus argumentuma `run()` ezen az űrlapon fog megjelenni: 

```json
{
    "data": <model-specific-data-structure>
}
```

A metódusból visszaadott objektumnak `run()` meg kell valósítania `toJSON() -> string` .

Az alábbi példa bemutatja, hogyan tölthető be a regisztrált scikit-modell, és hogyan szerzi be azt a NumPy-alapú adatelemzési szolgáltatással. Ez a példa az [oktatóanyag](tutorial-train-models-with-aml.md)modelljén és függőségein alapul.

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

A speciális példákhoz, beleértve az automatikus hencegés sémájának létrehozását és a bináris adatmegjelenítést (például képek), lásd: [speciális bejegyzés parancsfájl-készítés](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Üzembe helyezés helyi webszolgáltatásként a Docker használatával

A Azure Machine Learning által használt környezet replikálásának legegyszerűbb módja, ha a Docker használatával helyez üzembe egy webszolgáltatás-szolgáltatást. A helyi gépen futó Docker a következőket teszi:

1. Kapcsolódjon ahhoz a Azure Machine Learning-munkaterülethez, amelyben a modell regisztrálva van.
1. Hozzon létre egy `Model` objektumot, amely a modellt jelképezi.
1. Hozzon létre egy `Environment` objektumot, amely tartalmazza a függőségeket, és definiálja azt a szoftver-környezetet, amelyben a kód futni fog.
1. Hozzon létre egy `InferenceConfig` objektumot, amely társítja a bejegyzési parancsfájlt a paranccsal `Environment` .
1. Hozzon létre egy `DeploymentConfiguration` objektumot az alosztályból `LocalWebserviceDeploymentConfiguration` .
1. Objektum létrehozásához használja a következőt: `Model.deploy()` `Webservice` . Ez a metódus letölti a Docker-rendszerképet, és társítja azt a `Model` , `InferenceConfig` , és `DeploymentConfiguration` .
1. Aktiválja a t a `Webservice` használatával `Webservice.wait_for_deployment()` .

Az alábbi kód a következő lépéseket mutatja be:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

A hívása `Model.deploy()` néhány percet is igénybe vehet. Miután először telepítette a webszolgáltatást, hatékonyabban használhatja a `update()` metódust ahelyett, hogy teljesen megkezdené. Lásd: [központilag telepített webszolgáltatás frissítése](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>A helyi telepítés tesztelése

Az előző üzembe helyezési parancsfájl futtatásakor a rendszer kiírja az URI-t, amelybe adatokat tehet közzé (például: `http://localhost:6789/score` ). Az alábbi példa egy olyan parancsfájlt mutat be, amely a helyileg telepített modell használatával szerzi be a mintaadatok `"sklearn-mnist-local"` . A modell, ha megfelelően be van tanítva, a kikövetkeztetés `normalized_pixel_values` "2"-ként értelmezhető. 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>A modell letöltése és futtatása közvetlenül

A Docker használatával a modellt webszolgáltatásként helyezheti üzembe a leggyakoribb megoldásként. Előfordulhat azonban, hogy a kódot közvetlenül a helyi Python-parancsfájlok használatával szeretné futtatni. Két fontos összetevőre lesz szüksége: 

- Maga a modell
- Azok a függőségek, amelyekre a modell támaszkodik 

A modellt a következőket töltheti le:  

- A portálon válassza a **modellek** fület, válassza ki a kívánt modellt, majd a **részletek** lapon válassza a **Letöltés** lehetőséget.
- A parancssorból a használatával `az ml model download` . (Lásd a [modell letöltését.](/cli/azure/ext/azure-cli-ml/ml/model#ext_azure_cli_ml_az_ml_model_download))
- A Python SDK módszer használatával `Model.download()` . (Lásd a [Model osztályt.](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-))

Az Azure-modell egy vagy több szerializált Python-objektum, amely Python-lé-fájlként (. PKL kiterjesztéssel) van csomagolva. A lé-fájl tartalma a gépi tanulási könyvtártól vagy a modell betanításához használt technikától függ. Ha például az oktatóanyagban használja a modellt, a modellt a következővel töltheti be:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

A függőségek mindig trükkös, különösen a gépi tanulással, ahol gyakran egy adott verzióra vonatkozó követelmények szédítő webes változata lehet. A helyi gépen újra létrehozhat egy Azure Machine Learning környezetet teljes Conda-környezetként vagy Docker-rendszerképként a következő `build_local()` osztály metódusának használatával `Environment` : 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Ha az `build_local()` `useDocker` argumentumot a értékre állítja `True` , akkor a függvény Conda-környezet helyett Docker-rendszerképet hoz létre. Ha további vezérlésre van szüksége, használhatja a (z `save_to_directory()` ) metódusát `Environment` , amely conda_dependencies. yml és azureml_environment.jst ír a definíciós fájlokra, amelyeket a bővítmény alapjaként finomhangolásra és használatra használhat. 

Az `Environment` osztály számos más módszerrel is szinkronizálhatja a környezeteket a számítási hardverek, az Azure-munkaterület és a Docker-lemezképek között. További információ: [környezeti osztály](/python/api/azureml-core/azureml.core.environment(class)).

Miután letöltötte a modellt, és feloldotta a függőségeit, nincsenek Azure-definiált korlátozások a pontozási teljesítményének, a modell finomhangolásának, az átadási tanulás használatának és így tovább. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Újratanított modell feltöltése Azure Machine Learning

Ha van egy helyileg betanított vagy átképzett modellje, regisztrálhatja azt az Azure-ban. A regisztrációt követően folytathatja az Azure számítási szolgáltatással történő finomhangolását, illetve az Azure-szolgáltatások, például az [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) vagy a [Triton következtetési kiszolgáló (előzetes verzió)](how-to-deploy-with-triton.md)használatával történő üzembe helyezését.

A Azure Machine Learning Python SDK-val való használathoz a modellt egy olyan szerializált Python-objektumként kell tárolni, amely a lé formátumában (a. PKL fájl) található. Emellett olyan metódust is végre kell hajtania `predict(data)` , amely egy JSON-szerializálható objektumot ad vissza. Előfordulhat például, hogy egy helyileg betanított scikit-Learn diabétesz modellt tárol a következővel: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Ahhoz, hogy a modell elérhető legyen az Azure-ban, az `register()` osztály metódusát használhatja `Model` :

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Ezután megkeresheti az újonnan regisztrált modellt a Azure Machine Learning **Model (modell** ) lapon:

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Képernyőkép a Azure Machine Learning modell lapról, amely egy feltöltött modellt mutat.":::

A modellek és környezetek feltöltésével és frissítésével kapcsolatos további információkért lásd: [modell regisztrálása és helyi telepítése speciális használattal](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb).

## <a name="next-steps"></a>Következő lépések

- A környezetek kezelésével kapcsolatos további információkért tekintse meg a következő témakört: [Create & Software Environments in Azure Machine learning](how-to-use-environments.md).
- Az adattárból származó adatok elérésének megismeréséhez lásd: [Kapcsolódás a Storage Services szolgáltatáshoz az Azure](how-to-access-data.md)-ban.
