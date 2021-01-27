---
title: Azure-beli virtuális gép címkézése a parancssori felület használatával
description: Ismerje meg, hogyan címkézheti a virtuális gépeket az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 32d15730557c96362602b5e324254c76637ecb55
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897442"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Virtuális gép címkézése a parancssori felület használatával

Ez a cikk bemutatja, hogyan címkézheti a virtuális gépet az Azure CLI használatával. A címkék felhasználó által definiált kulcs/érték párok, amelyek közvetlenül egy erőforráson vagy erőforráscsoporton helyezhetők el. Az Azure jelenleg legfeljebb 50 címkét támogat erőforrás és erőforráscsoport szerint. A címkéket a létrehozáskor vagy egy meglévő erőforráshoz való hozzáadáskor lehet elhelyezni egy erőforráson. A virtuális gépeket az Azure [PowerShell](tag-powershell.md)használatával is címkézheti.


Megtekintheti egy adott virtuális gép összes tulajdonságát, beleértve a címkéket is, a használatával `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Ha új virtuálisgép-címkét szeretne hozzáadni az Azure CLI-n keresztül, a `azure vm update` parancsot a címke paraméterrel együtt használhatja `--set` :

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

Most, hogy címkéket alkalmazottunk az erőforrásokhoz az Azure CLI-ben és a portálon, vessünk egy pillantást a használati adatokra, hogy megtekintsék a címkéket a számlázási portálon.

### <a name="next-steps"></a>Következő lépések

- Az Azure-erőforrások címkézésével kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md) és [címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).
- Ha szeretné megtekinteni, hogyan segíthet az Azure-erőforrások használatának kezelésében, tekintse meg [Az Azure-számla ismertetése](../cost-management-billing/understand/review-individual-bill.md)című témakört.
