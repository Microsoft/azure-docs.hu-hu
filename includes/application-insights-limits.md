---
title: fájl belefoglalása
description: fájl belefoglalása
services: application-insights
author: lgayhardt
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: lagayhar
ms.custom: include file
ms.openlocfilehash: 1c4f6b876a4aa80c7e51f2bb3ca88234203d0daa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726179"
---
A mérőszámok és események másodpercenkénti száma korlátozott a rendszerállapot-kulcs alapján. A korlátozások a választott [díjszabási csomagtól](https://azure.microsoft.com/pricing/details/application-insights/) függően változnak.

| Erőforrás | Alapértelmezett korlát | Megjegyzés
| --- | --- | --- |
| Napi teljes adatmennyiség | 100 GB | Egy korlát beállításával csökkenthető az adatmennyiség. Ha több adatra van szüksége, növelheti a portálon megjelenő korlátot, amely akár 1 000 GB is lehet. A 1 000 GB-nál nagyobb kapacitások esetében küldjön e-mailt a címre AIDataCap@microsoft.com .
| Throttling | 32 000 esemény/másodperc | A korlát megállapítása egy percnyi mérés alapján történik.
| Adatmegőrzési naplók | [30-730 nap](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | Ez az erőforrás [naplókhoz](../articles/azure-monitor/logs/log-query-overview.md)használható.
| Adatmegőrzési metrikák | 90 nap| Ez az erőforrás a [Metrikaböngésző](../articles/azure-monitor/essentials/metrics-charts.md).
| [Többlépéses rendelkezésre állási teszt](../articles/azure-monitor/app/availability-multistep.md) a részletes eredmények megőrzésével | 90 nap | Ez az erőforrás minden lépésről részletes eredményeket biztosít.
| Telemetria maximális mérete | 64 kB |
| Telemetria elemek maximális száma kötegben | 64 K |
| Tulajdonság- és metrikanév hossza | 150 | Lásd: [sémák beírása](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Tulajdonságérték-sztring hossza | 8,192  | Lásd: [sémák beírása](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Nyomkövetési és kivételüzenet hossza | 32 768  | Lásd: [sémák beírása](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| [Rendelkezésre állási tesztek](../articles/azure-monitor/app/monitor-web-app-availability.md) száma alkalmazásonként | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) adatmegőrzés | 5 nap |
| [Profiler](../articles/azure-monitor/app/profiler.md) -adat elküldve naponta | 10 GB |

További információt [az Application Insights árait és kvótáit](../articles/azure-monitor/app/pricing.md) ismertető cikkben talál.