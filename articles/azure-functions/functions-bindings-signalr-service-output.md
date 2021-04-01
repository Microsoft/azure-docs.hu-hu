---
title: Azure Functions szignáló szolgáltatás kimeneti kötése
description: Ismerkedjen meg a Signaler szolgáltatás kimeneti kötéseivel Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 159df4c228f74bb605c745e899d99bfff2aa511b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763285"
---
# <a name="signalr-service-output-binding-for-azure-functions"></a>A signaler szolgáltatás kimeneti kötése Azure Functions

A *signaler* kimeneti kötés használatával küldjön egy vagy több üzenetet az Azure Signaler szolgáltatással. A következő üzenetet küldheti el:

- Minden csatlakoztatott ügyfél
- Megadott felhasználóhoz hitelesített csatlakoztatott ügyfelek

A kimeneti kötés a csoportok kezelését is lehetővé teszi.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-signalr-service.md).

## <a name="broadcast-to-all-clients"></a>Közvetítés az összes ügyfél számára

Az alábbi példa egy olyan függvényt mutat be, amely az összes csatlakoztatott ügyfél kimeneti kötését használva üzenetet küld. A *cél* az egyes ügyfeleken meghívott metódus neve. Az *argumentumok* tulajdonság az ügyfél metódusának átadandó nulla vagy több objektum tömbje.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="python"></a>[Python](#tab/python)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
  "type": "signalR",
  "name": "outMessage",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a Python-kód:

```python
def main(req: func.HttpRequest, outMessage: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    outMessage.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="java"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

## <a name="send-to-a-user"></a>Küldés felhasználó számára

Csak a felhasználó számára hitelesített kapcsolatokra küldhet üzenetet, ha beállítja a *felhasználói azonosítót* a jelző üzenetben.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Példa function.jsa következőn:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Példa function.jsa következőn:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="python"></a>[Python](#tab/python)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
  "type": "signalR",
  "name": "outMessage",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a Python-kód:

```python
def main(req: func.HttpRequest, outMessage: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    outMessage.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="java"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

## <a name="send-to-a-group"></a>Küldés egy csoportba

Az üzenetet csak olyan kapcsolatokhoz küldheti, amelyeket a rendszer hozzáad egy csoporthoz a *csoport nevének* a jelző üzenetben való beállításával.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Példa function.jsa következőn:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Példa function.jsa következőn:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="python"></a>[Python](#tab/python)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
  "type": "signalR",
  "name": "outMessage",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Itt látható a Python-kód:

```python
def main(req: func.HttpRequest, outMessage: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    outMessage.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="java"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

## <a name="group-management"></a>Csoportkezelés

A signaler szolgáltatás lehetővé teszi a felhasználóknak a csoportokba való felvételét. Ezután üzeneteket küldhetnek egy csoportnak. A `SignalR` kimeneti kötés használatával kezelheti a felhasználók csoportjának tagságát.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa egy felhasználót távolít el egy csoportból.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> A megfelelő kötés eléréséhez `ClaimsPrincipal` konfigurálnia kell a hitelesítési beállításokat a Azure Functionsban.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

Példa *function.jsbekapcsolva*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Futtassa a. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa egy felhasználót távolít el egy csoportból.

Példa *function.jsbekapcsolva*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Futtassa a. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> A megfelelő kötés eléréséhez `ClaimsPrincipal` konfigurálnia kell a hitelesítési beállításokat a Azure Functionsban.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

Példa *function.jsbekapcsolva*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa egy felhasználót távolít el egy csoportból.

Példa *function.jsbekapcsolva*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

# <a name="python"></a>[Python](#tab/python)

### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

Példa *function.jsbekapcsolva*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init.py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa egy felhasználót távolít el egy csoportból.

Példa *function.jsbekapcsolva*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init.py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="java"></a>[Java](#tab/java)

### <a name="add-user-to-a-group"></a>Felhasználó felvétele egy csoportba

A következő példa egy felhasználót egy csoportba helyez.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

### <a name="remove-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

A következő példa egy felhasználót távolít el egy csoportból.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

---

## <a name="configuration"></a>Konfiguráció

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `SignalRConnectionInfo` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa**| n.a. | Értékre kell állítani `signalRConnectionInfo` .|
|**irányba**| n.a. | Értékre kell állítani `in` .|
|**név**| n.a. | A kapcsolatkérelem-objektumhoz tartozó függvény kódjában használt változó neve. |
|**hubName**|**HubName**| Ezt az értéket annak a jelző-hubhoz a nevére kell beállítani, amelyhez a kapcsolódási adatok létrejöttek.|
|**userId**|**UserId**| Nem kötelező: a hozzáférési kulcs jogkivonatában beállítani kívánt felhasználói azonosító jogcím értéke. |
|**connectionStringSetting**|**ConnectionStringSetting**| A jelző szolgáltatás kapcsolódási sztringjét tartalmazó Alkalmazásbeállítás neve (alapértelmezett érték: "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `SignalR` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa**| n.a. | Értékre kell állítani `signalR` .|
|**irányba**| n.a. | Értékre kell állítani `out` .|
|**név**| n.a. | A kapcsolatkérelem-objektumhoz tartozó függvény kódjában használt változó neve. |
|**hubName**|**HubName**| Ezt az értéket annak a jelző-hubhoz a nevére kell beállítani, amelyhez a kapcsolódási adatok létrejöttek.|
|**connectionStringSetting**|**ConnectionStringSetting**| A jelző szolgáltatás kapcsolódási sztringjét tartalmazó Alkalmazásbeállítás neve (alapértelmezett érték: "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Következő lépések

- [A Signaler szolgáltatás üzeneteinek kezelése (trigger kötése)](./functions-bindings-signalr-service-trigger.md)
- [A szolgáltatási végpont URL-címének és hozzáférési jogkivonatának visszaadása (bemeneti kötés)](./functions-bindings-signalr-service-input.md)
