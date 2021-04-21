---
title: Felügyelt rendszerkép klónozása rendszerképverzióra az Azure CLI használatával
description: Megtudhatja, hogyan klónozható egy felügyelt rendszerkép egy rendszerképverzióra egy Shared Image Gallery Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d0644b9ec9009fe5d1db7701834cb9788f86ab0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790166"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Felügyelt rendszerkép klónozása rendszerképverzióra az Azure CLI használatával
Ha egy meglévő felügyelt rendszerképet szeretne klónozni egy Shared Image Gallery-be, létrehozhat egy Shared Image Gallery-rendszerképet közvetlenül a felügyelt rendszerképből. Az új rendszerkép tesztelése után törölheti a forrásként felügyelt rendszerképet. Felügyelt rendszerképről felügyelt rendszerképről a [PowerShell](image-version-managed-image-powershell.md)használatával Shared Image Gallery át.

A képkatatárban található képek két összetevőből áll, amelyeket ebben a példában hozunk létre:
- A **képdefiníciók** információt tartalmaznak a rendszerképről és a használatának követelményeiről. Ez magában foglalja, hogy a rendszerkép Windows vagy Linux, specializált vagy általánosított, kibocsátási megjegyzések, valamint minimális és maximális memóriakövetelmények. Ez egy képtípus definíciója. 
- A **rendszerképverziók** a virtuális gépek létrehozásához használhatók a virtuális gépek Shared Image Gallery. A rendszerképnek több verziója is lehet, ha szükséges a környezete számára. Virtuális gép létrehozásakor a rendszer a rendszerképverzióval hoz létre új lemezeket a virtuális géphez. A rendszerképverziók többször is használhatók.


## <a name="before-you-begin"></a>Előkészületek

A cikk befejezéséhez a következővel kell [Shared Image Gallery.](shared-images-cli.md) 

A cikkben található példa befejezéséhez egy általánosított virtuális gép meglévő felügyelt rendszerképének kell lennie. További információ: [Felügyelt rendszerkép rögzítése.](./linux/capture-image.md) Ha a felügyelt rendszerkép adatlemezt tartalmaz, az adatlemez mérete nem lehet nagyobb 1 TB-osnál.

A cikk során szükség esetén cserélje le az erőforráscsoportot és a virtuális gépek nevét.



## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

Mivel a felügyelt rendszerképek mindig általánosított rendszerképek, létre fog hozni egy rendszerképdefiníciót a használatával `--os-state generalized` egy általánosított rendszerképhez.

A képdefiníciók neve kis- és nagybetűkből, számjegyekből, pontból, kötőjelekből és pontból is áll. 

További információ a képdefiníciókhoz megadható értékekről: [Képdefiníciók.](./shared-image-galleries.md#image-definitions)

Hozzon létre egy képdefiníciót a katalógusban [az az sig image-definition create használatával.](/cli/azure/sig/image-definition#az_sig_image_definition_create)

Ebben a példában [a](./shared-image-galleries.md#generalized-and-specialized-images) képdefiníció neve *myImageDefinition*, és egy általános linuxos operációsrendszer-rendszerképhez használható. A lemezképek definíciójának Windows operációs rendszer használatával történő létrehozásához használja a `--os-type Windows` következőt: . 

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
   --os-state generalized
```


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Hozzon létre verziókat [az az image gallery create-image-version használatával.](/cli/azure/sig/image-version#az_sig_image_version_create) Meg kell adni a felügyelt rendszerkép azonosítóját, hogy a rendszerkép verziójának létrehozásához alapkonfigurációként használható. Az [az image list használatával](/cli/azure/image?view#az_image_list) lekérte a rendszerképekhez azid-eket. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

A képverzió megengedett karakterei a számok és a időszakok. A számoknak egy 32 bites egész szám tartományán belül kell lennie. Formátum: *Főverzió.* *MinorVersion (Alverzió).* *Javítás.*

Ebben a példában a rendszerkép verziója *1.0.0,* és 1  replikát fogunk létrehozni az USA déli középső régiójában, és 1 replikát az *USA 2.* keleti régiójában zónaredundáns tárolás használatával. A replikáció célrégióinak kiválasztásakor ne  feledje, hogy a forrásrégió is szerepel a replikáció céljának.

Adja át a felügyelt rendszerkép azonosítóját a `--managed-image` paraméterben.


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép teljes létrehozása és replikálása befejeződik, mielőtt ugyanezt a felügyelt rendszerképet egy másik rendszerképverzió létrehozására használhatja.
>
> A rendszerkép összes verzióreplikát [](../storage/common/storage-redundancy.md) zónaredundáns tárolásban is tárolhatja, ha a rendszerképverzió létrehozásakor `--storage-account-type standard_zrs` hozzáadja a adatokat.
>

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy virtuális gépet egy [általános rendszerképverzióból.](vm-generalized-image-version-cli.md)

További információ a vásárlási terv információinak meg arról, hogy [a rendszerképek létrehozásakor](marketplace-images.md)Azure Marketplace meg a vásárlási terv adatait.