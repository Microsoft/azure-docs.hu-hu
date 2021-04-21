---
title: Azure Monitor Application Insights Java
description: Alkalmazásteljesítmény-figyelés bármilyen környezetben futó Java-alkalmazásokhoz kódmódosítás nélkül. Elosztott nyomkövetés és alkalmazástérkép.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3f22e165fe4a3f86ecce8b1e307b19fae0eeac81
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812047"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Java kód nélküli alkalmazásfigyelési Azure Monitor Application Insights

A Java kód nélküli alkalmazásfigyelés az egyszerűségről szól – nincsenek kódváltozások, a Java-ügynök néhány konfigurációs módosítással engedélyezhető.

 A Java-ügynök bármilyen környezetben működik, és lehetővé teszi az összes Java-alkalmazás figyelését. Más szóval, függetlenül attól, hogy a Java-alkalmazásokat virtuális gépeken, a helyszínen, az AKS-ben, Windowson vagy Linuxon futtatja, a Neve legyen, a Java 3.0-ügynök figyelni fogja az alkalmazást.

A Application Insights Java SDK hozzáadása az alkalmazáshoz már nem szükséges, mivel a 3.0-s ügynök automatikusan gyűjti a kéréseket, a függőségeket és a naplókat.

Továbbra is küldhet egyéni telemetriát az alkalmazásból. A 3.0-s ügynök nyomon követi és korrelál az automatikusan gyűjtött telemetriával.

A 3.0-s ügynök támogatja a Java 8-at és a fentieket.

## <a name="quickstart"></a>Gyorsútmutató

**1. Az ügynök letöltése**

> [!WARNING]
> **Ha 3.0-s előzetes verzióról frissít**
>
> Alaposan tekintse [](./java-standalone-config.md) át az összes konfigurációs beállítást, mivel a JSON-struktúra teljesen megváltozott, és magát a fájlnevet is kisbetűvel módosította.

Az [applicationinsights-agent-3.0.3.jar letöltése](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.3/applicationinsights-agent-3.0.3.jar)

**2. A JVM mutattatása az ügynökre**

Hozzáadás `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` az alkalmazás JVM-args-hez

A JVM-args jellemzően a és a `-Xmx512m` `-XX:+UseG1GC` . Tehát ha tudja, hol kell hozzáadnia ezeket, akkor már tudja, hogy hol kell hozzáadnia.

Az alkalmazás JVM-args konfigurálásával kapcsolatos további segítségért lásd: Tippek a [JVM-args frissítéséhez.](./java-standalone-arguments.md)

**3. Az ügynök mutatása a Application Insights erőforrásra**

Ha még nem rendelkezik Application Insights erőforrással, az erőforrás-létrehozási útmutató lépéseit követve létrehozhat egy [újat.](./create-new-resource.md)

Mutasson az ügynökre Application Insights erőforrásra egy környezeti változó beállításával:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Vagy hozzon létre egy nevű konfigurációs fájlt, és helyezze a könyvtárba a következő `applicationinsights.json` `applicationinsights-agent-3.0.3.jar` tartalommal:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

A kapcsolati sztring a saját erőforrásában Application Insights található:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights sztring létrehozása":::

**4. Ennyi az!**

Most indítsa el az alkalmazást, és a Application Insights a Azure Portal meg a monitorozási adatokat.

> [!NOTE]
> Eltarthat néhány percig, hogy a monitorozási adatok megnyissnak a portálon.


## <a name="configuration-options"></a>Beállítási lehetőségek

A `applicationinsights.json` fájlban emellett a következő beállításokat is konfigurálhatja:

* Felhőbeli szerepkör neve
* Felhőbeli szerepkörpéldány
* Mintavételezés
* JMX-metrikák
* Egyéni dimenziók
* Telemetriai processzorok (előzetes verzió)
* Automatikusan gyűjtött naplózás
* Automatikusan gyűjtött mikrometermetrikák (beleértve Spring Boot Actuator-metrikákat)
* Szívverés
* HTTP-proxy
* Öndiagnosztika

További [részletekért tekintse meg](./java-standalone-config.md) a konfigurációs beállításokat.

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Automatikusan gyűjtött kérések, függőségek, naplók és metrikák

### <a name="requests"></a>Kérelmek

* JMS-felhasználók
* Kafka-fogyasztók
* Netty/WebFlux
* Servletek
* Spring Scheduling

### <a name="dependencies-with-distributed-trace-propagation"></a>Függőségek elosztott nyomkövetési propagálással

* Apache HttpClient és HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* Jms
* Kafka
* Netty-ügyfél
* OkHttp

### <a name="other-dependencies"></a>Egyéb függőségek

* Cassandra
* JDBC
* MongoDB (async and sync)
* Redis (Lettuce and Jedis)

### <a name="logs"></a>Naplók

* java.util.logging
* Log4j (az MDC-tulajdonságokkal együtt)
* SLF4J/Logback (az MDC-tulajdonságokkal együtt)

### <a name="metrics"></a>Mérőszámok

* Mikrométer (beleértve Spring Boot Actuator-metrikákat)
* JMX-metrikák

### <a name="azure-sdks-preview"></a>Azure SDK-k (előzetes verzió)

Tekintse meg [az előzetes verziójú](./java-standalone-config.md#auto-collected-azure-sdk-telemetry-preview) funkció engedélyezésére és az azure-beli SZOFTVER-hez kibocsátott telemetria rögzítésére vonatkozó konfigurációs beállításokat:

* [App Configuration](https://docs.microsoft.com/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [Cognitive Search](https://docs.microsoft.com/java/api/overview/azure/search-documents-readme) 11.3.0+
* [Kommunikációs csevegés](https://docs.microsoft.com/java/api/overview/azure/communication-chat-readme) 1.0.0+
* [Kommunikáció – gyakori](https://docs.microsoft.com/java/api/overview/azure/communication-common-readme) 1.0.0+
* [Kommunikációs identitás](https://docs.microsoft.com/java/api/overview/azure/communication-identity-readme) 1.0.0+
* [Kommunikációs SMS](https://docs.microsoft.com/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Cosmos DB](https://docs.microsoft.com/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Event Grid](https://docs.microsoft.com/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [Event Hubs](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [Event Hubs – Azure Blob Storage Checkpoint Store](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [Form Recognizer](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [Identitás](https://docs.microsoft.com/java/api/overview/azure/identity-readme) 1.2.4+
* [Key Vault – Tanúsítványok](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [Key Vault –](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+ kulcsok
* [Key Vault – Titkos kulcsok](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [Service Bus](https://docs.microsoft.com/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [Text Analytics](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "a következőből lekért fenti nevek és hivatkozások: https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # "A és a verzió szinkronizálása manuálisan történik a legrégebbi verzióval szemben az azure-core 1.14.0-s verzióra épült Maven Centralban"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "for (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    Folytatódik"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="send-custom-telemetry-from-your-application"></a>Egyéni telemetria küldése az alkalmazásból

A 3.0+-s vagy azt a célt küldjük, hogy lehetővé tegye az egyéni telemetria standard API-k használatával való elküldését.

A Micrometert, a népszerű naplózási keretrendszereket és az Application Insights Java 2.x SDK-t támogatjuk.
Application Insights Java 3.0 automatikusan rögzíti az api-kon keresztül küldött telemetriát, és korrelál az automatikusan gyűjtött telemetriával.

### <a name="supported-custom-telemetry"></a>Támogatott egyéni telemetria

Az alábbi táblázat a Java 3.0-ügynök kiegészítésére jelenleg támogatott egyéni telemetriatípusokat mutatja be. Összefoglalva, az egyéni metrikák mikrométerrel támogatottak, az egyéni kivételek és nyomkövetések naplózási keretrendszerekkel engedélyezhetők, az egyéni telemetria bármely típusa pedig a [Application Insights Java 2.x SDK-val](#send-custom-telemetry-using-the-2x-sdk)támogatott.

|                     | Mikrométer | Log4j, logback, JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **Egyéni események**   |            |                     |  Yes    |
| **Egyéni metrikák**  |  Igen       |                     |  Yes    |
| **Függőségek**    |            |                     |  Yes    |
| **Kivételek**      |            |  Igen                |  Yes    |
| **Lapnézetek**      |            |                     |  Yes    |
| **Kérelmek**        |            |                     |  Yes    |
| **Hívásláncok**          |            |  Igen                |  Yes    |

Jelenleg nem tervezzük a 3.0-s Application Insights SDK kiadását.

Application Insights Java 3.0 már figyel az Application Insights Java 2.x SDK-nak küldött telemetriára. Ez a funkció fontos része a meglévő 2.x felhasználóknak készült frissítési történetnek, és fontos hézagokat tartalmaz az egyéni telemetriatámogatásban, amíg az OpenTelemetry API nem lesz ga ga.

### <a name="send-custom-metrics-using-micrometer"></a>Egyéni metrikák küldése a Micrometer használatával

Adja hozzá a Micrometert az alkalmazáshoz:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Használja a Micrometer globális [regisztrációs adatbázisát](https://micrometer.io/docs/concepts#_global_registry) egy mérő létrehozásához:

```java
static final Counter counter = Metrics.counter("test_counter");
```

és használja ezt a metrikák rögzítéséhez:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Egyéni nyomkövetések és kivételek küldése kedvenc naplózási keretrendszerének használatával

A Log4j, a Logback és a java.util.logging naplózás automatikusan ki van automatizálva, és az ezen naplózási keretrendszerekkel végzett naplózást a rendszer automatikusan nyomkövetési és kivételtelemetriának gyűjti.

Alapértelmezés szerint a naplózás csak akkor lesz gyűjtve, ha a naplózás az INFO vagy magasabb szinten történik.
A szint [beállítását](./java-standalone-config.md#auto-collected-logging) a konfigurációs beállításokban láthatja.

Ha egyéni dimenziókat szeretne csatolni a naplókhoz, a [Log4j 1.2 MDC,](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html) [a Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)vagy a [Logback MDC](http://logback.qos.ch/manual/mdc.html), és az Application Insights Java 3.0 automatikusan egyéni dimenziókként rögzíti ezeket az MDC-tulajdonságokat a nyomkövetési és kivételtelemetrián.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Egyéni telemetria küldése a 2.x SDK használatával

Adja `applicationinsights-core-2.6.2.jar` hozzá az alkalmazáshoz (a Java 3.0 Application Insights mind a 2.x verziót támogatja, de ha van választási lehetőség, érdemes a legújabb verziót használnia):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

TelemetryClient létrehozása:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

és használja ezt az egyéni telemetria elküldését:

##### <a name="events"></a>Események

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Mérőszámok

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Függőségek

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Naplók

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Kivételek

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Egyéni kérésdimenziók hozzáadása a 2.x SDK-val

> [!NOTE]
> Ez a funkció csak a 3.0.2-es és újabb

Adja `applicationinsights-web-2.6.2.jar` hozzá az alkalmazáshoz (a Java 3.0 Application Insights mind a 2.x verziót támogatja, de ha van választási lehetőség, érdemes a legújabb verziót használnia):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

és adjon hozzá egyéni dimenziókat a kódhoz:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>A kérelem telemetriai adatainak user_Id 2.x SDK használatával

> [!NOTE]
> Ez a funkció csak a 3.0.2-es és újabb

Adja `applicationinsights-web-2.6.2.jar` hozzá az alkalmazáshoz (a Java 3.0 Application Insights mind a 2.x verziót támogatja, de ha van választási lehetőség, érdemes a legújabb verziót használnia):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

és állítsa be a `user_Id` kódot:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>A kérelem-telemetria nevének felülbírálása a 2.x SDK-val

> [!NOTE]
> Ez a funkció csak a 3.0.2-es és újabb

Adja `applicationinsights-web-2.6.2.jar` hozzá az alkalmazáshoz (a Java 3.0 Application Insights mind a 2.x verziót támogatja, de ha van választási lehetőség, érdemes a legújabb verziót használnia):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

és állítsa be a nevet a kódban:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>Kérje le a kérelem telemetria-azonosítóját és a műveleti azonosítót a 2.x SDK-val

> [!NOTE]
> Ez a funkció csak a 3.0.3-as és újabb

Adja `applicationinsights-web-2.6.2.jar` hozzá az alkalmazáshoz (a Java 3.0 Application Insights mind a 2.x verziót támogatja, de ha van választási lehetőség, érdemes a legújabb verziót használnia):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

és kérje le a kérelem telemetria-azonosítóját és a műveleti azonosítót a kódban:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```
