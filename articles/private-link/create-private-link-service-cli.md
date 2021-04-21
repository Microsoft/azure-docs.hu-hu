---
title: Rövid útmutató – Azure Private Link létrehozása az Azure CLI használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Private Link szolgáltatást az Azure CLI használatával
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: c8e32a56148326104c3514b8a2fdb5d6bbd3f00a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778468"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Rövid útmutató: Private Link szolgáltatás létrehozása az Azure CLI használatával

Első lépések a Private Link szolgáltatásra hivatkozó szolgáltatás létrehozásához.  Hozzáférést Private Link Azure-beli virtuális gép mögött üzembe helyezett szolgáltatáshoz vagy erőforráshoz standard Load Balancer.  A szolgáltatás felhasználói privát hozzáféréssel rendelkezik a virtuális hálózatukról.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot [az az group create segítségével:](/cli/azure/group#az_group_create)

* **CreatePrivLinkService-rg névvel.** 
* Az **eastus helyen.**

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Ebben a szakaszban egy virtuális hálózatot és egy belső virtuális hálózatot fog Azure Load Balancer.

### <a name="virtual-network"></a>Virtuális hálózat

Ebben a szakaszban egy virtuális hálózatot és alhálózatot hoz létre a terheléselosztási szolgáltatáshoz hozzáférő Private Link számára.

Hozzon létre egy virtuális hálózatot [az az network vnet create használatával:](/cli/azure/network/vnet#az_network_vnet_create)

* A **neve myVNet.**
* A **10.1.0.0/16 címelőtagja.**
* A **mySubnet nevű alhálózat.**
* A **10.1.0.0/24 alhálózati előtag.**
* A **CreatePrivLinkService-rg erőforráscsoportban.**
* Az **eastus2 helye.**
* Tiltsa le a privát kapcsolati szolgáltatás hálózati szabályzatát az alhálózaton.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Ha frissíteni szeretné az alhálózatot a privát kapcsolati szolgáltatás hálózati szabályzatának letiltásához, használja [az az network vnet subnet update et:](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Standard terheléselosztási rendszer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtere IP-címkészlet, amely a terheléselosztáson fogadja a bejövő hálózati forgalmat.
  * Egy háttér-IP-címkészlet, ahol az előterekészlet az elosztott terhelésű hálózati forgalmat küldi.
  * Állapot-mintavétel, amely meghatározza a háttér virtuálisgép-példányok állapotát.
  * Egy terheléselosztási szabály, amely meghatározza, hogyan oszlik el a forgalom a virtuális gépek között.

### <a name="create-the-load-balancer-resource"></a>A terheléselosztási erőforrás létrehozása

Hozzon létre egy nyilvános terheléselosztást [az az network lb create segítségével:](/cli/azure/network/lb#az_network_lb_create)

* A **neve myLoadBalancer.**
* Egy **myFrontEnd** nevű előterekészlet.
* Egy **myBackEndPool nevű háttérkészlet.**
* A myVNet virtuális hálózathoz **van társítva.**
* A **mySubnet háttér-alhálózathoz van társítva.**

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapotfigyelő mintavétel az összes virtuálisgép-példányt ellenőrzi, hogy biztos lehet-e a hálózati forgalom elküldését. 

A rendszer eltávolítja a virtuális gépet a terheléselosztásból, amelynél a mintavétel ellenőrzése sikertelen volt. A hiba megoldása után a virtuális gép vissza lesz adva a terheléselosztásba.

Hozzon létre egy állapot-mintavételt [az az network lb probe create segítségével:](/cli/azure/network/lb/probe#az_network_lb_probe_create)

* Figyeli a virtuális gépek állapotát.
* A **neve myHealthProbe.**
* TCP **protokoll**.
* A **80-as port figyelése.**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztási szabályok a következőt határozzák meg:

* A bejövő forgalom előtere IP-konfigurációja.
* A forgalom fogadására a háttér-IP-készlet.
* A szükséges forrás- és célport. 

Hozzon létre egy terheléselosztási szabályt [az az network lb rule create segítségével:](/cli/azure/network/lb/rule#az_network_lb_rule_create)

* **MyHTTPRule nevű**
* A **myFrontEnd** előterekészlet **80-as** portján figyel.
* Elosztott terhelésű hálózati forgalom küldése a **myBackEndPool** háttércímkészletbe a **80-as port használatával.** 
* A **myHealthProbe állapot-mintavétel használata.**
* TCP **protokoll**.
* Üresjárati időkorlát **15 perc.**
* Engedélyezze a TCP-visszaállítást.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása

Ebben a szakaszban egy privát kapcsolati szolgáltatást hozunk létre, amely az előző Azure Load Balancer létrehozott kapcsolatot használja.

Hozzon létre egy privát kapcsolati szolgáltatást standard terheléselosztási előtere IP-konfigurációval az [az network private-link-service create használatával:](/cli/azure/network/private-link-service#az_network_private_link_service_create)

* A **neve myPrivateLinkService.**
* A **myVNet virtuális hálózatban.**
* A **myLoadBalancer** standard terheléselosztási eszközhöz és a **myFrontEnd előterekonfigurációhoz van társítva.**
* Az **eastus2 helyen.**
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

Létrejön a privát kapcsolati szolgáltatás, és fogadni tudja a forgalmat. Ha látni szeretné a forgalom áramlását, konfigurálja az alkalmazást a standard terheléselosztási rendszer mögött.


## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban egy privát végpontra fogja leképezni a privát kapcsolati szolgáltatást. A virtuális hálózat tartalmazza a privát kapcsolati szolgáltatás privát végpontját. Ez a virtuális hálózat tartalmazza azokat az erőforrásokat, amelyek hozzáférhetnek a privát kapcsolati szolgáltatáshoz.

### <a name="create-private-endpoint-virtual-network"></a>Privát végpont virtuális hálózatának létrehozása

Hozzon létre egy virtuális hálózatot [az az network vnet create használatával:](/cli/azure/network/vnet#az_network_vnet_create)

* A **neve myVNetPE.**
* A **11.1.0.0/16 címelőtagja.**
* A **mySubnetPE nevű alhálózat.**
* A **11.1.0.0/24 alhálózati előtag.**
* A **CreatePrivLinkService-rg** erőforráscsoportban.
* Az **eastus2 helye.**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Ha frissíteni szeretné az alhálózatot a privát végpont hálózati szabályzatának letiltásához, használja [az az network vnet subnet update et:](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Végpont és kapcsolat létrehozása

* Az [az network private-link-service show használatával](/cli/azure/network/private-link-service#az_network_private_link_service_show) lekérte a privát kapcsolati szolgáltatás erőforrás-azonosítóját. A parancs elhelyezi az erőforrás-azonosítót egy változóban későbbi használatra.

* Az [az network private-endpoint create használatával](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) hozza létre a privát végpontot a korábban létrehozott virtuális hálózatban.

* **MyPrivateEndpoint névvel.**
* A **CreatePrivLinkService-rg erőforráscsoportban.**
* Kapcsolat **neve: myPEconnectiontoPLS.**
* Az **eastus2 helye.**
* A **myVNetPE virtuális** hálózatban és a **mySubnetPE alhálózatban.**

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal távolítsa el az erőforráscsoportot, a privát kapcsolati szolgáltatást, a terheléselosztást és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőket hajtja végre:

* Létrehozott egy virtuális hálózatot és egy belső Azure Load Balancer.
* Privát kapcsolati szolgáltatás létrehozása

Ha többet szeretne megtudni az Azure privát végpontról, folytassa a következővel:
> [!div class="nextstepaction"]
> [Rövid útmutató: Privát végpont létrehozása az Azure CLI használatával](create-private-endpoint-cli.md)
