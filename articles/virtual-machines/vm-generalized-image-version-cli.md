---
title: Virtuális gép létrehozása általános rendszerképből az Azure CLI használatával
description: Hozzon létre egy virtuális gépet egy általános rendszerképverzióból az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5e0e5544c5e66f43b56de49beaa3ef3932d33f9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776878"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-azure-cli"></a>Virtuális gép létrehozása általános rendszerképverzióból az Azure CLI használatával

Hozzon létre egy virtuális [gépet](./shared-image-galleries.md#generalized-and-specialized-images) egy virtuális gépen tárolt általános rendszerképverzióból Shared Image Gallery. Ha egy speciális rendszerkép használatával szeretne virtuális gépet létrehozni, tekintse meg a virtuális gép specializált rendszerképből [való létrehozásáról való lásd:](vm-specialized-image-version-powershell.md). 


## <a name="get-the-image-id"></a>A rendszerkép-azonosító lekért

Lists the image definitions in a gallery [using az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) to see the name and ID of the definitions.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. A rendszerkép legújabb verziójának a használatára állítsa be a rendszerkép `--image` definíciójának azonosítóját. 

Ebben a példában szükség szerint cserélje le az erőforrásneveket. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Egy adott verziót is használhat a paraméter rendszerképverzió-azonosítójának `--image` használatával. Ha például a rendszerkép *1.0.0-s* verzióját használja, írja be a következőt: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Következő lépések

[Az Azure Image Builder (előzetes verzió)](./image-builder-overview.md) segíthet automatizálni a rendszerképverzió létrehozását, sőt akár egy meglévő rendszerképverzióból is frissíthet és létrehozhat egy új [rendszerképverziót.](./linux/image-builder-gallery-update-image-version.md)