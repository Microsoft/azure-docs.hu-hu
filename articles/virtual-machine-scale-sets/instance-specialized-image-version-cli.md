---
title: Méretezési csoport létrehozása specializált rendszerképverzióból az Azure CLI használatával
description: Méretezési csoport létrehozása specializált rendszerképverzióval egy Shared Image Gallery Azure CLI használatával.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5fc88c00d548c0a034984976557d316fdac7620f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792344"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Méretezési csoport létrehozása specializált rendszerképverzióval az Azure CLI használatával

Méretezési csoport létrehozása [egy](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) tárolóban tárolt specializált rendszerképverzióból Shared Image Gallery. Ha általános rendszerképverzióval szeretne méretezési csoportokat létrehozni, tekintse meg a méretezési csoport általános rendszerképből való [létrehozásáról való lásd:](instance-generalized-image-version-cli.md).

Ha a CLI helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.4.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

Ebben a példában szükség szerint cserélje le az erőforrásneveket. 

Listsa ki a katalógusban található képdefiníciókat [az az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) használatával, hogy lássa a definíciók nevét és azonosítóját.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Hozzon létre egy méretezési készletet a paraméterrel annak jelzésére, hogy [`az vmss create`](/cli/azure/vmss#az_vmss_create) `--specialized` a rendszerkép egy specializált rendszerkép.

A rendszerképdefiníció-azonosítójával hozza létre a méretezésicsoport-példányokat a rendszerkép legújabb elérhető `--image` verziójából. A méretezési csoport példányait egy adott verzióból is létrehozhatja, ha a rendszerkép verzióazonosítóját adja meg `--image` a számára. Vegye figyelembe, hogy egy adott rendszerképverzió használata azt jelenti, hogy az automatizálás meghiúsulhat, ha az adott rendszerképverzió nem érhető el, mert törölték vagy eltávolították a régióból. Javasoljuk, hogy az új virtuális gép létrehozásához használja a rendszerképdefiníció-azonosítót, kivéve, ha egy adott rendszerképverzióra van szükség.

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
[Az Azure Image Builder (előzetes verzió)](../virtual-machines/image-builder-overview.md) segíthet automatizálni a rendszerképverzió létrehozását, sőt akár egy meglévő rendszerképverzióból is frissíthet és létrehozhat egy új [rendszerképverziót.](../virtual-machines/linux/image-builder-gallery-update-image-version.md) 

Sablonokkal is létrehozhat Shared Image Gallery erőforrást. Számos Azure gyorsindítási sablon érhető el: 

- [Shared Image Gallery létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerképverzió létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)