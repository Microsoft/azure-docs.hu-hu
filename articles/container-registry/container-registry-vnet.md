---
title: Hozzáférés korlátozása szolgáltatás-végpont használatával
description: Az Azure Container Registry elérésének korlátozása egy Azure-beli virtuális hálózat szolgáltatási végpontjának használatával. A szolgáltatás-végponti hozzáférés a prémium szintű szolgáltatási szint egyik funkciója.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 5f9bc7c9a6c8f2061765510a6396611502fd4a2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93026224"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Tároló-beállításjegyzékhez való hozzáférés korlátozása egy Azure-beli virtuális hálózat szolgáltatási végpontjának használatával

Az [azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonságos, privát hálózatkezelést biztosít az Azure-hoz és a helyszíni erőforrásokhoz. A [szolgáltatás-végpont](../virtual-network/virtual-network-service-endpoints-overview.md) lehetővé teszi, hogy a tároló-beállításjegyzék nyilvános IP-címét csak a virtuális hálózatra védje. Ez a végpont optimális útvonalat biztosít az erőforrásnak az Azure gerinc hálózaton keresztül. A virtuális hálózat és az alhálózat identitásait is továbbítjuk az egyes kérésekhez.

Ez a cikk bemutatja, hogyan konfigurálható egy tároló beállításjegyzék-szolgáltatási végpontja (előzetes verzió) egy virtuális hálózatban. 

> [!IMPORTANT]
> A Azure Container Registry mostantól támogatja az [Azure Private-hivatkozást](container-registry-private-link.md), amely lehetővé teszi, hogy a virtuális hálózatról származó magánhálózati végpontok egy beállításjegyzékbe kerüljenek. A magánhálózati végpontok a virtuális hálózaton belül, magánhálózati IP-címek használatával érhetők el. Javasoljuk, hogy a legtöbb hálózati forgatókönyvben ne használjon privát végpontokat a szolgáltatási végpontok helyett.

A beállításjegyzék-szolgáltatási végpontok konfigurálása a **Premium** Container Registry szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry szolgáltatási szintek](container-registry-skus.md).

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* A Azure Container Registry szolgáltatási végpontjának jövőbeni fejlesztése jelenleg nem tervezett. Azt javasoljuk, hogy ehelyett használjon [privát végpontokat](container-registry-private-link.md) .
* Nem használhatja a Azure Portal a szolgáltatási végpontok konfigurálására a beállításjegyzékben.
* Csak egy [Azure Kubernetes Service](../aks/intro-kubernetes.md) -fürt vagy Azure-beli [virtuális gép](../virtual-machines/linux/overview.md) használható gazdagépként egy tároló-beállításjegyzék eléréséhez szolgáltatási végpont használatával. *Más Azure-szolgáltatások, többek között a Azure Container Instances nem támogatottak.*
* Mindegyik beállításjegyzék legfeljebb 100 hálózati hozzáférési szabályt támogat.
* A Azure Container Registry szolgáltatásbeli végpontok nem támogatottak az Azure US government-felhőben vagy az Azure China Cloud-ban.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Előfeltételek

* A cikkben szereplő Azure CLI-lépések használatához az Azure CLI 2.0.58 vagy újabb verziójára van szükség. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

* Ha még nem rendelkezik tároló-beállításjegyzékkel, hozzon létre egyet (prémium szintű csomag szükséges), és küldjön le egy minta képet, például `hello-world` a Docker hub-ból. A beállításjegyzék létrehozásához például használja az [Azure Portal][quickstart-portal] vagy az [Azure CLI][quickstart-cli] -t. 

* Ha egy másik Azure-előfizetésben lévő szolgáltatás-végponttal szeretné korlátozni a beállításjegyzék-hozzáférést, regisztrálja az erőforrás-szolgáltatót az előfizetésben lévő Azure Container Registryhoz. Például:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Hálózati hozzáférés konfigurálása a beállításjegyzékhez

Ebben a szakaszban úgy konfigurálja a tároló-beállításjegyzéket, hogy engedélyezze a hozzáférést egy Azure-beli virtuális hálózat alhálózatán. A lépéseket az Azure CLI használatával biztosítjuk.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Szolgáltatási végpont hozzáadása egy alhálózathoz

Amikor létrehoz egy virtuális GÉPET, az Azure alapértelmezés szerint ugyanahhoz az erőforráscsoporthoz hoz létre egy virtuális hálózatot. A virtuális hálózat neve a virtuális gép nevén alapul. Ha például a virtuális gép *myDockerVM* nevezi el, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, és egy *myDockerVMSubnet* nevű alhálózattal rendelkezik. Ennek ellenőrzéséhez használja az az [Network vnet List][az-network-vnet-list] parancsot:

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

Kimenet:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Az az [Network vnet subnet Update][az-network-vnet-subnet-update] paranccsal adhat hozzá **Microsoft. ContainerRegistry** szolgáltatási végpontot az alhálózathoz. Helyettesítse be a virtuális hálózat és az alhálózat nevét a következő parancsban:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Az alhálózat erőforrás-AZONOSÍTÓjának lekéréséhez használja az az [Network vnet subnet show][az-network-vnet-subnet-show] parancsot. Ezt egy későbbi lépésben kell konfigurálnia egy hálózati hozzáférési szabály konfigurálásához.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Kimenet:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="change-default-network-access-to-registry"></a>A beállításjegyzék alapértelmezett hálózati hozzáférésének módosítása

Alapértelmezés szerint az Azure Container Registry lehetővé teszi, hogy bármely hálózaton a gazdagépek kapcsolatai legyenek. A kiválasztott hálózathoz való hozzáférés korlátozásához módosítsa az alapértelmezett műveletet, hogy megtagadja a hozzáférést. Helyettesítse be a beállításjegyzék nevét a következő az [ACR Update][az-acr-update] paranccsal:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Hálózati szabály hozzáadása a beállításjegyzékhez

Az az [ACR Network-Rule Add][az-acr-network-rule-add] paranccsal adhat hozzá egy hálózati szabályt a beállításjegyzékhez, amely lehetővé teszi a virtuális gép alhálózatának elérését. Helyettesítse be a tároló beállításjegyzékének nevét és az alhálózat erőforrás-AZONOSÍTÓját a következő parancsban: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>A beállításjegyzékhez való hozzáférés ellenőrzése

Néhány perc várakozás után a konfiguráció frissítését követően ellenőrizze, hogy a virtuális gép hozzáférhet-e a tároló-beállításjegyzékhez. Létesítsen SSH-kapcsolatokat a virtuális géppel, és futtassa az az [ACR login][az-acr-login] parancsot a beállításjegyzékbe való bejelentkezéshez. 

```bash
az acr login --name mycontainerregistry
```

A beállításjegyzékből olyan rendszerleíróadatbázis-műveleteket hajthat végre, mint például a Futtatás `docker pull` . Helyettesítse be a beállításjegyzékbe a rendszerkép és a címke értékét, a beállításjegyzék bejelentkezési kiszolgálójának nevével (az összes kisbetűs) együtt:

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

A Docker sikeresen lekéri a rendszerképet a virtuális gépre.

Ez a példa azt mutatja be, hogy a magánhálózati hozzáférési szabály használatával férhet hozzá a Private Container registryhez. A beállításjegyzék azonban nem érhető el olyan bejelentkezési gazdagépről, amelyhez nincs konfigurálva hálózati hozzáférési szabály. Ha a parancs vagy parancs használatával próbál bejelentkezni egy másik gazdagépről `az acr login` `docker login` , a kimenet a következőhöz hasonló:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Alapértelmezett beállításjegyzék-hozzáférés visszaállítása

Ha a beállításjegyzéket úgy szeretné visszaállítani, hogy alapértelmezés szerint engedélyezze a hozzáférést, távolítsa el a konfigurált hálózati szabályokat. Ezután állítsa be az alapértelmezett műveletet a hozzáférés engedélyezéséhez. 

### <a name="remove-network-rules"></a>Hálózati szabályok eltávolítása

A beállításjegyzékhez konfigurált hálózati szabályok listájának megtekintéséhez futtassa a következőt az [ACR Network-Rule List][az-acr-network-rule-list] paranccsal:

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Minden konfigurált szabályhoz futtassa az az [ACR Network-Rule Remove][az-acr-network-rule-remove] parancsot az eltávolításához. Például:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Hozzáférés engedélyezése

Helyettesítse be a beállításjegyzék nevét a következő az [ACR Update][az-acr-update] paranccsal:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az összes Azure-erőforrást ugyanabban az erőforráscsoporthoz hozta létre, és már nincs rájuk szükség, akkor az erőforrásokat egyetlen [az Group delete](/cli/azure/group) paranccsal törölheti:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

* Ha egy virtuális hálózat privát végpontján keresztül szeretné korlátozni a beállításjegyzékhez való hozzáférést, tekintse meg az [Azure Private-hivatkozás konfigurálása Azure Container registryhez](container-registry-private-link.md)című témakört.
* Ha be kell állítania a beállításjegyzék-hozzáférési szabályokat az ügyfél tűzfala mögött, tekintse meg a [szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez](container-registry-firewall-access-rules.md)című témakört.


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
