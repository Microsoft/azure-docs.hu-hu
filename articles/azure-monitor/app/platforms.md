---
title: 'Application Insights: nyelvek, platformok és integrációk | Microsoft Docs'
description: A Application Insights számára elérhető nyelvek, platformok és integrációk
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 399e57377a779622aa3073dfd3313cee1db345f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583865"
---
# <a name="supported-languages"></a>Támogatott nyelvek

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-get-started.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Támogatott platformok és keretrendszerek

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Már üzembe helyezett alkalmazások rendszerállapotba állítása (kód nélküli, ügynök-alapú)
* [Azure-beli virtuális gépek és Azure-beli virtuálisgép-méretezési csoportok](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [ASP.NET – már élő alkalmazásokhoz](./monitor-performance-live-website-now.md)
* [Azure-Cloud Services](./cloudservices.md), beleértve a webes és a feldolgozói szerepköröket is
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Rendszerállapot-kódok (SDK-k)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../app/mobile-center-quickstart.md) (App Center)
* [iOS](../app/mobile-center-quickstart.md) (App Center)
* [Java EE](./java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [Univerzális Windows-alkalmazás](../app/mobile-center-quickstart.md) (App Center)
* [Asztali Windows-alkalmazások, szolgáltatások és feldolgozói szerepkörök](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md)

## <a name="logging-frameworks"></a>Naplózási keretrendszerek
* [ILogger](./ilogger.md)
* [Log4Net, NLog, vagy System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J, vagy Logback](./java-trace-logs.md)
* [LogStash beépülő modul](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Exportálás és adatelemzés
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>Nem támogatott SDK-k
Tisztában vagyunk azzal, hogy számos más Közösség által támogatott SDK létezik. A Azure Monitor azonban csak az ezen a lapon felsorolt támogatott SDK-k használata esetén nyújt támogatást. Folyamatosan kivizsgáljuk, hogyan bővítjük támogatását más nyelvekre, ezért kövesse a [GitHub-közlemények](https://github.com/microsoft/ApplicationInsights-Announcements/issues) oldalon a legújabb SDK-híreket. 

