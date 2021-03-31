---
title: Hosszú adattípusok támogatása a Azure Time Series Insights Gen2 | Microsoft Docs
description: Hosszú adattípus támogatása Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/07/2020
ms.custom: dpalled
ms.openlocfilehash: 0f7ac9844bfe7eac7094d3b7fdf653e07f236599
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780824"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Hosszú adattípusok támogatásának hozzáadása a Azure Time Series Insights Gen2

A hosszú adattípusok támogatásának kiegészítése befolyásolja, hogy a numerikus adatok csak Azure Time Series Insights Gen2-környezetekben legyenek tárolva és indexelve. Ha Gen1-környezettel rendelkezik, figyelmen kívül hagyhatja ezeket a módosításokat.

Az 2020-tól június 29-én vagy június 30-án kezdődően a régiótól függően az adatai **hosszúak** és **kétszeresak** lesznek indexelve.  Ha bármilyen kérdése vagy problémája van a változással kapcsolatban, küldjön egy támogatási jegyet a Azure Portal és megemlítjük ezt a kommunikációt.

Ha a következő esetek valamelyikét érinti, végezze el az ajánlott módosításokat:

- **1. eset**: jelenleg az idősorozat-modell változóit használja, és csak a telemetria-adataiban található szerves adattípusokat küldi el.
- **2. eset**: jelenleg az idősorozat-modell változóit használja, és a telemetria-adataiban is elküldheti az integrált és a nem integrált adattípusokat.
- **3. eset**: kategorikus változók használata az egész értékek kategóriákra való leképezéséhez.
- **4. eset**: a JavaScript SDK használatával hozzon létre egy egyéni előtér-alkalmazást.
- **5. eset**: közel van a 1 000-tulajdonságnév korlátja a meleg tárolóban, és mind az integrált, mind a nem integrált adatküldés. A tulajdonságok száma metrikaként is megtekinthető a [Azure Portalban](https://portal.azure.com/).

Ha bármelyik eset Önre vonatkozik, módosítsa a modellt. Frissítse az idősorozat-kifejezést (TSX) a változó definíciójában a javasolt módosításokkal. Mindkettő frissítése:

- Azure Time Series Insights Explorer
- Az API-kat használó egyéni ügyfelek

A IoT-megoldástól és a megkötéstől függően előfordulhat, hogy nem láthatók a Azure Time Series Insights Gen2-környezetbe elküldett adatai. Ha nem biztos abban, hogy az adatai csak integráltak, vagy mind az integrált, sem a nem integrált, néhány lehetőség közül választhat:

- Megvárhatja a funkció felszabadítását. Ezután vizsgálja meg a nyers eseményeit az Explorer felhasználói felületén, és ismerkedjen meg a két különálló oszlopba mentett tulajdonságokkal.
- A javasolt módosításokat megelőző jelleggel teheti az összes numerikus címkén.
- Az események egy részhalmazát ideiglenesen átirányíthatja a tárolóba a séma jobb megismerése és megismerése érdekében.

Az események tárolásához kapcsolja be az Azure Event Hubs az [esemény-rögzítést](../event-hubs/event-hubs-capture-overview.md) , vagy a IoT hub az Azure Blob Storage-ba [irányítsa](../iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint) .

Az adatkezelés az [Event hub Explorerben](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)vagy az [Event Processor Host](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md#receive-events)használatával is megfigyelhető.

Ha IoT Hub használ, ugorjon az [eszközről a felhőbe irányuló üzenetek olvasására a beépített végpontról](../iot-hub/iot-hub-devguide-messages-read-builtin.md) a beépített végpont eléréséhez.

> [!NOTE]
> Ha nem hajtja végre az ajánlott módosításokat, előfordulhat, hogy a rendszer megszakítja a folyamatot. Például a lekérdezési API-kon keresztül elért érintett Time Series Insights változók, vagy a Time Series Insights Explorer **Null értéket** ad vissza (azaz nem jeleníti meg az adatkezelőt).

## <a name="recommended-changes"></a>Ajánlott módosítások

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>1. eset: idősorozat-modell változók használata és csak az telemetria-adatokban található szerves adattípusok küldése

Az 1. eset esetében a javasolt módosítások megegyeznek a 2. esettel. Kövesse a 2. eset című szakasz utasításait.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>2. eset: az idősorozat-modell változóinak használata, valamint az integrált és a nem integrált típusok küldése a telemetria-adatokban

Ha jelenleg az egész telemetria adatait küldi el, az adatai két oszlopba lesznek osztva:

- **propertyValue_double**
- **propertyValue_long**

Az egész számú adatot a **propertyValue_longba** írja. A korábban betöltött (és a jövőben betöltött) numerikus adatot **propertyValue_double** nem másolja át a rendszer.

Ha a **tulajdonságérték** tulajdonság ezen két oszlopán keresztül szeretné lekérdezni az adatait, akkor az **Egyesítés ()** skaláris függvényt kell használnia a TSX. A függvény elfogadja az azonos **adattípusú** argumentumokat, és az első nem null értéket adja vissza az argumentumok listájában. További információ: [Azure Time Series Insights Gen2 adatelérési fogalmai](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions).

#### <a name="variable-definition-in-tsx---numeric"></a>Változó definíciója a TSX – numerikus

*Előző változó definíciója:*

[![A képernyőképen az új változó hozzáadása párbeszédpanel jelenik meg a tulajdonságérték változóhoz, numerikus értékre.](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Új változó definíciója:*

[![Képernyőfelvétel: az új változó hozzáadása párbeszédpanel az tulajdonságérték változóhoz egyéni értékkel (numerikus).](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Az **Egyesítés ($Event. tulajdonságérték. Double, a toDouble ($Event. tulajdonságérték. Long))** is használható az egyéni [Idősorozat-kifejezésként](/rest/api/time-series-insights/reference-time-series-expression-syntax).

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>Beágyazott változó definíciója a TSX lekérdezési API-k használatával – numerikus

*Előző változó definíciója:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*Új változó definíciója:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

Az **Egyesítés ($Event. tulajdonságérték. Double, a toDouble ($Event. tulajdonságérték. Long))** is használható az egyéni [Idősorozat-kifejezésként](/rest/api/time-series-insights/reference-time-series-expression-syntax).

> [!NOTE]
> Azt javasoljuk, hogy minden olyan helyen frissítse ezeket a változókat, amelyeket érdemes használni. Ezen helyek közé tartozik a Time Series-modell, a mentett lekérdezések és az Power BI-összekötő lekérdezései.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>3. eset: kategorikus változók használata egész értékek kategóriákra való leképezéséhez

Ha jelenleg olyan kategorikus változókat használ, amelyek egész értékeket rendelnek a kategóriákhoz, valószínű, hogy a **toLong** függvényt használja az adatok **dupla** típusról **hosszú** típusra való átalakításához. Az 1. és a 2. esetekhez hasonlóan egyesíteni kell a **dupla** és a **hosszú** **adattípusú** oszlopokat.

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>Változó definíciója a Time Series Explorerben – kategorikus

*Előző változó definíciója:*

[![Képernyőfelvétel: az új változó hozzáadása párbeszédpanel a tulajdonságérték változóhoz, kategorikus.](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Új változó definíciója:*

[![Képernyőfelvétel: az új változó hozzáadása párbeszédpanel a tulajdonságérték változóhoz egyéni értékkel, kategorikus.](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Az **Egyesítés ($Event. tulajdonságérték. Double, a toDouble ($Event. tulajdonságérték. Long))** is használható az egyéni [Idősorozat-kifejezésként](/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

A kategorikus változók esetében az értéknek egész típusúnak kell lennie. Az **Egyesítés ()** összes argumentumának **adattípusa** csak **hosszú** lehet az egyéni [idősorozat kifejezésében.](/rest/api/time-series-insights/reference-time-series-expression-syntax)

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>Beágyazott változó definíciója a TSX Query API-k használatával – kategorikus

*Előző változó definíciója:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*Új változó definíciója:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

A kategorikus változók esetében az értéknek egész típusúnak kell lennie. Az **Egyesítés ()** összes argumentumának **adattípusa** csak **hosszú** lehet az egyéni [idősorozat kifejezésében](/rest/api/time-series-insights/reference-time-series-expression-syntax).

> [!NOTE]
> Azt javasoljuk, hogy minden olyan helyen frissítse ezeket a változókat, amelyeket érdemes használni. Ezen helyek közé tartozik a Time Series-modell, a mentett lekérdezések és az Power BI-összekötő lekérdezései.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>4. eset: a JavaScript SDK használatával hozzon létre egy egyéni előtér-alkalmazást

Ha az 1 – 3. eset által érintett és egyéni alkalmazásokat készít, frissítenie kell a lekérdezéseket az **Egyesítés ()** függvény használatára, ahogy az az előző példákban is látható.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>5. eset: a meleg tároló 1 000-es tulajdonságának korlátozása

Ha nagy mennyiségű tulajdonsággal rendelkező meleg áruházbeli felhasználó, és úgy gondolja, hogy ez a változás az 1 000-es meleg áruházbeli tulajdonság-név korlátot fogja leküldeni a környezetbe, küldjön támogatási jegyet a Azure Portal és megemlítjük ezt a kommunikációt.

## <a name="next-steps"></a>Következő lépések

- A [támogatott adattípusok](concepts-supported-data-types.md)teljes listájának megtekintése.
