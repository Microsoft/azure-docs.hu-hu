---
title: Azure Functions figyelésének konfigurálása
description: Megtudhatja, hogyan csatlakoztatható a Function app Application Insights a figyeléshez, és hogyan konfigurálhatja az adatgyűjtést.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: e24f2b1a61d77dafd7a23b04d225d0301f82ca59
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070140"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>A Azure Functions figyelésének konfigurálása

A Azure Functions együttműködik a Application Insightsekkel, hogy jobban megfigyelje a Function apps-alkalmazásokat. Application Insights a Azure Monitor egy funkciója, amely egy bővíthető Application Performance Management-(APM-) szolgáltatás, amely a Function alkalmazás által generált adatokat gyűjti, beleértve az alkalmazás által a naplókba írt adatokat is. A Application Insights integráció általában engedélyezve van a Function alkalmazás létrehozásakor. Ha az alkalmazás nem rendelkezik a kialakítási kulccsal, először [engedélyeznie kell Application Insights integrációt](#enable-application-insights-integration). 

A Application Insightst egyéni konfiguráció nélkül is használhatja. Az alapértelmezett konfiguráció nagy mennyiségű adattal járhat. Ha Visual Studio Azure-előfizetést használ, előfordulhat, hogy a Application Insightshoz tartozó adatkorlátot is elérheti. Ha többet szeretne megtudni a Application Insights költségekről, tekintse meg [a Application Insights használatának és költségeinek kezelésével foglalkozó](../azure-monitor/app/pricing.md)témakört.

A cikk későbbi részében megtudhatja, hogyan konfigurálhatja és testre szabhatja a függvények által Application Insightsba küldött adatok konfigurálását. Egy Function alkalmazás esetében a naplózás a fájl [host.js] van konfigurálva. 

> [!NOTE]
> A speciálisan konfigurált Alkalmazásbeállítások használatával meghatározott beállításokat adhat meg egy adott környezet host.jsfájljában. Ezzel a beállítással gyakorlatilag módosíthatja host.jsbeállításait anélkül, hogy újra közzé kellene tennie a projektben lévő fájl host.js. További információ: [host.jsfelülbírálása az értékeken](functions-host-json.md#override-hostjson-values).

## <a name="configure-categories"></a>Kategóriák konfigurálása

A Azure Functions Logger minden naplóhoz tartalmaz *kategóriát* . A kategória azt jelzi, hogy a futásidejű kód mely része vagy a függvény kódja írta a naplót. A kategóriák eltérnek az 1. x és újabb verziókban. A következő diagram a futtatókörnyezet által létrehozott naplók fő kategóriáit ismerteti. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

| Kategória | Táblázat | Leírás |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **függőségek**| Egyes szolgáltatások esetében a rendszer automatikusan gyűjti a függőségi adatokat. A sikeres futtatáshoz ezek a naplók a `Information` szinten vannak. További információ: [függőségek](functions-monitoring.md#dependencies). A kivételek naplózása a `Error` szinten történik. A futtatókörnyezet `Warning` olyan szintű naplókat is létrehoz, mint az üzenetsor-üzenetek elküldése a [méreg várólistára](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | A C# és a JavaScript SDK-k lehetővé teszik egyéni metrikák gyűjtését és egyéni események naplózását. További információ: [Egyéni telemetria-információk](functions-monitoring.md#custom-telemetry-data).|
| **`Function.<YOUR_FUNCTION_NAME>`** | **nyomok**| Tartalmazza a függvény elindított és befejezett naplóit az adott függvény futtatásához. A sikeres futtatáshoz ezek a naplók a `Information` szinten vannak. A kivételek naplózása a `Error` szinten történik. A futtatókörnyezet `Warning` olyan szintű naplókat is létrehoz, mint az üzenetsor-üzenetek elküldése a [méreg várólistára](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **nyomok**| A felhasználó által létrehozott naplók, amelyek bármilyen naplózási szint lehet. Ha többet szeretne megtudni a függvények naplóiba való írásról, olvassa el a következő témakört: [írás a naplókba](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Ezek a futtatókörnyezet által generált naplók számokkal és átlagosan [konfigurálható](#configure-the-aggregator) időszakra vonatkozó függvényeket biztosítanak. Az alapértelmezett időtartam 30 másodperc vagy 1 000 eredmény, attól függően, hogy melyik következik be először. Ilyenek például a futtatások száma, a sikerességi arány és az időtartam. Ezen naplók mindegyike szinten van írva `Information` . Ha a `Warning` -t vagy a fentit szűri, akkor nem jelenik meg ezek az információk. |
| **`Host.Results`** | **kérések** | Ezek a futtatókörnyezet által generált naplók a függvények sikerességét vagy hibáját jelzik. Ezen naplók mindegyike szinten van írva `Information` . Ha a `Warning` -t vagy a fentit szűri, akkor nem jelenik meg ezek az információk. |
| **`Microsoft`** | **nyomok** | Teljesen minősített naplózási kategória, amely a gazdagép által meghívott .NET-futtatókörnyezeti összetevőt tükrözi.  |
| **`Worker`** | **nyomok** | A non-.NET nyelveken a nyelv munkavégző folyamata által létrehozott naplók. A nyelvi feldolgozói naplók is bekerülhetnek egy `Microsoft.*` kategóriába, például: `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` . Ezek a naplók szinten vannak írva `Information` .|

> [!NOTE]
> A .NET Class Library függvények esetében ezek a kategóriák feltételezik, hogy Ön a-t használja, `ILogger` és nem `ILogger<T>` . További információt a [functions ILogger dokumentációjában](functions-dotnet-class-library.md#ilogger)talál. 

# <a name="v1x"></a>[v1. x](#tab/v1)

| Kategória | Táblázat | Leírás |
| ----- | ----- | ----- |
| **`Function`** | **nyomok**| A felhasználó által létrehozott naplók, amelyek bármilyen naplózási szint lehet. Ha többet szeretne megtudni a függvények naplóiba való írásról, olvassa el a következő témakört: [írás a naplókba](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Ezek a futtatókörnyezet által generált naplók számokkal és átlagosan [konfigurálható](#configure-the-aggregator) időszakra vonatkozó függvényeket biztosítanak. Az alapértelmezett időtartam 30 másodperc vagy 1 000 eredmény, attól függően, hogy melyik következik be először. Ilyenek például a futtatások száma, a sikerességi arány és az időtartam. Ezen naplók mindegyike szinten van írva `Information` . Ha a `Warning` -t vagy a fentit szűri, akkor nem jelenik meg ezek az információk. |
| **`Host.Executor`** | **nyomok** | Magában foglalja az **elindított függvényt** , és az adott függvény futtatásához tartozó **befejezett** naplókat. A sikeres futtatáshoz ezek a naplók `Information` szint. A kivételek naplózása `Error` szinten történik. A futtatókörnyezet `Warning` olyan szintű naplókat is létrehoz, mint az üzenetsor-üzenetek elküldése a [méreg várólistára](functions-bindings-storage-queue-trigger.md#poison-messages).  |
| **`Host.Results`** | **kérések** | Ezek a futtatókörnyezet által generált naplók a függvények sikerességét vagy hibáját jelzik. Ezen naplók mindegyike szinten van írva `Information` . Ha a `Warning` -t vagy a fentit szűri, akkor nem jelenik meg ezek az információk. |

---

A **Table (tábla** ) oszlopban látható, hogy a napló Application Insights melyik táblára van írva. 

## <a name="configure-log-levels"></a>Naplózási szintek konfigurálása

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Minden kategória esetében meg kell határozni a küldéshez minimálisan szükséges naplózási szintet. A beállítások host.jsa [functions Runtime verziójától](functions-versions.md)függően változnak. 

Az alábbi példa a naplózást a következő szabályok alapján határozza meg:

+ A (z) vagy rendszerű naplók esetében `Host.Results` `Function` csak az események naplózása a `Error` vagy magasabb szinten. 
+ A naplóknál `Host.Aggregator` naplózza az összes generált metrikát ( `Trace` ).
+ Az összes többi naplóhoz, beleértve a felhasználói naplókat, csak naplózási `Information` szintet és magasabb eseményeket.

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

Ha [host.jsbekapcsolása] több olyan naplót tartalmaz, amelyek ugyanazzal a karakterlánccal kezdődnek, akkor a rendszer először a több definiált naplót is egyezteti. Vegye figyelembe a következő példát, amely a futtatókörnyezet összes elemét naplózza, kivéve `Host.Aggregator` a `Error` szintet:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

A naplózási szint beállításával `None` megakadályozhatja, hogy a naplók egy adott kategóriához legyenek írva. 

## <a name="configure-the-aggregator"></a>A gyűjtő konfigurálása

Ahogy az előző szakaszban is látható, a futtatókörnyezet összesíti a függvények végrehajtásával kapcsolatos adatokat egy adott időszakban. Az alapértelmezett időtartam 30 másodperc vagy 1 000 fut, amelyik előbb eléri a értéket. Ezt a beállítást a fájl [host.js] is konfigurálhatja.  Bemutatunk egy példát:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Mintavételezés konfigurálása

Application Insights tartalmaz egy [mintavételi](../azure-monitor/app/sampling.md) funkciót, amely képes arra, hogy túl sok telemetria-adatmennyiséget állítson elő a befejezett végrehajtásokon a maximális terhelés idején. Ha a bejövő végrehajtások aránya meghaladja a megadott küszöbértéket, Application Insights véletlenszerűen figyelmen kívül hagyja a bejövő végrehajtások némelyikét. A másodpercenkénti végrehajtások maximális számának alapértelmezett beállítása 20 (öt az 1. x verzióban). A mintavételezésthost.js- [ on](./functions-host-json.md#applicationinsights)is konfigurálhatja.  Bemutatunk egy példát:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


Bizonyos típusú telemetria kizárhat a mintavételezésből. Ebben a példában a típusú adatok `Request` és a `Exception` mintavételezésből ki vannak zárva. Ez gondoskodik arról, hogy az *összes* függvény végrehajtásának (kérésének) és a kivételek naplózása megtörténjen, míg más típusú telemetria továbbra is mintavételezés alá esnek. 

# <a name="v1x"></a>[v1. x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

További információ: [mintavétel Application Insightsban](../azure-monitor/app/sampling.md).

## <a name="configure-scale-controller-logs"></a>A skálázási vezérlő naplófájljainak konfigurálása

_Ez a funkció előzetes verzióban érhető el._ 

A [Azure functions méretezési vezérlő](./event-driven-scaling.md#runtime-scaling) a naplókat Application Insights vagy blob Storage-ba is kibocsáthatja, így jobban megismerheti a méretezési vezérlő által a Function alkalmazásra vonatkozó döntéseket.

A szolgáltatás engedélyezéséhez vegyen fel egy nevű Alkalmazásbeállítás `SCALE_CONTROLLER_LOGGING_ENABLED` -beállítást a Function app-beállításokhoz. Ennek a beállításnak a formátuma a `<DESTINATION>:<VERBOSITY>` következők alapján kell, hogy legyen:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Például a következő Azure CLI-parancs bekapcsolja a méretezési vezérlő részletes naplózását a Application Insightsra:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

Ebben a példában cserélje le a `<FUNCTION_APP_NAME>` és a `<RESOURCE_GROUP_NAME>` nevet a Function alkalmazás nevére, illetve az erőforráscsoport nevét. 

A következő Azure CLI-parancs letiltja a naplózást a részletesség beállításával `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

A naplózást a `SCALE_CONTROLLER_LOGGING_ENABLED` következő Azure CLI-parancs használatával is letilthatja a beállítás eltávolításával:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="enable-application-insights-integration"></a>Application Insights-integráció engedélyezése

Ahhoz, hogy egy Function alkalmazás adatküldést Application Insights, ismernie kell egy Application Insights erőforrás rendszerállapot-kulcsát. A kulcsnak egy **APPINSIGHTS_INSTRUMENTATIONKEY** nevű alkalmazás-beállításban kell lennie.

Ha a [Azure Portal](./functions-get-started.md)hozza létre a Function alkalmazást, a parancssorból [Azure functions Core Tools](./create-first-function-cli-csharp.md)vagy [Visual Studio Code](./create-first-function-vs-code-csharp.md)használatával, Application Insights az integráció alapértelmezés szerint engedélyezve van. A Application Insights erőforrás neve megegyezik a Function alkalmazás nevével, és az ugyanabban a régióban vagy a legközelebbi régióban jön létre.

### <a name="new-function-app-in-the-portal"></a>Új Function-alkalmazás a portálon

A létrehozandó Application Insights-erőforrás áttekintéséhez válassza ki azt a **Application Insights** ablak kibontásához. Módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** az [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol az adatait tárolni szeretné.

![Application Insights engedélyezése a Function app létrehozásakor](media/functions-monitoring/enable-ai-new-function-app.png)

Ha a **Létrehozás** lehetőséget választja, a rendszer létrehoz egy Application Insights erőforrást a Function alkalmazással, amely a `APPINSIGHTS_INSTRUMENTATIONKEY` set in Application settings (alkalmazás beállításai) beállítással rendelkezik. Minden készen áll.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Hozzáadás meglévő Function-alkalmazáshoz 

Ha egy Application Insights erőforrás nem lett létrehozva a Function alkalmazással, a következő lépésekkel hozza létre az erőforrást. Ezután hozzáadhatja a kialakítási kulcsot az adott erőforrásból a Function [alkalmazásban](functions-how-to-use-azure-function-app-settings.md#settings) .

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a **Function app** elemet, majd válassza ki a Function alkalmazást. 

1. Válassza ki az ablak tetején található **Az Application Insights nincs konfigurálva** szalagot. Ha nem látja ezt a szalagcímet, előfordulhat, hogy az alkalmazás már Application Insights engedélyezve van.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Az Application Insights engedélyezése a portálon":::

1. Bontsa ki az **erőforrás módosítása** elemet, és hozzon létre egy Application Insights-erőforrást az alábbi táblázatban megadott beállítások használatával.  

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Új erőforrásnév** | Egyedi alkalmazásnév | A legegyszerűbb, ha a függvényalkalmazás nevét használja, amelynek egyedinek kell lennie az előfizetésben. | 
    | **Hely** | Nyugat-Európa | Ha lehetséges, használja a függvényalkalmazás [régióját](https://azure.microsoft.com/regions/) vagy egy ahhoz közeli régiót. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Application Insights-erőforrás létrehozása":::

1. Kattintson az **Alkalmaz** gombra. 

   Az Application Insights-erőforrás ugyanabban az erőforráscsoportban és előfizetésben jön létre, ahol a függvényalkalmazás található. Az erőforrás létrehozása utána zárja be az Application Insights ablakát.

1. A Function alkalmazásban válassza a **Konfigurálás** lehetőséget a **Beállítások** területen, majd válassza az **Alkalmazásbeállítások** lehetőséget. Ha az `APPINSIGHTS_INSTRUMENTATIONKEY` nevű beállítás megjelenik, akkor engedélyezve van az Application Insights-integráció az Azure-ban futó függvényalkalmazáshoz. Ha valamilyen okból kifolyólag ez a beállítás nem létezik, adja hozzá a Application Insights rendszerállapot-kulcs értékeként.

> [!NOTE]
> A függvények korábbi verziói beépített figyelést használnak, ami már nem ajánlott. Ha egy ilyen Function alkalmazáshoz Application Insights integrációt engedélyez, le kell [tiltania a beépített naplózást](#disable-built-in-logging)is.  

## <a name="query-scale-controller-logs"></a>A méretezési vezérlő naplófájljainak lekérdezése

A méretezési vezérlő naplózásának és a Application Insights integrációjának engedélyezése után a Application Insights naplóbeli kereséssel kérdezheti le a kibocsátott skálázási vezérlő naplóit. A skálázási vezérlő naplóit a `traces` gyűjtemény a **ScaleControllerLogs** kategóriában tárolja.

A következő lekérdezéssel megkeresheti az aktuális Function alkalmazáshoz tartozó összes méretezési vezérlő naplóját a megadott időtartamon belül:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
```

A következő lekérdezés kibontja az előző lekérdezést, hogy bemutassa, hogyan lehet csak a skála változását jelző naplókat beolvasni:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
| where message == "Instance count changed"
| extend Reason = CustomDimensions.Reason
| extend PreviousInstanceCount = CustomDimensions.PreviousInstanceCount
| extend NewInstanceCount = CustomDimensions.CurrentInstanceCount
```

## <a name="disable-built-in-logging"></a>A beépített naplózás letiltása

A Application Insights engedélyezésekor tiltsa le az Azure Storage-t használó beépített naplózást. A beépített naplózás hasznos a könnyű számítási feladatokkal történő teszteléshez, de nem a nagy terhelésű éles használathoz. Éles monitorozáshoz ajánlott Application Insights. Ha a beépített naplózást éles környezetben használja, előfordulhat, hogy a naplózási rekord nem fejeződött be az Azure Storage-ban való szabályozás miatt.

A beépített naplózás letiltásához törölje az `AzureWebJobsDashboard` alkalmazás beállítását. Az Alkalmazásbeállítások a Azure Portalban való törlésével kapcsolatos további információkért tekintse meg a [functions-alkalmazás kezelésével](functions-how-to-use-azure-function-app-settings.md#settings)foglalkozó témakör **Alkalmazásbeállítások** szakaszát. Az Alkalmazásbeállítások törlése előtt győződjön meg arról, hogy az azonos Function alkalmazásban lévő meglévő függvények nem használják az Azure Storage-eseményindítók és-kötések beállítását.

## <a name="next-steps"></a>Következő lépések

A figyeléssel kapcsolatos további tudnivalókért tekintse meg a következő témakört:

+ [Az Azure Functions monitorozása](functions-monitoring.md)
+ [Azure Functions telemetria-Application Insights elemzése](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.jsbekapcsolva]: functions-host-json.md
