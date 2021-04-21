---
title: Rendszerkép létrehozása virtuális gépről az Azure CLI használatával
description: Megtudhatja, hogyan hozhat létre rendszerképet egy Shared Image Gallery Azure-beli virtuális gépről.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7bfe8b1255c88878c2dc4661e9daa3e16397e9f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792272"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Rendszerképverzió létrehozása Azure-beli virtuális gépről az Azure CLI használatával

Ha van egy meglévő virtuális gépe, amely több, azonos virtuális gép létrehozásához szeretne használni, használhatja azt a virtuális gépet, hogy rendszerképet hozzon létre egy Shared Image Gallery az Azure CLI használatával. Rendszerképet virtuális gépből is létrehozhat a [Azure PowerShell.](image-version-vm-powershell.md)

A **rendszerképverziók** használatával hozhat létre virtuális gépeket virtuális gépek Shared Image Gallery. A rendszerképnek több verziója is lehet, ha szükséges a környezete számára. Amikor rendszerképverziót használ egy virtuális gép létrehozásához, a rendszerképverzióval hoz létre lemezeket az új virtuális géphez. A rendszerképverziók többször is használhatók.


## <a name="before-you-begin"></a>Előkészületek

A cikk befejezéséhez egy meglévő fióknak kell Shared Image Gallery. 

Egy meglévő azure-beli virtuális gépnek is kell lennie, amely ugyanabban a régióban található, mint a katalógus. 

Ha a virtuális géphez adatlemez van csatlakoztatva, az adatlemez mérete nem lehet nagyobb 1 TB-nál.

A cikk munka közben szükség esetén cserélje le az erőforrásneveket.

## <a name="get-information-about-the-vm"></a>Információk lekért a virtuális gépről

A rendelkezésre álló virtuális gépek listáját az [az vm list listában láthatja.](/cli/azure/vm#az_vm_list) 

```azurecli-interactive
az vm list --output table
```

Ha már ismeri a virtuális gép nevét és erőforráscsoportját, az [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view)használatával szerezze be a virtuális gép azonosítóját. 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

A képdefiníciók logikai csoportosítást hoznak létre a képekhez. Ezek a bennük létrehozott rendszerképverziók információinak kezelésére használhatók. 

A képdefiníciók nevei nagybetűkből vagy kisbetűkből, számjegyekből, pontból, kötőjelekből és pontból is felhasználhatja őket. 

Győződjön meg arról, hogy a képdefiníció a megfelelő típus. Ha általánosította a virtuális gépet (a Windowshoz készült Sysprep használatával vagy Linuxon a waagent -deprovision használatával), akkor létre kell hoznia egy általánosított rendszerkép-definíciót a `--os-state generalized` használatával. Ha meglévő felhasználói fiókok eltávolítása nélkül szeretné használni a virtuális gépet, hozzon létre egy specializált rendszerkép-definíciót a `--os-state specialized` használatával.

További információ a képdefiníciókhoz megadható értékekről: [Képdefiníciók.](./shared-image-galleries.md#image-definitions)

Hozzon létre egy képdefiníciót a katalógusban [az az sig image-definition create használatával.](/cli/azure/sig/image-definition#az_sig_image_definition_create)

Ebben a példában a képdefiníció neve *myImageDefinition,* és [egy](./shared-image-galleries.md#generalized-and-specialized-images) specializált Linux operációsrendszer-rendszerképhez készült. A lemezképek definíciójának Windows operációs rendszer használatával történő létrehozásához használja a `--os-type Windows` következőt: . 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Hozzon létre egy rendszerképverziót a virtuális gépről [az az image gallery create-image-version használatával.](/cli/azure/sig/image-version#az_sig_image_version_create)  

A képverzió megengedett karakterei a számok és a időszakok. A számoknak egy 32 bites egész szám tartományán belül kell lennie. Formátum: *MajorVersion.* *Alverzió.* *Javítás.*

Ebben a példában a rendszerkép verziója *1.0.0,* és 2 replikát fogunk létrehozni az USA  nyugati középső régiójában, 1 replikát az USA déli középső régiójában és 1 replikát az USA *2.* keleti régiójában zónaredundáns tárolás használatával.  A replikációs régióknak tartalmazniuk kell azt a régiót, ahol a forrás virtuális gép található.

Cserélje le a értékét ebben a példában az előző lépésben `--managed-image` lekért virtuális gép azonosítójára.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép teljes verziószáma befejeződik és replikálódik, mielőtt ugyanezt a felügyelt rendszerképet egy másik rendszerképverzió létrehozásához használhatja.
>
> A rendszerképet prémium szintű tárolóban is tárolhatja, ha hozzáadja a et, vagy zónaredundáns tárolást ad hozzá a `--storage-account-type  premium_lrs` rendszerképverzió [](../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` létrehozásakor.
>

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy virtuális gépet az [általánosított rendszerképből](vm-generalized-image-version-cli.md) az Azure CLI használatával.

A vásárlási terv információinak megszabadlása: Supply Azure Marketplace purchase plan information when creating images (Vásárlási terv Azure Marketplace a [rendszerképek létrehozásakor).](marketplace-images.md)