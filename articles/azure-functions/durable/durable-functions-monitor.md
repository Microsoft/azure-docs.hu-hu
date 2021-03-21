---
title: Figyelők a Durable Functionsban – Azure
description: Ismerje meg, hogyan implementálhat egy állapotfigyelőt a Azure Functions Durable Functions-bővítményének használatával.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 8ef32ecfb6f69b71d29578d3b8314f568fd9386a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431074"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Figyelő forgatókönyv Durable Functions-Weather Watcher minta

A figyelő minta egy munkafolyamatban egy rugalmas *ismétlődő* folyamatra utal – például az egyes feltételek teljesülése esetén történő lekérdezésre. Ez a cikk a figyelés megvalósítását [Durable functions](durable-functions-overview.md) használó mintát ismerteti.

## <a name="prerequisites"></a>Előfeltételek

# <a name="c"></a>[C#](#tab/csharp)

* [A gyors üzembe helyezési cikk befejezése](durable-functions-create-first-csharp.md)
* [A Samples projekt klónozása vagy letöltése a GitHubról](https://github.com/Azure/azure-functions-durable-extension/tree/main/samples/precompiled)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [A gyors üzembe helyezési cikk befejezése](quickstart-js-vscode.md)
* [A Samples projekt klónozása vagy letöltése a GitHubról](https://github.com/Azure/azure-functions-durable-extension/tree/main/samples/javascript)

---

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

Ez a minta figyeli a hely aktuális időjárási feltételeit, és SMS-ben figyelmezteti a felhasználót, amikor az égbolt törölve van. Egy normál időzítő által aktivált függvény használatával ellenőrizhető az időjárási idő, és riasztásokat küldhet. Az ezzel a módszerrel kapcsolatos probléma azonban az **élettartam kezelése**. Ha csak egy riasztást kell elküldeni, a figyelőnek le kell tiltania magát a törlési idő észlelése után. A figyelési minta az egyéb előnyök mellett saját végrehajtást is elvégezheti:

* A figyelők időközönként futnak, és nem ütemezhetnek: időzítő-trigger óránként *fut* ; a figyelő egy órát *vár* a műveletek között. A figyelő műveletei nem lesznek átfedésben, ha meg vannak adva, ami fontos lehet a hosszan futó feladatok esetében.
* A figyelők lehetnek dinamikus időközök: a várakozási idő bizonyos feltételek alapján változhat.
* A figyelők leállhatnak, ha valamilyen feltétel teljesül, vagy egy másik folyamat leállítja azt.
* A figyelők paramétereket hozhatnak. A minta azt mutatja be, hogyan alkalmazható az időjárás-figyelési folyamat a kért helyekre és telefonszámokra.
* A figyelők méretezhetők. Mivel minden figyelő egy előkészítési példány, több figyelő is létrehozható anélkül, hogy új funkciókat kellene létrehoznia, vagy további kódokat kellene megadnia.
* A monitorok könnyen integrálhatók a nagyobb munkafolyamatokban. Egy figyelő lehet egy összetettebb összehangoló függvény, vagy egy [alfolyamatok](durable-functions-sub-orchestrations.md)egyik szakasza is.

## <a name="configuration"></a>Konfiguráció

### <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Az időjárási földalatti integráció konfigurálása

Ez a példa a Weather Underground API-t használja a hely aktuális időjárási feltételeinek vizsgálatához.

Az első dolog, amire szüksége van egy időjárási Underground-fiók. Létrehozhat egyet ingyen a következő címen: [https://www.wunderground.com/signup](https://www.wunderground.com/signup) . Ha már rendelkezik fiókkal, meg kell adnia egy API-kulcsot. Ehhez látogasson el [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1) , és válassza a Key Settings (Alapbeállítások) lehetőséget. Az Stratus fejlesztői terve ingyenes, és elegendő a minta futtatásához.

Ha már rendelkezik API-kulccsal, adja hozzá a következő **alkalmazás-beállítást** a Function alkalmazáshoz.

| Alkalmazás-beállítás neve | Érték leírása |
| - | - |
| **WeatherUndergroundApiKey**  | Az időjárási Underground API-kulcsa. |

## <a name="the-functions"></a>A függvények

Ez a cikk a minta alkalmazás következő funkcióit ismerteti:

* `E3_Monitor`: Egy [Orchestrator függvény](durable-functions-bindings.md#orchestration-trigger) , amely `E3_GetIsClear` rendszeresen hív meg. `E3_SendGoodWeatherAlert`Ha `E3_GetIsClear` igaz értéket ad vissza, meghívja a függvényt.
* `E3_GetIsClear`: Olyan [tevékenységi függvény](durable-functions-bindings.md#activity-trigger) , amely egy adott hely aktuális időjárási feltételeit ellenőrzi.
* `E3_SendGoodWeatherAlert`: Egy tevékenység-függvény, amely SMS-üzenetet küld a Twilio-on keresztül.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor Orchestrator függvény

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

A Orchestrator meg kell adni egy helyet a figyeléshez, valamint egy telefonszámot, hogy üzenetet küldjön, amikor a helyről törölve lesz. Ezeket az adattípusokat a rendszer erősen beírt objektumként adja át a Orchestrator `MonitorRequest` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A **E3_Monitor** függvény a standard *function.jst* használja a Orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Itt látható a függvényt megvalósító kód:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

Ez a Orchestrator-függvény a következő műveleteket hajtja végre:

1. Lekérdezi a figyelni kívánt *helyet* tartalmazó **MonitorRequest** , valamint azt a *telefonszámot* , amelyre SMS-értesítést fog küldeni.
2. Meghatározza a figyelő lejárati idejét. A minta egy nehezen kódolt értéket használ a rövidség kedvéért.
3. Meghívja a **E3_GetIsClear** annak megállapítására, hogy van-e egyértelmű égbolt a kért helyen.
4. Ha az időjárás egyértelmű, meghívja a **E3_SendGoodWeatherAlertt** , hogy SMS-értesítést küldjön a kért telefonszámra.
5. Tartós időzítőt hoz létre, amely a következő lekérdezési időszakban folytatja a koordinálást. A minta egy nehezen kódolt értéket használ a rövidség kedvéért.
6. A továbbra is fut, amíg az aktuális UTC-idő át nem adja a figyelő lejárati idejét, vagy SMS-riasztást küld.

Egyszerre több Orchestrator példány is futtatható a Orchestrator függvény többszöri meghívásával. Megadható a figyelni kívánt hely és a telefonszám, amely SMS-riasztást küld. Végezetül vegye figyelembe, hogy a Orchestrator függvény *nem* fut az időzítőre való várakozás közben, ezért nem kell fizetnie.
### <a name="e3_getisclear-activity-function"></a>E3_GetIsClear Activity függvény

Más mintákhoz hasonlóan a segítő tevékenység funkciói az trigger-kötést használó reguláris függvények `activityTrigger` . A **E3_GetIsClear** függvény az időjárási földalatti API használatával beolvassa az aktuális időjárási feltételeket, és meghatározza, hogy az ég tiszta-e.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A *function.jsa* következő módon van definiálva:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

Itt pedig a megvalósítás.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>E3_SendGoodWeatherAlert Activity függvény

A **E3_SendGoodWeatherAlert** függvény a Twilio kötés használatával küld SMS-üzenetet, amely értesíti a felhasználót arról, hogy jó idő van egy sétára.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> A mintakód futtatásához telepítenie kell a `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget csomagot.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A *function.js* egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

Itt látható az SMS-üzenetet küldő kód:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>Minta futtatása

A mintában szereplő HTTP-triggerű függvények használatával a következő HTTP POST-kérelem elküldésével elindíthatja a koordinálást:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

A **E3_Monitor** példány elindul, és lekérdezi a kért hely aktuális időjárási feltételeit. Ha az időjárás törölve van, akkor egy tevékenység függvényt hív meg, amely riasztást küld; Ellenkező esetben egy időzítőt állít be. Ha az időzítő lejár, a rendszer folytatja a koordinálást.

A munkafolyamatok tevékenysége a Azure Functions portálon megjelenő függvények naplói szerint látható.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

Az előkészítés az időtúllépés elérésekor vagy az égbolt észlelésének törlésével fejeződik be. Az `terminate` API-t egy másik függvényen belül is használhatja, vagy meghívja a **TERMINATEPOSTURI** http post webhookot, amelyre a fenti 202-válasz hivatkozik. A webhook használatához cserélje le a helyére a `{text}` korai megszakítás okát. A HTTP POST URL-címe nagyjából a következőképpen fog megjelenni:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Következő lépések

Ez a példa azt mutatja be, hogyan használható a Durable Functions egy külső forrás állapotának figyelésére [tartós időzítők](durable-functions-timers.md) és feltételes logika használatával. A következő minta bemutatja, hogyan használhatók a külső események és a [tartós időzítők](durable-functions-timers.md) az emberi interakció kezelésére.

> [!div class="nextstepaction"]
> [Az emberi interakciós minta futtatása](durable-functions-phone-verification.md)