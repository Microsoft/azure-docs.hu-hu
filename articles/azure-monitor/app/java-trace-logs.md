---
title: Ismerkedés a Java-nyomkövetési naplókkal az Azure Application Insights
description: Log4J-vagy Logback-nyomkövetés keresése a Application Insightsban
ms.topic: conceptual
ms.date: 05/18/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 4013b60a5718de6c196e806f894503f63ec9dafd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593687"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Ismerkedjen meg a Java-nyomkövetési naplók Application Insights

> [!IMPORTANT]
> A dokumentumban leírt módszer már nem ajánlott.
>
> A Java-alkalmazások figyelésének ajánlott módszere az automatikus kiépítés használata a kód módosítása nélkül. Kérjük, kövesse az [Application Insights Java 3,0-ügynökre](./java-in-process-agent.md)vonatkozó irányelveket.

Ha a nyomkövetéshez Logback vagy Log4J (v 1.2 vagy v 2.0) használ, a nyomkövetési naplókat automatikusan elküldheti Application Insights ahol megtekintheti és megkeresheti őket.

> [!TIP]
> Az alkalmazáshoz csak egyszer kell beállítania a Application Insights-kialakítási kulcsot. Ha olyan keretrendszert használ, mint a Java Spring, előfordulhat, hogy az alkalmazás konfigurációjában máshol már regisztrálta a kulcsot.

## <a name="using-the-application-insights-java-agent"></a>A Application Insights Java-ügynök használata

Alapértelmezés szerint a Application Insights Java-ügynök automatikusan rögzíti a naplózást a `WARN` szinten vagy felett.

A fájl használatával rögzített naplózási küszöbértéket módosíthatja `AI-Agent.xml` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

A Java-ügynök naplózási rögzítését a fájl használatával tilthatja le `AI-Agent.xml` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Másik lehetőségként (a Java-ügynök használata helyett) kövesse az alábbi utasításokat.

### <a name="install-the-java-sdk"></a>A Java SDK telepítése

Ha még nem tette meg, kövesse az utasításokat a [Java Application INSIGHTS SDK][java]telepítéséhez.

### <a name="add-logging-libraries-to-your-project"></a>Naplózási kódtárak hozzáadása a projekthez
*Válassza ki a projektnek megfelelő módszert.*

#### <a name="if-youre-using-maven"></a>Ha Mavent használ...
Ha a projekt már beállította a Maven használatát a buildhez, egyesítse az alábbi kódrészletek egyikét a pom.xml fájlba.

Ezután frissítse a projekt függőségeit a letöltött bináris fájlok letöltéséhez.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J 2.0-s verzió*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J 2.0-s verzió*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Ha Gradle-t használ...
Ha a projekt már be van állítva az Gradle for Build használatára, adja hozzá a következő sorok egyikét a `dependencies` Build. Gradle fájljában található csoporthoz:

Ezután frissítse a projekt függőségeit a letöltött bináris fájlok letöltéséhez.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J 2.0-s verzió**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J 2.0-s verzió**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Egyéb esetben...
Kövesse az irányelveket a Application Insights Java SDK manuális telepítéséhez, töltse le a jar-t (a Maven Central oldalának megérkezése után kattintson a "jar" hivatkozásra a letöltési szakaszban) a megfelelő hozzáfűzéshez, és adja hozzá a letöltött append jar-t a projekthez.

| Tuskózó | Letöltés | Kódtár |
| --- | --- | --- |
| Logback |[Logback-hozzáfűző jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights – naplózás – logback |
| Log4J 2.0-s verzió |[Log4J v2 hozzáfűzése jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights – naplózás – log4j2 |
| Log4j 2.0-s verzió |[Log4J v 1.2 hozzáfűzése jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights – naplózás – log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>A hozzáfűzése hozzáadása a naplózási keretrendszerhez
A nyomkövetési lépések elindításához egyesítse a megfelelő kódrészletet a Log4J vagy a Logback konfigurációs fájlba: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J 2.0-s verzió*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J 2.0-s verzió*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

A Application Insights-hozzáfűző hivatkozásokat bármely konfigurált naplózó hivatkozhat, és nem feltétlenül a root naplózó (a fenti mintakód-példákban látható).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>A Nyomkövetések megismerése a Application Insights portálon
Most, hogy úgy konfigurálta a projektet, hogy nyomkövetéseket küldjön a Application Insightsnak, megtekintheti és megkeresheti ezeket a nyomkövetéseket a Application Insights portálon, a [Keresés][diagnostic] panelen.

A gyűjtők által küldött kivételek a portálon a kivétel telemetria jelennek meg.

![A Application Insights portálon nyissa meg a keresést](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Következő lépések
[Diagnosztikai keresés][diagnostic]

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[java]: java-get-started.md

