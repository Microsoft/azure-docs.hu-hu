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
ms.openlocfilehash: b966f68e19794aadebff76e3e9b29ed79a32eebe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800155"
---
## <a name="update-resources"></a>Erőforrások frissítése

A frissíthető adatokra vonatkozik néhány korlátozás. A következő elemek frissíthetők: 

Megosztott rendszerkép-katalógus:
- Description

Képdefiníció:
- Ajánlott vCPU-k
- Ajánlott memória
- Description
- Az életciklus végének dátuma

Rendszerkép verziója:
- Regionális replikaszám
- Célrégió
- Kizárás a legutóbbiból
- Az életciklus végének dátuma

Ha replikarégió hozzáadását tervezi, ne törölje a forrásként felügyelt rendszerképet. A forrásként felügyelt rendszerképre szükség van a rendszerkép verziójának további régiókba való replikálása érdekében. 

Frissítse a katalógus leírását a következővel: ([az sig update](/cli/azure/sig#az_sig_update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Frissítse egy képdefiníció leírását [az az sig image-definition update használatával.](/cli/azure/sig/image-definition#az_sig_image_definition_update)

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Frissítsen egy rendszerképverziót, és adjon hozzá egy régiót, amelybe replikálni szeretne [az az sig image-version update használatával.](/cli/azure/sig/image-definition#az_sig_image_definition_update) Ez a módosítás egy kis ideig tart, amíg a rendszerkép replikálódik az új régióba.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

Ez a példa bemutatja, hogyan zárhatja ki ezt a rendszerképverziót az [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) használatával a legújabb *rendszerképként való használatból.*

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

Ez a példa bemutatja, hogyan használható [az az sig image-version update,](/cli/azure/sig/image-definition#az_sig_image_definition_update) hogy tartalmazza ezt a rendszerképverziót a legújabb *rendszerképhez.*

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Erőforrások törlése

Az erőforrásokat fordított sorrendben kell törölnie úgy, hogy először a rendszerkép verzióját törli. Az összes rendszerképverzió törlése után törölheti a rendszerkép definícióját. Az összes képdefiníció törlése után törölheti a katalógust. 

Töröljön egy rendszerképverziót [az az sig image-version delete parancs használatával.](/cli/azure/sig/image-version#az_sig_image_version_delete)

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Töröljön egy képdefiníciót [az az sig image-definition delete parancs használatával.](/cli/azure/sig/image-definition#az_sig_image_definition_delete)

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Rendszerkép-katalógus törlése [az az sig delete parancs használatával.](/cli/azure/sig#az_sig_delete)

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```