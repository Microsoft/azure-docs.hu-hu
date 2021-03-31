---
title: HTTP-API-k Durable Functions-Azure Functions
description: Megtudhatja, hogyan implementálhat HTTP API-kat a Azure Functions Durable Functions-bővítményében.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697843"
---
# <a name="http-api-reference"></a>HTTP API-referenciák

Az Durable Functions-bővítmény olyan beépített HTTP API-kat tesz elérhetővé, amelyek segítségével felügyeleti feladatokat hajthat végre az [előkészítési, az](durable-functions-types-features-overview.md#orchestrator-functions) [entitások](durable-functions-types-features-overview.md#entity-functions)és a [feladatok hubokon](durable-functions-task-hubs.md). Ezek a HTTP-API-k olyan bővíthetőségi webhookok, amelyek a Azure Functions gazdagép által engedélyezettek, de közvetlenül a Durable Functions bővítménnyel kezelhetők.

A bővítmény által megvalósított összes HTTP API-nak a következő paramétereket kell megadni. Az összes paraméter adattípusa `string` .

| Paraméter        | Paraméter típusa  | Description |
|------------------|-----------------|-------------|
| **`taskHub`**    | Lekérdezési sztring    | A [feladat hub](durable-functions-task-hubs.md)neve. Ha nincs megadva, a rendszer feltételezi, hogy az aktuális Function-alkalmazás feladatának hub-neve. |
| **`connection`** | Lekérdezési sztring    | A Storage-fiókhoz tartozó kapcsolatok karakterláncának **neve** . Ha nincs megadva, a rendszer a Function alkalmazás alapértelmezett kapcsolatok sztringjét feltételezi. |
| **`systemKey`**  | Lekérdezési sztring    | Az API meghívásához szükséges hitelesítési kulcs. |

`systemKey` a Azure Functions gazdagép által automatikusan generált engedélyezési kulcs. Kifejezetten hozzáférést biztosít a tartós feladathoz tartozó API-khoz, és ugyanúgy kezelhető, mint a [többi hitelesítési kulcs](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Olyan URL-címeket hozhat létre, amelyek a megfelelő `taskHub` , és lekérdezési karakterlánc-értékeket tartalmazzák a hanghangoló `connection` ügyfél- `systemKey` [kötési](durable-functions-bindings.md#orchestration-client) API-kkal, például a `CreateCheckStatusResponse` .net- `CreateHttpManagementPayload` ben és a `createCheckStatusResponse` `createHttpManagementPayload` JavaScriptben található API-kkal.

A következő néhány szakasz a bővítmény által támogatott HTTP API-kat fedi le, és példákat is tartalmaz a használatuk módjára.

## <a name="start-orchestration"></a>Előkészítés megkezdése

Elindítja a megadott Orchestrator függvény új példányának végrehajtását.

### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező              | Paramétertípus  | Leírás |
|--------------------|-----------------|-------------|
| **`functionName`** | URL-cím             | Az elindítani kívánt Orchestrator-függvény neve. |
| **`instanceId`**   | URL-cím             | Nem kötelező megadni. A koordináló példány azonosítója. Ha nincs megadva, a Orchestrator függvény véletlenszerűen kiválasztott AZONOSÍTÓJÚ példánnyal fog kezdődni. |
| **`{content}`**    | Tartalom kérése | Választható. A JSON-formátumú Orchestrator függvény bemenete. |

### <a name="response"></a>Reagálás

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (elfogadva)**: a megadott Orchestrator függvény futtatásának indítása ütemezve lett. A `Location` Válasz fejléce tartalmaz egy URL-címet, amely a hanghívási állapot lekérdezésére használható.
* **HTTP 400 (hibás kérés)**: a megadott Orchestrator függvény nem létezik, a megadott példány-azonosító érvénytelen, vagy a kérelem tartalma nem volt érvényes JSON.

Az alábbi példa egy Orchestrator-függvény indítására szolgáló kérelmet tartalmaz, amely `RestartVMs` JSON-objektumok hasznos adatait tartalmazza:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

A **HTTP 202** -esetek válasza egy JSON-objektum, amely a következő mezőket tartalmazhatja:

| Mező                       | Leírás                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |A koordináló példány azonosítója. |
| **`statusQueryGetUri`**     |A koordináló példány állapotának URL-címe |
| **`sendEventPostUri`**      |A hangolási példány "esemény előléptetése" URL-címe. |
| **`terminatePostUri`**      |A koordináló példány "leállítási" URL-címe. |
| **`purgeHistoryDeleteUri`** |A koordináló példány "törlési előzmények" URL-címe. |
| **`rewindPostUri`**         |előnézet A hangolási példány "Rewind" URL-címe. |

Az összes mező adattípusa `string` .

Az alábbi példa egy, a (z) AZONOSÍTÓval rendelkező összehangoló példány válaszának adattartalmát szemlélteti `abc123` (az olvashatóság érdekében formázva):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

A HTTP-válasz célja, hogy kompatibilis legyen a *lekérdezési fogyasztói mintával*. A következő jelentős válasz fejléceket is tartalmazza:

* **Hely**: az állapot VÉGPONTJÁNAK URL-címe. Ez az URL-cím a mezővel megegyező értéket tartalmaz `statusQueryGetUri` .
* **Újrapróbálkozás ennyi idő után**: ennyi másodpercig kell várni a lekérdezési műveletek között. Az alapértelmezett érték `10`.

Az aszinkron HTTP-lekérdezési mintával kapcsolatos további információkért tekintse meg az aszinkron http- [műveletek követése](durable-functions-http-features.md#async-operation-tracking) dokumentációját.

## <a name="get-instance-status"></a>Példány állapotának beolvasása

Egy megadott összehangoló példány állapotának beolvasása.

### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező                   | Paramétertípus  | Leírás |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL-cím             | A koordináló példány azonosítója. |
| **`showInput`**         | Lekérdezési sztring    | Nem kötelező megadni. Ha a értékre `false` van állítva, a függvény bemenete nem fog szerepelni a válasz adattartalomban.|
| **`showHistory`**       | Lekérdezési sztring    | Nem kötelező megadni. Ha a értékre `true` van állítva, a rendszer a hangfelvétel-végrehajtási előzményeket fogja tartalmazni a válasz adattartalomban.|
| **`showHistoryOutput`** | Lekérdezési sztring    | Nem kötelező megadni. Ha a értékre `true` van állítva, a függvény kimenetei szerepelni fognak a előkészítési végrehajtási előzményekben.|
| **`createdTimeFrom`**   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyegzőn vagy azt követően létrehozott visszaadott példányok listáját.|
| **`createdTimeTo`**     | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyegzőn vagy azt megelőzően létrehozott visszaadott példányok listáját.|
| **`runtimeStatus`**     | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, a visszaadott példányok listáját a futásidejű állapotuk alapján szűri. A lehetséges futásidejű állapotüzenetek listájának megtekintéséhez tekintse meg a [példányok lekérdezése](durable-functions-instance-management.md) című cikket. |

### <a name="response"></a>Reagálás

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 200 (ok)**: a megadott példány befejezett állapotban van.
* **HTTP 202 (elfogadva)**: a megadott példány folyamatban van.
* **HTTP 400 (hibás kérés)**: a megadott példány meghiúsult vagy leállt.
* **HTTP 404 (nem található)**: a megadott példány nem létezik, vagy nem indult el.
* **HTTP 500 (belső kiszolgálóhiba)**: a megadott példány nem kezelt kivétellel meghiúsult.

A **http 200** és a **http 202** esetek válaszának adattartalma egy JSON-objektum, amely a következő mezőket tartalmazhatja:

| Mező                 | Adattípus | Description |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | sztring    | A példány futtatókörnyezeti állapota. Az értékek közé tartoznak a következők: *Futtatás*, *függő*, *sikertelen*, *megszakított*, *leállított*, *befejezett*. |
| **`input`**           | JSON      | A példány inicializálásához használt JSON-adatbázis. Ez a mező akkor van `null` beállítva, ha a `showInput` lekérdezési karakterlánc paraméter értéke `false` .|
| **`customStatus`**    | JSON      | Az egyéni előkészítési állapothoz használt JSON-adatértékek. `null`Ha nincs beállítva ez a mező. |
| **`output`**          | JSON      | A példány JSON-kimenete. Ez a mező akkor áll fenn, `null` Ha a példány nem befejezett állapotban van. |
| **`createdTime`**     | sztring    | A példány létrehozásának időpontja. ISO 8601 kiterjesztett jelölést használ. |
| **`lastUpdatedTime`** | sztring    | Az az időpont, amikor a példányt utoljára megtartották. ISO 8601 kiterjesztett jelölést használ. |
| **`historyEvents`**   | JSON      | Egy JSON-tömb, amely a előkészítési végrehajtási előzményeket tartalmazza. Ez a mező `null` csak akkor van beállítva, ha a `showHistory` lekérdezési karakterlánc paraméter értéke `true` . |

Íme egy példa a válasz adattartalomra, beleértve a előkészítési végrehajtási előzményeket és a tevékenységek kimeneteit (az olvashatóság érdekében formázva):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

A **HTTP 202** válasza tartalmaz egy **Location** Response fejlécet is, amely ugyanarra az URL-címre hivatkozik, mint a `statusQueryGetUri` korábban említett mező.

## <a name="get-all-instances-status"></a>Az összes példány állapotának lekérése

Az összes példány állapotát lekérdezheti a `instanceId` "példány állapotának beolvasása" kérelemből. Ebben az esetben az alapszintű paraméterek ugyanazok, mint a "példány lekérése" állapot. A szűréshez a lekérdezési karakterlánc paraméterei is támogatottak.

Az egyik dolog megjegyezni, hogy `connection` `code` a és a nem kötelező. Ha a függvényben névtelen hitelesítés található, akkor `code` nem szükséges.
Ha nem szeretne más tárolási kapcsolódási karakterláncot használni, mint a AzureWebJobsStorage alkalmazás beállítása, akkor nyugodtan figyelmen kívül hagyhatja a kapcsolódási lekérdezési karakterlánc paraméterét.

### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező                   | Paramétertípus  | Leírás |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL-cím             | A koordináló példány azonosítója. |
| **`showInput`**         | Lekérdezési sztring    | Nem kötelező megadni. Ha a értékre `false` van állítva, a függvény bemenete nem fog szerepelni a válasz adattartalomban.|
| **`showHistory`**       | Lekérdezési sztring    | Nem kötelező megadni. Ha a értékre `true` van állítva, a rendszer a hangfelvétel-végrehajtási előzményeket fogja tartalmazni a válasz adattartalomban.|
| **`showHistoryOutput`** | Lekérdezési sztring    | Nem kötelező megadni. Ha a értékre `true` van állítva, a függvény kimenetei szerepelni fognak a előkészítési végrehajtási előzményekben.|
| **`createdTimeFrom`**   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyegzőn vagy azt követően létrehozott visszaadott példányok listáját.|
| **`createdTimeTo`**     | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyegzőn vagy azt megelőzően létrehozott visszaadott példányok listáját.|
| **`runtimeStatus`**     | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, a visszaadott példányok listáját a futásidejű állapotuk alapján szűri. A lehetséges futásidejű állapotüzenetek listájának megtekintéséhez tekintse meg a [példányok lekérdezése](durable-functions-instance-management.md) című cikket. |
| **`top`**               | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, korlátozza a lekérdezés által visszaadott példányok számát. |

### <a name="response"></a>Reagálás

Íme egy példa a válasz hasznos adataira, beleértve az előkészítési állapotot (az olvashatóság érdekében formázva):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Ez a művelet nagyon költséges lehet az Azure Storage I/O esetében, ha a példányok táblában sok sor van. A példányok táblával kapcsolatos további részleteket a [Durable functions (Azure functions) dokumentációjának teljesítményében és méretezésében](durable-functions-perf-and-scale.md#instances-table) találhat.
>

Ha több találat is létezik, a rendszer egy folytatási tokent ad vissza a válasz fejlécében.  A fejléc neve: `x-ms-continuation-token` .

Ha a folytatási jogkivonat értékét a következő kérelem fejlécében állítja be, akkor az eredmények következő oldalát szerezheti be. A kérelem fejlécének neve is `x-ms-continuation-token` .

## <a name="purge-single-instance-history"></a>Egypéldányos előzmények végleges törlése

Törli a megadott előkészítési példány előzményeit és a kapcsolódó összetevőket.

### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező             | Paramétertípus  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | A koordináló példány azonosítója. |

### <a name="response"></a>Reagálás

A következő HTTP-állapotkód értékei adhatók vissza.

* **HTTP 200 (ok)**: a példányok előzményeinek törlése sikeresen megtörtént.
* **HTTP 404 (nem található)**: a megadott példány nem létezik.

A **HTTP 200** eset válaszának tartalma egy JSON-objektum, amely a következő mezővel rendelkezik:

| Mező                  | Adattípus | Leírás |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | egész szám   | A példányok száma törölve. Az Egypéldányos eset esetében ennek az értéknek mindig a következőnek kell lennie: `1` . |

Íme egy példa a válasz hasznos adataira (az olvashatóság érdekében formázva):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Több példányos előzmények törlése

Az előzményeket és a kapcsolódó összetevőket is törölheti egy adott munkaközpontban található több példányhoz, ha eltávolítja az `{instanceId}` "egypéldány-előzmények kiürítése" kérést. A példányok előzményeinek szelektív törléséhez használja az "összes példány állapota" kérelemben ismertetett szűrőket.

### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező                 | Paramétertípus  | Description |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Lekérdezési sztring    | A megadott ISO8601 időbélyegzőn vagy azt követően létrehozott kiürített példányok listájának szűrése.|
| **`createdTimeTo`**   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri az adott ISO8601 időbélyegzőn vagy azt megelőzően létrehozott kiürített példányok listáját.|
| **`runtimeStatus`**   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, a rendszer kiszűri a törölt példányok listáját a futásidejű állapotuk alapján. A lehetséges futásidejű állapotüzenetek listájának megtekintéséhez tekintse meg a [példányok lekérdezése](durable-functions-instance-management.md) című cikket. |

> [!NOTE]
> Ez a művelet nagyon költséges lehet az Azure Storage I/O esetében, ha a példányok és/vagy az előzmények táblázatában sok sor van. A táblázatokkal kapcsolatos további részletekért tekintse meg a [Durable functions (Azure functions) dokumentációjának teljesítményét és méretezését](durable-functions-perf-and-scale.md#instances-table) .

### <a name="response"></a>Reagálás

A következő HTTP-állapotkód értékei adhatók vissza.

* **HTTP 200 (ok)**: a példányok előzményeinek törlése sikeresen megtörtént.
* **HTTP 404 (nem található)**: nem található olyan példány, amely megfelel a szűrő kifejezésének.

A **HTTP 200** eset válaszának tartalma egy JSON-objektum, amely a következő mezővel rendelkezik:

| Mező                   | Adattípus | Leírás |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | egész szám   | A példányok száma törölve. |

Íme egy példa a válasz hasznos adataira (az olvashatóság érdekében formázva):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Esemény előléptetése

Esemény-értesítési üzenet küldése egy futó összehangoló példánynak.

### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező             | Paramétertípus  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | A koordináló példány azonosítója. |
| **`eventName`**   | URL-cím             | Annak az eseménynek a neve, amelyre a cél-előkészítési példány várakozik. |
| **`{content}`**   | Tartalom kérése | A JSON-formátumú esemény hasznos adata. |

### <a name="response"></a>Reagálás

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (elfogadva)**: a felváltott esemény feldolgozásra lett elfogadva.
* **HTTP 400 (hibás kérés)**: a kérelem tartalma nem típusú, `application/json` vagy nem volt érvényes JSON.
* **HTTP 404 (nem található)**: a megadott példány nem található.
* **HTTP 410 (eltűnt)**: a megadott példány befejeződött vagy meghiúsult, és nem tudja feldolgozni a létrehozott eseményeket.

Íme egy példa egy olyan kérelemre, amely a JSON-karakterláncot egy olyan példányra küldi, amely a művelet elvégzésére `"incr"` vár: 

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Az API-val kapcsolatos válaszok nem tartalmaznak tartalmat.

## <a name="terminate-instance"></a>Példány leállítása

Leállítja a futó összehangoló példányt.

### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméter.

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | A koordináló példány azonosítója. |
| **`reason`**      | Lekérdezési sztring    | Választható. Az előkészítési példány megszakításának oka. |

### <a name="response"></a>Reagálás

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (elfogadva)**: a megszakítási kérelem el lett fogadva feldolgozásra.
* **HTTP 404 (nem található)**: a megadott példány nem található.
* **HTTP 410 (eltűnt)**: a megadott példány befejeződött vagy meghiúsult.

Íme egy példa egy futó példányt lezáró kérelemre, amely a **hibás** működés okát adja meg:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Az API-val kapcsolatos válaszok nem tartalmaznak tartalmat.

## <a name="rewind-instance-preview"></a>Felcsévélési példány (előzetes verzió)

Visszaállítja a sikertelen előkészítési példányt egy futó állapotba a legutóbbi sikertelen műveletek visszajátszásával.

### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméter.

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | A koordináló példány azonosítója. |
| **`reason`**      | Lekérdezési sztring    | Választható. Az előkészítési példány újratekercselésének oka. |

### <a name="response"></a>Reagálás

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (elfogadva)**: a rendszer a visszatekerési kérést fogadta el feldolgozásra.
* **HTTP 404 (nem található)**: a megadott példány nem található.
* **HTTP 410 (eltűnt)**: a megadott példány befejeződött vagy leállt.

Íme egy példa egy sikertelen példány újratekercselésére, és a **rögzített** okot adja meg:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Az API-val kapcsolatos válaszok nem tartalmaznak tartalmat.

## <a name="signal-entity"></a>Jel entitás

Egy egyirányú műveleti üzenetet küld egy [tartós entitásnak](durable-functions-types-features-overview.md#entity-functions). Ha az entitás nem létezik, a rendszer automatikusan létrehozza.

> [!NOTE]
> A tartós entitások Durable Functions 2,0-tól kezdve érhetők el.

### <a name="request"></a>Kérés

A HTTP-kérés a következőképpen van formázva (az érthetőség kedvéért több sor jelenik meg):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező             | Paramétertípus  | Leírás |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL-cím             | Az entitás neve (típusa). |
| **`entityKey`**   | URL-cím             | Az entitás kulcsa (egyedi azonosítója). |
| **`op`**          | Lekérdezési sztring    | Választható. A meghívni kívánt felhasználó által definiált művelet neve. |
| **`{content}`**   | Tartalom kérése | A JSON-formátumú esemény hasznos adata. |

Íme egy példa arra a kérelemre, amely a felhasználó által megadott "Hozzáadás" üzenetet küld egy `Counter` nevű entitásnak `steps` . Az üzenet tartalma az érték `5` . Ha az entitás még nem létezik, akkor ez a kérelem jön létre:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Alapértelmezés szerint az [osztály-alapú entitások a .net-ben](durable-functions-dotnet-entities.md#defining-entity-classes), az érték megadásával `op` `delete` törli egy entitás állapotát. Ha az entitás egy nevű műveletet határoz meg, akkor a rendszer a `delete` felhasználó által definiált műveletet fogja meghívni.

### <a name="response"></a>Reagálás

A művelet több lehetséges választ tartalmaz:

* **HTTP 202 (elfogadva)**: a rendszer a Signal műveletet aszinkron feldolgozásra fogadta el.
* **HTTP 400 (hibás kérés)**: a kérelem tartalma nem típusú `application/json` , nem érvényes JSON, vagy érvénytelen `entityKey` értékkel rendelkezett.
* **HTTP 404 (nem található)**: a megadott `entityName` nem található.

A sikeres HTTP-kérések nem tartalmaznak semmilyen tartalmat a válaszban. A hibás HTTP-kérések tartalmazhatnak JSON-formátumú hibákat a válasz tartalmában.

## <a name="get-entity"></a>Entitás beolvasása

Lekéri a megadott entitás állapotát.

### <a name="request"></a>Kérés

A HTTP-kérés a következőképpen van formázva (az érthetőség kedvéért több sor jelenik meg):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Reagálás

A művelet két lehetséges választ tartalmaz:

* **HTTP 200 (ok)**: a megadott entitás létezik.
* **HTTP 404 (nem található)**: a megadott entitás nem található.

A sikeres válasz az entitás JSON-szerializált állapotát tartalmazza tartalomként.

### <a name="example"></a>Példa
Az alábbi HTTP-kérelem egy nevű meglévő entitás állapotát kéri le `Counter` `steps` :

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Ha az `Counter` entitás egyszerűen több, egy mezőben mentett lépést tartalmaz `currentValue` , a válasz tartalma a következőhöz hasonlóan fog kinézni (az olvashatóság érdekében formázva):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Entitások listázása

Az entitás neve vagy az utolsó művelet dátuma alapján több entitás is lekérdezhető.

### <a name="request"></a>Kérés

A HTTP-kérés a következőképpen van formázva (az érthetőség kedvéért több sor jelenik meg):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező                       | Paramétertípus  | Leírás |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL-cím             | Választható. Ha meg van adva, az entitás neve alapján szűri a visszaadott entitások listáját (kis-és nagybetűk megkülönböztetése). |
| **`fetchState`**            | Lekérdezési sztring    | Nem kötelező megadni. Ha a értékre `true` van állítva, az entitás állapota szerepelni fog a válasz adattartalmaban. |
| **`lastOperationTimeFrom`** | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a visszaadott entitások listáját, amelyek a megadott ISO8601 timestamp után feldolgozott műveleteket végeznek. |
| **`lastOperationTimeTo`**   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, az adott ISO8601 időbélyege előtt szűri a feldolgozott műveleteket végző visszaadott entitások listáját. |
| **`top`**                   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, korlátozza a lekérdezés által visszaadott entitások számát. |


### <a name="response"></a>Reagálás

A sikeres HTTP 200-válasz az entitások JSON-szerializált tömbjét és opcionálisan az egyes entitások állapotát tartalmazza.

Alapértelmezés szerint a művelet a lekérdezési feltételeknek megfelelő első 100 entitást adja vissza. A hívó a lekérdezési karakterlánc paraméterének értékét úgy adhatja meg `top` , hogy egy másik maximális eredményt ad vissza. Ha a visszaadott eredményeken felül több eredmény is szerepel, a rendszer visszaadja a folytatási jogkivonatot a válasz fejlécében. A fejléc neve: `x-ms-continuation-token` .

Ha a folytatási jogkivonat értékét a következő kérelem fejlécében állítja be, akkor az eredmények következő oldalát szerezheti be. A kérelem fejlécének neve is `x-ms-continuation-token` .

### <a name="example---list-all-entities"></a>Példa – az összes entitás listázása

A következő példában szereplő HTTP-kérelem felsorolja a tevékenység központja összes entitását:

```http
GET /runtime/webhooks/durabletask/entities
```

A válasz JSON a következőhöz hasonlóan jelenhet meg (az olvashatóság érdekében formázva):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Példa – az entitások listájának szűrése

A következő példában szereplő HTTP-kérelem csak az első két típusú entitást listázza `counter` , és az állapotukat is lekéri:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

A válasz JSON a következőhöz hasonlóan jelenhet meg (az olvashatóság érdekében formázva):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan használható a Application Insights a tartós függvények figyeléséhez](durable-functions-diagnostics.md)