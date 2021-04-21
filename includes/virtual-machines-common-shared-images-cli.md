---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: fc35f277bfafa80f6239ef807f1a83591646a503
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800184"
---
## <a name="create-an-image-gallery"></a>Rendszerkép-katalógus létrehozása 

A rendszerkép-katalógus a rendszerképmegosztás engedélyezéséhez használt elsődleges erőforrás. 

A Katalógus neve megengedett karakterei a kis- és nagybetűk, számjegyek, pont és pont. A katalógus neve nem tartalmazhat kötőjeleket.   A katalógusok nevének egyedinek kell lennie az előfizetésen belül. 

Hozzon létre egy rendszerkép-katalógust [az az sig create használatával.](/cli/azure/sig#az_sig_create) A következő példában létrehozunk egy *myGalleryRG* nevű erőforráscsoportot az *USA* keleti részén, valamint egy *myGallery nevű katalógust.*

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>A katalógus megosztása

A rendszerképeket előfizetések között oszthatja meg a Role-Based Access Control (RBAC) használatával. A rendszerképeket a katalógus, a rendszerkép definíciója vagy a rendszerképverzió szintjén oszthatja meg. Minden olyan felhasználó, aki olvasási engedéllyel rendelkezik egy rendszerképverzióhoz, akár több előfizetésben is, üzembe helyezhet egy virtuális gépet a rendszerkép verziójával.

Javasoljuk, hogy a katalógus szintjén ossza meg a többi felhasználóval. A katalógus objektumazonosítóját az [az sig show használatával lehet lekérte.](/cli/azure/sig#az_sig_show)

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Használja az objektumazonosítót hatókörként, valamint egy e-mail-címet és [az az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) címet, hogy hozzáférést adjon egy felhasználónak a megosztott rendszerkép-katalógushoz. Cserélje le `<email-address>` a és `<gallery iD>` a adatokat a saját adataira.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

További információ az erőforrások RBAC használatával való megosztásáról: Hozzáférés kezelése az RBAC és az [Azure CLI használatával.](../articles/role-based-access-control/role-assignments-cli.md)