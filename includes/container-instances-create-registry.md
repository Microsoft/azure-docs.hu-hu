---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 8d4178ea1f6f12def938dfc91e7ae0e6ee3b738c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786916"
---
## <a name="create-azure-container-registry"></a>Azure Container Registry létrehozása

A tárolójegyzék létrehozásához szüksége lesz egy *erőforráscsoportra*, amelyben üzembe helyezheti azt. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az összes Azure-erőforrást.

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. A következő példában egy *myResourceGroup* nevű erőforráscsoportot hozunk létre az *eastus* régióban:

```azurecli
az group create --name myResourceGroup --location eastus
```

Az erőforráscsoport létrehozása után hozzon létre egy Azure Container Registryt az [az acr create][az-acr-create] paranccsal. A tárolóregisztrációs adatbázis nevének egyedinek kell lennie az Azure rendszerében, és 5–50 alfanumerikus karakterből kell állnia. Cserélje le az `<acrName>` elemet az adatbázis egyedi nevére:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Az új Azure Container Registry *mycontainerregistry082* nevű részleges kimenete:

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Az oktatóanyag hátralevő részében az `<acrName>` helyettesíti a tárolójegyzék ebben a lépésben választott nevét.

## <a name="log-in-to-container-registry"></a>Bejelentkezés a tárolójegyzékbe

Először be kell jelentkeznie az Azure Container Registrybe, mielőtt rendszerképeket küldhetne a tárolóregisztrációs adatbázisba. Használja az [az acr login][az-acr-login] parancsot a művelet befejezéséhez. Meg kell adnia a tárolójegyzék egyedi nevét, amelyet az adatbázis létrehozásakor választott.

```azurecli
az acr login --name <acrName>
```

Például:

```azurecli
az acr login --name mycontainerregistry082
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött:

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
