---
title: Egyéni kezelők Azure Functions
description: Megtudhatja, hogyan használhatja a Azure Functionst bármilyen nyelvi vagy futtatókörnyezeti verzióval.
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: dd112c74ea9f013a0e14bddd735060ddbf73c14e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578531"
---
# <a name="azure-functions-custom-handlers"></a>Egyéni kezelők Azure Functions

Minden functions alkalmazást egy nyelvspecifikus kezelő hajt végre. Habár a Azure Functions alapértelmezés szerint számos [nyelvi kezelőt](./supported-languages.md) támogat, vannak olyan esetek, amikor más nyelveket vagy futtatókörnyezeteket szeretne használni.

Az egyéni kezelők olyan egyszerű webkiszolgálók, amelyek eseményeket fogadnak a functions gazdagépről. Minden olyan nyelv, amely támogatja a HTTP primitíveket, egyéni kezelőt valósíthat meg.

Az egyéni kezelők a legmegfelelőbbek olyan helyzetekben, amikor a következőket kívánja használni:

- Olyan nyelven implementálhat Function alkalmazást, amely jelenleg nem támogatott, például Go vagy Rust.
- Egy olyan futtatókörnyezet implementálása, amely jelenleg nem támogatott, például DENO.

Az egyéni kezelők esetében az [Eseményindítók és a bemeneti és kimeneti kötések](./functions-triggers-bindings.md) is használhatók [bővítmények](./functions-bindings-register.md)segítségével.

Ismerkedjen meg Azure Functions egyéni kezelővel a [Go-ban és a Rustban](create-first-function-vs-code-other.md).

## <a name="overview"></a>Áttekintés

Az alábbi ábrán a functions gazdagép és egy egyéni kezelőként megvalósított webkiszolgáló közötti kapcsolat látható.

![Azure Functions egyéni kezelő – áttekintés](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. Minden esemény elindítja a functions gazdagépnek küldendő kérelmet. Az esemény a Azure Functions által támogatott eseményindító.
1. A functions gazdagép ezután kibocsátja a [kérések adattartalmát](#request-payload) a webkiszolgáló számára. A hasznos adatok az trigger és a bemeneti kötési adatokat, valamint a függvény egyéb metaadatait tartalmazzák.
1. A webkiszolgáló végrehajtja az egyes függvényeket, és visszaküldi a functions gazdagépnek a [Válasz adattartalmát](#response-payload) .
1. A functions-gazdagép adatokat továbbít a függvénynek a művelet kimeneti kötéseire való választól a feldolgozáshoz.

Az egyéni kezelőként megvalósított Azure Functions-alkalmazásoknak néhány konvenciónak megfelelően be kell állítaniuk a *host.jst*, *local.settings.js* és *function.jsa* fájlokon.

## <a name="application-structure"></a>Alkalmazás szerkezete

Az egyéni kezelők megvalósításához a következő szempontokat kell figyelembe vennie az alkalmazásban:

- Az alkalmazás gyökerében lévő fájl *host.js*
- Az alkalmazás gyökerében lévő fájl *local.settings.js*
- Az egyes függvényekhez tartozó fájl *function.js* (a függvény nevével egyező mappában)
- Egy webkiszolgálót futtató parancs, parancsfájl vagy végrehajtható fájl

Az alábbi ábrán látható, hogyan jelennek meg a fájlok a "MyQueueFunction" nevű függvény fájlrendszerében, valamint egy *handler.exe* nevű egyéni kezelő végrehajtható fájl.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>Konfiguráció

Az alkalmazás a fájlok *host.jsán* és *local.settings.jsn* keresztül van konfigurálva.

#### <a name="hostjson"></a>host.jsbekapcsolva

*host.json* megadja, hogy a functions szolgáltatás hová küldi a kérelmeket, ha a http-események feldolgozására képes webkiszolgálóra mutat.

Az egyéni kezelőt úgy kell beállítani, hogy a *host.jsa* fájlban konfigurálja, és részletesen ismerteti, hogyan futtathatja a webkiszolgálót a `customHandler` szakasz használatával.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

A `customHandler` szakasz a által meghatározott célra mutat `defaultExecutablePath` . A végrehajtási cél lehet egy parancs, végrehajtható fájl vagy fájl, amelyben a webkiszolgáló implementálva van.

A `arguments` tömb használatával bármely argumentumot átadhat a végrehajtható fájlnak. Az argumentumok a környezeti változók (Alkalmazásbeállítások) kiterjesztését támogatják a `%%` jelöléssel.

A végrehajtható fájl által használt munkakönyvtárat is módosíthatja `workingDirectory` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>Kötések támogatása

A standard triggerek és a bemeneti és kimeneti kötések is elérhetők a *host.js* fájlon található [bővítmény-csomagok](./functions-bindings-register.md) hivatkozásával.

#### <a name="localsettingsjson"></a>local.settings.json

*Alocal.settings.json* a Function app helyi futtatásakor használt Alkalmazásbeállítások meghatározása. Mivel a titkos kulcsokat tartalmazhatnak, *alocal.settings.jsbe* kell zárni a forrás-vezérlőelemből. Az Azure-ban Ehelyett használja az Alkalmazásbeállítások beállítást.

Egyéni kezelők esetén a értékeként `FUNCTIONS_WORKER_RUNTIME` `Custom` *local.settings.json* értékre kell állítani.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

### <a name="function-metadata"></a>Függvény metaadatainak

Egyéni kezelővel való használat esetén a tartalom *function.jsa* nem különbözik attól, hogy a függvényt Hogyan határozná meg más környezetekben. Az egyetlen követelmény, hogy a fájlokon *function.jsnak* egy nevű mappában kell lennie, hogy egyezzen a függvény nevével.

A következő *function.js* konfigurál egy olyan függvényt, amely üzenetsor-triggerrel és várólista-kimeneti kötéssel rendelkezik. Mivel ez egy *MyQueueFunction* nevű mappában található, egy *MyQueueFunction* nevű függvényt határoz meg.

**MyQueueFunction/function.jsbekapcsolva**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>Kérelem tartalma

Üzenetsor-üzenet érkezésekor a functions-gazdagép HTTP POST-kérelmet küld az egyéni kezelőnek a törzsben található hasznos adatokkal.

A következő kód egy példaként szolgáló kérelem hasznos adatait jelöli. Az adattartalom tartalmaz egy JSON-struktúrát két taggal: `Data` és `Metadata` .

A `Data` tag olyan kulcsokat tartalmaz, amelyek megfelelnek a bemeneti és az indító névnek a fájlban lévő *function.js* kötések tömbben meghatározottak szerint.

A `Metadata` tag [az eseményforrás által generált metaadatokat](./functions-bindings-expressions-patterns.md#trigger-metadata)tartalmaz.

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>Válasz hasznos adat

Az egyezmény szerint a függvények válaszai kulcs/érték párokként vannak formázva. A támogatott kulcsok a következők:

| <nobr>Hasznos adatok kulcsa</nobr>   | Adattípus | Megjegyzések                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | object    | A (z `bindings` ) *function.jsban* található tömb által meghatározott válasz értékeket tartalmazza.<br /><br />Ha például egy függvény egy "myQueueOutput" nevű üzenetsor-kimeneti kötéssel van konfigurálva, akkor `Outputs` egy nevű kulcsot tartalmaz `myQueueOutput` , amelyet az egyéni kezelő állít be a várólistára küldött üzenetekhez. |
| `Logs`        | array     | Az üzenetek megjelennek a függvények hívási naplóiban.<br /><br />Az Azure-ban futtatott üzenetek Application Insightsban jelennek meg. |
| `ReturnValue` | sztring    | Olyan válasz megadására szolgál, amikor a kimenet `$return` a fájl *function.js* van konfigurálva. |

Ez egy példa egy válasz hasznos adattartalomra.

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>Példák

Az egyéni kezelők bármilyen nyelven végrehajthatók, amely támogatja a HTTP-események fogadását. Az alábbi példák bemutatják, hogyan implementálhat egy egyéni kezelőt a go programozási nyelv használatával.

### <a name="function-with-bindings"></a>Függvény kötésekkel

Az ebben a példában megvalósított forgatókönyv egy nevű függvényt tartalmaz `order` , amely a `POST` termék megrendelését jelképező hasznos adatokat fogad el. Mivel a függvény elküld egy megrendelést, létrejön egy Queue Storage üzenet, és a rendszer egy HTTP-választ ad vissza.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>Implementálás

A *sorrend* nevű mappában a *function.js* fájl konfigurálja a http által aktivált függvényt.

**megrendelés/function.jsbekapcsolva**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Ez a függvény olyan http- [triggert használó függvényként](./functions-bindings-http-webhook-trigger.md) van meghatározva, amely [http-választ](./functions-bindings-http-webhook-output.md) ad vissza, és egy [üzenetsor-tárolási](./functions-bindings-storage-queue-output.md) üzenetet küld.

Az alkalmazás gyökerében a fájl *host.js* úgy van konfigurálva, hogy egy `handler.exe` ( `handler` Linux vagy MacOS) nevű végrehajtható fájlt futtasson.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

Ez a functions futtatókörnyezetnek elküldhető HTTP-kérelem.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

A functions futtatókörnyezet ezután a következő HTTP-kérelmet küldi el az egyéni kezelőnek:

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> A rendszer a hasznos adatok bizonyos részeit eltávolították a rövidség kedvéért.

*handler.exe* a webkiszolgálót futtató, lefordított go Custom Handler-kezelő program, amely válaszol a functions gazdagépen Meghívási kérelmekre.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

Ebben a példában az egyéni kezelő egy webkiszolgálót futtat a HTTP-események kezeléséhez, és úgy van beállítva, hogy a használatával figyelje a kérelmeket `FUNCTIONS_CUSTOMHANDLER_PORT` .

Annak ellenére, hogy a functions Host eredeti HTTP-kérelmet kapott a címen `/api/order` , a függvény nevével hívja meg az egyéni kezelőt (a mappa neve). Ebben a példában a függvény a következő elérési úton van definiálva: `/order` . A gazdagép egy HTTP-kérelmet küld az egyéni kezelőnek a következő elérési úton: `/order` .

Mivel a `POST` rendszer elküldi a kérelmeket a függvénynek, a trigger adatok és függvények metaadatai a HTTP-kérés törzsén keresztül érhetők el. Az eredeti HTTP-kérelem törzse az adattartalomban érhető el `Data.req.Body` .

A függvény válasza a kulcs/érték párokba van formázva, ahol a `Outputs` tag olyan JSON-értéket tartalmaz, amelyben a kulcsok egyeznek a *function.js* fájlban megadott kimenetekkel.

Ez egy példa arra a hasznos adattartalomra, amelyet a kezelő a functions gazdagépnek ad vissza.

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

Ha úgy állítja `message` be a kimenetet, hogy megegyezzen a kérelemből beérkező megrendelési adatokkal, a függvény az adatokat a konfigurált várólistára írja. A functions gazdagép a hívóhoz konfigurált HTTP-választ is visszaadja `res` .

### <a name="http-only-function"></a>Csak HTTP-függvény

A további kötések és kimenetek nélküli HTTP-triggerű függvények esetében előfordulhat, hogy a kezelőnek közvetlenül a HTTP-kérelemmel és a választal kell működnie az egyéni kezelői [kérelem](#request-payload) és a [Válasz](#response-payload) hasznos adatai helyett. Ez a viselkedés *host.js* konfigurálható a `enableForwardingHttpRequest` beállítás használatával.

> [!IMPORTANT]
> Az egyéni kezelők funkció elsődleges célja az, hogy olyan nyelveket és futtatókörnyezeteket engedélyezzen, amelyek jelenleg nem rendelkeznek az első osztályú támogatással a Azure Functions. Habár lehetséges, hogy a webalkalmazások egyéni kezelők használatával is futtathatók, Azure Functions nem szabványos fordított proxy. Néhány funkció, például a Response streaming, a HTTP/2 és a WebSockets nem érhető el. A HTTP-kérelem egyes összetevői, például bizonyos fejlécek és útvonalak korlátozottak lehetnek. Előfordulhat, hogy az [alkalmazás túlzott mértékben](event-driven-scaling.md#cold-start)is felmerül.
>
> Ezeknek a körülményeknek a megoldásához érdemes lehet a webalkalmazásait [Azure app Service](../app-service/overview.md)-on futtatni.

Az alábbi példa bemutatja, hogyan konfigurálhat egy HTTP által aktivált függvényt további kötések és kimenetek nélkül. Az ebben a példában megvalósított forgatókönyv egy nevű függvényt tartalmaz `hello` , amely a vagy a paramétert fogadja `GET` `POST` .

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>Implementálás

A *Hello* nevű mappában a *function.js* fájl konfigurálja a http által aktivált függvényt.

**Hello/function.js**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

A függvény a (z) és a kérelmek fogadására van konfigurálva, `GET` `POST` és az eredmény értékét egy nevű argumentummal kell megadnia `res` .

Az alkalmazás gyökerében a *host.jsa* fájl futtatására van konfigurálva, és a értékre `handler.exe` `enableForwardingHttpRequest` van állítva `true` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

Ha `enableForwardingHttpRequest` Igen `true` , a csak HTTP-alapú függvények viselkedése különbözik az alapértelmezett egyéni kezelők viselkedéstől az alábbi módokon:

* A HTTP-kérelem nem tartalmazza az egyéni kezelők [kérelem](#request-payload) adattartalmát. Ehelyett a functions Host meghívja a kezelőt az eredeti HTTP-kérelem másolatával.
* A functions gazdagép ugyanazzal az elérési úttal hívja meg a kezelőt, mint az eredeti kérelem, beleértve a lekérdezési karakterlánc paramétereit is.
* A functions gazdagép a kezelő HTTP-válaszának másolatát adja vissza az eredeti kérelemre adott válaszként.

A következő egy POST-kérelem a functions gazdagépre. A functions gazdagép ezt követően elküldi a kérés másolatát az egyéni kezelőnek ugyanazon az elérési úton.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

A fájlkezelő *. go* fájl egy webkiszolgálót és egy http-függvényt valósít meg.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

Ebben a példában az egyéni kezelő létrehoz egy webkiszolgálót a HTTP-események kezelésére, és úgy van beállítva, hogy a segítségével figyelje a kérelmeket `FUNCTIONS_CUSTOMHANDLER_PORT` .

`GET` a kérelmeket a rendszer egy sztring visszaadásával kezeli, és `POST` a kérelmekhez hozzáfér a kérés törzséhez.

A megrendelés függvény útvonala itt `/api/hello` ugyanaz, mint az eredeti kérelem.

>[!NOTE]
>A `FUNCTIONS_CUSTOMHANDLER_PORT` nem a függvény meghívásához használt nyilvános port. A functions gazdagép ezt a portot használja az egyéni kezelő meghívásához.

## <a name="deploying"></a>Telepítése

Minden Azure Functions üzemeltetési lehetőséghez egyéni kezelő helyezhető üzembe. Ha a kezelő az operációs rendszer vagy a platform függőségeinek (például nyelvi futtatókörnyezet) megadását igényli, előfordulhat, hogy [Egyéni tárolót](./functions-create-function-linux-custom-image.md)kell használnia.

Ha egyéni kezelők számára hoz létre egy Function-alkalmazást az Azure-ban, javasoljuk, hogy a .NET Core-t veremként válassza. A jövőben az egyéni kezelők "egyéni" verem lesz hozzáadva.

Ha Azure Functions Core Tools használatával szeretne egyéni kezelő alkalmazást üzembe helyezni, futtassa a következő parancsot.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> Győződjön meg arról, hogy az egyéni kezelő futtatásához szükséges összes fájl a mappában található, és a központi telepítésben szerepel. Ha az egyéni kezelő bináris végrehajtható fájl, vagy platform-specifikus függőségekkel rendelkezik, győződjön meg arról, hogy ezek a fájlok megfelelnek a cél telepítési platformnak.

## <a name="restrictions"></a>Korlátozások

- Az egyéni kezelő webkiszolgálónak 60 másodpercen belül el kell indulnia.

## <a name="samples"></a>Példák

Tekintse át az [Egyéni kezelő a GitHub](https://github.com/Azure-Samples/functions-custom-handlers) -tárházat, amely példákat mutat be a függvények különböző nyelveken való megvalósítására.

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

### <a name="trace-logging"></a>Nyomkövetés naplózása

Ha az egyéni kezelői folyamat nem indul el, vagy ha problémái vannak a functions gazdagépkel való kommunikációval, növelheti a Function alkalmazás naplózási szintjét, hogy `Trace` több diagnosztikai üzenetet tudjon látni a gazdagépről.

A Function alkalmazás alapértelmezett naplózási szintjének módosításához konfigurálja a (z `logLevel` `logging` ) *host.js* szakaszának beállítását.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

A functions gazdagép további naplófájlokat jelenít meg, beleértve az egyéni kezelő folyamatával kapcsolatos információkat is. A naplók segítségével vizsgálja meg az egyéni kezelői folyamat indításával vagy a függvények az egyéni kezelőben való meghívásával kapcsolatos problémákat.

Helyileg a naplók a konzolon lesznek kinyomtatva.

Az Azure-ban a naplófájlok megtekintéséhez [Application Insights nyomkövetést kell lekérdezni](analyze-telemetry-data.md#query-telemetry-data) . Ha az alkalmazás nagy mennyiségű naplót hoz létre, akkor a rendszer csak a naplóüzenetek egy részhalmazát továbbítja Application Insightsnak. A [mintavétel letiltása](configure-monitoring.md#configure-sampling) az összes üzenet naplózásának biztosításához.

### <a name="test-custom-handler-in-isolation"></a>Egyéni kezelő tesztelése elkülönítésben

Az egyéni kezelői alkalmazások egy webkiszolgálói folyamat, ezért hasznos lehet a saját és a tesztelési függvény [meghívásával](#request-payload) elindítani a teszteket egy olyan eszköz használatával, mint például a [curl](https://curl.haxx.se/) vagy a [Poster](https://www.postman.com/).

Ezt a stratégiát a CI/CD-folyamatokban is használhatja, hogy automatizált teszteket futtasson az egyéni kezelőn.

### <a name="execution-environment"></a>Végrehajtási környezet

Az egyéni kezelők ugyanabban a környezetben futnak, mint egy tipikus Azure Functions alkalmazás. Tesztelje a kezelőt annak biztosításához, hogy a környezet tartalmazza az összes futtatásához szükséges függőségeket. A további függőségeket igénylő alkalmazások esetében előfordulhat, hogy egy Azure Functions [prémium csomagon](functions-premium-plan.md)futtatott [Egyéni tároló-rendszerkép](functions-create-function-linux-custom-image.md) használatával kell futtatnia azokat.

### <a name="get-support"></a>Támogatás kérése

Ha segítségre van szüksége egy egyéni kezelővel rendelkező Function alkalmazáshoz, akkor a kérést rendszeres támogatási csatornákon keresztül küldheti el. Azonban az egyéni kezelői alkalmazások létrehozásához használt lehetséges nyelvek széles választéka miatt a támogatás nem korlátlan.

A támogatás akkor érhető el, ha a functions gazdagép problémákba ütközne az egyéni kezelő folyamatával. Az egyéni kezelői folyamat belső működésével kapcsolatos problémák, például a választott nyelven vagy keretrendszerrel kapcsolatos problémák esetén a támogatási csapatunk nem tud segítséget nyújtani ebben a kontextusban.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg egy Azure Functions alkalmazás létrehozásával a Go-ban vagy a Rustban az [Egyéni kezelők](create-first-function-vs-code-other.md)gyors üzembe helyezésével.
