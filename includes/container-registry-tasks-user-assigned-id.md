---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 70bd79ef944e5918d750a130bd2e2b2c2b656bf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781154"
---
### <a name="create-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás létrehozása

Hozzon létre egy *myACRTasksId* nevű identitást az előfizetésben az [az identity create paranccsal.][az-identity-create] Használhatja ugyanazt az erőforráscsoportot, amelyet korábban egy tároló-beállításjegyzék létrehozásához használt, vagy egy másikat.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Ha a felhasználó által hozzárendelt identitást a következő lépésekben konfigurálja, használja az [az identity show][az-identity-show] parancsot az identitás erőforrás-azonosítójának, rendszerbiztonsági tagjának és ügyfél-azonosítójának változókban való tárolására.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
