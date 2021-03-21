---
title: Kiugró/ventilátor-forgatókönyvek Durable Functions-Azure-ban
description: Megtudhatja, hogyan valósítható meg a ventilátorok által kipróbálható forgatókönyv a Azure Functions Durable Functions-bővítményében.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 91128033696af6a56488db7991987f1e384b719e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027646"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-out/ventilátor – forgatókönyv Durable Functions – Felhőbeli biztonsági mentési példa

A *ventilátor ki-* és bekapcsolása a több függvény egyidejű végrehajtásával, majd az eredmények összesítésének végrehajtásával kapcsolatos mintázatra utal. Ez a cikk egy olyan mintát ismertet, amely [Durable Functionst](durable-functions-overview.md) használ egy ventilátor-vagy ventilátor-kijelentkezési forgatókönyv megvalósításához. A minta egy olyan tartós funkció, amely egy alkalmazás összes vagy néhány oldalának tartalmát az Azure Storage-ba készíti.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

Ebben a példában a függvények a megadott könyvtár összes fájlját feltöltik rekurzív módon a blob Storage-ba. A feltöltött bájtok teljes számát is megszámolják.

Egyetlen függvényt is írhat, amely mindenről gondoskodik. A futtatásának fő problémája a **méretezhetőség**. Egyetlen függvény végrehajtása csak egyetlen virtuális gépen futtatható, így az átviteli sebességet az adott virtuális gép átviteli sebessége fogja korlátozni. Egy másik probléma a **megbízhatóság**. Ha a művelet félúton, vagy ha a teljes folyamat 5 percnél hosszabb időt vesz igénybe, akkor a biztonsági mentés részben befejezett állapotba kerülhet. Ezt követően újra kell indítani.

A robusztusabb megközelítés két normál függvény írásához szükséges: az egyik a fájlok enumerálása és a fájlnevek hozzáadása egy várólistához, a másik pedig a várólistából, és a fájlok feltöltése a blob Storage-ba. Ez a megközelítés jobb az átviteli sebesség és a megbízhatóság szempontjából, de a várólista üzembe helyezéséhez és kezeléséhez szükséges. Ennél is fontosabb, hogy az **állami felügyelet** és a **koordináció** szempontjából jelentős bonyolultságot jelent, ha többet szeretne tenni, például a feltöltött bájtok teljes számát.

A Durable Functions megközelítéssel az összes említett előnyt nagyon alacsony terheléssel ruházhatja fel.

## <a name="the-functions"></a>A függvények

Ez a cikk a minta alkalmazás következő funkcióit ismerteti:

* `E2_BackupSiteContent`: Egy [Orchestrator-függvény](durable-functions-bindings.md#orchestration-trigger) , amely meghívja a `E2_GetFileList` biztonsági mentéshez szükséges fájlok listájának beszerzését, majd `E2_CopyFileToBlob` az egyes fájlok biztonsági mentését kéri.
* `E2_GetFileList`: Egy [tevékenység-függvény](durable-functions-bindings.md#activity-trigger) , amely egy könyvtár fájljainak listáját adja vissza.
* `E2_CopyFileToBlob`: Egy tevékenység-függvény, amely egyetlen fájlról készít biztonsági másolatot az Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent Orchestrator függvény

Ez a Orchestrator-függvény lényegében a következő műveleteket végzi el:

1. `rootDirectory`Bemeneti paraméterként egy értéket vesz fel.
2. Meghív egy függvényt a fájlok rekurzív listájának beolvasásához a alatt `rootDirectory` .
3. Több párhuzamos függvényt hív meg az egyes fájlok Azure Blob Storageba való feltöltéséhez.
4. Megvárja, amíg az összes feltöltés befejeződik.
5. Az Azure Blob Storageba feltöltött összes bájt összegét adja vissza.

# <a name="c"></a>[C#](#tab/csharp)

Itt látható a Orchestrator függvényt megvalósító kód:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Figyelje meg a `await Task.WhenAll(tasks);` sort. A függvény minden egyes hívása `E2_CopyFileToBlob` *nem* várt, ami lehetővé teszi, hogy párhuzamosan fussanak. Ha a feladatok tömbjét átadja a `Task.WhenAll` -nek, egy olyan feladatot kapunk vissza, amely nem fejeződött be, *amíg az összes másolási művelet* be nem fejeződik. Ha már ismeri a .NET-ben a feladatok párhuzamos függvénytárát (TPL), akkor ez nem új Önnek. A különbség az, hogy ezek a feladatok egyszerre több virtuális gépen is futhatnak, és a Durable Functions bővítmény biztosítja, hogy a végpontok közötti végrehajtás rugalmasan dolgozza fel az újrahasznosítás folyamatát.

A szolgáltatástól való várakozás után tudjuk, `Task.WhenAll` hogy az összes függvény hívása befejeződött, és visszaadott értékeket értünk vissza. Minden hívás, amely `E2_CopyFileToBlob` a feltöltött bájtok számát adja vissza, így a teljes bájtok számának kiszámítása az összes visszaadott érték együttes hozzáadására szolgál.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A függvény a Orchestrator functions standard *function.js* alkalmazza.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Itt látható a Orchestrator függvényt megvalósító kód:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Figyelje meg a `yield context.df.Task.all(tasks);` sort. A függvény összes egyedi hívása `E2_CopyFileToBlob` *nem* lett elindítva, ami lehetővé teszi, hogy párhuzamosan fussanak. Ha a feladatok tömbjét átadja a `context.df.Task.all` -nek, egy olyan feladatot kapunk vissza, amely nem fejeződött be, *amíg az összes másolási művelet* be nem fejeződik. Ha már ismeri a [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) JavaScriptet, akkor ez nem új Önnek. A különbség az, hogy ezek a feladatok egyszerre több virtuális gépen is futhatnak, és a Durable Functions bővítmény biztosítja, hogy a végpontok közötti végrehajtás rugalmasan dolgozza fel az újrahasznosítás folyamatát.

> [!NOTE]
> Habár a feladatok fogalmi hasonlóságot mutatnak a JavaScript-ígéretekhez, a Orchestrator függvényeknek a és a helyett a feladatok párhuzamos kell használniuk `context.df.Task.all` `context.df.Task.any` `Promise.all` `Promise.race` .

A rendszerből való kilépést követően tudjuk, `context.df.Task.all` hogy az összes függvény hívása befejeződött, és visszaadott értékeket értünk vissza. Minden hívás, amely `E2_CopyFileToBlob` a feltöltött bájtok számát adja vissza, így a teljes bájtok számának kiszámítása az összes visszaadott érték együttes hozzáadására szolgál.

# <a name="python"></a>[Python](#tab/python)

A függvény a Orchestrator functions standard *function.js* alkalmazza.

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/function.json)]

Itt látható a Orchestrator függvényt megvalósító kód:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/\_\_init\_\_.py)]

Figyelje meg a `yield context.task_all(tasks);` sort. A függvény összes egyedi hívása `E2_CopyFileToBlob` *nem* lett elindítva, ami lehetővé teszi, hogy párhuzamosan fussanak. Ha a feladatok tömbjét átadja a `context.task_all` -nek, egy olyan feladatot kapunk vissza, amely nem fejeződött be, *amíg az összes másolási művelet* be nem fejeződik. Ha már ismeri a [`asyncio.gather`](https://docs.python.org/3/library/asyncio-task.html#asyncio.gather) Pythont, akkor ez nem új Önnek. A különbség az, hogy ezek a feladatok egyszerre több virtuális gépen is futhatnak, és a Durable Functions bővítmény biztosítja, hogy a végpontok közötti végrehajtás rugalmasan dolgozza fel az újrahasznosítás folyamatát.

> [!NOTE]
> Habár a feladatok elméletileg hasonlók a Python-awaitables, a Orchestrator függvények, valamint `yield` a és az API-k is használhatók a `context.task_all` `context.task_any` feladat-párhuzamos kezeléséhez.

A rendszerből való kilépést követően tudjuk, `context.task_all` hogy az összes függvény hívása befejeződött, és visszaadott értékeket értünk vissza. Minden hívás a `E2_CopyFileToBlob` feltöltött bájtok számának visszaadására szolgál, így kiszámíthatja a teljes bájtok számát az összes visszatérési érték együttes hozzáadásával.

---

### <a name="helper-activity-functions"></a>Segítő tevékenység functions

A segítő tevékenység más mintákhoz hasonlóan csak olyan rendszeres függvények, amelyek az `activityTrigger` trigger kötését használják.

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList Activity függvény

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A fájl *function.js* a `E2_GetFileList` következőhöz hasonlóan néz ki:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

És itt a megvalósítás:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A függvény a `readdirp` modult (2. x verzió) használja a címtár struktúrájának rekurzív olvasásához.

# <a name="python"></a>[Python](#tab/python)

A fájl *function.js* a `E2_GetFileList` következőhöz hasonlóan néz ki:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/function.json)]

És itt a megvalósítás:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/\_\_init\_\_.py)]

---

> [!NOTE]
> Lehet, hogy kíváncsi, miért nem tudta közvetlenül a kódot a Orchestrator függvénybe helyezni. Előfordulhat, hogy ez a Orchestrator functions egyik alapvető szabálya, ami azt eredményezi, hogy soha nem végeznek I/O-t, beleértve a helyi fájlrendszer elérését. További információ: [Orchestrator](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob Activity függvény

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> A mintakód futtatásához telepítenie kell a `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet csomagot.

A függvény a Azure Functions kötések néhány speciális funkcióját (azaz a [ `Binder` paraméter](../functions-dotnet-class-library.md#binding-at-runtime)használatát) használja, de nem kell aggódnia a jelen útmutatóban szereplő adatokkal kapcsolatban.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A fájl *function.js* a következőhöz `E2_CopyFileToBlob` hasonlóan egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

A JavaScript-implementáció az [Azure Storage SDK](https://github.com/Azure/azure-storage-node) -t használja a csomóponthoz a fájlok Azure Blob Storageba való feltöltéséhez.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

# <a name="python"></a>[Python](#tab/python)

A fájl *function.js* a következőhöz `E2_CopyFileToBlob` hasonlóan egyszerű:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/function.json)]

A Python-implementáció a [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) -val tölti fel a fájlokat az Azure Blob Storageba.

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/\_\_init\_\_.py)]

---

A megvalósítás betölti a fájlt a lemezről, és aszinkron módon továbbítja a tartalmat egy azonos nevű blobba a "Backups" tárolóban. A visszaadott érték a tárterületre másolt bájtok száma, amelyet a Orchestrator függvény használ az összesített összeg kiszámításához.

> [!NOTE]
> Ez egy tökéletes példa arra, hogy az I/O-műveletek egy függvénybe legyenek áthelyezve `activityTrigger` . A munka nem csupán számos különböző gépen van elosztva, de a folyamat ellenőrzőpontjának előnyeit is kihasználhatja. Ha a gazdagép folyamata valamilyen okból leáll, tudja, hogy mely feltöltések lettek végrehajtva.

## <a name="run-the-sample"></a>Minta futtatása

A Windowson a következő HTTP POST-kérelem elküldésével indíthatja el a-előkészítést.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

Azt is megteheti, hogy Linux-függvényalkalmazás (a Python jelenleg csak Linux rendszeren fut a App Service) a következőhöz hasonló módon indítható el:

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"/home/site/wwwroot"
```

> [!NOTE]
> A meghívó `HttpStart` függvény csak JSON-formátumú tartalommal működik. Emiatt a `Content-Type: application/json` fejléc megadása kötelező, a könyvtár elérési útja pedig JSON-karakterláncként van kódolva. Emellett a HTTP-kódrészlet feltételezi, hogy van egy bejegyzés a `host.json` fájlban, amely eltávolítja az alapértelmezett `api/` előtagot a http-trigger összes funkciójának URL-címéről. A konfigurációhoz tartozó jelölést a `host.json` mintákban található fájlban találja.

Ez a HTTP-kérelem elindítja a `E2_BackupSiteContent` Orchestrator, és paraméterként továbbítja a karakterláncot `D:\home\LogFiles` . A válasz egy hivatkozást tartalmaz a biztonsági mentési művelet állapotának lekéréséhez:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

A függvény alkalmazásban található naplófájlok számától függően a művelet végrehajtása több percet is igénybe vehet. A legutóbbi állapotot a `Location` korábbi HTTP 202-válasz fejlécében lévő URL-cím lekérdezésével érheti el.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

Ebben az esetben a függvény még fut. Megtekintheti a Orchestrator-állapotba mentett bemenetet és az utolsó frissítés időpontját. Továbbra is használhatja a `Location` fejléc értékeit a lekérdezés befejezéséhez. Ha az állapot "befejezett", a következőhöz hasonló HTTP-válaszüzenet jelenik meg:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Most láthatja, hogy a folyamat elkészült, és körülbelül mennyi időt vett igénybe. Megtekintheti a mező értékét is `output` , amely azt jelzi, hogy a rendszer körülbelül 450 kb-ot töltött fel a naplókban.

## <a name="next-steps"></a>Következő lépések

Ez a példa azt mutatja, hogyan lehet megvalósítani a ventilátor-out/Fan-in mintát. A következő minta bemutatja, hogyan valósítható meg a figyelő minta [tartós időzítők](durable-functions-timers.md)használatával.

> [!div class="nextstepaction"]
> [A figyelő minta futtatása](durable-functions-monitor.md)