---
title: 'Oktatóanyag: a "Helló világ!" futtatása Python-szkript'
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning Get-Started sorozat 2. része azt mutatja be, hogyan lehet beküldeni egy triviális "Helló világ!" Python-szkript a felhőbe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 20173c4ba02f53a526167a5a8e22bd0cedc85594
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393218"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Oktatóanyag: a "Helló világ!" futtatása Python-szkript (2. rész/4)

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Pythonhoz készült Azure Machine Learning SDK-t a "Hello World!" nevű Python beküldéséhez és futtatásához. parancsfájl.

Ez az oktatóanyag *egy négy részből álló oktatóanyag-Sorozat 2. része* , amelyben megismerheti a Azure Machine learning és az Azure-ban végzett feladatok-alapú gépi tanulási feladatok alapjait. Ez az oktatóanyag az [1. rész: a helyi gép beállítása Azure Machine Learninghoz című részben](tutorial-1st-experiment-sdk-setup-local.md)leírtak szerint épül fel.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * Hozzon létre és futtasson egy "Helló világ!" Python-szkript helyileg.
> * Hozzon létre egy Python-vezérlőt a "Helló világ!" elküldéséhez Azure Machine Learning.
> * Ismerje meg a Azure Machine Learning fogalmakat a vezérlő parancsfájlban.
> * A "Helló világ!" beküldése és futtatása parancsfájl.
> * A kód kimenetének megtekintése a felhőben.

## <a name="prerequisites"></a>Előfeltételek

- Az [1. rész](tutorial-1st-experiment-sdk-setup-local.md) befejezése, ha még nem rendelkezik Azure Machine learning munkaterülettel.
- A Python nyelv és a gépi tanulási munkafolyamatok bevezető ismerete.
- Helyi fejlesztési környezet, mint például a Visual Studio Code, a Jupyter vagy a Notebookshoz.
- Python (3,5-3,7-es verzió).

## <a name="create-and-run-a-python-script-locally"></a>Python-szkriptek helyi létrehozása és futtatása

Hozzon létre egy új alkönyvtárat `src` a könyvtár alatt a `tutorial` Azure Machine learning számítási fürtön futtatni kívánt kód tárolásához. A `src` alkönyvtárban hozza létre a `hello.py` Python-szkriptet:

```python
# src/hello.py
print("Hello world!")
```

A Project Directory-struktúra most a következőképpen fog kinézni:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>Parancsfájl helyi tesztelése

A kódot helyileg is futtathatja kedvenc IDE vagy egy terminál használatával. A kód helyi futtatásának előnye a kód interaktív hibakeresése.

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>Vezérlő parancsfájl létrehozása

A *vezérlési parancsfájlok* lehetővé teszik a szkript futtatását `hello.py` a felhőben. A vezérlő parancsfájl segítségével szabályozhatja, hogy a gépi tanulási kód hogyan és hol fusson.  

Az oktatóanyag könyvtárában hozzon létre egy új, nevű Python-fájlt, `03-run-hello.py` és másolja/illessze be a következő kódot a fájlba:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>A kód értelmezése

Itt látható a vezérlő parancsfájl működésének leírása:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      A [munkaterület](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) a Azure Machine learning munkaterülethez csatlakozik, hogy kommunikálni tudjon a Azure Machine learning erőforrásaival.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      A [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) egyszerű módszert kínál több Futtatás egyetlen névvel való rendszerezésére. Később láthatja, hogyan könnyíti meg a kísérletek a több tucat futtatás közötti mérőszámok összehasonlítását.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      A [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) becsomagolja a `hello.py` kódot, és átadja a munkaterületnek. Ahogy a neve is sugallja, ezt az osztályt használhatja annak _konfigurálásához_ , hogy a _parancsfájl_ hogyan _fusson_ Azure Machine learning. Azt is meghatározza, hogy a parancsfájl milyen számítási célt fog futni. Ebben a kódban a cél az a számítási fürt, amelyet a [beállítási oktatóanyagban](tutorial-1st-experiment-sdk-setup-local.md)hozott létre.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Elküldi a parancsfájlt. Ezt a beküldést [futtatásnak](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)nevezzük. A Futtatás egyetlen végrehajtást ágyaz be a kódjába. Egy futtatással figyelheti a parancsfájl előrehaladását, rögzítheti a kimenetet, elemezheti az eredményeket, megjelenítheti a metrikákat, és egyéb műveleteket is alkalmazhat.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        Az `run` objektum egy leírót biztosít a kód végrehajtásához. Figyelje a Azure Machine Learning Studio folyamatát a Python-szkriptből kinyomtatott URL-címmel.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>A kód elküldése és futtatása a felhőben

Futtassa a vezérlési parancsfájlt, amely viszont a `hello.py` [telepítési oktatóanyagban](tutorial-1st-experiment-sdk-setup-local.md)létrehozott számítási fürtön fut.

A legelső Futtatás 5-10 percet vesz igénybe. Ennek az az oka, hogy a következők történnek:

* A Docker-rendszerkép a felhőben van felépítve
* A számítási fürt 0 és 1 csomópont között van átméretezve
* A rendszer letölti a Docker-rendszerképet a számításba. 

A későbbi futtatások sokkal gyorsabbak (~ 15 másodperc), mivel a Docker-rendszerkép gyorsítótárazva van a számításban – ezt úgy tesztelheti, hogy az első futtatás befejezése után újraküldi az alábbi kódot.

```bash
python 03-run-hello.py
```

> [!TIP]
> Ha ezt a kódot futtatja, akkor a hitelesítési beállításokkal kapcsolatos információkért tekintse meg a [Kapcsolódás munkaterülethez](how-to-manage-workspace.md?tab=python#connect-multi-tenant) című témakört.

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a>A kód figyelése a felhőben a Studio használatával

A kimenet tartalmazni fog egy hivatkozást a studióhoz, amely a következőhöz hasonlóan néz ki: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` .

Kövesse a hivatkozást, és lépjen a **kimenetek és naplók** lapra. Itt látható egy `70_driver_log.txt` fájl, amely így néz ki:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

A 8. sorban a "Helló világ!" kifejezés látható kimeneti.

A `70_driver_log.txt` fájl egy futtatásból származó standard kimenetet tartalmaz. Ez a fájl akkor lehet hasznos, ha távoli futtatásokat végez a felhőben.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy egyszerű "Helló világ!" szkriptet, és futtatta azt az Azure-ban. Megismerte, hogyan csatlakozhat a Azure Machine Learning-munkaterülethez, létrehozhat egy kísérletet, és elküldheti a `hello.py` kódot a felhőbe.

A következő oktatóanyagban ezeket a betanulásokat úgy építheti be, hogy valami érdekesebb, mint a `print("Hello world!")` .

> [!div class="nextstepaction"]
> [Oktatóanyag: Modell betanítása](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Ha itt szeretné befejezni az oktatóanyag-sorozatot, és nem halad a következő lépéssel, ne felejtse el [törölni az erőforrásokat](tutorial-1st-experiment-bring-data.md#clean-up-resources).