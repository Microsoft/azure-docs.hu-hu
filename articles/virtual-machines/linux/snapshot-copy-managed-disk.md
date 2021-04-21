---
title: Virtuális merevlemez pillanatképének létrehozása az Azure CLI használatával
description: Megtudhatja, hogyan hozhat létre másolatot egy VHD-ről az Azure-ban biztonsági másolatként vagy hibaelhárításhoz.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ab19bb1c6cc43334a3d0d427b6aff6ced2d6cc69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789662"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>Pillanatkép létrehozása a portál vagy az Azure CLI használatával

Pillanatkép készítése operációs rendszerről vagy adatlemezről biztonsági mentéshez vagy a virtuális gép hibáinak elhárításához. A pillanatképek a virtuális merevlemezek teljes, csak olvasható másolatai. 

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával 

Az alábbi példában az szükséges, hogy [Cloud Shell](https://shell.azure.com/bash) vagy az Azure CLI telepítve legyen.

A következő lépések azt mutatják be, hogyan készíthet pillanatképet az **az snapshot create** paranccsal a **--source-disk paraméterrel.** Az alábbi példa feltételezi, hogy a *myResourceGroup* erőforráscsoportban található egy *myVM* nevű virtuális gép.

Szerezze be a lemezazonosítót [az az vm show használatával.](/cli/azure/vm#az_vm_show)

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Készítsen egy *osDisk-backup nevű pillanatképet* [az az snapshot create használatával.](/cli/azure/snapshot#az_snapshot_create)

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Ha a pillanatképet zóna rugalmas tárolóban szeretné tárolni, létre kell hoznia [](../../availability-zones/az-overview.md) egy olyan régióban, amely támogatja a rendelkezésre állási zónákat, és tartalmaznia kell a **--sku** Standard_ZRS paramétert.

A pillanatképek listáját az az snapshot list használatával [láthatja.](/cli/azure/snapshot#az_snapshot_list)

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Az Azure Portal használata 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal felső sarokban kattintson az **Erőforrás létrehozása** elemre, és keressen rá a **pillanatképre.** A **keresési eredmények közül** válassza a Pillanatkép lehetőséget.
3. A Pillanatkép **panelen** kattintson a **Létrehozás elemre.**
4. Adja meg a **pillanatkép** nevét.
5. Válasszon ki egy meglévő erőforráscsoportot, vagy írja be egy új erőforrás nevét. 
7. A **Forráslemez beállításnál** válassza ki a felügyelt lemezt, amelyről pillanatképet készít.
8. Válassza ki **a pillanatkép** tárolására használni kívánt Fióktípust. Használja **standard HDD,** kivéve, ha nagy teljesítményű SSD-n kell tárolnia.
9. Kattintson a **Létrehozás** lehetőségre.


## <a name="next-steps"></a>Következő lépések

 Virtuális gép létrehozása pillanatképből: hozzon létre egy felügyelt lemezt a pillanatképből, majd csatlakoztassa az új felügyelt lemezt operációsrendszer-lemezként. További információ: Virtuális gép [létrehozása pillanatkép-szkriptből.](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json)