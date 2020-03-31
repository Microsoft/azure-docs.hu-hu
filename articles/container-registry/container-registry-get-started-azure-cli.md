---
title: Rövid útmutató – Rendszerleíró adatbázis létrehozása – Azure CLI
description: Az útmutató azt ismerteti, hogyan hozhat létre egy privát Docker regisztrációs adatbázist az Azure CLI-vel.
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 551a3659feb39943c9f794484abb6f2da4367f39
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74455168"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Rövid útmutató: Hozzon létre egy privát tároló beállításjegyzéket az Azure CLI használatával

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató az Azure Container Registry-példányok Azure CLI-vel való létrehozásának módját ismerteti. Ezután a Docker-parancsok használatával leküldéses egy tárolórendszerképet a rendszerleíró adatbázisba, és végül lekéri és futtatja a rendszerképet a rendszerleíró adatbázisból.

Ez a rövid útmutató megköveteli, hogy az Azure CLI (2.0.55-ös vagy újabb verzió ajánlott) fut. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

Mivel az Azure Cloud Shell nem tartalmazza az összes szükséges Docker-összetevőt (a `dockerd`-démont), ehhez a rövid útmutatóhoz nem használható a Cloud Shell.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben a rövid útmutatóban hozzon létre egy *alapszintű* beállításjegyzéket, amely egy költségoptimalizált lehetőség a fejlesztők számára, hogy megismerjék az Azure Container Registry-t. Az elérhető szolgáltatásszintekről a [Container registry ska-k][container-registry-skus]ban talál további részleteket.

Hozzon létre egy ACR-példányt az [az acr create][az-acr-create] paranccsal. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában a *myContainerRegistry007* nevet használjuk. Ezt cserélje le egy egyedi értékre.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
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

Vegye figyelembe `loginServer` a kimenet, amely a teljesen minősített rendszerleíró adatbázis nevét (minden kisbetű). A rövid útmutató hátralevő részében az `<acrName>` elem helyettesíti a tárolóregisztrációs adatbázis nevét.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolóképek lenyomása és behúzása előtt be kell jelentkeznie a rendszerleíró adatbázisba. Ehhez használja az [az acr login][az-acr-login] parancsot.

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tárolórendszerképek listázása

A következő példa a rendszerleíró adatbázisban lévő adattárakat sorolja fel:

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```
Result
----------------
hello-world
```

A következő példa a **címkéket a hello-world** tárház.

```azurecli
az acr repository show-tags --name <acrName> --repository hello-world --output table
```

Kimenet:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, az [az csoport törlési][az-group-delete] parancsával eltávolíthatja az erőforráscsoportot, a tároló beállításjegyzékét és az ott tárolt tárolólemezképeket.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry az Azure CLI, leadott egy tárolólemezképet a rendszerleíró adatbázisba, és lekérése és futtatta a rendszerképet a beállításjegyzékből. Folytassa az Azure Container Registry oktatóanyagokkal az ACR mélyebb megtekintéséhez.

> [!div class="nextstepaction"]
> [Azure Container Registry oktatóanyagok][container-registry-tutorial-quick-task]

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
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
