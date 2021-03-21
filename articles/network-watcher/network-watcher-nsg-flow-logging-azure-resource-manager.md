---
title: Network Watcher – NSG-naplók létrehozása Azure Resource Manager sablon használatával
description: Egy Azure Resource Manager sablonnal és PowerShell-lel egyszerűen állíthatja be a NSG folyamat naplóit.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: fasttrack-edit
ms.openlocfilehash: 2cae673dd319e55670357ae468d21ff63e4f9c72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669440"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>NSG-adatforgalmi naplók konfigurálása Azure Resource Manager sablonból

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


A [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) az Azure natív és hatékony módja az [infrastruktúra kódként](/azure/devops/learn/what-is-infrastructure-as-code)való kezelésének.

Ebből a cikkből megtudhatja, hogyan engedélyezheti programozott módon a [NSG-flow-naplókat](./network-watcher-nsg-flow-logging-overview.md) egy Azure Resource Manager sablon és Azure PowerShell használatával. Kezdjük azzal, hogy áttekintjük a NSG flow log objektum tulajdonságait, amelyet néhány sablon követ. Ezután a sablon üzembe helyezése egy helyi PowerShell-példány használatával.


## <a name="nsg-flow-logs-object"></a>NSG-folyamat naplófájljainak objektuma

Az NSG flow az összes paraméterrel rendelkező objektumot alább mutatja.
A tulajdonságok teljes áttekintéséhez olvassa el a [NSG flow-naplók sablonjának referenciáját](/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Microsoft. Network/networkWatchers/flowLogs erőforrás létrehozásához adja hozzá a fenti JSON-t a sablon erőforrások szakaszához.


## <a name="creating-your-template"></a>A sablon létrehozása

Ha első alkalommal használja Azure Resource Manager-sablonokat, további információt az alábbi hivatkozásokon talál.

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
* [Oktatóanyag: az első Azure Resource Manager-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


Az alábbi két példa a teljes sablonokat mutatja be a NSG-naplók beállításához.

**1. példa**: a fenti legegyszerűbb verziója az átadott minimális paraméterekkel együtt. Az alábbi sablon lehetővé teszi, hogy a NSG a cél NSG, és tárolja őket egy adott Storage-fiókban.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Az erőforrás neve "szülő Resource_Child erőforrás" formátumú. Itt a szülő erőforrás a regionális Network Watcher-példány (Format: NetworkWatcher_RegionName. Példa: NetworkWatcher_centraluseuap)
> * a Targetresourceid azonosítója a cél NSG erőforrás-azonosítója.
> * a tárolóazonosítót a cél Storage-fiók erőforrás-azonosítója.

**2. példa**: a következő sablonok lehetővé teszik, hogy a NSG flow-naplók (2. verzió) 5 napig tartson megőrzést. A Traffic Analytics engedélyezése 10 perces feldolgozási intervallummal.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {
          "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
          }
        },
        "retentionPolicy": {
          "days": 5,
          "enabled": true
        },
        "format": {
          "type": "JSON",
          "version": 2
        }
      }
    }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon üzembe helyezése

Ez az oktatóanyag feltételezi, hogy rendelkezik egy meglévő erőforráscsoporthoz és egy NSG, amelyen engedélyezheti a folyamat naplózását.
A fenti példák bármelyikét helyileg is mentheti `azuredeploy.json` . Frissítse a tulajdonságértékek értékét úgy, hogy az előfizetése érvényes erőforrásokra mutasson.

A sablon üzembe helyezéséhez futtassa a következő parancsot a PowerShellben.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId <SubscriptionId>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> A fenti parancsok egy erőforrást helyeznek üzembe a NetworkWatcherRG erőforráscsoporthoz, és nem a NSG tartalmazó erőforráscsoportot.


## <a name="verifying-your-deployment"></a>Az üzemelő példány ellenőrzése

Több módon is ellenőrizhető, hogy sikeres volt-e a központi telepítés. A PowerShell-konzol a "ProvisioningState" kifejezést "sikeres" értékre kell mutatnia. Emellett a [NSG flow-naplók portál oldalán](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) is megtekintheti a módosításokat. Ha probléma merült fel az üzembe helyezéssel kapcsolatban, tekintse meg a [gyakori Azure-telepítési hibák elhárítását Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="deleting-your-resource"></a>Erőforrás törlése
Az Azure lehetővé teszi az erőforrások törlését a "teljes" üzembe helyezési módban. A flow-naplók erőforrásának törléséhez adja meg a telepítést teljes módban anélkül, hogy a törölni kívánt erőforrást is tartalmazza. További információ a [teljes telepítési módból](../azure-resource-manager/templates/deployment-modes.md#complete-mode)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan jelenítheti meg a NSG flow-adatait a következő használatával:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Nyílt forráskódú eszközök](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure-Traffic Analytics](./traffic-analytics.md)
