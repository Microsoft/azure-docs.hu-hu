---
title: Támogatott adattípusok – Azure Time Series Insights Gen2 | Microsoft Docs
description: Ismerkedjen meg a támogatott adattípusokkal Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 8e8e6f007e19d2514a5f75dbdda7da13e1f8be3c
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505178"
---
# <a name="supported-data-types"></a>Támogatott adattípusok

A következő táblázat felsorolja a Azure Time Series Insights Gen2 által támogatott adattípusokat

| Adattípus | Leírás | Példa | [Idősorozat-kifejezés szintaxisa](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Tulajdonság oszlopának neve a parkettában
|---|---|---|---|---|
| **logikai** | Olyan adattípus, amely két állapot egyikét adja meg: `true` vagy `false` . | `"isQuestionable" : true` | `$event.isQuestionable.Bool` vagy `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **dátum/idő** | Egy azonnali időpontot jelöl, amely általában dátum és napszak szerint van megadva. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) formátumban kifejezve. A DateTime tulajdonságokat a rendszer mindig UTC formátumban tárolja. Az időzóna-eltolások, ha megfelelően vannak formázva, a rendszer alkalmazza, majd az UTC szerint tárolt értéket fogja alkalmazni. Tekintse meg [ezt](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) a szakaszt a környezet timestamp tulajdonságával és a DateTime típusú eltolásokkal kapcsolatban. | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  Ha a "eventProcessedLocalTime" az eseményforrás időbélyegzője: `$event.$ts` . Ha ez egy másik JSON-tulajdonság: `$event.eventProcessedLocalTime.DateTime` vagy `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | Kétszeres pontosságú 64 bites szám  | `"value": 31.0482941` | `$event.value.Double` vagy `$event['value'].Double` |  `value_double`
| **long** | Aláírt 64 bites egész szám  | `"value" : 31` | `$event.value.Long` vagy `$event['value'].Long` |  `value_long`
| **sztring** | A szöveges értékeknek érvényes UTF-8 típusúnak kell lenniük. A null értékű és az üres karakterláncok azonosak. |  `"site": "DIM_MLGGG"`| `$event.site.String` vagy `$event['site'].String`| `site_string`
| **dinamikus** | Egy tömbből vagy egy tárolóból (szótárból) álló összetett (nem primitív) típus. Jelenleg csak a sztringesített vagy a TS ID-t, illetve az időbélyeg-tulajdonságot (IES) tartalmazó tömböket tartalmazó JSON-tömbök lesznek tárolva dinamikusként. Ebből a [cikkből](./concepts-json-flattening-escaping-rules.md) megtudhatja, hogyan lehet összeállítani az objektumokat, és lehet, hogy a tömbök nem állnak le. Az ebben a típusban tárolt hasznos adatok tulajdonságai csak a `Explore Events` Time Series Insights Explorerben láthatók a nyers események megtekintéséhez, vagy az [`GetEvents`](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) ügyféloldali elemzéshez használt LEKÉRDEZÉSi API-n keresztül. |  `"values": "[197, 194, 189, 188]"` | Egy idősorozat-kifejezésben lévő dinamikus típusok hivatkozása még nem támogatott | `values_dynamic`

> [!NOTE]
> 64 bites egész értékek támogatottak, de a Azure Time Series Insights Explorer által biztonságosan kiértékelhető legnagyobb szám a JavaScript-korlátozások miatt 9 007 199 254 740 991 (2 ^ 53-1). Ha a fenti adatmodellben szereplő számok használatával dolgozik, csökkentheti a méretet egy [idősorozat-modell változó](./concepts-variables.md#numeric-variables) létrehozásával és az érték [átalakításával](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) .

> [!NOTE]
> A **sztring** típusa nem üres:
>
> * Egy idősorozat- [lekérdezésben](/rest/api/time-series-insights/reference-query-apis) kifejezett [idősorozat-kifejezés (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax) , amely egy üres karakterlánc (**""**) **Null** értékkel való összevetésével hasonlítja össze az értéket, ugyanúgy viselkedik, mint a következő: `$event.siteid.String = NULL` `$event.siteid.String = ''` .
> * Előfordulhat, hogy az API **Null** értékeket ad vissza, még akkor is, ha az eredeti események üres karakterláncokat tartalmaznak.
> * A **karakterlánc** -oszlopokban lévő **Null** értékektől való függőséget ne használja összehasonlítások vagy értékelések elvégzéséhez, ugyanúgy kezelje őket, mint az üres karakterláncokat.

## <a name="sending-mixed-data-types"></a>Vegyes adattípusok küldése

A Azure Time Series Insights Gen2-környezete erősen be van írva. Ha az eszközök vagy címkék különböző típusú adattípusokat küldenek egy eszköz tulajdonságnak, az értékeket két külön oszlopban tárolja a rendszer, és az [Egyesítés () függvényt](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) kell használni, amikor az API-hívásokban az idősorozat-modell változó kifejezéseit definiálja.

A Azure Time Series Insights Explorer lehetővé teszik, hogy automatikusan egyesítse az azonos eszköz tulajdonságának különálló oszlopait. Az alábbi példában az érzékelő egy olyan `PresentValue` tulajdonságot küld, amely egyszerre hosszú vagy dupla is lehet. Ha a tulajdonság összes tárolt értékére (az adattípustól függetlenül) szeretne lekérdezni `PresentValue` , válassza `PresentValue (Double | Long)` a és az oszlopok coalesced lehetőséget.

[![Explorer automatikus egyesítése](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objektumok és tömbök

Az esemény hasznos adatainak részeként összetett típusokat (például objektumokat és tömböket) is küldhet. A beágyazott objektumokat a rendszer lelapulja, és a tömbök a `dynamic` környezeti konfigurációtól és a JSON-alakzattól függően több esemény előállítására lesznek tárolva. További információ a [JSON-összeolvasztási és-Escape-szabályokról](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Következő lépések

* Olvassa el a [JSON-összeolvasztási és-Escape-szabályokat](./concepts-json-flattening-escaping-rules.md) , hogy megtudja, hogyan lesznek tárolva az események.

* A környezet [adatátviteli korlátainak](./concepts-streaming-ingress-throughput-limits.md) megismerése

* Ismerje meg az adatfolyamok betöltéséhez szükséges [esemény-forrásokat](concepts-streaming-ingestion-event-sources.md) .
