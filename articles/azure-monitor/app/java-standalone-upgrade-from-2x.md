---
title: Frissítés 2. x-Azure Monitor Application Insights Java-ból
description: Frissítés Azure Monitor Application Insights Java 2. x verzióról
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: 9a0e8237d81428b1ecab95627fe106a563d2090c
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532457"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Frissítés Application Insights Java 2. x SDK-ból

Ha már használja Application Insights Java 2. x SDK-t az alkalmazásban, nem kell eltávolítania.
A Java 3,0-ügynök észlelni fogja, és rögzíti és korrelálja a 2. x SDK-n keresztül küldött egyéni telemetria, miközben letiltja a 2. x SDK által végrehajtott automatikus gyűjtést a duplikált telemetria elkerülése érdekében.

Ha Application Insights 2. x ügynököt használta, el kell távolítania a JVM ARG-t, `-javaagent:` amely a 2. x ügynökre mutat.

A dokumentum többi része a 2. x és a 3,0 közötti frissítés során felmerülő korlátozásokat és változásokat, valamint a hasznos megoldásokat is tartalmaz.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers és TelemetryProcessors

A 2. x SDK-TelemetryInitializers és a TelemetryProcessors nem fog futni a 3,0-ügynök használata esetén.
A korábban megkövetelt használati esetek többsége 3,0-ban oldható meg az [Egyéni dimenziók](./java-standalone-config.md#custom-dimensions) konfigurálásával vagy a [telemetria processzorok](./java-standalone-telemetry-processors.md)konfigurálásával.

## <a name="multiple-applications-in-a-single-jvm"></a>Több alkalmazás egyetlen JVM

Jelenleg a 3,0 csak egyetlen [kapcsolatok sztringjét és a szerepkör nevét](./java-standalone-config.md#connection-string-and-role-name) támogatja a futtatási folyamat során. Különösen nem rendelkezhet több tomcat-webalkalmazással ugyanazon a Tomcat-telepítésben eltérő kapcsolati karakterláncok vagy eltérő szerepkör-nevek használatával.

## <a name="operation-names"></a>Műveletek nevei

A 3,0-es műveleti nevek általában úgy változtak, hogy a Application Insights portál U/X-ben jobb összesített nézetet biztosítanak.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="Műveleti nevek a 3,0-ben":::

Egyes alkalmazások esetében azonban továbbra is használhatja a korábbi műveleti nevek által biztosított aggregált nézetet az U/X-ben, ebben az esetben a 3,0-es [telemetria processzorok](./java-standalone-telemetry-processors.md) (előzetes verzió) funkcióját használhatja a korábbi viselkedés replikálásához.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>A művelet nevének előtagja a http-metódussal ( `GET` , `POST` stb.)

A 2. x SDK-ban a műveleti neveket a http-metódus ( `GET` , `POST` stb.) előre rögzítették, például:

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Http-metódus által előrögzített műveleti nevek":::

Az alábbi kódrészlet 3 telemetria processzort állít be, amelyek az előző viselkedés replikálásához vannak egyesítve.
A telemetria processzorok a következő műveleteket hajtják végre (sorrendben):

1. Az első telemetria processzor egy span processzor (típus `span` ), ami azt jelenti, hogy a és a rendszerre vonatkozik `requests` `dependencies` .

   Minden olyan span egyezik, amely egy nevű attribútummal rendelkezik, `http.method` és rendelkezik egy, a-val kezdődő tartománynévvel `/` .

   Ezután kicsomagolja az adott tartomány nevét egy nevű attribútumba `tempName` .

2. A második telemetria processzor is egy span processzor.

   A rendszer minden olyan tartományhoz egyeztet, amely rendelkezik nevű attribútummal `tempName` .

   Ezután frissíti a span nevet a két attribútum összefűzésével `http.method` `tempName` , szóközzel elválasztva.

3. Az utolsó telemetria processzor egy attribútum processzor (típus `attribute` ), ami azt jelenti, hogy az összes olyan telemetria vonatkozik, amely rendelkezik attribútumokkal (jelenleg `requests` `dependencies` és `traces` ).

   Minden olyan telemetria meg fog egyezni, amely egy nevű attribútummal rendelkezik `tempName` .

   Ezután törli a nevű attribútumot `tempName` , így azt nem egyéni dimenzióként fogja jelenteni.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="set-the-operation-name-to-the-full-path"></a>A művelet nevének beállítása a teljes elérési útra

Emellett a 2. x SDK-ban bizonyos esetekben a műveletek nevei a teljes elérési utat tartalmazzák, például:

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Teljes elérési úttal rendelkező műveletek nevei":::

Az alábbi kódrészlet 4 telemetria processzort állít be, amelyek az előző viselkedés replikálásához vannak egyesítve.
A telemetria processzorok a következő műveleteket hajtják végre (sorrendben):

1. Az első telemetria processzor egy span processzor (típus `span` ), ami azt jelenti, hogy a és a rendszerre vonatkozik `requests` `dependencies` .

   A rendszer minden olyan tartományhoz egyeztet, amely rendelkezik nevű attribútummal `http.url` .

   Ezután frissíti a span nevet az `http.url` attribútumérték értékével.

   Ez lesz a vége, kivéve, `http.url` Ha valami hasonlót keres `http://host:port/path` , és valószínű, hogy csak a részt kívánja használni `/path` .

2. A második telemetria processzor is egy span processzor.

   Ez minden olyan tartományhoz igazodik, amely rendelkezik egy nevű attribútummal `http.url` (azaz bármely, az első processzor által egyeztetett tartományba).

   Ezután kicsomagolja a span Name (elérési út) részét egy nevű attribútumba `tempName` .

3. A harmadik telemetria processzor is egy span processzor.

   A rendszer minden olyan tartományhoz egyeztet, amely rendelkezik nevű attribútummal `tempPath` .

   Ezt követően a rendszer frissíti a span nevet az attribútumból `tempPath` .

4. Az utolsó telemetria processzor egy attribútum processzor (típus `attribute` ), ami azt jelenti, hogy az összes olyan telemetria vonatkozik, amely rendelkezik attribútumokkal (jelenleg `requests` `dependencies` és `traces` ).

   Minden olyan telemetria meg fog egyezni, amely egy nevű attribútummal rendelkezik `tempPath` .

   Ezután törli a nevű attribútumot `tempPath` , így azt nem egyéni dimenzióként fogja jelenteni.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="dependency-names"></a>Függőségek nevei

A függőségek nevei 3,0-ben is módosultak, és ismét a Application Insights portál U/X-ben általában jobb összesített nézetet biztosítunk.

Néhány alkalmazás esetében továbbra is használhatja a korábbi függőségi nevek által biztosított, az U/X-ben összesített nézetet, amely esetben az előző viselkedés replikálásához a fentiek szerint hasonló technikákat is használhat.

## <a name="operation-name-on-dependencies"></a>Művelet neve a függőségeken

Korábban a 2. x SDK-ban a kérelem telemetria tartozó művelet neve is be lett állítva a függőségi telemetria.
Application Insights Java 3,0 már nem tölti fel a művelet nevét a függőségi telemetria.
Ha szeretné megtekinteni a művelet nevét, amely a függőségi telemetria szülője, írhat naplókat (Kusto) tartalmazó lekérdezést, amely a függőségi táblából a kérési táblához csatlakozik.
