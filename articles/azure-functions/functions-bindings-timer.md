---
title: Időzítő trigger a Azure Functionshoz
description: Megtudhatja, hogyan használhatja az időzítő eseményindítókat a Azure Functionsban.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f99f9d240a0a9220d3b7f57cddd0a4f8ba6b6101
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595991"
---
# <a name="timer-trigger-for-azure-functions"></a>Időzítő trigger a Azure Functionshoz

Ez a cikk azt ismerteti, hogyan használhatók időzítő eseményindítók a Azure Functionsban. Az időzítő-trigger lehetővé teszi, hogy ütemezés szerint futtasson egy függvényt.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Az időzítő által aktivált függvények manuális futtatásával kapcsolatos információkért lásd a [nem http-triggert futtató függvények kézi futtatása](./functions-manually-run-non-http.md)című témakört.

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

Az időzítő triggert a [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet csomagban, 3. x verzióban kell megadnia. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

Az időzítő triggert a [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet csomagban, 2. x verzióban kell megadnia. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amelyet minden alkalommal végrehajt, amikor a percek értéke öt (például ha a függvény 18:57:00-kor kezdődik), a következő teljesítmény a 19:00:00-nél lesz. A rendszer átadja az [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objektumot a függvénynek.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy időzítő trigger kötést mutat be egy *function.jsa* fájlban és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy naplót ír, amely azt jelzi, hogy a függvény meghívása egy kihagyott ütemterv miatt következik-e be. A rendszer átadja az [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objektumot a függvénynek.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

A C# szkript kódja:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="java"></a>[Java](#tab/java)

A következő példa függvény elindítja és végrehajtja az öt percenkénti műveletet. A `@TimerTrigger` függvény megjegyzése az ütemtervet a [cron-kifejezésekkel](https://en.wikipedia.org/wiki/Cron#CRON_expression)megegyező karakterlánc-formátum használatával határozza meg.

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy időzítő trigger kötést mutat be egy *function.jsa* fájlban, és egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egy naplót ír, amely azt jelzi, hogy a függvény meghívása egy kihagyott ütemterv miatt következik-e be. A függvény egy [időzítő objektumot](#usage) ad át.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Itt látható a JavaScript-kód:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa bemutatja, hogyan konfigurálhatja a [PowerShellben](./functions-reference-powershell.md)a *function.jst* és *run.ps1* fájlt egy időzítő-triggerhez.

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

Az [időzítő objektum](#usage) egy példányát a függvény első argumentumaként adja át a rendszer.

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy időzítő trigger kötést használ, amelynek konfigurációját a fájl *function.js* írja le. A kötést használó tényleges [Python-függvényt](functions-reference-python.md) az *__init__.* rajzfájl fájl írja le. A függvénynek átadott objektum [Azure. functions. TimerRequest objektum](/python/api/azure-functions/azure.functions.timerrequest)típusú. A függvény logikája azt jelzi, hogy az aktuális hívás egy kimaradt ütemterv miatt következik-e be.

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Itt látható a Python-kód:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Az attribútum konstruktora egy CRON kifejezést vagy egy-t használ `TimeSpan` . Csak akkor használható, `TimeSpan` Ha a Function alkalmazás egy app Service csomagon fut. `TimeSpan` Fogyasztási vagy rugalmas prémium szintű függvények esetében nem támogatott.

A következő példa egy CRON-kifejezést mutat be:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `@TimerTrigger` függvény megjegyzése az ütemtervet a [cron-kifejezésekkel](https://en.wikipedia.org/wiki/Cron#CRON_expression)megegyező karakterlánc-formátum használatával határozza meg.

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `TimerTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n.a. | "TimerTrigger" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**irányba** | n.a. | "In" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | n.a. | Annak a változónak a neve, amely az időzítő objektumot jelöli a függvény kódjában. | 
|**menetrend**|**ScheduleExpression**|Egy [cron kifejezés](#ncrontab-expressions) vagy egy [TimeSpan](#timespan) érték. A `TimeSpan` csak egy app Service csomagon futó Function alkalmazás esetében használható. Az ütemezett kifejezést beállíthatja egy alkalmazás-beállításban, és ezt a tulajdonságot megadhatja a jelek között becsomagolt Alkalmazásbeállítások nevében, az **%** alábbi példában látható módon: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Ha a `true` rendszer meghívja a függvényt a futtatókörnyezet indításakor. Például a futtatókörnyezet akkor indul el, amikor a Function alkalmazás felébred, miután inaktivitás miatt tétlen marad. Ha a Function alkalmazás újraindul a függvény változásai miatt, és a függvény alkalmazás skálázása. Így a **runOnStartup** ritkán kell beállítani `true` , különösen éles környezetben. |
|**useMonitor**|**UseMonitor**|Állítsa be `true` vagy `false` értékre, ha azt szeretné, hogy a program figyelje az ütemtervet. Az ütemterv figyelése továbbra is fenntartja az ütemezett előfordulásokat, hogy a támogatás az ütemterv megfelelő karbantartása legyen, még akkor is, ha a Function app instances újraindul Ha nincs explicit beállítva, az alapértelmezett érték az olyan `true` ütemezések esetében, amelyeknél az ismétlődési időköz nagyobb vagy egyenlő, mint 1 perc. Az olyan ütemtervek esetében, amelyek percenként többször aktiválódnak, az alapértelmezett érték `false` .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Javasoljuk, hogy éles környezetben **runOnStartup** a beállítást `true` . Ha ezt a beállítást használja, a kód nagy előre nem látható időpontokban lesz végrehajtva. Bizonyos éles beállításokban ezek az extra végrehajtások jelentős mértékben magasabb költségekkel járhatnak a használati tervekben üzemeltetett alkalmazások esetében. Ha például a **runOnStartup** engedélyezve van, akkor a rendszer meghívja az eseményindítót, amikor a Function alkalmazás skálázásra kerül. Győződjön meg arról, hogy teljesen tisztában van a függvények üzemi viselkedésével, mielőtt engedélyezi a **runOnStartup** az éles környezetben.

## <a name="usage"></a>Használat

Időzítő eseményindító függvény meghívásakor a függvény egy időzítő objektumot ad át. A következő JSON példa az időzítő objektum ábrázolására.

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

A `isPastDue` tulajdonság az, `true` amikor az aktuális függvény meghívása az ütemezettnél későbbi. Előfordulhat például, hogy egy Function alkalmazás újraindítása miatt a hívás kimarad.

## <a name="ncrontab-expressions"></a>NCRONTAB kifejezések

Azure Functions a [NCronTab](https://github.com/atifaziz/NCrontab) -függvénytárat használja a NCronTab kifejezések értelmezéséhez. Egy NCRONTAB kifejezés hasonló egy CRON-kifejezéshez, kivéve, ha az elején további hatodik mező szerepel az idő pontosságához másodpercben:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Minden mezőhöz a következő típusú értékek tartozhatnak:

|Típus  |Példa  |Aktiváláskor  |
|---------|---------|---------|
|Egy adott érték |<nobr>`0 5 * * * *`</nobr>| A nap minden órájában, minden órában 5 percenként |
|Minden érték ( `*` )|<nobr>`0 * 5 * * *`</nobr>| Óránként percenként, 5 óra |
|Tartomány ( `-` operátor)|<nobr>`5-7 * * * * *`</nobr>| Percenként háromszor – 5 – 7 percen belül minden nap minden percében |
|Értékek halmaza ( `,` operátor)|<nobr>`5,8,10 * * * * *`</nobr>| Percenként háromszor – az 5, 8 és 10 másodperc minden nap minden percében |
|Intervallum értéke ( `/` operátor)|<nobr>`0 */5 * * * *`</nobr>| óránként 12 alkalommal – az egyes napok minden órájában minden ötödik percenként 0. |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB-példák

Íme néhány példa a Azure Functions időzítő-triggeréhez használható NCRONTAB-kifejezésekre.

| Példa            | Aktiváláskor                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | öt percenként            |
| `0 0 * * * *`      | egyszer minden óra elején      |
| `0 0 */2 * * *`    | két óránként egyszer               |
| `0 0 9-17 * * *`   | óránként, 9 órától 5 óráig  |
| `0 30 9 * * *`     | minden nap 9:30-kor               |
| `0 30 9 * * 1-5`   | minden hétköznap 9:30 órakor           |
| `0 30 9 * Jan Mon` | Január 9:30 órakor |

> [!NOTE]
> A NCRONTAB kifejezésben **hat mező** formátuma szükséges. A hatodik mező pozíciója a kifejezés elejére helyezett másodpercek értéke. Az Azure nem támogatja az öt mezőből származó cron-kifejezéseket.

### <a name="ncrontab-time-zones"></a>NCRONTAB időzónái

A CRON-kifejezésben szereplő számok egy időre és dátumra hivatkoznak, nem pedig időtartományra. A mezőben lévő 5 `hour` érték például 5:00, nem 5 óránként.

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>időtartam

 A `TimeSpan` csak egy app Service csomagon futó Function alkalmazás esetében használható.

A CRON kifejezéstől eltérően `TimeSpan` az érték határozza meg az egyes függvények meghívása közötti időtartamot. Ha egy függvény a megadott intervallumnál hosszabb ideig fut, az időzítő azonnal meghívja a függvényt.

Karakterláncként kifejezve a `TimeSpan` formátum a `hh:mm:ss` 24- `hh` nél kisebb. Ha az első két számjegy 24 vagy nagyobb, a formátum: `dd:hh:mm` . Íme néhány példa:

| Példa      | Aktiváláskor |
|--------------|----------------|
| "01:00:00"   | óránként     |
| "00:01:00"   | percenként   |
| "25:00:00"   | 25 naponta  |
| "1.00:00:00" | minden nap      |

## <a name="scale-out"></a>Bővítés

Ha egy függvény alkalmazás több példányra is kiterjed, csak egy időzítő által aktivált függvény egyetlen példánya fut az összes példányon. Ha egy függőben lévő hívás még fut, a rendszer nem indítja újra.

## <a name="function-apps-sharing-storage"></a>Function apps megosztása Storage

Ha a Storage-fiókokat az App Service-be nem telepített functions-alkalmazások között osztja meg, előfordulhat, hogy explicit módon kell kiosztania a gazdagép AZONOSÍTÓját az egyes alkalmazásokhoz.

| Függvények verziója | Beállítás                                              |
| ----------------- | ---------------------------------------------------- |
| 2. x (és újabb)  | `AzureFunctionsWebHost__hostid` környezeti változó |
| 1. x               | `id`*host.jsbekapcsolva*                                  |

Kihagyhatja az azonosító értéket, vagy manuálisan állíthatja be az egyes functions-alkalmazások azonosítási konfigurációját egy másik értékre.

Az időzítő-trigger tároló-zárolással biztosítja, hogy csak egy időzítő példány legyen, ha egy függvény alkalmazás több példányra is méretezhető. Ha két Function-alkalmazás ugyanazt az azonosítási konfigurációt használja, és mindegyik időzítő-triggert használ, csak egy időzítő fut.

## <a name="retry-behavior"></a>Újrapróbálkozási viselkedés

A várólista-triggertől eltérően az időzítő trigger nem próbálkozik újra a függvény meghibásodása után. Ha egy függvény meghibásodik, azt a rendszer nem hívja újra az ütemezés következő időpontjáig.

## <a name="manually-invoke-a-timer-trigger"></a>Időzítő eseményindító manuális meghívása

A Azure Functions időzítő eseményindítója olyan HTTP webhookot biztosít, amely a függvény manuális elindítására szolgál. Ez a következő helyzetekben rendkívül hasznos lehet.

* Integrációtesztelés
* Tárolóhelyek cseréje a füst vagy a bemelegedési tevékenység részeként
* Egy függvény kezdeti üzembe helyezése, amely azonnal feltölti a gyorsítótárat vagy a keresési táblázatot egy adatbázisban

Az időzítő által aktivált függvények manuális meghívásával kapcsolatos részletekért tekintse meg a [nem http-triggert futtató függvények manuális futtatását](./functions-manually-run-non-http.md) ismertető témakört.

## <a name="troubleshooting"></a>Hibaelhárítás

További információ arról, hogy mi a teendő, ha az időzítő trigger nem a várt módon működik, lásd: [kivizsgálás és jelentéskészítési hibák az időzítő által aktivált függvények nem égetéssel](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ugrás olyan rövid útmutatóra, amely időzítő-triggert használ](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
