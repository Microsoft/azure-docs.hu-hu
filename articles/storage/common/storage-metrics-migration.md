---
title: Áthelyezés Storage Analytics metrikák Azure Monitor metrikák számára | Microsoft Docs
description: Megtudhatja, hogyan válthat a Storage Analytics metrikák (klasszikus metrikák) és a Azure Monitor metrikái között.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: a1f977cef614a52853407c0d0665399f1a249c53
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422062"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Váltás a mérőszámokra Azure Monitor

Az Azure Storage mostantól integrálja a metrikákat a Azure Monitor platformba. Ez a cikk segít az áttérésben.

## <a name="steps-to-complete-the-transition"></a>Az áttérés befejezésének lépései

A Azure Monitor mérőszámokra való áttéréshez a következő módszert ajánljuk.

1. Ismerkedjen meg a klasszikus metrikák és a Azure Monitor a metrikák [főbb különbségei](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) között. 

2. Lefordíthatja a jelenleg használt klasszikus metrikák listáját.

3. Határozza meg, hogy a [Azure monitor mely mérőszámai](#metrics-mapping-between-old-metrics-and-new-metrics) rendelkeznek ugyanazokkal az adatokkal, mint a jelenleg használt mérőszámok. 
   
4. [Diagramokat](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) és [irányítópultokat](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) hozhat létre a metrikai adatok megtekintéséhez.

   > [!NOTE]
   > A Azure Monitor metrikái alapértelmezés szerint engedélyezve vannak, ezért a metrikák rögzítésének megkezdéséhez semmit nem kell tennie. A metrikák megtekintéséhez azonban diagramokat vagy irányítópultokat kell létrehoznia. 
 
5. Ha olyan riasztási szabályokat hozott létre, amelyek klasszikus tárolási metrikán alapulnak, akkor hozzon létre Azure Monitor mérőszámokon alapuló [riasztási szabályokat](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) . 

6. Miután megtekintheti Azure Monitor összes mérőszámát, kikapcsolhatja a klasszikus naplózást. 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>Klasszikus metrikák és metrikák a Azure Monitor

Ez a szakasz néhány fontos különbséget ismertet a két metrikai platform között.

A fő különbség a metrikák kezelése. A klasszikus metrikákat az Azure Storage felügyeli, míg a Azure Monitor metrikáit Azure Monitor kezeli. A klasszikus metrikák esetében az Azure Storage metrikus értékeket gyűjt, összesíti őket, majd a Storage-fiókban található táblázatokban tárolja őket. Azure Monitor metrikáinak használatával az Azure Storage metrikai adatokat küld a Azure Monitor háttérbe. Azure Monitor egységes figyelési élményt nyújt, amely a Azure Portalból és a betöltött adatokból származó adatokkal is rendelkezik. 

A metrikák támogatása érdekében a klasszikus metrikák csak az Azure Blob Storage-hoz biztosítanak **Kapacitási** metrikákat. A Azure Monitor metrikái a blob, a Table, a file, a üzenetsor és a Premium Storage kapacitási mérőszámait biztosítják. A klasszikus metrikák **tranzakciós** mérőszámokat biztosítanak a blob-, tábla-, Azure-fájl-és üzenetsor-tároláshoz. A Azure Monitor a prémium szintű Storage-t adja hozzá a listához.

Ha a fiókban lévő tevékenység nem indít el mérőszámot, a klasszikus metrikák a nulla (0) értéket jelenítik meg az adott metrika esetében. A Azure Monitor metrikái teljes mértékben kihagyják az adatokat, ami tisztább jelentésekhez vezet. Például klasszikus metrikákkal, ha nem jelentettek időtúllépési hibát a rendszer, akkor a `ServerTimeoutError` metrikák tábla értéke 0. A Azure Monitor nem ad vissza adatokat, ha a mérőszám értékét a `Transactions` dimenzióval megegyező értékkel kérdezi le `ResponseType` `ServerTimeoutError` . 

Ha többet szeretne megtudni a Azure Monitor metrikákkal kapcsolatban, tekintse meg a [Azure monitor mérőszámait](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Klasszikus metrikák leképezése a metrikák Azure Monitor

 Ezekkel a táblázatokkal azonosíthatja, hogy a Azure Monitor mely mérőszámok rendelkeznek ugyanazokkal az adatokkal, mint a jelenleg használt mérőszámok. 

**Kapacitásmetrikák**

| Klasszikus metrika | Metrika Azure Monitor |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity`a dimenzióval `BlobType` egyenlő `BlockBlob` vagy`PageBlob` |
| `ObjectCount`        | `BlobCount`a dimenzióval `BlobType` egyenlő `BlockBlob` vagy`PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> Több új kapacitási mérőszám is létezik, amelyek klasszikus metrikák nem voltak elérhetők. A teljes lista megtekintéséhez lásd: [mérőszámok](../common/monitor-storage-reference.md#metrics).

**Tranzakciómetrikák**

| Klasszikus metrika | Metrika Azure Monitor |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `AuthorizationError` `Authentication``Anonymous` |
| `AnonymousClientOtherError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `ClientOtherError` `Authentication``Anonymous` |
| `AnonymousClientTimeoutError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `ClientTimeoutError` `Authentication``Anonymous` |
| `AnonymousNetworkError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `NetworkError` `Authentication``Anonymous` |
| `AnonymousServerOtherError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `ServerOtherError` `Authentication``Anonymous` |
| `AnonymousServerTimeoutError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `ServerTimeoutError` `Authentication``Anonymous` |
| `AnonymousSuccess` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `Success` `Authentication``Anonymous` |
| `AnonymousThrottlingError` | A dimenzióval `ResponseType` megegyező `ClientThrottlingError` vagy azzal `ServerBusyError` egyenlő dimenzióval rendelkező tranzakciók `Authentication``Anonymous` |
| `AuthorizationError` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``ClientOtherError` |
| `ClientTimeoutError` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``ClientTimeoutError` |
| `NetworkError` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``NetworkError` |
| `PercentAuthorizationError` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``AuthorizationError` |
| `PercentClientOtherError` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``ClientOtherError` |
| `PercentNetworkError` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``NetworkError` |
| `PercentServerOtherError` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``ServerOtherError` |
| `PercentSuccess` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``Success` |
| `PercentThrottlingError` | A dimenzióval `ResponseType` egyenlő `ClientThrottlingError` vagy`ServerBusyError` |
| `PercentTimeoutError` | A dimenzióval `ResponseType` egyenlő `ServerTimeoutError` vagy `ResponseType` azzal egyenlő értékű tranzakciók`ClientTimeoutError` |
| `SASAuthorizationError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `AuthorizationError` `Authentication``SAS` |
| `SASClientOtherError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `ClientOtherError` `Authentication``SAS` |
| `SASClientTimeoutError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `ClientTimeoutError` `Authentication``SAS` |
| `SASNetworkError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `NetworkError` `Authentication``SAS` |
| `SASServerOtherError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `ServerOtherError` `Authentication``SAS` |
| `SASServerTimeoutError` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `ServerTimeoutError` `Authentication``SAS` |
| `SASSuccess` | A dimenzióval egyenlő és a dimenzióval egyenlő értékű tranzakciók `ResponseType` `Success` `Authentication``SAS` |
| `SASThrottlingError` | A dimenzióval `ResponseType` megegyező `ClientThrottlingError` vagy azzal `ServerBusyError` egyenlő dimenzióval rendelkező tranzakciók `Authentication``SAS` |
| `ServerOtherError` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``ServerOtherError` |
| `ServerTimeoutError` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``ServerTimeoutError` |
| `Success` | Egyenlő dimenzióval rendelkező tranzakciók `ResponseType``Success` |
| `ThrottlingError` | `Transactions`a dimenzióval `ResponseType` egyenlő `ClientThrottlingError` vagy`ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>Következő lépések

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Tárolási metrikák a Azure Monitorban](./storage-metrics-in-azure-monitor.md)
