---
title: Automatizált gépi tanulási kísérletek létrehozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan határozhat meg adatforrásokat, számításokat és konfigurációs beállításokat az automatizált gépi tanulási kísérletekhez.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 146697dec7e3d22e745fba2a1e9fae5d486195ef
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819311"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Automatizált gépi tanulási kísérletek konfigurálása Pythonban


Ebből az útmutatóból megtudhatja, hogyan határozhatja meg az automatizált gépi tanulási kísérletek különböző konfigurációs beállításait a [Azure Machine Learning SDK-val.](/python/api/overview/azure/ml/intro) Az automatizált gépi tanulás kiválaszt egy algoritmust és hiperparamétereket, és létrehoz egy üzembe helyezésre kész modellt. Az automatizált gépi tanulási kísérletek konfigurál többféle módon is konfigurálhatóak.

Az automatizált gépi tanulási kísérletek példáiért [lásd: Oktatóanyag:](tutorial-auto-train-models.md) Besorolási modell betanítása automatizált gépi tanulással vagy Modellek betanítása automatizált gépi tanulással a [felhőben.](how-to-auto-train-remote.md)

Az automatizált gépi tanulásban elérhető konfigurációs lehetőségek:

* Kísérlet típusának kiválasztása: Besorolás, Regresszió vagy Idősorozat-előrejelzés
* Adatforrás, formátumok és adatok beolvasása
* Válassza ki a számítási célt: helyi vagy távoli
* Automatizált gépi tanulási kísérlet beállításai
* Automatizált gépi tanulási kísérlet futtatása
* Modellmetrikák megismerés
* Modell regisztrálása és üzembe helyezése

Ha kódot nem szeretne, létrehozhatja az automatizált [gépi](how-to-use-automated-ml-for-ml-models.md)tanulási kísérleteket a következő Azure Machine Learning stúdió.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez a következőre lesz szüksége: 
* Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásához lásd: [Munkaterület Azure Machine Learning létrehozása.](how-to-manage-workspace.md)

* A Azure Machine Learning Python SDK telepítve van.
    Az SDK telepítéséhez a következőt használhatja: 
    * Hozzon létre egy számítási példányt, amely automatikusan telepíti az SDK-t, és előre konfigurálva van a gépi tanulási munkafolyamatokhoz. További [információ: Azure Machine Learning számítási példány létrehozása](how-to-create-manage-compute-instance.md) és kezelése. 

    * [Telepítse saját `automl` maga a csomagot,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)amely tartalmazza [az](/python/api/overview/azure/ml/install#default-install) SDK alapértelmezett telepítését.
    
    > [!WARNING]
    > A Python 3.8 nem kompatibilis a `automl` verzióval. 

## <a name="select-your-experiment-type"></a>A kísérlet típusának kiválasztása

A kísérlet megkezdése előtt meg kell határoznia a megoldott gépi tanulási problémát. Az automatizált gépi tanulás támogatja a `classification` , `regression` és feladattípusokat. `forecasting` További információ a [feladattípusokról:](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

A következő kód a konstruktor paraméterét használja a kísérlet `task` `AutoMLConfig` típusának `classification` megadásához.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Adatforrás és formátum

Az automatizált gépi tanulás támogatja a helyi számítógépen vagy a felhőben, például az Azure Blob Storage-ban található adatokat. Az adatok beolvashatóak egy **Pandas DataFrame-be** vagy egy **Azure Machine Learning Dataset adatkészletbe.** [További információ az adathalmazokról](how-to-create-register-datasets.md).

A gépi tanulás betanítás adataira vonatkozó követelmények:
- Az adatoknak táblázatos formában kell lennie.
- Az előrejelezni szükséges céloszlopnak az adatokban kell lennie.

**Távoli kísérletekhez a** betanítás adatainak elérhetőknek kell lenniük a távoli számításból. Az AutoML kizárólag [Azure Machine Learning TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) típusú adathalmazokat fogad el a távoli számításokon végzett munka során. 

Az Azure Machine Learning-adathalmazok a következő funkciókat biztosítják:

* Könnyedén továbbíthat adatokat statikus fájlokból vagy URL-forrásokból a munkaterületre.
* Az adatok elérhetővé tétele a betanítási szkriptek számára a felhőbeli számítási erőforrásokon való futtatáskor. Az adatok távoli számítási célhoz való csatlakoztatására a osztály használatával az adatkészletekkel való betanítás példáját itt láthatja: How to train with [datasets](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) `Dataset` (Betanítás adatkészletekkel).

Az alábbi kód egy TabularDataset adatkészletet hoz létre egy webes URL-címből. Az adatkészletek más forrásokból, például helyi fájlokból és adattárolókból való létrehozásáról a Create [a TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) (Táblázatos adatkészletek létrehozása) oldalon talál példákat.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Helyi számítási kísérletekhez** a pandas-adatkeretek használatát javasoljuk a gyorsabb feldolgozási idő érdekében.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Betanítési, ellenőrzési és tesztelési adatok

Közvetlenül a konstruktorban külön **betanítési adatokat** és ellenőrzési adatkészleteket `AutoMLConfig` is megadhat. További információ az [adatfedés és](how-to-configure-cross-validation-data-splits.md) keresztellenőrzés AutoML-kísérletekhez való konfigurálásról. 

Ha nem ad meg explicit módon egy vagy paramétert, az automatikus gépi tanulási szolgáltatás alapértelmezett technikákat alkalmaz `validation_data` `n_cross_validation` az érvényesítés érvénybe lépésének megállapításához. Ez a meghatározás a paraméterhez rendelt adathalmaz sorai számától `training_data` függ. 

|Betanítás &nbsp; adatainak &nbsp; mérete| Ellenőrzési technika |
|---|-----|
|**Nagyobb, &nbsp; &nbsp; mint 20 000 &nbsp; sor**| A betanítás/ellenőrzés adatfelosztója lesz alkalmazva. Az alapértelmezett beállítás a kezdeti betanítás adatkészletének 10%-a az ellenőrzési készlet. Ezt az ellenőrzési készletet használja a rendszer a metrikák kiszámításához.
|**&nbsp; &nbsp; 20 000 sornál &nbsp; kisebb**| A rendszer keresztellenőrzési megközelítést alkalmaz. Az összecsukott értékek alapértelmezett száma a sorok számától függ. <br> **Ha az adathalmaz kevesebb, mint 1000** sor, a rendszer 10-szerest használ. <br> **Ha a sorok 1000 és 20 000** közé esnek, akkor három összecsukás lesz használva.

Jelenleg meg kell adnia a saját **tesztadatát a** modell kiértékelése céljából. A saját tesztadatok modellértékeléshez való behozása kódpára a [Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)notebook Teszt szakaszában található. 

## <a name="compute-to-run-experiment"></a>Számítás a kísérlet futtatásához

A következő lépés a modell betanítás helyének meghatározása. Az automatizált gépi tanuláshoz kapcsolódó tanítási kísérlet az alábbi számítási lehetőségeken futhat. Ismerje meg a [helyi és távoli számítási lehetőségek előnyeit és hátrányait](concept-automated-ml.md#local-remote). 

* Helyi **gépe,** például egy helyi asztali számítógép vagy laptop – Általában akkor, ha kisebb adatkészlete van, és még a feltárási szakaszban van. A helyi számításra [ebben a jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) tekinthet meg egy példát. 
 
* Távoli **gép** a felhőben – Azure Machine Learning [Managed Compute](concept-compute-target.md#amlcompute) egy felügyelt szolgáltatás, amely lehetővé teszi a gépi tanulási modellek betanítása Azure-beli virtuális gépek fürtjére. 

    Az Azure Machine Learning Managed Compute-tal történő távoli számításra [ebben a jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) tekinthet meg egy példát. 

* Egy **Azure Databricks az** Azure-előfizetésben. További részleteket az Automatikus gépi tanulási [fürt Azure Databricks talál.](how-to-configure-databricks-automl-environment.md) Tekintse meg ezt a [GitHub-webhelyet,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) amely példákat tartalmaz a Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Kísérlet beállításainak konfigurálása

Az automatizált gépi tanulási kísérlet konfigurál többféle módon is konfigurálható. Ezek a paraméterek egy objektum példányosodása alapján vannak `AutoMLConfig` beállítva. A paraméterek teljes listájáért tekintse meg az [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) osztályt.

Néhány példa:

1. Besorolási kísérlet, amely az AUC-t használja elsődleges metrikaként, és a kísérlet időtúllépési percei 30 percre és 2 keresztellenőrzési kísérletre vannak beállítva.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. Az alábbi példa egy 60 perc után véget érő regressziós kísérlet öt ellenőrző keresztdátlálással.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Az előrejelzési feladatokhoz további beállításokra van szükség. További részletekért tekintse meg az idősorozat-előrejelzési modell automatikus [képzését.](how-to-auto-train-forecast.md) 

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

Az automatizált gépi tanulás különböző modelleket és algoritmusokat próbál meg használni az automatizálási és finomhangolási folyamat során. Felhasználóként nincs szükség az algoritmus megadására. 

A három különböző paraméterérték határozza meg az alkalmazandó algoritmusok `task` vagy modellek listáját. A vagy a paraméter használatával további iterációkat módosíthat az elérhető modellekkel, amelyekbe be- vagy `allowed_models` `blocked_models` kizárhatja őket. 

Az alábbi táblázat a támogatott modelleket foglalja össze feladattípus szerint. 

> [!NOTE]
> Ha azt tervezi, hogy az automatikusan létrehozott modelleket [egy ONNX-modellbe](concept-onnx.md)exportálja, csak a * jelű algoritmusok konvertálhatók ONNX formátumra. További információ a [modellek ONNX-gé konvertálásával kapcsolatban.](concept-automated-ml.md#use-with-onnx) <br> <br> Azt is vegye figyelembe, hogy az ONNX jelenleg csak a besorolási és regressziós feladatokat támogatja. 

Osztályozás | Regresszió | Idősoros előrejelzés
|-- |-- |--
[Logisztikai regresszió](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Színátmenet-növelés](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Színátmenet-növelés](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Színátmenet-növelés](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)
[K legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineáris SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS-lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS-lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Támogatóvektor-besorolás (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Sztochasztikus gradiens gradiens (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Sztochasztikus gradiens gradiens (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Rendkívül véletlenszerű fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Rendkívül véletlenszerű fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Rendkívül véletlenszerű fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Averaged Perceptron Classifier](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Online gradiens gradiens regressor](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Gyors lineáris regresszió](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Próféta](https://facebook.github.io/prophet/docs/quick_start.html)
[Sztochasztikus gradiens gradiens (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN (Előrejelzési TCN)
|[Lineáris SVM-osztályozó](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Elsődleges metrika
A paraméter határozza meg a modell optimalizáláshoz való betanítása `primary metric` során használt metrikát. A kiválasztható metrikákat a kiválasztott feladattípus határozza meg, és az alábbi táblázatban az egyes feladattípusokkal kapcsolatos érvényes elsődleges metrikák érhetők el.

Az automatikus gépi tanulás optimalizálása elsődleges metrika kiválasztása számos tényezőtől függ. Javasoljuk, hogy elsősorban olyan metrikát válasszon, amely a legjobban megfelelő az üzleti igényeinek. Ezután fontolja meg, hogy a metrika megfelelő-e az adatkészlet-profilhoz (adatméret, tartomány, osztályelosztás stb.).

A metrikák konkrét definícióiról az Automatizált gépi tanulási eredmények [megismerése cikkből olvashat.](how-to-understand-automated-ml.md)

|Osztályozás | Regresszió | Idősoros előrejelzés
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>Besorolási forgatókönyvek elsődleges metrikai 

A küszöbértékkel határolt metrikák, például , , és előfordulhat, hogy nem optimalizálnak olyan adatkészletek esetében, amelyek kis méretűek, nagyon nagy osztály-eltérést (osztály-kiegyensúlyozatlanság), vagy ha a várt metrikaérték nagyon közel esik `accuracy` `average_precision_score_weighted` a `norm_macro_recall` `precision_score_weighted` 0,0 vagy 1,0 értékhez. Ilyen esetekben a `AUC_weighted` jobb választás lehet az elsődleges metrikákhoz. Az automatizált gépi tanulás befejezése után az üzleti igényeinek leginkább megfelelő metrika alapján kiválaszthatja a nyerési modellt.

| Metric | Példahasználati eset(nek) |
| ------ | ------- |
| `accuracy` | Képbesorolás, hangulatelemzés, adatváltozás-előrejelzés |
| `AUC_weighted` | Csalásészlelés, képosztályozás, anomáliadetektálás/levélszemét észlelése |
| `average_precision_score_weighted` | Hangulatelemzés |
| `norm_macro_recall` | Adatváltozás-előrejelzés |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>Elsődleges metrikák regressziós forgatókönyvekhez

Az olyan metrikák, mint a és a jobban ábrázolják a modell minőségét, ha az előrejelzhető érték nagyságrendje sok `r2_score` `spearman_correlation` nagyságrendet fed le. Például fizetésbecslés, ahol sokak fizetése 20 000 és 100 000 USD között van, de a skála nagyon magas, és néhány fizetés a 100 milliós tartományban van. 

`normalized_mean_absolute_error` A és ebben az esetben a 20 000 USD-s előrejelzési hibát ugyanúgy kezeli, mint egy 30 000 USD-s fizetéssel dolgozó alkalmazott esetében, aki 20 millió dollárt `normalized_root_mean_squared_error` keres. Bár a valóságban a 20 ezer dolláros fizetéstől való eltérés csupán 20 000 USD-től való eltérést jelez előre, nagyon közeli (kicsi, 0,1%-os relatív különbség), míg a 30 000 USD-hez képest 20 000 USD-től való eltérés nincs közel (nagy, 67%-os relatív különbség). `normalized_mean_absolute_error` A `normalized_root_mean_squared_error` és a akkor hasznosak, ha az előrejelezni nem megfelelő értékek hasonló léptékben vannak.

| Metric | Példahasználati eset(nek) |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Ár-előrejelzés (ház/termék/tipp), pontszám-előrejelzés áttekintése |
| `r2_score` | Légitársaság késése, bérbecslés, hibafeloldási idő |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>Az idősor-előrejelzési forgatókönyvek elsődleges metrikai

Lásd fent a regressziós megjegyzéseket.

| Metric | Példahasználati eset(nek) |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Ár-előrejelzés (előrejelzés), leltároptimalizálás, igény-előrejelzés |
| `r2_score` | Ár-előrejelzés (előrejelzés), leltároptimalizálás, igény-előrejelzés |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>Adatok featurizálása

Minden automatizált gépi tanulási kísérlet automatikusan méretezi és  normalizálja az adatokat, hogy segítsen bizonyos algoritmusok számára, amelyek érzékenyek a különböző skálán található jellemzőkre. Ezt a skálázást és normalizálást featurizálásnak nevezzük. További [részletekért és kódpépéékért](how-to-configure-auto-features.md#) tekintse meg a Featurization in AutoML (Az AutoML-sel való megrekedés) kódot. 

A kísérletek objektumban való `AutoMLConfig` konfigurálásakor engedélyezheti vagy letilthatja a `featurization` beállítást. Az alábbi táblázatban az [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)objektumban elfogadott, a funkcióra vonatkozó beállításokat mutatjuk be. 

|A featurizálás konfigurációja | Description |
| ------------- | ------------- |
|`"featurization": 'auto'`| Azt jelzi, hogy az előfeldolgozás részeként az adatkorlátok és a [featurizálási](how-to-configure-auto-features.md#featurization) lépések automatikusan végre vannak hajtva. **Alapértelmezett beállítás:**.|
|`"featurization": 'off'`| Azt jelzi, hogy a featurizálási lépést nem szabad automatikusan tenni.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Azt jelzi, hogy testreszabott featurizálási lépést kell használni. [Megtudhatja, hogyan szabhatja testre a featurizálást.](how-to-configure-auto-features.md#customize-featurization)|

> [!NOTE]
> Az automatizált gépi tanulás jellemzősítési lépései (a funkciók normalizálása, a hiányzó adatok kezelése, a szöveg numerikus formátumra konvertálása stb.) az alapul szolgáló modell részét képezi. Ha a modellt előrejelzésekhez használja, a rendszer automatikusan alkalmazza a betanítás során alkalmazott modellezési lépéseket a bemeneti adatokra.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Együttes konfigurációja

Az együttes modellek alapértelmezés szerint engedélyezve vannak, és az AutoML-futtatás utolsó futtatási iterációiként jelennek meg. Jelenleg **a VotingEnsemble** és **a StackEnsemble** támogatott. 

A szavazás a soft-votingot valósítja meg, amely súlyozott átlagokat használ. A halmozási implementáció kétrétegű implementációt használ, ahol az első rétegben ugyanazok a modellek vannak, mint a szavazási együttes, a második rétegben pedig az első réteg modelljeinek optimális kombinációját találják meg. 

Ha ONNX-modelleket **használ,** vagy ha engedélyezve van a modell elmagyarázhatósága, a halmozás le van tiltva, és csak a szavazást használja.

Az együttes betanítás a és logikai paraméterekkel `enable_voting_ensemble` `enable_stack_ensemble` tiltható le.

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

Az együttes működés alapértelmezett viselkedésének megváltoztatásához több alapértelmezett argumentumot is meg lehet adni, mint egy `kwargs` `AutoMLConfig` objektumban.

> [!IMPORTANT]
>  Az alábbi paraméterek nem az AutoMLConfig osztály explicit paraméterei. 

* `ensemble_download_models_timeout_sec`: **A VotingEnsemble és** **a StackEnsemble** modell létrehozása során a program több illesztett modellt tölt le az előző gyermekfuttatásból. Ha a következő hibával találkozik: , akkor előfordulhat, hogy több időt kell adnia a modellek `AutoMLEnsembleException: Could not find any models for running ensembling` letöltésére. Az alapértelmezett érték 300 másodperc ezeknek a modelleknek a párhuzamos letöltéséhez, és nincs maximális időkorlát. Ha több időre van szükség, konfigurálja ezt a paramétert 300 másodpercnél nagyobb értékre. 

  > [!NOTE]
  >  Ha eléri az időkorlátot, és letölti a modelleket, az ensembling (ensembling) annyi modellt tartalmaz, amennyit letöltött. Nem szükséges, hogy az összes modellt le kell tölteni az időtúllépési időkorláton belül.

A következő paraméterek csak a **StackEnsemble-modellekre vonatkoznak:** 

* `stack_meta_learner_type`: a meta tanuló egy olyan modell, amely az egyes heterogén modellek kimenetén van betanítva. Az alapértelmezett meta learnerek besorolási feladatokhoz (vagy keresztellenőrzés engedélyezése esetén) és `LogisticRegression` `LogisticRegressionCV` `ElasticNet` regressziós/előrejelzési feladatokhoz (vagy ha a keresztellenőrzés `ElasticNetCV` engedélyezve van). Ez a paraméter a következő sztringek egyike lehet: `LogisticRegression` , , , , , vagy `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` `LinearRegression` .

* `stack_meta_learner_train_percentage`: megadja a betanítás készletének azon részét (a betanítás és az érvényesítés típusának kiválasztásakor), amely a meta learner betanítás céljából le lesz foglalva. Az alapértelmezett érték `0.2`. 

* `stack_meta_learner_kwargs`: nem kötelező paraméterek, amelyek a meta tanuló inicializálója számára átadhatóak. Ezek a paraméterek és paramétertípusok tükrözik a megfelelő modell konstruktor paramétereit és paramétertípusát, és továbbítják őket a modell konstruktorának.

Az alábbi kód egy példát mutat be az egyéni együttes viselkedés megadására egy `AutoMLConfig` objektumban.

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

Az AutoMLConfigben több lehetőséget is meghatározhat a kísérlet végére.

|Feltételek| leírás
|----|----
Nincsenek &nbsp; feltételek | Ha nem határoz meg kilépési paramétereket, a kísérlet addig folytatódik, amíg nem történik további előrehaladás az elsődleges metrikán.
Hosszú &nbsp; &nbsp; idő &nbsp; &nbsp; után| A beállításban megadhatja, hogy a kísérlet mennyi ideig fusson (percben). `experiment_timeout_minutes` <br><br> A kísérlet időkorét túllépő hibák elkerülése érdekében legalább 15 perc, vagy 60 perc szükséges, ha a sor oszlopmérete meghaladja a 10 milliót.
A &nbsp; pontszám el lett &nbsp; &nbsp; &nbsp; érve| A `experiment_exit_score` használata befejezi a kísérletet a megadott elsődleges metrikapontszám elérése után.

## <a name="run-experiment"></a>Kísérlet futtatása

Az automatizált gépi tanulási folyamathoz létre kell hoznia egy objektumot, amely egy nevestűs objektum a kísérletek `Experiment` `Workspace` futtatásához.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Küldje el a kísérletet futtatásra, és hozzon létre egy modellt. Adja át a `AutoMLConfig` metódusnak `submit` a modellt generáló metódust.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>A függőségek először egy új gépen vannak telepítve.  Akár 10 percig is eltarthat, amíg a kimenet megjelenik.
>Ha `show_output` ezt `True` a beállítást, akkor a kimenet megjelenik a konzolon.

### <a name="multiple-child-runs-on-clusters"></a>Több gyermekfutat a fürtökön

A gépi tanulási kísérlet gyermekfuttatása olyan fürtökön is elvégezhető, amelyek már egy másik kísérletet futtatnak. Az időzítés azonban attól függ, hogy a fürt hány csomóponttal rendelkezik, és hogy ezek a csomópontok elérhetők-e egy másik kísérlet futtatásához.

A fürt minden csomópontja különálló virtuális gépként (VM) működik, amely képes egyetlen betanítás futtatására; az automatizált gépi tanulási folyamathoz ez gyermekfutatot jelent. Ha az összes csomópont foglalt, az új kísérlet a várólistára kerül. Ha azonban vannak szabad csomópontok, az új kísérlet automatikusan futtatja a gépi tanulási gyermekfuttatásokat párhuzamosan az elérhető csomópontokon/virtuális gépeken.

A gyermekfuttatások kezeléséhez és azok hajtható végre való futásának kezeléséhez javasoljuk, hogy kísérletenként hozzon létre egy dedikált fürtöt, és egyezést hozzon létre a kísérlet számának és a fürtben található csomópontok `max_concurrent_iterations` számának. Így a fürt összes csomópontját egyidejűleg használhatja a kívánt számú egyidejű gyermekfuttetéssel/iterációval.

Konfigurálja  `max_concurrent_iterations` a `AutoMLConfig` objektumot. Ha nincs konfigurálva, akkor alapértelmezés szerint kísérletenként csak egy egyidejű gyermekfuttetés/-iteráció engedélyezett.  

## <a name="explore-models-and-metrics"></a>Modellek és metrikák megismerása

Az automatizált gépi tanulási lehetőségeket biztosít a betanítás eredményeinek figyeléséhez és értékeléséhez. 

* A betanítás eredményeit widgetben vagy beágyazottan is megtekintheti, ha jegyzetfüzetben van. További [részletekért lásd: Automatizált gépi](#monitor) tanulási futtatás figyelése.

* Az egyes futtatásokhoz megadott teljesítménydiagramok és metrikák definícióit és példáit lásd: [Automatizált gépi tanulási kísérlet](how-to-understand-automated-ml.md) eredményeinek értékelése. 

* A jellemzősítés összegzését és az adott modellhez hozzáadott funkciók áttekintését lásd: [Jellemzősítés átláthatósága.](how-to-configure-auto-features.md#featurization-transparency) 

A következő egyéni kódmegoldással megtekintheti a hiperparamétereket, a méretezési és normalizálási technikákat, valamint az adott automatizált gépi tanulási futtatás algoritmusát. 

Az alábbiakban az egyéni metódust () definiáljuk, amely az automatizált gépi tanulási betanítás folyamatának egyes lépései `print_model()` hiperparaméterét nyomtatja ki.
 
```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        elif hasattr(step[1], '_base_learners') and hasattr(step[1], '_meta_learner'):
            print("\nMeta Learner")
            pprint(step[1]._meta_learner)
            print()
            for estimator in step[1]._base_learners:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()   
```

Egy helyi vagy távoli futtatás esetén, amelyről az előbb lett elküldve és betanítva ugyanabban a kísérleti jegyzetfüzetben, a legjobb modellt használhatja a `get_output()` metódussal. 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

A következő kimenet azt jelzi, hogy:
 
* A StandardScalerWrapper technikával a betanítás előtt skálázhatja és normalizálhatja az adatokat.

* Az XGBoostClassifier algoritmus lett azonosítva a legjobb futtatásként, és a hiperparaméter-értékeket is megjeleníti. 

```python
StandardScalerWrapper
{'class_name': 'StandardScaler',
 'copy': True,
 'module_name': 'sklearn.preprocessing.data',
 'with_mean': False,
 'with_std': False}

XGBoostClassifier
{'base_score': 0.5,
 'booster': 'gbtree',
 'colsample_bylevel': 1,
 'colsample_bynode': 1,
 'colsample_bytree': 0.6,
 'eta': 0.4,
 'gamma': 0,
 'learning_rate': 0.1,
 'max_delta_step': 0,
 'max_depth': 8,
 'max_leaves': 0,
 'min_child_weight': 1,
 'missing': nan,
 'n_estimators': 400,
 'n_jobs': 1,
 'nthread': None,
 'objective': 'multi:softprob',
 'random_state': 0,
 'reg_alpha': 0,
 'reg_lambda': 1.6666666666666667,
 'scale_pos_weight': 1,
 'seed': None,
 'silent': None,
 'subsample': 0.8,
 'tree_method': 'auto',
 'verbose': -10,
 'verbosity': 1}
```

A munkaterület egy másik kísérletében futtatott meglévő futtatás esetén szerezze be a vizsgálni kívánt futtatás azonosítóját, és adja át a `print_model()` metódusnak. 

```python
from azureml.train.automl.run import AutoMLRun

ws = Workspace.from_config()
experiment = ws.experiments['automl-classification']
automl_run = AutoMLRun(experiment, run_id = 'AutoML_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')

automl_run
best_run, model_from_aml = automl_run.get_output()

print_model(model_from_aml)

```
> [!NOTE]
> Az automatizált gépi tanulási algoritmusok eredendő véletlenszerűséggel vannak alkalmazva, amely kis mértékben variációt okozhat egy ajánlott modell végső metrikapontszámában, például a pontosságban. Az automatizált ml olyan adatokat is végrehajt, mint a betanítás-tesztelés felosztása, a betanítás-ellenőrzés felosztása vagy a keresztellenőrzés, ha szükséges. Ha tehát többször is futtat egy kísérletet ugyanazokkal a konfigurációs beállításokkal és elsődleges metrikával, valószínűleg az egyes kísérletek végső metrikapontszámát fogja látni ezen tényezők miatt. 

## <a name="monitor-automated-machine-learning-runs"></a><a name="monitor"></a> Automatizált gépi tanulási futtatás figyelése

Automatizált gépi tanulási futtatás esetén az előző futtatás diagramjainak eléréséhez cserélje le a helyére a `<<experiment_name>>` megfelelő kísérlet nevét:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Jupyter Notebook-widget az automatizált Machine Learning](./media/how-to-configure-auto-train/azure-machine-learning-auto-ml-widget.png)

## <a name="register-and-deploy-models"></a>Modellek regisztrálása és üzembe helyezése

Regisztrálhat egy modellt, hogy később visszatérhet hozzá. 

Egy modell automatizált gépi tanulási futtatásból való regisztráláshoz használja a [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) metódust. 

```Python

best_run, fitted_model = run.get_output()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = remote_run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


Az üzembe helyezési konfigurációk létrehozásáról és a regisztrált modell webszolgáltatásban való üzembe helyezéséről további információt a modell üzembe helyezésének útmutatójában [és helyében talál.](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration)

> [!TIP]
> Regisztrált modellek esetén az egykattintásos üzembe helyezés az [Azure Machine Learning stúdió.](https://ml.azure.com) Tekintse [meg, hogyan helyezhet üzembe regisztrált modelleket a Studióból.](how-to-use-automated-ml-for-ml-models.md#deploy-your-model) 
<a name="explain"></a>

## <a name="model-interpretability"></a>Modell értelmezhetősége

A modell értelmezhetősége lehetővé teszi annak értelmezését, hogy a modellek miért készítettek előrejelzéseket, és hogy a mögöttes funkciók fontossági értékeit is megértheti. Az SDK különböző csomagokat tartalmaz a modell értelmezhetőségi funkcióinak a betanításkor és a következtetéskor való engedélyezéséhez a helyi és az üzembe helyezett modellekhez.

Tekintse meg [a kódmintákat,](how-to-machine-learning-interpretability-automl.md) amelyekből megismerheti, hogyan engedélyezheti az értelmezhetőségi funkciókat kifejezetten automatizált gépi tanulási kísérletekben.

A modell magyarázatának és a funkciók fontosságának az automatizált gépi tanuláson kívüli [](how-to-machine-learning-interpretability.md) más területein történő alkalmazásával kapcsolatos általános információkért tekintse meg az értelmezhetőséggel kapcsolatos fogalmakat.

> [!NOTE]
> A Magyarázat ügyfél jelenleg nem támogatja az ForecastTCN modellt. Ez a modell nem ad vissza magyarázat-irányítópultot, ha a legjobb modellként van visszaadva, és nem támogatja az igény szerinti magyarázatfuttatásokat.

## <a name="next-steps"></a>Következő lépések

+ További információ a [modellek üzembe helyezéséről.](how-to-deploy-and-where.md)

+ További információ a [regressziós](tutorial-auto-train-models.md) modellek automatizált gépi tanulással történő betanításról vagy az automatizált gépi tanulás távoli [erőforráson történő betanításról.](how-to-auto-train-remote.md)

+ Ismerje meg, hogyan képezhet be több modellt az AutoML segítségével a [Több modell megoldásgyorsítóban.](https://aka.ms/many-models)

+ [Automatizált gépi tanulási kísérletek hibaelhárítása.](how-to-troubleshoot-auto-ml.md) 
