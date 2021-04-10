---
title: Üzembe helyezési előzmények
description: Ismerteti, hogyan lehet megtekinteni Azure Resource Manager telepítési műveleteket a portál, a PowerShell, az Azure CLI és a REST API használatával.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 898af2365dfbb0f61b6b87e7532c9256269d799a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732771"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Az üzembe helyezési előzmények megtekintése Azure Resource Manager

Azure Resource Manager lehetővé teszi az üzembe helyezési előzmények megtekintését. Megvizsgálhatja a korábbi üzembe helyezések adott műveleteit és megtekintheti, hogy mely erőforrások üzembe helyezésére került sor. Ez az előzmény a hibákkal kapcsolatos információkat tartalmaz.

Egy erőforráscsoport üzembe helyezési előzményei a 800-es üzemelő példányokra korlátozódnak. A korlát közelében az üzembe helyezések automatikusan törlődnek az előzményekből. További információ: [automatikus törlések az üzembe helyezési előzményekből](deployment-history-deletions.md).

Az egyes telepítési hibák elhárításával kapcsolatos segítségért lásd: [gyakori hibák megoldása az erőforrások Azure-ba való telepítésekor a Azure Resource Manager használatával](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Központi telepítések és korrelációs AZONOSÍTÓk beolvasása

A központi telepítés részleteit a Azure Portal, a PowerShell, az Azure CLI vagy a REST API segítségével tekintheti meg. Az egyes központi telepítések korrelációs AZONOSÍTÓval rendelkeznek, amely a kapcsolódó események nyomon követésére szolgál. Ha [létrehoz egy Azure-támogatási kérelmet](../../azure-portal/supportability/how-to-create-azure-support-request.md), a támogatás kérheti a KORRELÁCIÓs azonosító megadását. A támogatás a korrelációs azonosító használatával azonosítja a sikertelen telepítés műveleteit.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Válassza ki azt az erőforráscsoportot, amelyet meg szeretne vizsgálni.

1. Válassza ki az üzemelő **példányok** alatt lévő hivatkozást.

   ![Telepítési előzmények kiválasztása](./media/deployment-history/select-deployment-history.png)

1. Válasszon egyet az üzembe helyezési előzmények közül.

   ![Központi telepítés kiválasztása](./media/deployment-history/select-details.png)

1. Megjelenik a központi telepítés összegzése, beleértve a korrelációs azonosítót is.

    ![A központi telepítés összegzése](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Egy erőforráscsoport összes központi telepítésének listázásához használja a [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) parancsot.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Egy adott telepítés erőforráscsoporthoz való beszerzéséhez adja hozzá a **DeploymentName** paramétert.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

A korrelációs azonosító beszerzéséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Egy erőforráscsoport központi telepítésének listázásához használja az [az Deployment Group List](/cli/azure/group/deployment#az-deployment-group-list)lehetőséget.

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Egy adott központi telepítés beszerzéséhez használja az az [üzembe helyezési csoport megjelenítése](/cli/azure/group/deployment#az-deployment-group-show)lehetőséget.

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

A korrelációs azonosító beszerzéséhez használja a következőt:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Egy erőforráscsoport központi telepítésének listázásához használja a következő műveletet. A kérelemben használandó legújabb API-verzióhoz lásd:  [Deployments – List by erőforráscsoport](/rest/api/resources/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Egy adott központi telepítés beszerzéséhez. használja a következő műveletet. A kérelemben használandó legújabb API-verzióhoz lásd: [üzembe helyezések – Get](/rest/api/resources/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

A válasz tartalmazza a korrelációs azonosítót.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Üzembe helyezési műveletek és hibaüzenetek beolvasása

Az egyes központi telepítések több műveletet is tartalmazhatnak. A központi telepítéssel kapcsolatos további részletekért tekintse meg az üzembe helyezési műveleteket. Ha egy telepítés meghiúsul, a központi telepítési műveletek egy hibaüzenetet tartalmaznak.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A központi telepítés összegzése lapon válassza a **művelet részletei** lehetőséget.

    ![Művelet részleteinek kiválasztása](./media/deployment-history/get-operation-details.png)

1. Ekkor megjelenik a központi telepítés adott lépésének részletei. Hiba esetén a részletek között szerepel a hibaüzenet.

    ![Művelet részleteinek megjelenítése](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha szeretné megtekinteni az erőforráscsoporthoz való központi telepítés központi telepítési műveleteit, használja a [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) parancsot.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

A sikertelen műveletek megtekintéséhez szűrési műveletek **sikertelen** állapottal.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

A sikertelen műveletek állapotjelző üzenetének lekéréséhez használja a következő parancsot:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha meg szeretné tekinteni az erőforráscsoport üzembe helyezésének központi telepítési műveleteit, használja az az [Deployment Operation Group List](/cli/azure/deployment/operation/group#az-deployment-operation-group-list) parancsot. Az Azure CLI 2.6.0 vagy újabb verzióval kell rendelkeznie.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

A sikertelen műveletek megtekintéséhez szűrési műveletek **sikertelen** állapottal.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

A sikertelen műveletek állapotjelző üzenetének lekéréséhez használja a következő parancsot:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Az üzembe helyezési műveletek beszerzéséhez használja a következő műveletet. A kérelemben használandó legújabb API-verzióhoz lásd: [Deployment Operations-List](/rest/api/resources/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

A válasz egy hibaüzenetet tartalmaz.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Következő lépések

* Az egyes telepítési hibák elhárításával kapcsolatos segítségért lásd: [gyakori hibák megoldása az erőforrások Azure-ba való telepítésekor a Azure Resource Manager használatával](common-deployment-errors.md).
* Ha többet szeretne megtudni arról, hogy az üzembe helyezések hogyan kezelhetők az előzményekben, tekintse meg az [automatikus törlés a telepítési előzményekből](deployment-history-deletions.md)című témakört.
* Az üzembe helyezés előtti ellenőrzéshez tekintse meg az [erőforráscsoport üzembe helyezése Azure Resource Manager sablonnal](deploy-powershell.md)című témakört.

