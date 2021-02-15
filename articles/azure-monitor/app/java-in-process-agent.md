---
title: Azure Monitor Application Insights Java
description: Alkalmazások teljesítményének figyelése bármilyen környezetben futó Java-alkalmazásokhoz programkód módosítása nélkül. Elosztott nyomkövetési és alkalmazás-hozzárendelés.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 811827c1053349d4fa80a25e5cf362331e5d87bc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383177"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>A Java Code unapplication monitoring Azure monitor Application Insights

A Java kód nélküli alkalmazások figyelése az egyszerűségről szól – a kód módosítása nem változik, a Java-ügynök csak néhány konfigurációs módosítással engedélyezhető.

 A Java-ügynök bármilyen környezetben működik, és lehetővé teszi a Java-alkalmazások figyelését. Más szóval, függetlenül attól, hogy a Java-alkalmazásait virtuális gépeken, a helyszínen, az AK-ban, Windowson, Linuxon futtatja, a Java 3,0-ügynök fogja figyelni az alkalmazást.

Az Application Insights Java SDK hozzáadása az alkalmazáshoz már nem szükséges, mivel az 3,0-ügynök automatikusan gyűjti a kérelmeket, a függőségeket és a naplókat.

Továbbra is küldhet egyéni telemetria az alkalmazásból. Az 3,0-ügynök nyomon követheti és korrelálhatja azt az összes automatikusan összegyűjtött telemetria együtt.

Az 3,0-ügynök támogatja a Java 8-as és újabb verzióit.

## <a name="quickstart"></a>Gyorsútmutató

**1. Töltse le az ügynököt**

> [!WARNING]
> **Ha 3,0 előzetes verzióról frissít**
>
> Figyelmesen tekintse át az összes [konfigurációs beállítást](./java-standalone-config.md) , mivel a JSON-struktúra teljes mértékben megváltozott, és a fájlneven kívül minden kisbetűt ment.

A [applicationinsights-Agent-3.0.2. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.2/applicationinsights-agent-3.0.2.jar) letöltése

**2. irányítsa a JVM az ügynökre**

Hozzáadás `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` az alkalmazás JVM argumentumai

A tipikus JVM argumentumok közé tartoznak `-Xmx512m` a és a `-XX:+UseG1GC` . Tehát ha tudja, hol adja hozzá ezeket, akkor már tudja, hová adja hozzá ezt a lehetőséget.

Ha további segítségre van az alkalmazás JVM argumentumok konfigurálásához, tekintse meg a [Tippek a JVM-argumentumok frissítéséhez](./java-standalone-arguments.md)című témakört.

**3. irányítsa az ügynököt a Application Insights erőforrásra**

Ha még nem rendelkezik Application Insights erőforrással, létrehozhat egy újat az [Erőforrás-létrehozási útmutató](./create-new-resource.md)lépéseit követve.

Mutasson az ügynököt a Application Insights erőforrásra, vagy egy környezeti változó beállításával:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Vagy hozzon létre egy nevű konfigurációs fájlt `applicationinsights.json` , és helyezze ugyanabba a könyvtárba `applicationinsights-agent-3.0.2.jar` , a következő tartalommal:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

A Application Insights erőforrásban található a kapcsolatok karakterlánca:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights a kapcsolatok karakterlánca":::

**4. ennyi!**

Most indítsa el az alkalmazást, és nyissa meg a Application Insights erőforrást a Azure Portalban a figyelési adatai megtekintéséhez.

> [!NOTE]
> Eltarthat néhány percig, amíg a figyelési adatai megjelennek a portálon.


## <a name="configuration-options"></a>Beállítási lehetőségek

A `applicationinsights.json` fájlban emellett a következőket is konfigurálhatja:

* Felhőbeli szerepkör neve
* Felhőalapú szerepkör-példány
* Mintavételezés
* JMX metrikák
* Egyéni dimenziók
* Telemetria processzorok (előzetes verzió)
* Automatikusan összegyűjtött naplózás
* Automatikusan összegyűjtött mérőműszer-metrikák (beleértve a Spring boot indítószerkezet metrikáit)
* Szívverés
* HTTP-proxy
* Öndiagnosztika

További részletek: [konfigurációs beállítások](./java-standalone-config.md) .

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Automatikusan összegyűjtött kérelmek, függőségek, naplók és metrikák

### <a name="requests"></a>Kérelmek

* JMS-fogyasztók
* Kafka-fogyasztók
* Nettó/webflux
* Servlets
* Rugó ütemezése

### <a name="dependencies-with-distributed-trace-propagation"></a>Elosztott nyomkövetési propagálással rendelkező függőségek

* Apache HttpClient és HttpAsyncClient
* gRPC
* Java. net. HttpURLConnection
* JMS
* Kafka
* Nettó ügyfél
* OkHttp

### <a name="other-dependencies"></a>Egyéb függőségek

* Cassandra
* JDBC
* MongoDB (aszinkron és szinkronizálás)
* Redis (saláta és jedik)

### <a name="logs"></a>Naplók

* Java. util. Logging
* Log4j (beleértve a MDC tulajdonságokat)
* SLF4J/Logback (beleértve a MDC-tulajdonságokat)

### <a name="metrics"></a>Mérőszámok

* Mikrométer (beleértve a Spring boot indítószerkezet metrikáit)
* JMX metrikák

## <a name="send-custom-telemetry-from-your-application"></a>Egyéni telemetria küldése az alkalmazásból

A 3.0-s célunk, hogy az egyéni telemetria standard API-k használatával küldje el.

Eddig támogatjuk a mikrométereket, a népszerű naplózási keretrendszereket és a Application Insights Java 2. x SDK-t.
Application Insights Java 3,0 automatikusan rögzíti az ezen API-kon keresztül továbbított telemetria, és korrelálja azt az automatikusan összegyűjtött telemetria.

### <a name="supported-custom-telemetry"></a>Támogatott egyéni telemetria

Az alábbi táblázat a jelenleg támogatott egyéni telemetria-típusokat jeleníti meg, amelyeket a Java 3,0-ügynök kiegészítéseként engedélyezhet. Az összegzéshez az egyéni metrikákat a Mikrométerek támogatják, az egyéni kivételek és a nyomkövetési funkciók a naplózási keretrendszereken keresztül engedélyezhetők, és az egyéni telemetria bármilyen típusa támogatott a [Application Insights Java 2. x SDK](#send-custom-telemetry-using-the-2x-sdk)-n keresztül.

|                     | Mikrométer | Log4j, logback, JUL | 2. x SDK |
|---------------------|------------|---------------------|---------|
| **Egyéni események**   |            |                     |  Yes    |
| **Egyéni metrikák**  |  Igen       |                     |  Yes    |
| **Függőségek**    |            |                     |  Yes    |
| **Kivételek**      |            |  Igen                |  Yes    |
| **Lapok nézetei**      |            |                     |  Yes    |
| **Kérelmek**        |            |                     |  Yes    |
| **Hívásláncok**          |            |  Igen                |  Yes    |

Jelenleg nem tervezzük Application Insights 3,0-es SDK kiadását.

Application Insights Java 3,0 már figyeli a Application Insights Java 2. x SDK-nak eljuttatott telemetria. Ez a funkció fontos részét képezi a meglévő 2. x-felhasználók frissítési történetének, és az egyéni telemetria-támogatás fontos hiányosságot tölt be, amíg a OpenTelemetry API nem a GA.

### <a name="send-custom-metrics-using-micrometer"></a>Egyéni metrikák küldése a Mikrométer használatával

Adja hozzá a mikrométert az alkalmazáshoz:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

A Mikrométer [globális beállításjegyzékének](https://micrometer.io/docs/concepts#_global_registry) használata mérőszám létrehozásához:

```java
static final Counter counter = Metrics.counter("test_counter");
```

a metrikák rögzítéséhez használja a következőt:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Egyéni Nyomkövetések és kivételek küldése a kedvenc naplózási keretrendszer használatával

A Log4j, a Logback és a Java. util. Logging automatikusan lett kialakítva, és ezekkel a naplózási keretrendszerek használatával végrehajtott naplózást a nyomkövetési és kivételi telemetria automatikusan gyűjti.

Alapértelmezés szerint a rendszer csak akkor gyűjti a naplózást, ha a naplózás az adatok szintjén vagy felett van elvégezve.
A szint módosításához tekintse meg a [konfigurációs beállításokat](./java-standalone-config.md#auto-collected-logging) .

Ha egyéni dimenziókat szeretne csatolni a naplókhoz, használhatja a [Log4j 1,2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), a [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)vagy a [Logback MDC](http://logback.qos.ch/manual/mdc.html), és a Application Insights Java 3,0 automatikusan rögzíti ezeket a MDC tulajdonságokat egyéni dimenzióként a nyomkövetési és kivételi telemetria.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Egyéni telemetria küldése a 2. x SDK használatával

Vegye fel az `applicationinsights-core-2.6.2.jar` alkalmazást az alkalmazásba (az összes 2. x verziót támogatja Application Insights Java 3,0, de érdemes a legújabbat használni, ha van ilyen választása):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

Hozzon létre egy TelemetryClient:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

és használja az egyéni telemetria küldéséhez:

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

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Egyéni dimenzió kérésének hozzáadása a 2. x SDK használatával

> [!NOTE]
> Ez a funkció csak a 3.0.2 és újabb verziókban érhető el

Vegye fel az `applicationinsights-web-2.6.2.jar` alkalmazást az alkalmazásba (az összes 2. x verziót támogatja Application Insights Java 3,0, de érdemes a legújabbat használni, ha van ilyen választása):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

és vegyen fel egyéni dimenziókat a kódban:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>A telemetria kérésének beállítása user_Id a 2. x SDK használatával

> [!NOTE]
> Ez a funkció csak a 3.0.2 és újabb verziókban érhető el

Vegye fel az `applicationinsights-web-2.6.2.jar` alkalmazást az alkalmazásba (az összes 2. x verziót támogatja Application Insights Java 3,0, de érdemes a legújabbat használni, ha van ilyen választása):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

és állítsa be a `user_Id` kódot a kódban:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>A kérelem telemetria nevének felülbírálása a 2. x SDK használatával

> [!NOTE]
> Ez a funkció csak a 3.0.2 és újabb verziókban érhető el

Vegye fel az `applicationinsights-web-2.6.2.jar` alkalmazást az alkalmazásba (az összes 2. x verziót támogatja Application Insights Java 3,0, de érdemes a legújabbat használni, ha van ilyen választása):

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
