---
title: Tevékenységnapló-riasztások fogadása Azure-szolgáltatásértesítések esetén Resource Manager sablon használatával
description: Értesítést kap SMS-ben, e-mailben vagy webhookon keresztül az Azure-szolgáltatás bekövetkeztével.
ms.date: 06/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 730c023de61275d95fe594642149770af42b34b9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535756"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Rövid útmutató: Tevékenységnapló-riasztások létrehozása szolgáltatásértesítések esetén ARM-sablon használatával

Ez a cikk bemutatja, hogyan állíthat be tevékenységnapló-riasztásokat a szolgáltatás állapotával kapcsolatos értesítésekhez egy Azure Resource Manager (ARM-sablon) használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

A szolgáltatás állapotértesítései az [Azure-tevékenységnaplóban vannak tárolva.](../azure-monitor/essentials/platform-logs-overview.md) Mivel valószínűleg nagy mennyiségű információ található a tevékenységnaplóban, egy külön felhasználói felület áll rendelkezésre, amely megkönnyíti a szolgáltatás állapotértesítésére vonatkozó riasztások megtekintését és beállítását.

Riasztást kaphat, ha az Azure szolgáltatás állapotértesítéseket küld az Azure-előfizetésének. A riasztást a következő alapján konfigurálhatja:

- A szolgáltatás állapotával kapcsolatos értesítések osztálya (szolgáltatási problémák, tervezett karbantartás, állapot-tanácsadások).
- Az érintett előfizetés.
- Az érintett szolgáltatás(k).
- Az érintett régió(k).

> [!NOTE]
> A szolgáltatás állapotával kapcsolatos értesítések nem küldenek riasztást az erőforrás állapoteseményeiről.

Azt is beállíthatja, hogy kinek legyen elküldve a riasztás:

- Válasszon ki egy meglévő műveletcsoportot.
- Hozzon létre egy új műveletcsoportot (amely a jövőbeli riasztások létrehozására használható).

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/alerts/action-groups.md) szóló cikkben talál.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- A parancsok helyi számítógépről való futtatásához telepítse az Azure CLI-t vagy a Azure PowerShell modulokat. További információ: [Az Azure CLI](/cli/azure/install-azure-cli) telepítése és [telepítés Azure PowerShell.](/powershell/azure/install-az-ps)

## <a name="review-the-template"></a>A sablon áttekintése

A következő sablon létrehoz egy műveletcsoportot egy e-mail-célértékekkel, és engedélyezi a szolgáltatás állapotával kapcsolatos összes értesítést a cél-előfizetéshez. Mentse ezt a sablont *CreateServiceHealthAlert.jsfájlként a következőn:*.

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

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezése [](../azure-resource-manager/templates/deploy-portal.md) bármilyen standard módszerrel arm-sablon üzembe helyezéséhez, például az alábbi példákkal a parancssori felület és a PowerShell használatával. Cserélje le az **Erőforráscsoport** és az **emailAddress mintaértékeket** a környezetének megfelelő értékekre.

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

Az alábbi parancsok egyikével ellenőrizze, hogy a munkaterület létrejött-e. Cserélje le az Erőforráscsoport **mintaértékeket** a korábban használt értékre.

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

- Ismerje meg [a riasztások beállításának ajánlott Azure Service Health eljárásokat.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)
- Ismerje meg, hogyan [lehet mobil leküldéses értesítéseket beállítani a Azure Service Health.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)
- Ismerje meg, [hogyan konfigurálhatja a webhook-értesítéseket a meglévő problémakezelő rendszerekhez.](service-health-alert-webhook-guide.md)
- Tudnivalók a [szolgáltatás állapotértesítéseiről.](service-notifications.md)
- További információ az [értesítési sebesség korlátozásról.](../azure-monitor/alerts/alerts-rate-limiting.md)
- Tekintse át [a tevékenységnapló-riasztás webhooksémát.](../azure-monitor/alerts/activity-log-alerts-webhook.md)
- Áttekintheti [a tevékenységnapló-riasztásokat,](../azure-monitor/alerts/alerts-overview.md)és megtudhatja, hogyan kaphat riasztásokat.
- További információ a [műveletcsoportokról:](../azure-monitor/alerts/action-groups.md).
