---
title: Azure Service Bus-erőforrások létrehozása sablonok használatával
description: Service Bus erőforrások létrehozásának automatizálása Azure Resource Manager-sablonok használatával
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df8a7fde9114f03521f0e57e072f81a867efcf39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89075256"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Service Bus erőforrások létrehozása Azure Resource Manager sablonok használatával

Ez a cikk azt ismerteti, hogyan hozhatók létre és helyezhetők üzembe Service Bus erőforrások Azure Resource Manager sablonok, a PowerShell és a Service Bus erőforrás-szolgáltató használatával.

Azure Resource Manager sablonok segítségével meghatározhatja a megoldáshoz telepítendő erőforrásokat, valamint megadhatja azokat a paramétereket és változókat, amelyek lehetővé teszik a különböző környezetekben lévő értékek bevitelét. A sablon JSON-ban íródott, és olyan kifejezéseket tartalmaz, amelyekkel értékeket lehet létrehozni a központi telepítéshez. Azure Resource Manager sablonok írásához és a sablon formátumával kapcsolatos részletes információkért tekintse meg a [Azure Resource Manager sablonok szerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md)című témakört.

> [!NOTE]
> A jelen cikkben szereplő példák azt mutatják be, hogyan használható a Azure Resource Manager egy Service Bus névtér és üzenetküldési entitás (Üzenetsor) létrehozásához. További példákért tekintse meg az [Azure Gyorsindítás sablonok][Azure Quickstart Templates gallery] katalógusát, és keressen rá **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Resource Manager-sablonok Service Bus

Ezek a Service Bus Azure Resource Manager sablonok letölthetők és üzembe helyezésre is használhatók. A következő hivatkozásokra kattintva részletes információkat talál a GitHubon található sablonokra mutató hivatkozásokról:

* [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
* [Service Bus névtér létrehozása a várólistával](service-bus-resource-manager-namespace-queue.md)
* [Service Bus névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
* [Service Bus névtér létrehozása a várólista-és engedélyezési szabállyal](service-bus-resource-manager-namespace-auth-rule.md)
* [Service Bus névtér létrehozása témakörrel, előfizetéssel és szabállyal](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

Az alábbi eljárás azt ismerteti, hogyan használható a PowerShell egy olyan Azure Resource Manager-sablon üzembe helyezéséhez, amely standard szintű Service Bus névteret és a névtéren belüli várólistát hoz létre. Ez a példa a [Service Bus névtér létrehozása üzenetsor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) -sablonnal című részen alapul. A közelítő munkafolyamat a következő:

1. Telepítse a PowerShellt.
2. Hozza létre a sablont, és (opcionálisan) egy paraméter-fájlt.
3. A PowerShellben jelentkezzen be az Azure-fiókjába.
4. Hozzon létre egy új erőforráscsoportot, ha még nem létezik ilyen.
5. Tesztelje az üzemelő példányt.
6. Ha szükséges, állítsa be a telepítési módot.
7. A sablon üzembe helyezése.

Azure Resource Manager sablonok telepítésével kapcsolatos információkért lásd: [erőforrások üzembe helyezése Azure Resource Manager-sablonokkal][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>A PowerShell telepítése

Telepítse Azure PowerShell a [Azure PowerShell első lépéseinek](/powershell/azure/get-started-azureps)utasításait követve.

### <a name="create-a-template"></a>Sablon létrehozása

A tárház klónozása vagy a [201-servicebus-Create-várólista](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) sablon másolása a githubról:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Parameters fájl létrehozása (nem kötelező)

Ha nem kötelező paramétert szeretne használni, másolja a [201-servicebus-Create-üzenetsor](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) fájlt. Cserélje le az értéket az `serviceBusNamespaceName` ebben a központi telepítésben létrehozni kívánt Service Bus névtér nevére, és cserélje le a értéket a `serviceBusQueueName` létrehozni kívánt várólista nevére.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

További információkért lásd a [paramétereket](../azure-resource-manager/templates/parameter-files.md) ismertető cikket.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Jelentkezzen be az Azure-ba, és állítsa be az Azure-előfizetést

A PowerShell-parancssorból futtassa a következő parancsot:

```powershell
Connect-AzAccount
```

A rendszer felszólítja, hogy jelentkezzen be az Azure-fiókjába. A bejelentkezés után futtassa a következő parancsot az elérhető előfizetések megtekintéséhez:

```powershell
Get-AzSubscription
```

Ez a parancs az elérhető Azure-előfizetések listáját adja vissza. A következő parancs futtatásával válasszon egy előfizetést az aktuális munkamenethez. Cserélje le a helyére a `<YourSubscriptionId>` használni kívánt Azure-előfizetés GUID azonosítóját:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Az erőforráscsoport beállítása

Ha nem rendelkezik meglévő erőforráscsoporthoz, hozzon létre egy új erőforráscsoportot a **New-AzResourceGroup** paranccsal. Adja meg az erőforráscsoport nevét és a használni kívánt helyet. Például:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Ha ez sikeres, megjelenik az új erőforráscsoport összefoglalása.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

Ellenőrizze az üzemelő példányt a `Test-AzResourceGroupDeployment` parancsmag futtatásával. A központi telepítés tesztelésekor pontosan úgy adja meg a paramétereket, ahogy az üzemelő példány végrehajtásakor.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>A központi telepítés létrehozása

Az új központi telepítés létrehozásához futtassa a `New-AzResourceGroupDeployment` parancsmagot, és adja meg a szükséges paramétereket, ha a rendszer kéri. A paraméterek közé tartozik az üzemelő példány neve, az erőforráscsoport neve, valamint a sablonfájl elérési útja vagy URL-címe. Ha a **Mode** paraméter nincs megadva, a **növekményes** érték alapértelmezett értékét használja a rendszer. További információ: [növekményes és teljes telepítések](../azure-resource-manager/templates/deployment-modes.md).

A következő parancs a PowerShell-ablak három paraméterének megadását kéri:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Ha ehelyett a paramétereket szeretné megadni, használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

A telepítési parancsmag futtatásakor beágyazott paramétereket is használhat. A parancs formátuma a következő:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

A [teljes](../azure-resource-manager/templates/deployment-modes.md) telepítés futtatásához állítsa a **Mode** paramétert a **befejezéshez**:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése
Ha az erőforrások központi telepítése sikeresen megtörtént, a központi telepítés összegzése megjelenik a PowerShell-ablakban:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Következő lépések
Ekkor megtekintette az alapszintű munkafolyamatot és parancsokat Azure Resource Manager sablon üzembe helyezéséhez. Részletesebb információkért tekintse meg az alábbi hivatkozásokat:

* [Az Azure Resource Manager áttekintése][Azure Resource Manager overview]
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel][Deploy resources with Azure Resource Manager templates]
* [Azure Resource Manager-sablonok készítése](../azure-resource-manager/templates/template-syntax.md)
* [Microsoft. ServiceBus erőforrástípusok](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
