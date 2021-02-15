---
title: 'Oktatóanyag: a prediktív modell létrehozása jegyzetfüzettel (2. rész)'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe gépi tanulási modellt egy Jupyter Notebook programkódjának használatával. Hozzon létre egy pontozási parancsfájlt is, amely meghatározza a bemenetet és a kimenetet a Microsoft Power BIba való egyszerű integrációhoz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 00d5fa43245fb25b8ee99a0523d680bef891b71e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387002"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Oktatóanyag: Power BI integráció – a prediktív modell létrehozása Jupyter Notebookvel (2. rész)

Az oktatóanyag 1. részében egy prediktív gépi tanulási modellt kell betanítania és üzembe helyezni egy Jupyter Notebook programkód használatával. Létrehoz egy pontozási parancsfájlt is, amely meghatározza a modell bemeneti és kimeneti sémáját a Power BIba való integrációhoz.  A 2. részben a modellt fogja használni a Microsoft Power BIban várható eredmények előrejelzéséhez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Jupyter Notebook létrehozása.
> * Hozzon létre egy Azure Machine Learning számítási példányt.
> * Egy regressziós modell betanítása a scikit-Learn használatával.
> * Olyan pontozási parancsfájlt írhat, amely meghatározza a bemenetet és a kimenetet a Microsoft Power BIba való egyszerű integrációhoz.
> * A modell üzembe helyezése valós idejű pontozási végponton.

A Power BI-ben használni kívánt modell létrehozása és üzembe helyezése három módon történik.  Ez a cikk az "A" lehetőséget ismerteti, amely a modellek jegyzetfüzetek használatával történő betanítását és üzembe helyezését mutatja be.  Ez a lehetőség a kód első létrehozási felülete. A Azure Machine Learning Studio üzemeltetett Jupyter jegyzetfüzeteket használja. 

Ehelyett használhatja a további lehetőségek egyikét:

* [B. lehetőség: modellek betanítása és üzembe helyezése a Azure Machine learning Designer használatával](tutorial-power-bi-designer-model.md). Ez az alacsony kódú szerzői művelet egy fogd és vidd felhasználói felületet használ.
* [C. lehetőség: modellek betanítása és üzembe helyezése automatizált gépi tanulás használatával](tutorial-power-bi-automated-model.md). Ez a kód nélküli létrehozási élmény teljes mértékben automatizálja az adatelőkészítést és a modell betanítását.


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs előfizetése, használhat [ingyenes próbaverziót](https://aka.ms/AMLFree). 
- Egy Azure Machine Learning-munkaterület. Ha még nem rendelkezik munkaterülettel, tekintse meg [Azure Machine learning munkaterületek létrehozása és kezelése](./how-to-manage-workspace.md#create-a-workspace)című témakört.
- A Python nyelv és a gépi tanulási munkafolyamatok bevezető ismerete.

## <a name="create-a-notebook-and-compute"></a>Jegyzetfüzet és számítás létrehozása

A [**Azure Machine learning Studio**](https://ml.azure.com) kezdőlapon válassza az **új**  >  **Jegyzetfüzet** létrehozása elemet:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Képernyőkép a jegyzetfüzetek létrehozásáról.":::
 
Az **új fájl létrehozása** lapon:

1. Nevezze el a jegyzetfüzetet (például *my_model_notebook*).
1. Módosítsa a **fájltípust** **jegyzetfüzetre**.
1. Válassza a **Létrehozás** lehetőséget. 
 
Ezután futtassa a kód celláit, hozzon létre egy számítási példányt, és csatolja a jegyzetfüzethez. Első lépésként válassza a plusz ikont a jegyzetfüzet tetején:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="A számítási példányok létrehozását bemutató képernyőkép.":::

A **számítási példány létrehozása** lapon:

1. Válassza ki a CPU virtuális gép méretét. Ebben az oktatóanyagban választhat egy **Standard_D11_v2**, 2 maggal és 14 GB RAM memóriával.
1. Kattintson a **Tovább** gombra. 
1. A **beállítások konfigurálása** lapon adjon meg egy érvényes **számítási nevet**. Az érvényes karakterek: nagybetűk, kisbetűk, számjegyek és kötőjelek (-).
1. Válassza a **Létrehozás** lehetőséget.

A jegyzetfüzetben észreveheti, hogy a **kiszámított** ciánkék (számítás) elem melletti kör is látható. Ez a színváltozás azt jelzi, hogy a számítási példány létrehozása folyamatban van:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Képernyőfelvétel a létrehozott számítási feladatokról.":::

> [!NOTE]
> A számítási példány 2 – 4 percet is igénybe vehet.

A számítás kiépítés után a jegyzetfüzet használatával futtathat programkódot. A cellában például beírhatja a következő kódot:

```python
import numpy as np

np.sin(3)
```

Ezután válassza a SHIFT + ENTER (vagy a CTRL + ENTER billentyűkombinációt, vagy kattintson a cella melletti **Lejátszás** gombra). A következő kimenetnek kell megjelennie:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="A cella kimenetét ábrázoló képernyőkép.":::

Most már készen áll a gépi tanulási modell létrehozására.

## <a name="build-a-model-by-using-scikit-learn"></a>Modell létrehozása a scikit használatával – Learn

Ebben az oktatóanyagban a [cukorbetegség adatkészletet](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)használja. Ez az adatkészlet elérhető az [Azure Open adatkészletekben](https://azure.microsoft.com/services/open-datasets/).


### <a name="import-data"></a>Adatok importálása

Az adatimportáláshoz másolja a következő kódot, és illessze be egy új, a jegyzetfüzetbe beillesztett *cellába* .

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

A `X_df` pandák adatkeret 10 alapszintű bemeneti változót tartalmaz. Ezek a változók például az életkor, a szex, a testtömeg-index, az átlagos vérnyomás és a hat Vérszérum mérése. A `y_df` pandák adatkeret a cél változó. A betegség előrehaladásának mennyiségi mértékét egy évvel az alaptervet követően egy évig tartalmazza. Az adatkeret 442 rekordot tartalmaz.

### <a name="train-the-model"></a>A modell betanítása

Hozzon létre egy új *kódlapot* a jegyzetfüzetben. Ezután másolja be a következő kódot, és illessze be a cellába. Ez a kódrészlet egy Ridge regressziós modellt hoz létre, és a modellt a Python-lé formátum használatával szerializálja.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X_df,y_df)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>A modell regisztrálása

A modell fájlján kívül a regisztrált modell a metaadatokat is tárolja. A metaadatok tartalmazzák a modell leírását, a címkéket és a keretrendszer információit. 

A metaadatok akkor hasznosak, ha a munkaterületen a modelleket kezeli és telepíti. A címkék használatával például kategorizálhatja a modelleket, és szűrőket alkalmazhat a munkaterület modelljeinek listázásához. Ha ezt a modellt a scikit-Learn keretrendszerrel jelöli meg, egyszerűbbé válik a webes szolgáltatásként való üzembe helyezése.

Másolja a következő kódot, majd illessze be egy új, a jegyzetfüzetbe beillesztett *cellába* .

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

A modellt Azure Machine Learning Studio is megtekintheti. A bal oldali menüben válassza a **modellek** elemet:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="A modellek megtekintését bemutató képernyőkép.":::

## <a name="define-the-scoring-script"></a>Pontozási parancsfájl definiálása

Ha olyan modellt telepít, amely integrálva lesz Power BIba, meg kell határoznia egy Python- *pontozási parancsfájlt* és egy egyéni környezetet. A pontozási parancsfájl két függvényt tartalmaz:

- A `init()` függvény a szolgáltatás indításakor fut. Betölti a modellt (amelyet a rendszer automatikusan letölt a modell beállításjegyzékből) és deszerializálja.
- A `run(data)` függvény akkor fut le, ha a szolgáltatás hívása olyan bemeneti adatokat tartalmaz, amelyeket fel kell venni. 

>[!NOTE]
> Az alábbi kódban szereplő Python-dekorációk határozzák meg a bemeneti és kimeneti adatok sémáját, ami fontos a Power BIba való integráláshoz.

Másolja be a következő kódot, és illessze be egy új *kódrészletbe* a jegyzetfüzetben. A következő kódrészlet tartalmaz egy cella magict, amely a kódot egy *score.py* nevű fájlba írja.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Egyéni környezet definiálása

Ezután adja meg a környezetet a modell kiértékeléséhez. A környezetben adja meg a Python-csomagokat, például a pandák és a scikit-Learn csomagot, hogy a pontozási parancsfájl (*score.py*) megköveteli.

A környezet definiálásához másolja a következő kódot, és illessze be egy új *kódrészletbe* a jegyzetfüzetben.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>A modell üzembe helyezése

A modell üzembe helyezéséhez másolja a következő kódot, és illessze be a jegyzetfüzet új *kódjába* :

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> A szolgáltatás üzembe helyezése 2 – 4 percet is igénybe vehet.

Ha a szolgáltatás sikeresen települ, a következő kimenetnek kell megjelennie:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

A szolgáltatást Azure Machine Learning Studio is megtekintheti. A bal oldali menüben válassza a **végpontok** lehetőséget:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="A szolgáltatás megtekintését bemutató képernyőkép.":::

Javasoljuk, hogy tesztelje a webszolgáltatást, és győződjön meg róla, hogy az a vártnak megfelelően működik-e. A jegyzetfüzet visszaküldéséhez Azure Machine Learning Studio a bal oldali menüben válassza a **jegyzetfüzetek** lehetőséget. Ezután másolja be a következő kódot, és illessze be a jegyzetfüzet új *kódjába* a szolgáltatás teszteléséhez.

```python
import json

input_payload = json.dumps({
    'data': X_df[0:2].values.tolist()
})

output = service.run(input_payload)

print(output)
```

A kimenetnek a következő JSON-struktúrához hasonlóan kell kinéznie: `{'predict': [[205.59], [68.84]]}` .

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan hozhat létre és helyezhet üzembe egy modellt, hogy Power BI lehessen használni. A következő részben megtudhatja, hogyan használhatja ezt a modellt egy Power BI-jelentésben.

> [!div class="nextstepaction"]
> [Oktatóanyag: modell felhasználása Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
