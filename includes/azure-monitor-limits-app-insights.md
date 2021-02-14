---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: e224faca3c384ee5b3df02b015199ac5eab403bc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370102"
---
A mérőszámok és események másodpercenkénti száma korlátozott a rendszerállapot-kulcs alapján. A korlátozások a választott [díjszabási csomagtól](https://azure.microsoft.com/pricing/details/application-insights/) függően változnak.

| Erőforrás | Korlát | Megjegyzés
| --- | --- | --- |
| Napi teljes adatmennyiség | 100 GB | Egy korlát beállításával csökkenthető az adatmennyiség. Ha több adatra van szüksége, növelheti a portálon megjelenő korlátot, amely akár 1 000 GB is lehet. A 1 000 GB-nál nagyobb kapacitások esetében küldjön e-mailt a címre AIDataCap@microsoft.com .
| Throttling | 32 000 esemény/másodperc | A korlát megállapítása egy percnyi mérés alapján történik.
| Adatmegőrzés | [30-730 nap](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period) | Ez az erőforrás a [Keresés](../articles/azure-monitor/app/diagnostic-search.md), [Elemzés](../articles/azure-monitor/log-query/log-query-overview.md) és [Metrikaböngésző](../articles/azure-monitor/platform/metrics-charts.md) esetén használható.
| [Többlépéses rendelkezésre állási teszt](../articles/azure-monitor/app/availability-multistep.md) a részletes eredmények megőrzésével | 90 nap | Ez az erőforrás minden lépésről részletes eredményeket biztosít.
| Esemény maximális mérete | 64 000 bájt |
| Tulajdonság- és metrikanév hossza | 150 | Lásd: [sémák beírása](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Tulajdonságérték-sztring hossza | 8,192 | Lásd: [sémák beírása](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Nyomkövetési és kivételüzenet hossza | 32 768  | Lásd: [sémák beírása](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| [Rendelkezésre állási tesztek](../articles/azure-monitor/app/monitor-web-app-availability.md) száma alkalmazásonként | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) adatmegőrzés | 5 nap |
| [Profiler](../articles/azure-monitor/app/profiler.md) -adat elküldve naponta | 10 GB |

További információt [az Application Insights árait és kvótáit](../articles/azure-monitor/app/pricing.md) ismertető cikkben talál.
