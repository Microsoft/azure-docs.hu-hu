---
title: 'Gyors útmutató: hálózati biztonsági csoport folyamatábráinak konfigurálása Azure Resource Manager sablon használatával (ARM-sablon)'
description: Megtudhatja, hogyan engedélyezheti programozott módon a hálózati biztonsági csoport (NSG) folyamatait egy Azure Resource Manager sablon (ARM-sablon) és Azure PowerShell használatával.
services: network-watcher
author: damendo
Customer intent: I need to enable the network security group flow logs by using an Azure Resource Manager template.
ms.service: network-watcher
ms.topic: quickstart
ms.date: 01/07/2021
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: bc075e5074fe39ad38e45235af932b40fef78fce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521866"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Gyors útmutató: hálózati biztonsági csoport folyamatábráinak konfigurálása ARM-sablonnal

Ebből a rövid útmutatóból megtudhatja, hogyan engedélyezheti a [hálózati biztonsági csoport (NSG) folyamatának naplóit](network-watcher-nsg-flow-logging-overview.md) [Azure Resource Manager](../azure-resource-manager/management/overview.md) sablon (ARM-sablon) és Azure PowerShell használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Első lépésként tekintse át a NSG flow log objektum tulajdonságait. Minta sablonokat biztosítunk. Ezután egy helyi Azure PowerShell példányt használunk a sablon üzembe helyezéséhez.

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon megnyílik a Azure Portalban.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)származik.

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Ezek az erőforrások a sablonban vannak definiálva:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Resources/üzemelő példányok](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG-folyamat naplófájljainak objektuma

A következő kód egy NSG-folyamatábra objektumot és annak paramétereit jeleníti meg. Erőforrás létrehozásához `Microsoft.Network/networkWatchers/flowLogs` adja hozzá ezt a kódot a sablon erőforrások szakaszához:

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

A NSG flow-naplók objektum tulajdonságainak teljes áttekintését lásd: [Microsoft. Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Sablon létrehozása

Ha első alkalommal használ ARM-sablonokat, tekintse meg az alábbi cikkeket az ARM-sablonokkal kapcsolatos további információkért:

- [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
- [Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

A következő példa egy teljes sablon. Ez a sablon legegyszerűbb verziója is. A példa a NSG-naplók beállításakor átadott minimális paramétereket tartalmazza. További példákért tekintse meg a [NSG flow-naplók konfigurálása Azure Resource Manager sablonból](network-watcher-nsg-flow-logging-azure-resource-manager.md)című cikket.

### <a name="example"></a>Példa

A következő sablon lehetővé teszi a NSG számára a flow-naplókat, majd egy adott Storage-fiókban tárolja a naplókat:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - Az erőforrás neve a _ParentResource_ChildResource_ formátumot használja. A példánkban a szülő erőforrás a regionális Azure Network Watcher példány:
>    - **Formátum**: NetworkWatcher_RegionName
>    - **Példa**: NetworkWatcher_centraluseuap
> - `targetResourceId` a cél NSG erőforrás-azonosítója.
> - `storageId` a cél Storage-fiók erőforrás-azonosítója.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Ez az oktatóanyag feltételezi, hogy rendelkezik egy meglévő erőforráscsoporthoz és egy NSG, amely lehetővé teszi a folyamat bejelentkezését.

A cikkben látható példák bármelyikét a következő módon mentheti: *azuredeploy.js*. Frissítse a tulajdonságértékek értékét, hogy az előfizetése érvényes erőforrásaira mutasson.

A sablon üzembe helyezéséhez futtassa a következő parancsot Azure PowerShellban:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Ezek a parancsok egy erőforrást helyeznek üzembe a példa NetworkWatcherRG erőforráscsoporthoz, és nem a NSG tartalmazó erőforráscsoporthoz.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Két lehetősége van annak megtekintésére, hogy sikeres volt-e az üzembe helyezés:

- A PowerShell-konzol a következőképpen jelenik meg: `ProvisioningState` `Succeeded` .
- A változtatások megerősítéséhez lépjen a [NSG flow-naplók portál oldalra](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) .

Ha probléma merült fel a központi telepítéssel kapcsolatban, tekintse meg a [gyakori Azure-telepítési hibák elhárítása a Azure Resource Managerkal](../azure-resource-manager/templates/common-deployment-errors.md)című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure-erőforrásokat a teljes telepítési mód használatával törölheti. A flow-naplók erőforrásának törléséhez adja meg a telepítést teljes módban anélkül, hogy a törölni kívánt erőforrást is tartalmazza. További információ a [teljes telepítési módból](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

A Azure Portal NSG is letilthatja:

1. Jelentkezzen be az Azure Portalra.
1. Válassza az **Összes szolgáltatás** elemet. A **szűrő** mezőbe írja be a **Network Watcher** értéket. A keresési eredmények között válassza a **Network Watcher** lehetőséget.
1. A **naplók** területen válassza a **NSG flow-naplók** lehetőséget.
1. A NSG listából válassza ki azt a NSG, amely számára le szeretné tiltani a folyamat naplóit.
1. A **flow-naplók beállításai** területen válassza a **ki** lehetőséget.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan engedélyezheti a NSG-folyamatok naplóit egy ARM-sablon használatával. Következő lépésként megtudhatja, hogyan jelenítheti meg a NSG folyamat adatait a következő lehetőségek egyikének használatával:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Nyílt forráskódú eszközök](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure-Traffic Analytics](traffic-analytics.md)
