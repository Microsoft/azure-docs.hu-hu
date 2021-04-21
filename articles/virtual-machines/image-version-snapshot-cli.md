---
title: CLI – Rendszerkép létrehozása pillanatképből vagy felügyelt lemezről egy Shared Image Gallery
description: Megtudhatja, hogyan hozhat létre rendszerképet pillanatképből vagy felügyelt lemezről egy Shared Image Gallery Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2dc6d99b8b1c913479fc584b52f6ff919dfac675
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792290"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Rendszerkép létrehozása felügyelt lemezről vagy pillanatképből egy Shared Image Gallery Azure CLI használatával

Ha van egy meglévő pillanatképe vagy felügyelt lemeze, amelyről egy Shared Image Gallery-lemezképet szeretne átemelni, létrehozhat egy Shared Image Gallery-rendszerképet közvetlenül a felügyelt lemezről vagy a pillanatképből. Az új rendszerkép tesztelése után törölheti a forrásként felügyelt lemezt vagy pillanatképet. Lemezképet felügyelt lemezről vagy pillanatképből is létrehozhat egy Shared Image Gallery a [Azure PowerShell.](image-version-snapshot-powershell.md)

A képkatatárban található képek két összetevőből áll, amelyeket ebben a példában hozunk létre:
- A **képdefiníciók** információt tartalmaznak a rendszerképről és a használatának követelményeiről. Ez magában foglalja, hogy a rendszerkép Windows vagy Linux, specializált vagy általánosított, kibocsátási megjegyzések, valamint minimális és maximális memóriakövetelmények. Ez egy képtípus definíciója. 
- A **rendszerképverziók** a virtuális gépek létrehozásához használhatók a virtuális gépek Shared Image Gallery. A rendszerképnek több verziója is lehet, ha szükséges a környezete számára. Virtuális gép létrehozásakor a rendszer a rendszerképverzióval hoz létre új lemezeket a virtuális géphez. A rendszerképverziók többször is használhatók.


## <a name="before-you-begin"></a>Előkészületek

A cikk befejezéséhez pillanatképet vagy felügyelt lemezt kell készítenie. 

Ha adatlemezt szeretne bevetni, az adatlemez mérete nem lehet nagyobb 1 TB-osnál.

A cikk munka közben szükség esetén cserélje le az erőforrásneveket.

## <a name="find-the-snapshot-or-managed-disk"></a>A pillanatkép vagy a felügyelt lemez megkeresi 

Az erőforráscsoportban elérhető pillanatképek listáját az az snapshot list használatával [láthatja.](/cli/azure/snapshot#az_snapshot_list) 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Pillanatkép helyett felügyelt lemezt is használhat. Felügyelt lemezt az [az disk list használatával lehet lekértni.](/cli/azure/disk#az_disk_list) 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Ha már megvan a pillanatkép vagy a felügyelt lemez azonosítója, és hozzárendeli egy nevű változóhoz, `$source` hogy később használni tudja.

Ugyanezzel a folyamattal lekért bármilyen adatlemezt, amit bele szeretne foglalni a lemezképbe. Rendelje hozzá őket a változókhoz, majd később, a rendszerkép verziójának létrehozásakor használja őket.


## <a name="find-the-gallery"></a>A katalógus megkeresve

A képdefiníció létrehozásához szüksége lesz a képkatatárra vonatkozó információkra.

Az elérhető rendszerkép-katalógusok információinak felsorolása [az az sig list használatával.](/cli/azure/sig#az_sig_list) Jegyezze fel a katalógus nevét, amelybe a katalógus később használni fog erőforráscsoportot.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

A képdefiníciók logikai csoportosítást hoznak létre a képekhez. Ezek a rendszerkép információinak kezelésére használhatók. A képdefiníciók neve kis- és nagybetűkből, számjegyekből, pontból, kötőjelekből és pontból is áll. 

A képdefiníció meghatározásakor győződjön meg arról, hogy az rendelkezik az összes megfelelő információval. Ebben a példában feltételezzük, hogy a pillanatkép vagy a felügyelt lemez használatban lévő virtuális gépről származik, és nem lett általánosított. Ha a felügyelt lemez vagy a pillanatkép általánosított operációs rendszerről készült (a Sysprep Windows, [Waagent](https://github.com/Azure/WALinuxAgent) vagy Linux rendszeren való futtatása után), módosítsa a `-deprovision` `-deprovision+user` következőre: `-OsState` `generalized` . 

További információ a képdefiníciókhoz megadható értékekről: [Képdefiníciók.](./shared-image-galleries.md#image-definitions)

Hozzon létre egy képdefiníciót a katalógusban [az az sig image-definition create használatával.](/cli/azure/sig/image-definition#az_sig_image_definition_create)

Ebben a példában a képdefiníció neve *myImageDefinition*, és [egy](./shared-image-galleries.md#generalized-and-specialized-images) specializált Linux operációsrendszer-rendszerképhez készült. A lemezképek definíciójának Windows operációs rendszer használatával történő létrehozásához használja a `--os-type Windows` következőt: . 

Ebben a példában a katalógus neve *myGallery*, ez a *myGalleryRG* erőforráscsoportban található, a képdefiníció neve *pedig mImageDefinition* lesz.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Hozzon létre egy rendszerképverziót [az az image gallery create-image-version használatával.](/cli/azure/sig/image-version#az_sig_image_version_create) 

A képverzió megengedett karakterei a számok és a időszakok. A számoknak egy 32 bites egész szám tartományán belül kell lennie. Formátum: *Főverzió.* *MinorVersion (Alverzió).* *Javítás.*

Ebben a példában a rendszerkép verziója *1.0.0,* és 1  replikát fogunk létrehozni az USA déli középső régiójában, és 1 replikát az *USA 2.* keleti régiójában zónaredundáns tárolás használatával. A replikáció célrégióinak kiválasztásakor a  felügyelt lemez vagy pillanatkép forrásrégióját is meg kell foglalnia replikációs célként.

Adja át a pillanatkép vagy a felügyelt lemez azonosítóját a `--os-snapshot` paraméterben.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Ha adatlemezeket szeretne a rendszerképbe foglalni, akkor a paramétert a LUN számra, a halmazt pedig az adatlemez vagy a pillanatkép azonosítójára kell `--data-snapshot-luns` `--data-snapshots` állítania.

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép teljes létrehozása és replikálása befejeződik, mielőtt ugyanezt a felügyelt rendszerképet egy másik rendszerképverzió létrehozására használhatja.
>
> A rendszerkép összes verzióreplikát [](../storage/common/storage-redundancy.md) zónaredundáns tárolásban is tárolhatja, ha a rendszerképverzió létrehozásakor `--storage-account-type standard_zrs` hozzáadja a adatokat.
>

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy virtuális gépet egy [specializált rendszerképverzióból.](vm-specialized-image-version-cli.md)

További információ a vásárlási terv információinak meg arról, hogy [a rendszerképek létrehozásakor](marketplace-images.md)Azure Marketplace meg a vásárlási terv adatait.