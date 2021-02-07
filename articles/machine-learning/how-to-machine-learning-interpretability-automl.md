---
title: Elmagyarázható automatizált ML-ben (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állapíthatja meg, hogy az automatikus ML-modell hogyan határozza meg a funkció fontosságát, és előrejelzéseket készít az Azure Machine Learning SDK használatakor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: fe0b2abb7fa2ca986a896a75e5f6d4c238d70109
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807258"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Értelmezhetőség: modellmagyarázatok az automatizált gépi tanulásban (előzetes verzió)



Ebből a cikkből megtudhatja, hogyan kérheti le a Azure Machine Learning automatikus gépi tanulás (AutoML) magyarázatát. A AutoML segítségével megismerheti a generált modellek funkcióinak fontosságát. 

A 1.0.85 utáni összes SDK-verzió alapértelmezés szerint van beállítva `model_explainability=True` . Az SDK-verzió 1.0.85 és korábbi verzióiban a felhasználóknak be kell állítaniuk `model_explainability=True` az `AutoMLConfig` objektumban a modell-értelmező használata érdekében. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

- A legjobb modell vagy bármely modell tanítása során végezzen tolmácsolást.
- A vizualizációk lehetővé teszik az adatmintázatok és a magyarázatok megjelenítését.
- Értelmezhető megvalósítás a következtetés vagy pontozás során.

## <a name="prerequisites"></a>Előfeltételek

- Értelmező funkciók. Futtassa `pip install azureml-interpret` a parancsot a szükséges csomag beszerzéséhez.
- A AutoML-kísérletek létrehozásának ismerete. További információ az Azure Machine Learning SDK használatáról: a [regressziós modell oktatóanyagának](tutorial-auto-train-models.md) elvégzése, illetve a [AutoML-kísérletek konfigurálása](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>A legjobb modell képzésének értelmezése

Kérje le a magyarázatot a alkalmazásból `best_run` , amely a nyers és a megtervezett funkciók magyarázatait is tartalmazza.

> [!NOTE]
> A tolmácsolás, a legjobb modell magyarázata nem érhető el az automatikus ML-előrejelzési kísérletekhez, amelyek a következő algoritmusokat ajánlják a legjobb modellként: 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Próféta
> * Átlag 
> * Naiv
> * Szezonális átlag 
> * Szezonális naiv

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Az összetevő-tárolóban megjelenő Kiemelt funkciók fontosságának letöltése

A ( `ExplanationClient` ) használatával letöltheti a megtervezett funkció magyarázatait az összetevő-tárolóból `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Értelmezés a modell betanítása során 

Ha kiszámítja a modellre vonatkozó magyarázatokat, és megjeleníti őket, nem korlátozódik a AutoML-modell meglévő modelljére. A modellre vonatkozó magyarázatot különböző tesztelési adataival is megtalálhatja. Az ebben a szakaszban ismertetett lépések bemutatják, hogyan számíthatja ki és jelenítheti meg a tesztelési adatokon alapuló, mérnöki funkciók fontosságát.

### <a name="retrieve-any-other-automl-model-from-training"></a>Bármely más AutoML-modell beolvasása a képzésből

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>A modell magyarázatának beállítása

`automl_setup_model_explanations`A segítségével megtalálhatja a megtervezett magyarázatokat. A a következő elemeket hozhatja ki `fitted_model` :

- Kiemelt adatok a betanított vagy tesztelési mintákból
- Megtervezett funkciók neve listázza
- Megkereshető osztályok a címkével ellátott oszlopban besorolási helyzetekben

A `automl_explainer_setup_obj` tartalmazza a fenti lista összes szerkezetét.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>A funkciók fontosságának inicializálása az utánozó-magyarázattal

A AutoML-modellek magyarázatának létrehozásához használja a `MimicWrapper` osztályt. A MimicWrapper a következő paraméterekkel inicializálhatja:

- A magyarázó telepítő objektum
- Munkaterülete
- Helyettesítő modell a AutoML-modell magyarázatához `fitted_model`

A MimicWrapper arra az objektumra is kerül, `automl_run` ahol a megtervezett magyarázatok fel lesznek töltve.

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

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>A MimicExplainer használata a mérnöki funkciók fontosságának meghatározásához és megjelenítéséhez

Az `explain()` átalakított tesztelési mintákkal hívhatja meg a metódust a MimicWrapper-ben, hogy a funkció fontosságot kapjon a generált mérnöki funkciók számára. A segítségével `ExplanationDashboard` megtekintheti a AutoML featurizers által generált mérnöki funkciók fontossági értékeinek irányítópult-vizualizációját is.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Értelmezhető a következtetés során

Ebből a szakaszból megtudhatja, hogyan működővé tenni egy AutoML-modellt az előző szakaszban ismertetett magyarázatok kiszámításához használt magyarázattal.

### <a name="register-the-model-and-the-scoring-explainer"></a>A modell és a pontozási magyarázat regisztrálása

A `TreeScoringExplainer` segítségével hozza létre a pontozási magyarázatot, amely kiszámítja a megtervezett funkció fontossági értékeit a következtetések időpontjában. A pontozási magyarázatot a `feature_map` korábban kiszámított értékkel inicializálhatja. 

Mentse a pontozási magyarázatot, majd regisztrálja a modellt és a pontozási magyarázatot a modellkezelés szolgáltatással. Futtassa az alábbi kódot:

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>A szolgáltatás beállításához szükséges Conda-függőségek létrehozása

Ezután hozza létre a szükséges környezeti függőségeket a tárolóban az üzembe helyezett modellhez. Vegye figyelembe, hogy a (z) >= 1.0.45 verzióval rendelkező alapértelmezett azureml pip-függőségként kell szerepelnie, mert tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

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

### <a name="deploy-the-service"></a>A szolgáltatás üzembe helyezése

Telepítse a szolgáltatást a Conda fájl és az előző lépésekből származó pontozási fájl használatával.

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

### <a name="inference-with-test-data"></a>Következtetés tesztelési adattal

Bizonyos tesztelési adatok alapján megtekintheti a AutoML modell előre jelzett értékét, amely jelenleg csak Azure Machine Learning SDK-ban támogatott. Tekintse meg az előre jelzett értékhez hozzájáruló funkció fontosságát. 

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
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Vizualizáció az adatmodellek és a magyarázatok észleléséhez a képzés ideje alatt

A funkció fontossága diagramot a munkaterületen, [Azure Machine learning Studióban](https://ml.azure.com)jelenítheti meg. Miután befejeződött a AutoML futtatása, válassza a **modell részleteinek megtekintése** lehetőséget egy adott Futtatás megtekintéséhez. A **magyarázatok lapon** megtekintheti a magyarázat megjelenítése irányítópultot.

[![Machine Learning-értelmező architektúra](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

A magyarázó irányítópult-vizualizációkkal és az egyes mintaterületekkel kapcsolatos további információkért tekintse meg a következő [témakört: útmutató a tolmácsoláshoz](how-to-machine-learning-interpretability-aml.md).

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan engedélyezheti a modell magyarázatait és funkcióit a Azure Machine Learning SDK azon területein, amelyek [nem az automatizált](how-to-machine-learning-interpretability.md)gépi tanulást ismertetik.
