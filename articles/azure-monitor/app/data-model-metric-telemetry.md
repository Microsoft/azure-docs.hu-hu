---
title: Adatmodell metrikus telemetria – Azure Application Insights
description: Application Insights adatmodell metrikus telemetria
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 3e4a1fc3de58b8e65ab9c7a288bdf3eb37e7bae0
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671970"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Metrikus telemetria: Application Insights adatmodell

A [Application Insights](../../azure-monitor/app/app-insights-overview.md)kétféle metrikai telemetria támogat: egyetlen mérési és előre aggregált metrika. Az egyszeres mérés csak a név és az érték. Az előre aggregált metrika a metrika minimális és maximális értékét adja meg az összesítési intervallumban és a szórásban.

Az előre összesített metrikai telemetria feltételezi, hogy az összesítési időszak egy perc volt.

A Application Insights számos jól ismert metrikai nevet támogat. Ezek a metrikák a performanceCounters táblázatba kerülnek.

A rendszerszintű és a folyamat számlálóit jelölő metrika:

| **.NET-név**             | **Platform agnosztikus neve** | **REST API neve** | **Leírás**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Munka folyamatban... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | számítógép teljes PROCESSZORa
| `\Memory\Available Bytes`                 | Munka folyamatban... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | A számítógépen futó folyamatok számára elérhető fizikai memória mennyiségét jeleníti meg bájtban. A kiszámításához a rendszer a nulla, az ingyenes és a készenléti memória listán lévő tárterület összegét összesíti. A szabad memória készen áll a használatra. a letiltott memória a nullával kitöltött memóriából áll, hogy megakadályozza a későbbi folyamatok számára az előző folyamat által használt adatok megtekintését; a készenléti memória olyan memória, amely el lett távolítva egy folyamat munkakészletéről (fizikai memóriája) a lemezre, de továbbra is visszahívható. Lásd: [memória objektum](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Munka folyamatban... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | Az alkalmazást működtető folyamat PROCESSZORa
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Munka folyamatban... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | az alkalmazást működtető folyamat által használt memória
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Munka folyamatban... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | az alkalmazás futtatásának folyamata által futtatott I/O-műveletek sebessége
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Munka folyamatban... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | az alkalmazás által feldolgozott kérelmek aránya 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Munka folyamatban... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | az alkalmazás által eldobott kivételek aránya
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Munka folyamatban... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | kérelmek átlagos végrehajtási ideje
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Munka folyamatban... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | a várólistán való feldolgozásra váró kérelmek száma

## <a name="name"></a>Name (Név)

A Application Insights Portálon és felhasználói felületen megjelenő metrika neve. 

## <a name="value"></a>Érték

Egy érték a méréshez. Az összesítéshez tartozó egyedi mérések összege.

## <a name="count"></a>Darabszám

Az összesített metrika metrikájának súlya Nem szabad beállítani a mérést.

## <a name="min"></a>Min

Az összesített metrika minimális értéke. Nem szabad beállítani a mérést.

## <a name="max"></a>Max

Az összesített metrika maximális értéke. Nem szabad beállítani a mérést.

## <a name="standard-deviation"></a>Szórás

Az összesített metrika szórása. Nem szabad beállítani a mérést.

## <a name="custom-properties"></a>Egyéni tulajdonságok

A metrika az egyéni tulajdonsággal `CustomPerfCounter` beállítása `true` azt jelzi, hogy a metrika a Windows teljesítményszámláló értékét jelöli. Ezek a metrikák a performanceCounters táblában vannak elhelyezve. Nem a customMetrics. A metrika neve a kategória, a számláló és a példányok neveinek kinyeréséhez is értelmezve lesz.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan használhatja [a Application INSIGHTS API-t egyéni eseményekhez és mérőszámokhoz](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- Tekintse meg Application Insights által támogatott [platformokat](../../azure-monitor/app/platforms.md) .
