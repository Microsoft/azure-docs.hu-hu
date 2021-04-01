---
title: Betöltés áttekintése – Azure Time Series Insights Gen2 | Microsoft Docs
description: Ismerkedjen meg az adatfeldolgozással Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 2b76490ab23cb14755f6f90a27ec8b176bb30a11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780025"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Azure Time Series Insights Gen2 adatfeldolgozás áttekintése

A Azure Time Series Insights Gen2-környezet egy betöltési *motort* tartalmaz a streaming Time Series-adatok gyűjtéséhez, feldolgozásához és tárolásához. Amikor az adatforrás (ok) hoz érkezik, Azure Time Series Insights Gen2 közel valós időben fogja felhasználni és tárolni az adatait.

[![Az adatbetöltés áttekintése](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Betöltési témakörök

Az alábbi cikkek részletesen ismertetik az adatfeldolgozást, beleértve az alábbi ajánlott eljárásokat:

* Olvassa el [az eseményforrás és az](./concepts-streaming-ingestion-event-sources.md) eseményforrás időbélyegének kiválasztására vonatkozó útmutatást.

* A támogatott [adattípusok](./concepts-supported-data-types.md) áttekintése

* Megtudhatja, hogyan alkalmazza a betöltési motor a JSON-tulajdonságok [szabályait](./concepts-json-flattening-escaping-rules.md) a Storage-fiók oszlopainak létrehozásához.

* Tekintse át a környezet [átviteli sebességének korlátozásait](./concepts-streaming-ingress-throughput-limits.md) a méretezési igények megtervezéséhez.

## <a name="next-steps"></a>Következő lépések

* Folytassa tovább, ha többet szeretne megtudni az Azure Time Series Insights Gen2-környezethez tartozó [eseményforrás-forrásokról](./concepts-streaming-ingestion-event-sources.md) .
