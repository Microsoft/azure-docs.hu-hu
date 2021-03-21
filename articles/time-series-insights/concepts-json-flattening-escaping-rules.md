---
title: JSON-összeolvasztás és-Escape-szabályok – Azure Time Series Insights Gen2 | Microsoft Docs
description: Ismerkedjen meg a JSON-összeolvasztással, a szökéssel és a tömb-kezelési Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 9f768982e69f785c146f026040a91f7a63eef64c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673451"
---
# <a name="json-flattening-escaping-and-array-handling"></a>JSON-egybesimítás, escape-karaktersorozat és tömbkezelés

A Azure Time Series Insights Gen2-környezet dinamikusan létrehozza a meleg és a hűtőházi tárolók oszlopait, az elnevezési konvenciók egy adott halmazát követve. Egy esemény betöltése esetén a rendszer a JSON-adattartalomra és a tulajdonságok nevére alkalmazza a szabályok halmazát. Ilyenek például bizonyos speciális karakterek elhagyása és a beágyazott JSON-objektumok összeolvasztása. Fontos tudni ezeket a szabályokat, hogy tisztában legyen azzal, hogy a JSON-alakzat milyen hatással van az események tárolására és lekérdezésére. A szabályok teljes listájáért tekintse meg az alábbi táblázatot. Példák A & B azt is bemutatják, hogyan lehet hatékonyan batch több idősorozatot felvenni egy tömbben.

> [!IMPORTANT]
>
> * Tekintse át az alábbi szabályokat, mielőtt kiválasztja az [Idősorozat-azonosító tulajdonságot](./how-to-select-tsid.md) , és/vagy az eseményforrás [timestamp (IES)](concepts-streaming-ingestion-event-sources.md#event-source-timestamp). Ha a terminálszolgáltatási azonosító vagy időbélyeg egy beágyazott objektumon belül van, vagy egy vagy több speciális karakterből áll, akkor fontos, hogy az Ön által megadott tulajdonságnév megfeleljen az oszlop nevének a betöltési szabályok alkalmazása *után* . Lásd a lenti [B](concepts-json-flattening-escaping-rules.md#example-b) példát.

| Szabály | Példa JSON-ra | [Idősorozat-kifejezés szintaxisa](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Tulajdonság oszlopának neve a parkettában
|---|---|---|---|
| A Azure Time Series Insights Gen2 adattípusa az oszlopnév végéhez lesz hozzáfűzve "_" értékként. \<dataType\> | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| Az eseményforrás [timestamp tulajdonsága](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) Azure Time Series Insights Gen2 lesz mentve a tárolóban, és az UTC szerint tárolt érték. Az eseményforrás (ok) timestamp tulajdonsága testreszabható úgy, hogy megfeleljen a megoldás igényeinek, de az oszlop neve a meleg és a hideg tárolóban "Timestamp". Más datetime JSON-tulajdonságok, amelyek nem az eseményforrás időbélyege, az oszlopnév "_datetime" lesz mentve, ahogy az a fenti szabályban szerepel.  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| A speciális karaktereket tartalmazó JSON-tulajdonságok neve. [\ és "a (z) [" és "] használatával kerül megmenekülésre  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| A (z) ["és"] keretben további Escape-értékekkel és fordított perjelekkel rendelkezik. A rendszer egy idézőjelet (\) ír, a fordított perjelet pedig \\\ | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| A beágyazott JSON-objektumok az elválasztó ponttal vannak lelapulva. A legfeljebb 10 szint beágyazását támogatja. |  ```"series": {"value" : 316 }``` | `$event.series.value.Long`, `$event['series']['value'].Long` vagy `$event.series['value'].Long` |  `series.value_long` |
| Az egyszerű típusok tömbjét dinamikus típusként tárolja a rendszer. |  ```"values": [154, 149, 147]``` | A dinamikus típusok csak a [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API használatával kérhetők le | `values_dynamic` |
| Az objektumokat tartalmazó tömbök két viselkedése függ az objektum tartalmától: Ha a TS-azonosító (k) vagy a timestamp tulajdonság (ok) egy tömb objektumain belül van, akkor a tömb úgy lesz kivezetve, hogy a kezdeti JSON-adattartalom több eseményt állít elő. Ez lehetővé teszi több esemény egy JSON-struktúrába való kötegelt feldolgozását. A rendszer minden olyan legfelső szintű tulajdonságot, amely a tömbhöz csatlakozik, a rendszer minden egyes unrolled objektummal menti. Ha a TS-azonosító (k) és az időbélyeg *nem* a tömbön belül van, akkor a rendszer az egészet dinamikus típusként menti. | Lásd az alábbi példákat: [A](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b)és [C](concepts-json-flattening-escaping-rules.md#example-c)
| A vegyes elemeket tartalmazó tömbök nem lapított. |  ```"values": ["foo", {"bar" : 149}, 147]``` | A dinamikus típusok csak a [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API használatával kérhetők le | `values_dynamic` |
| 512 karakter a JSON-tulajdonság nevének korlátja. Ha a név hossza meghaladja az 512 karaktert, a rendszer csonkolja a 512 és a "_< kivonatoló kódot tartalmazó" > "utótagot. **Vegye figyelembe** , hogy ez az objektum által összefűzött, beágyazott objektum elérési útját jelölő tulajdonságok neveire is vonatkozik. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>A tömbök kettős viselkedésének megértése

Az objektumok tömbjét a rendszer az adatmodellezéstől függően teljes egészében tárolja vagy felosztja több eseményre. Ez lehetővé teszi, hogy tömböt használjon a Batch-eseményekhez, és elkerülje az ismétlődő telemetria-tulajdonságokat, amelyek a gyökérszintű objektum szintjén vannak meghatározva. A kötegelt feldolgozás előnyös lehet, mivel kevesebb Event Hubs vagy IoT Hub küldött üzenetet eredményez.

Bizonyos esetekben azonban az objektumokat tartalmazó tömbök csak más értékek kontextusában vannak értelmezve. Több esemény létrehozásakor a jelentés nem fog megjelenni. Az alábbi adatmodellezési útmutatást követve biztosíthatja, hogy az objektumok tömbje dinamikus típusúként legyen tárolva, és tekintse meg a [C példát](concepts-json-flattening-escaping-rules.md#example-c) .

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>Honnan tudhatja meg, hogy az objektumok tömbje több eseményt fog-e létrehozni

Ha egy vagy több idősorozat-azonosító (IES) be van ágyazva egy tömb objektumaiba, *vagy* ha az eseményforrás timestamp tulajdonsága be van ágyazva, a betöltési motor kibontja a több esemény létrehozásához. A terminálszolgáltatási azonosító (k) hoz és/vagy az időbélyeghez megadott tulajdonságok neveinek a fenti simítási szabályokat kell követniük, és ezért a JSON-alakot jelölik. Tekintse meg az alábbi példákat, és tekintse meg az [Idősorozat-azonosító tulajdonság kiválasztásának](./how-to-select-tsid.md) útmutatóját.

### <a name="example-a"></a>Példa

Idősorozat-azonosító az objektum gyökerében és az időbélyegző beágyazva \
**Környezeti idő sorozatának azonosítója:**`"id"`\
**Eseményforrás időbélyegzője:**`"values.time"`\
**JSON-adattartalom:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Eredmény a parketta-fájlban:**\
A fenti konfiguráció és adattartalom három oszlopot és négy eseményt hoz létre

| időbélyeg  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>B példa

Összetett idősorozat-azonosító egyetlen tulajdonsággal ágyazva \
**Környezeti idő sorozatának azonosítója:** `"plantId"` és `"telemetry.tagId"`\
**Eseményforrás időbélyegzője:**`"timestamp"`\
**JSON-adattartalom:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Eredmény a parketta-fájlban:**\
A fenti konfiguráció és hasznos adatok négy oszlopot és hat eseményt eredményeznek

| időbélyeg  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  – 31,149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20,560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | – 30,9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19,960796 |

### <a name="example-c"></a>C példa

Az idősorozat-azonosító és az időbélyeg a következő objektumban található: root \
**Környezeti idő sorozatának azonosítója:**`"id"`\
**Eseményforrás időbélyegzője:**`"timestamp"`\
**JSON-adattartalom:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Eredmény a parketta-fájlban:**\
A fenti konfiguráció és hasznos adatok három oszlopot és egy eseményt eredményeznek

| időbélyeg  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>Következő lépések

* A környezet [adatátviteli korlátainak](./concepts-streaming-ingress-throughput-limits.md) megismerése
