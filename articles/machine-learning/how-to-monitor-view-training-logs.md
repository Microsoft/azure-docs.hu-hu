---
title: ML futtatási naplók figyelése és megtekintése & metrikák
titleSuffix: Azure Machine Learning
description: Figyelje a ML-kísérleteket, és tekintse meg a futtatási metrikákat a Jupyter widgetekkel és a Azure Machine Learning Studióval.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 8b2a61a92a25e1c0da9f85439438e75969fcfbf0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661018"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>ML futtatási naplók és metrikák figyelése és megtekintése

Megtudhatja, hogyan figyelheti Azure Machine Learning futtatásait, és hogyan tekintheti meg a naplókat. 

Kísérlet futtatásakor a naplók és a metrikák továbbítva lesznek.  Emellett felveheti a sajátját is.  További információ: [a naplózás engedélyezése az Azure ml-képzésekben](how-to-track-experiments.md).

A naplók segíthetnek a futtatásához szükséges hibák és figyelmeztetések diagnosztizálásában. A teljesítmény-mérőszámok, például a paraméterek és a modell pontossága segíthet a futtatások nyomon követésében és figyelésében.

Ebből a cikkből megtudhatja, hogyan tekintheti meg a naplókat a következő módszerek használatával:

> [!div class="checklist"]
> * A monitor futtatása a Studióban
> * A monitor futtatása a Jupyter Notebook Widget használatával
> * Gépi tanulás automatizált futtatásának figyelése
> * Kimeneti naplók megtekintése a befejezés után
> * Kimeneti naplók megtekintése a Studióban

A kísérletek kezelésével kapcsolatos általános információkért lásd: a [betanítási futtatások elindítása, figyelése és megszakítása](how-to-manage-runs.md).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>A monitor futtatása a Jupyter notebook Widget használatával

Ha a **ScriptRunConfig** metódust használja a futtatások elküldéséhez, tekintse meg a Futtatás előrehaladását a [Jupyter widget](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py)használatával. A futtatás elküldéséhez hasonlóan a vezérlő aszinkron módon működik, és 10-15 másodpercenként élő állapotfrissítést biztosít a feladat befejeződéséig.

Tekintse meg a Jupyter widgetet a Futtatás befejeződésére való várakozás közben.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Képernyőkép a Jupyter notebook widgetről](./media/how-to-track-experiments/run-details-widget.png)

A munkaterületen megjelenő megjelenítésre mutató hivatkozást is kaphat.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Gépi tanulás automatizált futtatásának figyelése

Az automatizált gépi tanulás futtatásához az előző futtatásból származó diagramok eléréséhez cserélje le a parancsot `<<experiment_name>>` a megfelelő kísérlet nevére:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Automatikus Machine Learning Jupyter notebook widget](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Kimenet megjelenítése befejezés után

A **ScriptRunConfig** használatakor a ```run.wait_for_completion(show_output = True)``` segítségével megjelenítheti a modell betanításának befejeződését. A ```show_output``` jelző részletes kimenetet biztosít. További információt a [naplózás engedélyezésének](how-to-track-experiments.md#scriptrun-logs)ScriptRunConfig című szakaszában talál.

<a id="queryrunmetrics"></a>

### <a name="logging-run-metrics"></a>Naplózási futtatási metrikák 

A következő módszerek használhatók a naplózási API-kon a metrikák vizualizációinak befolyásolására. Jegyezze fel a naplózott metrikák [szolgáltatási korlátait](https://docs.microsoft.com/azure/machine-learning/resource-limits-quotas-capacity#metrics) . 

|Naplózott érték|Mintakód| Formátum a portálon|
|----|----|----|
|Numerikus értékek tömbje| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Egyváltozós vonalas diagram|
|Egy numerikus értéket többször is felhasználhat ugyanazzal a metrikai névvel (például a cikluson belül)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Egyváltozós vonalas diagram|
|Sor naplózása 2 numerikus oszloppal ismételten|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Kétváltozós vonalas diagram|
|Táblázat két numerikus oszloppal|`run.log_table(name='Sine Wave', value=sines)`|Kétváltozós vonalas diagram|

## <a name="query-run-metrics"></a>Lekérdezés-futtatási metrikák

A betanított modell metrikáit a használatával tekintheti meg ```run.get_metrics()``` . Például a fenti példával meghatározhatja a legjobb modellt úgy, hogy a legalacsonyabb Mean Square error (MSE) értékkel keresi a modellt.

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-records-in-the-studio"></a>Futtatási rekordok megtekintése a Studióban

A [Azure Machine learning Studióban](https://ml.azure.com)böngészhet a befejezett futtatási rekordok, beleértve a naplózott metrikákat is.

Navigáljon a **kísérletek** lapra. Ha a munkaterületen lévő összes futtatást a kísérletek között szeretné megtekinteni, válassza a **minden Futtatás** lapot. A futtatások részletezését meghatározott kísérletek esetén a felső menüsorban a kísérlet szűrő alkalmazásával végezheti el.

Az egyes kísérletek nézet esetében válassza a **minden kísérlet** lapot. A kísérlet futtatása irányítópulton láthatja az egyes futtatások nyomon követett mérőszámait és naplóit. 

A futtatási lista tábla szerkesztésével több futtatást is kijelölhet, és megjelenítheti a futtatások utolsó, minimális vagy maximális naplózott értékét. A diagramok testreszabásával összehasonlíthatja a naplózott mérőszámok értékeit és összesítéseit több Futtatás között. 

![Részletek futtatása a Azure Machine Learning Studióban](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="view-log-files-for-a-run"></a>Futtatási naplófájlok megtekintése 

A naplófájlok nélkülözhetetlen erőforrást biztosítanak az Azure ML-munkaterhelések hibakereséséhez. A naplók és kimenetek megtekintéséhez végezzen részletezést egy adott futtatásra:  

1. Navigáljon a **kísérletek** lapra.
1. Válassza ki az adott futtatáshoz tartozó runID.
1. Válassza ki a **kimeneteket és a naplókat** az oldal tetején.

:::image type="content" source="media/how-to-monitor-view-training-logs/view-logs.png" alt-text="A Run (kimenet és naplók) szakaszának képernyőképe":::

Az alábbi táblázat a naplófájlok tartalmát mutatja az ebben a szakaszban látható mappákban.

> [!NOTE]
> Minden futtatáskor nem feltétlenül fog megjelenni minden fájl. A 20_image_build_log *. txt például csak akkor jelenik meg, ha új rendszerkép van felépítve (például ha módosítja a környezetet).

#### <a name="azureml-logs-folder"></a>`azureml-logs` mappa

|Fájl  |Leírás  |
|---------|---------|
|20_image_build_log.txt     | Docker-rendszerkép-létrehozási napló a betanítási környezethez, nem kötelező, egy Futtatás. Csak a környezet frissítésekor alkalmazható. Máskülönben a pénzmosás felhasználja a gyorsítótárazott rendszerképet. Ha a művelet sikeres, a rendszerkép beállításjegyzékének részleteit tartalmazza a megfelelő rendszerképhez.         |
|55_azureml-Execution-<node_id # C1.txt     | StdOut/stderr log of Host Tool, egy csomóponton. A rendszerkép lekérése a számítási célra. Megjegyzés: Ez a napló csak akkor jelenik meg, ha biztonságos számítási erőforrásokkal rendelkezik.         |
|65_job_prep-<node_id # C1.txt     |   StdOut/stderr-napló a feladatok előkészítéséhez. Töltse le a kódot a számítási célra és adattárolóba (ha szükséges).       |
|70_driver_log (_x). txt      |  StdOut/stderr-napló a pénzmosás-vezérlési parancsfájlból és az ügyfél-betanítási parancsfájlból, egy folyamaton belül. **Ez a szkript standard kimenete. Itt jelennek meg a kód naplói (például Print utasítások).** Az esetek többségében itt fogja figyelni a naplókat.       |
|70_mpi_log.txt     |   MPI-keretrendszer naplója, választható, egy Futtatás. Csak MPI-futtatáshoz.   |
|75_job_post-<node_id # C1.txt     |  StdOut/stderr-napló a feladathoz tartozó kiadási parancsfájlból, egy csomóponton. Naplók elküldése, a számítási erőforrások felszabadítása vissza az Azure-ba.        |
|process_info.jsbekapcsolva      |   annak megjelenítése, hogy melyik folyamat fut a csomóponton.  |
|process_status.jsbekapcsolva      | a folyamat állapotának megjelenítése, azaz ha egy folyamat nem indult el, fut vagy nem fejeződött be.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` mappa

|Fájl  |Leírás  |
|---------|---------|
|110_azureml. log      |         |
|job_prep_azureml. log     |   rendszernapló a feladat-előkészítéshez        |
|job_release_azureml. log     | a feladat kiadásának rendszernaplója        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` mappa

Ha az oldalkocsi engedélyezve van, a feladatok előkészítési és a feladatok kiadására szolgáló szkriptek az oldalkocsis tárolón belül lesznek futtatva.  Mindegyik csomóponthoz egy mappa van. 

|Fájl  |Leírás  |
|---------|---------|
|start_cms.txt     |  Az oldalkocsi-tároló indításakor megjelenő folyamat naplózása       |
|prep_cmd.txt      |   A futtatáskor megadott ContextManagers naplója `job_prep.py` (ennek egy része a következőre lesz továbbítva: `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  A futtatáskor kilépett ComtextManagers naplója `job_release.py`        |

#### <a name="other-folders"></a>Egyéb mappák

A több számítási fürtön futó feladatok esetében a naplók az egyes csomópont-IP-címeknél jelennek meg. Az egyes csomópontok szerkezete ugyanaz, mint egy csomópontos feladat. A teljes végrehajtás, a stderr és az stdout-naplók számára egy további naplófájl található.

Azure Machine Learning a különböző forrásokból származó információkat naplózza a betanítás során, például a AutoML vagy a betanítási feladatot futtató Docker-tárolóban. A naplók közül sok nincs dokumentálva. Ha problémákat tapasztal, és felveszi a kapcsolatot a Microsoft ügyfélszolgálatával, előfordulhat, hogy a hibaelhárítás során ezeket a naplókat is használni tudja.

## <a name="monitor-a-compute-cluster"></a>Számítási fürt figyelése

Ha figyelni szeretné a futtatásokat egy adott számítási célra a böngészőben, kövesse az alábbi lépéseket:

1. A [Azure Machine learning Studióban](https://ml.azure.com/)válassza ki a munkaterületet, majd a lap bal oldalán kattintson a __számítás__ elemre.

1. Válassza a __betanítási fürtök__ lehetőséget a betanításhoz használt számítási célok listájának megjelenítéséhez. Ezután válassza ki a fürtöt.

    ![Válassza ki a betanítási fürtöt](./media/how-to-track-experiments/select-training-compute.png)

1. Válassza a __futtatások__ lehetőséget. Megjelenik a fürtöt használó futtatások listája. Egy adott Futtatás részleteinek megtekintéséhez használja a __Run (Futtatás__ ) oszlopban található hivatkozást. A kísérlet részleteinek megtekintéséhez használja a __kísérlet__ oszlopban található hivatkozást.

    ![A betanítási fürt futtatásának kiválasztása](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Mivel a kiszámított számítási célok egy megosztott erőforrás, több futtatási várólistára vagy aktívra lehet szükség egy adott időpontban.
    > 
    > A futtatások tartalmazhatnak alárendelt futtatásokat, így egy betanítási feladatok több bejegyzést is eredményezhetnek.

A Futtatás befejezése után már nem jelenik meg ezen a lapon. A befejezett futtatásokkal kapcsolatos információk megtekintéséhez látogasson el a Studio __kísérletek__ szakaszára, és válassza ki a kísérletet, majd futtassa a parancsot. További információért lásd a [befejezett futtatások metrikáinak megtekintése](#view-the-experiment-in-the-web-portal)című szakaszt.


## <a name="next-steps"></a>Következő lépések

Próbálja ki a következő lépéseket a Azure Machine Learning használatának megismeréséhez:

* Ismerje meg, hogyan [követheti nyomon a kísérleteket, és hogyan engedélyezheti a naplókat a Azure Machine learning Designerben](how-to-track-designer-experiments.md).

* [A képosztályozási modellek Azure Machine Learninggel való betanításával](tutorial-train-models-with-aml.md) kapcsolatos témakörben talál példát arra, hogyan regisztrálhatja a legjobb modellt, és hogyan helyezheti üzembe.
