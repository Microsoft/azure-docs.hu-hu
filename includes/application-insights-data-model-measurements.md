---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67178877"
---
Egyéni mérések gyűjteménye. Ezt a gyűjteményt a telemetria-elemmel társított mérések jelentésére használhatja. A tipikus használati esetek a következők:
- a függőségi telemetria-tartalom mérete
- a kérelem telemetria feldolgozott üzenetsor-elemek száma
- az ügyfél által a varázsló lépésének befejezéséhez szükséges idő telemetria.

Az alkalmazás-elemzési szolgáltatásban [Egyéni méréseket](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) lehet lekérdezni:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Az egyéni mérések ahhoz a telemetria-elemmel vannak társítva, amelyhez tartoznak. Ezeket a mértékeket tartalmazó telemetria-elemmel mintavételezéssel kell elvégezniük. Más telemetria eltérő értékkel rendelkező mérések nyomon követéséhez használja a [metrikus telemetria](../articles/azure-monitor/app/api-custom-events-metrics.md).

Maximális kulcs hossza: 150
