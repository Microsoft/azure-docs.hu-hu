---
title: Event Grid erőforrás elérési kulcsának lekérése
description: Ez a cikk egy Event Grid témakör vagy tartomány elérési kulcsának lekérését ismerteti
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: a642affbac79766684dc75a37dae0373450d20e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632529"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Hozzáférési kulcsok beolvasása Event Grid erőforrásokhoz (témakörök vagy tartományok)
A hozzáférési kulcsok használatával hitelesítheti az alkalmazás közzétételi eseményeit Azure Event Grid erőforrásokra (témakörökre és tartományokra). Javasoljuk, hogy rendszeresen újragenerálja a kulcsokat, és biztonságosan tárolja őket. A szolgáltatás két hozzáférési kulccsal rendelkezik, így az egyik kulccsal kezelheti a kapcsolatokat, miközben újragenerálja a másikat.

Ez a cikk azt ismerteti, hogyan lehet hozzáférési kulcsokat beolvasni egy Event Grid erőforráshoz (témakörhöz vagy tartományhoz) a Azure Portal, a PowerShell vagy a parancssori felület használatával. 

## <a name="azure-portal"></a>Azure Portal
A Azure Portal váltson a témakör vagy tartomány **Event Grid témakör** vagy **Event Grid tartomány** lap **hozzáférési kulcsok** lapjára.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Hozzáférési kulcsok lapja":::

## <a name="azure-powershell"></a>Azure PowerShell
A [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) paranccsal elérheti a témakörök elérési kulcsait. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Használja a [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) parancsot a tartományok hozzáférési kulcsainak lekéréséhez. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
A témakörök elérési kulcsainak megtekintéséhez használja az az [eventgrid témakört](/cli/azure/eventgrid/topic/key#az-eventgrid-topic-key-list) . 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Az [az eventgrid domain Key List](/cli/azure/eventgrid/domain/key#az-eventgrid-domain-key-list) paranccsal érheti el a tartományok hozzáférési kulcsait. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikket: [közzétételi ügyfelek hitelesítése](security-authenticate-publishing-clients.md). 
