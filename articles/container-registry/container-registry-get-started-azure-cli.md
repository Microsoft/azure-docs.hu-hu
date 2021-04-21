---
title: Rövid útmutató – Beállításjegyzék létrehozása – Azure CLI
description: Az útmutató azt ismerteti, hogyan hozhat létre egy privát Docker regisztrációs adatbázist az Azure CLI-vel.
ms.topic: quickstart
ms.date: 06/12/2020
ms.custom: seodec18, H1Hack27Feb2017, mvc, devx-track-azurecli
ms.openlocfilehash: 5c313ab43fd3dc18acf8261730686a4d6657291d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783790"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Rövid útmutató: Privát tároló-beállításjegyzék létrehozása az Azure CLI használatával

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató az Azure Container Registry-példányok Azure CLI-vel való létrehozásának módját ismerteti. Ezután Docker-parancsokkal leküld egy tároló-rendszerképet a regisztrációs adatbázisba, végül pedig leküldi és futtatja a rendszerképet a regisztrációs adatbázisból.

Ehhez a rövid útmutatóhoz az Azure CLI futtatására van szükség (2.0.55-ös vagy újabb verzió ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

Mivel az Azure Cloud Shell nem tartalmazza az összes szükséges Docker-összetevőt (a `dockerd`-démont), ehhez a rövid útmutatóhoz nem használható a Cloud Shell.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ebben a rövid útmutatóban egy *Alapszintű* beállításjegyzéket hoz létre, amely egy költségoptimalált lehetőség a fejlesztők számára a Azure Container Registry. Az elérhető szolgáltatásszintekkel kapcsolatos részletekért lásd: [Container Registry service tiers (Tároló-beállításjegyzék szolgáltatási rétegei).][container-registry-skus]

Hozzon létre egy ACR-példányt az [az acr create][az-acr-create] paranccsal. A tárolóregisztrációs adatbázis nevének egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában a *myContainerRegistry007* nevet használjuk. Ezt cserélje le egy egyedi értékre.

```azurecli
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry007 --sku Basic
```

A tárolóregisztrációs adatbázis létrehozásakor a kimenet a következő példához hasonló:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
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

Jegyezze fel a kimenetet, amely a regisztrációs adatbázis teljes `loginServer` neve (csak kisbetűk). A rövid útmutató további részében a tárolójegyzék nevének helyőrzője, a tárolójegyzék bejelentkezési kiszolgálójának neve pedig `<registry-name>` `<login-server>` helyőrzője.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

Mielőtt lekérte és lekérte a tároló rendszerképeket, be kell jelentkeznie a regisztrációs adatbázisba. Ehhez használja az [az acr login][az-acr-login] parancsot. Az Azure CLI-val való bejelentkezéskor csak a beállításjegyzék nevét adja meg. Ne használja a bejelentkezési kiszolgáló nevét, amely olyan tartomány-utótagot tartalmaz, mint `azurecr.io` a . 

```azurecli
az acr login --name <registry-name>
```

Példa:

```azurecli
az acr login --name mycontainerregistry
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tárolórendszerképek listázása

Az alábbi példa felsorolja a regisztrációs adatbázisban található adattárakat:

```azurecli
az acr repository list --name <registry-name> --output table
```

Kimenet:

```
Result
----------------
hello-world
```

Az alábbi példa a **hello-world** adattár címkéit sorolja fel.

```azurecli
az acr repository show-tags --name <registry-name> --repository hello-world --output table
```

Kimenet:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete][az-group-delete] paranccsal eltávolíthatja az erőforráscsoportot, a tároló-beállításjegyzéket és az ott tárolt tároló rendszerképeket.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy virtuális Azure Container Registry az Azure CLI-lel, leküldte a tároló rendszerképét a regisztrációs adatbázisba, majd lekérte és futtatta a rendszerképet a regisztrációs adatbázisból. Folytassa az Azure Container Registry oktatóanyagokkal az ACR mélyebb betekeredőbb leírásában.

> [!div class="nextstepaction"]
> [Azure Container Registry oktatóanyagok][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry-feladatok oktatóanyagok][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
