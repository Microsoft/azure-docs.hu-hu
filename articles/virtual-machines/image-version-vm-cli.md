---
title: Rendszerkép létrehozása virtuális gépről
description: Megtudhatja, hogyan hozhat létre egy rendszerképet egy megosztott képtárban egy Azure-beli virtuális gépről.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f53a6b63c744b0e3e41f7ad22270cd842da57674
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82796576"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Rendszerkép-verzió létrehozása az Azure-beli virtuális gépről az Azure CLI használatával

Ha rendelkezik egy meglévő virtuális géppel, amelyet több, azonos virtuális gép létrehozásához szeretne használni, akkor az adott virtuális géppel létrehozhat egy rendszerképet egy megosztott rendszerkép-katalógusban az Azure CLI használatával. [Azure PowerShell](image-version-vm-powershell.md)használatával is létrehozhat egy rendszerképet egy virtuális gépről.

A **rendszerkép verziója** a virtuális gép létrehozásához használható a megosztott képkatalógusok használatakor. A környezethez szükség lehet a rendszerkép több verziójára. Ha rendszerkép-verziót használ a virtuális gép létrehozásához, a rendszerkép verziója az új virtuális gép lemezének létrehozásához használatos. A rendszerkép verziója többször is használható.


## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához meg kell adnia egy meglévő megosztott képtárat. 

Rendelkeznie kell egy meglévő virtuális géppel is az Azure-ban, ugyanabban a régióban, ahol a gyűjtemény található. 

Ha a virtuális gépnek van csatolt adatlemeze, az adatlemez mérete nem lehet nagyobb 1 TB-nál.

A cikkben végzett munka során szükség esetén cserélje le az erőforrások nevét.

## <a name="get-information-about-the-vm"></a>A virtuális géppel kapcsolatos információk lekérése

Megtekintheti az [az VM List](/cli/azure/vm#az-vm-list)paranccsal elérhető virtuális gépek listáját. 

```azurecli-interactive
az vm list --output table
```

Ha ismeri a virtuális gép nevét és a hozzá tartozó erőforráscsoportot, szerezze be a virtuális gép AZONOSÍTÓját az [az VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view)paranccsal. 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. Ezek az adatok a bennük létrehozott rendszerkép-verziókra vonatkozó információk kezelésére szolgálnak. 

A képdefiníciók nevei kis-és nagybetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. 

Győződjön meg arról, hogy a megfelelő típus a rendszerkép definíciója. Ha általánosított a virtuális gépet (a Windows Sysprep használatával vagy a Linux-waagent), akkor létre kell hoznia egy általánosított rendszerkép-definíciót a használatával `--os-state generalized` . Ha a virtuális gépet a meglévő felhasználói fiókok eltávolítása nélkül szeretné használni, hozzon létre egy speciális rendszerkép-definíciót a használatával `--os-state specialized` .

További információ a képdefiníciók által megadható értékekről: [képdefiníciók](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Hozzon létre egy rendszerkép-definíciót a galériában az [az SIG rendszerkép-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create)paranccsal.

Ebben a példában a képdefiníció neve *myImageDefinition*, és egy [speciális](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) Linux operációsrendszer-rendszerképhez van. Ha Windows operációs rendszert használó lemezképek definícióját szeretné létrehozni, használja a következőt: `--os-type Windows` . 

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

Hozzon létre egy rendszerkép-verziót a virtuális gépről az [az rendszerkép-gyűjtemény létrehozása-rendszerkép-Version](/cli/azure/sig/image-version#az-sig-image-version-create)paranccsal.  

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és két replikát fogunk létrehozni az *USA nyugati középső* régiójában, 1 replika az *USA déli középső* régiójában és 1 replika az *USA 2. keleti* régiójában, a zóna redundáns tárolásával. A replikációs régióknak tartalmaznia kell azt a régiót, amelyben a forrás virtuális gép található.

Cserélje le a `--managed-image` példában szereplő értéket a virtuális gép azonosítójával az előző lépésből.

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
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.
>
> A rendszerképet a prémium tárolóban is tárolhatja egy hozzáadási `--storage-account-type  premium_lrs` vagy a [zóna redundáns tárterületével](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) , `--storage-account-type  standard_zrs` Ha létrehozza a rendszerkép verzióját.
>

## <a name="next-steps"></a>További lépések

Hozzon létre egy virtuális gépet az [általánosított rendszerképből](vm-generalized-image-version-cli.md) az Azure CLI használatával.
