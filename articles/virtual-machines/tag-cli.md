---
title: Azure-beli virtuális gép címkézése az Azure CLI használatával
description: Ismerje meg, hogyan címkézett meg egy virtuális gépet az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 20bb4ab622a01646bcc61d0f691c514a25a06edc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502606"
---
# <a name="how-to-tag-a-vm-using-the-azure-cli"></a>Virtuális gép címkézése az Azure CLI használatával

Ez a cikk bemutatja, hogyan címkéz egy virtuális gépet az Azure CLI használatával. A címkék felhasználó által definiált kulcs/érték párok, amelyek közvetlenül elhelyezhetőek egy erőforráson vagy erőforráscsoporton. Az Azure jelenleg erőforrásonként és erőforráscsoportonként legfeljebb 50 címkét támogat. A címkék elhelyezhetőek az erőforráson a létrehozáskor, vagy hozzáadhatóak egy meglévő erőforráshoz. A virtuális gépeket az Azure PowerShell használatával is [felcímkézheti.](tag-powershell.md)


Egy adott virtuális gép összes tulajdonságát megtekintheti, beleértve a címkéket is a `az vm show` használatával.

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Ha új virtuálisgép-címkét szeretne hozzáadni az Azure CLI-en keresztül, használhatja a parancsot és a `azure vm update` címkeparamétert: `--set`

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

A címkék eltávolításához használhatja a `--remove` paramétert a `azure vm update` parancsban.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Most, hogy címkéket alkalmazottunk az erőforrásokra az Azure CLI-hez és a Portálhoz, vessünk egy pillantást a használati adatokra, és lássuk a címkéket a számlázási portálon.

### <a name="next-steps"></a>Következő lépések

- További információ az Azure-erőforrások címkézésről: Azure Resource Manager [Áttekintés](../azure-resource-manager/management/overview.md) és Címkék használata [az Azure-erőforrások rendszerezéséhez.](../azure-resource-manager/management/tag-resources.md)
- Ha megmutatja, hogyan segíthetnek a címkék az Azure-erőforrások használatának kezelésében, tekintse meg [az Azure-számla megértését.](../cost-management-billing/understand/review-individual-bill.md)
