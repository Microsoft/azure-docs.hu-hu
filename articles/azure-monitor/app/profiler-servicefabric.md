---
title: A profil élő Azure Service Fabric alkalmazásai Application Insights
description: A Profiler engedélyezése Service Fabric alkalmazáshoz
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 67e7765a1f46c2be5790c11687e06ea624702b9b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589568"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>A profil élő Azure Service Fabric alkalmazásai Application Insights

A Application Insights Profiler a következő szolgáltatásokon is üzembe helyezhető:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>A környezet központi telepítési definíciójának beállítása

A Application Insights Profiler a Azure Diagnostics részét képezi. A Azure Diagnostics bővítményt a Service Fabric-fürthöz tartozó Azure Resource Manager sablonnal is telepítheti. Olyan sablon beszerzése [, amely Azure Diagnostics telepít egy Service Fabric-fürtön](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

A környezet beállításához végezze el a következő műveleteket:

1. A Profiler a .NET-keretrendszer és a .net Core használatát is támogatja. Ha .NET-keretrendszert használ, győződjön meg róla, hogy a [.NET-keretrendszer 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) -es vagy újabb verzióját használja. Elegendő meggyőződni arról, hogy a telepített operációs rendszer `Windows Server 2012 R2` vagy újabb. A Profiler támogatja a .NET Core 2,1-es és újabb alkalmazásokat.

1. Keresse meg a [Azure Diagnostics](../agents/diagnostics-extension-overview.md) bővítményt a telepítési sablon fájljában.

1. Adja hozzá a következő `SinksConfig` szakaszt a gyermek elemeként `WadCfg` . Cserélje le a `ApplicationInsightsProfiler` tulajdonság értékét a saját Application Insights kialakítási kulcsára:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      További információ a diagnosztikai bővítmény telepítési sablonhoz való hozzáadásáról: a [figyelés és a diagnosztika használata Windows rendszerű virtuális gépekkel és Azure Resource Manager sablonokkal](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json).

1. Telepítse a Service Fabric-fürtöt a Azure Resource Manager sablon használatával.  
  Ha a beállítások helyesek, Application Insights Profiler lesz telepítve és engedélyezve a Azure Diagnostics bővítmény telepítésekor. 

1. Application Insights hozzáadása a Service Fabric alkalmazáshoz.  
  Ahhoz, hogy a Profiler összegyűjtse a kérelmek profiljait, az alkalmazásnak nyomon kell követnie a Application Insights. Állapot nélküli API-k esetén a [profilkészítési kérelmek nyomon követésére](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)vonatkozó utasításokat tekintheti meg. További információ az egyéni műveletek más típusú alkalmazásokban való nyomon követéséről: [Egyéni műveletek nyomon követése Application Insights .net SDK-val](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Telepítse újra az alkalmazást.


## <a name="next-steps"></a>Következő lépések

* Adatforgalom létrehozása az alkalmazáshoz (például egy [rendelkezésre állási teszt](monitor-web-app-availability.md)elindításához). Ezután várjon 10 – 15 percet, amíg a Nyomkövetések el nem jutnak a Application Insights példányba.
* Lásd: [Profiler-Nyomkövetések](profiler-overview.md?toc=/azure/azure-monitor/toc.json) a Azure Portalban.
* A Profiler hibaelhárításával kapcsolatos segítségért lásd a [Profiler hibaelhárítását](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)ismertető témakört.
