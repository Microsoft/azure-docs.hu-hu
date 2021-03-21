---
title: Figyelők a Durable Functionsban (Python) – Azure
description: Ismerje meg, hogyan implementálhat egy állapotfigyelőt a Azure Functions (Python) Durable Functions-bővítményének használatával.
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 62b3c9bb1c6fd53d9f11227a9d7e774d56859d04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434763"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Figyelési forgatókönyv Durable Functions – GitHub-probléma figyelési mintája

A figyelő minta egy munkafolyamatban egy rugalmas ismétlődő folyamatra utal – például az egyes feltételek teljesülése esetén történő lekérdezésre. Ez a cikk a figyelés megvalósítását Durable Functions használó mintát ismerteti.

## <a name="prerequisites"></a>Előfeltételek

* [A gyors üzembe helyezési cikk befejezése](quickstart-python-vscode.md)
* [A Samples projekt klónozása vagy letöltése a GitHubról](https://github.com/Azure/azure-functions-durable-python/tree/main/samples/)


## <a name="scenario-overview"></a>A forgatókönyv áttekintése

Ez a minta figyeli a GitHub-tárházban felmerülő problémák számát, és figyelmezteti a felhasználót, ha több mint 3 nyitott probléma van. Használhat egy normál időzítő által aktivált függvényt, amely rendszeres időközönként kéri a megnyitott probléma megszámlálását. Az ezzel a módszerrel kapcsolatos probléma azonban az **élettartam kezelése**. Ha csak egy riasztást kell elküldeni, a figyelőnek 3 vagy több probléma észlelése után le kell tiltania magát. A figyelési minta az egyéb előnyök mellett saját végrehajtást is elvégezheti:

* A figyelők időközönként futnak, és nem ütemezhetnek: időzítő-trigger óránként *fut* ; a figyelő egy órát *vár* a műveletek között. A figyelő műveletei nem lesznek átfedésben, ha meg vannak adva, ami fontos lehet a hosszan futó feladatok esetében.
* A figyelők lehetnek dinamikus időközök: a várakozási idő bizonyos feltételek alapján változhat.
* A figyelők leállhatnak, ha valamilyen feltétel teljesül, vagy egy másik folyamat leállítja azt.
* A figyelők paramétereket hozhatnak. A minta azt mutatja, hogyan alkalmazható a tárház-figyelési folyamat a kért nyilvános GitHub-adattárra és-telefonszámra.
* A figyelők méretezhetők. Mivel minden figyelő egy előkészítési példány, több figyelő is létrehozható anélkül, hogy új funkciókat kellene létrehoznia, vagy további kódokat kellene megadnia.
* A monitorok könnyen integrálhatók a nagyobb munkafolyamatokban. Egy figyelő lehet egy összetettebb összehangoló függvény, vagy egy [alfolyamatok](durable-functions-sub-orchestrations.md)egyik szakasza is.

## <a name="configuration"></a>Konfiguráció

### <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>A függvények

Ez a cikk a minta alkalmazás következő funkcióit ismerteti:

* `E3_Monitor`: Egy [Orchestrator függvény](durable-functions-bindings.md#orchestration-trigger) , amely `E3_TooManyOpenIssues` rendszeresen hív meg. Azt hívja meg, hogy `E3_SendAlert` a visszatérési értéke: `E3_TooManyOpenIssues` `True` .
* `E3_TooManyOpenIssues`: Egy [tevékenység-függvény](durable-functions-bindings.md#activity-trigger) , amely ellenőrzi, hogy egy adattár túl sok nyitott problémával rendelkezik-e. A bemutató célja, hogy több mint 3 nyitott probléma legyen túl sok.
* `E3_SendAlert`: Egy tevékenység-függvény, amely SMS-üzenetet küld a Twilio-on keresztül.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor Orchestrator függvény


A **E3_Monitor** függvény a standard *function.jst* használja a Orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

Itt látható a függvényt megvalósító kód:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]


Ez a Orchestrator-függvény a következő műveleteket hajtja végre:

1. Beolvassa a figyelni kívánt tárházat *és azt a* *telefonszámot* , amelyre SMS-értesítést fog küldeni.
2. Meghatározza a figyelő lejárati idejét. A minta egy nehezen kódolt értéket használ a rövidség kedvéért.
3. Meghívja a **E3_TooManyOpenIssues** annak megállapítására, hogy túl sok nyitott probléma van-e a kért tárházban.
4. Ha túl sok probléma merül fel, a meghívja a **E3_SENDALERT** SMS-értesítés küldését a kért telefonszámra.
5. Tartós időzítőt hoz létre, amely a következő lekérdezési időszakban folytatja a koordinálást. A minta egy nehezen kódolt értéket használ a rövidség kedvéért.
6. A továbbra is fut, amíg az aktuális UTC-idő át nem adja a figyelő lejárati idejét, vagy SMS-riasztást küld.

Egyszerre több Orchestrator példány is futtatható a Orchestrator függvény többszöri meghívásával. A figyelni kívánt tárház, valamint az SMS-riasztások küldésének telefonszáma. Végezetül vegye figyelembe, hogy a Orchestrator függvény *nem* fut az időzítőre való várakozás közben, ezért nem kell fizetnie.


### <a name="e3_toomanyopenissues-activity-function"></a>E3_TooManyOpenIssues Activity függvény

Más mintákhoz hasonlóan a segítő tevékenység funkciói az trigger-kötést használó reguláris függvények `activityTrigger` . A **E3_TooManyOpenIssues** függvény beolvassa az aktuálisan megnyitott hibák listáját a tárházban, és meghatározza, hogy vannak-e "túl sok": több mint 3, mint a minta.


A *function.jsa* következő módon van definiálva:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

Itt pedig a megvalósítás.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]


### <a name="e3_sendalert-activity-function"></a>E3_SendAlert Activity függvény

A **E3_SendAlert** függvény a Twilio kötés használatával küld SMS-üzenetet, amely értesíti a felhasználót arról, hogy legalább 3 nyitott probléma vár a megoldásra.


A *function.js* egyszerű:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

Itt látható az SMS-üzenetet küldő kód:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]


## <a name="run-the-sample"></a>Minta futtatása

Szüksége lesz egy [GitHub](https://github.com/) -fiókra. Ezzel hozzon létre egy ideiglenes nyilvános tárházat, amely a következőhöz kapcsolódóan tud megnyílni:.

A mintában szereplő HTTP-triggerű függvények használatával a következő HTTP POST-kérelem elküldésével elindíthatja a koordinálást:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

Ha például a GitHub-felhasználóneve, `foo` a tárház pedig az, `bar` akkor a értékének a következőnek kell `"repo"` lennie: `"foo/bar"` .

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

A **E3_Monitor** példány elindul, és lekérdezi a megadott tárházat a nyílt problémákhoz. Ha a rendszer legalább 3 nyitott problémát észlel, akkor a rendszer egy tevékenységi függvényt hív meg, amely riasztást küld. Ellenkező esetben egy időzítőt állít be. Ha az időzítő lejár, a rendszer folytatja a koordinálást.

A munkafolyamatok tevékenysége a Azure Functions portálon megjelenő függvények naplói szerint látható.

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

A rendszer az időtúllépés elérésekor vagy több mint 3 nyitott probléma észlelésekor befejeződik. Az `terminate` API-t egy másik függvényen belül is használhatja, vagy meghívja a **TERMINATEPOSTURI** http post webhookot, amelyre a fenti 202-válasz hivatkozik. A webhook használatához cserélje le a helyére a `{text}` korai megszakítás okát. A HTTP POST URL-címe nagyjából a következőképpen fog megjelenni:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Következő lépések

Ez a példa azt mutatja be, hogyan használható a Durable Functions egy külső forrás állapotának figyelésére [tartós időzítők](durable-functions-timers.md) és feltételes logika használatával. A következő minta bemutatja, hogyan használhatók a külső események és a [tartós időzítők](durable-functions-timers.md) az emberi interakció kezelésére.

> [!div class="nextstepaction"]
> [Az emberi interakciós minta futtatása](durable-functions-phone-verification.md)
