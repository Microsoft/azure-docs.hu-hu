---
title: Konfigurációs beállítások – Azure Monitor Application Insights Javához
description: A Java-Azure Monitor Application Insights konfigurálása
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3806578f5d1af61329e2e32fa3e8eceb9afa4d42
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713966"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Konfigurációs beállítások – Azure Monitor Application Insights Javához

> [!WARNING]
> **Ha 3,0 előzetes verzióról frissít**
>
> Tekintse át figyelmesen az összes konfigurációs beállítást, mivel a JSON-struktúra teljes mértékben megváltozott, és a fájlneven kívül minden kisbetűt ment.

## <a name="connection-string-and-role-name"></a>A kapcsolatok karakterlánca és a szerepkör neve

A kezdéshez szükséges leggyakoribb beállítások a kapcsolódási karakterlánc és a szerepkör neve:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

A kapcsolódási karakterláncot kötelező megadni, és a szerepkör neve fontos minden alkalommal, amikor különböző alkalmazásokból küld adatokat ugyanarra a Application Insights erőforrásra.

Alább további részleteket és további konfigurációs beállításokat találhat.

## <a name="configuration-file-path"></a>Konfigurációs fájl elérési útja

Alapértelmezés szerint a Application Insights Java 3,0 elvárja, hogy a konfigurációs fájl legyen elnevezve `applicationinsights.json` , és hogy ugyanabban a könyvtárban legyen elhelyezve `applicationinsights-agent-3.0.2.jar` .

Megadhatja saját konfigurációs fájljának elérési útját a következők használatával

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` környezeti változó, vagy
* `applicationinsights.configuration.file` Java rendszertulajdonság

Ha relatív elérési utat ad meg, a rendszer a helyen található könyvtárhoz viszonyítva megoldódik `applicationinsights-agent-3.0.2.jar` .

## <a name="connection-string"></a>Kapcsolati sztring

A kapcsolatok karakterláncának megadása kötelező. A Application Insights erőforrásban található a kapcsolatok karakterlánca:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights a kapcsolatok karakterlánca":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

A környezeti változó használatával is beállíthatja a kapcsolatok karakterláncát `APPLICATIONINSIGHTS_CONNECTION_STRING` .

Ha nem állítja be a kapcsolatok karakterláncát, akkor letiltja a Java-ügynököt.

## <a name="cloud-role-name"></a>Felhőbeli szerepkör neve

A Felhőbeli szerepkör neve az alkalmazás térképén található összetevő címkézésére szolgál.

Ha be szeretné állítani a Felhőbeli szerepkör nevét:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Ha nincs beállítva a Felhőbeli szerepkör neve, a rendszer a Application Insights erőforrás nevét fogja használni az alkalmazás térképén lévő összetevő címkézéséhez.

A Felhőbeli szerepkör nevét a környezeti változó használatával is beállíthatja `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>Felhőalapú szerepkör-példány

A Felhőbeli szerepkör példánya alapértelmezés szerint a gép nevét adja meg.

Ha a Felhőbeli szerepkör-példányt a gép neve helyett más értékre szeretné beállítani:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

A Felhőbeli szerepkör-példányt a környezeti változó használatával is beállíthatja `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="sampling"></a>Mintavételezés

A mintavétel hasznos lehet, ha csökkenteni kell a költségeket.
A mintavétel függvényként van elvégezve a műveleti AZONOSÍTÓban (más néven nyomkövetési azonosító), így ugyanaz a műveleti azonosító mindig ugyanazt a mintavételi döntést fogja eredményezni. Ez biztosítja, hogy az elosztott tranzakciók részei ne legyenek kiszámítva, míg más részeinek mintavételezése nem történik meg.

Ha például 10%-ra állítja be a mintavételezést, akkor csak a tranzakciók 10%-át fogja látni, de ezek mindegyike a teljes végpontok közötti tranzakció részleteit tartalmazza.

Az alábbi példa bemutatja, hogyan állíthatja be a mintavételezést úgy, hogy az **összes tranzakció körülbelül 1/3** legyen, és ügyeljen arra, hogy a használati esetnek megfelelő mintavételi sebességet állítsa be:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

A mintavételezési százalékot a környezeti változó használatával is beállíthatja `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

> [!NOTE]
> A mintavételezési százaléknál válasszon egy olyan százalékot, amely a 100/N értéknél közelebb van, ahol N egész szám. A mintavétel jelenleg nem támogatja a többi értéket.

## <a name="jmx-metrics"></a>JMX metrikák

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

`name` a metrika neve, amely hozzá lesz rendelve ehhez a JMX metrikához (bármi lehet).

`objectName` a gyűjteni kívánt JMX-MBean [objektumának neve](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) .

`attribute` az attribútum neve a JMX-MBean belül, amelyet össze kíván gyűjteni.

A numerikus és a logikai JMX metrikájának értékei támogatottak. A logikai JMX metrikái a hamis értékre vannak leképezve `0` , és `1` igaz.

[//]: # "Megjegyzés: nem dokumentálja APPLICATIONINSIGHTS_JMX_METRICS itt"
[//]: # "az ENV var-ban beágyazott JSON-fájl zavaros, és csak a kód nem szükséges csatolása esetén dokumentálható"

## <a name="custom-dimensions"></a>Egyéni dimenziók

Ha egyéni dimenziókat szeretne hozzáadni az összes telemetria:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` a megadott környezeti változó értékének beolvasására használható az indításkor.

> [!NOTE]
> Ha egy nevű egyéni dimenziót ad hozzá az 3.0.2 verziótól kezdődően `service.version` , az érték a `application_Version` Application Insights naplók tábla oszlopában lesz tárolva egyéni dimenzió helyett.

## <a name="telemetry-processors-preview"></a>Telemetria processzorok (előzetes verzió)

Ez a funkció előzetes verzióban érhető el.

Lehetővé teszi olyan szabályok konfigurálását, amelyek a kérelemre, a függőségre és a nyomkövetési telemetria lesznek alkalmazva, például:
 * Bizalmas adatok maszkolása
 * Egyéni dimenziók feltételes hozzáadása
 * Az összesítéshez és a megjelenítéshez használt telemetria-név frissítése

További információkért tekintse meg a [telemetria-feldolgozó](./java-standalone-telemetry-processors.md) dokumentációját.

## <a name="auto-collected-logging"></a>Automatikusan összegyűjtött naplózás

A Log4j, a Logback és a Java. util. Logging automatikusan lett kialakítva, és ezekkel a naplózási keretrendszerekkel végrehajtott naplózás automatikusan begyűjtve lesz.

A rendszer csak akkor rögzíti a naplózást, ha először megfelel a naplózási keretrendszerek konfigurált küszöbértékének, a második pedig megfelel a Application Insights konfigurált küszöbértéknek is.

Az alapértelmezett Application Insights küszöbérték: `INFO` . Ha módosítani szeretné ezt a szintet:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

A küszöbértéket a környezeti változó használatával is megadhatja `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` .

Ezek az érvényes `level` értékek, amelyeket megadhat a `applicationinsights.json` fájlban, és hogyan felelnek meg a naplózási szintnek a különböző naplózási keretrendszerek esetében:

| szint             | Log4j  | Logback | JÚL     |
|-------------------|--------|---------|---------|
| KI               | KI    | KI     | KI     |
| VÉGZETES             | VÉGZETES  | HIBA   | SÚLYOS  |
| HIBA (vagy súlyos) | HIBA  | HIBA   | SÚLYOS  |
| Figyelmeztetés (vagy figyelmeztetés) | FIGYELMEZTETI   | FIGYELMEZTETI    | FIGYELMEZTETÉS |
| INFORMÁCIÓ              | INFORMÁCIÓ   | INFORMÁCIÓ    | INFORMÁCIÓ    |
| CONFIG            | HIBAKERESÉS  | HIBAKERESÉS   | CONFIG  |
| HIBAKERESÉS (vagy kiváló)   | HIBAKERESÉS  | HIBAKERESÉS   | RÉSZLETES    |
| KIFINOMULTABBAN             | HIBAKERESÉS  | HIBAKERESÉS   | KIFINOMULTABBAN   |
| NYOMKÖVETÉS (vagy legfinomabb) | NYOMKÖVETÉSI  | NYOMKÖVETÉSI   | LEGJOBB  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Ha a rendszer kivételt ad át a naplózó számára, akkor a rendszer a tábla helyett a Azure Portal megjeleníti a napló üzenetét (és kivételét) `exceptions` `traces` .

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Automatikusan összegyűjtött mérőműszer-metrikák (beleértve a Spring boot indítószerkezet metrikáit)

Ha az alkalmazás a [mikrométert](https://micrometer.io)használja, akkor a rendszer automatikusan begyűjti a Mikrométer globális beállításjegyzékbe küldendő metrikákat.

Továbbá, ha az alkalmazás a [Spring boot indítószerkezetet](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)használja, akkor a rugós rendszerindítási indítószerkezet által konfigurált mérőszámokat is automatikusan begyűjti a rendszer.

A Mikrométer metrikáinak automatikus gyűjtésének letiltása (beleértve a Spring boot-indítószerkezet metrikáit):

> [!NOTE]
> Az egyéni metrikák számlázása külön történik, és további költségek is megadhatók. Ügyeljen rá, hogy ellenőrizze a részletes [díjszabási információkat](https://azure.microsoft.com/pricing/details/monitor/). A Mikrométer és a Spring indítószerkezet metrikáinak letiltásához adja hozzá az alábbi konfigurációt a konfigurációs fájlhoz.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>Meghatározott automatikusan összegyűjtött telemetria letiltása

A 3.0.2 verziótól kezdődően a megadott automatikusan összegyűjtött telemetria ezekkel a konfigurációs beállításokkal lehet letiltani:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
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
    }
  }
}
```

## <a name="heartbeat"></a>Szívverés

Alapértelmezés szerint a Application Insights Java 3,0 15 percenként küld szívverési metrikát. Ha a szívverés metrikáját használja a riasztások elindításához, növelheti a szívverés gyakoriságát:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> A szívverés gyakorisága nem csökkenthető, mivel a szívverési adatok a Application Insights használatának nyomon követésére is használhatók.

## <a name="http-proxy"></a>HTTP-proxy

Ha az alkalmazás tűzfal mögött található, és nem tud közvetlenül kapcsolódni a Application Insightshoz (lásd: [Application Insights által használt IP-címek](./ip-addresses.md)), akkor a Application Insights Java 3,0 http-proxy használatára konfigurálhatja a következőt:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights a Java 3,0 is tiszteletben tartja a globális `-Dhttps.proxyHost` és a `-Dhttps.proxyPort` beállított értékeit.

[//]: # "Megjegyzés: a OpenTelemetry-támogatás privát előzetes verzióban érhető el, amíg a OpenTelemetry API eléri a 1,0-et"

[//]: # "# # Támogatás a OpenTelemetry API pre-1,0 kiadásához"

[//]: # "A OpenTelemetry API előre 1,0-es verziójának támogatása a következőben: opt-in, mivel a OpenTelemetry API még nem stabil"
[//]: # "így az ügynök minden verziója csak a OpenTelemetry API egy adott előre 1,0 verzióját támogatja"
[//]: # "(ez a korlátozás nem érvényes, ha a OpenTelemetry API 1,0-es verziója megjelent)."

[//]: # "' ' ' JSON"
[//]: # "{"
[//]: # "  \"előzetes verzió \" : {"
[//]: # "    \"openTelemetryApiSupport \" : true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Öndiagnosztika

Az "öndiagnosztika" a Application Insights Java 3,0 belső naplózására utal.

Ez a funkció hasznos lehet a Application Insights saját maga által felmerülő problémák felderítésében és diagnosztizálásában.

Alapértelmezés szerint a Application Insights Java 3,0 `INFO` a fájlra `applicationinsights.log` és a-konzolra is, a következő konfigurációnak megfelelően:

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

`destination` lehet a vagy az egyike `file` `console` `file+console` .

`level` lehet a,,,,, `OFF` `ERROR` `WARN` `INFO` `DEBUG` vagy `TRACE` .

`path` abszolút vagy relatív elérési út lehet. A relatív elérési utak feloldása a mappában található könyvtáron történik `applicationinsights-agent-3.0.2.jar` .

`maxSizeMb` a naplófájl maximális mérete a bedobás előtt.

`maxHistory` a megőrzött naplófájlok száma (az aktuális naplófájlon kívül).

A 3.0.2 verziótól kezdődően a környezeti változó használatával is beállíthatja az öndiagnosztika lehetőséget `level` `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` .

## <a name="an-example"></a>Példa

Ez csak egy példa arra, hogy a konfigurációs fájl hogyan néz ki több összetevővel.
Az igényeinek megfelelően konfigurálja az adott beállításokat.

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
