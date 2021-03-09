---
title: Azure Machine Learning folyamatok indítása
titleSuffix: Azure Machine Learning
description: Az aktivált folyamatok lehetővé teszik a rutin, az időigényes feladatok, például az adatfeldolgozás, a képzés és a figyelés automatizálását.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 01/29/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3ecf4458b052f4fdc0eb2e6e697b0468c71ce9c2
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519656"
---
# <a name="trigger-machine-learning-pipelines"></a>Gépi tanulási folyamatok indítása

Ebből a cikkből megtudhatja, hogyan ütemezhet programozott módon egy folyamat futtatását az Azure-ban. Az eltelt idő alapján vagy a fájl-rendszer változásain alapuló ütemtervet is létrehozhat. Az időalapú ütemtervek segítségével gondoskodhat a rutin feladatokról, például az adateltolódás figyeléséről. A változáson alapuló ütemtervek felhasználhatók a szabálytalan vagy kiszámíthatatlan változásokra való reagálásra, például a feltöltött új vagy régi adatfeldolgozásra. Az ütemtervek létrehozásának megismerése után megismerheti, hogyan kérheti le és inaktiválja azokat. Végezetül megtudhatja, hogyan használhatja a többi Azure-szolgáltatást, az Azure Logic app-t és a Azure Data Factory-t a folyamatok futtatásához. Az Azure logikai alkalmazások összetettebb kiváltó logikát vagy viselkedést tesznek lehetővé. Azure Data Factory folyamatok lehetővé teszik egy gépi tanulási folyamat meghívását egy nagyobb adatfeldolgozási folyamat részeként.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).

* Python-környezet, amelyben a Pythonhoz készült Azure Machine Learning SDK telepítve van. További információkért lásd: [újrafelhasználható környezetek létrehozása és kezelése a Azure Machine learning-vel való betanításhoz és üzembe helyezéshez.](how-to-use-environments.md)

* Egy Machine Learning munkaterület közzétett folyamattal. A [Machine learning-folyamatokat a Azure Machine learning SDK-val létrehozhatja és futtathatja](./how-to-create-machine-learning-pipelines.md).

## <a name="trigger-pipelines-with-azure-machine-learning-sdk-for-python"></a>Folyamatok indítása a Pythonhoz készült Azure Machine Learning SDK-val

Egy folyamat beütemezhet egy hivatkozást a munkaterületre, a közzétett folyamat azonosítóját, valamint annak a kísérletnek a nevére, amelyben létre kívánja hozni az ütemtervet. Ezeket az értékeket a következő kóddal érheti el:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Ütemterv létrehozása

Egy folyamat ismétlődő futtatásához létre kell hoznia egy ütemtervet. A egy folyamatot `Schedule` , egy kísérletet és egy triggert társít. Az trigger lehet egy `ScheduleRecurrence` , amely leírja a futtatások közötti várakozást, vagy egy adattár elérési útját, amely megadja a módosításokat bemutató könyvtárat. Mindkét esetben szüksége lesz a folyamat azonosítójára és annak a kísérletnek a nevére, amelyben létre kívánja hozni az ütemtervet.

Importálja a (z) `Schedule` és osztályokat a Python-fájl elejére `ScheduleRecurrence` :

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Időalapú ütemterv létrehozása

A `ScheduleRecurrence` konstruktor egy kötelező `frequency` argumentummal rendelkezik, amelynek a következő karakterláncok egyikének kell lennie: "minute", "Hour", "Day", "Week" vagy "Month". Egy egész szám argumentumot is igényel, amely `interval` meghatározza, hogy hány `frequency` egységnek kell eltelnie az ütemterv kezdete között. A választható argumentumok lehetővé teszik a [SCHEDULERECURRENCE SDK-dokumentációban](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence)részletezett időpontokra vonatkozó részletesebb tudnivalókat.

Hozzon létre egy-t `Schedule` , amely 15 percenként elindítja a futtatást:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Változáson alapuló ütemterv létrehozása

A fájlok változásai által aktivált folyamatok hatékonyabbak lehetnek, mint az időalapú ütemtervek. Ha meg szeretne tenni valamit a fájl módosítása előtt, vagy ha új fájlt adnak hozzá egy adatkönyvtárhoz, a fájl előfeldolgozására van lehetőség. Az adattáron belüli adattárolók és az adattárban lévő módosítások változásai figyelhetők. Ha egy adott könyvtárat figyel, akkor a könyvtár alkönyvtárain belüli módosítások _nem_ indítják el a futtatást.

Fájl – reaktív létrehozásához a (z) `Schedule` paramétert be kell állítania `datastore` a [Schedule. Create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-)értékre. Egy mappa figyeléséhez állítsa be az `path_on_datastore` argumentumot.

Az `polling_interval` argumentummal percek alatt megadhatja, hogy az adattár milyen gyakorisággal legyen bejelölve a változásokhoz.

Ha a folyamat [DataPath](/python/api/azureml-core/azureml.data.datapath.datapath) - [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter)lett létrehozva, a változót a módosított fájl nevére állíthatja be az argumentum beállításával `data_path_parameter_name` .

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Az ütemterv létrehozásakor nem kötelező argumentumok

A korábban tárgyalt argumentumokon kívül az argumentumot úgy is beállíthatja, hogy `status` `"Disabled"` inaktív ütemtervet hozzon létre. Végül a `continue_on_step_failure` lehetővé teszi, hogy egy olyan logikai értéket adjon át, amely felülbírálja a folyamat alapértelmezett meghibásodási viselkedését.

## <a name="view-your-scheduled-pipelines"></a>Az ütemezett folyamatok megtekintése

A böngészőben nyissa meg a Azure Machine Learning. A navigációs panel **végpontok** szakaszában válassza a **folyamat végpontjai** lehetőséget. Ekkor megjelenik a munkaterületen közzétett folyamatok listája.

:::image type="content" source="./media/how-to-trigger-published-pipeline/scheduled-pipelines.png" alt-text="PÉNZMOSÁS-folyamatok lapja":::

Ezen a lapon megtekintheti a munkaterület összes folyamatával kapcsolatos összegző információkat: nevek, leírások, állapot stb. Részletezés a folyamatra kattintva. Az eredményül kapott oldalon további részleteket talál a folyamatról, és az egyes futtatásokat is lerészletezheti.

## <a name="deactivate-the-pipeline"></a>A folyamat inaktiválása

Ha van `Pipeline` közzétett, de nincs ütemezve, akkor letilthatja a következőket:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Ha a folyamat ütemezve van, először le kell mondania az ütemezést. Kérje le az ütemterv azonosítóját a portálról, vagy futtassa a következőt:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Miután `schedule_id` letiltotta a letiltását, futtassa a következőt:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Ha ezt követően újra futtatja `Schedule.list(ws)` , üres listát kell kapnia.

## <a name="use-azure-logic-apps-for-complex-triggers"></a>Azure Logic Apps használata összetett eseményindítók esetén 

Az összetettebb trigger-szabályok vagy-viselkedés az [Azure Logic apps](../logic-apps/logic-apps-overview.md)használatával hozhatók létre.

Ahhoz, hogy egy Azure Logic App-alkalmazás elindítson egy Machine Learning folyamatot, szüksége lesz egy közzétett Machine Learning-folyamat REST-végpontra. [A folyamat létrehozása és közzététele](./how-to-create-machine-learning-pipelines.md). Ezután keresse meg a REST-végpontját a `PublishedPipeline` folyamat azonosítójának használatával:

```python
# You can find the pipeline ID in Azure Machine Learning studio

published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
published_pipeline.endpoint 
```

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Most hozzon létre egy [Azure Logic app](../logic-apps/logic-apps-overview.md) -példányt. Ha szeretné, [használjon integrációs szolgáltatási környezetet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , és [állítson be egy ügyfél által felügyelt kulcsot](../logic-apps/customer-managed-keys-integration-service-environment.md) a logikai alkalmazás általi használatra.

A logikai alkalmazás üzembe helyezése után a következő lépésekkel konfigurálhat egy triggert a folyamathoz:

1. [Hozzon létre egy rendszerhez rendelt felügyelt identitást](../logic-apps/create-managed-service-identity.md) , amely hozzáférést biztosít az alkalmazásnak a Azure Machine learning-munkaterülethoz.

1. Navigáljon a Logic app Designer nézethez, és válassza ki az üres logikai alkalmazás sablonját. 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/blank-template.png" alt-text="Üres sablon":::

1. A tervezőben keresse meg a **blob** kifejezést. Válassza ki a **blob hozzáadása vagy módosítása (csak tulajdonságok)** triggert, és adja hozzá ezt az triggert a logikai alkalmazáshoz.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/add-trigger.png" alt-text="Trigger hozzáadása":::

1. Adja meg a blob-hozzáadások vagy-módosítások figyeléséhez használni kívánt blob Storage-fiók kapcsolódási adatait. Válassza ki a figyelni kívánt tárolót. 
 
    Válassza ki az **időintervallumot** és a **gyakoriságot** az Önnek munkát igénylő frissítések lekérdezéséhez.  

    > [!NOTE]
    > Ez az trigger figyeli a kijelölt tárolót, de nem figyeli az almappákat.

1. Olyan HTTP-művelet hozzáadása, amely akkor fut le, amikor új vagy módosított blobot észlel. Válassza az **+ új lépés** elemet, majd keresse meg és válassza ki a http-műveletet.

  > [!div class="mx-imgBorder"]
  > :::image type="content" source="media/how-to-trigger-published-pipeline/search-http.png" alt-text="HTTP-művelet keresése":::

  A művelet konfigurálásához használja a következő beállításokat:

  | Beállítás | Érték | 
  |---|---|
  | HTTP-művelet | POST |
  | URI |az [előfeltételként](#prerequisites) megtalált közzétett folyamat végpontja |
  | Hitelesítési módszer | Felügyelt identitás |

1. Állítsa be úgy az ütemtervet, hogy beállítsa bármely [DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) értékét:

    ```json
    {
      "DataPathAssignments": {
        "input_datapath": {
          "DataStoreName": "<datastore-name>",
          "RelativePath": "@{triggerBody()?['Name']}" 
        }
      },
      "ExperimentName": "MyRestPipeline",
      "ParameterAssignments": {
        "input_string": "sample_string3"
      },
      "RunSource": "SDK"
    }
    ```

    `DataStoreName` [Előfeltételként](#prerequisites)adja hozzá a munkaterülethez hozzáadott feltételt.
     
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/http-settings.png" alt-text="HTTP-beállítások":::

1. Válassza a **Mentés** lehetőséget, és most már készen áll az ütemtervre.

> [!IMPORTANT]
> Ha Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) használ a folyamathoz való hozzáférés kezelésére, [állítsa be a folyamathoz tartozó forgatókönyv (képzés vagy pontozás) engedélyeit](how-to-assign-roles.md#common-scenarios).

## <a name="call-machine-learning-pipelines-from-azure-data-factory-pipelines"></a>Gépi tanulási folyamatok meghívása Azure Data Factory folyamatokból

Egy Azure Data Factory folyamat során a *Machine learning folyamat végrehajtása* tevékenység egy Azure Machine learning folyamatot futtat. Ez a tevékenység a Data Factory szerzői lapján található *Machine learning* kategóriában:

:::image type="content" source="media/how-to-trigger-published-pipeline/azure-data-factory-pipeline-activity.png" alt-text="A Azure Data Factory authoring Environment ML-feldolgozási tevékenységét bemutató képernyőkép":::

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a Pythonhoz készült Azure Machine Learning SDK-val két különböző módon ütemezhet egy folyamatot. Az egyik ütemezett ismétlődés az eltelt idő alapján. A másik ütemterv akkor fut, ha egy fájlt módosítanak egy adott `Datastore` vagy a tároló egyik könyvtárán belül. Látta, hogyan használhatja a portált a folyamat és az egyes futtatások vizsgálatára. Megtanulta, hogyan tilthatja le az ütemtervet, hogy a folyamat lefusson. Végül létrehozott egy Azure Logic app-alkalmazást egy folyamat elindításához. 

További információkért lásd:

> [!div class="nextstepaction"]
> [Azure Machine Learning folyamatok használata a Batch pontozáshoz](tutorial-pipeline-batch-scoring-classification.md)

* További információ a [folyamatokról](concept-ml-pipelines.md)
* További információ a [Azure Machine learning Jupyter való feltárásáról](samples-notebooks.md)