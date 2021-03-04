---
title: Azure-tevékenység naplójának küldése Log Analytics munkaterületre Azure Resource Manager sablon használatával
description: ARM-sablonokkal létrehozhat egy Log Analytics munkaterületet és egy diagnosztikai beállítást, amellyel elküldheti a tevékenység naplóját Azure Monitor naplókba.
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 56810cffcb9665810c452276be34e6924fd992b2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033279"
---
# <a name="quickstart-send-azure-activity-log-to-log-analytics-workspace-using-an-arm-template"></a>Gyors útmutató: Azure-beli tevékenység naplójának küldése Log Analytics munkaterületre ARM-sablon használatával

A Tevékenységnapló az Azure előfizetési szintű eseményekkel kapcsolatos megállapításokat biztosító platformnaplója. Olyan információkat tartalmaz, mint az erőforrások módosításának vagy a virtuális gépek indításának az időpontja. Megtekintheti a tevékenység naplóját a Azure Portal vagy beolvashatja a bejegyzéseket a PowerShell és a parancssori felület használatával. Ez a rövid útmutató azt ismerteti, hogyan használhatók Azure Resource Manager sablonok (ARM-sablonok) egy Log Analytics-munkaterület létrehozásához és egy diagnosztikai beállításhoz, amely a tevékenység naplóját Azure Monitor naplókba küldi, ahol elemezheti a [napló lekérdezésekkel](../logs/log-query-overview.md) , és más funkciókat is engedélyezhet, például a [naplózási riasztásokat](../alerts/alerts-log-query.md) és a [munkafüzeteket](../visualize/workbooks-overview.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- A parancsok helyi számítógépről való futtatásához telepítse az Azure CLI-t vagy a Azure PowerShell modulokat. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Azure PowerShell telepítése](/powershell/azure/install-az-ps).

## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

A következő sablon egy üres Log Analytics munkaterületet hoz létre. Mentse ezt a sablont *CreateWorkspace.jsként*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the workspace."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "pergb2018",
      "allowedValues": [
        "pergb2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral",
        "australiaeast",
        "australiasoutheast",
        "brazilsouth",
        "canadacentral",
        "centralindia",
        "centralus",
        "eastasia",
        "eastus",
        "eastus2",
        "francecentral",
        "japaneast",
        "koreacentral",
        "northcentralus",
        "northeurope",
        "southafricanorth",
        "southcentralus",
        "southeastasia",
        "switzerlandnorth",
        "switzerlandwest",
        "uksouth",
        "ukwest",
        "westcentralus",
        "westeurope",
        "westus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for the workspace."
      }
    },
    "retentionInDays": {
      "type": "int",
      "defaultValue": 120,
      "metadata": {
        "description": "Number of days to retain data."
      }
    },
    "resourcePermissions": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "true to use resource or workspace permissions. false to require workspace permissions."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "name": "[parameters('sku')]"
        },
        "retentionInDays": "[parameters('retentionInDays')]",
        "features": {
          "searchVersion": 1,
          "legacy": 0,
          "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
        }
      }
    }
  ]
}
```

Ez a sablon egy erőforrást definiál:

- [Microsoft. OperationalInsights/munkaterületek](/azure/templates/microsoft.operationalinsights/workspaces)

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablont bármely szabványos módszer használatával üzembe helyezheti [egy ARM-sablon üzembe helyezéséhez](../../azure-resource-manager/templates/deploy-portal.md) , például a parancssori felület és a PowerShell használatával. Cserélje le az **erőforráscsoport**, a **workspaceName** és a **hely** minta értékeit a környezetének megfelelő értékekkel. A munkaterület nevének egyedinek kell lennie az összes Azure-előfizetés között.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az login
az deployment group create \
    --name CreateWorkspace \
    --resource-group my-resource-group \
    --template-file CreateWorkspace.json \
    --parameters workspaceName='my-workspace-01' location='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile CreateWorkspace.json -workspaceName my-workspace-01 -location eastus
```

---

### <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Győződjön meg arról, hogy a munkaterület az alábbi parancsok egyikével lett létrehozva. Cserélje le az **erőforráscsoport** és a **workspaceName** minta értékeit a fent használt értékekre.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az monitor log-analytics workspace show --resource-group my-workspace-01 --workspace-name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzOperationalInsightsWorkspace -Name my-workspace-01 -ResourceGroupName my-resource-group
```

---

## <a name="create-diagnostic-setting"></a>Diagnosztikai beállítások létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

A következő sablon egy diagnosztikai beállítást hoz létre, amely elküldi a tevékenység naplóját egy Log Analytics munkaterületre. Mentse ezt a sablont *CreateDiagnosticSetting.jsként*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "settingName": {
        "type": "String"
    },
    "workspaceId": {
        "type": "String"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[parameters('settingName')]",
      "dependsOn": [],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "logs": [
          {
          "category": "Administrative",
          "enabled": true
          },
          {
          "category": "Alert",
          "enabled": true
          },
          {
          "category": "Autoscale",
          "enabled": true
          },
          {
          "category": "Policy",
          "enabled": true
          },
          {
          "category": "Recommendation",
          "enabled": true
          },
          {
          "category": "ResourceHealth",
          "enabled": true
          },
          {
          "category": "Security",
          "enabled": true
          },
          {
          "category": "ServiceHealth",
          "enabled": true
          }
        ]
      }
    }
  ]
}
```

Ez a sablon egy erőforrást definiál:

- [Microsoft. bepillantások/diagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablont bármely szabványos módszer használatával üzembe helyezheti [egy ARM-sablon üzembe helyezéséhez](../../azure-resource-manager/templates/deploy-portal.md) , például a parancssori felület és a PowerShell használatával. Cserélje le az **erőforráscsoport**, a **workspaceName** és a **hely** minta értékeit a környezetének megfelelő értékekkel. A munkaterület nevének egyedinek kell lennie az összes Azure-előfizetés között.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az deployment sub create --name CreateDiagnosticSetting --location eastus --template-file CreateDiagnosticSetting.json --parameters settingName='Send Activity log to workspace' workspaceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzSubscriptionDeployment -Name CreateDiagnosticSetting -location eastus -TemplateFile CreateDiagnosticSetting.json -settingName "Send Activity log to workspace" -workspaceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01"
```
---

### <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Győződjön meg arról, hogy a diagnosztikai beállítás az alábbi parancsok egyikével lett létrehozva. Cserélje le a minta értékeit az előfizetésre, és állítsa a nevet a fent használt értékekre.

> [!NOTE]
> Jelenleg nem lehet beolvasni az előfizetési szint diagnosztikai beállításait a PowerShell használatával.

```azurecli
az monitor diagnostic-settings show --resource '/subscriptions/00000000-0000-0000-0000-000000000000' --name 'Send Activity log to workspace'
```

## <a name="generate-log-data"></a>Naplófájlok előállítása

A rendszer csak az Log Analytics munkaterületre küldi el az új tevékenységeket, ezért olyan műveleteket hajt végre az előfizetésben, amelyek naplózásra kerülnek, például egy virtuális gép elindításához vagy leállításához, illetve egy másik erőforrás létrehozásához vagy módosításához. Előfordulhat, hogy néhány percet várnia kell, hogy a rendszer létrehozza a diagnosztikai beállítást, és hogy először a munkaterületre írja be az adatgyűjtést. A késleltetést követően a rendszer a tevékenység naplójába írt összes eseményt néhány másodpercen belül elküldi a munkaterületre.

## <a name="retrieve-data-with-a-log-query"></a>Az adatlekérdezés naplózási lekérdezéssel

Az adatok munkaterületről való lekéréséhez használja a Azure Portal a Log Analytics használatával. A Azure Portal keresse meg, majd válassza a **figyelő** elemet.

![Azure Portal](media/quick-collect-activity-log/azure-portal-monitor.png)

A **Azure monitor** menüben válassza a **naplók** lehetőséget. A **példákat tartalmazó lekérdezések** oldalának lezárása. Ha a hatókör nem a létrehozott munkaterületre van beállítva, kattintson a **hatókör kiválasztása** lehetőségre, és keresse meg.

![Log Analytics hatókör](media/quick-collect-activity-log/log-analytics-scope.png)

A lekérdezési ablakban írja be a parancsot, `AzureActivity` majd kattintson a **Futtatás** gombra. Ez egy egyszerű lekérdezés, amely visszaadja a *AzureActivity* tábla összes rekordját, amely a tevékenység naplójából elküldett összes rekordot tartalmazza.

![Egyszerű lekérdezés](media/quick-collect-activity-log/query-01.png)

Bontsa ki az egyik rekordot a részletes tulajdonságainak megtekintéséhez.

![Tulajdonságok kibontása](media/quick-collect-activity-log/expand-properties.png)

Összetettebb lekérdezéseket is kipróbálhat, például `AzureActivity | summarize count() by CategoryValue` a kategória szerint összefoglalt események számát.

![Összetett lekérdezés](media/quick-collect-activity-log/query-02.png)

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

Ebben a rövid útmutatóban úgy konfigurálta a tevékenység naplóját, hogy az Log Analytics munkaterületre legyen küldve. Mostantól más adatokat is konfigurálhat a munkaterületre, ahol elemezheti azt a Azure Monitor található [naplók](../logs/log-query-overview.md) használatával, és olyan funkciókat használhat, mint például a [naplózási riasztások](../alerts/alerts-log-query.md) és a [munkafüzetek](../visualize/workbooks-overview.md). A következő lépésekkel gyűjtsön [erőforrás-naplókat](../essentials/resource-logs.md) az Azure-erőforrásokból, amelyek a tevékenység naplójában lévő adatokat szolgáltatva betekintést nyújtanak az egyes erőforrásokon végrehajtott műveletekre.

> [!div class="nextstepaction"]
> [Erőforrás-naplók összegyűjtése és elemzése Azure Monitor](../essentials/tutorial-resource-logs.md)