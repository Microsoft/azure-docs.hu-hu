---
title: Az Azure Application Insights Telemetria adatmodell – függőségi Telemetria |} A Microsoft Docs
description: Application Insights-adatmodell – függőségi telemetria
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 3e3d6b8fdc9ac8dd28f73fecd6231e97a5645407
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60901025"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Függőségi telemetria: Application Insights-adatmodell

Függőségi Telemetria (a [Application Insights](../../azure-monitor/app/app-insights-overview.md)) közben a figyelt összetevő egy távoli összetevővel, például az SQL és a egy HTTP-végpontot jelenti.

## <a name="name"></a>Name (Név)

A parancsot kezdeményezett a függőségi hívás a neve. Alacsony cardinality értéke. Például a tárolt eljárás nevét és URL-cím elérési út sablonjának.

## <a name="id"></a>azonosító

A függőségi hívás példány azonosítója. Korrelációs használt a kérelemben szereplő telemetriai elem a függőségi hívás megfelelő. További információkért lásd: [korrelációs](../../azure-monitor/app/correlation.md) lapot.

## <a name="data"></a>Adatok

A függőségi hívás által kezdeményezett parancsot. Példák: SQL-utasítás és a HTTP URL-cím és az összes lekérdezési paramétert.

## <a name="type"></a>Típus

Függőség neve. A függőségek logikai jellegű csoportosítását és más mezőket is, például a commandName és resultCode értelmezése alacsony cardinality értéke. Példák az SQL Azure-tábla és a HTTP.

## <a name="target"></a>Target

Függőségi hívás célhelyre. Például a kiszolgáló nevét, az állomás címe. További információkért lásd: [korrelációs](../../azure-monitor/app/correlation.md) lapot.

## <a name="duration"></a>Időtartam

Kérelem időtartama a formátumban: `DD.HH:MM:SS.MMMMMM`. Lehet kisebb, mint `1000` nap.

## <a name="result-code"></a>Eredménykód

Függőségi hívás eredménykódja. Példák a következők: SQL-hibakód és HTTP-állapotkódot.

## <a name="success"></a>Siker

A sikeres vagy sikertelen hívás megjelölése.

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>További lépések

- Állítsa be a nyomon követése az függőségi [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Állítsa be a nyomon követése az függőségi [Java](../../azure-monitor/app/java-agent.md).
- [A függőségi egyéni telemetriát írhat](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Lásd: [adatmodell](data-model.md) Application Insights és modellhez.
- Tekintse meg [platformok](../../azure-monitor/app/platforms.md) Application Insights által támogatott.
