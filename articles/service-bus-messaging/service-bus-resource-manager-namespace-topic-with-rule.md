---
title: Service Bus témakör-előfizetés és-szabály létrehozása az Azure-sablon használatával
description: Service Bus névtér létrehozása témakörrel, előfizetéssel és szabállyal Azure Resource Manager sablon használatával
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3f3287dd67f89f678a9875ddce93e2d0d26b2209
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89077624"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Service Bus névtér létrehozása témakörrel, előfizetéssel és szabállyal Azure Resource Manager sablon használatával

Ez a cikk bemutatja, hogyan használható egy olyan Azure Resource Manager-sablon, amely egy Service Bus névteret hoz létre témakörrel, előfizetéssel és szabállyal (szűrővel). A cikk azt ismerteti, hogyan határozható meg, hogy mely erőforrások legyenek telepítve, és Hogyan határozható meg a központi telepítés végrehajtásakor megadott paraméterek. Ezt a sablont saját üzembe helyezési műveleteihez is használhatja, vagy akár igényeinek megfelelően testre is szabhatja

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál.

További információ az Azure-erőforrások elnevezési konvenciókkal kapcsolatos gyakorlatról és mintákról: [Az Azure-erőforrások ajánlott elnevezési konvenciói][Recommended naming conventions for Azure resources].

A teljes sablonhoz tekintse meg a következő témakört: [Service Bus névtér témakörrel, előfizetéssel és szabály][Service Bus namespace with topic, subscription, and rule] sablonnal.

> [!NOTE]
> A következő Azure Resource Manager sablonok tölthetők le és üzemelő példányban.
> 
> * [Service Bus névtér létrehozása a várólista-és engedélyezési szabállyal](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus névtér létrehozása a várólistával](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
> 
> A legújabb sablonok kereséséhez látogasson el az [Azure Gyorsindítás sablonok][Azure Quickstart Templates] galériába, és keressen rá Service Bus.

## <a name="what-do-you-deploy"></a>Mit telepít?

Ezzel a sablonnal Service Bus névteret helyezhet üzembe a témakör, az előfizetés és a szabály (Filter) alapján.

[Service Bus témakörök és előfizetések](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) egy-a-többhöz típusú kommunikációt biztosítanak egy *közzétételi/* előfizetési minta formájában. Témakörök és előfizetések használata esetén az elosztott alkalmazások összetevői nem kommunikálnak közvetlenül egymással, hanem olyan témakörön keresztül cserélnek üzeneteket, amely közvetítőként működik. A témakörre való előfizetés egy olyan virtuális várólistára hasonlít, amely a témakörbe küldött üzenetek másolatait fogadja. Az előfizetés szűrője lehetővé teszi annak megadását, hogy a témakörbe küldött üzenetek egy adott témakör-előfizetésen belül jelenjenek meg.

## <a name="what-are-rules-filters"></a>Mik a szabályok (szűrők)?

Számos esetben a konkrét tulajdonságokkal rendelkező üzeneteket különböző módokon kell feldolgozni. Az egyéni feldolgozás engedélyezéséhez az előfizetések konfigurálásával megkeresheti az adott tulajdonságokkal rendelkező üzeneteket, majd módosíthatja ezeket a tulajdonságokat. Habár Service Bus előfizetések a témakörbe küldött összes üzenetet láthatják, az üzenetek egy részhalmazát csak a virtuális előfizetési várólistára másolhatja. Az előfizetés-szűrők használatával valósítható meg. További információ a szabályokról (szűrők): [szabályok és műveletek](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

A Azure Resource Manager segítségével adja meg a sablon telepítésekor megadni kívánt értékek paramétereit. A sablonban található egy `Parameters` nevű rész, amely magába foglalja az összes paraméterértéket. Definiáljon egy paramétert azokhoz az értékekhez, amelyek a telepített projekttől függően változnak, vagy azon környezet alapján, amelyre telepíteni kívánja. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon a következő paramétereket definiálja:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

A létrehozandó Service Bus névtér neve.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

A Service Bus névtérben létrehozott témakör neve.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

A Service Bus névtérben létrehozott előfizetés neve.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusrulename"></a>serviceBusRuleName

A Service Bus névtérben létrehozott szabály (szűrő) neve.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

A sablon Service Bus API-verziója.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások

Létrehoz egy **üzenetküldés** típusú standard Service Bus névteret, amely témakört és előfizetést és szabályokat tartalmaz.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
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
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

A JSON szintaxisa és tulajdonságai esetében lásd: [névterek](/azure/templates/microsoft.servicebus/namespaces), [témakörök](/azure/templates/microsoft.servicebus/namespaces/topics), [előfizetések](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)és [szabályok](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kezelheti ezeket az erőforrásokat a következő cikkek megtekintésével:

* [Azure Service Bus kezelése](service-bus-management-libraries.md)
* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [Service Bus erőforrások kezelése a Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
