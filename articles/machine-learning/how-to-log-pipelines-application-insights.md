---
title: Figyelő & a folyamat naplófájljainak összegyűjtése
titleSuffix: Azure Machine Learning
description: Adja hozzá a naplózást a képzéshez és a Batch-pontozási folyamatokhoz, és tekintse meg a naplózott eredményeket Application Insightsban.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7ed27eba66b3d18bed8017934fce85928b961392
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520047"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>A gépi tanulási folyamat naplófájljainak összegyűjtése a riasztások és a hibakeresés Application Insights


A [OpenCensus](https://opencensus.io/quickstart/python/) Python Library segítségével átirányíthatja a naplókat a parancsfájlokból Application Insights. Az egyetlen helyen futtatott naplók összesítése lehetővé teszi a lekérdezések és a problémák diagnosztizálását. A Application Insights használata lehetővé teszi a naplók időbeli nyomon követését és a folyamat-naplók összehasonlítását a futtatások között.

Ha a naplók a helyükön vannak, a kivételek és a hibaüzenetek előzményeit fogja tartalmazni. Mivel Application Insights integrálható az Azure-riasztásokkal, Application Insights lekérdezések alapján is létrehozhat riasztásokat.

## <a name="prerequisites"></a>Előfeltételek

* Kövesse a [Azure Machine learning](./how-to-manage-workspace.md) munkaterület létrehozásához és [az első folyamat létrehozásához](./how-to-create-machine-learning-pipelines.md) szükséges lépéseket.
* [Állítsa be a fejlesztési környezetet](./how-to-configure-environment.md) az Azure Machine learning SDK telepítéséhez.
* Telepítse a [OpenCensus Azure monitor-exportáló](https://pypi.org/project/opencensus-ext-azure/) csomagot helyileg:
  ```python
  pip install opencensus-ext-azure
  ```
* Application Insights- [példány](../azure-monitor/app/opencensus-python.md) létrehozása (ez a dokumentum az erőforráshoz tartozó kapcsolatok karakterláncának beolvasásáról is tartalmaz információt)

## <a name="getting-started"></a>Első lépések

Ez a szakasz a OpenCensus egy Azure Machine Learning folyamatból történő használatával kapcsolatos bevezetés. Részletes oktatóanyagért lásd: [OpenCensus Azure monitor-exportőrök](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Adjon hozzá egy PythonScriptStep az Azure ML-adatcsatornához. Konfigurálja a [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) a opencensus-ext-Azure függőségével. Konfigurálja a `APPLICATIONINSIGHTS_CONNECTION_STRING` környezeti változót.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Hozzon létre egy `sample_step.py` nevű fájlt. Importálja a AzureLogHandler osztályt a naplók Application Insightsba való átirányításához. Emellett importálnia kell a Python naplózási könyvtárát is.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Ezután adja hozzá a AzureLogHandler a Python-naplózó.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Naplózás egyéni dimenziókkal
 
Alapértelmezés szerint a Application Insights továbbított naplók nem rendelkeznek elegendő környezettel a futtatáshoz vagy kísérlethez való visszatéréshez. Ahhoz, hogy a naplók végrehajthatók legyenek a problémák diagnosztizálásához, további mezőkre van szükség. 

A mezők hozzáadásához egyéni dimenziókat adhat hozzá, amelyekkel kontextust biztosíthat a naplófájlhoz. Az egyik példa az, amikor valaki több lépésben szeretné megtekinteni a naplókat ugyanabban a folyamat-futtatásban.

Az egyéni dimenziók a kulcs-érték (string, string) párokat tartalmazó szótárt alkotják. Ezután a rendszer elküldje a szótárt Application Insights és a lekérdezési eredményekben oszlopként jeleníti meg. Az egyes dimenziók [lekérdezési paraméterekként](#additional-helpful-queries)használhatók.

### <a name="helpful-context-to-include"></a>Hasznos környezet a következőkhöz:

| Mező                          | Indoklás/példa                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | A lekérdezheti a naplókat ugyanazzal a parent_run_idával, hogy az összes lépésnél több naplót is láthasson, ahelyett, hogy az egyes lépésekhez kellene fordulnia.                                        |
| step_id                        | Ugyanazzal a step_idekkel kérdezheti le a naplókat, és megtekintheti, hogy a probléma szűk hatókörrel történt-e az egyes lépésekhez.                                                        |
| step_name                      | Lekérdezheti a naplókat a lépés teljesítményének időbeli alakulásának megtekintéséhez. Emellett segít megkeresni a legutóbbi futtatások step_id a portál felhasználói felületének használata nélkül                                          |
| experiment_name                | Lekérdezéseket végezhet a naplók között a kísérlet teljesítményének időbeli alakulásával kapcsolatban. Emellett segít megkeresni a legutóbbi futtatások parent_run_id vagy step_id a portál felhasználói felületének használata nélkül                   |
| run_url                 | A közvetlenül a futtatási vizsgálathoz biztosít hivatkozást. |

**Egyéb hasznos mezők**

Ezek a mezők további programkód-kialakítást igényelhetnek, és nem a futtatási környezet által biztosítottak.

| Mező                   | Indoklás/példa                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Ha CI/CD-t használ a központi telepítéshez, ez a mező összekapcsolhatja a naplókat a kód azon verziójával, amely megadja a lépést és a folyamat logikáját. Ez a hivatkozás további segítséget nyújt a problémák diagnosztizálásához, illetve az adott jellemzőkkel rendelkező modellek azonosításához (log/metrikus értékek). |
| run_type                       | Megkülönböztetheti a különböző modellek típusát, vagy képzéseket és pontozást használhat. |

### <a name="creating-a-custom-dimensions-dictionary"></a>Egyéni dimenziók szótárának létrehozása

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", extra= {"custom_dimensions":custom_dimensions})
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus Python-naplózási szempontok

A OpenCensus AzureLogHandler a Python-naplók Application Insights való átirányítására szolgál. Ennek eredményeképpen a Python naplózási árnyalatait figyelembe kell venni. Egy naplózó létrehozásakor az alapértelmezett naplózási szinttel rendelkezik, és az adott szintnél nagyobb vagy azzal egyenlő naplókat fog megjeleníteni. A Python-naplózási funkciók használatának jó referenciája a [naplózási szakácskönyv](https://docs.python.org/3/howto/logging-cookbook.html).

A `APPLICATIONINSIGHTS_CONNECTION_STRING` OpenCensus könyvtárához a környezeti változó szükséges. Azt javasoljuk, hogy állítsa be ezt a környezeti változót ahelyett, hogy folyamat paraméterként adja át azt, hogy elkerülje az egyszerű szöveges kapcsolatok karakterláncának megadását.

## <a name="querying-logs-in-application-insights"></a>Naplók lekérdezése Application Insights

A Application Insights átirányított naplók a "nyomkövetés" vagy a "kivételek" alatt jelennek meg. Ügyeljen arra, hogy az időablakot úgy állítsa be, hogy a folyamat futtatását is tartalmazza.

![Application Insights lekérdezés eredménye](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

A Application Insights eredményében megjelenik a naplózási üzenet és a szint, a fájl elérési útja és a kód sorszáma. Emellett a benne foglalt egyéni dimenziók is megjelennek. Ebben a képen a customDimensions szótár az előző [kód mintájának](#creating-a-custom-dimensions-dictionary)kulcs/érték párokat jeleníti meg.

### <a name="additional-helpful-queries"></a>További hasznos lekérdezések

Az alábbi lekérdezések némelyike a "customDimensions. level" használatát használja. Ezek a súlyossági szintek annak a szintnek felelnek meg, amelyben a Python-napló eredetileg el lett küldve. További lekérdezési információk: [Azure monitor naplózási lekérdezések](/azure/data-explorer/kusto/query/).

| Használati eset                                                               | Lekérdezés                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Adott egyéni dimenzió (például "parent_run_id") eredményeinek naplózása | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Az elmúlt 7 nap során futó összes oktatóanyag eredményeinek naplózása                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Az elmúlt 7 nap severityLevel hibába tartozó eredményeinek naplózása              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Az elmúlt 7 napban severityLevel hibával rendelkező naplózási eredmények száma     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Következő lépések

Miután megtörtént a naplók beolvasása a Application Insights-példányban, a rendszer a lekérdezés eredményei alapján [Azure monitor riasztások](../azure-monitor/alerts/alerts-overview.md#what-you-can-alert-on) beállítására is használható.

A lekérdezéseket egy [Azure-irányítópulton](../azure-monitor/app/tutorial-app-dashboards.md#add-logs-query) is hozzáadhatja további információkhoz.