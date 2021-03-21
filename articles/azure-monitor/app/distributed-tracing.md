---
title: Elosztott nyomkövetés az Azure Application Insightsban | Microsoft Docs
description: Információt nyújt a Microsoft által az elosztott nyomkövetés támogatásáról a OpenCensus-projektben partneri kapcsolaton keresztül
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a47b41a8b7f4e18be58c32c97cf279b9229f26da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579514"
---
# <a name="what-is-distributed-tracing"></a>Mi az elosztott nyomkövetés?

A modern felhő-és a [Service](https://azure.com/microservices) -architektúrák megjelenése olyan egyszerű, egymástól függetlenül telepíthető szolgáltatásokat eredményezett, amelyek segíthetnek csökkenteni a költségeket a rendelkezésre állás és az átviteli sebesség növelése mellett. De bár ezek a mozgások az egyes szolgáltatásokat könnyebben megértették, a teljes rendszerek megnehezítik a rendszert és a hibakeresést.

A monolitikus architektúrákban a hívási veremekkel való hibakereséshez kaptunk felhasználva. A hívási verem olyan briliáns eszközök, amelyekkel megjeleníthető a végrehajtás folyamata (metódus A "B" metódus, amely a C módszernek nevezett), valamint az egyes hívásokra vonatkozó részletek és paraméterek. Ez nagyszerűen használható a monolitok vagy a szolgáltatások esetében, amelyek egyetlen folyamaton futnak, de hogyan lehet hibakeresést végezni, ha a hívás egy folyamat határán kívül esik, nem csupán a helyi veremre mutató hivatkozást? 

Ez az a hely, ahol az elosztott nyomkövetés bekerül.  

Az elosztott nyomkövetés a modern felhő-és a szolgáltatás-architektúrák hívási helyeinek megfelelője, és a rendszer leegyszerűsíti a teljesítményt. Azure Monitor két, az elosztott nyomkövetési adat felhasználására szolgáló élményt biztosítunk. Az első a [tranzakciós diagnosztika](./transaction-diagnostics.md) nézete, amely olyan, mint a hívási verem, amelyhez egy idődimenzió van hozzáadva. A tranzakciós diagnosztika nézet egyetlen tranzakció/kérelem megjelenítését teszi lehetővé, és hasznos lehet a megbízhatósági problémák kiváltó okának és a teljesítmény szűk keresztmetszetének megkeresésére a kérelmek alapján.

A Azure Monitor egy [alkalmazás-hozzárendelési](./app-map.md) nézetet is kínál, amely számos tranzakciót összesít, hogy a rendszer hogyan befolyásolja a rendszerek működését, és hogy az átlagos teljesítmény és a hibák milyen arányban jelenjenek meg. 

## <a name="how-to-enable-distributed-tracing"></a>Az elosztott nyomkövetés engedélyezése

Az elosztott nyomkövetés engedélyezése egy alkalmazás szolgáltatásaiban olyan egyszerű, mintha a megfelelő ügynököt, SDK-t vagy könyvtárat hozzáadja az egyes szolgáltatásokhoz a szolgáltatás implementált nyelve alapján.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Engedélyezés Application Insights keresztül automatikus rendszerállapot-vagy SDK-k használatával

A .NET, .NET Core, Java, Node.js és JavaScript Application Insights ügynökök és/vagy SDK-k mind támogatják az elosztott nyomkövetést natív módon. Az egyes Application Insights SDK-hoz történő telepítésére és konfigurálására vonatkozó utasítások alább érhetők el:

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

A megfelelő Application Insights SDK telepítésével és konfigurálásával az SDK-függőségi automatikus gyűjtők automatikusan gyűjtik a nyomkövetési információkat a népszerű keretrendszerek, könyvtárak és technológiák számára. A támogatott technológiák teljes listája [a függőségi automatikus gyűjtemény dokumentációjában](./auto-collect-dependencies.md)érhető el.

 Emellett a technológia manuálisan is nyomon követhető a [TelemetryClient](./api-custom-events-metrics.md) [TrackDependency](./api-custom-events-metrics.md) hívásával.

## <a name="enable-via-opencensus"></a>Engedélyezés a OpenCensus-n keresztül

A Application Insights SDK-k mellett Application Insights is támogatja az elosztott nyomkövetést a [OpenCensus](https://opencensus.io/)-en keresztül. A OpenCensus egy nyílt forráskódú, szállítói-agnosztikus, a könyvtárak egyetlen eloszlása, amely metrikák gyűjtését és elosztott nyomkövetését biztosítja a szolgáltatásokhoz. Emellett lehetővé teszi a nyílt forráskódú közösség számára, hogy lehetővé tegye az elosztott nyomkövetést olyan népszerű technológiákkal, mint a Redis, a Memcached vagy a MongoDB. [A Microsoft számos más monitorozási és felhőalapú partnerrel együttműködve együttműködik a OpenCensus](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

A OpenCensus webhely a [Python](https://opencensus.io/api/python/trace/usage.html) és a [Go](https://godoc.org/go.opencensus.io)API-referenciájának dokumentációját, valamint a OpenCensus használatának különböző útmutatóit tartalmazza. 

## <a name="next-steps"></a>Következő lépések

* [OpenCensus Python használati útmutató](https://opencensus.io/api/python/trace/usage.html)
* [Alkalmazás-hozzárendelés](./app-map.md)
* [Végpontok közötti teljesítmény figyelése](../app/tutorial-performance.md)

