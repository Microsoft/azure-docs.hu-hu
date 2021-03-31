---
title: Azure Resource Manager-sablonminták – Event Grid | Microsoft Docs
description: Ez a cikk a GitHub-Azure Event Grid Azure Resource Manager sablonjának mintáit sorolja fel.
ms.topic: sample
ms.date: 07/07/2020
ms.openlocfilehash: 910012adf2dc930e6f1a26f1a7fc41f5ed0580c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86119055"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Azure Resource Manager-sablonok az Event Gridhez

A sablonban használandó JSON-szintaxis és-tulajdonságok megtekintéséhez lásd: [Microsoft. EventGrid-erőforrástípusok](/azure/templates/microsoft.eventgrid/allversions). A következő táblázat az Event Gridhez való Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz.

## <a name="event-grid-subscriptions"></a>Event Grid-feliratkozások
- [Egyéni témakör és előfizetés webhook-végponttal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) – üzembe helyez egy Event Grid egyéni témakört. és létrehoz egy Webhook-végpontot használó feliratkozást az üzembe helyezett témakörhöz. 
- [Egyéni témakör-előfizetés EventHub-végponttal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler) – létrehoz egy Event Grid-előfizetést egy egyéni témakörhöz. A feliratkozás egy eseményközpontot használ végpontként. 
- [Azure-előfizetés vagy erőforráscsoport-előfizetés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) – előfizet egy erőforráscsoport vagy Azure-előfizetés eseményeire. Az üzembe helyezés során célként megadott erőforráscsoport az események forrása. A feliratkozás egy webhookot használ végpontként. 
- [Blob Storage-fiók és-előfizetés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) – üzembe helyez egy Azure Blob Storage-fiókot, és előfizet az adott tárolási fiók eseményeire. 

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő mintákat:

- [PowerShell-példák](powershell-samples.md)
- [CLI-minták](cli-samples.md)
