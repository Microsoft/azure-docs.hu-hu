---
title: Tartós összeszerelések – Azure Functions
description: Az Azure Durable Functions előkészítési funkciójának bemutatása.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: ba314963058389e171601407ff00411049eecd45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97845421"
---
# <a name="durable-orchestrations"></a>Tartós összeszerelések

Durable Functions a [Azure functions](../functions-overview.md)kiterjesztése. A *Orchestrator függvénnyel* más tartós függvények végrehajtását is elvégezheti a Function alkalmazásban. A Orchestrator függvények jellemzői a következők:

* A Orchestrator függvények eljárási kóddal határozzák meg a függvények munkafolyamatait. Nincs szükség deklaratív sémára vagy tervezőre.
* A Orchestrator függvények szinkronban és aszinkron módon hívhatják meg a többi tartós funkciót. A hívott függvények kimenete megbízhatóan menthető a helyi változókba.
* A Orchestrator függvények tartósak és megbízhatók. A végrehajtási folyamat automatikusan ellenőrzőpontra kerül, amikor a függvény "vár" vagy "hozam". A folyamat újraindításakor vagy a virtuális gép újraindításakor a helyi állapot soha nem vész el.
* A Orchestrator függvények hosszú ideig is futhatnak. Egy előkészítési *példány* teljes élettartama másodperc, nap, hónap vagy soha vége lehet.

Ez a cikk áttekintést nyújt a Orchestrator functions szolgáltatásról, valamint arról, hogy miként segíthetnek a különböző alkalmazás-fejlesztési problémák megoldásában. Ha még nem ismeri a Durable Functions alkalmazásban elérhető függvények típusát, először olvassa el a [tartós függvények típusait](durable-functions-types-features-overview.md) ismertető cikket.

## <a name="orchestration-identity"></a>Előkészítési identitás

Egy előkészítés minden *példánya* rendelkezik egy példány-azonosítóval (más néven *példány-azonosítóval*). Alapértelmezés szerint minden példány azonosítója egy automatikusan generált GUID. A példányok azonosítói azonban bármilyen felhasználó által generált karakterláncot is tartalmazhatnak. Minden egyes összehangoló példány AZONOSÍTÓjának egyedinek kell lennie a [feladatsoron](durable-functions-task-hubs.md)belül.

Az alábbi szabályok a példányok azonosítóit tárgyalják:

* A példány-azonosítónak 1 és 256 karakter közöttinek kell lennie.
* A példány-azonosítók nem kezdődhetnek együtt `@` .
* A példány-azonosítók nem tartalmazhatnak,, `/` `\` `#` vagy `?` karaktereket.
* A példány-azonosítók nem tartalmazhatnak vezérlő karaktereket.

> [!NOTE]
> Általában ajánlott automatikusan generált példány-azonosítókat használni, amikor csak lehetséges. A felhasználó által generált példányok azonosítói olyan forgatókönyvekhez készültek, ahol az egy-az-egyhez hozzárendelés egy összehangoló példány és egy külső, alkalmazásspecifikus entitás, például egy beszerzési rendelés vagy egy dokumentum között.

Egy előkészítési példány azonosítója egy kötelező paraméter a legtöbb példány- [felügyeleti művelethez](durable-functions-instance-management.md). Emellett a diagnosztika szempontjából is fontosak, például a Application Insights a munkafolyamatok [nyomon követési adatait](durable-functions-diagnostics.md#application-insights) , hibaelhárítási és elemzési célokra. Ezért javasoljuk, hogy a generált példányok azonosítóit egy külső helyre (például egy adatbázisba vagy az alkalmazás naplóiba) mentse, ahol később könnyen lehet hivatkozni.

## <a name="reliability"></a>Megbízhatóság

Az Orchestrator függvények az [esemény-beszerzések](/azure/architecture/patterns/event-sourcing) kialakítási mintája alapján megbízhatóan karbantartják végrehajtási állapotukat. A folyamat aktuális állapotának közvetlen tárolása helyett az állandó feladathoz tartozó keretrendszer egy csak Hozzáfűzéses tárolót használ a függvények összehangolása által végrehajtott műveletek teljes sorozatának rögzítéséhez. A csak Hozzáfűzéses tároló számos előnnyel jár, mint a teljes futtatókörnyezet állapotának "kiírása". Az előnyök többek között a teljesítmény, a méretezhetőség és a rugalmasság. A tranzakciós és a teljes naplózási nyomvonalak és előzmények végleges konzisztenciáját is biztosítjuk. A naplózási nyomvonalak támogatják a megbízható kompenzáló műveleteket.

A Durable Functions az események beszerzését transzparens módon használja. A színfalak mögött a `await` Orchestrator függvényben a (C#) vagy a `yield` (JavaScript/Python) operátor a Orchestrator-szál vezérlését eredményezi a tartós feladatok keretrendszer-diszpécserének. A diszpécser ezután véglegesít minden olyan új műveletet, amelyet a Orchestrator függvény ütemez (például egy vagy több alárendelt függvény hívása vagy tartós időzítő ütemezése) a tárolóba. Az átlátszó véglegesítő művelet hozzáfűzi a rendszerelőkészítési példány végrehajtási előzményeihez. Az előzmények tárolása egy tárolási táblában történik. A commit művelet ezután üzeneteket hoz létre egy várólistához a tényleges munka időzítése érdekében. Ezen a ponton a Orchestrator függvény eltávolítható a memóriából.

Ha egy összehangoló függvény több munkát tesz elérhetővé (például válaszüzenet érkezik vagy tartós időzítő lejár), a Orchestrator felébred, és újból végrehajtja a teljes függvényt az elejétől a helyi állapot újraépítéséhez. Ha a kód megpróbál meghívni egy függvényt (vagy bármilyen más aszinkron munkát hajt végre), akkor az állandó feladat-keretrendszer az aktuális előkészítés végrehajtási előzményeit kérdezi le. Ha úgy találja, hogy a [tevékenységi függvény](durable-functions-types-features-overview.md#activity-functions) már végre lett hajtva, és eredményként eredményezte, akkor az a függvény eredményét játssza le, és a Orchestrator-kód továbbra is futni fog. A visszajátszás addig folytatódik, amíg a függvény kódja be nem fejeződik, vagy amíg be nem ütemezi az új aszinkron munkát.

> [!NOTE]
> Ahhoz, hogy az újrajátszás mintája megfelelően működjön és megbízható legyen, a Orchestrator függvény kódjának *determinisztikus* kell lennie. A Orchestrator függvények kódjainak korlátozásával kapcsolatos további információkért lásd a [Orchestrator-függvényekre](durable-functions-code-constraints.md) vonatkozó korlátozásokkal foglalkozó témakört.

> [!NOTE]
> Ha egy Orchestrator-függvény naplófájlokat bocsát ki, a visszajátszás viselkedése duplikált üzeneteket eredményezhet. Tekintse meg a [naplózási](durable-functions-diagnostics.md#app-logging) témakört, ahol további információt talál a működésével és megoldásával kapcsolatban.

## <a name="orchestration-history"></a>Előkészítési előzmények

A tartós feladatok keretrendszerének esemény-beszerzés viselkedése szorosan összekapcsolja az Ön által írt Orchestrator-függvény kódját. Tegyük fel, hogy van egy Orchestrator-függvénye, például a következő Orchestrator függvény:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    result1 = yield context.call_activity('SayHello', "Tokyo")
    result2 = yield context.call_activity('SayHello', "Seattle")
    result3 = yield context.call_activity('SayHello', "London")
    return [result1, result2, result3]

main = df.Orchestrator.create(orchestrator_function)
```
---

Minden `await` (C#) vagy `yield` (JavaScript/Python) utasításban a tartós feladatok keretrendszere a függvény végrehajtási állapotát egy tartós tárolási háttérbe (általában az Azure Table Storage-ba) helyezi. Ezt az állapotot nevezzük a megszervezési *előzményeknek*.

### <a name="history-table"></a>Előzmények táblázat

Általánosságban elmondható, hogy az állandó feladathoz tartozó keretrendszer minden ellenőrzőponton a következőket végzi el:

1. Elmenti a végrehajtási előzményeket az Azure Storage-táblákba.
2. A Orchestrator által meghívott függvények Enqueues üzenetei.
3. A Orchestrator Enqueues üzenetei &mdash; , például tartós időzítő üzenetek.

Az ellenőrzőpont befejezését követően a Orchestrator függvény szabadon eltávolítható a memóriából, amíg még nem működik.

> [!NOTE]
> Az Azure Storage nem biztosít tranzakciós garanciát az adatmentés a Table Storage-ba és a várólistákba. A hibák kezelése érdekében a Durable Functions Storage-szolgáltató *végleges konzisztencia* -mintákat használ. Ezek a minták biztosítják, hogy az ellenőrzőpontok közepén lévő kapcsolat összeomlása vagy elvesztése esetén ne vesszenek el az adatvesztés.

Befejezésekor a korábban bemutatott függvény előzményei a következő táblázathoz hasonlóan jelennek meg az Azure Table Storage (illusztrációs célokra rövidítve):

| PartitionKey (InstanceId)                     | EventType             | Időbélyeg               | Bevitel | Name             | Eredmény                                                    | Állapot |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852 Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201 Z |       |                  | "" "Hello Tokyo!" "                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763 Z |       |                  | "" "Helló Seattle!" "                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919 Z |       |                  | "" "Helló Londonban!" "                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044 Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044 Z |       |                  | "[" "Hello Tokyo!" "," "Helló Seattle!" "," "Hello London!" "]" | Befejeződött           |

Néhány megjegyzés az oszlop értékeihez:

* **PartitionKey**: a folyamat PÉLDÁNYának azonosítóját tartalmazza.
* **EventType**: az esemény típusát jelöli. A következő típusok egyike lehet:
  * **OrchestrationStarted**: a Orchestrator függvény a várttól kezdve folytatódik, vagy első alkalommal fut. Az `Timestamp` oszlop a `CurrentUtcDateTime` (.net), `currentUtcDateTime` (JavaScript) és `current_utc_datetime` (Python) API-k determinisztikus értékének feltöltésére szolgál.
  * **ExecutionStarted**: a Orchestrator függvény első alkalommal indult el. Ez az esemény az oszlop függvény bemenetét is tartalmazza `Input` .
  * **TaskScheduled**: a tevékenységi függvény ütemezése megtörtént. A tevékenység-függvény neve az oszlopban van rögzítve `Name` .
  * **TaskCompleted**: egy tevékenység-függvény befejeződött. A függvény eredménye az `Result` oszlopban található.
  * **TimerCreated**: egy tartós időzítő lett létrehozva. Az `FireAt` oszlop azt az ütemezett UTC-időt tartalmazza, amelyen az időzítő lejár.
  * **TimerFired**: tartós időzítő.
  * **EventRaised**: a rendszer egy külső eseményt kapott a rendszerelőkészítési példánynak. Az `Name` oszlop rögzíti az esemény nevét, és az `Input` oszlop rögzíti az esemény hasznos adatait.
  * **OrchestratorCompleted**: a Orchestrator függvény várt.
  * **ContinueAsNew**: a Orchestrator függvény befejeződött, és új állapottal indult újra. Az `Result` oszlop tartalmazza azt az értéket, amelyet a rendszer az újraindított példány bemenetként használ.
  * **ExecutionCompleted**: a Orchestrator függvény befejeződött (vagy sikertelen). A függvény kimeneteit vagy a hiba részleteit az oszlopban tárolja a rendszer `Result` .
* **Időbélyeg**: az előzmények ESEMÉNYének UTC-időbélyege.
* **Name (név**): a meghívott függvény neve.
* **Bemenet**: a függvény JSON formátumú bemenete.
* **Eredmény**: a függvény kimenete; Ez a visszatérési értéke.

> [!WARNING]
> Habár hibakeresési eszközként hasznos, ne tegyen függőséget ezen a táblán. Előfordulhat, hogy a Durable Functions bővítmény fejlődése megváltozhat.

Minden alkalommal, amikor a függvény egy `await` (C#) vagy `yield` (JavaScript/Python) használatával folytatja a műveletet, a tartós feladat-keretrendszer újra lefuttatja a Orchestrator függvényt. Minden újrafuttatáskor megtekinti a végrehajtás előzményeit, és megállapítja, hogy az aktuális aszinkron művelet megtörtént-e.  Ha a művelet lezajlott, a keretrendszer azonnal visszajátssza a művelet kimenetét, és továbblép a következőre `await` (C#) vagy `yield` (JavaScript/Python). Ez a folyamat addig folytatódik, amíg a teljes előzmények újra nem lettek játszva. Az aktuális előzmények újbóli lejátszása után a helyi változók vissza lesznek állítva a korábbi értékekre.

## <a name="features-and-patterns"></a>Funkciók és minták

A következő szakaszok a Orchestrator függvények funkcióit és mintáit ismertetik.

### <a name="sub-orchestrations"></a>Alvezénylések

A Orchestrator függvények meghívhatják a tevékenységek függvényeit, de más Orchestrator-függvényeket is használhatnak. Létrehozhat például egy nagyobb, a Orchestrator-függvények könyvtárainak összeszerelését. A Orchestrator függvény több példányát is futtathatja párhuzamosan.

További információért és példákért tekintse meg az [alfolyamatok](durable-functions-sub-orchestrations.md) című cikket.

### <a name="durable-timers"></a>Tartós időzítők

A munkafolyamatok *tartós időzítőket* ütemezhetnek a késések megvalósításához, illetve az aszinkron műveletek időtúllépési kezelésére vonatkozóan. A `Thread.Sleep` `Task.Delay` (C#) és a `setTimeout()` `setInterval()` (JavaScript) vagy a `time.sleep()` (Python) helyett használjon tartós időzítőket a Orchestrator függvényekben.

További információért és példákért tekintse meg a [tartós időzítők](durable-functions-timers.md) című cikket.

### <a name="external-events"></a>Külső események

A Orchestrator függvények megvárhatják, hogy külső események frissítsenek egy előkészítési példányt. Ez a Durable Functions funkció gyakran hasznos az emberi interakciók vagy más külső visszahívások kezeléséhez.

További információkat és példákat a [külső események](durable-functions-external-events.md) című cikkben talál.

### <a name="error-handling"></a>Hibakezelés

A Orchestrator függvények használhatják a programozási nyelv hibakezelés funkcióit. A meglévő mintázatok, például a hangfelismerési `try` / `catch` kódban támogatottak.

A Orchestrator függvények újrapróbálkozási házirendeket is hozzáadhatnak a meghívott tevékenységekhez vagy Orchestrator függvényekhez. Ha egy tevékenység vagy egy alorchestratoron függvény kivételt jelez, a megadott újrapróbálkozási szabályzat automatikusan késleltetheti a műveletet, és a megadott számú alkalommal újra próbálkozhat a végrehajtással.

> [!NOTE]
> Ha egy Orchestrator-függvény nem kezelt kivételt ad meg, a koordináló példány egy állapotban fejeződik be `Failed` . Nem lehet újrapróbálkozni egy összehangoló példányban, ha az sikertelen volt.

További információt és példákat [a hibakezelés című cikkben talál](durable-functions-error-handling.md) .

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Kritikus fejezetek (Durable Functions 2. x, jelenleg csak .NET)

Az előkészítési példányok egyszálas, ezért nem kell aggódnia a versenyhelyzet feltételein *belül* . A versenyhelyzet azonban akkor is lehetséges, ha az előkészítési folyamat külső rendszerekkel kommunikál. Ha a külső rendszerekkel való interakció során csökkenteni szeretné a versenyhelyzet feltételeit, a Orchestrator függvények *kritikus szakaszt* határoznak meg a `LockAsync` .net-beli metódusok használatával.

Az alábbi mintakód egy Orchestrator függvényt mutat be, amely egy kritikus szakaszt határoz meg. A módszer használatával a kritikus szakasz lép életbe `LockAsync` . Ehhez a metódushoz egy vagy több, egy [tartós entitásra](durable-functions-entities.md)mutató hivatkozást kell átadni, amely tartósan kezeli a zárolási állapotot. Ennek a folyamatnak csak egyetlen példánya tudja végrehajtani a kódot a kritikus szakaszban.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

A `LockAsync` felvásárolja a tartós zárolás (oka) t, és visszaadja a `IDisposable` kritikus szakaszt a használatból. Ez `IDisposable` az eredmény együtt használható egy `using` blokkal a kritikus szakasz szintaktikai ábrázolásának beolvasásához. Ha egy Orchestrator függvény kritikus szakaszt ad meg, csak egy példány hajthatja végre ezt a kódot. Minden más, a kritikus szakaszt beírni próbáló példány le lesz tiltva, amíg az előző példány nem kilép a kritikus szakaszból.

A kritikus szakasz funkció a tartós entitások változásainak koordinálására is használható. A kritikus szakaszokkal kapcsolatos további információkért tekintse meg a [tartós entitások "entitások egyeztetése"](durable-functions-entities.md#entity-coordination) című témakört.

> [!NOTE]
> A kritikus fejezetek Durable Functions 2,0-es és újabb verziókban érhetők el. Jelenleg csak a .NET-eszközök implementálják ezt a funkciót.

### <a name="calling-http-endpoints-durable-functions-2x"></a>HTTP-végpontok hívása (Durable Functions 2. x)

A Orchestrator függvények nem engedélyezettek az I/O-műveletek esetében, ahogy azt a [Orchestrator-függvény kódjainak megkötései](durable-functions-code-constraints.md)című témakör írja le. Ennek a korlátozásnak a leggyakoribb megkerülő megoldás, ha olyan kódokat szeretne becsomagolni, amelyeknek az I/O-műveletekre van szükségük A külső rendszerekkel kommunikáló Összehangolók gyakran használják a tevékenységi funkciókat a HTTP-hívások elvégzéséhez, és az eredménynek az előkészítéshez való visszaküldését.

# <a name="c"></a>[C#](#tab/csharp)

Az általános minta leegyszerűsítése érdekében a Orchestrator függvények a `CallHttpAsync` metódus használatával közvetlenül is meghívhatják a http API-kat.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    res = yield context.call_http('GET', url)
    if res.status_code >= 400:
        # handing of error code goes here
```
---

Az alapszintű kérelmek/válaszok támogatása mellett a metódus támogatja a gyakori aszinkron HTTP 202 lekérdezési minták automatikus kezelését, valamint a [felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md)használatával támogatja a külső szolgáltatásokkal való hitelesítést is.

További információt és részletes példákat a [http-szolgáltatások](durable-functions-http-features.md) című cikkben talál.

> [!NOTE]
> A HTTP-végpontok közvetlenül a Orchestrator függvényekből való meghívása Durable Functions 2,0-es vagy újabb verzióban érhető el.

### <a name="passing-multiple-parameters"></a>Több paraméter átadása

Nem lehet átadni több paramétert egy tevékenységi függvénynek közvetlenül. A javaslat objektumok vagy összetett objektumok tömbjét adja át.

# <a name="c"></a>[C#](#tab/csharp)

A .NET-ben használhat [ValueTuple](/dotnet/csharp/tuples) objektumokat is. A következő minta a [ValueTuple](/dotnet/csharp/tuples) új funkcióit használja a [C# 7](/dotnet/csharp/whats-new/csharp-7#tuples)használatával:

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="getweather-activity"></a>`GetWeather` Tevékenység

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="orchestrator"></a>Orchestrator

```python
from collections import namedtuple
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    Location = namedtuple('Location', ['city', 'state'])
    location = Location(city='Seattle', state= 'WA')

    weather = yield context.call_activity("GetWeather", location)

    # ...

```
#### <a name="getweather-activity"></a>`GetWeather` Tevékenység

```python
from collections import namedtuple

Location = namedtuple('Location', ['city', 'state'])

def main(location: Location) -> str:
    city, state = location
    return f"Hello {city}, {state}!"
```

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Vezénylői kódkorlátozások](durable-functions-code-constraints.md)
