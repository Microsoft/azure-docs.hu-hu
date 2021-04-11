---
title: Konfigurációs beállítások – Azure Monitor Application Insights Javához
description: A Java-Azure Monitor Application Insights konfigurálása
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 997a4e115f8632544b2f73aef498d40dceb0d459
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449970"
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

Alapértelmezés szerint a Application Insights Java 3,0 elvárja, hogy a konfigurációs fájl legyen elnevezve `applicationinsights.json` , és hogy ugyanabban a könyvtárban legyen elhelyezve `applicationinsights-agent-3.0.3.jar` .

Megadhatja saját konfigurációs fájljának elérési útját a következők használatával

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` környezeti változó, vagy
* `applicationinsights.configuration.file` Java rendszertulajdonság

Ha relatív elérési utat ad meg, a rendszer a helyen található könyvtárhoz viszonyítva megoldódik `applicationinsights-agent-3.0.3.jar` .

## <a name="connection-string"></a>Kapcsolati sztring

A kapcsolatok karakterláncának megadása kötelező. A Application Insights erőforrásban található a kapcsolatok karakterlánca:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights a kapcsolatok karakterlánca":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

A kapcsolati karakterláncot a környezeti változó használatával is beállíthatja `APPLICATIONINSIGHTS_CONNECTION_STRING` (amely elsőbbséget élvez a JSON-konfigurációban megadott kapcsolati sztringnél).

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

A Felhőbeli szerepkör nevét a környezeti változó használatával is beállíthatja `APPLICATIONINSIGHTS_ROLE_NAME` (amely elsőbbséget élvez a JSON-konfigurációban megadott Felhőbeli szerepkör nevével).

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

A Felhőbeli szerepkör-példányt a környezeti változóval is beállíthatja `APPLICATIONINSIGHTS_ROLE_INSTANCE` (amely elsőbbséget élvez a JSON-konfigurációban megadott felhőalapú szerepkör-példánnyal szemben).

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

A mintavételezési százalékot a környezeti változó használatával is beállíthatja `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` (amely elsőbbséget élvez a JSON-konfigurációban megadott mintavételi százaléktal szemben).

> [!NOTE]
> A mintavételezési százaléknál válasszon egy olyan százalékot, amely a 100/N értéknél közelebb van, ahol N egész szám. A mintavétel jelenleg nem támogatja a többi értéket.

## <a name="sampling-overrides-preview"></a>Mintavételi felülbírálások (előzetes verzió)

Ez a funkció előzetes verzióban érhető el, a 3.0.3-től kezdve.

A mintavételi felülbírálások segítségével felülbírálhatja az [alapértelmezett mintavételi százalékot](#sampling), például:
* A mintavételezési százalékot állítsa 0 (vagy kis értékre) a zajos állapot-ellenőrzésekhez.
* A mintavételezési százalékot állítsa 0 (vagy kis értékre) a zajos függőségi hívásokhoz.
* Állítsa be a mintavételezési százalékot 100-re egy fontos kérelem típusához (például `/login` ), még akkor is, ha az alapértelmezett mintavételezés valami alacsonyabbra van konfigurálva.

További információkért tekintse meg a [mintavételi felülbírálások](./java-standalone-sampling-overrides.md) dokumentációját.

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
 * Frissítse a span nevet, amely a Azure Portal hasonló telemetria összesítésére szolgál.
 * Adott span-attribútumok eldobása a betöltési költségek szabályozásához.

További információkért tekintse meg a [telemetria-feldolgozó](./java-standalone-telemetry-processors.md) dokumentációját.

> [!NOTE]
> Ha a betöltési költségek szabályozására a konkrét (teljes) átnyúló mennyiségeket szeretné eldobni, tekintse meg a [mintavétel felülbírálását](./java-standalone-sampling-overrides.md)ismertető témakört.

## <a name="auto-collected-logging"></a>Automatikusan összegyűjtött naplózás

A Log4j, a Logback és a Java. util. Logging automatikusan lett kialakítva, és ezekkel a naplózási keretrendszerekkel végrehajtott naplózás automatikusan begyűjtve lesz.

A naplózás csak akkor rögzítve van, ha először megfelel a naplózási keretrendszerhez konfigurált szintnek, másrészt pedig megfelel a Application Insightshoz konfigurált szintnek is.

Ha például a naplózási keretrendszer úgy van konfigurálva, hogy a `WARN` csomagból (vagy felette) naplózza a csomagot `com.example` , és Application Insights úgy van beállítva, hogy rögzítse `INFO` (és a fentiket), akkor Application Insights csak `WARN` a csomagból (és felett) rögzíti `com.example` .

Application Insightshoz konfigurált alapértelmezett szint `INFO` . Ha módosítani szeretné ezt a szintet:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

A szintet a környezeti változóval is beállíthatja `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` (amely elsőbbséget élvez a JSON-konfigurációban megadott szinttel szemben).

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
> Ha egy kivételt jelző objektumot ad át a naplózó, akkor a rendszer megjeleníti a napló üzenetét (és a kivételi objektum részleteit) a tábla helyett a Azure Portal táblázatban `exceptions` `traces` .

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

## <a name="auto-collected-azure-sdk-telemetry"></a>Automatikusan összegyűjtött Azure SDK-telemetria

Ez a funkció előzetes verzióban érhető el.

A legújabb Azure SDK-kódtárak számos telemetria bocsátanak ki.

A 3.0.3 verziótól kezdődően engedélyezheti ennek a telemetria a gyűjtését:

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
(ez a beállítás elsőbbséget élvez a JSON-konfigurációban megadott beállításokkal szemben.

## <a name="suppressing-specific-auto-collected-telemetry"></a>Meghatározott automatikusan összegyűjtött telemetria letiltása

A 3.0.3 verziótól kezdődően a megadott automatikusan összegyűjtött telemetria ezekkel a konfigurációs beállításokkal lehet letiltani:

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

Ezeket a rendszerállapotokat az alábbi környezeti változók használatával is elvégezheti:

* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

(ez a beállítás elsőbbséget élvez a JSON-konfigurációban megadott beállításokkal szemben.

> Vegye figyelembe, hogy ha részletesebb szabályozásra van szüksége, például néhány Redis-hívást, de nem az összes Redis-hívást, tekintse meg a [mintavételi felülbírálások](./java-standalone-sampling-overrides.md)című témakört.

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
> Az intervallum nem növelhető 15 percnél hosszabb ideig, mert a szívverési adatok a Application Insights használatának nyomon követésére is használhatók.

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

## <a name="metric-interval"></a>Metrika intervalluma

Ez a funkció előzetes verzióban érhető el.

Alapértelmezés szerint a metrikák 60 másodpercenként lesznek rögzítve.

A 3.0.3 verziótól kezdődően módosíthatja ezt az intervallumot:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

A beállítás az összes alábbi metrikára vonatkozik:

* Alapértelmezett teljesítményszámlálók, például CPU és memória
* Alapértelmezett egyéni metrikák, például a szemét gyűjtésének időzítése
* Konfigurált JMX-metrikák ([lásd fent](#jmx-metrics))
* Mikrométer metrikái ([lásd fent](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


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

`path` abszolút vagy relatív elérési út lehet. A relatív elérési utak feloldása a mappában található könyvtáron történik `applicationinsights-agent-3.0.3.jar` .

`maxSizeMb` a naplófájl maximális mérete a bedobás előtt.

`maxHistory` a megőrzött naplófájlok száma (az aktuális naplófájlon kívül).

A 3.0.2 verziótól kezdődően beállíthatja az öndiagnosztika beállítást is `level` a környezeti változó használatával `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` (amely elsőbbséget élvez a JSON-konfigurációban megadott öndiagnosztika szinttel szemben).

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
