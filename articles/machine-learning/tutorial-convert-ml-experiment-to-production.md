---
title: Jegyzetfüzetkód konvertálása Python-szkriptekké
titleSuffix: Azure Machine Learning
description: Az MLOpsPython kódsablon használatával éles használatra kész kódba fordíthatja a gépi tanulás kísérleti jegyzetfüzeteit. Ezután tesztelheti, üzembe helyezheti és automatizálhatja a kódot.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 37778bc096c9089e3706907fcdd6b9c816cc5fbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817485"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Oktatóanyag: Gépi tanulási kísérletek konvertálása éles Python-kódká

Ez az oktatóanyag bemutatja, hogyan konvertálhat Juptyer-notebookokat Python-szkriptekké, hogy az MLOpsPython-kódsablon és -kód Azure Machine Learning. Ez a folyamat általában egy Juptyer-notebookból származó kísérletezés/betanítási kód Python-szkriptekké konvertálására használható. Ezek a szkriptek ezután teszteléssel és CI/CD-automatizálással használhatók az éles környezetben. 

Egy gépi tanulási projekthez olyan kísérletezésre van szükség, amelyben a hipotéziseket olyan agilis eszközökkel tesztelik, mint Jupyter Notebook valós adatkészletek használatával. Ha a modell készen áll az éles üzemre, a modell kódját egy éles kódtárba kell helyezni. Bizonyos esetekben a modellkódot Python-szkriptekké kell konvertálni, hogy az éles kódtárban legyen elhelyezve. Ez az oktatóanyag a kísérleti kód Python-szkriptekbe való exportálásának ajánlott megközelítését tartalmazza.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A nemes kódot megtisztítani
> * Kód újra Jupyter Notebook függvényekbe
> * Python-szkriptek létrehozása kapcsolódó feladatokhoz
> * Egységtesztek létrehozása

## <a name="prerequisites"></a>Előfeltételek

- Hozza létre az [MLOpsPython sablont,](https://github.com/microsoft/MLOpsPython/generate) és használja a `experimentation/Diabetes Ridge Regression Training.ipynb` és a `experimentation/Diabetes Ridge Regression Scoring.ipynb` jegyzetfüzetet. Ezek a jegyzetfüzetek példaként használhatók a kísérletezésről az éles környezetbe való konvertálásra. Ezeket a jegyzetfüzeteket itt [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) találja: .
- Telepítse a(z) `nbconvert` rendszert. A Telepítés lapon kövesse az __Nbconvert__ telepítése szakasz telepítési [utasításait.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Az összes nemnessential kód eltávolítása

A kísérletezés során írt egyes kódok csak felderítési célokra szolgálnak. Ezért a kísérleti kód éles kódká konvertálásának első lépése ennek a nem fontos kódnak az eltávolítása. A nem fontos kódok eltávolítása a kódot is karbantarthatóbb lesz. Ebben a szakaszban kódot fog eltávolítani a `experimentation/Diabetes Ridge Regression Training.ipynb` jegyzetfüzetből. A és a alakzatát és a cellát hívó utasítások csak adatfeltárásra valók, és `X` `y` `features.describe` eltávolíthatók. A felesleges kód eltávolítása után a kódnak a következő kódhoz kell hasonlítanának `experimentation/Diabetes Ridge Regression Training.ipynb` Markdown nélkül:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg_model.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Kód újragyártott függvényekbe

Másodszor a Jupyter-kódot függvényekbe kell újragyártatni. A kód függvényekbe való újrafactorolása megkönnyíti az egységtesztelést, és könnyebben karbantarthatóvé teszi a kódot. Ebben a szakaszban a következőt fogja újragyártatni:

- A Diabetes Diabetes Regression Training jegyzetfüzet( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- A Diabetes Diabetes Regression Scoring (Cukorbetegség regressziója pontozása) `experimentation/Diabetes Ridge Regression Scoring.ipynb` jegyzetfüzet( )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Diabetes Diabetes Regression Training notebook újrafactorása függvényekbe

A `experimentation/Diabetes Ridge Regression Training.ipynb` -ban kövesse az alábbi lépéseket:

1. Hozzon létre egy nevű `split_data` függvényt, amely felosztja az adatkeretet tesztre, és betanítja az adatokat. A függvénynek paraméterként kell az adatkeretet vennie, és egy szótárt kell visszaadni, amely tartalmazza a és a `df` `train` `test` kulcsokat.

    Helyezze át az  Adatok felosztása betanítás és ellenőrzési készletekbe fejléc alatti kódot a függvénybe, és módosítsa úgy, hogy `split_data` visszaadja az `data` objektumot.

1. Hozzon létre egy nevű függvényt, amely a `train_model` paramétereket veszi fel, `data` és egy `args` betanított modellt ad vissza.

    Helyezze át a Betanítás készlet *betanítása alatti* kódot a függvénybe, és módosítsa úgy, hogy `train_model` visszaadja az `reg_model` objektumot. Távolítsa el `args` a szótárt, az értékek a paraméterből fognak `args` előjönni.

1. Hozzon létre egy nevű függvényt, amely a és paramétereket veszi fel, majd kiértékeli a modellt, majd visszaadja a betanított modell `get_model_metrics` `reg_model` metrika-szótárát. `data`

    Helyezze át a kódot a *Validate Model on Validation Set* (Modell ellenőrzése ellenőrzési készleten) fejléc alatt a függvénybe, és módosítsa úgy, hogy `get_model_metrics` visszaadja az `metrics` objektumot.

A három függvénynek a következőnek kell lennie:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Még mindig `experimentation/Diabetes Ridge Regression Training.ipynb` a -ban maradva, kövesse az alábbi lépéseket:

1. Hozzon létre egy nevű új függvényt, amely `main` nem vesz fel paramétereket, és semmit sem ad vissza.
1. Helyezze át az "Adatok betöltése" fejléc alatti kódot a `main` függvénybe.
1. Adja hozzá az újonnan megírt függvények meghívását a `main` függvényhez:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Helyezze át a "Modell mentése" fejléc alatti kódot a `main` függvénybe.

A `main` függvénynek az alábbi kódhoz hasonlónak kell lennie:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

Ebben a szakaszban nem szabad olyan kódnak lennie a jegyzetfüzetben, amely nem egy függvényben van, csak az első cellában lévő importálási utasításokból.

Adjon hozzá egy utasítást, amely a függvényt `main` hívja meg.

```python
main()
```

Az újragyártás után a következő kódhoz hasonlónak kell lennie `experimentation/Diabetes Ridge Regression Training.ipynb` Markdown nélkül:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Cukorbetegség regressziópontozása jegyzetfüzet újraszámítása függvényekbe

`experimentation/Diabetes Ridge Regression Scoring.ipynb`A-ban kövesse az alábbi lépéseket:

1. Hozzon létre egy nevű új függvényt, amely `init` nem vesz fel paramétereket, és semmit sem ad vissza.
1. Másolja a "Modell betöltése" fejléc alatti kódot a `init` függvénybe.

A `init` függvénynek az alábbi kódhoz hasonlónak kell lennie:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

A függvény létrehozása után cserélje le a "Modell betöltése" fejléc alatti összes kódot egyetlen hívásra `init` a `init` következőképpen:

```python
init()
```

`experimentation/Diabetes Ridge Regression Scoring.ipynb`A-ban kövesse az alábbi lépéseket:

1. Hozzon létre egy nevű új függvényt, amely paraméterként a és a függvényt veszi fel, és az eredmények szótárát adja vissza `run` `raw_data` az alábbiak `request_headers` szerint:

    ```python
    {"result": result.tolist()}
    ```

1. Másolja az Adatok előkészítése és az Adatok pontozása fejléc alatti kódot a `run` függvénybe.

    A függvénynek a következő kódhoz kell hasonlítanunk (ne felejtse el eltávolítani a és változókat beállítva az utasításokat, amelyeket később a függvény `run` `raw_data` hív `request_headers` `run` meg):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

A függvény létrehozása után cserélje le az "Adatok előkészítése" és az "Adatok pontozása" fejléc alatti összes kódot a `run` következőre:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Az előző kód beállítja a és a változót, a és a használatával hívja meg a `raw_data` `request_header` `run` függvényt, és `raw_data` `request_header` kinyomtatja az előrejelzéseket.

Az újragyártás után a következő kódhoz kell hasonlítanunk `experimentation/Diabetes Ridge Regression Scoring.ipynb` Markdown nélkül:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Kapcsolódó függvények kombinálása Python-fájlokban

Harmadszor, a kapcsolódó függvényeket Egyesíteni kell Python-fájlokban, hogy a kód jobban használható legyen. Ebben a szakaszban Python-fájlokat fog létrehozni a következő jegyzetfüzetek számára:

- A Diabetes Diabetes Regression Training jegyzetfüzet( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- A Diabetes Diabetes Regression Scoring (Cukorbetegség regressziója pontozása) `experimentation/Diabetes Ridge Regression Scoring.ipynb` jegyzetfüzet( )

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Python-fájl létrehozása a Diabetes Diabetes Regression Training jegyzetfüzethez

Konvertálja a jegyzetfüzetet végrehajtható szkriptre a következő utasítás parancssorban való futtatásával, amely a csomagot és a elérési útját `nbconvert` `experimentation/Diabetes Ridge Regression Training.ipynb` használja:

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

Miután a jegyzetfüzet át lett alakítva a `train.py` alkalmazásra, távolítsa el a nem kívánt megjegyzéseket. Cserélje le a fájl végén található hívást a következő kódhoz hasonló feltételes `main()` hívásra:

```python
if __name__ == '__main__':
    main()
```

A `train.py` fájlnak az alábbi kódhoz hasonlónak kell lennie:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py` most már meghívható egy terminálból a `python train.py` futtatásával.
A függvénye `train.py` más fájlokból is hívható.

Az `train_aml.py` MLOpsPython-adattár könyvtárában található fájl egy kísérlet futtatásának kontextusában `diabetes_regression/training` `train.py` Azure Machine Learning függvényeket. A függvények az egységtesztek során is hívhatóak, amelyekről az útmutató egy későbbi, későbbi, is tartalmaz majd további útmutatót.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Python-fájl létrehozása a Diabetes Diabetes Regression Scoring jegyzetfüzethez

A következő utasítás parancssorban való futtatásával fedje le a notebookot egy végrehajtható parancsfájlba, amely a csomagot és a elérési útját `nbconvert` `experimentation/Diabetes Ridge Regression Scoring.ipynb` használja:

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

Miután a jegyzetfüzet át lett alakítva a `score.py` fájlra, távolítsa el a nem kívánt megjegyzéseket. A `score.py` fájlnak az alábbi kódhoz hasonlónak kell lennie:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

A változónak globálisnak kell lennie, hogy látható legyen `model` a szkriptben. Adja hozzá a következő utasítást a függvény `init` elejéhez:

```python
global model
```

Az előző utasítás hozzáadása után a `init` függvénynek az alábbi kódhoz hasonlónak kell lennie:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Egységtesztek létrehozása minden Python-fájlhoz

Negyedikként hozzon létre egységteszteket a Python-függvények számára. Az egységtesztek megvédik a kódot a funkcionális regresszióktól, és megkönnyítik a karbantartást. Ebben a szakaszban egységteszteket fog létrehozni a függvények `train.py` számára.

`train.py` Több függvényt is tartalmaz, de ebben az oktatóanyagban csak egy egységtesztet hozunk létre a függvényhez a `train_model` Pytest keretrendszer használatával. A Pytest nem az egyetlen Python-egységtesztelési keretrendszer, de az egyik leggyakrabban használt. További információkért látogasson el a [Pytest webhelyére.](https://pytest.org)

Az egységtesztek általában három fő műveletet tartalmaznak:

- Objektum elrendezése – szükséges objektumok létrehozása és beállítása
- Objektumra való alkalmazás
- A várt feltétel helyessíteni

Az egységteszt néhány nem kódolt adatot és argumentumot használva hívja meg a függvényt, majd ellenőrzi, hogy a várt módon viselkedett-e. Ennek érdekében az eredményül kapott betanított modellel előrejelzést hoz létre, majd összehasonlítja az előrejelzést egy várt `train_model` `train_model` értékkel.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan konvertálható kísérletről éles kódra, további információkért és a következő lépésekért tekintse meg az alábbi hivatkozásokat:

+ [MLOpsPython:](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md)CI-/CD-folyamat létrehozása saját modell betanítéséhez, kiértékeléséhez és üzembe helyezéséhez az Azure Pipelines és a Azure Machine Learning
+ [Azure ML-kísérletek futtatásának és metrikainak figyelése](./how-to-log-view-metrics.md)
+ [Adatok ML-webszolgáltatási végpontokról való monitorozása és gyűjtése](./how-to-enable-app-insights.md)