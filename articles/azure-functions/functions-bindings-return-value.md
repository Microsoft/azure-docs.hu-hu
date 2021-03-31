---
title: Visszatérési érték használata egy Azure-függvényből
description: További információ a Azure Functions visszatérési értékeinek kezeléséről
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 58ee9b682bc97dc4044d811392cf4ff5b51a69fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491358"
---
# <a name="using-the-azure-function-return-value"></a>Az Azure Function Return értékének használata

Ez a cikk azt ismerteti, hogyan működnek a visszatérési értékek egy függvényen belül.

A visszatérési értékkel rendelkező nyelveken a függvény [kimeneti kötését](./functions-triggers-bindings.md#binding-direction) a visszatérési értékhez kötheti:

* Egy C# osztályú függvénytárban alkalmazza a kimeneti kötési attribútumot a metódus visszatérési értékére.
* A javában alkalmazza a kimeneti kötési jegyzetet a Function metódusra.
* Más nyelveken állítsa be `name` *function.js* tulajdonságát a következőre: `$return` .

Ha több kimeneti kötés is van, akkor a visszatérési értéket csak az egyikre használja.

A C# és C# parancsfájlokban az adatok kimeneti kötésbe való küldésének alternatív módjai a `out` paraméterek és a [gyűjtő objektumok](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="c"></a>[C#](#tab/csharp)

A következő C#-kód egy kimeneti kötés visszatérési értékét használja, amelyet egy aszinkron példa követ:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A következő kimeneti kötés szerepel a *function.js* fájlban:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Itt látható a C# szkript kódja, amelyet egy aszinkron példa követ:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F#](#tab/fsharp)

A következő kimeneti kötés szerepel a *function.js* fájlban:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Itt látható az F # kód:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A következő kimeneti kötés szerepel a *function.js* fájlban:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

A JavaScriptben a visszatérési érték a második paraméterben a következő `context.done` :

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

A következő kimeneti kötés szerepel a *function.js* fájlban:

```json
{
    "name": "Response",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{blobname}"
}
```

A következő PowerShell-kód egy http kimeneti kötés visszatérési értékét használja:

```powershell
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $blobname
    })
```

# <a name="python"></a>[Python](#tab/python)

A következő kimeneti kötés szerepel a *function.js* fájlban:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Itt látható a Python-kód:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

A következő Java-kód egy kimeneti kötés visszatérési értékét használja:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Functions kötési hibák kezelése](./functions-bindings-errors.md)
