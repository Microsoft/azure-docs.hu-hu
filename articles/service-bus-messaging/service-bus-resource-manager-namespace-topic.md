---
title: Azure Service Bus témakör létrehozása sablon használatával
description: 'Rövid útmutató: Témakört és előfizetést Service Bus névtér létrehozása Azure Resource Manager sablon használatával'
documentationcenter: .net
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.custom:
- devx-track-azurecli
- devx-track-azurepowershell
- mode-arm
ms.openlocfilehash: 9c235f8e056bf185267aa0bb493b71b897f3c510
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534913"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Rövid útmutató: Témakört Service Bus előfizetést használó új névtér létrehozása Azure Resource Manager sablon használatával

Ez a cikk bemutatja, hogyan használható egy Azure Resource Manager sablon, amely létrehoz egy Service Bus névteret, valamint egy témakört és előfizetést a névtéren belül. A cikk bemutatja, hogyan adhatja meg, hogy mely erőforrásokat kell üzembe helyezni, és hogyan határozhatja meg az üzembe helyezés végrehajtásakor megadott paramétereket. Ezt a sablont saját üzembe helyezési műveleteihez is használhatja, vagy akár igényeinek megfelelően testre is szabhatja

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál.

A teljes sablont lásd a Service Bus [témakört és][Service Bus namespace with topic and subscription] előfizetési sablont is tartalmaz.

> [!NOTE]
> A következő Azure Resource Manager sablonokat töltheti le és üzembe is használhatja.
> 
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Új névtér Service Bus üzenetsorsal](service-bus-resource-manager-namespace-queue.md)
> * [Hozzon létre egy Service Bus-névteret az üzenetsor és az engedélyezési szabály segítségével](service-bus-resource-manager-namespace-auth-rule.md)
> * [Témakört, előfizetést Service Bus és sémával együtt hozzon létre egy új névteret](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> A legújabb sablonok kereséséhez keresse fel az [Azure gyorsindítási][Azure Quickstart Templates] sablonok katalógusát, és keressen rá a **Service Bus.**

## <a name="what-do-you-deploy"></a>Mit helyez üzembe?

Ezzel a sablonnal egy témakört és előfizetést Service Bus névteret fog üzembe helyezni.

[Service Bus témakörök](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) és előfizetések egy-a-többhez típusú kommunikációt biztosítanak *közzétételi/előfizetési mintában.*

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz egy nevű `Parameters` szakaszt, amely az összes paraméterértéket tartalmazza. Definiálhat egy paramétert ezekhez az értékekhez, amelyek az üzembe helyezett projekttől vagy az üzembe helyezési környezettől függően változnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon a következő paramétereket határozza meg:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

A létrehoz Service Bus névtér neve.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Az új névtérben létrehozott Service Bus neve.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Az új névtérben létrehozott Service Bus neve.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

A Service Bus API-verziója.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások

Létrehoz egy üzenetkezelés Service Bus típusú standard névteret témakör és előfizetés típussal.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

A JSON-szintaxissal és -tulajdonságokkal kapcsolatban lásd a [névtereket,](/azure/templates/microsoft.servicebus/namespaces) [témaköröket](/azure/templates/microsoft.servicebus/namespaces/topics)és [előfizetéseket.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az deployment group create \<my-resource-group\> --name \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozott és üzembe helyezett erőforrásokat a Azure Resource Manager, az alábbi cikkekből megtudhatja, hogyan kezelheti ezeket az erőforrásokat:

* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [A Service Bus erőforrások kezelése a Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
