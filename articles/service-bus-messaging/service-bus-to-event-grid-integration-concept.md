---
title: Az Azure Service Bus – Event Grid integráció áttekintése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan integrálható az Azure Service Bus üzenetkezelés a Azure Event Gridsal.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658107bb74396891c8e6e05a9e8074a9416a5f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369662"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Az Azure Service Bus – Azure Event Grid integráció áttekintése
A Service Bus mostantól az Event Gridre bocsáthatja ki az eseményeket, amikor üzenetek találhatók egy üzenetsorban vagy előfizetésben, és nincsenek jelen fogadók. Létrehozhat Event Grid-előfizetéseket a Service Bus-névterekhez, megfigyelheti ezeket az eseményeket, és egy fogadó indításával reagálhat rájuk. Ezzel a funkcióval a Service Bus reaktív programozási modellekben használható. A funkció által nyújtott legfontosabb előny, hogy a kevés üzenettel rendelkező Service Bus üzenetsoroknak vagy előfizetéseknek nem kell olyan fogadóval rendelkezniük, amely folyamatosan üzeneteket kérdez le. 

A funkció engedélyezéséhez a következőkre van szüksége:

* Egy prémium szintű Service Bus-névtér legalább egy Service Bus-üzenetsorral, vagy pedig egy legalább egy előfizetéssel rendelkező Service Bus-témakör.
* Közreműködői hozzáférés a Service Bus-névtérhez. Keresse meg a Service Bus névteret a Azure Portalban, majd válassza a **hozzáférés-vezérlés (iam)** lehetőséget, és válassza a **szerepkör-hozzárendelések** lapot. Győződjön meg arról, hogy a közreműködői hozzáférése van a névtérhez. 
* Ezenkívül szükség van egy Event Grid-előfizetésre a Service Bus-névtérhez. Ez az előfizetés értesítést kap az Event Gridtől, ha üzeneteket kell átvenni. Tipikus előfizetők lehetnek az Azure App Service Logic Apps funkciója, az Azure Functions vagy egy webalkalmazáshoz csatlakozó webhook. Az üzeneteket ezután az előfizető dolgozza fel. 

![19][]

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Service Bus névterek Event Grid előfizetések
Három különböző módon hozhatók létre Event Grid-előfizetések a Service Bus-névterekhez.

- Azure Portal. Az alábbi oktatóanyagokból megtudhatja, hogyan hozhat létre Event Grid-előfizetéseket Service Bus eseményekhez Azure Logic Apps és Azure Functions as kezelők Azure Portal használatával. 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Azure CLI. A következő CLI-példa bemutatja, hogyan hozhat létre Azure Functions-előfizetést egy Service Bus-névtér által létrehozott [rendszertémakörhöz](../event-grid/system-topics.md) .

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell. Bemutatunk egy példát:
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>Hány eseményt bocsát ki és milyen gyakran?

Ha több üzenetsora és témaköre / előfizetése található a névtéren, üzenetsoronként vagy előfizetésenként legalább egy eseményt kap. Az események kibocsátása azonnal megtörténik, ha nincsenek üzenetek a Service Bus entitásban, és új üzenet érkezik. Vagy pedig két percenként, ha az Azure Service Bus nem észlel aktív fogadót. Az üzenetek közötti böngészés nem szakítja meg az eseményeket.

Alapértelmezés szerint a Service Bus a névtérben lévő összes entitáshoz eseményeket bocsát ki. Ha csak adott entitásokhoz szeretne eseményeket kapni, tekintse meg a következő szakaszt.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Az események forrásainak korlátozása szűrők használatával

Ha a névtérben például csak egy üzenetsor vagy egy előfizetés eseményeit szeretné megkapni, az Event Grid *Kezdete* és *Vége* szűrőit használhatja. Egyes interfészek esetén a szűrők *Előtag* és *Utótag* néven szerepelnek. Ha több üzenetsorból és előfizetésből, de nem az összesből szeretne eseményeket kapni, létrehozhat több különböző Event Grid-előfizetést, és mindegyikhez megadhat egy-egy szűrőt.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi oktatóanyagokat: 
- [Azure Logic Apps a Event Grid-on keresztül fogadott Service Bus üzenetek kezelésére](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Azure Functions a Event Grid-on keresztül fogadott Service Bus üzenetek kezelésére](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
