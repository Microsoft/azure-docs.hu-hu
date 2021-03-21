---
title: Durable Functions-Azure-beli feladat-hubok
description: Megtudhatja, mi a Task hub a Azure Functions Durable Functions-bővítményében. Útmutató a feladatok hubok konfigurálásához.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009517"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) feladat-hubok

A [Durable Functionsban](durable-functions-overview.md) található egyik *feladat* egy olyan Azure Storage-erőforrások logikai tárolója, amelyeket a rendszer az előkészítéshez használ. A Orchestrator és a Activity függvények csak akkor kommunikálhatnak egymással, ha ugyanahhoz a feladathoz tartozó hubhoz tartoznak.

Ha több Function-alkalmazás is osztozik egy Storage-fiókkal, minden egyes Function *-alkalmazást külön* feladat-központtal kell konfigurálni. A Storage-fiókok több feladatot tartalmazó hubokat is tartalmazhatnak. A következő diagram egy Function hub-t ábrázol a megosztott és a dedikált Storage-fiókokban.

![A megosztott és dedikált Storage-fiókokat bemutató diagram.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage-erőforrások

A feladatok központja a következő tárolási erőforrásokból áll:

* Egy vagy több vezérlő-várólista.
* Egy munkaelem-várólista.
* Egy előzményi tábla.
* Egy példány tábla.
* Egy vagy több bérleti blobot tartalmazó tároló.
* Nagyméretű üzenet-adattartalomot tartalmazó tárolási tároló, ha van ilyen.

Az összes ilyen erőforrás automatikusan létrejön az alapértelmezett Azure Storage-fiókban, ha a Orchestrator, az entitás vagy a tevékenység funkció fut, vagy ütemezve van a futtatásra. A [teljesítmény-és méretezési](durable-functions-perf-and-scale.md) cikk elmagyarázza, hogyan használják ezeket az erőforrásokat.

## <a name="task-hub-names"></a>Task hub-nevek

A feladatok hubokat egy olyan név azonosítja, amely megfelel a következő szabályoknak:

* Csak alfanumerikus karaktereket tartalmaz
* Betűvel kezdődik
* Legalább 3 karakterből áll, maximális hossza 45 karakter.

A feladat központi neve a *host.js* fájlon van deklarálva, az alábbi példában látható módon:

### <a name="hostjson-functions-20"></a>host.jsbekapcsolva (functions 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.jsbekapcsolva (functions 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

A feladatok hubok az Alkalmazásbeállítások használatával is konfigurálhatók, ahogy az az alábbi példában is látható `host.json` :

### <a name="hostjson-functions-10"></a>host.jsbekapcsolva (functions 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.jsbekapcsolva (functions 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

A feladat hub neve az Alkalmazásbeállítás értékére lesz állítva `MyTaskHub` . Az alábbi `local.settings.json` útmutató bemutatja, hogyan határozhatja meg a beállítást a következő `MyTaskHub` módon `samplehubname` :

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

A következő kód azt mutatja be, hogyan írhat olyan függvényt, amely a beállítási [ügyfél kötését](durable-functions-bindings.md#orchestration-client) használja egy olyan feladattal való együttműködéshez, amely alkalmazás-beállításként van konfigurálva:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Az előző C# példa a Durable Functions 2. x. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A fájl Task hub tulajdonsága az `function.json` alkalmazás beállításain keresztül van beállítva:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

A fájl Task hub tulajdonsága az `function.json` alkalmazás beállításain keresztül van beállítva:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

A feladat-hub nevének betűvel kell kezdődnie, és csak betűkből és számokból állhat. Ha nincs megadva, a rendszer a következő táblázatban látható módon fogja használni az alapértelmezett feladat-hub nevet:

| Tartós bővítmény verziója | Alapértelmezett Task hub-név |
| - | - |
| 2. x | Az Azure-ban való üzembe helyezéskor a feladat hub neve a _Function alkalmazás_ nevéből származik. Az Azure-on kívül futtatva az alapértelmezett feladat hub-neve: `TestHubName` . |
| 1. x | Az összes környezet alapértelmezett feladatának központi neve `DurableFunctionsHub` . |

A bővítmény-verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

> [!NOTE]
> A név az, ami megkülönbözteti az egyik feladatot a másiktól, ha egy megosztott Storage-fiókban több Task hub található. Ha több Function-alkalmazás osztozik egy megosztott Storage-fiókkal, explicit módon konfigurálnia kell az egyes feladatok központjának különböző nevét a fájlok *host.js* . Ellenkező esetben a több függvényt használó alkalmazások versenyeznek egymással az üzeneteknél, ami nem definiált viselkedést eredményezhet, beleértve az előkészítést, amely váratlanul "ragadt" a `Pending` vagy `Running` állapotban.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan kezelheti a hangszerelési verziószámozást](durable-functions-versioning.md)
