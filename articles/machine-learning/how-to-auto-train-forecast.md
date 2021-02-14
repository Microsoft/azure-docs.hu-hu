---
title: Idősorozat-előrejelzési modell automatikus betanítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható a Azure Machine Learning egy idősorozat-előrejelzési regressziós modell automatikus gépi tanulással történő betanításához.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, automl
ms.date: 08/20/2020
ms.openlocfilehash: 6e686c7b22eb834a096cdd7a67beb6d8d291ef20
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392323"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Idősorozat-előrejelzési modell automatikus betanítása


Ebből a cikkből megtudhatja, hogyan konfigurálhat és betaníthat egy idősorozat-előrejelző regressziós modellt a [Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py)-ban, az automatikus gépi tanulás, a AutoML használatával. 

Ehhez a következőket kell tennie: 

> [!div class="checklist"]
> * Az idősorozat-modellezéssel kapcsolatos adatelőkészítés.
> * Adott idősorozat-paraméterek konfigurálása egy [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objektumban.
> * Előrejelzéseket futtathat az idősorozat-adatsorokkal.

Az alacsony kódú felhasználói élményért tekintse meg a következő [oktatóanyagot:](tutorial-automated-ml-forecast.md) az automatikus gépi tanulással kapcsolatos előrejelzési igények az automatikus gépi tanulással a [Azure Machine learning Studióban](https://ml.azure.com/).

A klasszikus idősorozat-módszerekkel ellentétben az automatikus ML-ben a múltbeli idősorozat-értékek "Pivotal", hogy további dimenziókat regressor a többi előrejelzővel együtt. Ez a megközelítés több kontextusbeli változót is magában foglal, és a képzés során egymáshoz fűződő kapcsolatukat. Mivel több tényező is befolyásolhatja az előrejelzést, ez a módszer jól illeszkedik a valós előrejelzési forgatókönyvekhez. Például az értékesítések előrejelzése, a korábbi trendek, az árfolyamok és az ár közötti interakciók, amelyek mindegyike közösen irányítja az értékesítés eredményét. 

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez szükséges, 

* Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásával kapcsolatban tekintse meg [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md)című témakört.

* Ez a cikk azt feltételezi, hogy az automatikus gépi tanulási kísérlet beállítása némi ismerettel rendelkezik. Kövesse az [oktatóanyagot](tutorial-auto-train-models.md) , vagy a [útmutató](how-to-configure-auto-train.md) segítségével tekintse meg a fő automatizált gépi tanulási kísérlet kialakítási mintáit.

## <a name="preparing-data"></a> Az adatok előkészítése

Az előrejelzési regressziós feladattípus és a regressziós feladattípus közötti legfontosabb különbség a AutoML belül, beleértve az adatok egy érvényes idősorozatot jelölő funkcióját is. A rendszeres idősorozatok jól definiált és konzisztens gyakorisággal rendelkeznek, és minden mintavételi ponton egy értékkel rendelkeznek, amely folyamatos időtartományban van. 

Vegye figyelembe a fájl következő pillanatképét `sample.csv` .
Ez az adathalmaz egy olyan vállalat napi értékesítési adatkészlete, amely két különböző üzlettel, A és B-vel rendelkezik. 

Emellett vannak funkciók a következőhöz:

 *  `week_of_year`: lehetővé teszi, hogy a modell képes legyen a heti szezonális felderíteni.
* `day_datetime`: a napi gyakoriságú tiszta idősorozatot jelöli.
* `sales_quantity`: az előrejelzések futtatásához használt cél oszlop. 

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```


Olvassa el az adatait egy Panda dataframe, majd használja a `to_datetime` függvényt az idősorozat `datetime` típusának biztosításához.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Ebben az esetben az adatmező már növekvő sorrendbe rendezi az adathalmazt `day_datetime` . A kísérlet beállításakor azonban győződjön meg arról, hogy a kívánt idő oszlop növekvő sorrendben van rendezve egy érvényes idősorozat létrehozásához. 

A következő kód, 
* Feltételezi, hogy az adatok 1 000-es rekordokat tartalmaznak, és az adatok alapján determinisztikus az adatkészletek képzéséhez és teszteléséhez. 
* A címke oszlopot azonosítja a következőképpen: `sales_quantity` .
* Elválasztja a Label (címke) mezőt a `test_data` készlet létrehozásához `test_target` .

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> A jövőbeli értékek előrejelzésére szolgáló modell betanításakor győződjön meg arról, hogy a képzésben használt összes funkció használható a kívánt horizonthoz tartozó előrejelzések futtatásakor. <br> <br>Például, ha egy igény-előrejelzést hoz létre, beleértve a jelenlegi tőzsdei árfolyam szolgáltatását is, jelentősen növelheti a képzés pontosságát. Ha azonban hosszú horizontot szeretne előre jelezni, akkor előfordulhat, hogy nem tudja pontosan megjósolni a jövőbeli idősorozat-pontoknak megfelelő készletek értékeit, és a modell pontossága romolhat.

<a name="config"></a>

## <a name="training-and-validation-data"></a>Betanítási és érvényesítési adatszolgáltatások

Az objektumban külön betanítási és érvényesítési készletek is megadhatók `AutoMLConfig` .   További információ a [AutoMLConfig](#configure-experiment).

Az idősorozat-előrejelzés esetében a rendszer alapértelmezés szerint csak a **Rolling Origin Cross Validation (ROCV)** használatát használja az érvényesítéshez. Adja át a betanítási és érvényesítési adatait, és állítsa be, hogy a többszörös ellenőrzés hány a `n_cross_validations` paraméterrel együtt `AutoMLConfig` . A ROCV a sorozatot az oktatási és érvényesítési adatpontok alapján osztja el. A forrás időbeli kidobása a kereszt-érvényesítési betöltést eredményezi. Ez a stratégia megőrzi az idősorozat-adatok integritását, és kiküszöböli az adatok szivárgásának kockázatát

![gördülő forrás – több ellenőrzés](./media/how-to-auto-train-forecast/ROCV.svg)

Saját ellenőrzési adatait is elvégezheti, ha további információra van szüksége az [adatfelosztások és a AutoML-hitelesítés konfigurálásáról](how-to-configure-cross-validation-data-splits.md#provide-validation-data).


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

További információ arról, hogy a AutoML hogyan alkalmazza a határokon átnyúló ellenőrzéseket a [túlzottan illeszkedő modellek megelőzése](concept-manage-ml-pitfalls.md#prevent-over-fitting)érdekében.

## <a name="configure-experiment"></a>Kísérlet konfigurálása

Az [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) objektum meghatározza az automatizált gépi tanulási feladatokhoz szükséges beállításokat és adatmennyiséget. Az előrejelzési modell konfigurációja hasonló a standard regressziós modell beállításához, de bizonyos modellek, konfigurációs beállítások és featurization lépések kifejezetten az idősorozat-adatsorokra vonatkoznak. 

### <a name="supported-models"></a>Támogatott modellek
Az automatizált gépi tanulás automatikusan különböző modelleket és algoritmusokat próbál a modell létrehozási és hangolási folyamatának részeként. Felhasználóként nem kell megadnia az algoritmust. Az előrejelzési kísérletek esetében a natív idősorozat és a Deep learning modellek is a javaslati rendszer részét képezik. A következő táblázat összefoglalja a modellek ezen részhalmazát. 

>[!Tip]
> A rendszer a hagyományos regressziós modelleket is teszteli a javaslatrendszer részeként az előrejelzési kísérletekhez. A modellek teljes listájáért tekintse meg a [támogatott modell táblázatát](how-to-configure-auto-train.md#supported-models) . 

Modellek| Description | Előnyök
----|----|---
Próféta (előzetes verzió)|A próféta a legjobb idősorozattal működik, amely erős szezonális hatásokat és több időszakot is tartalmaz. A modell kihasználása érdekében telepítse helyileg a használatával `pip install fbprophet` . | Pontos & gyors, robusztus a kiugró értékek, a hiányzó adatmennyiségek és az idősorozat drámai változásai.
Automatikus ARIMA (előzetes verzió)|Az automatikusan újradegresszív, integrált mozgóátlag (ARIMA) a legjobbat hajtja végre, ha az adatok állomáson vannak. Ez azt jelenti, hogy a statisztikai tulajdonságok, például a középérték és a variancia állandó a teljes készleten. Ha például egy érme tükrözését hajtja végre, akkor a fejek megszerzésének valószínűsége 50% lesz, függetlenül attól, hogy a mai, a holnapi vagy a következő évre fordít-e.| Kiválóan használható a univariate sorozatokhoz, mivel a korábbi értékeket a jövőbeli értékek előrejelzésére használjuk.
ForecastTCN (előzetes verzió)| A ForecastTCN egy olyan neurális hálózati modell, amely a legigényesebb előrejelzési feladatok kezelésére, a nem lineáris helyi és globális trendek rögzítésére szolgál az adatokban, valamint az idősorozatok közötti kapcsolatokat.|Képes az adathalmazok összetett trendjeinek kihasználása és a nagy adatkészletek rugalmas méretezésére.

### <a name="configuration-settings"></a>Konfigurációs beállítások

A regressziós problémákhoz hasonlóan szabványos betanítási paramétereket is definiálhat, például a feladattípust, az ismétlések számát, a betanítási adatok számát és az eltérő érvényességi értéket. Az előrejelzési feladatokhoz további paramétereket kell megadni, amelyek hatással vannak a kísérletre. 

A következő táblázat összefoglalja ezeket a további paramétereket. A szintaxis kialakítási mintáit a [ForecastingParameter osztály dokumentációjában](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) találja.

| Paraméter &nbsp; neve | Leírás | Kötelező |
|-------|-------|-------|
|`time_column_name`|A dátum-és idősorozatok létrehozásához használt bemeneti adatok datetime oszlopának megadására szolgál.|✓|
|`forecast_horizon`|Meghatározza, hogy hány időszakot továbbítson az előrejelzéshez. A horizont az idősorozat gyakoriságának egységében van. Az egységek a betanítási adatokat tartalmazó időintervallumon alapulnak, például havi, heti rendszerességgel, amelyet az előrejelzésnek meg kell jósolnia.|✓|
|`enable_dnn`|[Előrejelzési DNN engedélyezése]().||
|`time_series_id_column_names`|Az oszlop neve (i), amely az azonos időbélyegzővel rendelkező több sorból álló adatsorozatok egyedi azonosítására szolgál. Ha az idősorozat-azonosítók nincsenek meghatározva, az adathalmazt a rendszer egy idősorozatként feltételezi. Az egyidejű adatsorozatokkal kapcsolatos további tudnivalókért tekintse meg a [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).||
|`freq`| Az idősorozat-adatkészlet gyakorisága Ez a paraméter azt az időszakot jelöli, amelyben az események várhatóan előfordulnak, például naponta, hetente, évente stb. A gyakoriságnak [pandák eltolású aliasnak](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)kell lennie.||
|`target_lags`|A megcélzott értékeket az adatok gyakorisága alapján késleltető sorok száma. A lag listaként vagy egyetlen egész számként jelenik meg. A késést akkor kell használni, ha a független változók és a függő változó közötti kapcsolat alapértelmezés szerint nem felel meg egymásnak. ||
|`feature_lags`| A rendszer automatikusan eldönti a lag funkcióit, ha `target_lags` be van állítva, és `feature_lags` be van állítva `auto` . A szolgáltatás lekésésének engedélyezése segíthet a pontosság növelésében. A funkció alapértelmezés szerint le van tiltva. ||
|`target_rolling_window_size`|*n* korábbi időszakok, amelyeket az előre jelzett értékek előállítására használhat, <= betanítási készlet mérete. Ha nincs megadva, az *n* a teljes betanítási készlet mérete. Akkor válassza ezt a paramétert, ha csak bizonyos mennyiségű előzményt szeretne figyelembe venni a modell betanításakor. További információ a [cél gördülő ablak összesítéséről](#target-rolling-window-aggregation).||
|`short_series_handling_config`| Lehetővé teszi a rövid idősorozatok kezelését, hogy elkerülje a nem megfelelő adatmennyiséget a betanítás során. A rövid adatsorozatok kezelője alapértelmezés szerint be van állítva `auto` . További információ a [rövid adatsorozatok kezelésére](#short-series-handling).|


A következő kód, 
* Kihasználja az [`ForecastingParameters`](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) osztályt, hogy meghatározza az előrejelzési paramétereket a kísérlet betanításához
* Beállítja a `time_column_name` értékét az `day_datetime` adatkészlet mezőjére. 
* A paramétert adja meg a következőhöz: `time_series_id_column_names` `"store"` . Ez biztosítja, hogy az **adatsorozatok két különálló csoportja** legyen létrehozva. egyet az A és A B áruházhoz.
* A 50 értékre állítja a `forecast_horizon` teljes tesztelési készlet előrejelzését. 
* Előrejelzési ablak beállítása 10 időszakra a következővel `target_rolling_window_size`
* A (z) paraméterrel rendelkező két időszakra vonatkozóan egyetlen késést ad meg a célként megadott értékeken `target_lags` . 
* `target_lags`Az ajánlott "automatikus" beállítást állítja be, amely automatikusan felismeri ezt az értéket.

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                               forecast_horizon=50,
                                               time_series_id_column_names=["store"],
                                               freq='W',
                                               target_lags='auto',
                                               target_rolling_window_size=10)
                                              
```

Ezeket a `forecasting_parameters` rendszer a szabványos `AutoMLConfig` objektumba továbbítja a `forecasting` feladat típusa, az elsődleges metrika, a kilépési feltételek és a betanítási adatok mellett. 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **forecasting_parameters)
```

Az automatikus ML-előrejelzési modell sikeres betanításához szükséges adatok mennyiségét a, a és a `forecast_horizon` `n_cross_validations` `target_lags` `target_rolling_window_size` konfigurálásakor megadott értékek befolyásolják `AutoMLConfig` . 

A következő képlet kiszámítja az idősorozat-funkciók létrehozásához szükséges történelmi adatmennyiséget.

Minimálisan szükséges korábbi adatmennyiség: (2x `forecast_horizon` ) + # `n_cross_validations` + Max (max ( `target_lags` ), `target_rolling_window_size` )

Hiba történt az adatkészlet bármely olyan adatsorozata esetében, amely nem felel meg a megfelelő megadott beállításoknak. 

### <a name="featurization-steps"></a>Featurization lépések

Az automatikus skálázási és normalizáló technikákat minden automatizált gépi tanulási kísérlet során alapértelmezés szerint alkalmazza a rendszer az adataira. Ezek a technikák olyan **featurization** -típusok, amelyek *bizonyos* , különböző léptékű funkciókra érzékeny algoritmusokat nyújtanak. További információ a [AutoML-beli featurization](how-to-configure-auto-features.md#automatic-featurization) alapértelmezett featurization lépéseiről

A következő lépések azonban csak a feladattípusok esetén hajthatók végre `forecasting` :

* Az idősorozat mintavételi gyakoriságának (például óránkénti, napi, heti) észlelése, és új rekordok létrehozása a hiányzó időpontokhoz, hogy a sorozat folyamatos legyen.
* Hiányzó értékek bevonása a célhelyből (továbbítási kitöltéssel) és a funkció oszlopai (medián Column Values használatával)
* Funkciók létrehozása idősorozat-azonosítók alapján a különböző adatsorozatok rögzített hatásainak engedélyezéséhez
* Időalapú szolgáltatások létrehozása a szezonális minták tanulásának segítésére
* Kategorikus változók kódolása numerikus mennyiségre

A fenti lépések eredményeképpen létrehozott funkciók összefoglalását lásd: [Featurization átlátszóság](how-to-configure-auto-features.md#featurization-transparency)

> [!NOTE]
> Az automatizált gépi tanulás featurization lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos featurization-lépéseket automatikusan alkalmazza a rendszer a bemeneti adatokra.

#### <a name="customize-featurization"></a>Featurization testreszabása

Lehetősége van arra is, hogy testreszabja a featurization beállításait, így biztosítva, hogy a ML-modell betanításához használt adatokat és szolgáltatásokat a megfelelő előrejelzések képezzék. 

A feladatok támogatott testreszabásai a `forecasting` következők:

|Testreszabás|Definíció|
|--|--|
|**Oszlop céljának frissítése**|Felülbírálja a megadott oszlop automatikusan észlelt funkciójának típusát.|
|**A transzformátor paraméterének frissítése** |Frissítse a megadott átalakító paramétereit. Jelenleg támogatja az *imputált* (fill_value és medián).|
|**Oszlopok eltávolítása** |Meghatározza az eldobni kívánt oszlopokat a featurized.|

Az SDK-val való featurizations testreszabásához adja meg az `"featurization": FeaturizationConfig` `AutoMLConfig` objektumot az objektumban. További információ az [Egyéni featurizations](how-to-configure-auto-features.md#customize-featurization).

>[!NOTE]
> A **drop Columns** funkció az SDK 1,19-es verziójától kezdve elavult. Az adatkészletből származó oszlopok eldobása az adatok tisztításának részeként, az automatikus ML-kísérletben való felhasználás előtt. 

```python
featurization_config = FeaturizationConfig()

# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']

# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')

# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})

# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

Ha a kísérlethez a Azure Machine Learning Studiot használja, tekintse meg [a featurization testreszabása a Studióban](how-to-use-automated-ml-for-ml-models.md#customize-featurization)című témakört.

## <a name="optional-configurations"></a>Választható konfigurációk

További opcionális konfigurációk is elérhetők az előrejelzési feladatokhoz, például a Deep learning engedélyezéséhez és a cél gördülő ablak összesítésének megadásához. 

### <a name="enable-deep-learning"></a>Mély tanulás engedélyezése

> [!NOTE]
> Az automatikus Machine Learning DNN támogatása **előzetes** verzióban érhető el, és helyi futtatások esetén nem támogatott.

A részletes tanulási és a mélyreható neurális hálózatokkal is kihasználhatja a DNN, így hatékonyabbá teheti a modell pontszámait. Az automatizált ML mély tanulási funkciói lehetővé teszik az előrejelzési univariate és a többváltozós idősorozatok adatsorait.

A Deep learning-modellek három belső képességgel rendelkeznek:
1. Tanulhatnak a bemenetekről a kimenetekre való tetszőleges leképezésekről
1. Több bemenetet és kimenetet támogatnak
1. Automatikusan kinyerhetik a hosszú sorokra kiterjedő bemeneti adatok mintáit. 

A Deep learning engedélyezéséhez állítsa be az `enable_dnn=True` `AutoMLConfig` objektumot az objektumban.

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **forecasting_parameters)
```
> [!Warning]
> Ha az SDK-val létrehozott kísérletek DNN engedélyezi, a [legjobb modell magyarázata](how-to-machine-learning-interpretability-automl.md) le van tiltva.

Ha engedélyezni szeretné a DNN a Azure Machine Learning Studióban létrehozott AutoML-kísérlethez, tekintse meg a következő témakörben található feladattípus [-beállításokat: a Studio útmutatója](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

Tekintse meg az [üzemi előrejelzést tartalmazó jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) , amely egy részletes kód, például a DNN kihasználása.

### <a name="target-rolling-window-aggregation"></a>Cél gördülő ablak összesítése
A legjobb információ gyakran a cél legutóbbi értéke lehet.  A cél gördülő ablak összesítései lehetővé teszik, hogy az adatértékek folyamatos összesítését adja hozzá szolgáltatásként. Ezeket a kiegészítő funkciókat további kontextusbeli adatok létrehozásával és használatával segítheti a betanítási modell pontosságát.

Tegyük fel például, hogy meg szeretné jósolni az energia iránti keresletet. Előfordulhat, hogy három napos gördülő ablak-szolgáltatást szeretne hozzáadni a fűtött szóközök termikus változásainak figyelembevételéhez. Ebben a példában hozza létre ezt az ablakot `target_rolling_window_size= 3` a konstruktor beállításával `AutoMLConfig` . 

A táblázat megjeleníti a funkciók mérnöki felépítésének eredményét, amely az ablak összesítésének alkalmazása után következik be. A **minimális, a maximális** és az **összeg** oszlopok a megadott beállítások alapján három ablakból jönnek létre. Az egyes sorok új számított funkcióval rendelkeznek, amely az időbélyegzőnél szeptember 8-án 2017 4: a maximális, a minimum és az összeg értékének kiszámításához az **igény értékeit** kell kiszámítani. szeptember 8., 2017 1:20:00 – 3:20:00. Ez az ablak három műszakban jelenik meg, hogy feltöltse a fennmaradó sorokhoz tartozó adatokat.

![cél gördülő ablak](./media/how-to-auto-train-forecast/target-roll.svg)

Tekintse meg a [cél gördülő ablak összesítési funkcióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)kihasználó Python-kód példáját.

### <a name="short-series-handling"></a>Rövid adatsorozat-kezelő

Az automatizált ML egy **rövid** adatsorozatot vesz figyelembe, ha nincs elegendő adatpont a modell fejlesztésének betanítási és ellenőrzési fázisának végrehajtásához. Az adatpontok száma minden kísérlet esetében változik, és a max_horizontól, a több ellenőrzési osztástól, valamint a modell lookback hosszától függ, azaz az idősorozat-funkciók létrehozásához szükséges előzmények maximális száma. A pontos számításhoz tekintse meg a [short_series_handling_configuration dokumentációját](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py#short-series-handling-configuration).

Az automatikus ML az `short_series_handling_configuration` objektumban található paraméterrel alapértelmezés szerint rövid adatsorozat-kezelést nyújt `ForecastingParameters` . 

A rövid adatsorozatok kezelésére a `freq` paramétert is meg kell adni. Az óránkénti gyakoriság meghatározásához be kell állítani a következőt: `freq='H'` . [Itt](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)tekintheti meg a gyakorisági karakterlánc beállításait. Az alapértelmezett viselkedés módosításához `short_series_handling_configuration = 'auto'` frissítse a `short_series_handling_configuration` paramétert az `ForecastingParameter` objektumban.  

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecast_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                            forecast_horizon=50,
                                            short_series_handling_configuration='auto',
                                            freq = 'H',
                                            target_lags='auto')
```
Az alábbi táblázat a rendelkezésre álló beállításait foglalja össze `short_series_handling_config` .
 
|Beállítás|Leírás
|---|---
|`auto`| A rövid adatsorozatok kezelésére vonatkozó alapértelmezett viselkedés a következő: <li> *Ha az összes adatsorozat rövid*, a pad az adategységeket. <br> <li> *Ha nem minden adatsor rövid*, dobja el a rövid sorozatot. 
|`pad`| Ha `short_series_handling_config = pad` a, akkor az automatikus ml véletlenszerű értékeket hoz létre minden egyes talált rövid adatsorozathoz. Az alábbi táblázat felsorolja az oszlop típusait, valamint azt, hogy mire vannak feltöltve: <li>NaNs rendelkező objektumok oszlopai <li> Numerikus oszlopok 0 <li> Logikai/logikai oszlopok hamis értékkel <li> A célként megadott oszlop véletlenszerű értékekkel van feltöltve, és az értéke nulla, a szórás pedig 1. 
|`drop`| Ha `short_series_handling_config = drop` a, akkor az automatikus ml elveszi a rövid sorozatot, és nem lesz használatban képzésre vagy előrejelzésre. A sorozathoz tartozó előrejelzések a NaN által visszaadott értéket adják vissza.
|`None`| Egy adatsorozat nem párnázott vagy nem lett elvetve

>[!WARNING]
>A kitöltés hatással lehet az eredményül kapott modell pontosságára, mivel a mesterséges adatok bevezetését csak a múltbeli képzések lekérése okozta hibák nélkül. <br> <br> Ha az adatsorozatok közül sok rövid, akkor előfordulhat, hogy bizonyos hatással van a magyarázó eredményekre

## <a name="run-the-experiment"></a>Kísérlet futtatása 

Ha `AutoMLConfig` elkészült az objektum, elküldheti a kísérletet. A modell befejeződése után kérje le a legjobb futtatási iterációt.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-forecasting")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```
 
## <a name="forecasting-with-best-model"></a>Előrejelzés a legjobb modellel

Használja a legjobb modell iterációt a tesztelési adatkészletre vonatkozó előrejelzési értékekhez.

A `forecast()` függvény lehetővé teszi a specifikációk használatát, amikor a jóslatok elindulnak, a-től eltérően `predict()` , amely jellemzően besorolási és regressziós feladatok esetében használatos.

A következő példában először az összes értéket kell lecserélnie a alkalmazásban `y_pred` `NaN` . Ebben az esetben az előrejelzési forrás az adatképzés végén lesz. Ha azonban csak a második felét cserélte le `y_pred` `NaN` , a függvény a numerikus értékeket az első fél változatlanul hagyja, de a második félidőben is megbecsüli az `NaN` értékeket. A függvény az előre jelzett értékeket és az igazított funkciókat is visszaadja.

A `forecast_destination` függvény paraméterét is használhatja az `forecast()` értékek előrejelzésére a megadott dátumig.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

A `actual_labels` tényleges értékek és az előre jelzett értékek között a legfelső szintű négyzetes hiba (gyökátlagos) kiszámítása `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Most, hogy a modell teljes pontossága meg lett határozva, a legreálisabb következő lépés a modell használata az ismeretlen jövőbeli értékek előrejelzésére. 

Adja meg az adathalmazt a tesztelési csoporttal megegyező formátumban `test_data` , de a jövőbeli dátum/idő értékkel, az eredményül kapott előrejelzési készlet pedig az egyes idősorozat-lépések előre jelzett értékei. Tegyük fel, hogy az adatkészletben az utolsó idősorozat rekord a 12/31/2018-es értékre van állítva. Ha a következő napra (vagy az előrejelzéshez szükséges számos időszakra, <=) szeretne előrejelzést `forecast_horizon` készíteni, hozzon létre egy egyidejű adatsorozat-rekordot a 01/01/2019-es tárolóhoz.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Ismételje meg a szükséges lépéseket a jövőbeli adatok egy dataframe való betöltéséhez, majd futtassa a parancsot `best_run.predict(test_data)` a jövőbeli értékek előrejelzéséhez.

> [!NOTE]
> A mintavételi előrejelzések nem támogatottak az automatikus ML-vel való előrejelzéshez, ha `target_lags` és/vagy `target_rolling_window_size` engedélyezve vannak.


## <a name="example-notebooks"></a>Példajegyzetfüzetek
Tekintse meg az [előrejelzési minta jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) a speciális előrejelzési konfigurációk részletes kódjával kapcsolatban, beleértve a következőket:

* [üdülés észlelése és featurization](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [gördülő-eredetű kereszt-ellenőrzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurálható késések](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [a gördülő ablak összesített funkciói](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>Következő lépések

* További információ a [modellek telepítéséről és helyéről](how-to-deploy-and-where.md).
* Ismerje meg az [értelmezést: modell-magyarázatok az automatikus gépi tanulásban (előzetes verzió)](how-to-machine-learning-interpretability-automl.md). 
* Ismerje meg, hogyan taníthat több modellt a AutoML [számos modell megoldás-gyorsító](https://aka.ms/many-models)használatával.
* A kísérletek automatikus gépi tanulással történő létrehozásához kövesse az [oktatóanyagot](tutorial-auto-train-models.md) a teljes körű példához.
