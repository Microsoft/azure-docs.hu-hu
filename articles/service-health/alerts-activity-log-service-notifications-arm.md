---
title: Eseménynapló-riasztások fogadása Azure-szolgáltatásbeli értesítésekhez Resource Manager-sablon használatával
description: Értesítés küldése SMS-ben, e-mailben vagy webhookon az Azure-szolgáltatás bekövetkeztekor.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 532fbae505e0bcaa6ab31a2e935362114537d134
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594956"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Gyors útmutató: műveletnapló-riasztások létrehozása a szolgáltatási értesítésekben ARM-sablon használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be a Azure Resource Manager sablon (ARM-sablon) használatával a szolgáltatás állapotával kapcsolatos értesítéseket a tevékenység naplójában.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

A szolgáltatás állapotával kapcsolatos értesítéseket az [Azure-tevékenység naplójában](../azure-monitor/essentials/platform-logs-overview.md)tárolja a rendszer. A tevékenység naplójában tárolt, valószínűleg nagy mennyiségű információ miatt külön felhasználói felület van, amely megkönnyíti a riasztások megtekintését és beállítását a szolgáltatás állapotára vonatkozó értesítésekben.

Riasztást kaphat, ha az Azure szolgáltatás-állapotra vonatkozó értesítéseket küld az Azure-előfizetésre. A riasztást a következő alapján állíthatja be:

- A szolgáltatás állapotáról szóló értesítés (szolgáltatási problémák, tervezett karbantartás, egészségügyi tanácsadók) osztálya.
- Az érintett előfizetés.
- Az érintett szolgáltatás (ok).
- Az érintett régió (k).

> [!NOTE]
> A szolgáltatás állapotára vonatkozó értesítések nem küldenek riasztást az erőforrás-állapottal kapcsolatos eseményekről.

Azt is beállíthatja, hogy a rendszer kik számára küldje a riasztást:

- Válasszon ki egy meglévő műveleti csoportot.
- Hozzon létre egy új műveleti csoportot (amely a jövőbeli riasztásokhoz használható).

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/alerts/action-groups.md) szóló cikkben talál.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- A parancsok helyi számítógépről való futtatásához telepítse az Azure CLI-t vagy a Azure PowerShell modulokat. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Azure PowerShell telepítése](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>A sablon áttekintése

A következő sablon egy e-mail-célként létrehozott műveleti csoportot hoz létre, és lehetővé teszi az összes szolgáltatás állapotának értesítését a cél előfizetéshez. Mentse ezt a sablont *CreateServiceHealthAlert.jsként*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
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
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
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
      }
    }
  ]
}
```

A sablon két erőforrást határoz meg:

- [Microsoft. bepillantások/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. bepillantások/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablont bármely szabványos módszer használatával üzembe helyezheti [egy ARM-sablon üzembe helyezéséhez](../azure-resource-manager/templates/deploy-portal.md) , például a parancssori felület és a PowerShell használatával. Cserélje le az **erőforráscsoport** és az **emailAddress** minta értékeit a környezete megfelelő értékeire.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Győződjön meg arról, hogy a munkaterület az alábbi parancsok egyikével lett létrehozva. Cserélje le az **erőforráscsoport** mintavételi értékeit a fent használt értékre.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
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

- További információ [a Azure Service Health riasztások beállításával kapcsolatos ajánlott eljárásokról](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Ismerje meg, hogyan állíthat be [Azure Service Health Mobile leküldéses értesítéseket](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Megtudhatja, hogyan [konfigurálhat webhook-értesítéseket a meglévő probléma-felügyeleti rendszerekhez](service-health-alert-webhook-guide.md).
- Tudnivalók a [szolgáltatás állapotával kapcsolatos értesítésekről](service-notifications.md).
- Tudnivalók az [értesítési ráta korlátozásáról](../azure-monitor/alerts/alerts-rate-limiting.md).
- Tekintse át a [tevékenység naplójának riasztása webhook sémáját](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- [Tekintse át a tevékenységek naplójának riasztásait](../azure-monitor/alerts/alerts-overview.md), és Ismerje meg, hogyan fogadhat riasztásokat.
- További információ a [műveleti csoportokról](../azure-monitor/alerts/action-groups.md).
