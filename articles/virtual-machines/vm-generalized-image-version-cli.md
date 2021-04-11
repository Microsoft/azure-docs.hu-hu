---
title: Virtuális gép létrehozása általánosított rendszerképből az Azure CLI használatával
description: Hozzon létre egy virtuális gépet egy általánosított rendszerkép-verzióból az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2bcaf85f61a4d8cf4d23c9c5be7f46d765d77dbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551042"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>Virtuális gép létrehozása általánosított rendszerkép-verzióból a parancssori felület használatával

Hozzon létre egy virtuális gépet egy megosztott rendszerkép-gyűjteményben tárolt [általánosított rendszerkép-verzióból](./shared-image-galleries.md#generalized-and-specialized-images) . Ha speciális rendszerkép használatával szeretne virtuális gépet létrehozni, tekintse meg [a virtuális gép létrehozása speciális rendszerképből](vm-specialized-image-version-powershell.md)című témakört. 


## <a name="get-the-image-id"></a>A rendszerkép AZONOSÍTÓjának beolvasása

A katalógusban szereplő képdefiníciók listázása az az [SIG rendszerkép-definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list) paranccsal, hogy megtekintse a definíciók nevét és azonosítóját.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. A lemezkép legújabb verziójának használatához állítsa `--image` a rendszerkép-DEFINÍCIÓ azonosítóját. 

Szükség szerint cserélje le az erőforrás-neveket ebben a példában. 

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

Egy adott verziót is használhat a paraméterhez tartozó rendszerkép-verzió AZONOSÍTÓjának használatával `--image` . Például a következő rendszerkép *-verzió használata* `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` :

## <a name="next-steps"></a>Következő lépések

Az [Azure rendszerkép-szerkesztő (előzetes verzió)](./image-builder-overview.md) segítségével automatizálhatja a rendszerkép-verziók létrehozását, és [egy meglévő rendszerkép-verzióból](./linux/image-builder-gallery-update-image-version.md)is frissítheti és létrehozhatja az új rendszerkép verzióját.