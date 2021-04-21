---
title: Hozzáférés korlátozása szolgáltatásvégpont használatával
description: Az Azure Container Registryhez való hozzáférés korlátozása egy Azure-beli virtuális hálózat szolgáltatásvégpontjának használatával. A szolgáltatásvégpont-hozzáférés a Prémium szolgáltatási szint egyik funkciója.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 8a67a011c75a192df9ad3460458fd766b5ec1ec1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773465"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Tároló-beállításjegyzékhez való hozzáférés korlátozása egy Azure-beli virtuális hálózat szolgáltatásvégpontjának használatával

[Az Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonságos magánhálózatot biztosít az Azure-beli és a helyszíni erőforrások számára. A [szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) lehetővé teszi, hogy a tároló-beállításjegyzék nyilvános IP-címét csak a virtuális hálózat számára biztosítsa. Ez a végpont optimális útvonalat biztosít az erőforráshoz az Azure gerinchálózatán keresztül. A rendszer minden kéréssel továbbítja a virtuális hálózat és az alhálózat identitását is.

Ez a cikk bemutatja, hogyan konfigurálható egy tároló-beállításjegyzék szolgáltatásvégpontja (előzetes verzió) egy virtuális hálózaton. 

> [!IMPORTANT]
> Azure Container Registry támogatja a [Azure Private Link,](container-registry-private-link.md)így a virtuális hálózatból származó privát végpontok elhelyezhetőek egy beállításjegyzékben. A privát végpontok a virtuális hálózaton belülről érhetők el magánhálózati IP-címek használatával. A legtöbb hálózati forgatókönyvben szolgáltatásvégpont helyett privát végpontok használatát javasoljuk.

A beállításjegyzék-szolgáltatásvégpont konfigurálása a **Prémium** tároló-beállításjegyzék szolgáltatásszinten érhető el. A beállításjegyzék szolgáltatásszintekkel és korlátozásokkal kapcsolatos további információkért lásd: Azure Container Registry [szolgáltatási szintek.](container-registry-skus.md)

## <a name="preview-limitations"></a>Az előzetes verzió korlátozásai

* A szolgáltatások szolgáltatásvégpontjainak jövőbeli fejlesztése Azure Container Registry jelenleg nem tervezünk. Javasoljuk ehelyett [privát végpontok](container-registry-private-link.md) használatát.
* A szolgáltatásvégpontokat nem Azure Portal konfigurálhatja a beállításjegyzékben.
* Csak [Azure Kubernetes Service](../aks/intro-kubernetes.md) fürt vagy [](../virtual-machines/linux/overview.md) Azure-beli virtuális gép használható gazdagépként egy tároló-beállításjegyzék szolgáltatásvégponttal való eléréséhez. *Más Azure-szolgáltatások Azure Container Instances beleértve a Azure Container Instances, nem támogatottak.*
* Az Azure US Government Azure Container Registry az Azure China-felhő nem támogatja a szolgáltatásvégpontokat.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Előfeltételek

* A cikkben található Azure CLI-lépésekhez az Azure CLI 2.0.58-as vagy újabb verziójára van szükség. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

* Ha még nem rendelkezik tároló-beállításjegyzékpel, hozzon létre egyet (prémium szint szükséges), és hozzon létre egy minta rendszerképet , például a `hello-world` Docker Hub. Regisztrációs adatbázis létrehozásához [Azure Portal][quickstart-portal] az [Azure CLI-t.][quickstart-cli] 

* Ha egy másik Azure-előfizetés szolgáltatásvégpontjával szeretné korlátozni a regisztrációs adatbázishoz való hozzáférést, regisztrálja az erőforrás-szolgáltatót Azure Container Registry előfizetésben. Például:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Hálózati hozzáférés konfigurálása beállításjegyzékhez

Ebben a szakaszban konfigurálja a tároló-beállításjegyzéket úgy, hogy engedélyezze a hozzáférést egy Azure-beli virtuális hálózat alhálózata számára. A lépések az Azure CLI használatával biztosítanak lépéseket.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Szolgáltatásvégpont hozzáadása alhálózathoz

Virtuális gép létrehozásakor az Azure alapértelmezés szerint létrehoz egy virtuális hálózatot ugyanabban az erőforráscsoportban. A virtuális hálózat neve a virtuális gép nevén alapul. Ha például a virtuális gépnek a *myDockerVM* nevet ad, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, egy *myDockerVMSubnet nevű alhálózattal.* Ezt az [az network vnet list paranccsal ellenőrizheti:][az-network-vnet-list]

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

Az [az network vnet subnet update paranccsal][az-network-vnet-subnet-update] adjon hozzá egy **Microsoft.ContainerRegistry** szolgáltatásvégpontot az alhálózathoz. A következő parancsban helyettesítse be a virtuális hálózat és az alhálózat nevét:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Az [az network vnet subnet show paranccsal][az-network-vnet-subnet-show] lekérhető az alhálózat erőforrás-azonosítója. Erre egy későbbi lépésben szüksége lesz egy hálózati hozzáférési szabály konfigurálásához.

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

Alapértelmezés szerint az Azure Container Registry bármilyen hálózaton található gazdagépről engedélyezi a kapcsolatokat. A kijelölt hálózathoz való hozzáférés korlátozásához módosítsa az alapértelmezett műveletet a hozzáférés megtagadása műveletre. Helyettesítse be a regisztrációs adatbázis nevét a következő [az acr update parancsban:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Hálózati szabály hozzáadása a beállításjegyzékhez

Az [az acr network-rule add paranccsal][az-acr-network-rule-add] adjon hozzá egy hálózati szabályt a beállításjegyzékhez, amely engedélyezi a hozzáférést a virtuális gép alhálózatán. A következő parancsban helyettesítse be a tároló-beállításjegyzék nevét és az alhálózat erőforrás-azonosítóját: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>A beállításjegyzékhez való hozzáférés ellenőrzése

Néhány perc várakozás után, amíg a konfiguráció frissül, ellenőrizze, hogy a virtuális gép hozzáfér-e a tároló-beállításjegyzékhez. Létesítsen SSH-kapcsolatot a virtuális géphez, és futtassa [az az acr login][az-acr-login] parancsot a regisztrációs adatbázisba való bejelentkezéshez. 

```bash
az acr login --name mycontainerregistry
```

A beállításjegyzékben olyan műveleteket hajthat végre, mint például a futtatás egy `docker pull` minta rendszerképnek a beállításjegyzékből való lekért futtatásához. Helyettesítse be a beállításjegyzéknek megfelelő rendszerképet és címkeértéket a beállításjegyzék bejelentkezési kiszolgálójának nevével előtaggal (csak kisbetűs):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

A Docker sikeresen leküldi a rendszerképet a virtuális gépre.

Ez a példa bemutatja, hogy a privát tárolójegyzék a hálózati hozzáférési szabályon keresztül is elérhető. A beállításjegyzék azonban nem érhető el olyan bejelentkezési gazdagépről, amely nem rendelkezik konfigurált hálózati hozzáférési s szabálysal. Ha az paranccsal vagy paranccsal próbál bejelentkezni egy másik gazdagépről, a kimenet `az acr login` `docker login` az alábbihoz hasonló lesz:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Alapértelmezett beállításjegyzék-hozzáférés visszaállítása

Ha a beállításjegyzéket úgy szeretné visszaállítani, hogy alapértelmezés szerint engedélyezze a hozzáférést, távolítsa el a konfigurált hálózati szabályokat. Ezután állítsa be az alapértelmezett műveletet úgy, hogy engedélyezze a hozzáférést. 

### <a name="remove-network-rules"></a>Hálózati szabályok eltávolítása

A beállításjegyzékhez konfigurált hálózati szabályok listájának listájáért futtassa a következő [az acr network-rule list parancsot:][az-acr-network-rule-list]

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Minden konfigurált szabály esetében futtassa az [az acr network-rule remove parancsot][az-acr-network-rule-remove] az eltávolításához. Például:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Hozzáférés engedélyezése

Helyettesítse be a regisztrációs adatbázis nevét a következő [az acr update parancsban:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az összes Azure-erőforrást ugyanabban az erőforráscsoportban hozta létre, és már nincs rájuk szüksége, törölheti az erőforrásokat egyetlen [az group delete paranccsal:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

* A regisztrációs adatbázishoz való hozzáférés virtuális hálózaton lévő privát végpont használatával való korlátozásához lásd: Configure Azure Private Link for an Azure Container Registry (Regisztrációs adatbázis konfigurálása [Azure-beli tároló-beállításjegyzékhez).](container-registry-private-link.md)
* Ha egy ügyfél tűzfal mögötti beállításjegyzék-hozzáférési szabályokat kell beállítania, tekintse meg a következőt: Configure rules to access an Azure Container Registry behind a firewall (Szabályok konfigurálása tűzfal mögötti [Azure Container Registry eléréséhez).](container-registry-firewall-access-rules.md)


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
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
