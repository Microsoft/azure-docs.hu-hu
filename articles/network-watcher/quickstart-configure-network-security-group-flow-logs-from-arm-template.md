---
title: 'Rövid útmutató: Hálózati biztonsági csoport forgalomnaplóinak konfigurálása Azure Resource Manager sablonnal (ARM-sablonnal)'
description: Megtudhatja, hogyan engedélyezheti programozott módon a hálózati biztonsági csoport (NSG) folyamatnaplóit egy Azure Resource Manager-sablon (ARM-sablon) és egy Azure PowerShell.
services: network-watcher
author: damendo
ms.author: damendo
ms.date: 01/07/2021
ms.topic: quickstart
ms.service: network-watcher
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: df0ccb5bf5ecd60d80526085983e35abf58e9966
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532443"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Rövid útmutató: Hálózati biztonsági csoport forgalomnaplóinak konfigurálása ARM-sablonnal

Ebből a rövid útmutatóból megtudhatja, hogyan engedélyezheti a hálózati biztonsági [](../azure-resource-manager/management/overview.md) csoport [(NSG)](network-watcher-nsg-flow-logging-overview.md) folyamatnaplóit egy Azure Resource Manager-sablon (ARM-sablon) és egy Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Kezdjük az NSG-folyamatnapló objektum tulajdonságainak áttekintéssel. Mintasablonokat biztosítunk. Ezután egy helyi virtuális gép Azure PowerShell a sablon üzembe helyezéséhez.

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon megnyílik a Azure Portal.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure gyorsindítási sablonokból áll.](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Ezek az erőforrások a sablonban vannak definiálva:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG-forgalomnaplók objektum

Az alábbi kód egy NSG-forgalomnapló-objektumot és annak paramétereit mutatja be. Erőforrás létrehozásához adja hozzá ezt a kódot a sablon `Microsoft.Network/networkWatchers/flowLogs` resources szakaszhoz:

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

Az NSG-forgalomnaplók objektumtulajdonságának teljes áttekintését lásd: [Microsoft.Network networkWatchers/flowLogs.](/azure/templates/microsoft.network/networkwatchers/flowlogs)

## <a name="create-your-template"></a>A sablon létrehozása

Ha első alkalommal használ ARM-sablonokat, az ARM-sablonokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
- [Oktatóanyag: Az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Az alábbi példa egy teljes sablon. Emellett ez a sablon legegyszerűbb verziója is. A példa az NSG-forgalom naplóinak beállítására megadott minimális paramétereket tartalmazza. További példákért tekintse meg az NSG-forgalomnaplók sablonból való [konfigurálásról Azure Resource Manager cikket.](network-watcher-nsg-flow-logging-azure-resource-manager.md)

### <a name="example"></a>Példa

Az alábbi sablon engedélyezi egy NSG folyamatnaplóit, majd egy adott tárfiókban tárolja a naplókat:

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
> - Az erőforrás neve a következő _formátumot ParentResource_ChildResource._ A példánkban a szülőerőforrás a regionális Azure Network Watcher példány:
>    - **Formátum:** NetworkWatcher_RegionName
>    - **Például:** NetworkWatcher_centraluseuap
> - `targetResourceId` A a cél NSG erőforrás-azonosítója.
> - `storageId` A a cél tárfiók erőforrás-azonosítója.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Ez az oktatóanyag feltételezi, hogy van egy meglévő erőforráscsoportja és egy NSG-je, amelybe engedélyezheti a folyamatnaplózást.

A cikkben bemutatott példasablonok bármelyikét mentheti helyileg,azuredeploy.js *a fájlban.* Frissítse a tulajdonságértékeket, hogy érvényes erőforrásokra mutasson az előfizetésben.

A sablon üzembe helyezéséhez futtassa a következő parancsot a Azure PowerShell:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Ezek a parancsok egy erőforrást helyeznek üzembe a példa NetworkWatcherRG erőforráscsoportban, és nem az NSG-t tartalmazó erőforráscsoportban.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Két lehetőség közül választhat, hogy lássa, sikeres volt-e az üzembe helyezés:

- A PowerShell-konzol a következőként `ProvisioningState` jelenik `Succeeded` meg: .
- A módosítások megerősítéséhez kattintson az [NSG-forgalom naplói](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) portáloldalára.

Ha problémái voltak az üzembe helyezéssel, tekintse meg az Azure-beli üzembe helyezés [gyakori hibáinak elhárítása Azure Resource Manager.](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure-erőforrásokat a teljes üzembe helyezési mód használatával törölheti. Folyamatnapló-erőforrás törléséhez teljes módban adjon meg egy üzembe helyezést a törölni kívánt erőforrás nélkül. További információ a [teljes üzembe helyezési módról.](../azure-resource-manager/templates/deployment-modes.md#complete-mode)

Az NSG-forgalom naplóját a következő Azure Portal:

1. Jelentkezzen be az Azure Portalra.
1. Válassza az **Összes szolgáltatás** elemet. A Szűrő **mezőbe írja** be a **network watcher mezőt.** A keresési eredmények között válassza **a** Network Watcher.
1. A **Naplók alatt** válassza az **NSG-forgalom naplói lehetőséget.**
1. Az NSG-k listájában válassza ki azt az NSG-t, amelyhez le szeretné tiltani a folyamatnaplókat.
1. A **Folyamatnaplók beállításai alatt válassza** a Ki **lehetőséget.**
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan engedélyezheti az NSG-forgalom naplóit egy ARM-sablonnal. Ezután megtudhatja, hogyan vizualizálhatja az NSG-forgalom adatait az alábbi lehetőségek egyikének használatával:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Nyílt forráskódú eszközök](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Traffic Analytics](traffic-analytics.md)
