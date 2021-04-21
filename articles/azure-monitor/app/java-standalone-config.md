---
title: Konfigurációs beállítások – Azure Monitor Application Insights Java-hoz
description: A Javához Azure Monitor Application Insights konfigurálása
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: b78aaa659598e6eb58841c5cef0c209daaced5e0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811975"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Konfigurációs beállítások – Azure Monitor Application Insights Java-hoz

> [!WARNING]
> **Ha a 3.0-s előzetes verzióról frissít**
>
> Alaposan tekintse át az alábbi konfigurációs lehetőségeket, mivel a JSON-struktúra teljesen megváltozott, és magát a fájlnevet is kisbetűvel módosította.

## <a name="connection-string-and-role-name"></a>Kapcsolati sztring és szerepkörnév

Az első lépésekhez a kapcsolati sztring és a szerepkör neve a leggyakoribb beállítás:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

A kapcsolati sztringre szükség van, és a szerepkör neve minden alkalommal fontos, amikor különböző alkalmazásokból küld adatokat ugyanannak a Application Insights erőforrásnak.

Az alábbiakban további részleteket és további konfigurációs lehetőségeket talál.

## <a name="configuration-file-path"></a>Konfigurációs fájl elérési útja

Alapértelmezés szerint a Application Insights Java 3.0 azt várja, hogy a konfigurációs fájl neve , és ugyanabban a könyvtárban legyen, mint `applicationinsights.json` `applicationinsights-agent-3.0.3.jar` a .

Saját konfigurációs fájl elérési útját a következővel adhatja meg:

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` környezeti változó, vagy
* `applicationinsights.configuration.file` Java rendszertulajdonság

Ha megad egy relatív elérési utat, az feloldódik ahhoz a könyvtárhoz viszonyítva, `applicationinsights-agent-3.0.3.jar` ahol a található.

## <a name="connection-string"></a>Kapcsolati sztring

Kapcsolati sztringre van szükség. A kapcsolati sztring a saját erőforrásában Application Insights található:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights sztring létrehozása":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

A kapcsolati sztringet a környezeti változóval is beállíthatja (amely ekkor elsőbbséget élvez a `APPLICATIONINSIGHTS_CONNECTION_STRING` json-konfigurációban megadott kapcsolati sztringtel).

Ha nem ad meg kapcsolati sztringet, az letiltja a Java-ügynököt.

## <a name="cloud-role-name"></a>Felhőbeli szerepkör neve

A felhőbeli szerepkör neve az összetevő címkézésére használatos az alkalmazástérképen.

Ha be szeretné állítani a felhőbeli szerepkör nevét:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Ha a felhőbeli szerepkör neve nincs beállítva, Application Insights erőforrás neve lesz használva az összetevő címkézésére az alkalmazástérképen.

A felhőbeli szerepkör nevét a környezeti változóval is beállíthatja (amely elsőbbséget élvez a json-konfigurációban megadott felhőbeli `APPLICATIONINSIGHTS_ROLE_NAME` szerepkör nevével).

## <a name="cloud-role-instance"></a>Felhőbeli szerepkörpéldány

A felhőbeli szerepkörpéldány alapértelmezett neve a gép neve.

Ha a felhőbeli szerepkörpéldányt a gép neve helyett másra szeretné beállítani:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

A felhőbeli szerepkörpéldányt a környezeti változóval is beállíthatja (amely elsőbbséget élvez a JSON-konfigurációban megadott felhőbeli `APPLICATIONINSIGHTS_ROLE_INSTANCE` szerepkörpéldányokkal).

## <a name="sampling"></a>Mintavételezés

A mintavételezés akkor hasznos, ha csökkentenie kell a költségeket.
A mintavételezés a műveletazonosító (más néven nyomkövetési azonosító) függvényeként történik, így ugyanaz a műveletazonosító mindig ugyanazt a mintavételezési döntést eredményezi. Ez biztosítja, hogy ne mintavételez egy elosztott tranzakció részeit, amíg a rendszer a többi részét ki nem mintavétele.

Ha például 10%-ra adja meg a mintavételezést, a tranzakcióknak csak 10%-a látható, de a 10% mindegyikének teljes körű tranzakciós adatai lesznek.

Az alábbi példa bemutatja, hogyan állíthatja be a mintavételezést úgy, hogy az összes tranzakció körülbelül **1/3-át** rögzítse – ügyeljen arra, hogy a saját esetének megfelelő mintavételezési sebességet állítsa be:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

A mintavételezési százalékot a környezeti változóval is beállíthatja (amely elsőbbséget élvez a `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` json-konfigurációban megadott százalékos mintavételezéssel).

> [!NOTE]
> A százalékos mintavételezéshez olyan százalékos arányt válasszon, amely közel van 100/N-hez, ahol az N egész szám. A mintavételezés jelenleg nem támogat más értékeket.

## <a name="sampling-overrides-preview"></a>Mintavételezési felülbírálások (előzetes verzió)

Ez a funkció előzetes verzióban érhető el, a 3.0.3-as verziótól kezdve.

A mintavételezési felülbírálások lehetővé teszik az alapértelmezett százalékos [mintavételezés felülbírálást,](#sampling)például:
* A százalékos mintavételezést állítsa 0-ra (vagy egy kis értékre) a zajos állapotellenőrzéshez.
* A százalékos mintavételezést állítsa 0-ra (vagy egy kis értékre) a zajos függőségi hívások esetén.
* A mintavételezés százalékos arányát állítsa 100-ra egy fontos kérelemtípushoz (például ) annak ellenére, hogy az alapértelmezett mintavételezés `/login` alacsonyabbra van konfigurálva.

További információért tekintse meg a mintavételezési [felülbírálások dokumentációját.](./java-standalone-sampling-overrides.md)

## <a name="jmx-metrics"></a>JMX-metrikák

Ha további JMX-metrikákat szeretne gyűjteni:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` A a JMX-metrikákhoz hozzárendelt metrika neve (bármi lehet).

`objectName` A a [gyűjteni](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) kívánt JMX MBean objektumneve.

`attribute` A a gyűjteni kívánt JMX MBean attribútumneve.

A numerikus és logikai JMX metrikaértékek támogatottak. A logikai JMX-metrikák false (hamis) és "true" (igaz) `0` `1` értékhez vannak leképezve.

## <a name="custom-dimensions"></a>Egyéni dimenziók

Ha egyéni dimenziókat szeretne hozzáadni az összes telemetriai adathoz:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` A használható a megadott környezeti változó értékének beolvassa indításkor.

> [!NOTE]
> A 3.0.2-es verziótól kezdődően, ha hozzáad egy nevű egyéni dimenziót, az érték nem egyéni dimenzióként, hanem a Application Insights Logs tábla oszlopában lesz `service.version` `application_Version` tárolva.

## <a name="telemetry-processors-preview"></a>Telemetriai processzorok (előzetes verzió)

Ez a funkció előzetes verzióban érhető el.

Lehetővé teszi a kérelmekre, függőségre és nyomkövetési telemetrira alkalmazott szabályok konfigurálését, például:
 * Bizalmas adatok maszkolása
 * Egyéni dimenziók feltételes hozzáadása
 * Frissítse a span nevet, amely hasonló telemetria összesítésére használatos a Azure Portal.
 * Adott span attribútumok eldobása a bebedobási költségek szabályozása érdekében.

További információért tekintse meg a [telemetriafeldolgozó dokumentációját.](./java-standalone-telemetry-processors.md)

> [!NOTE]
> Ha adott (teljes) időtartamokat szeretné eldobni a bebevételi költségek szabályozására, tekintse meg a [mintavételezési felülbírálásokat.](./java-standalone-sampling-overrides.md)

## <a name="auto-collected-logging"></a>Automatikusan gyűjtött naplózás

A Log4j, a Logback és a java.util.logging naplózás automatikusan ki van automatizálva, és az ezen naplózási keretrendszerekkel végzett naplózás automatikusan gyűjtve lesz.

A naplózás csak akkor lesz rögzíti, ha először megfelel a naplózási keretrendszerhez konfigurált szintnek, a második pedig a naplózási keretrendszerhez konfigurált szintnek Application Insights.

Ha például a naplózási keretrendszer úgy van konfigurálva, hogy naplózva (vagy magasabb) a csomagból, és az Application Insights úgy van konfigurálva, hogy rögzítse (és magasabb) a(Application Insights) csak a csomagból rögzíti (és magasabbra) a(Application Insights) `WARN` `com.example` `INFO` `WARN` `com.example` csomagot.

Az alapértelmezett szint a következő: `INFO` Application Insights. Ha módosítani szeretné ezt a szintet:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

A szintet a környezeti változóval is beállíthatja (amely ekkor elsőbbséget élvez a `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` json-konfigurációban megadott szinttel).

Ezek a fájlban megadható érvényes értékek, és hogyan felelnek meg a különböző naplózási keretrendszerek `level` `applicationinsights.json` naplózási szintjeinek:

| szint             | Log4j  | Logback | JÚL     |
|-------------------|--------|---------|---------|
| KI               | KI    | KI     | KI     |
| Végzetes             | Végzetes  | HIBA   | Súlyos  |
| HIBA (VAGY SÚLYOS) | HIBA  | HIBA   | Súlyos  |
| FIGYELMEZTETÉS (VAGY FIGYELMEZTETÉS) | Figyelmeztet   | Figyelmeztet    | FIGYELMEZTETÉS |
| Info              | Info   | Info    | Info    |
| Config            | HIBAKERESÉS  | HIBAKERESÉS   | Config  |
| HIBAKERESÉS (VAGY FINOM)   | HIBAKERESÉS  | HIBAKERESÉS   | Finom    |
| Finomabb             | HIBAKERESÉS  | HIBAKERESÉS   | Finomabb   |
| TRACE (VAGY EGYSZŰK) | Nyomkövetési  | Nyomkövetési   | Legjobb  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Ha egy kivételobjektumot ad át a naplózónak, a naplóüzenet (és a kivételobjektum részletei) a tábla helyett a tábla alatt Azure Portal megjelenik a `exceptions` `traces` táblázatban.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Automatikusan gyűjtött mikrometermetrikák (beleértve Spring Boot Actuator-metrikákat)

Ha az alkalmazás [a Micrometert](https://micrometer.io)használja, a rendszer automatikusan gyűjti a Micrometer globális regisztrációs adatbázisának küldött metrikákat.

Ha az alkalmazás az [Actuator Spring Boot,](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)akkor az Spring Boot Actuator által konfigurált metrikákat is automatikusan gyűjti a rendszer.

A Mikrometer-metrikák (többek között az Actuator-metrikák) Spring Boot automatikus gyűjtésének letiltása:

> [!NOTE]
> Az egyéni metrikák számlázása külön történik, és további költségeket okozhatnak. Ellenőrizze a részletes [díjszabási információkat.](https://azure.microsoft.com/pricing/details/monitor/) A Micrometer és a Spring Actuator metrikák letiltásához adja hozzá az alábbi konfigurációt a konfigurációs fájlhoz.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="auto-collected-azure-sdk-telemetry-preview"></a>Automatikusan gyűjtött Azure SDK-telemetria (előzetes verzió)

Számos legújabb Azure SDK-kódtár telemetriát bocsát ki (lásd a [teljes listát).](./java-in-process-agent.md#azure-sdks-preview)

A Java 3.0.3-as Application Insights kezdve engedélyezheti a telemetria rögzítését.

Ha engedélyezni szeretné ezt a funkciót:

```json
{
  "preview": {
    "instrumentation": {
      "azureSdk": {
        "enabled": true
      }
    }
  }
}
```

Ezt a funkciót a környezeti változó használatával is engedélyezheti `APPLICATIONINSIGHTS_PREVIEW_INSTRUMENTATION_AZURE_SDK_ENABLED`
(ez elsőbbséget élvez a json-konfigurációban megadottakhoz képest).

## <a name="suppressing-specific-auto-collected-telemetry"></a>Az automatikusan gyűjtött telemetria mellőzése

A 3.0.3-as verziótól kezdve az automatikusan gyűjtött telemetria az alábbi konfigurációs beállításokkal tiltható le:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "jms": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    },
    "springScheduling": {
      "enabled": false
    }
  }
}
```

Ezeket a rendszereket az alábbi környezeti változók használatával is letilthatja:

* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

(ez elsőbbséget élvez a json-konfigurációban megadottakhoz képest).

> MEGJEGYZÉS Ha pontosabb vezérlést keres, például egyes Redis-hívások mellőzését, de nem minden Redis-hívást, tekintse meg a mintavételezési [felülbírálásokat.](./java-standalone-sampling-overrides.md)

## <a name="heartbeat"></a>Szívverés

Alapértelmezés szerint a Application Insights Java 3.0 15 percenként küld szívverési metrikát. Ha a szívverési metrikát használja a riasztások kiváltása érdekében, növelheti ennek a szívverésnek a gyakoriságát:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Az időközt nem lehet 15 percnél hosszabbra növelni, mert a szívverési adatok a használati adatok nyomon követésére Application Insights is használhatók.

## <a name="http-proxy"></a>HTTP-proxy

Ha az alkalmazás tűzfal mögött található, és nem tud közvetlenül csatlakozni az Application Insights-hoz (lásd az [Application Insights](./ip-addresses.md)által használt IP-címeket), konfigurálhatja az Application Insights Java 3.0-t http-proxy használatára:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3.0 a globálist is tiszteletben tartja, és `-Dhttps.proxyHost` ha ezek be vannak `-Dhttps.proxyPort` állítva.

## <a name="metric-interval"></a>Metrikaintervallum

Ez a funkció előzetes verzióban érhető el.

Alapértelmezés szerint a rendszer 60 másodpercenként rögzíti a metrikákat.

A 3.0.3-as verziótól kezdve a következő időközt módosíthatja:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

A beállítás az összes metrikákra vonatkozik:

* Alapértelmezett teljesítményszámlálók, például processzor és memória
* Alapértelmezett egyéni metrikák, például szemétgyűjtés időzítése
* Konfigurált JMX-metrikák ([lásd fent](#jmx-metrics))
* Mikrométeres metrikák ([lásd fent](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "MEGJEGYZÉS Az OpenTelemetry támogatása privát előzetes verzióban érhető el, amíg az OpenTelemetry API el nem éri az 1.0-t"

[//]: # "## OpenTelemetry API 1.0 előtti kiadások támogatása"

[//]: # "Az OpenTelemetry API 1.0 előtti verzióinak támogatása nem támogatott, mivel az OpenTelemetry API még nem stabil"
[//]: # "és így az ügynök minden verziója csak az OpenTelemetry API adott, 1.0 előtti verzióit támogatja"
[//]: # "(Ez a korlátozás nem vonatkozik az OpenTelemetry API 1.0-s verziójának kiadott kiadott verzióra."

[//]: # "'''json"
[//]: # "{"
[//]: # "  \"előzetes \" verzió: {"
[//]: # "    \"openTelemetryApiSupport: \" true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Öndiagnosztika

Az "öndiagnosztika" a Java 3.0 Application Insights belső naplózását jelenti.

Ez a funkció hasznos lehet az alkalmazással kapcsolatos problémák Application Insights diagnosztizálásában.

Alapértelmezés szerint a Application Insights a Java 3.0-naplókat a fájlhoz és a konzolhoz `INFO` `applicationinsights.log` is, a következő konfigurációnak megfelelően:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` A a vagy `file` a egyike `console` `file+console` lehet.

`level` A lehet a `OFF` , , , , vagy `ERROR` `WARN` `INFO` `DEBUG` `TRACE` egyike.

`path` A lehet abszolút vagy relatív elérési út. A relatív elérési utak arra a könyvtárra vannak feloldva, `applicationinsights-agent-3.0.3.jar` ahol a található.

`maxSizeMb` A a naplófájl legnagyobb mérete a fájl átmérete előtt.

`maxHistory` A (az aktuális naplófájlon kívül) megőrzített naplófájlok száma.

A 3.0.2-es verziótól kezdődően az öndiagnosztika a környezeti változóval is beállítható (amely elsőbbséget élvez a `level` `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` JSON-konfigurációban megadott öndiagnosztikai szinttel).

## <a name="an-example"></a>Példa

Ez csak egy példa arra, hogyan néz ki egy konfigurációs fájl több összetevővel.
Adja meg az igényeinek megfelelő konkrét beállításokat.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```
