---
title: A .NET-alkalmazások Snapshot Debugger engedélyezése a Azure App Serviceban | Microsoft Docs
description: .NET-alkalmazások Snapshot Debuggerának engedélyezése Azure App Service
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0fc46c815906b9aaca7b3ce964e0d23cfdc4e1aa
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673574"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>.NET-alkalmazások Snapshot Debuggerának engedélyezése Azure App Service

A Snapshot Debugger jelenleg a ASP.NET és a ASP.NET Core a Windows-szolgáltatási csomagok Azure App Service futó alkalmazásain működik. Javasoljuk, hogy az alkalmazást az alapszintű szolgáltatási szinten vagy magasabb szinten futtassa a Snapshot Debugger használatakor. A legtöbb alkalmazás esetében az ingyenes és a közös szolgáltatási rétegek nem rendelkeznek elegendő memóriával vagy lemezterülettel a pillanatképek mentéséhez.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Snapshot Debugger engedélyezése
Az alkalmazások Snapshot Debuggerának engedélyezéséhez kövesse az alábbi utasításokat.

Ha más típusú Azure-szolgáltatást futtat, akkor a Snapshot Debugger más támogatott platformokon való engedélyezésével kapcsolatban itt talál útmutatást:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure-Virtual Machines és virtuálisgép-méretezési csoportok](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Helyszíni virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> Ha a .NET Core előzetes verzióját használja, vagy az alkalmazása Application Insights SDK-ra hivatkozik közvetlenül vagy közvetve egy függő szerelvényen keresztül, kövesse a [Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomagnak az alkalmazással való használatának [Snapshot Debugger engedélyezéséhez](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) szükséges lépéseket, majd végezze el az alábbi utasítások további részét. 

A Snapshot Debugger a App Services futtatókörnyezet részeként előre telepítve van, de a App Service alkalmazáshoz tartozó Pillanatképek lekéréséhez be kell kapcsolni.

Miután telepítette az alkalmazást, kövesse az alábbi lépéseket a Snapshot Debugger engedélyezéséhez:

1. Navigáljon a App Servicehoz tartozó Azure-vezérlőpultra.
2. Lépjen a **beállítások > Application Insights** lapra.

   ![Az alkalmazás-felismerés engedélyezése App Services portálon](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Egy új erőforrás létrehozásához kövesse az oldalon megjelenő utasításokat, vagy válasszon ki egy meglévő alkalmazás-keresési erőforrást az alkalmazás figyeléséhez. Győződjön meg arról is, hogy a Snapshot Debugger mindkét kapcsolója **be van kapcsolva**.

   ![Alkalmazás-áttekintési hely kiterjesztésének hozzáadása][Enablement UI]

4. A Snapshot Debugger mostantól engedélyezve van egy App Services alkalmazás-beállítás használatával.

    ![Alkalmazás-beállítás a Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Snapshot Debugger letiltása

Hajtsa végre az **Snapshot Debugger engedélyezésének** lépéseit, de mindkét kapcsolót a Snapshot Debuggerra kapcsolhatja **ki**.

Azt javasoljuk, hogy az alkalmazás-kivételek diagnosztizálásának megkönnyítéséhez minden alkalmazáson Snapshot Debugger engedélyezzen.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Azure App Service esetén a Azure Resource Manager sablonban megadhatja az alkalmazás beállításait a Snapshot Debugger és a Profiler engedélyezéséhez, lásd az alábbi sablon kódrészletet:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Következő lépések

- Adatforgalom létrehozása az alkalmazás számára, amely kivételt indíthat. Ezután várjon 10 – 15 percet a pillanatképek Application Insights példányba való elküldésekor.
- A Azure Portal található [Pillanatképek](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) .
- Snapshot Debugger problémák elhárításával kapcsolatos segítségért lásd: [Snapshot Debugger hibaelhárítás](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

