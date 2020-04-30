---
title: 'Rövid útmutató: Event hub létrehozása fogyasztói csoporttal – Azure Event Hubs'
description: 'Gyors útmutató: Event Hubs névtér létrehozása egy Event hub és egy fogyasztói csoport számára Azure Resource Manager sablonok használatával'
services: event-hubs
documentationcenter: .net
author: spelluru
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.custom: subject-armqs
ms.openlocfilehash: c2221fe5b5ab38afbdde167e5bcbf6b47ed4f861
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79528081"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Rövid útmutató: Event hub létrehozása Azure Resource Manager sablon használatával

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket. Ebben a rövid útmutatóban egy [Azure Resource Manager sablon](../azure-resource-manager/management/overview.md)használatával hoz létre egy Event hub-t. Egy Azure Resource Manager sablon üzembe helyezésével létrehozhat egy [Event Hubs](event-hubs-what-is-event-hubs.md)típusú névteret egy Event hub-val.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Nincs.

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-eventhubs-create-namespace-and-eventhub/)származik.

:::code language="json" source="~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json" range="1-61" highlight="32-59":::

A sablonban definiált erőforrások a következők:

- [**Microsoft. EventHub/névterek**](/azure/templates/microsoft.eventhub/namespaces)
- [**Microsoft. EventHub/névterek/eventhubs**](/azure/templates/microsoft.eventhub/namespaces/eventhubs)

További sablon-példákat az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular)talál.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezése:

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget, majd kövesse az utasításokat, hogy bejelentkezzen a Azure Cloud Shellba.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Az Event hub létrehozása néhány percet vesz igénybe.

1. A PowerShell-szkript másolásához válassza a **Másolás** lehetőséget.
1. Kattintson a jobb gombbal a rendszerhéj-konzolra, majd válassza a **Beillesztés**lehetőséget.

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

A központi telepítés ellenőrzéséhez megnyithatja az erőforráscsoportot a [Azure Portalból](https://portal.azure.com), vagy használhatja az alábbi Azure PowerShell parancsfájlt.  Ha a Cloud Shell továbbra is nyitva van, nem kell átmásolnia/futtatnia az első sort (olvasás-gazdagép).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Ha a Cloud Shell továbbra is nyitva van, nem kell átmásolnia/futtatnia az első sort (olvasás-gazdagép).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy Event Hubs névteret és egy Event hubot a névtérben. Az események küldése az Event hub-tól (vagy) események fogadására vonatkozó részletes utasításokért lásd a **küldési és fogadási események** oktatóanyagokat:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Ugrás](event-hubs-go-get-started-send.md)
- [C (csak küldés)](event-hubs-c-getstarted-send.md)
- [Apache Storm (csak fogadás)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
