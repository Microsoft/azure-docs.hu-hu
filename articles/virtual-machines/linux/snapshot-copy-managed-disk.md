---
title: Virtuális merevlemez pillanatképének létrehozása az Azure CLI használatával
description: Megtudhatja, hogyan hozhat létre egy virtuális merevlemez másolatát az Azure-ban biztonsági mentés vagy hibaelhárítási problémák esetén.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d041f864c6c8cd3ae9c522d79447d71c86f9ac04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98875604"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>Pillanatkép létrehozása a portál vagy az Azure CLI használatával

Készítsen pillanatképet az operációs rendszerről vagy az adatlemezről a biztonsági mentéshez vagy a virtuális gépekkel kapcsolatos hibák elhárításához. A pillanatkép egy virtuális merevlemez teljes, írásvédett másolata. 

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával 

Az alábbi példában a [Cloud Shell](https://shell.azure.com/bash) használatára van szükség, vagy az Azure CLI telepítve van.

A következő lépések bemutatják, hogyan készíthet pillanatképet az az **Snapshot Create** paranccsal a **--Source-Disk** paraméterrel. Az alábbi példa azt feltételezi, hogy létezik egy *myVM* nevű virtuális gép a *myResourceGroup* -erőforráscsoporthoz.

Töltse le a lemez AZONOSÍTÓját az [az VM show](/cli/azure/vm#az-vm-show)paranccsal.

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Hozzon létre egy *osDisk-Backup* nevű pillanatképet [az az Snapshot Create](/cli/azure/snapshot#az-snapshot-create)paranccsal.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Ha a pillanatképet a zóna rugalmas tárterületén szeretné tárolni, létre kell hoznia egy olyan régióban, amely támogatja a [rendelkezésre állási zónákat](../../availability-zones/az-overview.md) , és tartalmazza a **--SKU Standard_ZRS** paramétert.

A pillanatképek listáját az [az Snapshot List](/cli/azure/snapshot#az-snapshot-list)paranccsal tekintheti meg.

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Az Azure Portal használata 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal felső sarokban kattintson az **erőforrás létrehozása** elemre, és keresse meg a **pillanatképet**. Válassza a **Pillanatkép** lehetőséget a keresési eredmények közül.
3. A **Pillanatkép** panelen kattintson a **Létrehozás** gombra.
4. Adja meg a pillanatkép **nevét** .
5. Válasszon ki egy meglévő erőforráscsoportot, vagy írja be egy új nevet. 
7. A **forrásoldali lemez** lapon válassza ki a felügyelt lemezt a pillanatképhez.
8. Válassza ki a pillanatkép tárolására használni kívánt **fiókot** . A **standard HDD** csak akkor használja, ha nagy teljesítményű SSD-meghajtón tárolja.
9. Kattintson a **Létrehozás** lehetőségre.


## <a name="next-steps"></a>Következő lépések

 Hozzon létre egy virtuális gépet egy pillanatképből egy felügyelt lemez létrehozásával a pillanatképből, majd csatlakoztassa az új felügyelt lemezt az operációsrendszer-lemezként. További információ: [virtuális gép létrehozása pillanatkép-](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json) parancsfájlból.