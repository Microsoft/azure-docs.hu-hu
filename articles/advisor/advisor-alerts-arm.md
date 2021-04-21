---
title: Új Azure Advisor riasztások létrehozása Resource Manager sablon használatával
description: Megtudhatja, hogyan állíthat be riasztást új javaslatokhoz a Azure Advisor sablonnal Azure Resource Manager (ARM-sablonnal).
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 06/29/2020
ms.openlocfilehash: 716ab104ce6517aeb554b42522e5906829877259
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765665"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Rövid útmutató: Azure Advisor riasztások létrehozása új javaslatokhoz ARM-sablon használatával

Ez a cikk bemutatja, hogyan állíthat be riasztást új javaslatokhoz a Azure Advisor sablonnal Azure Resource Manager (ARM-sablonnal).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Amikor Azure Advisor erőforrásra vonatkozó új javaslatot észlel, a rendszer egy eseményt tárol az [Azure-tevékenységnaplóban.](../azure-monitor/essentials/platform-logs-overview.md) Ezekhez az eseményekhez riasztásokat állíthat be a Azure Advisor egy javaslatspecifikus riasztás-létrehozási folyamattal. Kiválaszthat egy előfizetést és igény szerint egy erőforráscsoportot is azon erőforrások megadásához, amelyekről riasztásokat szeretne kapni.

A javaslatok típusait az alábbi tulajdonságokkal is meghatározhatja:

- Kategória
- Hatásszint
- Javaslat típusa

Azt a műveletet is konfigurálhatja, amely akkor történik, ha a riasztást a következő aktiválja:

- Meglévő műveletcsoport kiválasztása
- Új műveletcsoport létrehozása

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/alerts/action-groups.md) szóló cikkben talál.

> [!NOTE]
> Az Advisor-riasztások jelenleg csak a magas rendelkezésre állásra, teljesítményre és költségre vonatkozó javaslatokhoz érhetők el. A biztonsági javaslatok nem támogatottak.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- A parancsok helyi számítógépről való futtatásához telepítse az Azure CLI-t vagy a Azure PowerShell modulokat. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Azure PowerShell.](/powershell/azure/install-az-ps)

## <a name="review-the-template"></a>A sablon áttekintése

Az alábbi sablon létrehoz egy e-mail-célként megadott műveletcsoportot, és engedélyezi a szolgáltatás állapotával kapcsolatos összes értesítést a cél-előfizetéshez. Mentse ezt a sablont *CreateAdvisorAlert.jsfájlként a következőn:*.

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

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezése [](../azure-resource-manager/templates/deploy-portal.md) bármilyen standard módszerrel arm-sablon üzembe helyezéséhez, például az alábbi példákkal a parancssori felület és a PowerShell használatával. Cserélje le a erőforráscsoport és az **emailAddress** mintaértékét a környezetének megfelelő értékekre. A munkaterület nevének egyedinek kell lennie az összes Azure-előfizetés között.

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

Az alábbi parancsok egyikével ellenőrizze, hogy a munkaterület létrejött-e. Cserélje le az Erőforráscsoport **mintaértékeket** a korábban használt értékre.

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

Ha azt tervezi, hogy az ezt követő rövid útmutatóval és oktatóanyagokkal dolgozik tovább, érdemes ezeket az erőforrásokat a helyén hagyni. Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli a riasztási szabályt és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell

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

- Áttekintheti [a tevékenységnapló-riasztásokat,](../azure-monitor/alerts/alerts-overview.md)és megtudhatja, hogyan kaphat riasztásokat.
- További információ a [műveletcsoportokról:](../azure-monitor/alerts/action-groups.md).
