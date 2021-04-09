---
title: .NET-és .NET Core-alkalmazások Snapshot Debuggerének engedélyezése a Azure Functionsban | Microsoft Docs
description: .NET-és .NET Core-alkalmazások Snapshot Debuggerének engedélyezése Azure Functions
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: 48eb3cf81384446a07fea69572ac16e0b80cee38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025628"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>.NET-és .NET Core-alkalmazások Snapshot Debuggerének engedélyezése Azure Functions

A Snapshot Debugger jelenleg a ASP.NET és a ASP.NET Core a Windows-szolgáltatási csomagok Azure Functions futó alkalmazásain működik.

Javasoljuk, hogy az alkalmazást az alapszintű szolgáltatási szinten vagy magasabb szinten futtassa Snapshot Debugger használatakor.

A legtöbb alkalmazás esetében az ingyenes és a közös szolgáltatási rétegek nem rendelkeznek elegendő memóriával vagy lemezterülettel a pillanatképek mentéséhez.

## <a name="prerequisites"></a>Előfeltételek

* [Application Insights figyelés engedélyezése a függvényalkalmazás](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>Snapshot Debugger engedélyezése

Ha más típusú Azure-szolgáltatást futtat, akkor a Snapshot Debugger más támogatott platformokon való engedélyezésével kapcsolatban itt talál útmutatást:
* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure-Virtual Machines és virtuálisgép-méretezési csoportok](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Helyszíni virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Ha engedélyezni szeretné a Snapshot Debugger a Function alkalmazásban, frissítenie kell a `host.json` fájlt úgy, hogy hozzáadja az alább meghatározott tulajdonságot, `snapshotConfiguration` és újból üzembe helyezi a függvényt.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

Snapshot Debugger előre telepítve van a Azure Functions futtatókörnyezet részeként, amely alapértelmezés szerint le van tiltva.

Mivel Snapshot Debugger a Azure Functions Runtime része, nincs szükség további NuGet-csomagok és Alkalmazásbeállítások hozzáadására.

Az egyszerű functions-alkalmazás (.NET Core) esetében az alábbi módon fog megjelenni a, a `.csproj` `{Your}Function.cs` és az `host.json` Engedélyezés után Snapshot Debugger.

Projekt csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Function osztály

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

Gazda fájl

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Snapshot Debugger engedélyezése más felhők számára

Jelenleg csak a végpontok módosítását igénylő régiók [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) és az [Azure China](/azure/china/resources-developer-guide).

Alább látható egy példa az `host.json` Egyesült államokbeli kormányzati Felhőbeli ügynök végpontjának frissítésére:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

Alább láthatók a Snapshot Debugger ügynök végpontjának támogatott felülbírálásai:

|Tulajdonság    | Egyesült államokbeli kormányzati felhő | Kínai felhő |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>Snapshot Debugger letiltása

Ha le szeretné tiltani a Snapshot Debugger a Function alkalmazásban, csak `host.json` a tulajdonság beállításával kell frissítenie a fájlt `false` `snapshotConfiguration.isEnabled` .

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

Azt javasoljuk, hogy az alkalmazás-kivételek diagnosztizálásának megkönnyítéséhez minden alkalmazáson Snapshot Debugger engedélyezzen.

## <a name="next-steps"></a>Következő lépések

- Adatforgalom létrehozása az alkalmazás számára, amely kivételt indíthat. Ezután várjon 10 – 15 percet a pillanatképek Application Insights példányba való elküldésekor.
- [Pillanatképek megtekintése](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) a Azure Portalban.
- Szabja testre Snapshot Debugger konfigurációját a Function alkalmazás használati esete alapján. További információ: [Pillanatkép-konfiguráció host.json](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration).
- Snapshot Debugger problémák elhárításával kapcsolatos segítségért lásd: [Snapshot Debugger hibaelhárítás](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).