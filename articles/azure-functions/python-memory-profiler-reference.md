---
title: Memória-profilkészítés a Azure Functions Python-alkalmazásaiban
description: Ismerje meg a Python-alkalmazások memóriahasználat és a memória szűk keresztmetszetének azonosítását ismertető témakört.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: f47451809a2fa2b7d22805e6415056f19c4c1d88
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258809"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Profil Python-alkalmazások memóriahasználat a Azure Functions

A fejlesztés során vagy a helyi Python-függvény alkalmazás-projektnek az Azure-ba történő üzembe helyezése során célszerű elemezni a függvények potenciális memóriájának szűk keresztmetszeteit. Az ilyen szűk keresztmetszetek csökkenthetik a függvények teljesítményét, és hibákhoz vezethetnek. Az alábbi utasítások bemutatják, hogyan használhatja a [Memory-Profiler](https://pypi.org/project/memory-profiler) Python-csomagot, amely az általuk futtatott függvények soron belüli memória-felhasználási elemzését biztosítja.

> [!NOTE]
> A memória-profilkészítés csak a fejlesztési környezetek memória-lábnyomának elemzésére szolgál. Ne alkalmazza a Memory Profilert az üzemi függvények alkalmazásaira.

## <a name="prerequisites"></a>Előfeltételek

A Python-Function alkalmazás fejlesztésének megkezdése előtt meg kell felelnie a következő követelményeknek:

* [Python 3.6. x vagy újabb verzió](https://www.python.org/downloads/release/python-374/). Az Azure Functions-ban támogatott Python-verziók teljes listáját a [Python fejlesztői útmutatójában](functions-reference-python.md#python-version)tekintheti meg.

* A [Azure functions Core Tools](functions-run-local.md#v2) 3. x verzió.

* A [Visual Studio Code](https://code.visualstudio.com/) telepítve van az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>Memória-profilkészítési folyamat

1. A requirements.txt a Hozzáadás `memory-profiler` gombra kattintva győződjön meg arról, hogy a csomag a telepítéshez lesz csomagolva. Ha a helyi gépen fejleszti a fejlesztést, érdemes lehet [egy Python virtuális környezetet aktiválni](create-first-function-cli-python.md#create-venv) , és a csomag megoldását `pip install -r requirements.txt` .

2. A függvény parancsfájljában (általában \_ \_ \_ \_ az init. szúra) adja hozzá a következő sorokat a `main()` függvény fölé. Ezzel biztosíthatja, hogy a gyökérszintű naplózó jelentést készítsen a gyermekobjektumok neveiről, hogy a memória-profilkészítési naplók az előtag alapján megkülönböztethetők legyenek `memory_profiler_logs` .

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=profiler_logstream)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>Példa

Íme egy példa arra, hogyan végezhető el a memória-profilkészítés aszinkron és szinkron HTTP-triggereken, "HttpTriggerAsync" és "HttpTriggerSync" néven. Létrehozunk egy Python-függvény alkalmazást, amely egyszerűen elküldi a GET-kéréseket a Microsoft kezdőlapjának.

### <a name="create-a-python-function-app"></a>Python-függvény alkalmazásának létrehozása

A Python-függvény alkalmazásának Azure Functions megadott [mappastruktúrát](functions-reference-python.md#folder-structure)kell követnie. A projekt elindításához a Azure Functions Core Tools használatát javasoljuk a következő parancsok futtatásával:

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>Fájl tartalmának frissítése

A requirements.txt a projektben használt csomagokat határozza meg. A Azure Functions SDK és a memória-Profiler mellett bemutatjuk `aiohttp` az ASZINKRON http-kérelmeket és `requests` a szinkron http-hívásokat.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

Emellett újra kell írnia az aszinkron HTTP-triggert, `HttpTriggerAsync/__init__.py` és konfigurálnia kell a memória Profilert, a gyökérszintű naplózó formátumot és a naplózó adatfolyam-kötést.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

Szinkron HTTP-trigger esetén tekintse meg a következő `HttpTriggerSync/__init__.py` kódrészletet:

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>Profil Python Function alkalmazás helyi fejlesztési környezetben

A fenti módosítások elvégzése után néhány további lépés a Python virtuális envionment inicializálása Azure Functions futtatókörnyezethez.

1. A kívánt módon nyisson meg egy Windows PowerShellt vagy egy Linux-rendszerhéjat.
2. Python virtuális környezet létrehozása `py -m venv .venv` Windows vagy `python3 -m venv .venv` Linux rendszeren.
3. Aktiválja a Python-alapú környezetet a `.venv\Scripts\Activate.ps1` Windows PowerShellben vagy `source .venv/bin/activate` a Linux-rendszerhéjban.
4. A Python-függőségek visszaállítása a `pip install requirements.txt`
5. A Azure Functions futtatókörnyezet helyi elindítása Azure Functions Core Tools `func host start`
6. GET kérelem küldése a következőnek: `https://localhost:7071/api/HttpTriggerAsync` vagy `https://localhost:7071/api/HttpTriggerSync` .
7. A következőhöz hasonló memória-profilkészítési jelentést kell megjeleníteni Azure Functions Core Tools.

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>Következő lépések

Azure Functions Python-fejlesztéssel kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Azure Functions Python fejlesztői útmutató](functions-reference-python.md)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
