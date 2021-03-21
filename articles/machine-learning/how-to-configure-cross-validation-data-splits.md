---
title: Az Adatelosztás és a több érvényesítés az automatizált gépi tanulásban
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan konfigurálhatja az adatkészletek felosztását és átellenőrzését az automatizált gépi tanulási kísérleteknél
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 02/23/2021
ms.openlocfilehash: 31d3dc2c2d8194541ba1fe7d0865e6c939d75f73
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501582"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Adatfelosztások és keresztvalidálás konfigurálása az automatizált gépi tanulásban

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a betanítási és az érvényesítési adatmegosztási lehetőségeket, valamint az automatikus gépi tanulás, az automatizált ML és a kísérletek több ellenőrzési beállítását is.

Ha Azure Machine Learning, ha az automatizált ML-t több ML modell kiépítésére használja, minden gyermeknek a modell minőségi metrikáinak kiszámításával ellenőriznie kell a kapcsolódó modellt, például a pontosságot vagy a AUC súlyozottan. Ezek a metrikák az egyes modellekhez tartozó előrejelzések összehasonlításával vannak kiszámítva, és az érvényesítési adatokban szereplő korábbi észrevételekből származó valódi címkékkel vannak ellátva. [További információ a metrikák kiszámításáról az ellenőrzési típus alapján](#metric-calculation-for-cross-validation-in-machine-learning). 

Az automatizált ML-kísérletek automatikusan végzik a modell érvényesítését. Az alábbi szakaszok azt ismertetik, hogyan lehet tovább testreszabni az érvényesítési beállításokat a [Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/)-val. 

Az alacsony kódú vagy a kód nélküli felhasználói élményért lásd: [az automatizált gépi tanulási kísérletek létrehozása Azure Machine learning Studióban](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment). 

> [!NOTE]
> A Studio jelenleg támogatja a betanítási és érvényesítési adatfelosztást, valamint a több ellenőrzési lehetőséget is, de nem támogatja az egyes adatfájlok megadását az érvényesítési csoport számára. 

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez szükséges,

* Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásával kapcsolatban tekintse meg [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md)című témakört.

* A Azure Machine Learning SDK-val való automatikus gépi tanulási kísérlet beállításának ismerete. Kövesse az [oktatóanyagot](tutorial-auto-train-models.md) , vagy az [útmutató](how-to-configure-auto-train.md) segítségével tekintse meg az alapvető automatizált gépi tanulási kísérlet tervezési mintáit.

* A betanítási/érvényesítési adatmegosztások és a gépi tanulásra vonatkozó fogalmak megismerése. A magas szintű magyarázathoz

    * [Tudnivalók a gépi tanulásban betanítási, ellenőrzési és tesztelési célú információkról](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [A gépi tanulásban való több ellenőrzés ismertetése](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>A gépi tanulásban az alapértelmezett adatfelosztás és a több érvényesítés

A kísérlet és a betanítási beállítások megadásához használja a [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objektumot. A következő kódrészletben figyelje meg, hogy csak a szükséges paraméterek vannak definiálva, azaz a vagy a paraméterei `n_cross_validation` `validation_ data` **nem** szerepelnek benne.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Ha explicit módon nem ad meg `validation_data` vagy `n_cross_validation` paramétert, az automatikus ml az alapértelmezett technikákat alkalmazza az egyetlen adatkészletben megadott sorok számától függően `training_data` :

|Betanítási &nbsp; &nbsp; adatméret| Érvényesítési módszer |
|---|-----|
|**Nagyobb, &nbsp; mint &nbsp; 20 000 &nbsp; sor**| A betanítási/érvényesítési adatfelosztást alkalmazza a rendszer. Az alapértelmezett érték a kezdeti betanítási adatkészlet 10%-át adja meg az érvényesítési készletként. Ezt az ellenőrzési készletet pedig a metrikák számításához használja a rendszer.
|**Kisebb, &nbsp; mint &nbsp; 20 000 &nbsp; sor**| A rendszer több ellenőrzési módszert alkalmaz. A kidobások alapértelmezett száma a sorok számától függ. <br> **Ha az adatkészlet kevesebb mint 1 000 sor, a** rendszer 10 karámot használ. <br> **Ha a sorok 1 000 és 20 000 között vannak**, akkor három hajtogatás van használatban.

## <a name="provide-validation-data"></a>Érvényesítési információ megadása

Ebben az esetben akár egyetlen adatfájllal is elindítható, és kioszthatja azokat a betanítási és érvényesítési adatkészletekben, vagy megadhat egy külön adatfájlt az érvényesítési készlethez. Mindkét `validation_data` esetben az objektumban lévő paraméter `AutoMLConfig` hozzárendeli az érvényesítési csoportként használandó összes adattípust. Ez a paraméter csak [Azure Machine learning adatkészlet](how-to-create-register-datasets.md) vagy pandák dataframe formájában fogad adatkészleteket.   

> [!NOTE]
> Az `validation_size` előrejelzési helyzetekben nem támogatott a paraméter.

A következő mintakód kifejezetten meghatározza, hogy a megadott adatmennyiség mely részét `dataset` használja a betanításhoz és az érvényesítéshez.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Ellenőrzési készlet méretének megadása

Ebben az esetben csak egyetlen adatkészlet van megadva a kísérlethez. Ez a paraméter nincs `validation_data` megadva,  és a megadott adatkészlet hozzá van rendelve a `training_data` paraméterhez.  

Az `AutoMLConfig` objektumban beállíthatja, hogy a `validation_size` paraméter a betanítási adatai egy részét kitartsa az érvényesítéshez. Ez azt jelenti, hogy az érvényesítési készletet a kezdeti megadott értéktől kezdve a rendszer automatizált ML-vel osztja el `training_data` . Ennek az értéknek 0,0 és 1,0 közöttinek kell lennie (például a 0,2 azt jelenti, hogy az adatellenőrzések 20%-át az érvényesítési adatként tároljuk).

> [!NOTE]
> Az `validation_size` előrejelzési helyzetekben nem támogatott a paraméter. 

Tekintse meg a következő kódrészletet:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>K-szeres keresztek ellenőrzése

A k-szeres kereszt-ellenőrzés végrehajtásához adja meg a `n_cross_validations` paramétert, és állítsa egy értékre. Ezzel a paraméterrel állítható be, hogy hány több műveletet hajtson végre a rendszer az azonos számú hajtogatás alapján.

> [!NOTE]
> A `n_cross_validations` paraméter nem támogatott a mélyreható neurális hálózatokat használó besorolási helyzetekben.
 
A következő kódban öt összehajtogatható a kereszt-ellenőrzés. Ebből kifolyólag öt különböző képzés, az egyes képzések 4/5-es, valamint minden egyes, a 1/5-es és az összes érvényesítési Holdout az egyes időpontokban.

Ennek eredményeképpen a metrikák kiszámítása az öt ellenőrzési metrika átlagával történik.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>Monte Carlo – kereszt-ellenőrzés

A Monte Carlo kereszt-ellenőrzésének végrehajtásához adja `validation_size` meg a és a paramétereket is az `n_cross_validations` `AutoMLConfig` objektumban. 

A Monte Carlo Cross Validation esetében az automatikus ML a paraméter által az érvényesítéshez megadott betanítási adatok részét képezi `validation_size` , majd kiosztja a többi adat betanítását. Ezt a folyamatot a paraméterben megadott érték alapján megismétli a rendszer `n_cross_validations` , amely új képzést és érvényesítést generál, véletlenszerűen, minden alkalommal.

> [!NOTE]
> Az előrejelzési forgatókönyvek esetében nem támogatott a Monte Carlo kereszt-érvényesítése.

A követési kód határozza meg, 7 kiszorul az átellenőrzéshez, és a betanítási adatmennyiség 20%-át kell használni az ellenőrzéshez. Ezért 7 különböző tanítás, az egyes képzések az adatmennyiség 80%-át használják, és minden egyes ellenőrzés az adatmennyiség 20%-át használja minden alkalommal egy másik holdout.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Egyéni, kereszt-ellenőrzési bedobások meghatározása

Megadhatja a saját kereszt-ellenőrzési (CV) adatait is. Ez egy fejlettebb forgatókönyvnek tekintendő, mert meghatározza, hogy mely oszlopok legyenek felosztva és használhatók az érvényesítéshez.  Vegyen fel egyéni KtgE felosztott oszlopokat a betanítási adatokba, és adja meg, hogy mely oszlopok vannak feltöltve a paraméterben szereplő oszlopnevek alapján `cv_split_column_names` . Minden oszlop egy több-ellenőrzéses felosztást jelöl, és 1 vagy 0 egész számmal van kitöltve – ahol 1 azt jelzi, hogy a sort kell használni a betanításhoz, és 0 azt jelzi, hogy a sort kell használni az ellenőrzéshez.

A következő kódrészlet a bank marketing-adattartalmait tartalmazza, és két, "cV1" és "CV2" típusú KtgE felosztott oszlopot tartalmaz.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> A `cv_split_column_names` és a `training_data` használata `label_column_name` esetén frissítse a Azure Machine learning Python SDK-1.6.0 vagy újabb verzióját. Az előző SDK-verziók esetében tekintse meg a használatát `cv_splits_indices` , de vegye figyelembe, hogy a `X` és az `y` adatkészletek csak a bemenettel együtt használatosak. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Metrikai számítás a gépi tanulásban való kereszt-ellenőrzéshez

Ha a k-fold vagy a Monte Carlo kereszt-ellenőrzés van használatban, a metrikák kiszámítása az egyes ellenőrzésekhez szükséges, majd összesítve történik. Az összesítési művelet a skaláris mérőszámok és a diagramok összegének átlaga. A több ellenőrzés során kiszámított metrikák az összes hajtogatáson alapulnak, ezért a betanítási készletből származó összes mintát. [További információ a mérőszámokról az automatizált gépi tanulásban](how-to-understand-automated-ml.md).

Ha egy egyéni ellenőrző készlet vagy egy automatikusan kiválasztott ellenőrzési készlet van használatban, a modell értékelési mérőszámai csak az adott ellenőrzési készletből vannak kiszámítva, nem a betanítási adatokból.

## <a name="next-steps"></a>Következő lépések

* [A kiegyensúlyozatlan és a túlterheléses adatkezelés megakadályozása](concept-manage-ml-pitfalls.md).
* [Oktatóanyag: automatikus gépi tanulás használata a taxi viteldíjak előrejelzéséhez – az Adatfelosztás szakasz](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Az [idősorozat-előrejelzési modell automatikus betanítása](how-to-auto-train-forecast.md).
