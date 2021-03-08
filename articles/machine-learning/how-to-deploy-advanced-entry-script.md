---
title: A speciális forgatókönyvek szerzői bejegyzési parancsfájlja
titleSuffix: Azure Machine Learning entry script authoring
description: Megtudhatja, hogyan írhat Azure Machine Learning bejegyzési parancsfájlokat az üzembe helyezés előtti és utáni feldolgozáshoz.
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 22b22cf364f4422c358fc71db74465b839ddae45
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454968"
---
# <a name="advanced-entry-script-authoring"></a>Speciális bejegyzésszkript létrehozása

Ez a cikk bemutatja, hogyan írhatók be a speciális használati esetekben a beléptetési parancsfájlok.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már rendelkezik egy betanított gépi tanulási modellel, amelyet a Azure Machine Learning használatával szeretne üzembe helyezni. A modell üzembe helyezésével kapcsolatos további tudnivalókért tekintse meg [ezt az oktatóanyagot](how-to-deploy-and-where.md).

## <a name="automatically-generate-a-swagger-schema"></a>Swagger-séma automatikus létrehozása

Ha automatikusan szeretne létrehozni egy sémát a webszolgáltatás számára, adja meg a bemeneti és/vagy kimeneti adatokat a konstruktorban egy adott típusú objektumhoz. A rendszer a típust és a mintát használja a séma automatikus létrehozásához. A Azure Machine Learning Ezután létrehoz egy [OpenAPI](https://swagger.io/docs/specification/about/) (hencegő) specifikációt a webszolgáltatás számára az üzembe helyezés során. 

> [!WARNING]
> Nem használhat bizalmas vagy magánjellegű adatokat a minta bemeneti vagy kimeneti adatokhoz. A pénzmosás által szolgáltatott következtetések lapja elérhetővé teszi a mintaadatok betekintését. 

Ezek a típusok jelenleg támogatottak:

* `pandas`
* `numpy`
* `pyspark`
* Standard Python-objektum

A séma generálásához a függőségek fájljában adja meg a nyílt forráskódú `inference-schema` csomag 1.1.0-es vagy újabb verzióját. A csomaggal kapcsolatos további információkért lásd: [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . A webszolgáltatások automatikus felhasználásának kialakításához a pontozási szkript futtatása () függvénynek API-formának kell lennie a következőhöz:
* Egy "StandardPythonParameterType" típusú, **bemenetek** és beágyazott nevű paraméter.
* Egy "StandardPythonParameterType" típusú, nem kötelező második paraméter, amelynek neve **GlobalParameters**.
* Egy "StandardPythonParameterType" típusú szótárt ad vissza, amelynek neve **Results** és nested.

Adja meg a bemeneti és kimeneti minták formátumait a `input_sample` és a `output_sample` változóban, amelyek a webszolgáltatáshoz tartozó kérések és válaszok formátumait jelölik. Ezeket a mintákat a függvény bemeneti és kimeneti függvényében használhatja `run()` . A következő scikit példa a séma generálását használja.



## <a name="power-bi-compatible-endpoint"></a>Power BI kompatibilis végpont 

Az alábbi példa bemutatja, hogyan határozhatja meg az API-alakzatokat a fenti utasítások szerint. Ez a módszer a központilag telepített webszolgáltatás Power BI való felhasználására használható. ([További információ a webszolgáltatás Power BIból való](/power-bi/service-machine-learning-integration)használatáról.)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


# providing 3 sample inputs for schema generation
numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
standard_sample_input = StandardPythonParameterType(0.0)

# This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                        'input2': pandas_sample_input, 
                                        'input3': standard_sample_input})

sample_global_parameters = StandardPythonParameterType(1.0) # this is optional
sample_output = StandardPythonParameterType([1.0, 1.0])
outputs = StandardPythonParameterType({'Results':sample_output}) # 'Results' is case sensitive

@input_schema('Inputs', sample_input) 
# 'Inputs' is case sensitive

@input_schema('GlobalParameters', sample_global_parameters) 
# this is optional, 'GlobalParameters' is case sensitive

@output_schema(outputs)

def run(Inputs, GlobalParameters): 
    # the parameters here have to match those in decorator, both 'Inputs' and 
    # 'GlobalParameters' here are case sensitive
    try:
        data = Inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> Bináris (azaz Képfájlos) adatok

Ha a modellben bináris adatok (például rendszerkép) is szerepelnek, akkor módosítania kell a `score.py` központi telepítéshez használt fájlt, hogy fogadja a nyers http-kérelmeket. A nyers adat elfogadásához használja az `AMLRequest` osztályt a bejegyzési parancsfájlban, és adja hozzá a `@rawhttp` bedekorációt a `run()` függvényhez.

Az alábbi példa egy olyan példát mutat be, `score.py` amely a bináris adatmennyiséget fogadja el:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse
from PIL import Image
import json


def init():
    print("This is init()")
    

@rawhttp
def run(request):
    print("This is run()")
    
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        file_bytes = request.files["image"]
        image = Image.open(file_bytes).convert('RGB')
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the size of the image as the response..
        return AMLResponse(json.dumps(image.size), 200)
    else:
        return AMLResponse("bad request", 500)
```


> [!IMPORTANT]
> Az `AMLRequest` osztály a `azureml.contrib` névtérben található. A névtérben lévő entitások gyakran változnak, ahogy dolgozunk a szolgáltatás fejlesztésekor. Az ebben a névtérben található bármit olyan előzetes verziónak kell tekinteni, amelyet a Microsoft nem támogat.
>
> Ha ezt a helyi fejlesztési környezetben kell tesztelni, akkor a következő paranccsal telepítheti az összetevőket:
>
> ```shell
> pip install azureml-contrib-services
> ```

Az `AMLRequest` osztály csak a score.py található nyers közzétett adat elérését teszi lehetővé, nincs ügyféloldali összetevő. Egy ügyfélről az adatok a szokásos módon kerülnek közzétételre. Például a következő Python-kód beolvas egy képfájlt, és az adatokat:

```python
import requests

uri = service.scoring_uri
image_path = 'test.jpg'
files = {'image': open(image_path, 'rb').read()}
response = requests.post(url, files=files)

print(response.json)
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>Eltérő eredetű erőforrások megosztása (CORS)

Az eltérő eredetű erőforrás-megosztás lehetővé teszi, hogy egy weblapon lévő erőforrásokat egy másik tartománytól lehessen kérni. A CORS az ügyfél kérelmével elküldött HTTP-fejléceken keresztül működik, és a szolgáltatás válaszával tért vissza. További információ a CORS és az érvényes fejlécekről: a wikipedia [több eredetű erőforrás-megosztása](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

A modell telepítésének a CORS támogatására való konfigurálásához használja a `AMLResponse` osztályt a bejegyzési parancsfájlban. Ez az osztály lehetővé teszi a fejlécek beállítását a válasz objektumon.

A következő példa a `Access-Control-Allow-Origin` bejegyzési parancsfájlból származó válasz fejlécét állítja be:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Az `AMLResponse` osztály a `azureml.contrib` névtérben található. A névtérben lévő entitások gyakran változnak, ahogy dolgozunk a szolgáltatás fejlesztésekor. Az ebben a névtérben található bármit olyan előzetes verziónak kell tekinteni, amelyet a Microsoft nem támogat.
>
> Ha ezt a helyi fejlesztési környezetben kell tesztelni, akkor a következő paranccsal telepítheti az összetevőket:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning csak a POST és a GET kérelmeket irányítja a pontozási szolgáltatást futtató tárolók számára. Ez hibákhoz vezethet, mert a böngészők a CORS-kérelmekre vonatkozó beállításokat használnak.
> 


## <a name="load-registered-models"></a>Regisztrált modellek betöltése

A következő két módon kereshet modelleket a nevezési parancsfájlban:
* `AZUREML_MODEL_DIR`: Egy környezeti változó, amely a modell helyének elérési útját tartalmazza.
* `Model.get_model_path`: Egy API, amely visszaadja a modell elérési útját a regisztrált modell nevével.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR a szolgáltatás telepítése során létrehozott környezeti változó. Ezt a környezeti változót használhatja a telepített modell (ek) helyének megkereséséhez.

A következő táblázat a telepített modellek számától függően AZUREML_MODEL_DIR értékét ismerteti:

| Üzembe helyezés | Környezeti változó értéke |
| ----- | ----- |
| Egyetlen modell | A modellt tartalmazó mappa elérési útja. |
| Több modell | Az összes modellt tartalmazó mappa elérési útja. A modellek a mappa neve és verziója szerint találhatók ( `$MODEL_NAME/$VERSION` ) |

A modell regisztrálása és üzembe helyezése során a rendszer a modelleket a AZUREML_MODEL_DIR útvonalon helyezi el, és az eredeti fájlnevek megmaradnak.

Ha szeretné beolvasni a parancsfájl elérési útját a bejegyzési parancsfájlban, akkor a környezeti változót a keresett fájl elérési útjával kell egyesíteni.

**Példa egyetlen modellre**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Több modell – példa**

Ebben a forgatókönyvben két modell van regisztrálva a munkaterületen:

* `my_first_model`: Egy fájlt ( `my_first_model.pkl` ) tartalmaz, és csak egy verzió ( `1` ) található.
* `my_second_model`: Egy fájlt ( `my_second_model.pkl` ) tartalmaz, és két verzió; `1` és `2` .

A szolgáltatás üzembe helyezése után mindkét modell a telepítés műveletben van megadva:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

A szolgáltatást futtató Docker-rendszerképben a `AZUREML_MODEL_DIR` környezeti változó tartalmazza azt a könyvtárat, ahol a modellek találhatók.
Ebben a könyvtárban mindegyik modell egy könyvtár elérési útjában található `MODEL_NAME/VERSION` . Ahol a a `MODEL_NAME` regisztrált modell neve, és `VERSION` a modell verziója. A regisztrált modellt alkotó fájlokat a rendszer ezekben a címtárakban tárolja.

Ebben a példában az elérési utak a következőek: `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` és `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

Modell regisztrálása esetén meg kell adnia a modellnek a beállításjegyzékben való kezeléséhez használt modell nevét. Ezt a nevet használja a [Model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) metódussal, hogy lekérje a modell fájljának vagy fájljainak elérési útját a helyi fájlrendszerben. Ha egy mappát vagy fájl-gyűjteményt regisztrál, az API a fájlokat tartalmazó könyvtár elérési útját adja vissza.

Modell regisztrálása esetén a nevet adja meg. A név a modell elhelyezésének helyét adja meg helyileg vagy a szolgáltatás telepítése során.

## <a name="framework-specific-examples"></a>Keretrendszer-specifikus példák

Az egyes gépi tanulási használati esetekben további bejegyzési parancsfájlokra példákat talál a következő címen:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

## <a name="next-steps"></a>Következő lépések

* [Sikertelen üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Ügyfélalkalmazások létrehozása webszolgáltatások felhasználásához](how-to-consume-web-service.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Esemény-riasztások és eseményindítók létrehozása a modell üzembe helyezéséhez](how-to-use-event-grid.md)
