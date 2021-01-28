---
title: Profil élő Azure App Service alkalmazások a Application Insightskal | Microsoft Docs
description: A Azure App Serviceon futó élő alkalmazások a Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: b1158a614da9ba32f628aba5dd2ed2cc71b4b455
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947043"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profil élő Azure App Service alkalmazások Application Insights

A Profilert futtathatja a ASP.NET-on, és ASP.NET Core a Azure App Service-on futó alkalmazások alapszintű vagy magasabb szintű használatát. A Profiler Linux rendszeren való engedélyezése jelenleg csak [ezen a metóduson](profiler-aspnetcore-linux.md)keresztül lehetséges.

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> A Profiler engedélyezése az alkalmazáshoz
A Profiler engedélyezéséhez kövesse az alábbi utasításokat. Ha más típusú Azure-szolgáltatást futtat, akkor a Profiler más támogatott platformokon való engedélyezésével kapcsolatban itt talál útmutatást:
* [Felhőszolgáltatások](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Alkalmazások Service Fabric](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Virtual Machines](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler az App Services futtatókörnyezet részeként előre telepítve van. Az alábbi lépések bemutatják, hogyan engedélyezheti a App Service számára. Kövesse az alábbi lépéseket még akkor is, ha felépítésekor az alkalmazás betekintő SDK-t is feltöltötte az alkalmazásba.

1. Navigáljon a App Servicehoz tartozó Azure-vezérlőpultra.
1. Az App Service "always on" beállításának engedélyezése. Ezt a beállítást a **Beállítások**, a **konfiguráció** lapon találja (lásd a következő lépés képernyőképét), majd kattintson az **általános beállítások** fülre.
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

### <a name="enabling-profiler-for-other-clouds-manually"></a>A Profiler engedélyezése más felhők számára manuálisan

Ha engedélyezni szeretné a Profilert más felhők esetében, használhatja az alábbi beállításokat.

|Alkalmazásbeállítás    | USA kormányzati értékei| Kínai felhő |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Profiler letiltása

Egy adott alkalmazás példányának leállításához vagy újraindításához a **webjobs** alatt állítsa le a ApplicationInsightsProfiler3 nevű webjobs. Még ha a Profiler is le van tiltva a Application Insights lapon a kapcsoló használatával, a Profiler-folyamat továbbra is futni fog. A Profiler megtekinti, hogy engedélyezve van-e. Ha a szolgáltatás le van tiltva, az alvó állapotba kerül egy ideig, az újbóli ellenőrzés előtt. Nem végez profilkészítést, ha le van tiltva. Ha letiltja ezt a webjobs, a Profiler-folyamat egyáltalán nem fog futni, még annak ellenőrzéséhez, hogy engedélyezve van-e.

  ![A Profiler letiltása webes feladatokhoz][disable-profiler-webjob]

Azt javasoljuk, hogy az összes alkalmazáson engedélyezve legyen a Profiler, hogy a lehető leghamarabb észlelje a teljesítménnyel kapcsolatos problémákat.

A Profiler fájljai törölhető, ha a webalkalmazás használatával helyezi üzembe a webalkalmazás módosításait. A törlést megakadályozhatja, ha nem törli a App_Data mappát az üzembe helyezés során. 


## <a name="next-steps"></a>Következő lépések

* [Az Application Insights használata a Visual Studióban](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

