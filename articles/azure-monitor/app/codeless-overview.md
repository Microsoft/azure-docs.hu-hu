---
title: Alkalmazások figyelése programkód módosítása nélkül – automatikus rendszerállapot-Azure Monitor Application Insights | Microsoft Docs
description: A Azure Monitor Application Insights-kód alapú alkalmazások teljesítményének felügyeletéhez szükséges automatikus kiépítés áttekintése
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: fe57174f1b090cbaa2196930f5ddd252074f1978
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526449"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Mi az Auto-Instrumentation vagy a kód-Azure Monitor Application Insights?

Az automatikus és a kód nélküli csatolás lehetővé teszi, hogy a kód módosítása nélkül engedélyezze az alkalmazások figyelését Application Insights.  

Application Insights integrálva van különböző erőforrás-szolgáltatókkal, és különböző környezetekben működik. Lényegében mindössze annyit kell tennie, hogy engedélyezi és – bizonyos esetekben – konfigurálja az ügynököt, amely összegyűjti a telemetria automatikusan mezőt. A Application Insights erőforrásban a metrikák, az adatok és a függőségek nem jelennek meg, ami lehetővé teszi a lehetséges problémák forrásának megvásárlását, mielőtt azok bejelentkeznek, és elemezze a kiváltó okot a végpontok közötti tranzakciós nézettel.

## <a name="supported-environments-languages-and-resource-providers"></a>Támogatott környezetek, nyelvek és erőforrás-szolgáltatók

Ha további integrációkat adunk hozzá, az automatikus rendszerállapot-kialakítási képesség mátrixa összetett lesz. Az alábbi táblázat azt mutatja be, hogy milyen aktuális állapotban van a különböző erőforrás-szolgáltatók, nyelvek és környezetek támogatása.

|Környezet/erőforrás-szolgáltató          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service Windows rendszeren           | GA, OnBD *       | GA, opt-in      | Folyamatban     | Folyamatban     | Nem támogatott   |
|Azure App Service Linuxon             | N/A             | Nem támogatott   | Folyamatban     | Nyilvános előzetes verzió  | Nem támogatott   |
|Azure App Service AK-on               | N/A             | A tervezésben       | A tervezésben       | A tervezésben       | Nem támogatott   |
|Azure Functions – alapszintű                | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Functions Windows-függőségek | Nem támogatott   | Nem támogatott   | Nyilvános előzetes verzió  | Nem támogatott   | Nem támogatott   |
|Azure Kubernetes Service               | N/A             | A tervezésben       | Ügynökön keresztül   | A tervezésben       | Nem támogatott   |
|Azure-beli virtuális gépek Windows                      | Nyilvános előzetes verzió  | Nem támogatott   | Nem támogatott   | Nem támogatott   | Nem támogatott   |
|Helyszíni virtuális gépek Windows                | GA, opt-in      | Nem támogatott   | Ügynökön keresztül   | Nem támogatott   | Nem támogatott   |
|Önálló ügynök – bármely env.            | Nem támogatott   | Nem támogatott   | FE              | Nem támogatott   | Nem támogatott   |

* A OnBD alapértelmezés szerint a következő: a Application Insights automatikusan engedélyezve lesz, amint üzembe helyezi az alkalmazást a támogatott környezetekben. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
A Windows rendszeren futó Azure App Service alkalmazás-figyelése a [.NET-alkalmazások](./azure-web-apps.md?tabs=net) .net számára érhető el, és alapértelmezés szerint engedélyezve van.

#### <a name="netcore"></a>. NETCore
A figyelése [. A NETCore-alkalmazások](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=netcore) egyetlen kattintással engedélyezhetők.

#### <a name="java"></a>Java
A App Service Windows rendszeren futó Java-alkalmazások figyelésére szolgáló portál integrációja jelenleg nem érhető el, azonban a kód módosítása nélkül is hozzáadhat Application Insights [Java 3,0 önálló ügynököt](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) az alkalmazáshoz anélkül, hogy az alkalmazásokat a app Servicere telepítené. A Application Insights Java 3,0-ügynök általánosan elérhető.

#### <a name="nodejs"></a>Node.js
Node.js-alkalmazások figyelése a Windowsban jelenleg nem engedélyezhető a portálon. Node.js alkalmazások figyeléséhez használja az [SDK](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)-t.

### <a name="linux"></a>Linux

#### <a name="netcore"></a>. NETCore
A figyeléshez. A Linux rendszeren futó NETCore-alkalmazások az [SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)-t használják.

#### <a name="java"></a>Java 
A Java-alkalmazások App Service figyelésének engedélyezése a portálon a Linuxon nem érhető el, de [Application Insights java 3,0-ügynököt](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) is hozzáadhat az alkalmazáshoz, mielőtt a app Servicere telepítené az alkalmazásokat. A Application Insights Java 3,0-ügynök általánosan elérhető.

#### <a name="nodejs"></a>Node.js
A [Linux rendszeren futó app Service-alkalmazások Node.js figyelése](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=nodejs) nyilvános előzetes verzióban érhető el, és a Azure Portal-ben is engedélyezhető, minden régióban elérhető. 

#### <a name="python"></a>Python
Az SDK használata a [Python-alkalmazás figyelésére](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python) 

## <a name="azure-functions"></a>Azure Functions

A Azure Functions alapszintű figyelése alapértelmezés szerint engedélyezve van a naplók, a teljesítmény, a hibák és a HTTP-kérések gyűjtésére. A Java-alkalmazások esetében engedélyezheti a szélesebb körű figyelést az elosztott nyomkövetéssel, és lekérheti a végpontok közötti tranzakció részleteit. A Javához készült funkció nyilvános előzetes verzióban érhető el, és [Azure Portalban is engedélyezhető](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Az Azure Kubernetes szolgáltatás kód nélküli üzembe helyezése jelenleg a [különálló ügynökön](./java-in-process-agent.md)keresztül érhető el Java-alkalmazásokhoz. 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows-alapú virtuális gépek és virtuálisgép-méretezési csoport

Az Azure-beli virtuális gépek és a virtuálisgép-méretezési csoport automatikus kiosztása a [.net](./azure-vm-vmss-apps.md) és a [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)esetében is elérhető.  

## <a name="on-premises-servers"></a>Helyszíni kiszolgálók
Egyszerűen engedélyezheti a helyszíni Windows- [kiszolgálók .NET-alkalmazásokhoz](./status-monitor-v2-overview.md) és [Java](./java-in-process-agent.md)-alkalmazásokhoz való figyelését.

## <a name="other-environments"></a>Egyéb környezetek
A sokoldalú Java önálló ügynök bármilyen környezetben működik, nincs szükség a kód megalkotására. Az [útmutatót követve](./java-in-process-agent.md) engedélyezze Application Insightst, és olvassa el a Java-ügynök csodálatos képességeit. Az ügynök nyilvános előzetes verzióban érhető el, és minden régióban elérhető. 

## <a name="next-steps"></a>Következő lépések

* [Application Insights áttekintése](./app-insights-overview.md)
* [Alkalmazás-hozzárendelés](./app-map.md)
* [Végpontok közötti teljesítmény figyelése](../learn/tutorial-performance.md)

