---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 4392e7f146f13e581f722b94f13038ad8abff0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244869"
---
## <a name="update-resources"></a>Erőforrások frissítése

Bizonyos korlátozások vonatkoznak a frissítésre. A következő elemek frissíthetők: 

Megosztott képgyűjtemény:
- Leírás

Rendszerkép definíciója:
- Ajánlott vCPU
- Ajánlott memória
- Leírás
- Élettartam vége

Rendszerkép verziója:
- Regionális replika száma
- Célcsoportok
- Legutóbbi kizárás
- Élettartam vége

Ha a replika-régiók hozzáadását tervezi, ne törölje a forrás által felügyelt képet. A forrás által felügyelt rendszerkép a rendszerkép verziójának további régiókba való replikálásához szükséges. 

Frissítse a katalógus leírását az ([az SIG Update](/cli/azure/sig#az-sig-update)) használatával. 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Frissítse a rendszerkép definíciójának leírását az [az SIG rendszerkép-definition Update](/cli/azure/sig/image-definition#az-sig-image-definition-update)paranccsal.

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Frissítsen egy rendszerkép-verziót, és adjon hozzá egy régiót a replikáláshoz az [az SIG rendszerkép-Version Update](/cli/azure/sig/image-definition#az-sig-image-definition-update)használatával. Ez a változás hosszabb időt vesz igénybe, amíg a rendszerkép replikálása az új régióba történik.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

Ez a példa azt mutatja be, hogyan használható az az [SIG lemezkép-Version Update](/cli/azure/sig/image-definition#az-sig-image-definition-update) , hogy kizárja ezt a lemezkép-verziót a *legújabb* rendszerképként való használatból.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

Ez a példa azt mutatja be, hogyan használható az [az SIG lemezkép-Version Update](/cli/azure/sig/image-definition#az-sig-image-definition-update) , hogy a lemezkép verziója szerepeljen a *legújabb* rendszerképben.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Erőforrások törlése

Az erőforrásokat fordított sorrendben kell törölnie, ha először törli a rendszerkép verzióját. Az összes rendszerkép-verzió törlése után törölheti a rendszerkép definícióját. Az összes rendszerkép-definíció törlését követően törölheti a katalógust. 

Rendszerkép verziójának törlése [az az SIG rendszerkép-Version delete](/cli/azure/sig/image-version#az-sig-image-version-delete)paranccsal.

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

A rendszerkép definíciójának törlése [az az SIG rendszerkép-definition delete](/cli/azure/sig/image-definition#az-sig-image-definition-delete)paranccsal.

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Töröljön egy képtárat az [az SIG delete](/cli/azure/sig#az-sig-delete)paranccsal.

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```