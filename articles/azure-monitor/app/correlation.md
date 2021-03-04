---
title: Az Azure Application Insights telemetria korrelációja | Microsoft Docs
description: Application Insights telemetria korreláció
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: beaeb0131a2c9b326d663f6fcbb8273a9b52b412
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100967"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetria korreláció a Application Insightsban

A szolgáltatások világában minden logikai művelet végrehajtásához a szolgáltatás különböző összetevőinek kell működniük. Ezeket az összetevőket külön is figyelheti [Application Insights](../../azure-monitor/app/app-insights-overview.md)használatával. A Application Insights támogatja az elosztott telemetria korrelációt, amelyet annak észlelésére használ, hogy melyik összetevő felelős a hibákért vagy a teljesítmény romlásához.

Ez a cikk a Application Insights által a több összetevő által elküldett telemetria korrelációt okozó adatmodellt ismerteti. Magában foglalja a kontextus-propagálási technikákat és protokollokat. Emellett a korrelációs taktikák megvalósítását is magában foglalja a különböző nyelveken és platformokon.

## <a name="data-model-for-telemetry-correlation"></a>Telemetria korrelációs adatmodell

Application Insights definiál egy [adatmodellt](../../azure-monitor/app/data-model.md) az elosztott telemetria korrelációhoz. A telemetria logikai művelettel való hozzárendeléséhez minden telemetria-elemhez tartozik egy nevű környezeti mező `operation_Id` . Ezt az azonosítót az elosztott nyomkövetés minden telemetria-eleme megosztja. Így még akkor is, ha egyetlen rétegből elveszíti a telemetria, a többi összetevő által jelentett telemetria is hozzárendelheti.

Az elosztott logikai műveletek jellemzően olyan kisebb műveletekből állnak, amelyek az egyik összetevő által feldolgozott kérelmek. Ezeket a műveleteket a [kérelem telemetria](../../azure-monitor/app/data-model-request-telemetry.md)határozza meg. Minden kérelem telemetria-eleme rendelkezik a saját `id` egyedi és globális azonosítására. A kérelemhez társított összes telemetria-elemnek (például a nyomkövetéseknek és kivételeknek) a kérelem értékét kell beállítania `operation_parentId` `id` .

Minden kimenő művelet, például egy HTTP-hívás egy másik összetevőhöz, a [függőségi telemetria](../../azure-monitor/app/data-model-dependency-telemetry.md)szerint jelenik meg. A függőségi telemetria a `id` globálisan egyedit is definiálja. A függőségi hívás által kezdeményezett telemetria kérelme ezt használja `id` `operation_parentId` .

Az elosztott logikai művelet nézetét a `operation_Id` , `operation_parentId` `request.id` a és a használatával is létrehozhatja `dependency.id` . Ezek a mezők a telemetria-hívások oksági sorrendjét is meghatározzák.

A Service-környezetekben az összetevőkből származó nyomkövetési adatok különböző tárolóhelyekre léphetnek. Minden összetevő rendelkezhet saját kialakítási kulccsal Application Insightsban. A logikai művelet telemetria lekéréséhez Application Insights minden tárolási elemről lekérdezi az adatait. Ha a tárolási elemek száma nagy, szüksége lesz egy tippre, ahol a következőt kell megkeresnie. A Application Insights adatmodell két mezőt definiál a probléma megoldásához: `request.source` és `dependency.target` . Az első mező azonosítja a függőségi kérelmet kezdeményező összetevőt. A második mező azonosítja, hogy melyik összetevő adta vissza a függőségi hívás válaszát.

## <a name="example"></a>Példa

Lássunk erre egy példát. A tőzsdei díjak nevű alkalmazás a készlet aktuális piaci árát jeleníti meg egy Stock nevű külső API használatával. A tőzsdei díjszabási alkalmazás rendelkezik egy Stock oldal nevű oldallal, amelyet az ügyfél webböngészője a használatával nyit meg `GET /Home/Stock` . Az alkalmazás a HTTP-hívás használatával kérdezi le a Stock API-t `GET /api/stock/value` .

Az eredményül kapott telemetria a következő lekérdezés futtatásával elemezheti:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Az eredmények között vegye figyelembe, hogy az összes telemetria-elem megosztja a gyökeret `operation_Id` . Ha a lapon Ajax-hívás történik, a rendszer új egyedi azonosítót ( `qJSXU` ) rendel a függőségi telemetria, és az OLDALMEGTEKINTÉS azonosítóját használja `operation_ParentId` . A kiszolgálói kérelem ezután az Ajax ID-t használja `operation_ParentId` .

| itemType   | name                      | ID (Azonosító)           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| oldalmegtekintést   | Stock lap                | STYz         |                    | STYz         |
| függőségi | /Home/Stock beolvasása           | qJSXU        | STYz               | STYz         |
| kérelem    | Kezdőlap/készlet letöltése            | KqKwlrSt9PA = | qJSXU              | STYz         |
| függőségi | /API/Stock/Value beolvasása      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Ha a hívás `GET /api/stock/value` egy külső szolgáltatásra történik, ismernie kell a kiszolgáló identitását, hogy megfelelően be tudja állítani a `dependency.target` mezőt. Ha a külső szolgáltatás nem támogatja a figyelést, a a `target` szolgáltatás állomásneve (például) értékre van állítva `stock-prices-api.com` . Ha azonban a szolgáltatás egy előre meghatározott HTTP-fejléc visszaadásával azonosítja magát, `target` az tartalmazza azt a szolgáltatás-identitást, amely lehetővé teszi, hogy a Application Insights egy elosztott nyomkövetést hozzon létre a szolgáltatásból származó telemetria lekérdezésével.

## <a name="correlation-headers-using-w3c-tracecontext"></a>Korrelációs fejlécek a W3C nyomkövetési környezet – használatával

Application Insights a [W3C nyomkövetési kontextusra](https://w3c.github.io/trace-context/)vált, amely az alábbiakat határozza meg:

- `traceparent`: A hívás globálisan egyedi műveleti AZONOSÍTÓját és egyedi azonosítóját is végrehajtja.
- `tracestate`: Rendszerspecifikus nyomkövetési környezetet végez.

A Application Insights SDK legújabb verziója támogatja a Trace-Context protokollt, de előfordulhat, hogy be kell jelentkeznie. (A Application Insights SDK által támogatott korábbi korrelációs protokoll visszamenőleges kompatibilitása továbbra is fennáll.)

A [korrelációs HTTP protokoll, más néven a Request-ID is](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)elavult. Ez a protokoll két fejlécet definiál:

- `Request-Id`: A hívás globálisan egyedi AZONOSÍTÓját hordozza.
- `Correlation-Context`: Az elosztott nyomkövetési tulajdonságok név-érték párokból álló gyűjteményét adja meg.

A Application Insights a korrelációs HTTP protokoll [bővítményét](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) is meghatározza. `Request-Context`Név-érték párokat használ a közvetlen hívó vagy a hívott által használt tulajdonságok gyűjteményének propagálásához. A Application Insights SDK ezt a fejlécet használja a `dependency.target` és a mezők beállításához `request.source` .

A [W3C nyomkövetési környezet](https://w3c.github.io/trace-context/) és a Application Insights adatmodellek leképezése a következő módon történik:

| Application Insights                   | W3C nyomkövetési környezet –                                      |
|------------------------------------    |-------------------------------------------------|
| `Id``Request`és`Dependency`     | [szülő-azonosító](https://w3c.github.io/trace-context/#parent-id)                                     |
| `Operation_Id`                         | [nyomkövetési azonosító](https://w3c.github.io/trace-context/#trace-id)                                           |
| `Operation_ParentId`                   | a span szülő-tartományának [szülő-azonosítója](https://w3c.github.io/trace-context/#parent-id) . Ha ez egy gyökérszintű tartomány, akkor ennek a mezőnek üresnek kell lennie.     |

További információ: [Application Insights telemetria adatmodell](../../azure-monitor/app/data-model.md).

### <a name="enable-w3c-distributed-tracing-support-for-net-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése a .NET-alkalmazások számára

A W3C nyomkövetési környezet –-alapú elosztott nyomkövetés alapértelmezés szerint engedélyezve van az összes legújabb .NET-keretrendszerben/. NET Core SDK-ban, valamint visszamenőleges kompatibilitással az örökölt Request-Id protokollal.

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése Java-alkalmazásokhoz

#### <a name="java-30-agent"></a>Java 3,0-ügynök

  A Java 3,0-ügynök támogatja a W3C-t a box-ban, és nincs szükség további konfigurálásra. 

#### <a name="java-sdk"></a>Java SDK
- **Bejövő konfiguráció**

  - Java EE-alkalmazások esetén adja hozzá a következőt a `<TelemetryModules>` címkéhez ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```

  - A Spring boot-alkalmazások esetében adja hozzá a következő tulajdonságokat:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Kimenő konfiguráció**

  Adja hozzá a következőt a AI-Agent.xmlhoz:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > A visszafelé kompatibilitási mód alapértelmezés szerint engedélyezve van, és a `enableW3CBackCompat` paraméter nem kötelező. Csak akkor használja, ha a visszafelé való kompatibilitást ki szeretné kapcsolni.
  >
  > Ideális esetben ezt ki kell kapcsolni, ha az összes szolgáltatás frissítve lett a W3C protokollt támogató SDK-k újabb verzióira. Javasoljuk, hogy a lehető leghamarabb váltson át ezekre az újabb SDK-kat.

> [!IMPORTANT]
> Győződjön meg arról, hogy a bejövő és a kimenő konfigurációk pontosan ugyanazok.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>A W3C elosztott nyomkövetési támogatásának engedélyezése a Web Apps számára

Ez a szolgáltatás a-ben érhető el `Microsoft.ApplicationInsights.JavaScript` . Alapértelmezés szerint le van tiltva. Az engedélyezéséhez használja a `distributedTracingMode` config parancsot. A AI_AND_W3C a Application Insights által kiállított örökölt szolgáltatásokkal való visszamenőleges kompatibilitás érdekében van megadva.

- **[NPM-alapú telepítés](./javascript.md#npm-based-setup)**

Adja hozzá a következő konfigurációt:
  ```JavaScript
    distributedTracingMode: DistributedTracingModes.W3C
  ```

- **[Kódrészlet-alapú telepítés](./javascript.md#snippet-based-setup)**

Adja hozzá a következő konfigurációt:
  ```
      distributedTracingMode: 2 // DistributedTracingModes.W3C
  ```
> [!IMPORTANT] 
> A korreláció engedélyezéséhez szükséges összes konfiguráció megtekintéséhez tekintse meg a [JavaScript korrelációs dokumentációját](./javascript.md#enable-correlation).

## <a name="telemetry-correlation-in-opencensus-python"></a>Telemetria korreláció a OpenCensus Pythonban

A OpenCensus Python további konfigurálás nélkül támogatja a [W3C nyomkövetési környezetet](https://w3c.github.io/trace-context/) .

Hivatkozásként a OpenCensus adatmodellje [itt](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace)található.

### <a name="incoming-request-correlation"></a>Bejövő kérelmek korrelációja

A OpenCensus Python összekapcsolja a W3C Trace-Context fejléceket a beérkező kérelmektől a kérelmekből generált felölelő adatoktól. A OpenCensus ezt a népszerű webalkalmazás-keretrendszer integrációs szolgáltatásával automatikusan elvégzi: a lombik, a Django és a piramis. Csak a W3C Trace-Context fejléceket kell megadnia a [megfelelő formátumban](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) , és el kell küldenie őket a kérelemmel. Íme egy példa a lombik alkalmazásra, amely a következőket mutatja be:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Ez a kód egy minta lombik-alkalmazást futtat a helyi gépen, és figyeli a portot `8080` . A nyomkövetési kontextus összekapcsolásához egy kérést küld a végpontnak. Ebben a példában a következő `curl` parancsot használhatja:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
A [nyomkövetési kontextus fejlécének formátuma](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)alapján a következő információk származhatnak:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Ha megtekinti a Azure Monitorba küldendő kérelem bejegyzést, a nyomkövetési fejléc információit tartalmazó mezőket láthat. Ezeket az adatnaplókat (Analitika) a Azure Monitor Application Insights erőforrásban találja.

![Telemetria kérése a naplókban (Analitika)](./media/opencensus-python/0011-correlation.png)

A `id` mező formátuma `<trace-id>.<span-id>` , ahol a a `trace-id` kérelemben átadott nyomkövetési fejlécből származik, és a `span-id` érték a generált 8 bájtos tömb ehhez a tartományhoz.

A `operation_ParentId` mező formátuma `<trace-id>.<parent-id>` , ahol a `trace-id` és a `parent-id` is a kérelemben átadott nyomkövetési fejlécből származik.

### <a name="log-correlation"></a>Napló-összefüggések

A OpenCensus Python lehetővé teszi a naplók összekapcsolását egy nyomkövetési azonosító, egy span-azonosító és egy mintavételi jelző hozzáadásával a rekordok naplózásához. Ezeket az attribútumokat a OpenCensus [naplózási integrációjának](https://pypi.org/project/opencensus-ext-logging/)telepítésével adhatja hozzá. A következő attribútumok lesznek hozzáadva a Python- `LogRecord` objektumokhoz: `traceId` , `spanId` és `traceSampled` . Vegye figyelembe, hogy ez csak az integráció után létrehozott naplózók esetén lép életbe.

Íme egy példa az alkalmazásra, amely a következőket mutatja be:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
A kód futtatásakor a következő nyomatok jelennek meg a konzolon:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Figyelje meg, hogy a `spanId` naplózási üzenetnek van jelen a tartományon belül. Ez ugyanaz, mint `spanId` a nevű tartományhoz `hello` .

A naplózási adatai a használatával exportálhatók `AzureLogHandler` . További információkért tekintse meg [ezt a cikket](./opencensus-python.md#logs).

A megfelelő korreláció érdekében a nyomkövetési adatokat is átadhatjuk az egyik összetevőből a másikba. Vegyünk például egy olyan forgatókönyvet, amelyben két összetevő van `module1` és `module2` . A Module1 a Module2 függvényeit hívja meg, és egyetlen nyomkövetésből is beolvashatja a naplókat a `module1` `module2` következő módszer használatával:

```python
# module1.py
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer
from module2 import function_1

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
   logger.warning('In the span')
   function_1(tracer)
logger.warning('After the span')


# module2.py

import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

def function_1(parent_tracer=None):
    if parent_tracer is not None:
        tracer = Tracer(
                    span_context=parent_tracer.span_context,
                    sampler=AlwaysOnSampler(),
                )
    else:
        tracer = Tracer(sampler=AlwaysOnSampler())

    with tracer.span("function_1"):
        logger.info("In function_1")
```

## <a name="telemetry-correlation-in-net"></a>Telemetria korreláció a .NET-ben

A .NET-futtatókörnyezet támogatott a [tevékenység](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) -és [DiagnosticSource](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) segítségével

A Application Insights .NET SDK a `DiagnosticSource` telemetria-t használja, és `Activity` összegyűjti és korrelálja.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Telemetria korreláció a javában

A [Java-ügynök](./java-in-process-agent.md) , valamint a [Java SDK](../../azure-monitor/app/java-get-started.md) 2.0.0 vagy újabb verziója támogatja a telemetria automatikus korrelációját. A rendszer automatikusan kitölti az `operation_id` összes telemetria (például a nyomkövetéseket, a kivételeket és az egyéni eseményeket) a kérelem hatókörén belül. Emellett a HTTP protokollon keresztül terjeszti a korrelációs fejléceket (lásd fentebb) a szolgáltatások közötti hívásokhoz, ha a [Java SDK-ügynök](../../azure-monitor/app/java-agent.md) konfigurálva van.

> [!NOTE]
> Application Insights Java-ügynök automatikusan gyűjti a JMS, a Kafka, a nettó és a webflux szolgáltatáshoz kapcsolódó kéréseket és függőségeket, és így tovább. A Java SDK-hoz csak az Apache HttpClient keresztül kezdeményezett hívások támogatottak a korrelációs szolgáltatásban. Az SDK-ban az automatikus környezeti propagálás nem támogatott az üzenetkezelési technológiák (például a Kafka, a RabbitMQ és a Azure Service Bus) között. 

> [!NOTE]
> Egyéni telemetria gyűjtéséhez az alkalmazást Java 2,6 SDK-val kell kidolgoznia. 

### <a name="role-names"></a>Szerepkörök nevei

Érdemes lehet testreszabni, ahogy az összetevők nevei megjelennek az alkalmazás- [hozzárendelésben](../../azure-monitor/app/app-map.md). Ehhez manuálisan is beállíthatja a `cloud_RoleName` következő műveletek egyikét:

- Application Insights Java-ügynök 3,0-as verziójában állítsa be a felhőalapú szerepkör nevét a következőképpen:

    ```json
    {
      "role": {
        "name": "my cloud role name"
      }
    }
    ```
    A Felhőbeli szerepkör nevét a környezeti változó használatával is beállíthatja `APPLICATIONINSIGHTS_ROLE_NAME` .

- A Application Insights Java SDK 2.5.0-es és újabb verzióiban megadhatja az `cloud_RoleName` `<RoleName>` ApplicationInsights.xml fájl hozzáadásával:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Ha rugós rendszerindítást használ a Application Insights Spring boot Starter-vel, csak be kell állítania az alkalmazás egyéni nevét az Application. properties fájlban:

  `spring.application.name=<name-of-app>`

  A Spring boot Starter automatikusan hozzárendeli `cloudRoleName` a `spring.application.name` tulajdonsághoz megadott értéket.

## <a name="next-steps"></a>Következő lépések

- [Egyéni telemetria](../../azure-monitor/app/api-custom-events-metrics.md)írása.
- A ASP.NET Core-és ASP.NET speciális korrelációs forgatókönyvei: [Egyéni műveletek nyomon követése](custom-operations-tracking.md).
- További információ a más SDK-k [cloud_RoleName beállításáról](./app-map.md#set-or-override-cloud-role-name) .
- A Application Insights összes összetevőjének bevezetését. Tekintse meg a [támogatott platformokat](./platforms.md).
- Tekintse meg Application Insights típusok [adatmodelljét](./data-model.md) .
- Ismerje meg [, hogyan bővítheti és szűrheti a telemetria](./api-filtering-sampling.md).
- Tekintse át a [Application Insights konfigurációs referenciát](configuration-with-applicationinsights-config.md).