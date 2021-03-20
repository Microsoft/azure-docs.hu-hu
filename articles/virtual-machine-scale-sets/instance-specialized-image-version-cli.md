---
title: Méretezési csoport létrehozása speciális rendszerkép-verzióból az Azure CLI használatával
description: Hozzon létre egy méretezési készletet egy speciális rendszerkép-verzió használatával egy megosztott rendszerkép-katalógusban az Azure CLI használatával.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ecc2bfe6bdc06ede61e6c4d1e6eccfc9ef6323a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878002"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Méretezési csoport létrehozása speciális rendszerkép-verzióval az Azure CLI használatával

Méretezési csoport létrehozása egy megosztott rendszerkép-katalógusban tárolt [speciális rendszerkép-verzióból](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) . Ha általánosított rendszerkép-verzió használatával szeretne méretezési készletet létrehozni, tekintse meg [a méretezési csoport létrehozása általánosított rendszerképből](instance-generalized-image-version-cli.md)című témakört.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.4.0 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

Szükség szerint cserélje le az erőforrás-neveket ebben a példában. 

A katalógusban szereplő képdefiníciók listázása az az [SIG rendszerkép-definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list) paranccsal, hogy megtekintse a definíciók nevét és azonosítóját.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Méretezési csoport létrehozása [`az vmss create`](/cli/azure/vmss#az-vmss-create) a (z `--specialized` ) paraméter használatával, amely jelzi, hogy a rendszerkép egy speciális rendszerkép.

A rendszerkép-definíció AZONOSÍTÓjának használatával `--image` hozza létre a méretezési csoport példányait az elérhető lemezkép legújabb verziójából. A méretezési csoport példányait egy adott verzióból is létrehozhatja, ha megadja a rendszerkép-verziójának AZONOSÍTÓját `--image` . Vegye figyelembe, hogy egy adott rendszerkép-verzió használata azt jelenti, hogy az Automation sikertelen lehet, ha az adott rendszerkép verziója nem érhető el, mert törölték vagy eltávolították a régióból. Javasoljuk, hogy az új virtuális gép létrehozásához használja a rendszerkép-definíció AZONOSÍTÓját, kivéve, ha egy adott rendszerkép-verzióra van szükség.

Ebben a példában a *myImageDefinition* rendszerkép legújabb verziójából hozunk létre példányokat.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```


## <a name="next-steps"></a>Következő lépések
Az [Azure rendszerkép-szerkesztő (előzetes verzió)](../virtual-machines/image-builder-overview.md) segítségével automatizálhatja a rendszerkép-verziók létrehozását, és [egy meglévő rendszerkép-verzióból](../virtual-machines/linux/image-builder-gallery-update-image-version.md)is frissítheti és létrehozhatja az új rendszerkép verzióját. 

Sablonok használatával is létrehozhat megosztott képgyűjteményi erőforrásokat. Több Azure Gyorsindítás-sablon is elérhető: 

- [Shared Image Gallery létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerképverzió létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)