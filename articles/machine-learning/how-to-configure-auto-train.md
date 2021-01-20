---
title: Automatizált gépi tanulási kísérletek létrehozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan határozhatja meg az automatizált gépi tanulási kísérletek adatforrásait, számítási és konfigurációs beállításait.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 9021d933e3808867ec784ad3c6d0f8810d608ea3
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600060"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Automatizált gépi tanulási kísérletek konfigurálása Pythonban


Ebből az útmutatóból megtudhatja, hogyan határozhatja meg az automatizált gépi tanulási kísérletek különböző konfigurációs beállításait a [Azure Machine learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)-val. Az automatizált gépi tanulás egy algoritmust és hiperparaméterek beállítása hoz létre, és létrehoz egy modellt, amely készen áll a telepítésre. Számos lehetőség közül választhat, amelyek segítségével konfigurálhatja az automatizált gépi tanulási kísérleteket.

Ha szeretné megtekinteni az automatizált gépi tanulási kísérletekre vonatkozó példákat, tekintse meg az [oktatóanyag: besorolási modell betanítása automatizált gépi tanulással](tutorial-auto-train-models.md) vagy [tanítási modelleket a Felhőbeli automatizált gépi tanulással](how-to-auto-train-remote.md).

Az automatizált gépi tanulásban elérhető konfigurációs beállítások:

* Válassza ki a kísérlet típusát: besorolás, regresszió vagy idősorozat-előrejelzés
* Adatforrás, formátumok és beolvasás
* Válassza ki a számítási célt: helyi vagy távoli
* Automatikus gépi tanulási kísérlet beállításai
* Automatizált gépi tanulási kísérlet futtatása
* Modell metrikáinak megismerése
* Modell regisztrálása és üzembe helyezése

Ha nem szeretne programkódot felvenni, akkor [Azure Machine learning Studióban is létrehozhatja az automatizált gépi tanulási kísérleteket](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez szükséges, 
* Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásával kapcsolatban tekintse meg [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md)című témakört.

* A Azure Machine Learning Python SDK telepítve van.
    Az SDK telepítéséhez használhatja a következőt is, 
    * Hozzon létre egy számítási példányt, amely automatikusan telepíti az SDK-t, és előre konfigurálva van a ML-munkafolyamatokhoz. További információkért lásd: [Azure Machine learning számítási példány létrehozása és kezelése](how-to-create-manage-compute-instance.md) . 

    * [Telepítse a `automl` csomagot saját kezűleg](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment), amely tartalmazza az SDK [alapértelmezett telepítését](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py#default-install) .

## <a name="select-your-experiment-type"></a>A kísérlet típusának kiválasztása

A kísérlet megkezdése előtt meg kell határoznia, hogy milyen típusú gépi tanulási probléma van a megoldásban. Az automatizált gépi tanulás támogatja a, a és a feladattípusait `classification` `regression` `forecasting` . További [információ a feladattípusokról](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

A következő kód a `task` konstruktor paraméterét használja a `AutoMLConfig` kísérlet típusának megadásához `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Adatforrás és formátum

Az automatizált gépi tanulás támogatja a helyi számítógépen vagy a felhőben, például az Azure Blob Storage-ban található adatokat. Az információk egy **Panda DataFrame** vagy egy **Azure Machine learning TabularDataset** is beolvashatók. [További információ az adathalmazokról](how-to-create-register-datasets.md).

A gépi tanulásban bekövetkező betanítási követelmények:
- Az adatokat táblázatos formában kell megadni.
- Az előre jelzett értéknek, a célként megadott oszlopnak szerepelnie kell az adatsorokban.

**Távoli kísérletek** esetén a betanítási adatoknak elérhetőnek kell lenniük a távoli számításból. Az AutoML kizárólag [Azure Machine Learning TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) típusú adathalmazokat fogad el a távoli számításokon végzett munka során. 

Az Azure Machine Learning-adathalmazok a következő funkciókat biztosítják:

* A statikus fájlokból vagy URL-forrásokból származó adatok egyszerűen átvihetők a munkaterületre.
* Az adatok elérhetővé tétele a betanítási szkriptek számára a felhőbeli számítási erőforrásokon való futtatáskor. Tekintse meg, [hogyan kell betanítani az adatkészletekkel](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) , hogy miként lehet az `Dataset` osztály használatával csatlakoztatni adatokat a távoli számítási célhoz.

A következő kód egy TabularDataset hoz létre egy webes URL-címről. A [TabularDatasets létrehozásával](how-to-create-register-datasets.md#create-a-tabulardataset) kapcsolatos példákat itt talál: adatkészletek létrehozása más forrásokból, például helyi fájlokból és adattárolóból.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Helyi számítási kísérletek** esetén a Panda dataframes használatát javasoljuk a gyorsabb feldolgozási idő érdekében.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Képzés, érvényesítés és tesztelés

Külön **betanítási és érvényesítési adatkészleteket** is megadhat közvetlenül a `AutoMLConfig` konstruktorban. További információ az [adatfelosztások és a AutoML-kísérletek határokon való ellenőrzésének konfigurálásáról](how-to-configure-cross-validation-data-splits.md) . 

Ha nem ad meg explicit módon egy `validation_data` vagy `n_cross_validation` paramétert, az automatikus ml az alapértelmezett technikákat alkalmazza az érvényesítés módjának meghatározásához. Ez a meghatározás a paraméterhez rendelt adatkészlet sorainak számától függ `training_data` . 

|Betanítási &nbsp; &nbsp; adatméret| Érvényesítési módszer |
|---|-----|
|**Nagyobb, &nbsp; mint &nbsp; 20 000 &nbsp; sor**| A betanítási/érvényesítési adatfelosztást alkalmazza a rendszer. Az alapértelmezett érték a kezdeti betanítási adatkészlet 10%-át adja meg az érvényesítési készletként. Ezt az ellenőrzési készletet pedig a metrikák számításához használja a rendszer.
|**Kisebb, &nbsp; mint &nbsp; 20 000 &nbsp; sor**| A rendszer több ellenőrzési módszert alkalmaz. A kidobások alapértelmezett száma a sorok számától függ. <br> **Ha az adatkészlet kevesebb mint 1 000 sor, a** rendszer 10 karámot használ. <br> **Ha a sorok 1 000 és 20 000 között vannak**, akkor három hajtogatás van használatban.

Jelenleg a modell kiértékeléséhez meg kell adnia a saját **tesztelési adatait** . Ha például a modell kiértékeléséhez saját tesztelési adatait szeretné bemutatni, tekintse meg a [Jupyter-jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) **tesztelési** szakaszát.

## <a name="compute-to-run-experiment"></a>Számítás a kísérlet futtatásához

A következő határozza meg, hogy a modell hogyan lesz betanítva. Az automatizált gépi tanuláshoz kapcsolódó tanítási kísérlet az alábbi számítási lehetőségeken futhat. Ismerje meg a [helyi és távoli számítási lehetőségek előnyeit és hátrányait](concept-automated-ml.md#local-remote). 

* **Helyi** számítógép, például helyi asztal vagy laptop – általában akkor, ha kis adatkészlettel rendelkezik, és még mindig a felderítési szakaszban van. A helyi számításra [ebben a jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) tekinthet meg egy példát. 
 
* Egy **távoli** gép a felhőben – [Azure Machine learning felügyelt számítás](concept-compute-target.md#amlcompute) egy felügyelt szolgáltatás, amely lehetővé teszi a gépi tanulási modellek betanítását az Azure-beli virtuális gépek fürtjén. 

    Az Azure Machine Learning Managed Compute-tal történő távoli számításra [ebben a jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) tekinthet meg egy példát. 

* Egy **Azure Databricks-fürt** az Azure-előfizetésében. További részletek: [Azure Databricks-fürt beállítása az automatikus ml-hez](how-to-configure-databricks-automl-environment.md). Tekintse meg ezt a [GitHub-webhelyet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) , amely példákat tartalmaz a Azure Databrickst tartalmazó notebookokra.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>A kísérlet beállításainak konfigurálása

Számos lehetőség közül választhat, amelyekkel konfigurálhatja az automatizált gépi tanulási kísérletet. Ezek a paraméterek egy objektum létrehozásával állíthatók be `AutoMLConfig` . A paraméterek teljes listájáért tekintse meg a [AutoMLConfig osztályt](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Néhány példa:

1. A besorolási kísérlet az elsődleges metrika és a kísérlet időkorlátja alapján AUC súlyozottan 30 percre van beállítva, a két kereszt-ellenőrzési hajtogatás pedig 2.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. Az alábbi példa egy olyan regressziós kísérlet, amely 60 perc után fejeződik be, és öt ellenőrző kereszttel rendelkezik.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Az előrejelzési feladatokhoz további beállítások szükségesek, további részletekért tekintse meg a [Time-Series előrejelzési modell autotrain](how-to-auto-train-forecast.md) című cikkét. 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>Támogatott modellek

Az automatizált gépi tanulás különböző modelleket és algoritmusokat próbál az automatizálási és hangolási folyamat során. Felhasználóként nem kell megadnia az algoritmust. 

A három különböző `task` paraméter értéke határozza meg az alkalmazandó algoritmusok vagy modellek listáját. Az `allowed_models` vagy a `blocked_models` paraméterrel további módosításokat is végrehajthat az elérhető modellekkel a belefoglaláshoz vagy kizáráshoz. 

A következő táblázat összefoglalja a feladattípus által támogatott modelleket. 

> [!NOTE]
> Ha úgy tervezi, hogy az automatikus ML által létrehozott modelleket egy [ONNX-modellbe](concept-onnx.md)exportálja, csak a * * értékkel jelzett algoritmusok alakíthatók át a ONNX formátumba. További információ a [modellek ONNX való átalakításáról](concept-automated-ml.md#use-with-onnx). <br> <br> Azt is vegye figyelembe, hogy a ONNX jelenleg csak a besorolási és a regressziós feladatokat támogatja. 

Besorolás | Regresszió | Idősoros előrejelzés
|-- |-- |--
[Logisztikai regresszió](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradiens fokozása](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Gradiens fokozása](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradiens fokozása](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)
[Legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[Legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[Legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineáris SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS-lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[A vektor besorolásának támogatása (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Sztochasztikus gradiens leereszkedés (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Sztochasztikus gradiens leereszkedés (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Átlagos Perceptron osztályozó](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Online színátmenet-leereszkedés Regressor](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Automatikus ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naiv Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Gyors lineáris Regressor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Próféta](https://facebook.github.io/prophet/docs/quick_start.html)
[Sztochasztikus gradiens leereszkedés (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Lineáris SVM osztályozó](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Elsődleges metrika
A `primary metric` paraméter határozza meg, hogy milyen mérőszámot kell használni az optimalizáláshoz a modell betanításakor. A kiválasztható mérőszámokat a kiválasztott feladattípus határozza meg, az alábbi táblázat pedig az egyes feladattípusok érvényes elsődleges metrikáit tartalmazza.

Ismerje meg a mérőszámok konkrét definícióit az [automatizált gépi tanulás eredményeinek megismeréséhez](how-to-understand-automated-ml.md).

|Besorolás | Regresszió | Idősoros előrejelzés
|--|--|--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>Az adatfeaturization

Minden automatizált gépi tanulási kísérlet során az adatok automatikusan méretezhetők és normalizálva vannak, hogy a különböző léptékű funkciókra *érzékeny algoritmusok* segítségével segítsenek. Ezt a skálázást és a normalizálás a featurization néven ismert. További részletekért és példákért tekintse [meg a Featurization a AutoML](how-to-configure-auto-features.md#) című témakört. 

A kísérletek az objektumban való konfigurálásakor `AutoMLConfig` engedélyezheti vagy letilthatja a beállítást `featurization` . A következő táblázat a [AutoMLConfig objektum](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)featurization elfogadott beállításait tartalmazza. 

|Featurization-konfiguráció | Leírás |
| ------------- | ------------- |
|`"featurization": 'auto'`| Azt jelzi, hogy az előfeldolgozás részeként a rendszer automatikusan végrehajtja az [guardrails és a featurization lépéseket](how-to-configure-auto-features.md#featurization) . **Alapértelmezett beállítás**.|
|`"featurization": 'off'`| Azt jelzi, hogy a featurization lépést nem kell automatikusan elvégezni.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Azt jelzi, hogy a testreszabott featurization lépést kell használni. [Megtudhatja, hogyan szabhatja testre a featurization](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Az automatizált gépi tanulás featurization lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos featurization-lépéseket automatikusan alkalmazza a rendszer a bemeneti adatokra.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Ensemble-konfiguráció

Az Ensemble-modellek alapértelmezés szerint engedélyezve vannak, és a AutoML-futtatások utolsó futtatási ismétlései jelennek meg. Jelenleg a **VotingEnsemble** és a **StackEnsemble** támogatottak. 

A szavazás a súlyozott átlagokat használó, lágy szavazást valósít meg. A halmozási implementáció egy kétrétegű implementációt használ, ahol az első réteg ugyanazokat a modelleket használja, mint a szavazó Ensemble, a második réteg modell pedig az első réteg modelljeinek optimális kombinációját használja. 

Ha ONNX-modelleket használ, **vagy** ha a modell-magyarázat engedélyezve van, a halmozás le van tiltva, és csak a szavazást használja.

Az Ensemble-képzés a `enable_voting_ensemble` és a logikai paraméterek használatával tiltható le `enable_stack_ensemble` .

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

Az alapértelmezett Ensemble-viselkedés módosításához több alapértelmezett argumentum is megadható, mint `kwargs` egy `AutoMLConfig` objektumban.

> [!IMPORTANT]
>  A következő paraméterek nem rendelkeznek explicit paraméterekkel a AutoMLConfig osztályhoz. 

* `ensemble_download_models_timeout_sec`: A **VotingEnsemble** és a **StackEnsemble** modell létrehozásakor a rendszer letölti az előző gyermekekről származó, több felszerelt modelleket. Ha ezt a hibát tapasztalja: `AutoMLEnsembleException: Could not find any models for running ensembling` , akkor előfordulhat, hogy több időt kell megadnia a modellek letöltéséhez. Az alapértelmezett érték 300 másodperc, ha párhuzamosan tölti le ezeket a modelleket, és nincs maximális időkorlát. A paramétert a 300 másodpercnél nagyobb értékre kell beállítani, ha több idő szükséges. 

  > [!NOTE]
  >  Ha elérte az időtúllépést, és a rendszer letölti a modelleket, akkor a ensembling a letöltött számú modellel együtt folytatja. Nem szükséges, hogy az összes modellt le kell tölteni az adott időtúllépésen belüli befejezéshez.

A következő paraméterek csak a **StackEnsemble** -modellekre vonatkoznak: 

* `stack_meta_learner_type`: a meta-Learner egy modell, amely az egyes heterogén modellek kimenetére van kiképezve. Az alapértelmezett meta-tanulók a `LogisticRegression` besorolási feladatokhoz (vagy ha a többszörös `LogisticRegressionCV` ellenőrzés engedélyezve van), valamint `ElasticNet` a regresszió/előrejelzési feladatokhoz (vagy `ElasticNetCV` Ha a kereszt-ellenőrzés engedélyezve van). Ez a paraméter a következő karakterláncok egyike lehet:,,,,, `LogisticRegression` `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` vagy `LinearRegression` .

* `stack_meta_learner_train_percentage`: meghatározza a betanítási készletnek azt az arányát (a betanítási típus kiválasztásakor), amelyet a meta-tanuló képzéséhez kell foglalni. Az alapértelmezett érték `0.2`. 

* `stack_meta_learner_kwargs`: választható paraméterek, amelyeket át kell adni a meta-Learner inicializálásához. Ezek a paraméterek és paraméterek típusai a megfelelő modell konstruktorában lévő paramétereket és paramétereket tükrözik, és továbbítva lesznek a modell konstruktorának.

Az alábbi kód egy példát mutat be az egyéni Ensemble viselkedésének megadására egy `AutoMLConfig` objektumban.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>Kilépési feltételek

A AutoMLConfig megadhat néhány lehetőséget a kísérlet befejezéséhez.

|Feltételek| leírás
|----|----
Nincsenek &nbsp; feltételek | Ha nem ad meg kilépési paramétereket, a kísérlet addig folytatódik, amíg az elsődleges metrika nem végez további előrehaladást.
&nbsp; &nbsp; Hosszú &nbsp; &nbsp; idő után| A `experiment_timeout_minutes` beállítások segítségével megadhatja, hogy a kísérlet hány perc elteltével fusson tovább. <br><br> Ha el szeretné kerülni az időtúllépési kísérletek elkerülését, legalább 15 percet vagy 60 percet is igénybe vehet, ha az oszlop mérete meghaladja a 10 000 000-es sorszámot.
&nbsp;Elérte A &nbsp; pontszámot &nbsp; &nbsp;| `experiment_exit_score`A használata a kísérlet befejezése után a megadott elsődleges metrikai pontszám elérésekor.

## <a name="run-experiment"></a>Kísérlet futtatása

Az automatikus ML esetében hozzon létre egy `Experiment` objektumot, amely egy megnevezett objektum, amely a `Workspace` kísérletek futtatására szolgál.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Küldje el a kísérletet futtatásra, és hozzon létre egy modellt. Adja `AutoMLConfig` meg a `submit` metódust a modell létrehozásához.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>A függőségek először egy új gépre települnek.  A kimenet megjelenítése előtt akár 10 percet is igénybe vehet.
>A `show_output` beállítás `True` hatására a rendszer kimenet jelenik meg a konzolon.

### <a name="multiple-child-runs-on-clusters"></a>Több gyermek fut a fürtökön

Az automatizált ML-kísérletek alárendelt futtatása olyan fürtön végezhető el, amely már futtat egy másik kísérletet. Az időzítés azonban attól függ, hogy a fürt hány csomópontja van, és ha ezek a csomópontok egy másik kísérlet futtatására is használhatók.

A fürt minden csomópontja önálló virtuális gépnek (VM) működik, amely egyetlen betanítási futtatást képes végrehajtani. automatizált ML esetén a gyermek futtatását jelenti. Ha az összes csomópont foglalt, az új kísérlet várólistára kerül. Ha azonban ingyenes csomópontok vannak, az új kísérlet automatikusan futtatja az automatikus ML-t a rendelkezésre álló csomópontokon/virtuális gépeken.

A gyermekek futtatásának kezeléséhez és a végrehajtásuk elvégzéséhez javasoljuk, hogy kísérletezzen egy dedikált fürtöt, és egyezzen a `max_concurrent_iterations` fürt csomópontjainak számával. Így a fürt összes csomópontját egyszerre kell használni az egyidejű alárendelt futtatások/ismétlések számával.

Konfigurálás  `max_concurrent_iterations` az `AutoMLConfig` objektumban. Ha nincs konfigurálva, a rendszer alapértelmezés szerint csak egy egyidejű alárendelt futtatási/iterációs kísérletet engedélyez.  

## <a name="explore-models-and-metrics"></a>Modellek és mérőszámok megismerése

Megtekintheti a betanítási eredményeket egy widgetben vagy beágyazottan, ha egy jegyzetfüzetben van. További részletekért tekintse meg a [modellek követése és értékelése](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) című témakört.

Lásd: az [automatizált gépi tanulási kísérlet eredményeinek kiértékelése](how-to-understand-automated-ml.md) az egyes futtatásokhoz megadott teljesítménymutatók és mérőszámok definíciói és példái alapján. 

A featurization összegzéséhez és az adott modellhez hozzáadott funkciók megismeréséhez lásd: [featurization átlátszósága](how-to-configure-auto-features.md#featurization-transparency). 

> [!NOTE]
> A (z) automatizált ML-algoritmusok olyan véletlenszerű adatmennyiséget alkalmaznak, amely kisebb eltérést okozhat az ajánlott modell végleges mérőszámok pontszámában, például a pontosságban. Az automatizált ML olyan adatokra is végrehajt műveleteket, mint például a vonat-teszt felosztása, a vonat-ellenőrzés felosztása vagy a kereszt-érvényesítés, ha szükséges. Tehát ha egy kísérletet ugyanazzal a konfigurációs beállításokkal és az elsődleges metrikával többször is futtat, akkor valószínű, hogy az egyes kísérleteknél a végső metrikák pontszáma a következő tényezők miatt fog megjelenni. 

## <a name="register-and-deploy-models"></a>Modellek regisztrálása és üzembe helyezése

A webszolgáltatások üzembe helyezéséhez szükséges modellek letöltéséről vagy regisztrálásáról további információt a [modell üzembe helyezésének módja és helye](how-to-deploy-and-where.md)című témakörben talál.

<a name="explain"></a>

## <a name="model-interpretability"></a>Modell értelmezhetősége

A modell értelmezése lehetővé teszi, hogy megtudja, miért hoztak létre a modellek előrejelzéseit, és a mögöttes funkciók fontossági értékeit. Az SDK különböző csomagokat tartalmaz, amelyek lehetővé teszik a modell-értelmező funkciók használatát a betanítási és a következtetési időben, a helyi és a központilag telepített modellekhez.

Tekintse meg az [útmutató](how-to-machine-learning-interpretability-automl.md) a kódokhoz című témakört, amely bemutatja, hogyan engedélyezheti a tolmácsolási funkciókat kifejezetten az automatizált gépi tanulási kísérleteken belül.

Általános információk arról, hogy a modell magyarázatait és funkcióinak fontosságát az SDK más területein is engedélyezheti az automatikus gépi tanuláson kívül: a [koncepcióról](how-to-machine-learning-interpretability.md) szóló cikk értelmezése.

> [!NOTE]
> A magyarázat-ügyfél jelenleg nem támogatja a ForecastTCN modellt. Ez a modell nem ad vissza magyarázat-irányítópultot, ha az a legjobb modellként lett visszaadva, és nem támogatja az igény szerinti magyarázatok futtatását.

## <a name="troubleshooting"></a>Hibaelhárítás

* A **`AutoML` függőségek újabb verzióra való frissítése megszakítja a kompatibilitást**: az SDK verziójának 1.13.0 a modelleket a rendszer nem tölti be a régebbi SDK-k között, mert a korábbi csomagokban rögzített korábbi verziók és a most rögzített újabb verziók nem kompatibilisek. A következő hibaüzenetet fogja látni:
  * A modul nem található: ex. `No module named 'sklearn.decomposition._truncated_svd` ,
  * Importálási hibák: pl. `ImportError: cannot import name 'RollingOriginValidator'` ,
  * Attribútum hibái: pl. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  A probléma megkerüléséhez végezze el az alábbi két lépés valamelyikét az `AutoML` SDK-képzés verziójától függően:
    * Ha az `AutoML` SDK-képzés verziója meghaladja a 1.13.0-t, akkor a és a is szükséges `pandas == 0.25.1` `sckit-learn==0.22.1` . Ha a verzió nem egyezik, frissítse a scikit-Learn és/vagy a pandák verziót a megfelelő verzióra az alább látható módon:
      
      ```bash
         pip install --upgrade pandas==0.25.1
         pip install --upgrade scikit-learn==0.22.1
      ```
      
    * Ha az `AutoML` SDK-képzés verziója kisebb vagy egyenlő, mint a 1.12.0, akkor a és a értékre van szüksége `pandas == 0.23.4` `sckit-learn==0.20.3` . Ha a verzió nem egyezik, a scikit-Learn és/vagy a pandák verzióját az alább látható módon kell kijavítani:
  
      ```bash
        pip install --upgrade pandas==0.23.4
        pip install --upgrade scikit-learn==0.20.3
      ```

* **Sikertelen üzembe helyezés**: a (z) <= 1.18.0 verziók esetében a központi telepítéshez létrehozott alaprendszerkép a következő hiba miatt meghiúsulhat: "ImportError: a név nem importálható `cached_property` `werkzeug` ". 

  A probléma megoldásához a következő lépések használhatók:
  1. A modell csomag letöltése
  2. A csomag kicsomagolása
  3. Üzembe helyezés a kibontott eszközök használatával

* Az **előrejelzési R2 pontszáma mindig nulla**: Ez a probléma akkor fordul elő, ha a megadott betanítási adatsorozatban az utolsó `n_cv_splits`  +  `forecasting_horizon` adatpontok esetében azonos érték szerepel. Ha ez a minta várható az idősorozatban, átválthatja az elsődleges metrikát normalizált, legfelső szintű négyzetes hibára.
 
* **TensorFlow**: az SDK-ból származó 1.5.0-es verziótól kezdve az automatikus gépi tanulás nem telepíti a TensorFlow-modelleket alapértelmezés szerint. A TensorFlow telepítéséhez és az automatikus ML-kísérletekhez való használatához telepítse a TensorFlow = = 1.12.0-et a CondaDependecies-n keresztül. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```

* **Kísérletezési diagramok**: a bináris besorolású diagramok (precíziós visszahívás, ROC, nyereség görbe stb.) az automatizált ml-kísérletek ismétlései között nem megfelelően jelennek meg a felhasználói felületen a 4/12 óta. A diagram ábrázolása jelenleg inverz eredményeket mutat, ahol a jobb teljesítményű modellek alacsonyabb eredményekkel jelennek meg. Egy megoldás a vizsgálat alatt áll.

* **Automatikus gépi tanulási Databricks megszakítása**: Ha Azure Databrickson automatikus gépi tanulási funkciót használ, a Futtatás megszakításához és az új kísérlet futtatásához indítsa újra a Azure Databricks-fürtöt.

* **Databricks >10 iteráció az automatizált gépi tanuláshoz**: az automatikus gépi tanulási beállításokban, ha több mint 10 iteráció van, akkor állítsa `show_output` be `False` a parancsot a futtatáskor.

* **A Azure Machine learning SDK és az automatizált gépi tanulás Databricks widgetje**: az Azure Machine learning SDK widget nem támogatott a Databricks-jegyzetfüzetekben, mert a jegyzetfüzetek nem tudják elemezni a HTML widgeteket. A widgetet a portálon tekintheti meg a Azure Databricks notebook-cellában található Python-kód használatával:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup sikertelen**: 
    * Windows rendszeren futtasson automl_setup egy Anaconda-parancssorból. Ezzel a hivatkozással [telepítheti a Miniconda](https://docs.conda.io/en/latest/miniconda.html).
    * A parancs futtatásával győződjön meg arról, hogy a 64 bites Conda telepítve van, és nem 32 bites `conda info` . A legyen `platform` `win-64` Windows vagy Mac rendszerű `osx-64` .
    * Győződjön meg arról, hogy a Conda 4.4.10 vagy újabb verziója telepítve van. A verziót a paranccsal lehet megtekinteni `conda -V` . Ha telepítve van egy korábbi verziója, a paranccsal frissítheti a parancsot: `conda update conda` .
    * Linux `gcc: error trying to exec 'cc1plus'`
      *  Ha a `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` hiba előfordul, telepítse a Build Essentials parancsot a paranccsal `sudo apt-get install build-essential` .
      * Új Conda-környezet létrehozásához adjon meg egy új nevet az első paraméterként automl_setup. Megtekintheti a meglévő Conda-környezeteket `conda env list` , és eltávolíthatja őket a használatával `conda env remove -n <environmentname>` .
      
* a **automl_setup_linux. sh sikertelen**: ha a automl_setup_linus. sh sikertelen Ubuntu Linux a következő hibával:`unable to execute 'gcc': No such file or directory`-
  1. Győződjön meg arról, hogy a 53-es és a 80-es kimenő portok engedélyezve vannak. Egy Azure-beli virtuális gépen ezt elvégezheti a Azure Portal a virtuális gép kiválasztásával, majd a hálózatkezelés elemre kattintva.
  2. Futtassa a parancsot: `sudo apt-get update`
  3. Futtassa a parancsot: `sudo apt-get install build-essential --fix-missing`
  4. Futtatás `automl_setup_linux.sh` újra

* a **Configuration. ipynb sikertelen**:
  * A helyi Conda esetében először győződjön meg arról, hogy a automl_setup sikeresen futott.
  * Győződjön meg arról, hogy a subscription_id helyes. Keresse meg a subscription_id a Azure Portal a minden szolgáltatás, majd az előfizetések lehetőség kiválasztásával. A (z) "<" és a ">" karakterek nem szerepelhetnek a subscription_id értékben. Például `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` érvényes a formátuma.
  * Győződjön meg arról, hogy a közreműködő vagy a tulajdonos hozzáférése van az előfizetéshez.
  * Győződjön meg arról, hogy a régió a támogatott régiók egyike:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` , `southcentralus` .
  * A Azure Portal használatával győződjön meg arról, hogy a régióhoz fér hozzá.
  
* **`import AutoMLConfig` sikertelen**: módosult a csomag módosítása a gépi tanulási 1.0.76, amely megköveteli az előző verzió eltávolítását az új verzióra való frissítés előtt. Ha a `ImportError: cannot import name AutoMLConfig` rendszer a v 1.0.76 v 1.0.76 vagy újabb verzióra történő frissítés után észlelt, hárítsa el a hibát a következő parancs futtatásával: `pip uninstall azureml-train automl` és `pip install azureml-train-auotml` . A automl_setup. cmd parancsfájl ezt automatikusan elvégzi. 

* **Workspace.from_config sikertelen**: Ha a ws = Workspace.from_config () hívása sikertelen –
  1. Győződjön meg arról, hogy a Configuration. ipynb jegyzetfüzet sikeresen futott.
  2. Ha a jegyzetfüzet olyan mappából fut, amely nem a `configuration.ipynb` futtatott mappában található, másolja a aml_config mappát, és a fájl config.jsaz új mappába. Workspace.from_config beolvassa az config.jsa jegyzetfüzet mappájába vagy a szülőmappa mappájába.
  3. Ha új előfizetést, erőforráscsoportot, munkaterületet vagy régiót használ, győződjön meg arról, hogy a `configuration.ipynb` jegyzetfüzetet újra futtatja. config.jsközvetlen módosítása csak akkor működik, ha a munkaterület már létezik a megadott erőforrás-csoportban a megadott előfizetésben.
  4. Ha módosítani szeretné a régiót, módosítsa a munkaterületet, az erőforráscsoportot vagy az előfizetést. `Workspace.create` a nem hoz létre vagy frissít egy munkaterületet, ha már létezik, még akkor is, ha a megadott régió eltér.
  
* A **minta jegyzetfüzet sikertelen**: Ha egy minta jegyzetfüzet hibát jelez, a tulajdonság, a metódus vagy a könyvtár nem létezik:
  * Győződjön meg arról, hogy a megfelelő kernel van kiválasztva a Jupyter Notebook. A kernel a notebook oldal jobb felső sarkában jelenik meg. Az alapértelmezett érték azure_automl. A rendszer a rendszermagot a jegyzetfüzet részeként menti. Ha tehát új Conda-környezetre vált, ki kell választania az új kernelt a jegyzetfüzetben.
      * Azure Notebooks esetén a Python 3,6-es értéknek kell lennie. 
      * Helyi Conda környezetekben a automl_setupban megadott Conda-környezeti nevet kell megadni.
  * Győződjön meg arról, hogy a jegyzetfüzet a használt SDK-verzióhoz készült. Az SDK verziójának ellenőrzéséhez hajtsa végre `azureml.core.VERSION` Jupyter notebook cellát. A minta jegyzetfüzetek előző verzióját a GitHubról töltheti le, ha a `Branch` gombra kattint, majd kiválasztja a `Tags` fület, majd kiválasztja a verziót.

* **`import numpy` nem sikerül a Windows** rendszerben: egyes Windows-környezetekben a NumPy a legújabb Python-verzió 3.6.8 való betöltésekor hiba jelenik meg. Ha ezt a problémát látja, próbálkozzon a Python-verzió 3.6.7.

* **`import numpy` sikertelen**: az automatikus ml Conda-környezetben keresse meg a TensorFlow verzióját. A támogatott verziók a következők: < 1,13. Távolítsa el a TensorFlow a környezetből, ha a verzió >= 1,13. A TensorFlow és az Eltávolítás verzióját a következőképpen tekintheti meg:
  1. Indítsa el a parancssort, aktiválja a Conda-környezetet, amelyben az automatikus ml-csomagok telepítve vannak.
  2. Adja meg `pip freeze` és keresse meg `tensorflow` , ha található, a felsorolt verziónak < 1,13
  3. Ha a felsorolt verzió nem támogatott verziójú, a `pip uninstall tensorflow` parancs-rendszerhéjban írja be az y értéket a megerősítéshez.
  
 * **A Futtatás sikertelen `jwt.exceptions.DecodeError` a** következővel: pontos hibaüzenet: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

    A (<= SDK-1.17.0 tartozó verziók esetében a telepítés a PyJWT nem támogatott verzióját eredményezheti. A PyJWT verziójának ellenőrzését az automatikus ml Conda-környezetben. A támogatott verziók < 2.0.0. A PyJWT verzióját a következőképpen lehet megtekinteni:
    1. Indítsa el a parancssort, aktiválja a Conda-környezetet, amelyben az automatikus ml-csomagok telepítve vannak.
    2. Adja meg `pip freeze` és keresse meg `PyJWT` , ha található, a felsorolt verziónak < 2.0.0 kell lennie

    Ha a felsorolt verzió nem támogatott verzió:
    1. Érdemes lehet a AutoML SDK legújabb verziójára frissíteni: `pip install -U azureml-sdk[automl]` .
    2. Ha ez nem életképes, távolítsa el a PyJWT a környezetből, és telepítse a megfelelő verziót a következőképpen:
        - `pip uninstall PyJWT` a parancs-rendszerhéjban, és adja meg `y` a megerősítést.
        - Telepítés a használatával `pip install 'PyJWT<2.0.0'` .

## <a name="next-steps"></a>További lépések

+ További információ a [modellek telepítéséről és helyéről](how-to-deploy-and-where.md).

+ További információ a [regressziós modell automatikus gépi tanulással való betanításáról](tutorial-auto-train-models.md) , illetve a [távoli erőforrásokon való automatikus gépi tanulás használatával történő](how-to-auto-train-remote.md)betanításról.

+ Ismerje meg, hogyan taníthat több modellt a AutoML [számos modell megoldás-gyorsító](https://aka.ms/many-models)használatával.
