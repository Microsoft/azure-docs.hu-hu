---
title: Featurization automatizált gépi tanulással
titleSuffix: Azure Machine Learning
description: Megtudhatja Azure Machine Learning featurization beállításait, és hogyan szabhatja testre ezeket a funkciókat az automatizált ML-kísérleteknél.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to,automl,contperf-fy21q2
ms.date: 12/18/2020
ms.openlocfilehash: c90ef9fe49a87c18c7f4f55175bafaebfd31d722
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610301"
---
# <a name="data-featurization-in-automated-machine-learning"></a>Az automatizált gépi tanulásban tárolt featurization

Ismerkedjen meg a Azure Machine Learning featurization beállításaival, és hogyan szabhatja testre ezeket a funkciókat az [automatizált gépi tanulási kísérletekhez](concept-automated-ml.md).

## <a name="feature-engineering-and-featurization"></a>Szolgáltatások mérnöki és featurization

A betanítási információ sorokból és oszlopokból áll. Minden sor egy megfigyelési vagy rekord, az egyes sorok oszlopai pedig az egyes rekordokat leíró funkciók. A prediktív modellek létrehozásához jellemzően az adatmintákat legjobban jellemző funkciók vannak kiválasztva.

Bár a nyers adatmezők közül sok használható közvetlenül a modellek betanításához, gyakran szükséges további (megtervezett) funkciókat létrehozni, amelyek olyan információkat biztosítanak, amelyek jobban megkülönböztetik a mintákat az adatokban. Ennek a folyamatnak a neve a **szolgáltatás mérnöki** környezete, ahol az adat tartományon alapuló ismeretét kihasználva olyan funkciók hozhatók létre, amelyek segítségével a gépi tanulási algoritmusok segítenek a jobb megismerésben. 

Azure Machine Learning az adatméretezési és-normalizálás technikákat a funkciók mérnöki működésének megkönnyítésére alkalmazza a rendszer. Együttesen ezek a technikák és a szolgáltatások mérnöki funkciói az automatikus ML-kísérletek **featurization** vannak elnevezve.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már tudja, hogyan konfigurálhat egy automatizált ML-kísérletet. A konfigurálással kapcsolatos további információkért tekintse meg a következő cikkeket:

- Első [lépésként: az automatikus ml-kísérletek konfigurálása a Pythonhoz készült Azure Machine learning SDK használatával](how-to-configure-auto-train.md).
- Alacsony kódú vagy nem kódolású felhasználói élmény: [automatizált gépi tanulási modellek létrehozása, áttekintése és üzembe helyezése a Azure Machine learning Studio használatával](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Featurization konfigurálása

Az automatikus [skálázási és normalizáló technikákat](#featurization) minden automatizált gépi tanulási kísérlet során alapértelmezés szerint alkalmazza a rendszer az adataira. Ezek a technikák olyan featurization-típusok, amelyek *bizonyos* , különböző léptékű funkciókra érzékeny algoritmusokat nyújtanak. Több featurization is engedélyezheti, például a *hiányzó értékeket imputálási*, *kódolást* és *átalakításokat*.

> [!NOTE]
> Az automatizált gépi tanulási featurization (például a funkciók normalizálása, a hiányzó adatokat kezelő vagy a numerikus szöveg konvertálása) lépései az alapul szolgáló modell részévé válnak. Ha az előrejelzési modellt használja, a betanítás során alkalmazott featurization-lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

A Python SDK-val konfigurált kísérletek esetében engedélyezheti vagy letilthatja a featurization-beállítást, és további megadhatja a kísérlethez használni kívánt featurization-lépéseket. Ha a Azure Machine Learning Studiot használja, tekintse [meg a featurization engedélyezésének lépéseit](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

A következő táblázat a `featurization` [AutoMLConfig osztály](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)elfogadott beállításait mutatja be:

|Featurization-konfiguráció | Description|
------------- | ------------- |
|`"featurization": 'auto'`| Azt határozza meg, hogy az előfeldolgozás részeként a rendszer automatikusan végrehajtja az [guardrails](#data-guardrails) és a [featurization lépéseket](#featurization) . Ez az alapértelmezett beállítás.|
|`"featurization": 'off'`| Meghatározza, hogy a featurization lépések ne legyenek automatikusan elvégezve.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Megadja, hogy a rendszer testreszabott featurization-lépéseket használ. [Megtudhatja, hogyan szabhatja testre a featurization](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatikus featurization

Az alábbi táblázat összefoglalja az adataira automatikusan alkalmazott technikákat. Ezeket a technikákat az SDK-val vagy a Studióval konfigurált kísérletekre alkalmazza a rendszer. Ha le szeretné tiltani ezt a viselkedést, állítsa be `"featurization": 'off'` az `AutoMLConfig` objektumban.

> [!NOTE]
> Ha úgy tervezi, hogy a AutoML által létrehozott modelleket egy [ONNX-modellbe](concept-onnx.md)exportálja, csak a csillaggal ("*") jelölt featurization-beállítások támogatottak a ONNX formátumban. További információ a [modellek ONNX való átalakításáról](how-to-use-automl-onnx-model-dotnet.md).

|Featurization &nbsp; lépések| Description |
| ------------- | ------------- |
|**A nagyfokú és a variancia nélküli funkciók eldobása*** |Ezeket a funkciókat a betanítási és az ellenőrzési készletekből dobja el. Az összes hiányzó értékkel rendelkező szolgáltatásokra vonatkozik, amelyek az összes sorban azonos értékkel rendelkeznek, vagy magas fokú (például kivonatok, azonosítók vagy GUID azonosítók).|
|**Hiányzó értékek imputált értéke*** |Numerikus funkciók esetében az érték az oszlopban szereplő értékek átlagát tartalmazza.<br/><br/>A kategorikus funkciók esetében a leggyakoribb értékkel kell eltulajdonítani a bevonást.|
|**További funkciók**– _ |A DateTime funkciók esetében: év, hónap, nap, hét napja, év napja, negyedév, év hete, óra, perc, másodperc.<br><br> _For előrejelzési feladatok, * ezek a további DateTime-funkciók a következők: ISO év, félév, naptári hónap karakterláncként, hét, hét napja karakterláncként, a negyedév napja, az év napja, AM/PM (0, ha az óra délig van (12 PM), 1 egyéb), AM/PM as sztring, óra nap (12 – HR)<br/><br/>A szöveges funkciókhoz: unigrams, bigrams és Trigrams alapuló kifejezés gyakorisága. További információ arról, [hogy ez hogyan történik a bertban.](#bert-integration)|
|**Átalakítás és kódolás***|A több egyedi értékkel rendelkező numerikus funkciók átalakítása kategorikus funkciókba.<br/><br/>A kis-és nagymértékű kategorikus funkciók esetében egy gyors kódolást használunk. A rendszer egy-egy gyors kivonatoló kódolást használ a magas fokú, kategorikus funkciókhoz.|
|**Word-beágyazások**|A szöveges Képtulajdonság egy előre betanított modell használatával alakítja át a szöveges tokenek vektorait a mondatokra. Az egyes Word-dokumentumok beágyazási vektora a többivel együtt a dokumentum-szolgáltatás vektorának előállítására szolgál.|
|**Fürt távolsága**|A k – a csoportosítási modellt jelenti az összes numerikus oszlopban. *K* új szolgáltatásokat (egy új numerikus funkciót) hoz létre, amely az egyes minták távolságát tartalmazza az egyes fürtök középpontját.|

## <a name="data-guardrails"></a>Az adatguardrails

Az *adatok guardrails* segítségével azonosíthatja az adataival kapcsolatos lehetséges problémákat (például a hiányzó értékeket vagy az [osztály egyensúlyhiányát](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). Emellett segítenek a jobb eredmények javítása érdekében.

A rendszer alkalmazza az guardrails:

- **SDK-kísérletek esetén**: Ha a paraméterek `"featurization": 'auto'` vagy az `validation=auto` `AutoMLConfig` objektumban vannak megadva.
- **Studio-kísérletek** esetén: Ha engedélyezve van az automatikus featurization.

A kísérlethez tartozó guardrails áttekintéséhez tekintse át a következőt:

- Azáltal, hogy `show_output=True` az SDK használatával elküld egy kísérletet.

- A Studióban, az automatikus ML-Futtatás **guardrails** lapján.

### <a name="data-guardrail-states"></a>Adatguardrail állapotok

Az adatguardrails három állapot egyikét jeleníti meg:

|Állam| Leírás |
|----|---- |
|**Telt**| A rendszer nem észlelt adatproblémákat, és nincs szükség beavatkozásra. |
|**Kész**| A módosítások érvénybe lépnek az adatain. Javasoljuk, hogy tekintse át a AutoML által végzett javítási műveleteket, hogy a módosítások megfeleljenek a várt eredményeknek. |
|**Riasztást kap**| A rendszer adatproblémát észlelt, de nem tudta kijavítani. Javasoljuk, hogy módosítsa és javítsa ki a problémát.|

### <a name="supported-data-guardrails"></a>Támogatott adatguardrails

A következő táblázat ismerteti a jelenleg támogatott guardrails, valamint a kísérlet elküldésekor esetlegesen megjelenő társított állapotokat:

Guardrail|Állapot|Trigger feltétele &nbsp; &nbsp;
---|---|---
**Hiányzó szolgáltatási értékek imputálási** |Telt <br><br><br> Kész| A betanítási adatok nem észleltek hiányzó szolgáltatási értékeket. További információ a [hiányzó értékű imputálási.](./how-to-use-automated-ml-for-ml-models.md#customize-featurization) <br><br> A rendszer hiányzó szolgáltatási értékeket észlelt a betanítási adatokban, és imputáltak voltak.
**Magas fokú szolgáltatások kezelését** |Telt <br><br><br> Kész| A rendszer elemezte a bemeneteket, és nem észlelt magas szintű funkciókat. <br><br> A rendszer a Kiemelt funkciókat észlelte a bemenetekben, és kezelte azokat.
**Ellenőrzés felosztásának ellenőrzése** |Kész| Az érvényesítési konfiguráció értékre lett állítva `'auto'` , a betanítási adatként pedig *kevesebb, mint 20 000 sor* szerepel. <br> A betanított modell minden egyes iterációját kereszt-ellenőrzéssel érvényesíti a program. További információ az [érvényesítési](./how-to-configure-auto-train.md#training-validation-and-test-data)információkról. <br><br> Az érvényesítési konfiguráció értékre lett állítva `'auto'` , a betanítási adatként pedig *több mint 20 000 sor* szerepel. <br> A bemeneti adatok egy betanítási adatkészletbe és egy ellenőrzési adatkészletbe vannak osztva a modell érvényesítéséhez.
**Osztály kiegyensúlyozásának észlelése** |Telt <br><br><br><br>Riasztást kap <br><br><br>Kész | A rendszer elemezte a bemeneteket, és az összes osztály kiegyensúlyozottan szerepel a betanítási adatokban. Az adatkészletek akkor tekinthetők kiegyensúlyozottnak, ha az egyes osztályok jó ábrázolással rendelkeznek az adatkészletben, a minták számával és arányával mérve. <br><br> A rendszer kiegyensúlyozatlan osztályokat észlelt a bemenetekben. A modell torzításának javításához javítsa ki a kiegyenlítési problémát. További információ a [kiegyensúlyozatlan adatvédelemről](./concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data).<br><br> A rendszer kiegyensúlyozatlan osztályokat észlelt a bemenetekben, és a megtakarítási logikát úgy döntött, hogy az egyensúlyt alkalmazza.
**Memória-problémák észlelése** |Telt <br><br><br><br> Kész |<br> A kiválasztott értékeket (horizont, lag, gördülő ablak) elemezték, és a rendszer nem észlelt memóriabeli problémákat. További információ az idősorozat- [előrejelzési konfigurációkról](./how-to-auto-train-forecast.md#configuration-settings). <br><br><br>A kiválasztott értékeket (horizont, lag, gördülő ablak) elemezte a rendszer, és a kísérlet miatt kifogyhat a memória. A késés vagy a működés közbeni ablak konfigurációk ki lettek kapcsolva.
**Gyakoriság észlelése** |Telt <br><br><br><br> Kész |<br> Az idősorozat elemzése megtörtént, és az összes adatpont az észlelt gyakorisággal van igazítva. <br> <br> Az idősorozat elemzése megtörtént, és az észlelt gyakorisággal nem igazított adatpontok észlelhetők. Ezek az adatpontok el lettek távolítva az adatkészletből. További információ az [idősorozat-előrejelzés adatainak előkészítéséről](./how-to-auto-train-forecast.md#preparing-data).

## <a name="customize-featurization"></a>Featurization testreszabása

Testreszabhatja a featurization beállításait, így biztosíthatja, hogy a ML-modellek betanításához használt adatokat és szolgáltatásokat a megfelelő előrejelzések képezzék.

A featurizations testreszabásához adja meg `"featurization": FeaturizationConfig` az `AutoMLConfig` objektumot az objektumban. Ha a kísérlethez a Azure Machine Learning Studiot használja, tekintse meg a [útmutató cikket](how-to-use-automated-ml-for-ml-models.md#customize-featurization). Az előrejelzési feladatokhoz tartozó featurization testreszabásához tekintse meg az [előrejelzési útmutató című témakört](how-to-auto-train-forecast.md#customize-featurization).

A támogatott testreszabások a következők:

|Testreszabás|Definíció|
|--|--|
|**Oszlop céljának frissítése**|Felülbírálja a megadott oszlop automatikus észlelési funkciójának típusát.|
|**A transzformátor paraméterének frissítése** |Frissítse a megadott átalakító paramétereit. Jelenleg támogatja az *imputált* (mean, leggyakoribb és közepes) és a *HashOneHotEncoder*.|
|**Oszlopok eltávolítása** |Meghatározza az eldobni kívánt oszlopokat a featurized.|
|**Transzformátorok letiltása**| Megadja a featurization-folyamathoz használandó blokk-átalakítókat.|

>[!NOTE]
> A **drop Columns** funkció az SDK 1,19-es verziójától kezdve elavult. Az adatkészletből származó oszlopok eldobása az adatok tisztításának részeként, az automatikus ML-kísérletben való felhasználás előtt. 

Hozza létre az `FeaturizationConfig` OBJEKTUMOT API-hívások használatával:

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="featurization-transparency"></a>Featurization átlátszósága

Minden AutoML-modell featurization automatikusan alkalmazva van.  A Featurization magában foglalja az automatizált funkció-mérnöki (ha `"featurization": 'auto'` ) és a skálázást és a normalizálás funkciót, amely ezután hatással van a kiválasztott algoritmusra és annak hiperparaméter értékeire. A AutoML különböző módszereket támogat, amelyekkel meggyőződhet arról, hogy a modell milyen módon lett alkalmazva.

Tekintse meg ezt az előrejelzési példát:

+ Négy bemeneti funkció létezik: A (numerikus), B (numerikus), C (numerikus), D (DateTime).
+ A C numerikus funkció el lett dobva, mert egy azonosító oszlop, amely minden egyedi értéket tartalmaz.
+ Az "A" és "B" numerikus funkcióinak hiányoznak az értékei, ezért az a középérték alapján van eltulajdonítva.
+ A D DateTime funkció featurized 11 különböző mérnöki funkcióval rendelkezik.

Ezen információk beszerzéséhez használja az `fitted_model` automatikus ml-kísérlet kimenetét.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>Automatizált funkciók tervezése 
A a `get_engineered_feature_names()` megjelenő szolgáltatások neveinek listáját adja vissza.

  >[!Note]
  >Használja a "timeseriestransformer" műveletet a Task = "forecasting" művelethez, máskülönben használja a "datatransformer" műveletet a "regresszió" vagy a "besorolás" feladathoz.

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

Ez a lista tartalmazza az összes megtervezett szolgáltatás nevét. 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

A beolvassa az `get_featurization_summary()` összes bemeneti funkció featurization összegzését.

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

Kimenet

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |Kimenet|Definíció|
   |----|--------|
   |RawFeatureName|A megadott adatkészlet bemeneti funkciójának vagy oszlopának neve.|
   |TypeDetected|A bemeneti funkció észlelt adattípusa.|
   |Csökkent|Jelzi, hogy a bemeneti szolgáltatás el lett-e dobva vagy használatban van-e.|
   |EngineeringFeatureCount|Az automatizált funkciók mérnöki átalakításán keresztül generált szolgáltatások száma.|
   |Átalakítások|A bemenő funkciókra alkalmazott átalakítások listája a mérnöki funkciók létrehozásához.|

### <a name="scaling-and-normalization"></a>Skálázás és normalizálás

A skálázás/normalizálás és a kiválasztott algoritmus hiperparaméter-értékekkel való megismeréséhez használja a következőt: `fitted_model.steps` . 

A következő minta kimenete nem fut a `fitted_model.steps` kiválasztott futtatáshoz:

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

További részletekért használja ezt a segítő funkciót: 

```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(model)
```

Ez a segítő függvény a következő kimenetet adja vissza egy adott futtatáshoz `LogisticRegression with RobustScalar` , amely az adott algoritmust használja.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Előrejelzési osztály valószínűsége

Az automatikus ML-vel előállított modellek mindegyike rendelkezik olyan burkoló objektumokkal, amelyek a nyílt forráskódú Origin osztályból származó funkciókat tükröznek. Az automatizált ML által visszaadott legtöbb besorolási modell a `predict_proba()` függvényt alkalmazza, amely a funkciók (X értékek) tömbhöz hasonló vagy ritka mátrix-adatmintáját fogadja el, és az egyes minták n-dimenziós tömbjét és a hozzá tartozó osztályra vonatkozó valószínűségét adja vissza.

Feltételezve, hogy a fenti hívások közül a legjobb futtatást és a beillesztett modellt is lekérte, `predict_proba()` közvetlenül a beszerelt modellből hívhatja meg a mintát, a `X_test` modell típusától függően a megfelelő formátumban.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Ha az alapul szolgáló modell nem támogatja a `predict_proba()` függvényt, vagy helytelen a formátum, a modell osztályra jellemző kivételt fog dobni. Tekintse meg a [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) és a [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) dokumentációját, amely példákat tartalmaz arra, hogy a függvény hogyan legyen implementálva különböző típusú modellekhez.

<a name="bert-integration"></a>

## <a name="bert-integration-in-automated-ml"></a>BERT-integráció automatizált ML-ben

A [Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) a AutoML featurization rétegében van használatban. Ebben a rétegben, ha egy oszlop tartalmaz szabad szöveget vagy más típusú adattípusokat, például időbélyegeket vagy egyszerű számokat, akkor a featurization ennek megfelelően lesz alkalmazva.

BERT esetében a modell a felhasználó által megadott címkék használatával jól hangolt és betanított. Innentől kezdve a dokumentumok beágyazásai olyan funkciók, mint a többi, például timestamp-alapú funkciók, a hét napja. 


### <a name="steps-to-invoke-bert"></a>A BERT meghívásának lépései

A BERT meghívásához állítsa be a  `enable_dnn: True` automl_settings, és használjon GPU-számítást ( `vm_size = "STANDARD_NC6"` vagy egy magasabb GPU-t). Ha CPU-számítást használ, akkor a BERT helyett a AutoML engedélyezi a BiLSTM DNN Képtulajdonság.

A AutoML a következő lépéseket hajtja végre a BERT esetében. 

1. **Az összes szöveges oszlop előfeldolgozása és jogkivonatok létrehozása**. Például a "StringCast" átalakító a végső modell featurization összegzésében található. [Ebben a jegyzetfüzetben](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)egy példát láthat a modell featurization összegzésének elkészítésére.

2. Az **összes szöveges oszlop összefűzése egyetlen szöveges oszlopba**, ezért a `StringConcatTransformer` végső modellben. 

    A BERT implementációja a betanítási minta teljes szöveges hosszát 128 tokenre korlátozza. Ez azt jelenti, hogy az összes szöveges oszlop összefűzése esetén ideális esetben legfeljebb 128 tokennek kell lennie. Ha több oszlop van jelen, az egyes oszlopokat metszeni kell, hogy ez a feltétel teljesül. Ellenkező esetben az összefűzött oszlopok hossza >128 tokens BERT tokenizer rétege csonkolja ezt a bemenetet 128-tokenekre.

3. **A szolgáltatások elsöprő részeként a AutoML összehasonlítja a BERTt egy adatmintán lévő alapkonfigurációval (a szavak zacskójának funkcióival).** Ez az összehasonlítás meghatározza, hogy a BERT pontos eredményeket adna-e. Ha a BERT jobban teljesíti az alapkonfigurációt, a AutoML ezt követően a BERT szöveget használja a teljes adatok featurization. Ebben az esetben az `PretrainedTextDNNTransformer` utolsó modellben jelenik meg.

A BERT általában hosszabb ideig fut a többi featurizers. A jobb teljesítmény érdekében javasoljuk, hogy a "STANDARD_NC24r" vagy a "STANDARD_NC24rs_V3" használatával RDMA képességeiket. 

A AutoML több csomóponton is terjeszti a BERTs-képzést, ha elérhetők (legfeljebb nyolc csomópontot). Ezt megteheti az `AutoMLConfig` objektumban úgy, `max_concurrent_iterations` hogy a paraméter értéke 1-nél nagyobb. 

## <a name="supported-languages-for-bert-in-automl"></a>A BERT által támogatott nyelvek a autoML-ben 

A AutoML jelenleg 100 nyelvet támogat, és az adatkészlet nyelvétől függően a autoML kiválasztja a megfelelő BERT modellt. Német nyelven a német BERT modellt használjuk. Angol nyelven az angol BERT modellt használjuk. Minden más nyelven a többnyelvű BERT modellt használjuk.

A következő kódban a német BERT modellt indítja el a rendszer, mivel az adatkészlet nyelve meg van adva `deu` , a német nyelvhez tartozó három betűs nyelvi kód az [ISO besorolás](https://iso639-3.sil.org/code/deu)szerint:

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan állíthatja be az automatikus ML-kísérleteket:

    * Első [lépésként: az automatikus ml-kísérletek konfigurálása a Azure Machine learning SDK használatával](how-to-configure-auto-train.md).
    * Alacsony kódú vagy kód nélküli felhasználói felület esetén: [hozzon létre AUTOMATIZÁLT ml-kísérleteket a Azure Machine learning Studióban](how-to-use-automated-ml-for-ml-models.md).

* További információ a [modellek telepítéséről és helyéről](how-to-deploy-and-where.md).

* További információ a [regressziós modellek automatikus gépi tanulással való betanításáról](tutorial-auto-train-models.md) , illetve a [távoli erőforrásokon való automatikus gépi tanulás használatával történő betanításról](how-to-auto-train-remote.md).
