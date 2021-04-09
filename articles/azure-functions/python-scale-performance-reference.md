---
title: A Python-alkalmazások átviteli teljesítményének növelése Azure Functions
description: Megtudhatja, hogyan fejleszthet Azure Functions alkalmazásokat a nagy teljesítményű és a terhelés alatt jól méretezhető Python használatával.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786106"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>A Python-alkalmazások átviteli teljesítményének növelése Azure Functions

A Python használatával történő Azure Functions fejlesztésekor ismernie kell a függvények működésének módját és azt, hogy a teljesítmény milyen hatással van a Function alkalmazás skálázására. Nagyon fontos a nagy teljesítményű alkalmazások tervezése. A functions-alkalmazások tervezése, írása és konfigurálása során megfontolandó szempontok a horizontális skálázás és az átviteli teljesítmény-konfigurációk.

## <a name="horizontal-scaling"></a>Horizontális skálázás
Alapértelmezés szerint a Azure Functions automatikusan figyeli az alkalmazás terhelését, és szükség esetén további gazdagép-példányokat hoz létre a Pythonhoz. Azure Functions a különböző trigger-típusok beépített küszöbértékeit használja annak eldöntésére, hogy mikor kell hozzáadni a példányokat, például az üzenetek korát és a várólista méretét a QueueTrigger. Ezek a küszöbértékek nem állíthatók be a felhasználó számára. További információ: [eseményvezérelt skálázás Azure Functionsban](event-driven-scaling.md).

## <a name="improving-throughput-performance"></a>Az átviteli teljesítmény javítása

Az alapértelmezett konfigurációk a legtöbb Azure Functions alkalmazáshoz megfelelőek. Az alkalmazások teljesítményének növelését azonban a munkaterhelés-profilon alapuló konfigurációk alkalmazásával növelheti. Első lépésként ismernie kell a futtatott számítási feladatok típusát.

| Munkaterhelés típusa | Function alkalmazás jellemzői       | Példák                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| **I/O-kötés**     | • Az alkalmazásnak számos egyidejű hívást kell kezelnie.<br>• Az alkalmazás nagy mennyiségű I/O-eseményt dolgoz fel, például hálózati hívásokat és lemezes olvasási/írási műveleteket. | • Webes API-k                                          |
| **CPU-kötésű**     | • Az alkalmazás hosszan futó számításokat végez, például a képek átméretezését.<br>• Az alkalmazás adatátalakítást végez.                                                | • Adatfeldolgozás<br>• Gépi tanulás – következtetés<br> |

 
Mivel a valós működést kihasználó számítási feladatok általában az I/O-és a CPU-kötések kombinációja, az alkalmazást reális üzemi terhelésnek kell megjelennie.


### <a name="performance-specific-configurations"></a>Teljesítmény-specifikus konfigurációk

A Function alkalmazás munkaterhelés-profiljának megismerése után a következő konfigurációkat használhatja a függvények teljesítményének növeléséhez.

* [Aszinkron](#async)
* [Több nyelvi dolgozó](#use-multiple-language-worker-processes)
* [Munkavégző folyamaton belüli maximális feldolgozók](#set-up-max-workers-within-a-language-worker-process)
* [Esemény hurok](#managing-event-loop)
* [Vertikális skálázás](#vertical-scaling)



#### <a name="async"></a>Aszinkron

Mivel a [Python egy egyszálas futtatókörnyezet](https://wiki.python.org/moin/GlobalInterpreterLock), a Python gazdagép-példánya alapértelmezés szerint egyszerre csak egy függvény hívását képes feldolgozni. A nagy mennyiségű I/O-eseményt feldolgozó alkalmazások és/vagy I/O-kötések esetében a függvények aszinkron futtatásával növelheti a teljesítményt.

A függvények aszinkron futtatásához használja az `async def` utasítást, amely a függvényt a [asyncio](https://docs.python.org/3/library/asyncio.html) közvetlenül futtatja:

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Íme egy példa a HTTP-triggert használó függvényre, amely [aiohttp](https://pypi.org/project/aiohttp/) http-ügyfelet használ:

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


A kulcsszó nélküli függvény `async` automatikusan fut egy ThreadPoolExecutor szál-készletben:

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

Ahhoz, hogy a függvények aszinkron módon elérhetők legyenek, a kódban használt I/O műveletnek/könyvtárnak is aszinkron módon kell megvalósítani. A szinkron I/O-műveletek használata az aszinkron módon definiált függvényeknél **megsértheti** a teljes teljesítményt. Ha a használt kódtárak nem rendelkeznek aszinkron verzióval, akkor továbbra is kihasználhatja a kód aszinkron futtatását az alkalmazásban az [Event loop kezelésével](#managing-event-loop) . 

Íme néhány példa arra, hogy az ügyféloldali kódtárak implementálták az aszinkron mintát:
- [aiohttp](https://pypi.org/project/aiohttp/) – http-ügyfél/-kiszolgáló asyncio 
- [Streams API](https://docs.python.org/3/library/asyncio-stream.html) – magas szintű aszinkron/várakozásra kész primitívek a hálózati kapcsolatban való együttműködéshez
- [Janus üzenetsor](https://pypi.org/project/janus/) – Thread-Safe asyncio-kompatibilis üzenetsor a Pythonhoz
- [pyzmq](https://pypi.org/project/pyzmq/) – Python-kötések a ZeroMQ
 
##### <a name="understanding-async-in-python-worker"></a>Az aszinkron ismertetése a Python Worker szolgáltatásban

A `async` függvények aláírása előtt a Python a függvényt egy rutinként jelöli meg. Az egyazon rutin meghívásakor feladatként ütemezhető egy esemény hurokba. Ha `await` aszinkron függvényt hív meg, akkor a folytatást regisztrálja az esemény hurokban, és lehetővé teszi az Event loop számára a következő feladat feldolgozását a várakozási idő alatt.

Python-feldolgozónk esetében a feldolgozó megosztja az Event loopot az ügyfél `async` funkciójával, és képes egyszerre több kérést kezelni. Javasoljuk ügyfeleinknek, hogy a asyncio-kompatibilis kódtárakat használják (például [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/)). A javaslatok alkalmazása nagy mértékben növeli a függvény átviteli sebességét a szinkron módon megvalósított könyvtárakhoz képest.

> [!NOTE]
>  Ha a függvény nem a `async` `await` megvalósításon belül van deklarálva, a függvény teljesítménye súlyosan hatással lesz, mivel az esemény hurok le lesz tiltva, ami tiltja a Python-feldolgozó számára az egyidejű kérelmek kezelését.

#### <a name="use-multiple-language-worker-processes"></a>Több nyelvet használó munkavégző folyamat használata

Alapértelmezés szerint minden functions Host-példány egyetlen nyelvi munkavégző folyamattal rendelkezik. A [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) alkalmazás beállításával növelheti a munkavégző folyamatok számát a gazdagépen (legfeljebb 10). Azure Functions ezt követően megpróbál egyenletesen terjeszteni egyidejű függvényeket a feldolgozók között.

A CPU-kötésű alkalmazások esetében állítsa be, hogy a nyelvi feldolgozó hány vagy annál nagyobb legyen, mint a Function app által elérhető magok száma. További információ: [elérhető példány SKU](functions-premium-plan.md#available-instance-skus)-i. 

Az I/O-kötésű alkalmazások is kihasználhatják a munkavégző folyamatok számának növelését az elérhető magok száma után. Ne feledje, hogy a feldolgozók számának beállítása túl nagy hatással lehet az általános teljesítményre a szükséges környezeti kapcsolók megnövekedett száma miatt. 

A FUNCTIONS_WORKER_PROCESS_COUNT minden olyan gazdagépre vonatkozik, amelyet a functions hoz létre, amikor az alkalmazás az igények kielégítése érdekében felskálázást végez.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>Maximális feldolgozók beállítása egy nyelvi munkavégző folyamaton belül

Ahogy azt az aszinkron [szakaszban](#understanding-async-in-python-worker)is említettük, a Python nyelvi feldolgozó másképp kezeli a függvényeket és a különböző [eljárásokat](https://docs.python.org/3/library/asyncio-task.html#coroutines) . Az egyazon hurokon belül fut egy közös rutin, amelyen a nyelvi feldolgozó fut. Másfelől a függvény meghívása egy [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor)belül fut, amelyet a nyelvi feldolgozó a szálként tart fenn.

Megadhatja a szinkronizálási függvények futtatásához engedélyezett maximális feldolgozók értékét a [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) Alkalmazásbeállítás használatával. Ez az érték beállítja a `max_worker` ThreadPoolExecutor objektum argumentumát, amely lehetővé teszi, hogy a Python a legtöbb szálból álló készletet használja `max_worker` aszinkron módon a hívások végrehajtásához. A `PYTHON_THREADPOOL_THREAD_COUNT` minden olyan feldolgozóra vonatkozik, amelyik a gazdagépet hozza létre, és a Python dönti el, hogy mikor hozzon létre új szálat, vagy használja újra a meglévő tétlen A régebbi Python-verziók (azaz, `3.8` , `3.7` és) esetében az `3.6` `max_worker` érték 1. Python-verzió esetén a értéke a következő: `3.9` `max_worker` `None` .

A CPU-kötésű alkalmazások esetében a beállítást alacsony értékre kell állítani, 1-től kezdődően és a számítási feladattal való kísérletezéssel. Ez a javaslat a környezeti kapcsolókra fordított idő csökkentése, valamint a PROCESSZORral kötött feladatok befejezésének engedélyezése.

Az I/O-kötésű alkalmazások esetében jelentős nyereségeket kell látnia az egyes meghívásokon dolgozó szálak számának növelésével. Javasoljuk, hogy kezdje a Python alapértelmezésével – a magok száma + 4, majd a csípés a megjelenő átviteli értékek alapján.

A vegyes számítási feladatokhoz tartozó alkalmazások esetében egyensúlyt kell kiosztania `FUNCTIONS_WORKER_PROCESS_COUNT` a két és `PYTHON_THREADPOOL_THREAD_COUNT` a konfiguráció között az átviteli sebesség maximalizálása érdekében. Annak megismeréséhez, hogy a Function apps hogyan költi el a legtöbb időt, javasoljuk, hogy a profilokat és az értékeket az aktuális viselkedésnek megfelelően állítsa be. Tekintse át ezt a [szakaszt](#use-multiple-language-worker-processes) FUNCTIONS_WORKER_PROCESS_COUNT Alkalmazásbeállítások megismeréséhez.

> [!NOTE]
>  Habár ezek a javaslatok a HTTP-és a nem HTTP-alapú aktivált függvényekre is érvényesek, előfordulhat, hogy a nem HTTP-alapú aktivált függvények más trigger-specifikus konfigurációit is módosítania kell, hogy a függvény alkalmazásaiban a várt teljesítményt kapja meg. Erről további információt ebben a [cikkben](functions-best-practices.md)találhat.


#### <a name="managing-event-loop"></a>Az Event loop kezelése

Használjon asyncio-kompatibilis, külső gyártótól származó kódtárakat. Ha a harmadik féltől származó kódtárak egyike sem felel meg az igényeinek, az Azure Functionsban is kezelheti az esemény-hurkokat. Az Event Loops kezelése nagyobb rugalmasságot biztosít a számítási erőforrások kezelésében, és lehetővé teszi a szinkron I/O-kódtárak összekapcsolását is.

Számos hasznos Python hivatalos dokumentum van, amely a beépített **asyncio** könyvtár használatával tárgyalja a munkafolyamatokat [és a feladatokat](https://docs.python.org/3/library/asyncio-task.html) , valamint az [esemény hurokait](https://docs.python.org/3.8/library/asyncio-eventloop.html) .

Tegyük fel, hogy a következő [kérések](https://github.com/psf/requests) könyvtára példaként, ez a kódrészlet a **asyncio** könyvtárat használja a `requests.get()` metódus egy rutinba való becsomagolásához, több webes kérelem SAMPLE_URL párhuzamosan történő futtatásához.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>Vertikális skálázás
Ha több feldolgozási egységre van szüksége, különösen a CPU-kötésű műveletekben, akkor lehetséges, hogy a prémium csomagra való frissítéssel magasabb specifikációkat kell megszereznie. A magasabb feldolgozási egységek esetében a munkavégző folyamatok számának beállítása a rendelkezésre álló magok számának és a magasabb fokú párhuzamosságnak megfelelően állítható be. 

## <a name="next-steps"></a>Következő lépések

Azure Functions Python-fejlesztéssel kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Azure Functions Python fejlesztői útmutató](functions-reference-python.md)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)

