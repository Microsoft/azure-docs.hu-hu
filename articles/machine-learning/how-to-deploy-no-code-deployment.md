---
title: Nincs programkód-telepítés (előzetes verzió)
titleSuffix: Azure Machine Learning
description: A kód központi telepítése nem teszi lehetővé, hogy egy modellt webszolgáltatásként telepítsen anélkül, hogy manuálisan létre kellene hoznia egy belépési parancsfájlt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: a17126695aa5138d1df7fd17cfaa2f5f75ad1004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324925"
---
# <a name="preview-no-code-model-deployment"></a>Előnézet Nem kód modell telepítése

A nem kód modellek üzembe helyezése jelenleg előzetes verzióban érhető el, és a következő Machine learning-keretrendszereket támogatja:

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel formátuma
A TensorFlow-modelleket **SavedModel formátumban** kell regisztrálni, hogy a kód nélküli üzembe helyezéssel működjön.

A SavedModel létrehozásával kapcsolatos információkért tekintse meg [ezt a hivatkozást](https://www.tensorflow.org/guide/saved_model) .

A DockerHub-t [kiszolgáló TensorFlow](https://registry.hub.docker.com/r/tensorflow/serving/tags)a "címkék" területen felsorolt bármely TensorFlow-verziót támogatjuk.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>ONNX-modellek

A ONNX-modell regisztrálása és üzembe helyezése bármely ONNX-következtetési gráf esetében támogatott. Az előfeldolgozási és a utófeldolgozás lépések jelenleg nem támogatottak.

Íme egy példa arra, hogyan regisztrálhatók és telepíthetők egy MNIST ONNX-modell:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

A modellek kiértékeléséhez tekintse meg a [webszolgáltatásként üzembe helyezett Azure Machine learning modell felhasználása](./how-to-consume-web-service.md)című témakört. Számos ONNX-projekt protopuf-fájlokat használ a betanítási és érvényesítési adatmennyiségek tömörítéséhez, ami megnehezíti a szolgáltatás által várt adatformátum megismeretét. Modell fejlesztőként dokumentálja a fejlesztőket:

* Bemeneti formátum (JSON vagy bináris)
* Bemeneti adatok alakja és típusa (például az alakban lévő lebegőpontos tömb [100100, 3])
* Tartományi információk (például egy képnek, a színtérnek, az összetevők sorrendjének, valamint az értékek normalizálása)

Ha a Pytorch-t használja, a [Pytorch-ről a ONNX-re való exportáláskor a rendszer](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) az átalakítással és korlátozásokkal kapcsolatos részleteket tartalmaz. 

## <a name="scikit-learn-models"></a>Scikit – modellek megismerése

Az összes beépített scikit-típushoz nem támogatott a programkód-modell üzembe helyezése.

Íme egy példa arra, hogyan regisztrálhat és helyezhet üzembe egy sklearn-modellt további kód nélkül:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> A predict_proba támogató modellek alapértelmezés szerint ezt a metódust fogják használni. Ennek felülbírálásához a következőképpen módosíthatja a POST törzset:

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> Ezeket a függőségeket az előre elkészített scikit-Learn következtetési tároló tartalmazza:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
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