---
title: Azure Event Grid-előfizetések lekérdezése
description: Ez a cikk az Azure-előfizetésben található Event Grid-előfizetések listázását ismerteti. Az előfizetés típusától függően eltérő paramétereket kell megadnia.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3d700f543bc5e3c7add2a346c10acf975e1c2462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86120449"
---
# <a name="query-event-grid-subscriptions"></a>Event Grid-előfizetések lekérdezése 

Ez a cikk az Azure-előfizetéshez tartozó Event Grid-előfizetések listázását ismerteti. A meglévő Event Grid-előfizetések lekérdezése során fontos megérteni a különböző típusú előfizetéseket. A lekérdezni kívánt előfizetés típusától függően eltérő paramétereket kell megadnia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Erőforráscsoportok és Azure-előfizetések

Az Azure-előfizetések és-erőforráscsoportok nem Azure-erőforrások. Ezért az Event Grid-előfizetések vagy az Azure-előfizetések nem rendelkeznek az Azure-erőforrásokra vonatkozó Event Grid-előfizetésekkel. Az Event Grid-előfizetések erőforráscsoportok vagy Azure-előfizetések globálisnak tekintendők.

Az Azure-előfizetéshez és az erőforráscsoporthoz tartozó Event Grid-előfizetések lekéréséhez semmilyen paramétert nem kell megadnia. Győződjön meg arról, hogy kiválasztotta a lekérdezni kívánt Azure-előfizetést. Az alábbi példák nem kapják meg az egyéni témakörökhöz vagy az Azure-erőforrásokhoz tartozó Event Grid-előfizetéseket.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Az Azure-előfizetéshez tartozó Event Grid-előfizetések beszerzéséhez adja meg a **Microsoft. Resources. Subscriptions** témakör típusát.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Az Azure-előfizetésben lévő összes erőforráscsoport Event Grid-előfizetésének beszerzéséhez adja meg a **Microsoft. Resources. ResourceGroups** témakör típusát.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Egy adott erőforráscsoport Event Grid-előfizetésének lekéréséhez adja meg az erőforráscsoport nevét paraméterként.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Egyéni témakörök és Azure-erőforrások

Az Event Grid egyéni témakörei Azure-erőforrások. Ezért az Event Grid-előfizetéseket az egyéni témakörökhöz és más erőforrásokhoz, például a blob Storage-fiókhoz hasonlóan kell lekérdezni. Az Event Grid-előfizetések egyéni témakörökhöz való beszerzéséhez meg kell adnia azokat a paramétereket, amelyek azonosítják az erőforrást, vagy az erőforrás helyét azonosítják. Az Event Grid-előfizetéseket nem lehet széles körben lekérdezni az Azure-előfizetésben lévő erőforrásokhoz.

Ha az Event Grid-előfizetéseket egyéni témakörökhöz és más erőforrásokhoz szeretné beolvasni egy adott helyen, adja meg a hely nevét.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

A helyhez tartozó egyéni témakörökre vonatkozó előfizetések beszerzéséhez adja meg a **Microsoft. EventGrid.** topics helyét és a témakör típusát.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Egy adott helyhez tartozó Storage-fiókokra vonatkozó előfizetések lekéréséhez adja meg a **Microsoft. Storage. StorageAccounts** helyét és a témakör típusát.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Az Event Grid-előfizetések egyéni témakörhöz való beszerzéséhez adja meg az egyéni témakör nevét és az erőforráscsoport nevét.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Az Event Grid-előfizetések egy adott erőforráshoz való lekéréséhez adja meg az erőforrás-azonosítót.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítéséről és újrapróbálkozásáról, [Event Grid az üzenetek kézbesítéséről, és próbálkozzon újra](delivery-and-retry.md).
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
