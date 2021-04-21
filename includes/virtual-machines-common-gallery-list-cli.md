---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 83d70a8d4c5806120ddb4ea776a8f4a6f7e63857
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799948"
---
## <a name="list-information"></a>Listainformációk

Az az sig list használatával lekérhetők az elérhető rendszerkép-katalógusok helye, [állapota és egyéb adatai.](/cli/azure/sig#az_sig_list)

```azurecli-interactive 
az sig list -o table
```

Az [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list)használatával listába sorolja a katalógusban található rendszerkép-definíciókat, beleértve az operációs rendszer típusával és állapotával kapcsolatos információkat.

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Listába sorolja egy katalógus megosztott rendszerkép-verzióit [az az sig image-version list használatával.](/cli/azure/sig/image-version#az_sig_image_version_list)

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Szerezze be egy rendszerképverzió azonosítóját [az az sig image-version show használatával.](/cli/azure/sig/image-version#az_sig_image_version_show)

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
