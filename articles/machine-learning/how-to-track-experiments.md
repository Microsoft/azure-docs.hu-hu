---
title: ML-kísérletek és -metrikák naplózása
titleSuffix: Azure Machine Learning
description: A valós idejű futtatási metrikák figyeléséhez, valamint a hibák és a figyelmeztetések diagnosztizálásához engedélyezze a beléptetést az ML-képzésekben.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9576730d9c4f8d4d237dce9ce8f207ea14b04f45
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471601"
---
# <a name="enable-logging-in-ml-training-runs"></a>A naplózás engedélyezése ML betanítási futtatásokban


Az Azure Machine Learning Python SDK az alapértelmezett Python naplózási csomaggal és SDK-specifikus funkciókkal is lehetővé teszi valós idejű információk naplózását. Naplózhat helyben, és elküldheti a naplókat a portálon lévő munkaterületekre.

A naplókkal diagnosztizálhatja a hibákat és a figyelmeztetéseket, vagy nyomon követheti a teljesítménymetrikákat, például a paramétereket és a modell teljesítményét. Ebben a cikkben azzal fog megismerkedni, hogyan engedélyezheti a naplózást a következő forgatókönyvekben:

> [!div class="checklist"]
> * Interaktív betanítási munkamenetek
> * Betanítási feladatok elküldése a ScriptRunConfig használatával
> * Python natív `logging` beállításai
> * Naplózás további forrásokból


> [!TIP]
> Ez a cikk a modell betanítási folyamatának monitorozását mutatja be. Ha az erőforrás-használatot és az eseményeket szeretné monitorozni az Azure Machine Learningből, például a kvótákat, a befejezett betanítási futtatásokat vagy a modell kész üzemelő példányait, tekintse meg [az Azure Machine Learning monitorozásával](monitor-azure-machine-learning.md) kapcsolatos szakaszt.

## <a name="data-types"></a>Adattípusok

Többféle adattípust is naplózhat, például skaláris értékeket, listákat, táblákat, rendszerképeket, könyvtárakat és egyebeket. További információt és a különböző adattípusok Python-kódmintáit a [futtatási osztály referenciaoldalán](/python/api/azureml-core/azureml.core.run%28class%29) találja.

### <a name="logging-run-metrics"></a>Naplózási futtatási metrikák 

A következő módszerek használhatók a naplózási API-kon a metrikák vizualizációinak befolyásolására. Jegyezze fel a naplózott metrikák [szolgáltatási korlátait](https://docs.microsoft.com/azure/machine-learning/resource-limits-quotas-capacity#metrics) . 

|Naplózott érték|Mintakód| Formátum a portálon|
|----|----|----|
|Numerikus értékek tömbje| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Egyváltozós vonalas diagram|
|Egy numerikus értéket többször is felhasználhat ugyanazzal a metrikai névvel (például a cikluson belül)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Egyváltozós vonalas diagram|
|Sor naplózása 2 numerikus oszloppal ismételten|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Kétváltozós vonalas diagram|
|Táblázat két numerikus oszloppal|`run.log_table(name='Sine Wave', value=sines)`|Kétváltozós vonalas diagram|
|Napló képe|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Ezzel a módszerrel egy képfájlt vagy egy matplotlib-rajzot naplózhat a futtatáshoz. Ezek a képek láthatók és összehasonlíthatók lesznek a futtatási rekordban|

### <a name="logging-with-mlflow"></a>Naplózás a MLflow
MLFlowLogger használata a metrikák naplózásához.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="interactive-logging-session"></a>Interaktív naplózási munkamenet

Az interaktív naplózási munkameneteket általában jegyzetfüzet típusú környezetekben használják. Az [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) metódus elindít egy interaktív naplózási munkamenetet. A munkamenet során naplózott összes metrika a kísérlet futtatási rekordjába kerül. A [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) metódus befejezi a munkameneteket, és befejezettként jelöli meg a futtatást.

## <a name="scriptrun-logs"></a>ScriptRun naplók

Ebben a szakaszban azt ismertetjük, hogyan adhat meg naplózási kódot a létrehozott futtatásokban a ScriptRunConfig paranccsal való konfiguráláskor. A [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) osztállyal szkripteket és környezeteket ágyazhat be ismételhető futtatásokhoz. Ezzel a lehetőséggel azt is megteheti, hogy megjelenít egy vizuális Jupyter Notebooks-vezérlőt a monitorozáshoz.

Ez a példa paraméteres frissítést végez az alfa értékeken, és az eredményeket a [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----) metódussal rögzíti.

1. Hozza létre a naplózási logikát tartalmazó `train.py` betanítási szkriptet.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Küldje be a ```train.py``` szkriptet a felhasználó által kezelt környezetekben való futtatáshoz. A rendszer a teljes szkriptmappát elküldi a betanításhoz.

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? név = src)]


   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? név = Futtatás)]

    A `show_output` paraméter bekapcsolja a részletes naplózást, amely lehetővé teszi a betanítási folyamat részleteinek, valamint a távoli erőforrásokkal vagy számítási célokkal kapcsolatos információknak a megtekintését. A következő kóddal kapcsolja be a részletes naplózást a kísérlet beküldésekor.

```python
run = exp.submit(src, show_output=True)
```

Ugyanezt a paramétert használhatja az eredményül kapott futtatás `wait_for_completion` függvényén.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Natív Python-naplózás

Az SDK egyes naplói olyan hibát tartalmazhatnak, amely arra utasítja, hogy a naplózást állítja DEBUG (hibakeresési) szintre. A naplózási szint beállításához adja a következő kódot a szkripthez.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>További naplózási források

Az Azure Machine Learning a betanítás során más forrásokból, például automatizált gépi tanulási futtatásokból vagy a feladatokat futtató Docker-tárolókból származó adatokat is tud naplózni. Ezek a naplók nincsenek dokumentálva, de ha problémákba ütközik, és kapcsolatba lép a Microsoft ügyfélszolgálatával, előfordulhat, hogy a hibaelhárítás során ők tudják használni ezeket a naplókat.

Az Azure Machine Learning-tervező naplózási metrikáiról [tervezőben végzett metrikanaplózással](how-to-track-designer-experiments.md) kapcsolatos témakörben talál információt.

## <a name="example-notebooks"></a>Példajegyzetfüzetek

A cikkben szereplő fogalmakat a következő jegyzetfüzetek mutatják be:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Ezekben a cikkekben többet tudhat meg az Azure Machine Learning használatáról:

* Ismerje meg, hogyan [naplózhat metrikákat az Azure Machine Learning-tervezőben](how-to-track-designer-experiments.md).

* [A képosztályozási modellek Azure Machine Learninggel való betanításával](tutorial-train-models-with-aml.md) kapcsolatos témakörben talál példát arra, hogyan regisztrálhatja a legjobb modellt, és hogyan helyezheti üzembe.
