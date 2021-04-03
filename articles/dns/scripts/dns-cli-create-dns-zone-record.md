---
title: DNS-zóna és-rekord létrehozása egy tartománynévhez – Azure CLI-Azure DNS
description: Ez az Azure CLI-példaszkript bemutatja, hogyan hozhat létre DNS-zónát és -rekordot egy tartománynévhez.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 348b7911930711a25c88595b6360341ef6e00468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954426"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Azure CLI-példaszkript: DNS-zóna és -rekord létrehozása

Ez az Azure CLI-példaszkript DNS-zónát és -rekordot hoz létre egy tartománynévhez. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a DNS-zóna és az összes kapcsolódó erőforrás.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép, egy rendelkezésre állási csoport, egy terheléselosztó és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Létrehoz egy Azure DNS-zónát. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Egy *A* rekordot ad hozzá egy DNS-zónához. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Egy DNS-zóna összes *A* rekordkészletét listázza. |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).