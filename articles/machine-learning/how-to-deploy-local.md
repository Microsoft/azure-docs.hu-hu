---
title: Helyi futtatás és üzembe helyezés
titleSuffix: Azure Machine Learning
description: Ez a cikk bemutatja, hogyan használhatja a helyi számítógépet a szolgáltatásban létrehozott modellek betanítása, hibakeresése vagy üzembe helyezése Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 75836580fc2dc5a2090047865610e26d856387b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861214"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Helyi gépeken Azure Machine Learning modellekkel betanított modellek üzembe helyezése 

Ez a cikk bemutatja, hogyan használhatja a helyi számítógépet a környezetben létrehozott modellek betanítása vagy üzembe helyezése Azure Machine Learning. Azure Machine Learning a legtöbb Python gépi tanulási keretrendszer használatához elég rugalmas. A gépi tanulási megoldások általában összetett függőségekkel is vannak, amelyek duplikálhatóak. Ez a cikk bemutatja, hogyan egyensúlyba használhatja a teljes ellenőrzést.

Helyi üzembe helyezési forgatókönyvek:

* Az adatok, szkriptek és modellek gyors iterai a projekt korai szakaszában.
* Hibakeresés és hibaelhárítás a későbbi szakaszokban.
* Végső üzembe helyezés a felhasználó által felügyelt hardveren.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md)(Munkaterület Azure Machine Learning létrehozása).
- Egy modell és egy környezet. Ha nem áll rendelkezésre betanított modell, használhatja az oktatóanyagban megadott modell- és [függőségfájlokat.](tutorial-train-models-with-aml.md)
- A [pythonhoz készült Azure Machine Learning SDK.](/python/api/overview/azure/ml/intro)
- Egy Conda-vezető, például az Anaconda vagy a Miniconda, ha tükrözni szeretné Azure Machine Learning csomagfüggőségeket.
- Docker, ha a tárolóba ezett verzióját szeretné használni a Azure Machine Learning környezetben.

## <a name="prepare-your-local-machine"></a>A helyi gép előkészítése

A legmegbízhatóbb módszer egy Azure Machine Learning modell helyi futtatására egy Docker-rendszerkép segítségével. A Docker-rendszerkép elkülönített, tárolóba ezett felhasználói élményt biztosít, amely a hardverhibák kivételével duplikálható az Azure végrehajtási környezetében. A Docker fejlesztési forgatókönyvekhez való telepítésével és konfigurálásával kapcsolatos további információkért lásd: A Docker távoli fejlesztés áttekintése [Windows rendszeren.](/windows/dev-environment/docker/overview)

Hibakeresőt csatolhat a Dockerben futó folyamathoz. [(Lásd: Csatolás futó tárolóhoz.)](https://code.visualstudio.com/docs/remote/attach-container) De érdemesebb lehet a Python-kód hibakeresését és iterálni a Docker bevonása nélkül. Ebben a forgatókönyvben fontos, hogy a helyi gép ugyanazt a kódtárakat használja, mint a kísérlet futtatásakor a Azure Machine Learning. A Python-függőségek kezeléséhez az Azure a [Condát használja.](https://docs.conda.io/) A környezet más csomagkezelőkkel is újra létrehozható, de a conda helyi gépen való telepítése és konfigurálása a legegyszerűbb módja a szinkronizálásnak. 

## <a name="prepare-your-entry-script"></a>A bejegyzési szkript előkészítése

Még ha a Docker használatával is kezeli a modellt és a függőségeket, a Python-pontozószkentnek helyinek kell lennie. A szkriptnek két metódussal kell lennie:

- Egy `init()` metódus, amely nem vesz fel argumentumokat, és semmit sem ad vissza 
- Egy `run()` metódus, amely egy JSON-formátumú sztringet vesz fel, és egy JSON-szerializálható objektumot ad vissza

A metódus `run()` argumentuma a következő formában lesz: 

```json
{
    "data": <model-specific-data-structure>
}
```

A metódusból visszaadott `run()` objektumnak implementálja a `toJSON() -> string` metódust.

Az alábbi példa bemutatja, hogyan lehet betölteni egy regisztrált scikit-learn modellt, és pontozást használni NumPy-adatokkal. Ez a példa az oktatóanyag modelljén és [függőségein alapul.](tutorial-train-models-with-aml.md)

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

További speciális példákat, például az automatikus Swagger-séma generálása és a bináris adatok pontozása (például képek) a Speciális bejegyzési [parancsfájlok létrehozása című cikk tartalmaz.](how-to-deploy-advanced-entry-script.md) 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Üzembe helyezés helyi webszolgáltatásként a Docker használatával

A szolgáltatás által használt környezet Azure Machine Learning a Webszolgáltatás üzembe helyezése a Docker használatával. Ha a Docker a helyi gépen fut, a következőt kell megtennie:

1. Csatlakozzon ahhoz Azure Machine Learning munkaterülethez, ahol a modell regisztrálva van.
1. Hozzon létre `Model` egy objektumot, amely a modellt jelöli.
1. Hozzon létre egy objektumot, amely tartalmazza a függőségeket, és meghatározza a szoftverkörnyezetet, amelyben `Environment` a kód futni fog.
1. Hozzon létre `InferenceConfig` egy objektumot, amely társítja a bejegyzési parancsfájlt a következővel: `Environment` .
1. Hozza létre a `DeploymentConfiguration` alosztály `LocalWebserviceDeploymentConfiguration` objektumát.
1. Objektum `Model.deploy()` létrehozásához használja a `Webservice` objektumot. Ez a metódus letölti a Docker-rendszerképet, és társítja a `Model` , `InferenceConfig` és `DeploymentConfiguration` rel.
1. Aktiválja a `Webservice` et a `Webservice.wait_for_deployment()` használatával.

Az alábbi kód ezeket a lépéseket mutatja be:

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

A `Model.deploy()` hívása eltarthat néhány percig. A webszolgáltatás első üzembe helyezése után hatékonyabb, ha a metódust használja, és nem az `update()` eredetiből indul ki. Lásd: [Telepített webszolgáltatás frissítése.](how-to-deploy-update-web-service.md)

### <a name="test-your-local-deployment"></a>A helyi üzemelő példány tesztelése

Az előző üzembe helyezési szkript futtatásakor az annak az URI-nak a kimenete lesz, amelyre POST-adatokat lehet pontozásra (például `http://localhost:6789/score` ). Az alábbi példa egy olyan szkriptet mutat be, amely a helyileg üzembe helyezett modell használatával pontja a `"sklearn-mnist-local"` mintaadatokat. A modell megfelelő betanítás esetén arra a következtetésre ad ki, hogy `normalized_pixel_values` a "2" legyen értelmezve. 

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

## <a name="download-and-run-your-model-directly"></a>A modell közvetlen letöltése és futtatása

A leggyakoribb lehetőség a modell webszolgáltatásként való üzembe helyezése a Docker használatával. Előfordulhat azonban, hogy a kódot közvetlenül a helyi Python-szkriptekkel szeretné futtatni. Két fontos összetevőre lesz szüksége: 

- Maga a modell
- A függőségek, amelyekre a modell támaszkodik 

Letöltheti a modellt:  

- A portálon a Modellek lap **kiválasztásával** válassza ki a kívánt modellt, majd a Részletek lapon **válassza** a Letöltés **gombra.**
- A parancssorból használja a `az ml model download` parancsot. (Lásd [a modell letöltését.](/cli/azure/ml/model#az_ml_model_download))
- A Python SDK `Model.download()` metódusával. [(Lásd: Modellosztály.](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-))

Az Azure-modell egy vagy több szerializált Python-objektum, amely Python pickle-fájlként (.pkl kiterjesztéssel) van csomagolva. A pickle-fájl tartalma a modell betanításakor használt gépi tanulási kódtártól vagy technikától függ. Ha például az oktatóanyagban használt modellt használja, a következővel töltheti be a modellt:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

A függőségeket mindig nehéz megfelelően behozni, különösen a gépi tanulásnál, ahol gyakran előfordul, hogy a konkrét verziókövetelmények egy túl nagy hálóban vannak. Újra létrehozhat egy Azure Machine Learning-környezetet a helyi gépen teljes Conda-környezetként vagy Docker-rendszerképként a `build_local()` osztály `Environment` metódusával: 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Ha az argumentumot `build_local()` `useDocker` a argumentumra adja `True` meg, a függvény a Conda-környezet helyett egy Docker-rendszerképet hoz létre. Ha nagyobb kontrollt szeretne, használhatja a metódusát, amely `save_to_directory()` conda_dependencies.yml és azureml_environment.jsolyan definíciós fájlokat ír, amelyeket finomhangolhat és bővítményként `Environment` használhat. 

A osztály számos egyéb metódussal is rendelkezik a környezetek szinkronizálása a számítási hardver, az Azure-munkaterület és a `Environment` Docker-rendszerképek között. További információ: [Környezeti osztály.](/python/api/azureml-core/azureml.core.environment(class))

A modell letöltése és függőségeinek megoldása után az Azure nem korlátozza a pontozást, a modell finomhangolását, a transzfertanulás használatát stb. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Újraképezett modell feltöltése a Azure Machine Learning

Ha helyileg betanított vagy újraképzett modellt kapott, regisztrálhatja azt az Azure-ban. A regisztrációt követően folytathatja a finomhangolást az Azure Compute használatával, vagy üzembe helyezheti olyan Azure-létesítményekkel, mint az [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) vagy a [Triton Inference Server (előzetes verzió)](how-to-deploy-with-triton.md).

Az Azure Machine Learning Python SDK-val való használatához a modellt szerializált Python-objektumként kell tárolni pickle formátumban (.pkl-fájlként). Egy olyan metódust is meg `predict(data)` kell valósítson, amely JSON-szerializálható objektumot ad vissza. Tárolhat például egy helyileg betanított scikit-learn diabetes modellt a következővel: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Ahhoz, hogy a modell elérhető legyen az Azure-ban, használhatja a `register()` osztály `Model` metódusát:

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Ezután megkeresheti az újonnan regisztrált modellt a Azure Machine Learning **lapon:**

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="A Azure Machine Learning modell lap képernyőképe, amely egy feltöltött modellt mutat.":::

A modellek és környezetek feltöltésével és frissítésével kapcsolatos további információkért lásd: Modell regisztrálása és helyi üzembe helyezés [speciális használattal.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)

## <a name="next-steps"></a>Következő lépések

- További információ a környezetek kezeléséről: [Create & use software environments in Azure Machine Learning.](how-to-use-environments.md)
- További információ az adatok adattárból való eléréséről: Csatlakozás tárolási [szolgáltatásokhoz az Azure-ban.](how-to-access-data.md)
