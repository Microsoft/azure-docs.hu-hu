---
title: Események küldése egy környezetbe – Azure Time Series Insights | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Event hubot, hogyan futtathat egy minta alkalmazást, és hogyan küldhet eseményeket a Azure Time Series Insights-környezetbe.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 8ee2cc5473d83eafc2443f3aea7cab6a2e77727c
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463561"
---
# <a name="send-events-to-an-azure-time-series-insights-gen1-environment-by-using-an-event-hub"></a>Események küldése egy Azure Time Series Insights Gen1-környezetbe az Event hub használatával

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat egy Event hub-t az Azure Event Hubsban. Azt is leírja, hogyan futtathat egy minta alkalmazást, hogy leküldéses eseményeket Azure Time Series Insights a Event Hubsról. Ha van egy meglévő Event hub JSON formátumú eseményekkel, ugorja át ezt az oktatóanyagot, és tekintse meg a környezetét [Azure Time Series Insightsban](./tutorial-set-up-environment.md).

## <a name="configure-an-event-hub"></a>Eseményközpont konfigurálása

1. Az Event hub létrehozásának megismeréséhez olvassa el a [Event Hubs dokumentációját](../event-hubs/index.yml).
1. A keresőmezőbe keressen **Event Hubs**. A visszaadott listában válassza a **Event Hubs** lehetőséget.
1. Válassza ki az Event hub-t.
1. Az Event hub létrehozásakor egy Event hub-névteret hoz létre. Ha még nem hozott létre egy Event hubot a névtéren belül, a menüben az **entitások** alatt hozzon létre egy Event hubot.

    [![Az Event hubok listája](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Az Event hub létrehozása után válassza ki azt az Event hubok listájában.
1. A menü **entitások** területén válassza a **Event Hubs** lehetőséget.
1. Válassza ki az Event hub nevét a konfiguráláshoz.
1. Az **Áttekintés** területen válassza a **fogyasztói csoportok** lehetőséget, majd válassza a **fogyasztói csoport** elemet.

    [![Fogyasztói csoport létrehozása](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Győződjön meg arról, hogy olyan fogyasztói csoportot hoz létre, amelyet kizárólag a Azure Time Series Insights-eseményforrás használ.

    > [!IMPORTANT]
    > Győződjön meg arról, hogy a fogyasztói csoportot nem használja más szolgáltatás, például Azure Stream Analytics vagy más Azure Time Series Insights-környezet. Ha a fogyasztói csoportot más szolgáltatások használják, az olvasási műveletek negatív hatással vannak erre a környezetre és más szolgáltatásokra. Ha a **$Defaultt** használja fogyasztói csoportként, a többi olvasó esetleg újra felhasználhatja a fogyasztói csoportot.

1. A menü **Beállítások** területén válassza a **megosztott elérési házirendek** elemet, majd kattintson a **Hozzáadás** gombra.

    [![Válassza a megosztott hozzáférési házirendek lehetőséget, majd kattintson a Hozzáadás gombra.](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. Az **új megosztott elérési házirend hozzáadása** panelen hozzon létre egy **MySendPolicy** nevű közös hozzáférést. Ezt a közös hozzáférési szabályzatot használja a jelen cikk későbbi, a C#-példákban szereplő események küldéséhez.

    [![A házirend neve mezőbe írja be a MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. A **jogcím** területen jelölje be a **Küldés** jelölőnégyzetet.

## <a name="add-an-azure-time-series-insights-instance"></a>Azure Time Series Insights-példány hozzáadása

Azure Time Series Insights Gen2 az idősorozat-modell (TSM) használatával a bejövő telemetria is hozzáadhat kontextusbeli adataikat. A TSM a címkéket vagy jeleket *példányoknak nevezzük,* és a kontextusban tárolt adatait is tárolhatja a *példány mezőiben.* Az adatai egy **Idősorozat-azonosító** használatával csatlakoznak a lekérdezési időponthoz. A jelen cikk későbbi részében használt minta szélmalmok projekt **idősorozat-azonosítója** `id` . Ha többet szeretne megtudni az adattárolásról a példány mezőiben, olvassa el az [Idősorozat-modell](./concepts-model-overview.md) áttekintését.

### <a name="create-an-azure-time-series-insights-event-source"></a>Azure Time Series Insights eseményforrás létrehozása

1. Ha még nem hozott létre egy eseményforrás, hajtsa végre az [eseményforrás létrehozásához](./how-to-ingest-data-event-hub.md)szükséges lépéseket.

1. Állítsa be a értékét `timeSeriesId` . Ha többet szeretne megtudni az **idősorozat-azonosítóról**, olvassa el a [Time Series-modelleket](./concepts-model-overview.md).

### <a name="push-events-to-windmills-sample"></a>Leküldéses események a szélmalmok mintába

1. A keresési sávban keressen **Event Hubs**. A visszaadott listában válassza a **Event Hubs** lehetőséget.

1. Válassza ki az Event hub-példányt.

1. Lépjen a **közös hozzáférésű házirendek**  >  **MySendPolicy**. Másolja az értéket a következőhöz: **kapcsolatok karakterlánca – elsődleges kulcs**.

    [![Az elsődleges kulcshoz tartozó kapcsolási karakterlánc értékének másolása](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Nyissa meg a következőt: <https://tsiclientsample.azurewebsites.net/windFarmGen.html>. Az URL-cím szimulált szélmalom-eszközöket hoz létre és futtat.
1. A weblap **esemény hub kapcsolati sztring** mezőjébe illessze be a [szélmalom beviteli mezőjébe](#push-events-to-windmills-sample)másolt kapcsolati karakterláncot.

    [![Illessze be az elsődleges kulcs kapcsolati karakterláncát az Event hub kapcsolati sztring mezőjébe](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Válassza **a Start gombra**.

    > [!TIP]
    > A szélmalom-szimulátor emellett olyan JSON-t is létrehoz, amelyet hasznos adattartalomként használhat a [Azure Time Series INSIGHTS GA lekérdezési API](/rest/api/time-series-insights/gen1-query)-kkal.

    > [!NOTE]
    > A szimulátor továbbra is küldi az adatküldést, amíg be nem zárul a böngésző lapja.

1. Térjen vissza az Event hubhoz a Azure Portal. Az **Áttekintés** oldalon az Event hub által fogadott új események jelennek meg.

    [![Az Event hub áttekintő lapja, amely az Event hub metrikáit jeleníti meg](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Támogatott JSON-alakzatok

### <a name="example-one"></a>Példa egy

* **Bemenet**: egy egyszerű JSON-objektum.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Kimenet**: egy esemény.

    |id|időbélyeg|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Példa kettőre

* **Bemenet**: egy JSON-tömb két JSON-objektummal. Minden JSON-objektum egy eseményre lesz konvertálva.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Kimenet**: két esemény.

    |id|időbélyeg|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Harmadik példa

* **Bemenet**: egy beágyazott JSON-tömböt tartalmazó JSON-objektum, amely két JSON-objektumot tartalmaz.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Kimenet**: két esemény. A rendszer átmásolja a tulajdonság **helyét** az egyes eseményekre.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Négy példa

* **Bemenet**: egy beágyazott JSON-tömböt tartalmazó JSON-objektum, amely két JSON-objektumot tartalmaz. Ez a bemenet azt mutatja be, hogy a globális tulajdonságok a komplex JSON-objektum által is szerepelhetnek.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Kimenet**: két esemény.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|nyomás|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Következő lépések

* [Tekintse meg a környezetet](https://insights.timeseries.azure.com) a Azure Time Series Insights Explorerben.

* További információ a [IoT hub eszköz üzeneteiről](../iot-hub/iot-hub-devguide-messages-construct.md)