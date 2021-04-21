---
title: Naplók & metrikák és naplófájlok megtekintése
titleSuffix: Azure Machine Learning
description: Engedélyezze a naplózást a gépi tanulási betanítás futtatásaiban a valós idejű futtatás metrikák monitorzásához, valamint a hibák és figyelmeztetések diagnosztizálásához.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bc7564a11f570833bd6cb10c14267e08c3b5753
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820438"
---
# <a name="log--view-metrics-and-log-files"></a>Naplók & metrikák és naplófájlok megtekintése

Valós idejű adatokat naplóz az alapértelmezett Python-naplózási csomag és Azure Machine Learning Python SDK-specifikus funkciók használatával. Naplózhat helyben, és elküldheti a naplókat a portálon lévő munkaterületekre.

A naplókkal diagnosztizálhatja a hibákat és a figyelmeztetéseket, vagy nyomon követheti a teljesítménymetrikákat, például a paramétereket és a modell teljesítményét. Ebben a cikkben azzal fog megismerkedni, hogyan engedélyezheti a naplózást a következő forgatókönyvekben:

> [!div class="checklist"]
> * Naplófuttassa a metrikákat
> * Interaktív betanítási munkamenetek
> * Betanítási feladatok elküldése a ScriptRunConfig használatával
> * Python natív `logging` beállításai
> * Naplózás további forrásokból


> [!TIP]
> Ez a cikk a modell betanítási folyamatának monitorozását mutatja be. Ha az erőforrás-használatot és az eseményeket szeretné monitorozni az Azure Machine Learningből, például a kvótákat, a befejezett betanítási futtatásokat vagy a modell kész üzemelő példányait, tekintse meg [az Azure Machine Learning monitorozásával](monitor-azure-machine-learning.md) kapcsolatos szakaszt.

## <a name="data-types"></a>Adattípusok

Többféle adattípust is naplózhat, például skaláris értékeket, listákat, táblákat, rendszerképeket, könyvtárakat és egyebeket. További információt és a különböző adattípusok Python-kódmintáit a [futtatási osztály referenciaoldalán](/python/api/azureml-core/azureml.core.run%28class%29) találja.

### <a name="logging-run-metrics"></a>Futtatásmetrikák naplózása 

A metrikavizualizációk befolyásolása érdekében használja a következő módszereket a naplózási API-kban. Figyelje [meg ezeknek](./resource-limits-quotas-capacity.md#metrics) a naplózott metrikáknak a szolgáltatási korlátait. 

|Naplózott érték|Mintakód| Formázás a portálon|
|----|----|----|
|Numerikus értékeket tartalmazó tömb naplózása| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|egyváltozós vonaldiagram|
|Egyetlen numerikus érték naplózása ugyanazokkal a metrikanévvel, ismételten használva (például egy for ciklusból)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Egyváltozós vonaldiagram|
|Sor ismétlődő naplózása 2 numerikus oszloppal|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Kétváltozós vonaldiagram|
|Naplótábla 2 numerikus oszloppal|`run.log_table(name='Sine Wave', value=sines)`|Kétváltozós vonaldiagram|
|Naplókép|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Ezzel a módszerrel naplózhat egy képfájlt vagy matplotlib-rajzot a futtatásba. Ezek a lemezképek láthatók és összehasonlíthatók lesznek a futtatásrekordban|

### <a name="logging-with-mlflow"></a>Naplózás az MLflow-val
A metrikák naplózása az MLFlowLogger használatával.

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

## <a name="view-run-metrics"></a>Futtatás metrika megtekintése

## <a name="via-the-sdk"></a>Az SDK-n keresztül
A betanított modell metrikákat a használatával tudja ```run.get_metrics()``` megtekinteni. Lásd az alábbi példát. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-aml-studio-ui"></a>Futtatás metrika megtekintése az AML Studio felhasználói felületén

A befejezett futtatásrekordokat, köztük a naplózott metrikákat a következő [Azure Machine Learning stúdió.](https://ml.azure.com)

Lépjen a **Kísérletek lapra.** A munkaterületen a kísérletek során végzett összes futtatás megtekintéséhez válassza a **Minden futtatás lapot.** Az egyes kísérletekre vonatkozó futtatásokat a felső menüsávon található Kísérlet szűrő alkalmazásával részletezheti.

Az egyes kísérletek nézetnél válassza a **Minden kísérlet** lapot. A kísérlet futtatás irányítópultján láthatja az egyes futtatásokkal kapcsolatos metrikákat és naplókat. 

A futtatáslista táblázatának szerkesztésével több futtatás is kijelölhető, és megjeleníthető a futtatás utolsó, minimális vagy maximális naplózott értéke. A diagramok testreszabása több futtatás naplózott metrikaértékének és összesítésének összehasonlításához. Több metrikát is ábrázolhat a diagram y tengelyére, és az X tengely testreszabásával ábrázolhatja a naplózott metrikákat. 


### <a name="view-and-download-log-files-for-a-run"></a>Futtatáshoz szükséges naplófájlok megtekintése és letöltése 

A naplófájlok alapvető erőforrások az Azure ML számítási feladatainak hibakereséséhez. A betanítási feladat elküldése után részletesen tekintse meg az adott futtatást a naplók és kimenetek megtekintéséhez:  

1. Lépjen a **Kísérletek lapra.**
1. Válassza ki egy adott futtatás futtatásazonosítóját.
1. Válassza **az oldal tetején található** Kimenetek és naplók lehetőséget.
2. Válassza **az Összes letöltése** lehetőséget az összes naplófájl zip-mappába való letöltéséhez.
3. Az egyes naplófájlokat a naplófájl kiválasztásával, majd a Letöltés lehetőség kiválasztásával is **letöltheti.**

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="A futtatás Kimenet és naplók szakaszának képernyőképe.":::

Az alábbi táblázatok a naplófájlok tartalmát mutatják az ebben a szakaszban látható mappákban.

> [!NOTE]
> Nem feltétlenül fog minden egyes futtatásnál minden fájlt látni. Például a 20_image_build_log*.txt fájl csak új rendszerkép létrehozása esetén jelenik meg (például a környezet módosításakor).

#### <a name="azureml-logs-folder"></a>`azureml-logs` Mappa

|Fájl  |Description  |
|---------|---------|
|20_image_build_log.txt     | Docker-rendszerkép-építési napló a betanító környezethez (nem kötelező) futtatásonként egy. Csak a környezet frissítéseke esetén alkalmazható. Ellenkező esetben az AML újra felhasználja a gyorsítótárazott képet. Ha ez sikeres, a tartalmazza a megfelelő rendszerkép rendszerkép-beállításjegyzékének adatait.         |
|55_azureml-execution-<node_id>.txt     | a gazdaeszköz stdout/stderr naplója, csomópontonként egy. Lekért rendszerképet a számítási célhoz. Vegye figyelembe, hogy ez a napló csak akkor jelenik meg, ha már rendelkezik biztonságos számítási erőforrásokkal.         |
|65_job_prep -<node_id>.txt     |   a feladat-előkészítési szkript stdout/stderr naplója, csomópontonként egy. Töltse le a kódot a számítási cél- és adattárakba (ha a rendszer kéri).       |
|70_driver_log(_x).txt      |  stdout/stderr napló az AML-vezérlési szkriptből és az ügyfélbeképzési szkriptből folyamatonként egy. **A szkript standard kimenete. A kód naplói (például a nyomtatási utasítások) ebben a fájlban jelentéseket tartalmaznak.** Az esetek többségében itt fogja figyelni a naplókat.       |
|70_mpi_log.txt     |   MPI-keretrendszer naplója, nem kötelező, futtatásonként egy. Csak MPI-futtatás esetén.   |
|75_job_post-<node_id>.txt     |  a feladat kiadási szkript stdout/stderr naplója, csomópontonként egy. Naplók küldése, a számítási erőforrások felszabadítása az Azure-ba.        |
|process_info.jsbe      |   azt mutatja meg, hogy melyik folyamat melyik csomóponton fut.  |
|process_status.jsbe      | folyamat állapotának megjelenítése, például ha egy folyamat nem indult el, nem fut vagy fejeződött be.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` Mappa

|Fájl  |Description  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   rendszernapló a feladat-előkészítéshez        |
|job_release_azureml.log     | a feladat kiadásának rendszernaplója        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` Mappa

Ha az oldalkocsi engedélyezve van, a feladat-előkészítési és feladat-kiadási szkriptek az oldalkocsi tárolóban futnak.  Minden csomóponthoz egy mappa van. 

|Fájl  |Description  |
|---------|---------|
|start_cms.txt     |  A Sidecar-tároló indításakor elinduló folyamat naplója       |
|prep_cmd.txt      |   A futtatáskor megadott ContextManagers naplója (a tartalom egy része a-be `job_prep.py` lesz streamelve) `azureml-logs/65-job_prep`       |
|release_cmd.txt     |  A ComtextManagers naplója kilépett a `job_release.py` futtatáskor        |

#### <a name="other-folders"></a>Egyéb mappák

A több számítási fürtön betanított feladatokhoz minden csomópont IP-címéhez naplók vannak jelen. Az egyes csomópontok struktúrája megegyezik az egycsomópontos feladatokkal. A teljes végrehajtáshoz, az stderrhez és az stdout-naplókhoz egy további naplómappa is van.

Azure Machine Learning különböző forrásokból, például az AutoML-ről vagy a betanítás feladatot futtató Docker-tárolóból naplózza a különböző forrásokból származó adatokat. A naplók nagy része nincs dokumentálva. Ha problémákba ütközik, és kapcsolatba lép a Microsoft ügyfélszolgálattal, előfordulhat, hogy a hibaelhárítás során fel tudják használni ezeket a naplókat.


## <a name="interactive-logging-session"></a>Interaktív naplózási munkamenet

Az interaktív naplózási munkameneteket általában jegyzetfüzet típusú környezetekben használják. Az [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) metódus elindít egy interaktív naplózási munkamenetet. A munkamenet során naplózott összes metrika a kísérlet futtatási rekordjába kerül. A [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) metódus befejezi a munkameneteket, és befejezettként jelöli meg a futtatást.

## <a name="scriptrun-logs"></a>ScriptRun naplók

Ebben a szakaszban azt ismertetjük, hogyan adhat meg naplózási kódot a létrehozott futtatásokban a ScriptRunConfig paranccsal való konfiguráláskor. A [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) osztállyal szkripteket és környezeteket ágyazhat be ismételhető futtatásokhoz. Ezzel a lehetőséggel azt is megteheti, hogy megjelenít egy vizuális Jupyter Notebooks-vezérlőt a monitorozáshoz.

Ez a példa paraméteres frissítést végez az alfa értékeken, és az eredményeket a [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----) metódussal rögzíti.

1. Hozza létre a naplózási logikát tartalmazó `train.py` betanítási szkriptet.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Küldje be a ```train.py``` szkriptet a felhasználó által kezelt környezetekben való futtatáshoz. A rendszer a teljes szkriptmappát elküldi a betanításhoz.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

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

## <a name="other-logging-sources"></a>Egyéb naplózási források

Az Azure Machine Learning a betanítás során más forrásokból, például automatizált gépi tanulási futtatásokból vagy a feladatokat futtató Docker-tárolókból származó adatokat is tud naplózni. Ezek a naplók nincsenek dokumentálva, de ha problémákba ütközik, és kapcsolatba lép a Microsoft ügyfélszolgálatával, előfordulhat, hogy a hibaelhárítás során ők tudják használni ezeket a naplókat.

Az Azure Machine Learning-tervező naplózási metrikáiról [tervezőben végzett metrikanaplózással](how-to-track-designer-experiments.md) kapcsolatos témakörben talál információt.

## <a name="example-notebooks"></a>Példajegyzetfüzetek

A cikkben szereplő fogalmakat a következő jegyzetfüzetek mutatják be:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Ezekben a cikkekben többet tudhat meg az Azure Machine Learning használatáról:

* [A képosztályozási modellek Azure Machine Learninggel való betanításával](tutorial-train-models-with-aml.md) kapcsolatos témakörben talál példát arra, hogyan regisztrálhatja a legjobb modellt, és hogyan helyezheti üzembe.