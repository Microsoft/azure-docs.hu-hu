---
title: A ASP.NET Core SignalR méretezése az Azure SignalR-rel
description: Az ASP.NET Core SignalR-alkalmazások az Azure SignalR szolgáltatással történő skálázásának áttekintése.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 8a4012d204b6dafa1233e4ce3d878590120be47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60640225"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>ASP.NET Core SignalR-alkalmazások skálázása az Azure SignalR szolgáltatással

## <a name="developing-signalr-apps"></a>SignalR-alkalmazások fejlesztése

Jelenleg a SignalR [két verziója](https://docs.microsoft.com/aspnet/core/signalr/version-differences) használható a webes alkalmazásokkal: a SignalR for ASP.NET és ASP.NET Core SignalR, amely a legújabb verzió. Az Azure SignalR szolgáltatás az ASP.NET Core SignalR-re épülő, az Azure által kezelt szolgáltatás.

Az ASP.NET Core SignalR az előző verzió átdolgozása. Ezért az ASP.NET Core SignalR visszamenőlegesen nem kompatibilis a SignalR korábbi verzióival. Az API-k és a szolgáltatás működése eltérő. Az ASP.NET Core SignalR SDK a .NET Standard verziójához készült, ezért továbbra is használható a .NET-keretrendszerrel. Azonban az új API-kat kell használnia a régiek helyett. Ha a SignalRt használja és szeretne ASP.NET Core SignalRre vagy Azure SignalR szolgáltatásra váltani, úgy kell módosítania a kódot, hogy kezelje az API-k közötti különbségeket.

Az Azure SignalR szolgáltatás használata esetén az ASP.NET Core SignalR kiszolgálóoldali összetevőjének üzemeltetése az Azure-ban történik. Mivel azonban a technológia az ASP.NET Core-ra épül, lehetőség van arra, hogy a tényleges webalkalmazást több különféle platformon (Windows, Linux és MacOS) futtassa, és az üzemeltetést az [Azure App Service](../app-service/overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) vagy a [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index) szolgáltatással végezze. Saját folyamatok esetében saját üzemeltetést is használhat.

Ha azt szeretné, hogy az alkalmazás támogassa a legújabb funkciókat a webes ügyfelek valós idejű tartalmakkal való frissítéséhez, futtatható legyen a több platformon (Azure, Windows, Linux és macOS), és üzemeltethető legyen különféle környezetekben, az Azure SignalR szolgáltatás az ideális választás.

## <a name="why-not-deploy-signalr-myself"></a>Miért ne telepítsem saját kezűleg a SignalRt?

Továbbra is lehetséges üzembe helyezni egy ASP.NET Core SignalRt támogató Azure-webalkalmazást háttérösszetevőként a teljes alkalmazáshoz.

Az Azure SignalR egyik legnagyobb előnye az egyszerű használata. Az Azure SignalR szolgáltatás révén nem kell a teljesítménnyel, a méretezéssel és a rendelkezésre állással kapcsolatos problémákkal foglalkoznia. Ezeket a problémákat a rendszer kezeli Ön helyett a 99,9%-os szolgáltatásiszint-szerződésben foglaltak szerint.

A valós idejű tartalomfrissítésekhez az előnyben részesített technológia általában a WebSocket. Azonban a nagy számú állandó WebSocket-kapcsolatok terheléselosztása egyre nagyobb problémát jelent, ahogy a rendszer mérete nő. Gyakori megoldás a DNS-terheléselosztás, a hardveres terheléselosztás és a szoftveres terheléselosztás. Az Azure SignalR szolgáltatás megoldja ezt a problémát Ön helyett.

A másik ok, hogy nem kell ténylegesen üzemeltetnie egy webalkalmazást. Előfordulhat, hogy a webalkalmazás logikája [kiszolgáló nélküli számítást](https://azure.microsoft.com/overview/serverless-computing/) használ. Például előfordulhat, hogy a kód csak igény szerint lesz üzemeltetve és végrehajtva az [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) eseményindítói segítségével. Ez a forgatókönyv bonyolult lehet, mivel a kód csak igény szerint fut és nem tart fenn hosszú kapcsolatot az ügyfelekkel. Az Azure SignalR szolgáltatás képes kezelni ezt a helyzetet, mivel a szolgáltatás már kezeli a kapcsolatokat. További részletekért tekintse meg a [SignalR és az Azure Functions együttes használatának az áttekintését](signalr-concept-azure-functions.md).

## <a name="how-does-it-scale"></a>Hogyan skálázható?

Gyakori, hogy a SignalR az SQL Server, az Azure Service Bus vagy az Azure Cache for Redis. Az Azure SignalR szolgáltatás kezeli a skálázási módszert Ön helyett. A teljesítmény és a költségek a módszertől függően változnak, és Önnek nem kell ezekkel a további szolgáltatásokkal foglalkoznia. Mindössze frissítenie kell a szolgáltatás egységeinek számát. Minden egység legfeljebb 1000 ügyfélkapcsolatot támogat.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Csevegőszoba létrehozása az Azure SignalRrel](signalr-quickstart-dotnet-core.md)