---
title: Azure Advisor riasztások létrehozása az új javaslatokhoz Resource Manager-sablon használatával
description: Megtudhatja, hogyan állíthatja be a Azure Advisor új javaslataira vonatkozó riasztásokat egy Azure Resource Manager sablon (ARM-sablon) használatával.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 7b10ad06e5397ab733987373a3bd18de981c8c97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590050"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Rövid útmutató: Azure Advisor riasztások létrehozása új javaslatokhoz ARM-sablon használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be a Azure Advisor új javaslataira vonatkozó riasztásokat egy Azure Resource Manager sablon (ARM-sablon) használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha Azure Advisor észleli az egyik erőforrásra vonatkozó új javaslatot, egy eseményt az [Azure-tevékenység naplójában](../azure-monitor/essentials/platform-logs-overview.md)tárol a rendszer. Az eseményekre vonatkozó riasztásokat a Azure Advisor-specifikus riasztások létrehozási felületének használatával állíthatja be. Kiválaszthat egy előfizetést, és opcionálisan egy erőforráscsoportot is megadhatja azokat az erőforrásokat, amelyekről riasztást szeretne kapni.

A következő tulajdonságokkal is meghatározhatja a javaslatok típusait:

- Kategória
- Hatás szintje
- Javaslat típusa

Azt is beállíthatja, hogy milyen műveletet hajtson végre a rendszer a riasztás indításakor:

- Meglévő műveleti csoport kiválasztása
- Új műveleti csoport létrehozása

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/alerts/action-groups.md) szóló cikkben talál.

> [!NOTE]
> Az Advisor-riasztások jelenleg csak a magas rendelkezésre állás, a teljesítmény és a Cost-javaslatok esetében érhetők el. A biztonsági javaslatok nem támogatottak.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- A parancsok helyi számítógépről való futtatásához telepítse az Azure CLI-t vagy a Azure PowerShell modulokat. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Azure PowerShell telepítése](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>A sablon áttekintése

A következő sablon egy e-mail-célként létrehozott műveleti csoportot hoz létre, és lehetővé teszi az összes szolgáltatás állapotának értesítését a cél előfizetéshez. Mentse ezt a sablont *CreateAdvisorAlert.jsként*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

A sablon két erőforrást határoz meg:

- [Microsoft. bepillantások/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. bepillantások/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablont bármely szabványos módszer használatával üzembe helyezheti [egy ARM-sablon üzembe helyezéséhez](../azure-resource-manager/templates/deploy-portal.md) , például a parancssori felület és a PowerShell használatával. Cserélje le az **erőforráscsoport** mintáit, és az **emailAddress** értéket a környezete megfelelő értékeivel. A munkaterület nevének egyedinek kell lennie az összes Azure-előfizetés között.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Győződjön meg arról, hogy a munkaterület az alábbi parancsok egyikével lett létrehozva. Cserélje le az **erőforráscsoport** mintavételi értékeit a fent használt értékre.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, érdemes lehet ezeket az erőforrásokat helyben hagyni. Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli a riasztási szabályt és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell használatával

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>További lépések

- [Tekintse át a tevékenységek naplójának riasztásait](../azure-monitor/alerts/alerts-overview.md), és Ismerje meg, hogyan fogadhat riasztásokat.
- További információ a [műveleti csoportokról](../azure-monitor/alerts/action-groups.md).
