---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6432c125c4fedd962faa28a4c84c7494300b0472
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92755736"
---
## <a name="list-information"></a>Információk listázása

A rendelkezésre álló képtárak helyének, állapotának és egyéb információinak lekérése az az [SIG List](/cli/azure/sig#az-sig-list)paranccsal.

```azurecli-interactive 
az sig list -o table
```

Egy katalógus képdefinícióinak listázása, beleértve az operációs rendszer típusával és állapotával kapcsolatos információkat az [az SIG rendszerkép-definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list)paranccsal.

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

A katalógusban szereplő megosztott rendszerkép-verziók listázása az [az SIG rendszerkép-Version List](/cli/azure/sig/image-version#az-sig-image-version-list)paranccsal.

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Szerezze be egy rendszerkép-verzió AZONOSÍTÓját az [az SIG rendszerkép-Version show](/cli/azure/sig/image-version#az-sig-image-version-show)paranccsal.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
