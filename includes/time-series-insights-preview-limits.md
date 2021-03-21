---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 016ad0e11f3378dba887e0a235f235fa91e3aa03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98109464"
---
### <a name="property-limits"></a>Tulajdonságok korlátai

Azure Time Series Insights a tulajdonságok korlátai a meleg tárolás érdekében 1 000-ra növekedtek, és nem vonatkoznak a hűtőházi tárolásra vonatkozó tulajdonságértékek. A megadott esemény tulajdonságai a megfelelő JSON-, CSV-és diagram-oszlopokkal rendelkeznek, amelyeket megtekintheti a [Azure Time Series Insights Gen2 Explorerben](../articles/time-series-insights/quickstart-explore-tsi.md).

| Termékváltozat | Maximális tulajdonságok |
| --- | --- |
| Gen2 (L1) | 1 000 tulajdonságok (oszlopok) a meleg tároláshoz és korlátlan a hideg tároláshoz|
| Gen1 (S1) | 600 tulajdonságok (oszlopok) |
| Gen1 (S2) | 800 tulajdonságok (oszlopok) |

### <a name="streaming-ingestion"></a>Streamek feldolgozása

* Környezetekben legfeljebb két [eseményforrás](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) lehet.

* Az Event sources szolgáltatással kapcsolatos ajánlott eljárások és általános útmutatás [itt](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) található

* Alapértelmezés szerint a Azure Time Series Insights Gen2 **legfeljebb 1 megabájt/másodperc (Mbps)** sebességgel képes befogadni a bejövő adatot Azure Time Series Insights Gen2-környezetben. A [hub-partíciók esetében](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)további korlátozások vannak érvényben. A támogatási jegynek a Azure Portalon keresztüli elküldésével legfeljebb 2 MBps sebesség adható meg. További információért olvassa el a streaming betöltési [átviteli sebességének korlátozásait](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>API-korlátok

A [REST API](/rest/api/time-series-insights/preview#limits-1)dokumentációjában a Azure Time Series Insights Gen2 REST API korlátai vannak megadva.