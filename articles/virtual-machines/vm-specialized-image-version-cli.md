---
title: Virtuális gép létrehozása specializált rendszerképverzióból az Azure CLI használatával
description: Hozzon létre egy virtuális gépet egy specializált rendszerképverzióval egy Shared Image Gallery Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3498037f3d2088459784ab066b8e94ba344a275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792182"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Virtuális gép létrehozása specializált rendszerképverzióval az Azure CLI használatával

Virtuális gép létrehozása [egy](./shared-image-galleries.md#generalized-and-specialized-images) virtuális gépen tárolt specializált rendszerképverzióból Shared Image Gallery. Ha általános rendszerképverzióval szeretne virtuális gépet létrehozni, tekintse meg a virtuális gép általános rendszerképverzióból [való létrehozásáról való lásd:](vm-generalized-image-version-cli.md).

Ebben a példában szükség szerint cserélje le az erőforrásneveket. 

Lists the image definitions in a gallery [using az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) to see the name and ID of the definitions.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Hozza létre a virtuális gépet [az az vm create](/cli/azure/vm#az_vm_create) használatával a --specialized paraméterrel, amely jelzi, hogy a rendszerkép egy specializált rendszerkép. 

A rendszerkép-definíció azonosítójával hozza létre a virtuális gépet a rendszerkép legújabb elérhető `--image` verziójából. A virtuális gépet egy adott verzióból is létrehozhatja, ha a rendszerkép verzióazonosítóját ad meg `--image` a számára. 

Ebben a példában egy virtuális gépet hozunk létre a *myImageDefinition rendszerkép* legújabb verziójából.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Következő lépések
[Az Azure Image Builder (előzetes verzió)](./image-builder-overview.md) segíthet automatizálni a rendszerképverzió létrehozását, sőt akár egy meglévő rendszerképverzióból is frissíthet és létrehozhat egy új [rendszerképverziót.](./linux/image-builder-gallery-update-image-version.md) 

Sablonokkal is létrehozhat Shared Image Gallery erőforrást. Számos Azure gyorsindítási sablon érhető el: 

- [Shared Image Gallery létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerképverzió létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerképverzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)