---
title: A betöltési és összeolvasztási szabályok közelgő változásai a Azure Time Series Insights Gen2 | Microsoft Docs
description: Betöltési szabály módosításai
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 56a1d5aab2f665f9c5bd8f6fa322f35e55483c7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995233"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Az új környezetekhez tartozó JSON-összeolvasztás és-Escape-szabályok közelgő módosításai

> [!IMPORTANT]
> Ezek a módosítások csak az *újonnan létrehozott* Microsoft Azure Time Series Insights Gen2-környezetekre lesznek alkalmazva. A módosítások nem vonatkoznak a Gen1-környezetekre.

Azure Time Series Insights Gen2-környezete dinamikusan létrehozza a tárolási oszlopokat az elnevezési konvenciók egy adott halmazának követésével. Egy esemény betöltése esetén Time Series Insights a JSON-adattartalomra és a tulajdonságok nevére alkalmazza a szabályok egy halmazát. Az új Azure Time Series Insights Gen2-környezetek esetében a JSON-adatkészletek és az azok tárolására szolgáló változások a 2020-es júliusi környezetben lépnek életbe. Ez a változás a következő esetekben van hatással:

* A JSON-adattartalom beágyazott objektumokat tartalmaz.
* A JSON-adattartalom tömböket tartalmaz.
* A következő négy speciális karakter valamelyikét használja egy JSON-tulajdonság nevében: `[` `\` `.``'`
* Egy vagy több idősoros (TS) azonosító tulajdonsága beágyazott objektumon belül van.

Ha új környezetet hoz létre, és ezek közül egy vagy több ilyen esetre vonatkozik, az adatok összevonása és tárolása másképp történik. A következő táblázat összefoglalja a módosításokat:

| Aktuális szabály | Új szabály | Példa JSON-ra | Előző oszlop neve | Új oszlop neve
|---|---| ---| ---|  ---|
| A beágyazott JSON-t egy aláhúzással, a határolóként használva kell lelapulni. |A beágyazott JSON-t egy, a határolót tartalmazó időszak használatával kell lelapulni.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| A speciális karakterek nem találhatók meg. | A speciális karaktereket tartalmazó JSON-tulajdonságnév, és a `.` `[`   `\` `'` és a használatával kerül megmenekülésre `['` `']` . `['`A és a-n belül `']` az aposztrófok és a fordított perjelek további Escape-értékekkel rendelkeznek. Egy árajánlatot `\'` és egy fordított perjelet is kell írni `\\` .  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| A primitívek tömbje karakterláncként van tárolva. | Az egyszerű típusok tömbjét dinamikus típusként tárolja a rendszer.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Az objektumok tömbje mindig össze van egyengetve, így több esemény is létrehozható. | Ha a tömbben lévő objektumok nem rendelkeznek a terminálszolgáltatási AZONOSÍTÓval vagy az időbélyegző tulajdonságaival, az objektumok tömbje a teljes dinamikus típusként tárolódik. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Új környezetek ajánlott módosításai

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Ha a terminálszolgáltatási azonosító és/vagy időbélyeg tulajdonsága egy objektumon belül van beágyazva

Minden új központi telepítésnek meg kell egyeznie az új betöltési szabályokkal. Ha például a TS `telemetry_tagId` -azonosítója, frissítenie kell a Azure Resource Manager sablonokat, vagy automatikus központi telepítésű parancsfájlokat kell konfigurálnia `telemetry.tagId` a környezet TS-azonosítójának konfigurálásához. Ezt a módosítást a beágyazott JSON-ban található eseményforrás-időbélyegek esetében is meg kell adni.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Ha a hasznos adatok beágyazott JSON-vagy speciális karaktereket tartalmaznak, és automatizálja az [idősorozat-modell](./concepts-model-overview.md) változó kifejezéseit.

Frissítse a [TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) -t végrehajtó ügyfél kódját az új betöltési szabályoknak megfelelően. Egy korábbi [Idősorozat-kifejezést](/rest/api/time-series-insights/reference-time-series-expression-syntax) például a `"value": {"tsx": "$event.series_value.Double"}` következő lehetőségek egyikére kell frissíteni:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg [Azure Time Series Insights Gen2-tárolóval és a bejövő](./concepts-ingestion-overview.md)forgalommal.

* Megtudhatja, hogyan kérdezheti le az adatait a [Time Series lekérdezési API](./concepts-query-overview.md)-k használatával.

* További információ az [új Idősorozat-kifejezés szintaxisáról](/rest/api/time-series-insights/reference-time-series-expression-syntax).