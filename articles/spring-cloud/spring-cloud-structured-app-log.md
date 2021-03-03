---
title: Az Azure Spring Cloud strukturált alkalmazási naplója | Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet strukturált alkalmazás-naplózási adatokat készíteni és gyűjteni az Azure Spring Cloud-ban.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: brendanm
ms.custom: devx-track-java
ms.openlocfilehash: e846da81444ae1632cb7f9a4cd413bc3f9b7b232
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701943"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Az Azure Spring Cloud strukturált alkalmazási naplója

Ez a cikk bemutatja, hogyan lehet strukturált alkalmazás-naplózási adatokat készíteni és gyűjteni az Azure Spring Cloud-ban. A megfelelő konfigurációval az Azure Spring Cloud hasznos alkalmazások naplójának lekérdezését és elemzését teszi lehetővé Log Analyticson keresztül.

## <a name="log-schema-requirements"></a>A naplózási séma követelményei
A naplózási lekérdezési élmény javítása érdekében JSON formátumban kell lennie, és meg kell felelnie egy sémának. Az Azure Spring Cloud ezt a sémát használja az alkalmazás és a Log Analytics-adatfolyam elemzéséhez. 

**JSON-séma követelményei:**

| JSON-kulcs      | JSON-érték típusa|  Kötelező | Oszlop Log Analytics| Leírás |
| --------------| ------------|-----------|-----------------|--------------------------|
| időbélyeg     | sztring      |     Igen   | AppTimestamp    | időbélyeg UTC formátumban  |
| Tuskózó        | sztring      |     No    | Tuskózó          | Tuskózó                   |
| szint         | sztring      |     No    | CustomLevel     | naplózási szint                |
| szál        | sztring      |     No    | Szál          | szál                   |
| message       | sztring      |     No    | Üzenet         | napló üzenete              |
| stackTrace    | sztring      |     No    | StackTrace      | kivételek veremének nyomkövetése    |
| exceptionClass| sztring      |     No    | ExceptionClass  | kivételi osztály neve     |
| MDC           | beágyazott JSON |     Nem    |                 | leképezett diagnosztikai környezet|
| MDC. traceId   | sztring      |     No    | TraceId         |az elosztott nyomkövetés nyomkövetési azonosítója|
| MDC. spanId    | sztring      |     No    | SpanId          |elosztott nyomkövetés tartományának azonosítója |
|               |             |           |                 |                          |

* A "Timestamp" mező megadása kötelező, és UTC formátumban kell lennie, az összes többi mező megadása nem kötelező.
* a "MDC" mezőben a "traceId" és a "spanId" a nyomkövetés céljára szolgál.
* Minden JSON-rekord naplózása egy sorban. 

**Naplóbeli rekord mintája** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Séma-kompatibilis JSON-napló előállítása  
A Spring Applications esetében az általános [naplózási keretrendszerek](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration), például a [logback](http://logback.qos.ch/) és a [log4j2](https://logging.apache.org/log4j/2.x/)használatával hozhatja meg a várt JSON-napló formátumát. 

### <a name="log-with-logback"></a>Bejelentkezés logback 
A Spring boot Starter használatakor a rendszer alapértelmezés szerint a logback használja. A logback alkalmazások esetében használja a [logstash-Encodert](https://github.com/logstash/logstash-logback-encoder) a JSON formátumú napló létrehozásához. Ez a módszer a Spring boot 2.1 + verziójában támogatott. 

Az eljárás:

1. Adja hozzá a logstash függőséget a pom.xml-fájlhoz. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. A JSON formátumának beállításához frissítse logback.xml konfigurációs fájlját.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Bejelentkezés log4j2 

Log4j2-alkalmazások esetén a [JSON-template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) használatával hozhatja meg a JSON formátumú naplót. Ez a módszer a Spring boot 2.1 + verziójában támogatott.

Az eljárás:

1. Kizárás `spring-boot-starter-logging` a következőből `spring-boot-starter` :, függőségek hozzáadása `spring-boot-starter-log4j2` `log4j-layout-template-json` a pom.xml fájlban.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Készítse elő a JSON-sablon fájlját jsonTemplate.jsa osztály elérési útján.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. Használja ezt a JSON-elrendezési sablont a log4j2.xml konfigurációs fájljában. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>A naplók elemzése Log Analytics

Az alkalmazás megfelelő beállítása után az alkalmazás konzoljának naplóját Log Analytics továbbítja a rendszer. A struktúra lehetővé teszi a Log Analytics hatékony lekérdezését.

### <a name="check-log-structure-in-log-analytics"></a>A naplózási struktúra ellenõrzése Log Analytics
Kövesse az alábbi eljárást:
1. Nyissa meg a szolgáltatási példány szolgáltatás áttekintése lapját.
2. Kattintson `Logs` a szakasz elemre `Monitoring` .
3. Futtassa ezt a lekérdezést.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Az alkalmazás naplói a következő képen látható módon térnek vissza:

![JSON-napló megjelenítése](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Hibákat tartalmazó naplóbejegyzések megjelenítése

A hibával rendelkező naplóbejegyzések áttekintéséhez futtassa a következő lekérdezést:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Ezzel a lekérdezéssel megkeresheti a hibákat, vagy módosíthatja a lekérdezési feltételeket adott kivételi osztály vagy hibakód kereséséhez. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Megadott traceId tartozó naplóbejegyzések megjelenítése
Egy adott "trace_id" nyomkövetési azonosító bejegyzéseinek áttekintéséhez futtassa a következő lekérdezést:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Következő lépések
* További információ a napló lekérdezéséről: Ismerkedés [a Azure monitor](../azure-monitor/logs/get-started-queries.md)