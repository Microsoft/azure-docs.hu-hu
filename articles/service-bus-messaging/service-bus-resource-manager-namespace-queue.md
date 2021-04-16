---
title: Új Azure Service Bus és üzenetsor létrehozása Azure-sablonnal
description: 'Rövid útmutató: Új Service Bus és üzenetsor létrehozása Azure Resource Manager sablon használatával'
documentationcenter: .net
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6577abf1900a166087ff63c331d7c0390fab1342
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535027"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>Rövid útmutató: Service Bus névtér és üzenetsor létrehozása ARM-sablonnal

Ez a cikk bemutatja, hogyan használható egy Azure Resource Manager sablon (ARM-sablon), amely létrehoz egy Service Bus névteret és egy üzenetsort a névtéren belül. A cikk bemutatja, hogyan adhatja meg, hogy mely erőforrásokat telepítse a rendszer, és hogyan határozhatja meg az üzembe helyezés végrehajtásakor megadott paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue) közül származik.

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json":::

A sablonban definiált erőforrások a következők:

- [**Microsoft.ServiceBus/névterek**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/névterek/üzenetsorok**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> A következő ARM-sablonok tölthetők le és üzembe helyezést érhetők el.
>
> * [Hozzon létre egy Service Bus-névteret az üzenetsor és az engedélyezési szabály segítségével](service-bus-resource-manager-namespace-auth-rule.md)
> * [Témakört és előfizetést Service Bus névtér létrehozása](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Témakört, előfizetést Service Bus és sémával együtt hozzon létre egy új névteret](service-bus-resource-manager-namespace-topic-with-rule.md)

További sablonokat az [Azure gyorsindítási sablonok között találhat](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Ezzel a sablonnal üzembe helyez egy Service Bus-névteret egy üzenetsokkal.

[Service Bus üzenetsorok](service-bus-queues-topics-subscriptions.md#queues) elsőnek be, elsőnek ki (First In, First Out, FIFO) üzenetet kínálnak egy vagy több versengő fogyasztónak.

Az üzembe helyezés automatikus futtatásához kattintson a következő gombra: Hozzon létre egy új erőforráscsoportot az üzemelő példányhoz, hogy később könnyen eltávolítható legyen.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Válassza **a felül található** Értesítések lehetőséget az üzembe helyezés állapotának megtekintése érdekében. Várjon, amíg az üzembe helyezés sikeres lesz. Ezután az **értesítési üzenetben** válassza az Ugrás az erőforráscsoportra lehetőséget az erőforráscsoport oldalának a névteret tartalmazó Service Bus meg. 

    ![Üzembe helyezésről szóló értesítés](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Győződjön meg arról, hogy Service Bus a névtér az erőforrások listájában. 

    ![Erőforráscsoport – névtér](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Válassza ki a névteret a listából a **névtér Service Bus lapját.** 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. A Azure Portal lépjen az **erőforráscsoport** Erőforráscsoport lapjára.
2. Válassza az **Erőforráscsoport törlése** elemet az eszköztáron. 
3. Írja be az erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.** 

    ![Erőforráscsoport – törlés](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő témakört, amely bemutatja, hogyan hozhat létre engedélyezési szabályt a névtérhez/üzenetsorhoz:

[Új Service Bus engedélyezési szabály létrehozása névtérhez és üzenetsorhoz ARM-sablon használatával](service-bus-resource-manager-namespace-auth-rule.md)

Az alábbi cikkekből megtudhatja, hogyan kezelheti ezeket az erőforrásokat:

* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [A Service Bus erőforrások kezelése a Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
