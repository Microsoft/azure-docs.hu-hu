---
title: A Azure Functions jelző szolgáltatás triggerének kötése
description: Útmutató a szignáló szolgáltatás üzeneteinek küldéséhez Azure Functionsról.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: 2482a26987ec142880acc51bf470d844655b6e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763513"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>A signaler szolgáltatás triggerének kötése Azure Functions

A *signaler* trigger-kötés használatával válaszolhat az Azure Signaler szolgáltatásból küldött üzenetekre. Ha a függvény aktiválódik, a függvénynek átadott üzenetek JSON-objektumként vannak értelmezve.

A Signaler Service kiszolgáló nélküli módban a Signaler szolgáltatás a [felsőbb rétegbeli](../azure-signalr/concept-upstream.md) szolgáltatással küld üzeneteket az ügyféltől a függvényalkalmazás. A és a függvényalkalmazás a Signaler szolgáltatás triggerének kötését használja az üzenetek kezelésére. Az általános architektúra alább látható: a :::image type="content" source="media/functions-bindings-signalr-service/signalr-trigger.png" alt-text="Signal trigger architektúrája":::

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-signalr-service.md).

## <a name="example"></a>Példa

Az alábbi példa egy olyan függvényt mutat be, amely az trigger kötés használatával üzenetet fogad, és naplózza az üzenetet.

# <a name="c"></a>[C#](#tab/csharp)

A signaler szolgáltatás triggerének kötése C#-ban két programozási modell van. Osztály alapú modell és hagyományos modell. Az osztályon alapuló modell egységes Signaler kiszolgálóoldali programozási élményt biztosíthat. A és a hagyományos modell nagyobb rugalmasságot és hasonló funkciókat biztosít más függvények kötésekhez.

### <a name="with-class-based-model"></a>Osztály alapú modellel

Részletekért lásd a [Class-alapú modellt](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) .

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>Hagyományos modellel

A hagyományos modell a C# által fejlesztett Azure Function egyezményt engedelmeskedik. Ha még nem ismeri, megismerheti a [dokumentumokat](./functions-dotnet-class-library.md).

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>Az attribútum használatának `[SignalRParameter]` egyszerűsítése `ParameterNames`

Mivel a használata nehézkes a használathoz `ParameterNames` , `SignalRParameter` ugyanezt a célt szolgálja.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

A C# szkript kódja:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Itt található a fájlban lévő *function.js* lévő kötési érték:

Példa function.jsa következőn:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Itt látható a Python-kód:

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Konfiguráció

### <a name="signalrtrigger"></a>SignalRTrigger

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `SignalRTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa**| n.a. | Értékre kell állítani `SignalRTrigger` .|
|**irányba**| n.a. | Értékre kell állítani `in` .|
|**név**| n.a. | A függvény kódjában használt változó neve a trigger meghívása környezeti objektumhoz. |
|**hubName**|**HubName**| Ezt az értéket az aktiválni kívánt függvény jelző központjajának nevére kell beállítani.|
|**Kategória**|**Kategória**| Ezt az értéket úgy kell beállítani, hogy az üzenetek kategóriája legyen az elindítható függvény számára. A kategória a következő értékek egyike lehet: <ul><li>**kapcsolatok**: a *csatlakoztatott* és a *leválasztott* eseményeket is beleértve</li><li>**üzenetek**: beleértve az összes többi eseményt, kivéve a *kapcsolatok* kategóriájában lévőket</li></ul> |
|**esemény**|**Esemény**| Ezt az értéket úgy kell beállítani, hogy a függvény elindítsa az üzenetek eseményét. Az *üzenetek* kategória esetében az esemény az a *cél* , amely az ügyfelek által küldött [Meghívási üzenetben](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) szerepel. A *kapcsolatok* kategória esetében csak a *csatlakoztatott* és a *leválasztott* kapcsolat van használatban. |
|**parameterNames**|**ParameterNames**| Választható A paraméterekhez kötődő nevek listája. |
|**connectionStringSetting**|**ConnectionStringSetting**| A jelző szolgáltatás kapcsolódási sztringjét tartalmazó Alkalmazásbeállítás neve (alapértelmezett érték: "AzureSignalRConnectionString") |

## <a name="payload"></a>Adattartalom

Az trigger típusú bemeneti típust `InvocationContext` vagy egyéni típusként deklaráljuk. Ha úgy dönt `InvocationContext` , hogy teljes hozzáférést kap a kérelem tartalmához. Egyéni típus esetén a futásidejű megpróbálja elemezni a JSON-kérés törzsét az objektum tulajdonságainak beállításához.

### <a name="invocationcontext"></a>InvocationContext

A InvocationContext tartalmazza az üzenetben a Signaler szolgáltatásból küldött összes tartalmat.

|Tulajdonság a InvocationContext | Leírás|
|------------------------------|------------|
|Argumentumok| Az *üzenetek* kategóriához érhető el. *Argumentumokat* tartalmaz a [Meghívási üzenetben](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)|
|Hiba| *Leválasztott* eseményhez elérhető. Ez akkor lehet üres, ha a kapcsolatok hiba nélkül lezárult, vagy a hibaüzeneteket tartalmazza.|
|Hub| A hub neve, amelyhez az üzenet tartozik.|
|Kategória| Az üzenet kategóriája.|
|Esemény| Az üzenet eseménye.|
|ConnectionId| Az üzenetet küldő ügyfél kapcsolatazonosító.|
|UserId (Felhasználóazonosító)| Az üzenetet küldő ügyfél felhasználói identitása.|
|Fejlécek| A kérelem fejlécei.|
|Lekérdezés| A kérés lekérdezése, amikor az ügyfelek csatlakoznak a szolgáltatáshoz.|
|Jogcímek| Az ügyfél jogcímei.|

## <a name="using-parameternames"></a>Az `ParameterNames` használata

A tulajdonsága `ParameterNames` `SignalRTrigger` lehetővé teszi, hogy a Meghívási üzenetek argumentumait a függvények paramétereinek megfelelően kösse. A megadott név más kötésben vagy a kódban szereplő paraméterekként is használható a [kötési kifejezések](../azure-functions/functions-bindings-expressions-patterns.md) részeként. Ez kényelmes módot biztosít az argumentumai eléréséhez `InvocationContext` .

Tegyük fel, hogy van egy JavaScript Signaler-ügyfél `broadcast` , amely két argumentummal próbálkozik az Azure-függvény metódusának meghívásához `message1` `message2` .

```javascript
await connection.invoke("broadcast", message1, message2);
```

A beállítás után `parameterNames` a megadott név az ügyfél oldalán elküldett argumentumoknak felel meg. 

```cs
[SignalRTrigger(parameterNames: new string[] {"arg1, arg2"})]
```

Ezután a `arg1` fogja tartalmazni a tartalmát `message1` , és `arg2` a tartalmait fogja tartalmazni `message2` .


### <a name="remarks"></a>Megjegyzések

A paraméter kötése esetén a sorrend számít. A használata esetén `ParameterNames` a sorrend `ParameterNames` megegyezik az ügyfélben meghívott argumentumok sorrendjével. Ha a C#-ban használja az attribútumot `[SignalRParameter]` , akkor az Azure Function metódusokban az argumentumok sorrendje megegyezik az ügyfelek argumentumai sorrendjével.

`ParameterNames` és az attribútum `[SignalRParameter]` **nem** használható egyszerre, vagy kivételt fog kapni.

## <a name="signalr-service-integration"></a>A signaler szolgáltatás integrációja

A signaler szolgáltatásnak szüksége van egy URL-címre a függvényalkalmazás eléréséhez, ha a jelző szolgáltatás triggerének kötését használja. Az URL-címet a jeladó szolgáltatás oldalán lévő **felsőbb rétegbeli beállításokban** kell konfigurálni. 

:::image type="content" source="../azure-signalr/media/concept-upstream/upstream-portal.png" alt-text="Felsőbb rétegbeli portál":::

A Signaler szolgáltatás triggerének használatakor az URL-cím egyszerű és formázható, ahogy az alábbi ábrán is látható:

```http
<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>
```

A `Function_App_URL` Függvényalkalmazás áttekintése oldalon található, `API_KEY` amelyet az Azure Function generál. A `API_KEY` from elemet `signalr_extension` a függvényalkalmazás **alkalmazás kulcsok** paneljén érheti el.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API-kulcs":::

Ha egynél több függvényalkalmazást szeretne használni egy Signaler-szolgáltatással együtt, az upstream is támogathatja az összetett útválasztási szabályokat. További részleteket a [felsőbb rétegbeli beállításokban](../azure-signalr/concept-upstream.md)talál.

## <a name="step-by-step-sample"></a>Lépésről lépésre minta

A GitHubon található minta követheti a csevegési helyiség üzembe helyezését függvényalkalmazás a Signal Service trigger kötésével és a felsőbb rétegbeli szolgáltatással: [kétirányú csevegési szoba minta](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)

## <a name="next-steps"></a>Következő lépések

* [Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással](../azure-signalr/signalr-concept-serverless-development-config.md)
* [A signaler szolgáltatás Triggerének kötési mintája](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
