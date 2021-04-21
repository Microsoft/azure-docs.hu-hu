---
title: Üzembe helyezési előzmények
description: Ez a cikk azt ismerteti Azure Resource Manager hogyan lehet megtekinteni a portál, a PowerShell, az Azure CLI és a REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e7ed2096a696efdc9a2654a8fd0c294c82cbd4f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781864"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Telepítési előzmények megtekintése Azure Resource Manager

Azure Resource Manager lehetővé teszi az üzembe helyezési előzmények megtekintését. Megvizsgálhatja a korábbi üzembe helyezések adott műveleteit és megtekintheti, hogy mely erőforrások üzembe helyezésére került sor. Ez az előzmények a hibákkal kapcsolatos információkat tartalmaznak.

Egy erőforráscsoport üzembe helyezési előzményei legfeljebb 800 üzemelő példányra korlátozódnak. A korlát közeledtével az üzemelő példányok automatikusan törlődnek az előzményekből. További információ: Automatikus törlés [az üzembe helyezési előzményekből.](deployment-history-deletions.md)

Az egyes üzembe helyezési hibák elhárításával kapcsolatos segítségért tekintse meg az Erőforrások Azure-ba való üzembe helyezésekor a következővel kapcsolatos [gyakori Azure Resource Manager.](common-deployment-errors.md)

## <a name="get-deployments-and-correlation-id"></a>Üzemelő példányok és korrelációs azonosító lekért száma

Az üzembe helyezés részleteinek megtekintéséhez használja a Azure Portal, a PowerShellt, az Azure CLI-t vagy a REST API. Minden üzemelő példány rendelkezik egy korrelációs azonosítóval, amely a kapcsolódó események nyomon követésére használható. Ha létrehoz [egy Azure-támogatás,](../../azure-portal/supportability/how-to-create-azure-support-request.md)a támogatás kérheti a korrelációs azonosítót. A támogatás a korrelációs azonosítóval azonosítja a sikertelen üzembe helyezés műveleteit.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Válassza ki a vizsgálni kívánt erőforráscsoportot.

1. Válassza az Üzemelő **példányok alatt található hivatkozást.**

   ![Üzembe helyezési előzmények kiválasztása](./media/deployment-history/select-deployment-history.png)

1. Válasszon ki egy üzemelő példányt az üzembe helyezési előzmények közül.

   ![Üzembe helyezés kiválasztása](./media/deployment-history/select-details.png)

1. Megjelenik az üzembe helyezés összegzése, beleértve a korrelációs azonosítót.

    ![A központi telepítés összegzése](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Egy erőforráscsoport összes üzemelő példányának listához használja a [Get-AzResourceGroupDeployment parancsot.](/powershell/module/az.resources/Get-AzResourceGroupDeployment)

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Ha egy adott üzembe helyezést szeretne lekért egy erőforráscsoportból, adja hozzá a **DeploymentName paramétert.**

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

A korrelációs azonosító lekért használhatja a következőt:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Egy erőforráscsoport üzemelő példányának listához használja [az az deployment group list listában.](/cli/azure/group/deployment#az_deployment_group_list)

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Egy adott üzemelő példány letelepítéséhez használja [az az deployment group show használhatja.](/cli/azure/group/deployment#az_deployment_group_show)

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

A korrelációs azonosító lekért használhatja a következőt:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Egy erőforráscsoport üzemelő példányának listához használja a következő műveletet. A kérelemben használható legújabb API-verziószámért lásd: Üzemelő példányok [– Lista erőforráscsoport szerint.](/rest/api/resources/deployments/listbyresourcegroup)

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Egy adott üzemelő példány lekért példánya. használja a következő műveletet. A kérelemben használható legújabb API-verziószámért lásd: [Üzemelő példányok – Lekérés.](/rest/api/resources/deployments/get)

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

## <a name="get-deployment-operations-and-error-message"></a>Üzembehely helyezési műveletek és hibaüzenet lekért

Minden üzemelő példány több műveletet is tartalmazhat. A telepítéssel kapcsolatos további részletekért tekintse meg az üzembe helyezési műveleteket. Ha egy üzembe helyezés meghiúsul, az üzembe helyezési műveletek hibaüzenetet tartalmaznak.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A központi telepítés összegzésében válassza a Művelet **részletei lehetőséget.**

    ![Művelet részleteinek kiválasztása](./media/deployment-history/get-operation-details.png)

1. Itt láthatja az üzembe helyezés lépésének részleteit. Hiba esetén a részletek között a hibaüzenet is szerepel.

    ![Művelet részleteinek megjelenítése](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoportban való üzembe helyezés üzembe helyezési műveleteinek megtekintéséhez használja a [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) parancsot.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

A sikertelen műveletek megtekintéséhez szűrje a Sikertelen **állapotot a műveletekre.**

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

A sikertelen műveletek állapotüzenetének lekért üzenetét a következő paranccsal használhatja:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az erőforráscsoportban való üzembe helyezés üzembe helyezési műveleteinek megtekintéséhez használja [az az deployment operation group list parancsot.](/cli/azure/deployment/operation/group#az_deployment-operation-group-list) Az Azure CLI 2.6.0-s vagy újabb használatával kell lennie.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

A sikertelen műveletek megtekintéséhez szűrje a Sikertelen **állapotot a műveletekre.**

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

A sikertelen műveletek állapotüzenetének lekért üzenetét a következő paranccsal használhatja:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Az üzembe helyezési műveletek lekért műveletéhez használja a következő műveletet. A kérelemben használt legújabb API-verziószámért lásd: Üzembe [helyezési műveletek – Lista.](/rest/api/resources/deploymentoperations/list)

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

A válasz tartalmaz egy hibaüzenetet.

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

* Az egyes üzembe helyezési hibák elhárításával kapcsolatos segítségért tekintse meg az Erőforrások Azure-ba való üzembe helyezésekor a következővel kapcsolatos [gyakori Azure Resource Manager.](common-deployment-errors.md)
* Az üzemelő példányok előzményeinek kezelése az Automatikus törlés az üzembe helyezési [előzményekből cikkből ismerkedik meg.](deployment-history-deletions.md)
* Az üzemelő példány a végrehajtása előtt való ellenőrzéséhez lásd: Erőforráscsoport üzembe helyezése [Azure Resource Manager sablonnal.](deploy-powershell.md)
