---
title: Az Azure Application Insights Agent áttekintése | Microsoft Docs
description: A Application Insights-ügynök áttekintése. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 69f80856150e461c6edfafdf0aa89de77c4ab0fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583804"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>A helyszíni kiszolgálók Azure Monitor Application Insights ügynökének üzembe helyezése

> [!IMPORTANT]
> Ez az útmutató a Application Insights-ügynök helyszíni és nem Azure-beli Felhőbeli üzembe helyezéséhez ajánlott. Az [Azure-beli virtuális gépek és a virtuálisgép-méretezési csoport üzembe helyezésének](./azure-vm-vmss-apps.md)ajánlott módszere.

Application Insights ügynök (korábbi nevén Állapotmonitor v2) a [PowerShell-Galéria](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)közzétett PowerShell-modul.
Lecseréli [Állapotmonitor](./monitor-performance-live-website-now.md).
A rendszer elküldje a telemetria a Azure Portalba, ahol [nyomon](./app-insights-overview.md) követheti az alkalmazást.

> [!NOTE]
> A modul jelenleg csak az IIS-ben üzemeltetett .NET-webalkalmazások Code uninstrumentation-alapú rendszerállapotát támogatja. SDK használata ASP.NET Core, Java és Node.js alkalmazásokhoz.

## <a name="powershell-gallery"></a>PowerShell-galéria

Application Insights ügynök itt található: https://www.powershellgallery.com/packages/Az.ApplicationMonitor .

![PowerShell-galéria](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Utasítások
- Tekintse meg az [első lépéseket ismertető útmutatót](status-monitor-v2-get-started.md) , amelyből megtudhatja, hogyan kezdheti el a rövid kód mintáit.
- Tekintse meg az első lépések [részletes leírását](status-monitor-v2-detailed-instructions.md) .

## <a name="powershell-api-reference"></a>PowerShell API-referenciák
- [ApplicationInsightsMonitoring letiltása](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [InstrumentationEngine letiltása](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [ApplicationInsightsMonitoring engedélyezése](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [InstrumentationEngine engedélyezése](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Start – ApplicationInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Hibaelhárítás
- [Hibaelhárítás](status-monitor-v2-troubleshoot.md)
- [Ismert problémák](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>GYIK

- Támogatja a Application Insights ügynök a proxy telepítéseit?

  *Igen*. Application Insights-ügynök letöltése több módon is lehetséges. Ha a számítógépnek van internet-hozzáférése, akkor paraméterek használatával bejelentkezhet a PowerShell-galériaba `-Proxy` .
Manuálisan is letöltheti a modult, és telepítheti a számítógépre, vagy közvetlenül is használhatja azt.
Ezeket a beállításokat a [részletes utasítások](status-monitor-v2-detailed-instructions.md)ismertetik.

- Támogatja az Állapotmonitor v2 ASP.NET Core alkalmazásokat?

  *Nem*. ASP.NET Core alkalmazások figyelésének engedélyezéséhez lásd: [Application Insights ASP.net Core alkalmazásokhoz](./asp-net-core.md). Nincs szükség a StatusMonitor telepítésére egy ASP.NET Core alkalmazáshoz. Ez akkor is igaz, ha ASP.NET Core alkalmazást az IIS üzemelteti.

- Hogyan ellenőrizze, hogy az engedélyezés sikeres volt-e?

  - A [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) parancsmag használatával ellenőrizheti, hogy az engedélyezés sikeres volt-e.
  - Javasoljuk, hogy [élő metrikák](./live-stream.md) használatával gyorsan megállapítsa, hogy az alkalmazás telemetria küld-e.

  - A [log Analytics](../logs/log-analytics-tutorial.md) használatával is listázhatja az összes jelenleg telemetria küldő felhőalapú szerepkört:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Következő lépések

A telemetriai adatok megtekintése:

* [Ismerje meg a mérőszámokat](../essentials/metrics-charts.md) a teljesítmény és a használat figyeléséhez.
* [Események és naplók keresése](./diagnostic-search.md) a problémák diagnosztizálásához.
* További speciális lekérdezésekhez [használja az elemzést](../logs/log-query-overview.md) .
* [Irányítópultok létrehozása](./overview-dashboard.md).

További telemetriai funkciók hozzáadása:

* [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely elérhető maradjon.
* [Vegyen fel webes ügyfél-telemetria](./javascript.md) a kivételek megjelenítéséhez a weboldali kódból és a nyomkövetési hívások engedélyezéséhez.
* [Adja hozzá a Application INSIGHTS SDK-t a kódhoz](./asp-net.md) , hogy nyomkövetési és naplózási hívásokat helyezzen el.