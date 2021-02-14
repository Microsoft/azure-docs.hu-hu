---
title: A kísérletek nyomon követése és üzembe helyezése
titleSuffix: Azure Data Science Virtual Machine
description: Megtudhatja, hogyan követheti nyomon és naplózhatja a Data Science Virtual Machinei kísérleteket Azure Machine Learning és/vagy MLFlow.
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 3795d531c5c4c543587ab817c05cd1cfeea6be06
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518538"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Kísérletek követése és modellek üzembe helyezése Azure Machine Learning

A kísérletek és a figyelési futtatási metrikák nyomon követésével növelheti a modell létrehozásának folyamatát. Ebből a cikkből megtudhatja, hogyan adhat hozzá naplózási kódot a betanítási szkripthez a [MLflow](https://mlflow.org/) API használatával, és nyomon követheti a kísérletet Azure Machine Learningban.

Az alábbi ábra azt szemlélteti, hogy a MLflow nyomon követésével nyomon követheti a kísérlet futtatási metrikáit, és az Azure Machine Learning munkaterületen tárolhatja a modell összetevőit.

![kísérletek követése](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>Előfeltételek

* Létre kell [hoznia egy Azure Machine learning-munkaterület](../how-to-manage-workspace.md#create-a-workspace)

## <a name="create-a-new-notebook"></a>Új jegyzetfüzet létrehozása

A Azure Machine Learning és a MLFlow SDK előre telepítve van a Data Science VM, és a **azureml_py36_ \*** Conda-környezetben érhető el. A JupyterLab-ben kattintson az indítóra, és válassza ki a következő kernelt:

![kernel kiválasztása](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>A munkaterület beállítása

Lépjen a [Azure Portalra](https://portal.azure.com) , és válassza ki az előfeltételek részeként kiépített munkaterületet. Ekkor megjelenik a __letöltés config.js__ (lásd alább) – töltse le a konfigurációt, és győződjön meg róla, hogy az a DSVM található munkakönyvtárában van tárolva.

![Konfigurációs fájl beolvasása](./media/how-to-track-experiments/experiment-tracking-2.png)

A konfiguráció olyan adatokat tartalmaz, mint például a munkaterület neve, az előfizetés stb., és ez azt jelenti, hogy nem kell ezeket a paramétereket megadnia.

## <a name="track-dsvm-runs"></a>DSVM-futtatások nyomon követése

Adja hozzá a következő kódot a jegyzetfüzethez (vagy parancsfájlhoz) a AzureML-munkaterület objektum beállításához.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
A nyomkövetési URI-azonosító legfeljebb egy órán belül érvényes. Ha némi üresjárati idő után újraindítja a szkriptet, a get_mlflow_tracking_uri API-val új URI-t kap.

### <a name="load-the-data"></a>Az adatok betöltése

Ez a példa a cukorbetegség-adathalmazt, egy jól ismert kis adatkészletet használ, amely a scikit-Learn szolgáltatáshoz tartozik. Ez a cella betölti az adatkészletet, és felosztja véletlenszerű betanítási és tesztelési készletekbe.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>Nyomkövetés hozzáadása

A Azure Machine Learning SDK használatával vegyen fel kísérlet-követést, és töltsön fel egy megőrzött modellt a kísérlet futtatása rekordba. A következő kód naplófájlokat szúr be, és feltölt egy modellt a kísérlet futtatásához. A modell regisztrálva van a Azure Machine Learning modell beállításjegyzékében is.

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Futtatások megtekintése Azure Machine Learning

Megtekintheti a kísérlet futtatását [Azure Machine learning Studioban](https://ml.azure.com). Kattintson a __kísérletek__ elemre a bal oldali menüben, és válassza a "experiment_with_mlflow" (vagy ha úgy döntött, hogy a kísérletet a fenti kódrészletben eltérően nevezi el, kattintson a használt névre):

![kísérlet kiválasztása](./media/how-to-track-experiments/mlflow-experiments.png)

Ekkor meg kell jelennie a naplózott Meaned error (MSE):

![MSE](./media/how-to-track-experiments/mlflow-experiments-2.png)

Ha a Futtatás gombra kattint, a __kimenetek és a naplókban__ a többi részlet és a pácolt modell is megjelenik.

## <a name="deploy-model-in-azure-machine-learning"></a>Modell üzembe helyezése Azure Machine Learning

Ebben a szakaszban felvázoljuk, hogyan helyezhetők üzembe a DSVM betanított modellek a Azure Machine Learning.

### <a name="step-1-create-inference-compute"></a>1. lépés: következtetés létrehozása a számításhoz

A [AzureML Studio](https://ml.azure.com) bal oldali menüjében kattintson a __számítás__ , majd a megjelenő __fürtök__ fülre. Ezután kattintson az __+ új__ elemre az alább leírtak szerint:

![Következtetési számítás létrehozása](./media/how-to-track-experiments/mlflow-experiments-6.png)

Az __új viszonyítási fürt__ ablaktáblán adja meg a következő adatokat:

* Számítási név
* Kubernetes szolgáltatás – válassza az új létrehozása lehetőséget.
* Régió kiválasztása
* Válassza ki a virtuális gép méretét (ebben az oktatóanyagban az alapértelmezett Standard_D3_v2 elegendő)
* Fürt célja – __fejlesztési-tesztelési__ lehetőség kiválasztása
* A csomópontok számának __1__ értékűnek kell lennie
* Hálózati konfiguráció – alapszintű

Ezután kattintson a __Létrehozás__ gombra.

![számítás részletei](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>2. lépés: a nem Code következtetési szolgáltatás üzembe helyezése

Amikor regisztrálta a modellt a kódban a használatával `register_model` , a keretrendszert sklearn-ként adtunk meg. A Azure Machine Learning a következő keretrendszerek esetében nem támogatja a kód központi telepítését:

* scikit-learn
* Tensorflow SaveModel formátuma
* ONNX modell formátuma

A kód nélküli központi telepítés azt jelenti, hogy az adott pontozási parancsfájl megadásához nem szükséges, hogy a modellből egyenesen lehessen telepíteni.

A diabétesz modell üzembe helyezéséhez lépjen a [Azure Machine learning Studio](https://ml.azure.com) bal oldali menüjére, és válassza a __modellek__ lehetőséget. Ezután kattintson a regisztrált diabetes_modelra:

![Modell kiválasztása](./media/how-to-track-experiments/mlflow-experiments-3.png)

Ezután kattintson a __telepítés__ gombra a modell részletei ablaktáblán:

![Üzembe helyezés](./media/how-to-track-experiments/mlflow-experiments-4.png)

A modellt az 1. lépésben létrehozott következtetési fürt (Azure Kubernetes szolgáltatás) számára fogjuk üzembe helyezni. Adja meg az alábbi adatokat a szolgáltatás nevének megadásával, valamint az AK számítási fürt nevét (az 1. lépésben létrehozva). Azt is javasoljuk, hogy növelje a __CPU-foglalási kapacitást__ 1 (0,1) értékre, és a __memória tartalék kapacitása__ 1 (0,5) értékre, ha a __speciális__ gombra kattint, és kitölti a részleteket. Ezután kattintson a __telepítés__ elemre.

![központi telepítés részletei](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>3. lépés: felhasználás

Ha a modell üzembe helyezése sikeresen megtörtént, akkor a következőnek kell megjelennie (az oldal eléréséhez kattintson a bal oldali menüben található végpontok elemre > majd kattintson a telepített szolgáltatás nevére):

![Modell felhasználása](./media/how-to-track-experiments/mlflow-experiments-8.png)

Látnia kell, hogy az üzemelő példány állapota nem változik __kifogástalan__ __állapotba__ . Ezen felül a részletek szakasz a REST-végpontot és a hencegő URL-címeket is tartalmazza, amelyeket az alkalmazás fejlesztője használhat a ML-modell integrálására az alkalmazásokba.

A végpontot a [Poster](https://www.postman.com/)használatával tesztelheti, vagy használhatja a AzureML SDK-t is:

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>4. lépés: karbantartás

Törölje az 1. lépésben létrehozott következtetési számítást, hogy ne legyenek folyamatos számítási költségek. A Azure Machine Learning Studio bal oldali menüjében kattintson a számítás >-fürtökre > a számítási > Törlés elemre.

## <a name="next-steps"></a>Következő lépések

* További információ a [modellek üzembe helyezéséről a AzureML-ben](../how-to-deploy-and-where.md)
