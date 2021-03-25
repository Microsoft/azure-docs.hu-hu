---
title: Profil élő Azure App Service alkalmazások a Application Insightskal | Microsoft Docs
description: A Azure App Serviceon futó élő alkalmazások a Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a53db9deb07863010c792943c71eb0af5d845af8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026505"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profil élő Azure App Service alkalmazások Application Insights

A Profilert futtathatja a ASP.NET-on, és ASP.NET Core a Azure App Service-on futó alkalmazások alapszintű vagy magasabb szintű használatát. A Profiler Linux rendszeren való engedélyezése jelenleg csak [ezen a metóduson](profiler-aspnetcore-linux.md)keresztül lehetséges.

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> A Profiler engedélyezése az alkalmazáshoz
A Profiler engedélyezéséhez kövesse az alábbi utasításokat. Ha más típusú Azure-szolgáltatást futtat, akkor a Profiler más támogatott platformokon való engedélyezésével kapcsolatban itt talál útmutatást:
* [Felhőszolgáltatások](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Alkalmazások Service Fabric](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Virtual Machines](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler az App Services futtatókörnyezet részeként előre telepítve van. Az alábbi lépések bemutatják, hogyan engedélyezheti a App Service számára. Kövesse az alábbi lépéseket még akkor is, ha felépítésekor az alkalmazás betekintő SDK-t is feltöltötte az alkalmazásba.

> [!NOTE]
> Application Insights Profiler kód nélküli telepítése a .NET Core támogatási szabályzatot követi.
> További információ a támogatott futtatókörnyezetekről: [.net Core támogatási szabályzat](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

1. Navigáljon a App Servicehoz tartozó Azure-vezérlőpultra.
1. Az App Service "always on" beállításának engedélyezése. Ezt a beállítást a **Beállítások**, a **konfiguráció** lapon találja (lásd a következő lépés képernyőképét), majd válassza az **általános beállítások** lapot.
1. Navigáljon a **beállítások > Application Insights** lapra.

   ![Az alkalmazás-felismerés engedélyezése App Services portálon](./media/profiler/AppInsights-AppServices.png)

1. Kövesse a panel utasításait egy új erőforrás létrehozásához, vagy válasszon ki egy meglévő alkalmazás-keresési erőforrást az alkalmazás figyeléséhez. Győződjön meg arról is, hogy a Profiler **be van kapcsolva**. Ha a Application Insights erőforrás a App Servicetól eltérő előfizetésben található, nem használhatja ezt a lapot a Application Insights konfigurálásához. Ezt manuálisan is megteheti, ha manuálisan hozza létre a szükséges alkalmazás-beállításokat. [A következő szakasz a Profiler manuális engedélyezésére vonatkozó utasításokat tartalmazza.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Alkalmazás-áttekintési hely kiterjesztésének hozzáadása][Enablement UI]

1. A Profiler mostantól engedélyezve van egy App Services alkalmazás-beállítás használatával.

    ![A Profiler alkalmazásának beállítása][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>A Profiler manuális engedélyezése vagy Azure Resource Manager
Application Insights Profiler a Azure App Service alkalmazás-beállításainak létrehozásával engedélyezhető. A fent látható beállításokkal rendelkező oldal létrehozza ezeket az Alkalmazásbeállítások. Ezeket a beállításokat azonban a sablonok vagy más eszközök használatával automatizálhatja. Ezek a beállítások abban az esetben is működni fognak, ha a Application Insights erőforrás a Azure App Servicetól eltérő előfizetésben található.
A Profiler engedélyezéséhez a következő beállítások szükségesek:

|Alkalmazásbeállítás    | Érték    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | a Application Insights-erőforrás Rendszerállapotkulcsot    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


Ezeket az értékeket [Azure Resource Manager sablonok](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure POWERSHELL](/powershell/module/az.websites/set-azwebapp)és  [Azure CLI](/cli/azure/webapp/config/appsettings)használatával állíthatja be.

## <a name="enable-profiler-for-other-clouds"></a>A Profiler engedélyezése más felhők számára

Jelenleg csak a végpontok módosítását igénylő régiók [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) és az [Azure China](/azure/china/resources-developer-guide).

|Alkalmazásbeállítás    | Egyesült államokbeli kormányzati felhő | Kínai felhő |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Profiler letiltása

Egy adott alkalmazás példányának leállításához vagy újraindításához a bal oldali oldalsávon válassza a **webjobs** lehetőséget, és állítsa le a megnevezett webjobs `ApplicationInsightsProfiler3` .

  ![A Profiler letiltása webes feladatokhoz][disable-profiler-webjob]

Azt javasoljuk, hogy az összes alkalmazáson engedélyezve legyen a Profiler, hogy a lehető leghamarabb észlelje a teljesítménnyel kapcsolatos problémákat.

A Profiler fájljai törölhető, ha a webalkalmazás használatával helyezi üzembe a webalkalmazás módosításait. A törlést megakadályozhatja, ha nem törli a App_Data mappát az üzembe helyezés során. 


## <a name="next-steps"></a>Következő lépések

* [Az Application Insights használata a Visual Studióban](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png