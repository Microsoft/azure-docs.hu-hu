---
title: Az operációsrendszer-lemezek közötti felcserélés az Azure CLI használatával
description: Az Azure-beli virtuális gépek által használt operációsrendszer-lemez módosítása az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c9b0c1948dc4ecef74cd78ec1736803a0c0b4bc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497370"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-azure-cli"></a>Az Azure-beli virtuális gép által használt operációsrendszer-lemez módosítása az Azure CLI használatával


Ha már van virtuális gépe, de biztonsági mentési lemezre vagy más operációsrendszer-lemezre szeretné cserélni a lemezt, az Azure CLI használatával felcserélheti az operációsrendszer-lemezeket. Nem kell törölnie és újra létrehoznia a virtuális gépet. Akár felügyelt lemezt is használhat egy másik erőforráscsoportban, ha az még nincs használatban.

A virtuális gépet le kell állítani/fel kell szabadodni, akkor a felügyelt lemez erőforrás-azonosítója lecserélhető egy másik felügyelt lemez erőforrás-azonosítójára. 

Győződjön meg arról, hogy a virtuális gép mérete és tárolótípusa kompatibilis a csatolni kívánt lemezzel. Ha például a használni kívánt lemez egy Premium Storage-ban található, akkor a virtuális gépnek képesnek kell Premium Storage (például egy DS-sorozat méretére).

Ehhez a cikkhez az Azure CLI 2.0.25-ös vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 


Az [az disk list](/cli/azure/disk) használatával lekért lista az erőforráscsoportban lévő lemezekről.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


A [lemezek felcserélését](/cli/azure/vm) megelőzően használja az az vm stop parancsot a virtuális gép leállításhoz/felszabadításhoz.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Használja [az az vm update](/cli/azure/vm#az-vm-update) paramétert az új lemez teljes erőforrás-azonosítójával `--osdisk` 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Indítsa újra a virtuális gépet [az az vm start parancs használatával.](/cli/azure/vm)

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Következő lépések**

A lemez másolatának létrehozásához lásd: [Pillanatkép készítése lemezről.](snapshot-copy-managed-disk.md)
