---
title: Mintavételi felülbírálások (előzetes verzió) – Azure Monitor Application Insights Javához
description: Megtudhatja, hogyan konfigurálhat mintavételi felülbírálásokat Azure Monitor Application Insights Javához.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 03d3093f14d97b2cc64d91e0d1b7adf34204a021
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962519"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Mintavételi felülbírálások (előzetes verzió) – Azure Monitor Application Insights Javához

> [!NOTE]
> A mintavételi felülbírálások funkciója előzetes verzióban érhető el.

Íme néhány felhasználási eset a mintavételezési felülbírálásokhoz:
 * Az telemetria gyűjtésének mellőzése.
 * A zajos függőségi hívások telemetria gyűjtésének mellőzése.
 * Csökkentse az állapot-ellenőrzés vagy a zajos függőségi hívások okozta zajt anélkül, hogy teljesen letiltsa őket.
 * A telemetria 100%-át egy fontos kérelem típusához (pl.) gyűjti, `/login` még akkor is, ha az alapértelmezett mintavételezés egy alacsonyabb értékre van konfigurálva.

## <a name="terminology"></a>Terminológia

A mintavételi felülbírálások megismerése előtt meg kell értenie *az időtartamot.* A span a következő általános kifejezése:

* Egy bejövő kérelem.
* Kimenő függőség (például távoli hívás egy másik szolgáltatásnak).
* Folyamaton belüli függőség (például a szolgáltatás alösszetevői végzik a munkát).

A mintavételi felülbírálások esetében ezek a span-összetevők fontosak:

* Attribútumok

A span attribútumok az adott kérelem vagy függőség standard és egyéni tulajdonságait jelölik.

## <a name="getting-started"></a>Első lépések

A kezdéshez hozzon létre egy *applicationinsights.js* nevű konfigurációs fájlt. Mentse azt ugyanabban a könyvtárban, mint a *applicationinsights-Agent- \* . jar*. Használja az alábbi sablont.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Működés

Ha a rendszer elindít egy span-t, az adott időtartományon található attribútumok alapján ellenőrizhető, hogy a mintavételi felülbírálások bármelyike egyezik-e.

Ha a mintavételezési felülbírálások egyike egyezik, a rendszer a mintavételezési százalékos arányt használja annak eldöntésére, hogy a span vagy sem.

Csak az első mintavételezési felülbírálás van használatban.

Ha a mintavételi felülbírálások nem egyeznek:

* Ha ez a nyomkövetés első tartománya, akkor a rendszer a [normál mintavételi százalékot](./java-standalone-config.md#sampling) használja.
* Ha ez nem a nyomkövetés első tartománya, akkor a rendszer a szülő mintavételezési döntését használja.

> [!IMPORTANT]
> Ha döntés születik, hogy a rendszer ne gyűjtsön egy kört, akkor a rendszer nem gyűjti össze az összes alsóbb rétegbeli átadást, még akkor is, ha vannak olyan mintavételi felülbírálások, amelyek megfelelnek az alsóbb rétegbeli tartománynak.
> Ez a viselkedés azért szükséges, mert egyébként hibás nyomkövetés következik be, és az alsóbb rétegbeli találatok gyűjtése folyamatban van, de a rendszer a nem gyűjtött információk alapján származtatja azokat.

> [!NOTE]
> A mintavételi döntés alapjául a traceId (más néven operationId) kivonatolása 0 és 100 közötti számra, a kivonatot pedig a mintavételezési arányhoz képest.
> Mivel egy adott nyomkövetés minden elterjedése azonos traceId fog rendelkezni, ugyanazzal a kivonattal fog rendelkezni, így a mintavételi döntés konzisztens lesz a teljes nyomkövetésben.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Példa: az telemetria gyűjtésének letiltása az állapot-ellenőrzésekhez

Ez letiltja az összes kérelem telemetria gyűjtését `/health-checks` .

Ez azt is letiltja, hogy a rendszer minden olyan alsóbb rétegbeli (függőségi) összegyűjtést is begyűjt, amelyet általában a ( `/health-checks` )

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Példa: a telemetria begyűjtésének mellőzése zajos függőségi hívás esetén

Ez letiltja az összes Redis-hívás telemetria gyűjtését `GET my-noisy-key` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Példa: Gyűjtse össze a telemetria 100%-át egy fontos kérelem típusához

Ez a művelet a telemetria 100%-át gyűjti `/login` .

Mivel az alsóbb rétegbeli kiosztások (függőségek) betartják a szülő mintavételi döntését (az adott alsóbb réteghez tartozó mintavételi felülbírálások hiányában), az összes "i" kérelem esetében is össze lesznek gyűjtve.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Általános span attribútumok

Ez a szakasz felsorolja azokat a közös span-attribútumokat, amelyeket a mintavételezési felülbírálások használhatnak.

### <a name="http-spans"></a>HTTP-Átívelés

| Attribútum  | Típus | Description | 
|---|---|---|
| `http.method` | sztring | HTTP-kérelem módszere.|
| `http.url` | sztring | Teljes HTTP-kérelem URL-címe az űrlapon `scheme://host[:port]/path?query[#fragment]` . A töredék általában nem HTTP-n keresztül történik. Ha azonban ismert a töredék, azt bele kell foglalni.|
| `http.status_code` | szám | [Http-válasz állapotának kódja](https://tools.ietf.org/html/rfc7231#section-6)|
| `http.flavor` | sztring | A HTTP-protokoll típusa. |
| `http.user_agent` | sztring | Az ügyfél által eljuttatott [http User-Agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) fejléc értéke. |

### <a name="jdbc-spans"></a>JDBC-átívelő

| Attribútum  | Típus | Description  |
|---|---|---|
| `db.system` | sztring | Az adatbázis-kezelő rendszer (adatbázisok) által használt termék azonosítója. |
| `db.connection_string` | sztring | Az adatbázishoz való kapcsolódáshoz használt kapcsolati karakterlánc. Javasoljuk, hogy távolítsa el a beágyazott hitelesítő adatokat.|
| `db.user` | sztring | Az adatbázis eléréséhez használt Felhasználónév. |
| `db.name` | sztring | Az elérni kívánt adatbázis nevének jelentésére szolgáló sztring. Az adatbázist átváltó parancsok esetén a karakterláncot a célként megadott adatbázisra kell beállítani, még akkor is, ha a parancs végrehajtása sikertelen.|
| `db.statement` | sztring | Az adatbázis-utasítás fut.|
