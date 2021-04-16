---
title: Magyarázat az automatizált gépi tanulási folyamatokban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan állapíthatja meg az automatizált ML-modell funkció fontosságát, és hogyan tesz előrejelzéseket az Azure Machine Learning SDK használatakor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 3258a1d53c4aa5010758bcd93ef32c53611f4684
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576465"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Értelmezhetőség: modellmagyarázatok az automatizált gépi tanulásban (előzetes verzió)


Ebből a cikkből megtudhatja, hogyan kérhető le magyarázat az automatizált gépi tanuláshoz (automatizált gépi tanulás) Azure Machine Learning Python SDK használatával. Az automatizált gépi tanulási funkció segít megérteni a létrehozott modellek funkció fontosságát. 

Az 1.0.85 utáni összes SDK-verzió `model_explainability=True` alapértelmezés szerint be van állítva. Az SDK 1.0.85-ös és korábbi verzióiban a felhasználóknak be kell állítaniuk az objektumot a modell `model_explainability=True` `AutoMLConfig` értelmezhetősége érdekében. 


Ebben a cikkben az alábbiakkal ismerkedhet meg:

- Értelmezhetőség a betanítás során a legjobb modell vagy bármely modell esetében.
- Engedélyezze a vizualizációkat, hogy mintákat lássanak az adatokban és a magyarázatokban.
- Értelmezhetőség megvalósítása következtetés vagy pontozás során.

## <a name="prerequisites"></a>Előfeltételek

- Értelmezhetőségi funkciók. A `pip install azureml-interpret` szükséges csomag lefuttassa a következőt: .
- Automatizált gépi tanulási kísérletek építésének ismerete. Az Azure Machine Learning SDK használatával kapcsolatos további információkért tekintse meg ezt a [regressziós](tutorial-auto-train-models.md) modellre vonatkozó oktatóanyagot, vagy tekintse meg az automatizált [gépi tanulási kísérletek konfigurálási útmutatóját.](how-to-configure-auto-train.md)

## <a name="interpretability-during-training-for-the-best-model"></a>Értelmezhetőség a legjobb modell betanítás során

A magyarázatának lekérése a-ból, amely a nyers és a tervezett funkciók `best_run` magyarázatát is tartalmazza.

> [!NOTE]
> Az értelmezhetőség és a modell legjobb magyarázata nem érhető el az automatikus gépi tanulási előrejelzési kísérletekhez, amelyek a következő algoritmusokat ajánlják a legjobb modellként: 
> * TCNForecaster
> * AutoArima
> * ExponentialSmomoding (Exponenciális elazás)
> * Próféta
> * Átlag 
> * Naiv
> * Szezonális átlag 
> * Szezonális naiv

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>A tervezett funkciók fontosságai a legjobb futtatásból tölthetők le

A használatával letöltheti a megtervezett funkciók magyarázatát `ExplanationClient` a összetevőtárolóból. `best_run` 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Töltse le a nyers funkció fontosságokat a legjobb futtatásból

A használatával letöltheti a nyers funkció magyarázatát a `ExplanationClient` összetevőtárolóból. `best_run`

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Értelmezhetőség bármely modell betanításkor 

A modellek magyarázatának kiszámításakor és vizualizációjakor nem csak az AutoML-modellek meglévő modelljeinek magyarázatát kell látnia. A modellhez különböző tesztadatokkal is kaphat magyarázatot. Az ebben a szakaszban található lépések azt mutatják be, hogyan számítható ki és vizualizálható a funkciók fontossága a tesztadatok alapján.

### <a name="retrieve-any-other-automl-model-from-training"></a>Bármely más AutoML-modell lekérése a betanításból

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>A modell magyarázatának beállítása

A `automl_setup_model_explanations` használatával részletesen megtervezett és nyers magyarázatokat kap. A `fitted_model` a következő elemeket hozhatja létre:

- Betanított vagy tesztmintákból származó kiemelt adatok
- A szolgáltatásnévlisták megtervezett listája
- A címkével jelölt oszlopban kereshető osztályok besorolási forgatókönyvekben

A `automl_explainer_setup_obj` a fenti lista összes szerkezetét tartalmazza.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>A mimic Explainer inicializálása a funkció fontossága miatt

Az automatizált gépi tanulási modellek magyarázatának létrehozásához használja a `MimicWrapper` osztályt. A MimicWrapper a következő paraméterekkel inicializálhatja:

- A magyarázó telepítési objektum
- Saját munkaterület
- Helyettes modell az automatizált `fitted_model` gépi tanulási modell magyarázata

A MimicWrapper azt az objektumot is átveszi, ahová `automl_run` a tervezett magyarázatok fel lesznek töltve.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>A Mimic Explainer használata számításhoz és a funkciók fontosságának megtervezett megjelenítéséhez

A MimicWrapper metódusát az átalakított tesztmintákkal hívhatja meg, így fontossága lesz a generált `explain()` funkcióknak. A gépi tanulási funkciókba [Azure Machine Learning stúdió](https://ml.azure.com/) gépi tanulási jellemzők által létrehozott funkciók fontosságértékeivel kapcsolatos magyarázatok irányítópult-vizualizációját is megtekintheti.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```
Az automatizált gépi tanulási módszerrel betanított modellek esetében a legjobb modellt a metódus és a számítási magyarázatok helyi használatával `get_output()` kaphatja meg.  A magyarázat eredményeit a csomagból `ExplanationDashboard` `interpret-community` vizualizálhatja.

```python
best_run, fitted_model = remote_run.get_output()

from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations
automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train,
                                                             X_test=X_test, y=y_train,
                                                             task='regression')

from interpret.ext.glassbox import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel,
                         init_dataset=automl_explainer_setup_obj.X_transform, run=best_run,
                         features=automl_explainer_setup_obj.engineered_feature_names,
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
                         
pip install interpret-community[visualization]

engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, datasetX=automl_explainer_setup_obj.X_test_transform)

 

raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(raw_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, datasetX=automl_explainer_setup_obj.X_test_raw)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>A Mimic Explainer használata a nyers funkciók fontosságának számításokhoz és vizualizációhoz

A MimicWrapper metódusát az átalakított tesztmintákkal hívhatja meg, így fontossága lesz a nyers `explain()` jellemzőknek. A [Machine Learning Studióban](https://ml.azure.com/)megtekintheti a nyers jellemzők fontosságértékeivel kapcsolatos irányítópult-vizualizációt.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Értelmezhetőség dedokencia közben

Ebben a szakaszban megtudhatja, hogyan lehet egy automatizált ML-modellt az előző szakaszban található magyarázatok kiszámításához használt magyarázó segítségével működőképesíteni.

### <a name="register-the-model-and-the-scoring-explainer"></a>A modell és a pontozás magyarázó regisztrálása

A használatával létrehozhatja a pontozási magyarázót, amely kiszámítja a jellemző fontosságértékét `TreeScoringExplainer` a következtetéskor. A pontozás magyarázóját a korábban kiszámított pontozóval `feature_map` inicializálhatja. 

Mentse a pontozás magyarázóját, majd regisztrálja a modellt és a pontozó magyarázóját a modellkezelés szolgáltatásban. Futtassa az alábbi kódot:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Conda-függőségek létrehozása a szolgáltatás beállításához

Ezután hozza létre a szükséges környezeti függőségeket a tárolóban az üzembe helyezett modellhez. Vegye figyelembe, hogy az azureml-defaults with version >= 1.0.45 (azureml-defaults with version >= 1.0.45 )-nek pip-függőségként kell szerepelnie, mert tartalmazza a modell webszolgáltatásként való használatához szükséges funkciókat.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>A pontozó szkript létrehozása

Olyan szkriptet írhat, amely betölti a modellt, és előrejelzéseket és magyarázatokat állít elő egy új adatkötet alapján.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>A szolgáltatás üzembe helyezése

A szolgáltatást a Conda-fájl és az előző lépések pontozófájlja használatával helyezheti üzembe.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Következtetés tesztadatokkal

Dedukencia néhány tesztadatra az AutoML-modellből származó előrejelző értékhez, amely jelenleg csak az SDK-ban Azure Machine Learning támogatott. Az előre jelzett értékhez hozzájáruló funkciók fontosságának megtekintése. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

## <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Vizualizáció az adatokban és magyarázatok mintáinak felderítéséhez a betanítás során

A munkaterületen a következő helyen vizualizálhatja a [funkció fontosságdiagramját: Azure Machine Learning stúdió.](https://ml.azure.com) Az AutoML futtatása után válassza a **Modell részleteinek** megtekintése lehetőséget egy adott futtatás megtekintéséhez. Válassza a **Magyarázatok lapot** a vizualizációk a magyarázat irányítópulton való megjelenítéséhez.

[![Machine Learning architektúra értelmezése](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Az irányítópultok vizualizációiról és az egyes diagramok magyarázatáról az értelmezhetőséggel kapcsolatos további [tudnivalókat lásd:](how-to-machine-learning-interpretability-aml.md).

## <a name="next-steps"></a>Következő lépések

A modell magyarázatának és a funkciók fontosságának engedélyezéséről az Azure Machine Learning SDK automatizált gépi tanuláson kívül más területein is tekintse meg az értelmezhetőséggel kapcsolatos [fogalmat.](how-to-machine-learning-interpretability.md)
