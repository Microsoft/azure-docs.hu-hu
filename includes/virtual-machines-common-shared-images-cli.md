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
ms.openlocfilehash: 9556b20ba0ceac2d4c1ad92897e6f9d46293387f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026788"
---
## <a name="create-an-image-gallery"></a>Rendszerkép-gyűjtemény létrehozása 

A képgyűjtemény a képmegosztás engedélyezéséhez használt elsődleges erőforrás. 

A katalógus nevének megengedett karaktere nagybetűs vagy kisbetűk, számjegyek, pontok és időszakok. A gyűjtemény neve nem tartalmazhat kötőjeleket.   A katalógus nevének egyedinek kell lennie az előfizetésen belül. 

Hozzon létre egy képtárat [az az SIG Create](/cli/azure/sig#az-sig-create)paranccsal. A következő példában létrehozunk egy *myGalleryRG* nevű ERŐFORRÁSCSOPORTOT az *USA keleti* régiójában, valamint egy *MyGallery* nevű katalógust.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>A katalógus megosztása

Role-Based Access Control (RBAC) használatával megoszthatja a lemezképeket az előfizetések között. A képeket megoszthatja a katalógusban, a képdefinícióban vagy a lemezkép verzió szintjén. Minden olyan felhasználó, aki olvasási engedéllyel rendelkezik a lemezkép verziójához, még az előfizetések között is, a lemezkép verziója segítségével telepítheti a virtuális gépet.

Javasoljuk, hogy a katalógus szintjén ossza meg más felhasználókkal. A katalógus objektum-AZONOSÍTÓjának lekéréséhez használja az [az SIG show](/cli/azure/sig#az-sig-show)lehetőséget.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Használja az objektumazonosító hatókörként, valamint egy e-mail-cím és [az az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) lehetőséget, hogy a felhasználók hozzáférhessenek a megosztott képgyűjteményhez. Cserélje le `<email-address>` a és a értékét `<gallery iD>` a saját adataira.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Az erőforrások RBAC használatával történő megosztásával kapcsolatos további információkért lásd: [hozzáférés kezelése a RBAC és az Azure CLI használatával](../articles/role-based-access-control/role-assignments-cli.md).