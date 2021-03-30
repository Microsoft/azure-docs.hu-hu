---
title: Alkalmazások figyelése az Azure Kubernetes szolgáltatásban (ak) Application Insights-Azure Monitorsal | Microsoft Docs
description: A Azure Monitor zökkenőmentesen integrálható az Kubernetes-on futó alkalmazással, és lehetővé teszi, hogy az alkalmazásaival kapcsolatos problémák ne legyenek idő alatt.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87075308"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Zero Instrumentation-alkalmazás figyelése a Kubernetes-Azure Monitor Application Insights

> [!IMPORTANT]
>  A Kubernetes-on futó Java-alkalmazások figyelését jelenleg a kód kihelyezése nélkül engedélyezheti – használja a [Java önálló ügynököt](./java-in-process-agent.md). Habár az alkalmazások figyelésének zökkenőmentes engedélyezése a megoldás más nyelveken is működik, az SDK-k segítségével figyelheti az AK-on futó alkalmazásokat: [ASP.net Core](./asp-net-core.md), [ASP.net](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md)és [Python](./opencensus-python.md).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Alkalmazások figyelése a kód beszerelése nélkül
Jelenleg csak a Java lehetővé teszi, hogy az alkalmazás figyelését a kód kivezetése nélkül engedélyezze. Más nyelveken futó alkalmazások figyelése az SDK-k használatával. 

## <a name="java"></a>Java
Ha engedélyezve van, a Java-ügynök automatikusan gyűjt számos kérést, függőséget, naplót és metrikát a legszélesebb körben használt könyvtárakból és keretrendszerekből.

A Kubernetes-alkalmazásokban, valamint más környezetekben futó Java-alkalmazások figyeléséhez kövesse [a részletes útmutatást](./java-in-process-agent.md) . 

## <a name="other-languages"></a>További nyelvek

Az SDK-k használata esetén jelenleg a következő nyelveken javasolt az alkalmazások használata:
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure monitor](../overview.md) és a [Application Insights](./app-insights-overview.md)
* Tekintse át az [elosztott nyomkövetést](./distributed-tracing.md) , és tekintse meg, milyen [alkalmazás-hozzárendelést](./app-map.md?tabs=net) végezhet a vállalata számára
