---
title: Azure Application Insights adatmodell – nyomkövetési telemetria
description: Application Insights adatmodell nyomkövetési telemetria
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8fea4bbf590816b2ef168a2ed16f197389ee282e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320544"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Nyomkövetési telemetria: Application Insights adatmodell

A nyomkövetési telemetria ( [Application Insights](./app-insights-overview.md)) a `printf` szöveg által keresett stílusú nyomkövetési utasításokat jelöli. `Log4Net`, `NLog` és más szöveg-alapú naplófájl-bejegyzések lefordítása ilyen típusú példányokra történik. A nyomkövetés nem rendelkezik bővíthetőségi mértékkel.

## <a name="message"></a>Üzenet

Nyomkövetési üzenet.

Maximális hossz: 32768 karakter

## <a name="severity-level"></a>Súlyossági szint

Nyomkövetés súlyossági szintje Az érték lehet:,,, `Verbose` `Information` `Warning` `Error` , `Critical` .

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Következő lépések

- [Ismerkedjen meg a .net-nyomkövetési naplók Application Insights](./asp-net-trace-logs.md).
- [Ismerkedjen meg a Java-nyomkövetési naplók Application Insights](./java-trace-logs.md).
- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- [Egyéni nyomkövetési telemetria írása](./api-custom-events-metrics.md#tracktrace)
- Tekintse meg Application Insights által támogatott [platformokat](./platforms.md) .

