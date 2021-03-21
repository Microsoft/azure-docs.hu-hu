---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93325278"
---
A bejegyzés parancsfájlja fogadja az üzembe helyezett webszolgáltatásnak küldött adatokat, majd továbbítja azokat a modellnek. Ezután a modell által visszaadott választ visszaküldi az ügyfélnek. *A szkript a modellre jellemző*. Ismernie kell a modell által várt és visszaadott adatok értékét.

A következő két dolog szükséges a beléptetési parancsfájlban:

1. A modell betöltése (egy nevű függvény használatával `init()` )
1. A modell futtatása bemeneti adatokon (a nevű függvény használatával `run()` )

Nézzük végig ezeket a lépéseket részletesen.

### <a name="writing-init"></a>Init () írása 

#### <a name="loading-a-registered-model"></a>Regisztrált modell betöltése

A regisztrált modelleket egy nevű környezeti változó által mutatott útvonalon tárolja a rendszer `AZUREML_MODEL_DIR` . A pontos címtár-struktúrával kapcsolatos további információkért lásd: [a modell fájljainak megkeresése a belépési parancsfájlban](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models) .

#### <a name="loading-a-local-model"></a>Helyi modell betöltése

Ha úgy döntött, hogy regisztrálja a modellt, és a forrás könyvtára részeként adta át a modellt, akkor a helyileg is elolvashatja a modellt, ha a modell elérési útját a pontozási parancsfájlhoz viszonyítva átadja. Ha például a címtára a következőképpen lett strukturálva:

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

a modellek a következő Python-kóddal tölthetők be:

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>Futtatás írása ()

`run()` minden alkalommal fut, amikor a modell egy pontozási kérést kap, és a kérelem törzsét arra kéri, hogy JSON-dokumentum legyen a következő szerkezettel:

```json
{
    "data": <model-specific-data-structure>
}

```

A bemenet `run()` egy Python-karakterlánc, amely az "adat" kulcsot követi.

Az alábbi példa bemutatja, hogyan tölthető be a regisztrált scikit-modell, és hogyan szerezheti be a NumPy-információkkal:

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

A speciális példákhoz, beleértve az automatikus hencegés sémájának generálását és bináris (pl. képfájlok) adatát, olvassa el [a következő cikket a speciális bejegyzés parancsfájl-létrehozásáról.](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)