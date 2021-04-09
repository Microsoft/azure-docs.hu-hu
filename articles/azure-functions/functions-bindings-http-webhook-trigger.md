---
title: HTTP-trigger Azure Functions
description: Megtudhatja, hogyan hívhat meg egy Azure-függvényt HTTP-n keresztül.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4550be7ae8c543eea1bdfa085db6f23fe668a121
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025696"
---
# <a name="azure-functions-http-trigger"></a>HTTP-trigger Azure Functions

A HTTP-trigger lehetővé teszi a függvények HTTP-kérelemmel való meghívását. HTTP-trigger használatával kiszolgáló nélküli API-kat hozhat létre, és válaszolhat a webhookokra.

A HTTP által aktivált függvények alapértelmezett visszatérési értéke a következő:

- `HTTP 204 No Content` üres törzstel a 2. x és újabb függvényeknél
- `HTTP 200 OK` üres törzstel az 1. x függvényben

A HTTP-válasz módosításához állítson be egy [kimeneti kötést](./functions-bindings-http-webhook-output.md).

További információ a HTTP-kötésekről: [Áttekintés](./functions-bindings-http-webhook.md) és [kimeneti kötési hivatkozás](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy `name` paramétert keres a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében. Figyelje meg, hogy a rendszer a visszaadott értéket használja a kimeneti kötéshez, de a visszatérési érték attribútum nem kötelező.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy trigger kötést mutat be egy *function.jsa* fájlban, és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény a `name` paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében keresi.

A fájl *function.js* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Az itt található C#-szkript a következőhöz kötődik `HttpRequest` :

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

A helyett egyéni objektumhoz is köthető `HttpRequest` . Ez az objektum a kérelem törzsében jön létre, és JSON-ként van elemezve. Hasonlóképpen egy típus is átadható a HTTP-válasz kimeneti kötésnek, és a válasz törzsként, valamint egy állapotkódot is visszaküldhető `200` .

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="java"></a>[Java](#tab/java)

* [Paraméter olvasása a lekérdezési karakterláncból](#read-parameter-from-the-query-string)
* [Szövegtörzs beolvasása POST-kérelemből](#read-body-from-a-post-request)
* [Paraméter olvasása útvonalból](#read-parameter-from-a-route)
* [POJO-törzs olvasása POST-kérelemből](#read-pojo-body-from-a-post-request)

A következő példák a HTTP-trigger kötését mutatják be.

#### <a name="read-parameter-from-the-query-string"></a>Paraméter olvasása a lekérdezési karakterláncból

Ez a példa egy nevű paramétert olvas `id` be a lekérdezési karakterláncból, és felhasználja az ügyfélnek visszaadott JSON-dokumentum létrehozásához `application/json` .

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>Szövegtörzs beolvasása POST-kérelemből

Ez a példa egy POST-kérelem törzsét olvassa be, `String` és felhasználja az ügyfélnek visszaadott JSON-dokumentum létrehozásához `application/json` .

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Paraméter olvasása útvonalból

Ez a példa egy kötelező paramétert (name `id` ) és egy opcionális paramétert olvas `name` be az útvonal elérési útjából, és felhasználja őket az ügyfélnek visszaadott JSON-dokumentum tartalmának kiépítéséhez `application/json` . T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>POJO-törzs olvasása POST-kérelemből

Itt látható a következő `ToDoItem` példában hivatkozott osztály kódja:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Ez a példa egy POST-kérelem törzsét olvassa be. A kérés törzse automatikusan le lesz szerializálva egy `ToDoItem` objektumba, és a rendszer visszaadja az ügyfélnek a tartalomtípussal `application/json` . A (z `ToDoItem` ) paramétert a functions futtatókörnyezet szerializálja, mivel az `body` osztály tulajdonságához van rendelve `HttpMessageResponse.Builder` .

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy trigger kötést mutat be egy *function.jsa* fájlban, és egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény a `name` paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében keresi.

A fájl *function.js* :

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa egy trigger kötést mutat be egy *function.jsa* fájlban és egy [PowerShell-függvényben](functions-reference-node.md). A függvény a `name` paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében keresi.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

$body = "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."

if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body       = $body
})
```


# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy trigger kötést mutat be egy *function.jsa* fájlban, és egy [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény a `name` paramétert a lekérdezési karakterláncban vagy a HTTP-kérelem törzsében keresi.

A fájl *function.js* :

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "$return"
        }
    ]
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

A [C# osztály könyvtáraiban](functions-dotnet-class-library.md) és javában az `HttpTrigger` attribútum a függvény konfigurálására használható.

Beállíthatja az engedélyezési szintet és a HTTP-metódusokat az attribútumok konstruktorának paraméterei, a webhook típusa és egy útvonal-sablon számára. További információ ezekről a beállításokról: [konfiguráció](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Ez a példa bemutatja, hogyan használhatja a [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) attribútumot.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Teljes példaként tekintse meg az [trigger példáját](#example).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

Ez a példa bemutatja, hogyan használhatja a [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) attribútumot.

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Teljes példaként tekintse meg az [trigger példáját](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `HttpTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **típusa** | n.a.| Kötelező – a következőre kell beállítani: `httpTrigger` . |
| **irányba** | n.a.| Kötelező – a következőre kell beállítani: `in` . |
| **név** | n.a.| Kötelező – a kérelem vagy a kérelem törzse függvény kódjában használt változó neve. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Meghatározza, hogy a függvény meghívásához szükség van-e a kulcsokra, ha vannak ilyenek. Az engedélyezési szint az alábbi értékek egyike lehet: <ul><li><code>anonymous</code>&mdash;Nincs szükség API-kulcsra.</li><li><code>function</code>&mdash;Function-specifikus API-kulcs szükséges. Ez az alapértelmezett érték, ha nincs megadva.</li><li><code>admin</code>&mdash;A főkulcs megadása kötelező.</li></ul> További információt az [engedélyezési kulcsok](#authorization-keys)című szakaszban talál. |
| **módszerek** |**Metódusok** | A függvény által válaszoló HTTP-metódusok tömbje. Ha nincs megadva, a függvény az összes HTTP-metódusra válaszol. Lásd: [a http-végpont testreszabása](#customize-the-http-endpoint). |
| **route** | **Útvonal** | Meghatározza azt az útválasztási sablont, amely azt szabályozza, hogy a függvény milyen URL-címeket válaszol. Az alapértelmezett érték, ha nincs megadva `<functionname>` . További információ: [a http-végpont testreszabása](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Csak az 1. x verziójú futtatókörnyezet esetében támogatott._<br/><br/>Konfigurálja a HTTP-triggert, amely [webhook](https://en.wikipedia.org/wiki/Webhook) -fogadóként működik a megadott szolgáltatónál. `methods`Ha ezt a tulajdonságot beállítja, ne állítsa be a tulajdonságot. A webhook típusa a következő értékek egyike lehet:<ul><li><code>genericJson</code>&mdash;Egy általános célú webhook-végpont egy adott szolgáltató logikája nélkül. Ez a beállítás csak a HTTP POST és a tartalomtípus használatával korlátozza a kérelmeket `application/json` .</li><li><code>github</code>&mdash;A függvény válaszol a [GitHub-webhookokra](https://developer.github.com/webhooks/). Ne használja a  _authLevel_ tulajdonságot GitHub-webhookokkal. További információt a cikk későbbi, a GitHub-webhookok című szakaszában talál.</li><li><code>slack</code>&mdash;A függvény válaszol a [Slack webhookokra](https://api.slack.com/outgoing-webhooks). Ne használja a _authLevel_ tulajdonságot Slack webhookokkal. További információt a cikk későbbi, a Slack webhookok című szakaszában talál.</li></ul>|

## <a name="payload"></a>Adattartalom

Az trigger típusú bemeneti típust `HttpRequest` vagy egyéni típusként deklaráljuk. Ha úgy dönt `HttpRequest` , teljes hozzáférést kap a kérelem objektumhoz. Egyéni típus esetén a futásidejű megpróbálja elemezni a JSON-kérés törzsét az objektum tulajdonságainak beállításához.

## <a name="customize-the-http-endpoint"></a>A HTTP-végpont testreszabása

Alapértelmezés szerint a HTTP-triggerekhez tartozó függvény létrehozásakor a függvény címezhető az űrlap egy útvonalával:

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

Ezt az útvonalat a `route` http-trigger bemeneti kötésének opcionális tulajdonságával is testreszabhatja. A fájl következő *function.js* például a `route` http-triggerek tulajdonságát definiálja:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Ennek a konfigurációnak a használatával a függvény mostantól a következő útvonalon érhető el az eredeti útvonal helyett.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Ez a konfiguráció lehetővé teszi, hogy a függvény programkódja támogassa a címben, a _kategóriában_ és az _azonosítóban_ szereplő két paramétert. További információ arról, hogy az útválasztási paraméterek hogyan legyenek jogkivonatban az URL-címekben: [útválasztás ASP.net Coreban](/aspnet/core/fundamentals/routing#route-constraint-reference).

# <a name="c"></a>[C#](#tab/csharp)

A paraméterekkel bármilyen [webes API Route-korlátozást](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) használhat. A következő C#-függvény kód mindkét paraméter használatát teszi lehetővé.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A paraméterekkel bármilyen [webes API Route-korlátozást](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) használhat. A következő C#-függvény kód mindkét paraméter használatát teszi lehetővé.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="java"></a>[Java](#tab/java)

A függvény végrehajtási környezete az attribútumban deklarált tulajdonságok `HttpTrigger` . Az attribútum lehetővé teszi az útválasztási paraméterek, az engedélyezési szintek, a HTTP-műveletek és a bejövő kérelmek példányának definiálását.

Az útvonal paramétereinek meghatározása az `HttpTrigger` attribútumon keresztül történik.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A csomópontban a functions futtatókörnyezet biztosítja a kérelem törzsét az `context` objektumtól. További információ: [JavaScript trigger példa](#example).

Az alábbi példa bemutatja, hogyan olvashatja el az útválasztási paramétereket a alkalmazásból `context.bindingData` .

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A (z) *function.js* fájlon deklarált útvonal-paraméterek az objektum tulajdonsága számára érhetők el `$Request.Params` .

```powershell
$Category = $Request.Params.category
$Id = $Request.Params.id

$Message = "Category:" + $Category + ", ID: " + $Id

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $Message
})
```

# <a name="python"></a>[Python](#tab/python)

A függvény végrehajtási környezete egy deklarált paraméterrel van kitéve `func.HttpRequest` . Ez a példány lehetővé teszi a függvény számára az adatútvonal-paraméterek, a lekérdezési karakterlánc-értékek és a HTTP-válaszok visszaadását lehetővé tevő metódusok elérését.

A meghatározást követően az útvonal paraméterei elérhetők a függvény számára a metódus meghívásával `route_params` .

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

---

Alapértelmezés szerint az összes függvény útvonala az *API*-val előtaggal van ellátva. Az előtagot testre is szabhatja vagy eltávolíthatja a `extensions.http.routePrefix` [host.js](functions-host-json.md) fájljában található tulajdonság használatával. Az alábbi példa eltávolítja az *API* Route előtagot úgy, hogy az előtaghoz üres karakterláncot használ a fájl *host.js* .

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>Útvonal-paraméterek használata

A függvény mintáját definiáló útválasztási paraméterek `route` minden kötés számára elérhetők. Ha például egy útvonal van definiálva, akkor a `"route": "products/{id}"` tábla tárolási kötése használhatja a `{id}` paraméter értékét a kötési konfigurációban.

A következő konfiguráció azt mutatja be, hogyan adja `{id}` át a paramétert a kötésnek `rowKey` .

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

Ha útvonal-paramétereket használ, az `invoke_URL_template` automatikusan létrejön a függvényhez. Az ügyfelek az URL-címmel megtudhatják, hogy milyen paramétereket kell átadniuk az URL-címben, amikor a függvényt az URL-cím használatával hívja meg. Navigáljon a [Azure Portal](https://portal.azure.com) , majd válassza a **függvény URL-címének lekérése** lehetőséget a http által aktivált függvények egyikéhez.

Az Azure Resource Manager API-k használatával programozott módon férhet hozzá a `invoke_URL_template` [List](/rest/api/appservice/webapps/listfunctions) functions vagy a [Get függvényhez](/rest/api/appservice/webapps/getfunction).

## <a name="working-with-client-identities"></a>Ügyfél-identitások használata

Ha a Function alkalmazás [app Service hitelesítés/engedélyezés](../app-service/overview-authentication-authorization.md)funkciót használja, megtekintheti a kód alapján a hitelesített ügyfelek adatait. Ez [az információ a platform által befecskendezett kérelem fejlécként](../app-service/app-service-authentication-how-to.md#access-user-claims)érhető el.

Ezeket az információkat a kötési adatokból is elolvashatja. Ez a funkció csak a functions Runtime 2. x vagy újabb verziójában érhető el. Emellett jelenleg csak a .NET nyelveken érhető el.

# <a name="c"></a>[C#](#tab/csharp)

A hitelesített ügyfelekkel kapcsolatos információk [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)érhetők el. A ClaimsPrincipal a kérelem környezetének részeként érhető el, ahogy az az alábbi példában is látható:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Azt is megteheti, hogy a ClaimsPrincipal egyszerűen szerepelhet további paraméterként a függvény aláírása során:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A hitelesített ügyfelekkel kapcsolatos információk [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)érhetők el. A ClaimsPrincipal a kérelem környezetének részeként érhető el, ahogy az az alábbi példában is látható:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Azt is megteheti, hogy a ClaimsPrincipal egyszerűen szerepelhet további paraméterként a függvény aláírása során:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="java"></a>[Java](#tab/java)

A hitelesített felhasználó [http-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A hitelesített felhasználó [http-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A hitelesített felhasználó [http-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.

# <a name="python"></a>[Python](#tab/python)

A hitelesített felhasználó [http-fejléceken](../app-service/app-service-authentication-how-to.md#access-user-claims)keresztül érhető el.


---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>Függvény-hozzáférési kulcsok

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>Kulcsok beszerzése

A kulcsok tárolása az Azure-beli Function-alkalmazás részeként történik, és a rendszer a nyugalmi állapotban van titkosítva. Ha meg szeretné tekinteni a kulcsokat, újakat hoz létre, vagy új értékekre kell visszagörgetni a kulcsokat, navigáljon az egyik HTTP-triggert működtető függvényhez a [Azure Portal](https://portal.azure.com) , és válassza a **funkcióbillentyűk** elemet.

A gazdagép kulcsai is kezelhetők. Navigáljon a [Azure Portalban](https://portal.azure.com) található Function alkalmazásra, és válassza az **app Keys** elemet.

Az Azure Resource Manager API-k használatával programozott módon szerezheti be a függvényeket és a gazdagépek kulcsait. Vannak API-k a [funkcióbillentyűk](/rest/api/appservice/webapps/listfunctionkeys) listázásához és a [gazdagép kulcsainak](/rest/api/appservice/webapps/listhostkeys)listázásához, valamint az üzembe helyezési pontok használatakor az egyenértékű API-k a [kulcsok](/rest/api/appservice/webapps/listfunctionkeysslot) listázása és a [gazdagép kulcsainak](/rest/api/appservice/webapps/listhostkeysslot)listázása tárolóhely.

Az új függvényeket és a gazdagép kulcsait programozott módon is létrehozhatja a [create vagy az Update Function Secret](/rest/api/appservice/webapps/createorupdatefunctionsecret), a [create vagy a Update Function Secret tárolóhely](/rest/api/appservice/webapps/createorupdatefunctionsecretslot)használatával, a [gazdagép titkos](/rest/api/appservice/webapps/createorupdatehostsecret) kódjának létrehozásával vagy frissítésével, illetve a [gazdagép titkos tárolóhelyének API-jai létrehozásával vagy frissítésével](/rest/api/appservice/webapps/createorupdatehostsecretslot)

A függvény és a gazdagép kulcsa programozott módon törölhető a [delete Function Secret](/rest/api/appservice/webapps/deletefunctionsecret), a [Function Secret tárolóhely](/rest/api/appservice/webapps/deletefunctionsecretslot)törlése, az [állomás titkos](/rest/api/appservice/webapps/deletehostsecret)kódjának törlése és a [gazdagép titkos tárolóhelyének](/rest/api/appservice/webapps/deletehostsecretslot) API-k törlése használatával.

Az [örökölt kulcskezelő API-kkal is használhatja a funkcióbillentyűk beszerzését](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), de a Azure Resource Manager API-k használata ajánlott helyette.

## <a name="api-key-authorization"></a>API-kulcs engedélyezése

A legtöbb HTTP-trigger sablonhoz API-kulcs szükséges a kérelemben. Így a HTTP-kérelem általában a következő URL-címhez hasonlít:

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

A kulcs egy nevű lekérdezési karakterlánc-változóban is szerepelhet `code` , a fentiek szerint. Egy HTTP-fejlécben is szerepelhet `x-functions-key` . A kulcs értéke lehet a függvényhez definiált bármelyik funkcióbillentyű, vagy bármely gazdagép kulcsa.

Engedélyezheti a névtelen kérelmeket, amelyek nem igénylik a kulcsokat. Azt is megkövetelheti, hogy a főkulcs használatban legyen. Az alapértelmezett engedélyezési szintet a `authLevel` kötés JSON tulajdonságának használatával módosíthatja. További információ: [trigger-Configuration](#configuration).

> [!NOTE]
> A függvények helyi futtatásakor az engedélyezés le van tiltva a megadott engedélyezési szint beállításától függetlenül. Az Azure-ba való közzététel után a `authLevel` rendszer kikényszeríti az trigger beállítását. A kulcsokra továbbra is szükség van [, ha a tárolóban helyileg](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally)fut.


## <a name="secure-an-http-endpoint-in-production"></a>HTTP-végpont biztonságossá tétele éles környezetben

Az üzemi végpontok éles környezetben történő teljes biztonságossá tételéhez érdemes megfontolni a következő Function szintű biztonsági beállítások egyikének megvalósítását. Ha ezen alkalmazás-szintű biztonsági módszerek egyikét használja, állítsa be a HTTP által aktivált függvény engedélyezési szintjét a következőre: `anonymous` .

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>A Function alkalmazás üzembe helyezése elkülönítésben

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Webhookok

> [!NOTE]
> A webhook mód csak a functions futtatókörnyezet 1. x verziójához érhető el. Ez a változás a 2. x vagy újabb verziójú HTTP-eseményindítók teljesítményének javítása érdekében történt.

Az 1. x verzióban a webhook-sablonok további ellenőrzéseket biztosítanak a webhook hasznos adataihoz. A 2. x vagy újabb verzióban az alap HTTP-trigger továbbra is működik, és a webhookok ajánlott megközelítése. 

### <a name="github-webhooks"></a>GitHub-webhookok

A GitHub-webhookokra való reagáláshoz először hozza létre a függvényt egy HTTP-triggerrel, és állítsa be a **webHookType** tulajdonságot a következőre: `github` . Ezután másolja a saját URL-címét és API-kulcsát a GitHub-adattár **Hozzáadás webhook** lapjára. 

![Képernyőkép, amely bemutatja, hogyan adhat hozzá webhookot a függvényhez.](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Slack-webhookok

A Slack webhook létrehoz egy jogkivonatot, és nem adja meg az Ön által megadott értéket, ezért egy függvény-specifikus kulcsot kell konfigurálnia a biztonsági másolatból a Tartalékidőből. Lásd: [engedélyezési kulcsok](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhookok és kulcsok

A webhook-engedélyezést a webhook fogadó összetevője kezeli, amely a HTTP-trigger része, és a mechanizmus a webhook típusától függően változik. Mindegyik mechanizmus egy kulcsra támaszkodik. Alapértelmezés szerint a "default" nevű funkcióbillentyű van használatban. Ha másik kulcsot szeretne használni, konfigurálja úgy a webhook-szolgáltatót, hogy a következő módszerek egyikével küldje el a kulcs nevét a kérelemben:

* **Lekérdezési karakterlánc**: a szolgáltató átadja a kulcs nevét a `clientid` lekérdezési karakterlánc paraméterében (például `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>` ).
* **Kérelem fejléce**: a szolgáltató átadja a kulcs nevét a `x-functions-clientid` fejlécben.

## <a name="content-types"></a>Tartalomtípusok

A bináris és az űrlapadatok nem C # függvénybe való átadásához a megfelelő Content-type fejlécet kell használnia. A támogatott tartalomtípusok `octet-stream` a bináris adattípusok és a [többrészes típusok](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart)esetében is használhatók.

### <a name="known-issues"></a>Ismert problémák

A nem C # függvények esetében a Content-Type értékkel küldött kérések a `image/jpeg` `string` függvénynek átadott értéket eredményeznek. Ilyen esetekben az értéket manuálisan is átalakíthatja `string` egy byte-tömbre a nyers bináris értékek eléréséhez.

## <a name="limits"></a>Korlátok

A HTTP-kérés hossza legfeljebb 100 MB (104 857 600 bájt), az URL-cím hossza pedig 4 KB (4 096 bájt) lehet. Ezeket a korlátokat `httpRuntime` a futtatókörnyezet [Web.config fájljának](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config)eleme határozza meg.

Ha a HTTP-triggert használó függvény 230 másodpercen belül nem fejeződik be, akkor a [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) időtúllépést jelez, és http 502-hibát ad vissza. A függvény továbbra is futni fog, de nem tud HTTP-választ adni. A hosszú ideig futó függvények esetében javasoljuk, hogy kövesse az aszinkron mintákat, és olyan helyet küldjön vissza, amelyben pingelheti a kérés állapotát. További információ a függvények futtatásának időtartamáról: [skálázás és üzemeltetés – fogyasztási terv](functions-scale.md#timeout).


## <a name="next-steps"></a>Következő lépések

- [HTTP-válasz visszaadása függvényből](./functions-bindings-http-webhook-output.md)