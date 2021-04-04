---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001170"
---
A `ApplicationInsights` beállítás lehetővé teszi, hogy hozzáadja az [Azure Application Insights](/azure/application-insights) telemetria támogatását a tárolóhoz. Application Insights a tároló részletes figyelését teszi lehetővé. Egyszerűen figyelheti a tárolót a rendelkezésre állás, a teljesítmény és a használat érdekében. A tárolóban lévő hibák felismerése és diagnosztizálása is gyorsan elvégezhető.

A következő táblázat a szakasz által támogatott konfigurációs beállításokat ismerteti `ApplicationInsights` .

|Kötelező| Name | Adattípus | Leírás |
|--|------|-----------|-------------|
|Nem| `InstrumentationKey` | Sztring | Annak a Application Insights-példánynak a rendszerállapot-kulcsa, amelybe a rendszer a tárolóhoz tartozó telemetria-adatmennyiséget elküldi. További információ: [Application Insights ASP.net Corehoz](../articles/azure-monitor/app/asp-net-core.md). <br><br>Példa:<br>`InstrumentationKey=123456789`|