---
title: 'Képek besorolása oktatóanyag: modellek üzembe helyezése'
titleSuffix: Azure Machine Learning
description: Ez az oktatóanyag bemutatja, hogyan használhatók a Azure Machine Learning egy képbesorolási modell üzembe helyezéséhez a scikit-Learn használatával egy Python Jupyter notebookon.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: 0981d325b3c5982793ada480a87afc48bf58acf7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066578"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Oktatóanyag: lemezkép besorolási modell üzembe helyezése Azure Container Instances


Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az [előző oktatóanyagban](tutorial-train-models-with-aml.md) gépi tanulási modelleket tanított be, majd regisztrált egy modellt a felhőbeli munkaterületen.  Most már készen áll a modell webszolgáltatásként való üzembe helyezésére. A webszolgáltatás egy rendszerkép, ebben az esetben egy Docker-rendszerkép. Magába foglalja a pontozási logikát és a modellt. 

Az oktatóanyag ezen részében a Azure Machine Learning a következő feladatokhoz használhatja:

> [!div class="checklist"]
> * A tesztelési környezet beállítása.
> * A modell lekérése a munkaterületről.
> * A modell üzembe helyezése Container Instances.
> * Tesztelje az üzembe helyezett modellt.

A Container Instances nagyszerű megoldás a munkafolyamatok tesztelésére és megismerésére. Skálázható termelési környezetek esetén fontolja meg az Azure Kubernetes Service használatát. További információ: [a telepítés és a hol](how-to-deploy-and-where.md).

>[!NOTE]
> A cikkben ismertetett kód Azure Machine Learning SDK 1.0.83-verzióval lett tesztelve.

## <a name="prerequisites"></a>Előfeltételek

A jegyzetfüzet futtatásához először végezze el a modell betanítását az [oktatóanyagban (1. rész): képbesorolási modell betanítása](tutorial-train-models-with-aml.md).   Ezután nyissa meg az  *IMG-besorolás-part2-Deploy. ipynb* notebookot a klónozott *oktatóanyagok/lemezkép-besorolás-mnist-adat* mappában.

Ez az oktatóanyag a [githubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha saját [helyi környezetében](how-to-configure-environment.md#local)szeretné használni.  Győződjön meg arról, hogy telepítve van `matplotlib` és `scikit-learn` a környezetében. 

> [!Important]
> A cikk többi része ugyanazt a tartalmat tartalmazza, mint amit a jegyzetfüzetben lát.  
>
> Váltson a Jupyter jegyzetfüzetre, ha a kód futtatása közben szeretné olvasni.
> Ha egyetlen kód cellát szeretne futtatni egy jegyzetfüzetben, kattintson a kód cellára, és nyomja le a **SHIFT + ENTER billentyűkombinációt**. Vagy futtassa a teljes jegyzetfüzetet úgy, hogy az **összes futtatása** lehetőséget választja a felső eszköztáron.

## <a name="set-up-the-environment"></a><a name="start"></a>A környezet beállítása

Első lépésként állítsa be a tesztelési környezetet.

### <a name="import-packages"></a>Csomagok importálása

Importálja az oktatóanyaghoz szükséges Python-csomagokat.


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>Üzembe helyezés webszolgáltatásként

A modell üzembe helyezése az ACI-ban üzemeltetett webszolgáltatásként. 

Az ACI-hoz megfelelő környezet kialakításához biztosítsa az alábbiakat:
* Pontozószkript a modell használatának bemutatásához
* Az ACI létrehozásához szükséges konfigurációs fájl
* Az imént betanított modell

### <a name="create-scoring-script"></a>Pontozószkript létrehozása

Hozzon létre egy score.py nevű pontozószkriptet, amelyet a webszolgáltatás felé irányuló hívás használ a modell használatának bemutatásához.

A pontozószkriptnek két függvényt kell tartalmaznia:
* Az `init()` függvényt, amely általában a modellt tölti be a globális objektumba. Ezt a függvényt csak egyszer kell futtatni, a Docker-tároló indításakor. 

* A `run(input_data)` függvény a modell segítségével értékeket jelez elő a bemeneti adatok alapján. A futtatás bemenetei és kimenetei általában JSON-fájlokat használnak a szerializáláshoz vagy a deszerializáláshoz, de más formátumokat is támogatnak.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

### <a name="create-configuration-file"></a>Konfigurációs fájl létrehozása

Hozzon létre egy telepítési konfigurációs fájlt, és adja meg az ACI-tárolóhoz szükséges processzorok számát, illetve a RAM gigabájtban mért mennyiségét. Bár ez a modelltől is függ, az alapértelmezettként megadott 1 mag és 1 gigabyte RAM számos modell használatához elegendő. Ha később többre lenne szüksége, akkor újra létre kell hoznia a rendszerképet, és újra üzembe kell helyeznie a szolgáltatást.


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>Üzembe helyezés az ACI-ban
Várható befejezési idő: **körülbelül 2-5 perc**

Konfigurálja, majd helyezze üzembe a rendszerképet. Az alábbi kód a következő lépéseket hajtja végre:

1. Hozzon létre olyan környezeti objektumot, amely a modell által igényelt függőségeket tartalmazza a `tutorial-env` betanítás során mentett környezet () használatával.
1. A modell webszolgáltatásként való üzembe helyezéséhez szükséges következtetési konfiguráció létrehozása a következő használatával:
   * A pontozófájl (`score.py`)
   * az előző lépésben létrehozott környezeti objektum
1. Telepítse a modellt az ACI-tárolóba.
1. A webszolgáltatás HTTP-végpontjának lekérése.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Kérje le a pontozási webszolgáltatás REST-ügyfélhívásokat fogadó HTTP-végpontját. Ez a végpont bárkivel megosztható, aki tesztelni szeretné a webszolgáltatást vagy integrálni szeretné azt egy alkalmazásban.


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>A modell tesztelése


### <a name="download-test-data"></a>Tesztelési célú adatfájlok letöltése
A tesztelési célú adatfájlok letöltése a **./Data/** könyvtárba


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>Tesztadatok betöltése

A tesztadatokat az oktatóanyag követése során létrehozott **./data/** mappából töltheti be.


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>A tesztadatok előrejelzése

Az előrejelzés elkészítéséhez töltse be a tesztadatkészletet a modellbe.


Az alábbi kód a következő lépéseket hajtja végre:
1. Az adatok elküldése egy JSON-tömbként az ACI-ban üzemeltetett webszolgáltatásba. 

1. A szolgáltatás meghívása az SDK `run` API-jának használatával. A curl vagy bármely egyéb HTTP-eszköz használatával nyers hívásokat is indíthat.


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>A keveredési mátrix vizsgálata

Hozzon létre egy keveredési mátrixot, és ellenőrizze, hogy a tesztkészletből hány minta kapott helyes besorolást. Figyelje meg a helytelen előrejelzéseknél szereplő hibás besorolási értéket.


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

A kimenet a keveredési mátrixot jeleníti meg:

```output
[[ 960    0    1    2    1    5    6    3    1    1]
 [   0 1112    3    1    0    1    5    1   12    0]
 [   9    8  920   20   10    4   10   11   37    3]
 [   4    0   17  921    2   21    4   12   20    9]
 [   1    2    5    3  915    0   10    2    6   38]
 [  10    2    0   41   10  770   17    7   28    7]
 [   9    3    7    2    6   20  907    1    3    0]
 [   2    7   22    5    8    1    1  950    5   27]
 [  10   15    5   21   15   27    7   11  851   12]
 [   7    8    2   13   32   13    0   24   12  898]]
Overall accuracy: 0.9204
```

Használja a `matplotlib` kódtárat a keveredési mátrix grafikonként való megjelenítéséhez. Ezen a grafikonon az X tengely képviseli a tényleges értékeket, az Y tengely pedig az előre jelzett értékeket. Az egyes rácsok színe a hibaarányt jelöli. Minél világosabb a szín, annál magasabb a hibaarány. Például sok 5-ös hibásan 3-as besorolást kapott. Így a fényes rács jelenik meg (5, 3).

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![A zavart mátrixot bemutató diagram](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>Előrejelzések megjelenítése

Tesztelje a központilag telepített modellt a tesztelési adatokból származó, 30 rendszerképekből álló véletlenszerű mintával.  


1. A kapott előrejelzések kinyomtatása és a bemeneti képekkel való ábrázolása. A piros betűk és az inverz (fekete alapon fehér) képek kiemelik a tévesen besorolt mintákat. 

 Mivel a modell pontossága magas, előfordulhat, hogy a tévesen besorolt minta megtekintése előtt néhány alkalommal futtatnia kell a következő kódot.



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

A webszolgáltatás teszteléséhez nyers HTTP-kérést is küldhet.


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforráscsoportot és a munkaterületet más oktatóanyagokhoz és feltáráshoz szeretné megőrizni, csak a Container Instances üzemelő példányt törölheti az alábbi API-hívás használatával:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Következő lépések

+ További információ a Azure Machine Learning összes [üzembe helyezési lehetőségéről](how-to-deploy-and-where.md).
+ Ismerje meg, hogyan [hozhat létre ügyfeleket a webszolgáltatáshoz](how-to-consume-web-service.md).
+  [Előrejelzések készítése aszinkron módon nagy mennyiségű adattal](./tutorial-pipeline-batch-scoring-classification.md) .
+ A Azure Machine Learning-modellek monitorozása [Application Insightsokkal](how-to-enable-app-insights.md).
+ Próbálja ki az [automatikus algoritmus kiválasztása](tutorial-auto-train-models.md) oktatóanyagot.