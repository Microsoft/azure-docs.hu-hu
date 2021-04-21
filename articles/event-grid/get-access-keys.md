---
title: Hozzáférési kulcs lekérte egy Event Grid erőforráshoz
description: Ez a cikk azt ismerteti, hogyan lehet hozzáférési kulcsot lekért Event Grid témakör vagy tartomány számára
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: cd60777b2e28b82d72f8f2bf93fe0be301e9e280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775222"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Hozzáférési kulcsok le Event Grid erőforrásokhoz (témakörök vagy tartományok)
A hozzáférési kulcsokkal hitelesíthetők az alkalmazások közzétételi eseményei az Azure Event Grid (témakörök és tartományok) számára. Javasoljuk, hogy rendszeresen újragenerálja a kulcsokat, és tárolja őket biztonságosan. Két hozzáférési kulcsot biztosítunk Önnek, hogy az egyik kulccsal fenntartsa a kapcsolatokat a másik újragenerálása közben.

Ez a cikk azt ismerteti, hogyan lehet hozzáférési kulcsokat lekért egy Event Grid erőforráshoz (témakör vagy tartomány) a Azure Portal, a PowerShell vagy a parancssori felület használatával. 

## <a name="azure-portal"></a>Azure Portal
A Azure Portal a témakör vagy tartomány Event Grid  vagy Event Grid hozzáférési  kulcsok lapjára váltson.   

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Hozzáférési kulcsok oldal":::

## <a name="azure-powershell"></a>Azure PowerShell
A [Get-AzEventGridTopicKey paranccsal](/powershell/module/az.eventgrid/get-azeventgridtopickey) szerezze be a témakörök hozzáférési kulcsait. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

A [Get-AzEventGridDomainKey paranccsal](/powershell/module/az.eventgrid/get-azeventgriddomainkey) szerezze be a tartományok hozzáférési kulcsait. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
Az [az eventgrid topic key list használatával](/cli/azure/eventgrid/topic/key#az_eventgrid_topic_key_list) lekért hozzáférési kulcsokat a témakörökhez. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Az [az eventgrid domain key list használatával](/cli/azure/eventgrid/domain/key#az_eventgrid_domain_key_list) lekért hozzáférési kulcsokat a tartományokhoz. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Következő lépések
Lásd a következő cikket: [Közzétételi ügyfelek hitelesítése.](security-authenticate-publishing-clients.md) 
