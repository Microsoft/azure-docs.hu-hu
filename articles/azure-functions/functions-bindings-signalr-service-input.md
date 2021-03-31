---
title: Azure Functions szignáló szolgáltatás bemeneti kötése
description: Megtudhatja, hogyan adhat vissza a Signaler szolgáltatás végpontjának URL-címét és a hozzáférési tokent Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 3f3a99c83d4a18f3085419b91be947dd67f8eec4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763319"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>A signaler szolgáltatás bemeneti kötése Azure Functions

Ahhoz, hogy az ügyfél csatlakozni tudjanak az Azure Signaler szolgáltatáshoz, le kell kérnie a szolgáltatási végpont URL-címét és egy érvényes hozzáférési tokent. A *SignalRConnectionInfo* bemeneti kötése létrehozza a signaler szolgáltatás végpontjának URL-címét és a szolgáltatáshoz való kapcsolódáshoz használt érvényes jogkivonatot. Mivel a jogkivonat időkorlátja korlátozott, és egy adott felhasználó egy kapcsolathoz való hitelesítésére használható, ne gyorsítótárazza a tokent, vagy ossza meg az ügyfelek között. A kötést használó HTTP-triggereket az ügyfelek használhatják a kapcsolati adatok lekérésére.

Ha további információra van szükség arról, hogy ez a kötés hogyan használható a Signaler ügyféloldali SDK által felhasználható "egyeztetés" függvény létrehozásához, tekintse meg a következő témakört: [Azure functions fejlesztés és konfiguráció cikk](../azure-signalr/signalr-concept-serverless-development-config.md) a Signaler szolgáltatással kapcsolatos fogalmak dokumentációjában.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-signalr-service.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely a bemeneti kötés használatával szerzi be a Signal-kapcsolati adatokat, és visszaadja a HTTP protokollon keresztül.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A következő példa egy, a (z) *function.js* fájl és egy [C# parancsfájl-függvény](functions-reference-csharp.md) , amely a kötést használja a kapcsolati adatok visszaadásához.

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy, a (z) *function.js* fájlon belüli Signal kapcsolati adatbeviteli kötést mutat be, és egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötés használatával adja vissza a kapcsolódási adatokat.

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy, a (z) *function.js* fájlhoz tartozó jelző kapcsolati adatbeviteli kötést mutat be, és egy [Python-függvényt](functions-reference-python.md) , amely a kötés használatával adja vissza a kapcsolódási adatokat.

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Itt látható a Python-kód:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy [Java-függvényt](functions-reference-java.md) mutat be, amely a bemeneti kötés használatával szerzi be a jelző kapcsolati adatait, és visszaadja a HTTP protokollon keresztül.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>Hitelesített tokenek

Ha a függvényt egy hitelesített ügyfél indítja el, felhasználói azonosító jogcímet adhat hozzá a generált jogkivonathoz. [App Service hitelesítés](../app-service/overview-authentication-authorization.md)használatával egyszerűen adhat hozzá hitelesítést egy Function alkalmazáshoz.

App Service hitelesítés beállítja a nevű HTTP-fejléceket, `x-ms-client-principal-id` `x-ms-client-principal-name` amelyek tartalmazzák a hitelesített felhasználó ügyfél-azonosítóját és nevét.

# <a name="c"></a>[C#](#tab/csharp)

Megadhatja a `UserId` kötés tulajdonságát bármelyik fejléc értékére egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}` .

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Megadhatja a `userId` kötés tulajdonságát bármelyik fejléc értékére egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}` .

Példa function.jsa következőn:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Megadhatja a `userId` kötés tulajdonságát bármelyik fejléc értékére egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}` .

Példa function.jsa következőn:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Megadhatja a `userId` kötés tulajdonságát bármelyik fejléc értékére egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}` .

Példa function.jsa következőn:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Itt látható a Python-kód:

```python
def main(req: func.HttpRequest, connectionInfo: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfo,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Megadhatja a `userId` kötés tulajdonságát bármelyik fejléc értékére egy [kötési kifejezés](./functions-bindings-expressions-patterns.md)használatával: `{headers.x-ms-client-principal-id}` vagy `{headers.x-ms-client-principal-name}` .

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>Következő lépések

- [A Signaler szolgáltatás üzeneteinek kezelése (trigger kötése)](./functions-bindings-signalr-service-trigger.md)
- [Szignáló szolgáltatás üzeneteinek küldése (kimeneti kötés)](./functions-bindings-signalr-service-output.md) 
