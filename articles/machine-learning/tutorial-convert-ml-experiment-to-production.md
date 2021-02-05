---
title: Jegyzetfüzet-kód konvertálása Python-parancsfájlokba
titleSuffix: Azure Machine Learning
description: Állítsa be a gépi tanulási kísérleti jegyzetfüzeteket éles használatra kész kódra a MLOpsPython-kód sablonnal. Ezután tesztelheti, telepítheti és automatizálhatja a kódot.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: cdfeb2fdeefabb0d2d4af2fb63222adda5d023fb
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576025"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Oktatóanyag: ML-kísérletek konvertálása éles Python-kódra

Ebből az oktatóanyagból megtudhatja, hogyan alakíthatja át a Juptyer-jegyzetfüzeteket a Python-parancsfájlokba, hogy a tesztelés és az automatizálás felhasználóbarát legyen a MLOpsPython-kód sablonnal és Azure Machine Learningával. Ez a folyamat általában egy Juptyer-jegyzetfüzetből származó kísérletezési/tanítási kód készítésére és Python-parancsfájlokba való átalakítására szolgál. Ezeket a szkripteket ezután tesztelési és CI/CD-automatizálásra lehet használni az éles környezetben. 

A Machine learning-projektek kísérletezést igényelnek, ahol a hipotézisek olyan agilis eszközökkel vannak tesztelve, mint a valós adathalmazok használatával Jupyter Notebook. Miután a modell készen áll az éles üzemre, a modell kódját egy üzemi programkódba kell helyezni. Bizonyos esetekben a modell kódját át kell alakítani Python-szkriptekre az üzemi programkódba való adattárházba való helyezéshez. Ez az oktatóanyag a kísérletezési kódok Python-szkriptekre való exportálásának ajánlott módszerét ismerteti.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Nem nélkülözhetetlen kód tisztítása
> * Refactor Jupyter Notebook kódot a functions szolgáltatásba
> * Python-parancsfájlok létrehozása kapcsolódó feladatokhoz
> * Egységtesztek létrehozása

## <a name="prerequisites"></a>Előfeltételek

- A [MLOpsPython-sablon](https://github.com/microsoft/MLOpsPython/generate) előállítása és a `experimentation/Diabetes Ridge Regression Training.ipynb` és a `experimentation/Diabetes Ridge Regression Scoring.ipynb` jegyzetfüzetek használata. Ezek a jegyzetfüzetek példaként szolgálnak a kísérletezésről a termelésre való áttérésre. Ezek a jegyzetfüzetek a következő címen találhatók: [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) .
- Telepítse a(z) `nbconvert` rendszert. [A telepítés lapon csak](https://nbconvert.readthedocs.io/en/latest/install.html) a telepítési utasításokat kövesse a __nbconvert telepítése__ című részben.

## <a name="remove-all-nonessential-code"></a>Az összes nem nélkülözhetetlen kód eltávolítása

A kísérletezés során írt kódok némelyike csak tájékozódási célokra szolgál. Ezért a kísérleti kód éles kódban való átalakításának első lépése a nem nélkülözhetetlen kód eltávolítása. A nem nélkülözhetetlen kód eltávolításával a kód még karbantartható lesz. Ebben a szakaszban eltávolítja a kódot a `experimentation/Diabetes Ridge Regression Training.ipynb` jegyzetfüzetből. A és a cellák alakját kinyomtató utasítások `X` `y` `features.describe` csak az adatfeltáráshoz szükségesek, és eltávolíthatók. A nem nélkülözhetetlen kód eltávolítása után `experimentation/Diabetes Ridge Regression Training.ipynb` a következő kódhoz hasonlóan kell kinéznie, Markdown:

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

## <a name="refactor-code-into-functions"></a>Újrabontási kód a függvényekbe

Másodszor, a Jupyter-kódot át kell alakítani a függvényekbe. A kód into functions-re való újrabontása megkönnyíti az egységek tesztelését, és a kód karbantartását is lehetővé teszi. Ebben a szakaszban a következőt fogja újraválasztani:

- A diabetes Ridge regressziós betanítása notebook ( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- A diabetes Ridge regressziós pontozási notebook ( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refaction diabetes Ridge regressziós betanítási jegyzetfüzet a functions szolgáltatásba

A alkalmazásban `experimentation/Diabetes Ridge Regression Training.ipynb` hajtsa végre a következő lépéseket:

1. Hozzon létre egy nevű függvényt `split_data` az adatkeret tesztelési és betanítási célú felosztásához. A függvénynek paraméterként kell elvégeznie a dataframe `df` , és a kulcsokat és a-t tartalmazó szótárt kell visszaadnia `train` `test` .

    Helyezze át a kódot az *adat felosztása betanítási és érvényesítési készletek* fejlécbe a `split_data` függvénybe, és módosítsa az objektum visszaadásához `data` .

1. Hozzon létre egy nevű függvényt `train_model` , amely végrehajtja a paramétereket, `data` `args` és beolvassa a betanított modellt.

    Helyezze át a kódot a betanítási *modell* címsora alatt a `train_model` függvénybe, és módosítsa úgy, hogy visszaadja az `reg_model` objektumot. Távolítsa el a `args` szótárt, az értékek a `args` paraméterből származnak.

1. Hozzon létre egy nevű függvényt `get_model_metrics` , amely paramétereket fogad el `reg_model` `data` , és kiértékeli a modellt, majd a betanított modell metrikáinak szótárát adja vissza.

    Helyezze át a kódot az *érvényesítő modell ellenőrzése az érvényesítési készlet* fejlécében a `get_model_metrics` függvénybe, és módosítsa az objektum visszaadásához `metrics` .

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

Még mindig `experimentation/Diabetes Ridge Regression Training.ipynb` hajtsa végre a következő lépéseket:

1. Hozzon létre egy nevű új függvényt `main` , amely nem tartalmaz paramétereket, és nem ad vissza semmit.
1. Helyezze át a kódot az "adat betöltése" fejlécbe a `main` függvénybe.
1. Adja hozzá az újonnan írt függvények meghívásait a `main` függvényhez:
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
1. Helyezze át a kódot a "modell mentése" fejléc alá a `main` függvénybe.

A `main` függvénynek a következő kódhoz hasonlóan kell kinéznie:

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

Ebben a fázisban nem lehet a függvényben nem szereplő, az első cellán belüli importálási utasításokat nem tartalmazó kód.

Adjon hozzá egy utasítást, amely meghívja a `main` függvényt.

```python
main()
```

Az újrabontás után a `experimentation/Diabetes Ridge Regression Training.ipynb` következő kódhoz hasonlóan kell kinéznie a Markdown:

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

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refaction diabetes Ridge regressziós pontozási jegyzetfüzet into functions

A alkalmazásban `experimentation/Diabetes Ridge Regression Scoring.ipynb` hajtsa végre a következő lépéseket:

1. Hozzon létre egy nevű új függvényt `init` , amely nem tartalmaz paramétereket, és nem ad vissza semmit.
1. Másolja a kódot a "betöltési modell" fejléc alá a `init` függvénybe.

A `init` függvénynek a következő kódhoz hasonlóan kell kinéznie:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

A `init` függvény létrehozása után cserélje le az összes kódot a "modell betöltése" fejléc alá egyetlen hívással a `init` következőre:

```python
init()
```

A alkalmazásban `experimentation/Diabetes Ridge Regression Scoring.ipynb` hajtsa végre a következő lépéseket:

1. Hozzon létre egy nevű új függvényt `run` , amely paramétereket fogad el, `raw_data` `request_headers` és a következő módon adja vissza az eredmény szótárát:

    ```python
    {"result": result.tolist()}
    ```

1. Másolja a kódot az "adatelőkészítés" és az "adat pontszáma" fejlécbe a `run` függvénybe.

    A `run` függvénynek a következő kódhoz hasonlóan kell kinéznie (ne felejtse el eltávolítani a változókat beállító utasításokat `raw_data` `request_headers` , amelyeket később a `run` függvény hívásakor fog használni):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

A `run` függvény létrehozása után cserélje le az "adatelőkészítés" és a "pontszám-adat" fejléc alatti kódot az alábbi kódra:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Az előző kód beállítja a változókat `raw_data` `request_header` , és meghívja a `run` függvényt a és a értékkel, `raw_data` `request_header` és kinyomtatja az előrejelzéseket.

Az újrabontás után a `experimentation/Diabetes Ridge Regression Scoring.ipynb` következő kódhoz hasonlóan kell kinéznie a Markdown:

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

A harmadik, kapcsolódó függvényeket a Python-fájlokba kell egyesíteni a kód újrafelhasználásának jobb elősegítése érdekében. Ebben a szakaszban Python-fájlokat fog létrehozni a következő jegyzetfüzetekhez:

- A diabetes Ridge regressziós betanítása notebook ( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- A diabetes Ridge regressziós pontozási notebook ( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Python-fájl létrehozása a diabetes Ridge regressziós betanítási jegyzetfüzethez

Alakítsa át a jegyzetfüzetet egy végrehajtható parancsfájlba úgy, hogy futtatja a következő utasítást egy parancssorban, amely a `nbconvert` csomagot és az elérési útját használja `experimentation/Diabetes Ridge Regression Training.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

A jegyzetfüzet konvertálása után `train.py` távolítsa el a nemkívánatos megjegyzéseket. Cserélje le a fájl végére irányuló hívást egy feltételes meghívással, `main()` például a következő kóddal:

```python
if __name__ == '__main__':
    main()
```

A `train.py` fájlnak a következő kódhoz hasonlóan kell kinéznie:

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

`train.py` Most már meghívható egy terminálról a futtatásával `python train.py` .
A függvények `train.py` más fájlokból is meghívhatók.

A `train_aml.py` `diabetes_regression/training` MLOpsPython-adattár címtárában található fájl meghívja a `train.py` Azure Machine learning kísérlet környezetében definiált függvényeket. A függvények is meghívhatók az útmutató későbbi részében tárgyalt egység-tesztelésekben.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Python-fájl létrehozása a diabetes Ridge regressziós pontozási jegyzetfüzethez

A jegyzetfüzetet egy végrehajtható parancsfájlba úgy állítsa be, hogy a következő utasítást egy olyan parancssorból futtatja, amely a `nbconvert` csomagot és az elérési utat használja `experimentation/Diabetes Ridge Regression Scoring.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

A jegyzetfüzet konvertálása után `score.py` távolítsa el a nemkívánatos megjegyzéseket. A `score.py` fájlnak a következő kódhoz hasonlóan kell kinéznie:

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

A `model` változónak globálisnak kell lennie, hogy az a parancsfájlban látható legyen. Adja hozzá a következő utasítást a függvény elejéhez `init` :

```python
global model
```

Az előző utasítás hozzáadása után a `init` függvénynek a következő kódhoz hasonlóan kell kinéznie:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Egységbeli tesztek létrehozása minden Python-fájlhoz

Negyedszer, hozzon létre egység teszteket a Python-függvényekhez. Az egység tesztek védik a kódokat a funkcionális regressziók ellen, és egyszerűbbé teszik a karbantartást. Ebben a szakaszban egység-teszteket hoz létre a függvények számára a alkalmazásban `train.py` .

`train.py` több függvényt tartalmaz, de csak egyetlen egység tesztet hozunk létre a `train_model` függvényhez az oktatóanyag Pytest keretrendszere használatával. A Pytest nem az egyetlen Python-egység tesztelési keretrendszere, de az egyik leggyakrabban használt. További információért látogasson el a [Pytest](https://pytest.org)webhelyre.

Az egységek tesztelése általában három fő műveletet tartalmaz:

- Objektum elrendezése – szükséges objektumok létrehozása és beállítása
- Objektum megvonása
- A várt érték érvényesítése

Az egység tesztelése `train_model` néhány rögzített adattal és argumentummal fog megjelenni, és `train_model` az eredményül kapott betanított modell használatával ellenőrzi, hogy a várt módon járt-e el, és hasonlítsa össze az előrejelzést a várt értékkel.

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

Most, hogy megértette, hogyan alakíthat át kísérletből termelési kódra, tekintse meg a következő hivatkozásokat a további tudnivalókhoz és a következő lépésekhez:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): CI/CD-folyamat létrehozása a saját modellek betanításához, kiértékeléséhez és üzembe helyezéséhez az Azure-folyamatok és a Azure Machine learning használatával
+ [Azure ML-kísérletek futtatásának és metrikáinak monitorozása](./how-to-track-experiments.md)
+ [Adatok ML-webszolgáltatási végpontokról való monitorozása és gyűjtése](./how-to-enable-app-insights.md)