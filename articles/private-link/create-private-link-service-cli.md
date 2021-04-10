---
title: Rövid útmutató – Azure Private link szolgáltatás létrehozása az Azure CLI használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Private link Service-t az Azure CLI használatával
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 76fd959c28203132be4695031d96315f258cf53f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563064"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Rövid útmutató: privát link szolgáltatás létrehozása az Azure CLI-vel

Ismerkedjen meg a szolgáltatásra hivatkozó privát link szolgáltatás létrehozásával.  Adja meg az Azure-standard Load Balancer mögött üzembe helyezett szolgáltatáshoz vagy erőforráshoz való magánhálózati hivatkozást.  A szolgáltatás felhasználói a virtuális hálózatról privát hozzáféréssel rendelkeznek.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az_group_create)paranccsal:

* **CreatePrivLinkService-RG** névvel ellátott. 
* A **eastus** helyen.

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Ebben a szakaszban egy virtuális hálózatot és egy belső Azure Load Balancer hoz létre.

### <a name="virtual-network"></a>Virtuális hálózat

Ebben a szakaszban egy virtuális hálózatot és alhálózatot hoz létre a privát kapcsolati szolgáltatáshoz hozzáférő terheléselosztó üzemeltetéséhez.

Hozzon létre egy virtuális hálózatot [az az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal:

* Elnevezett **myVNet**.
* A **10.1.0.0/16** címnek az előtagja.
* **MySubnet** nevű alhálózat.
* A **10.1.0.0/24** alhálózati előtag.
* A **CreatePrivLinkService-RG** erőforráscsoporthoz.
* A **eastus2** helye.
* Tiltsa le a magánhálózati kapcsolati szolgáltatás hálózati házirendjét az alhálózaton.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Ha szeretné frissíteni az alhálózatot a magánhálózati kapcsolati szolgáltatás hálózati házirendjeinek letiltásához, használja az [az Network vnet subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer-resource"></a>A terheléselosztó erőforrásának létrehozása

Hozzon létre egy nyilvános Load balancert az [az Network LB Create](/cli/azure/network/lb#az-network-lb-create)paranccsal:

* Elnevezett **myLoadBalancer**.
* Egy **myFrontEnd** nevű frontend-készlet.
* Egy **myBackEndPool** nevű háttér-készlet.
* A virtuális hálózat **myVNet** társítva.
* A háttérbeli alhálózat **mySubnet** van társítva.

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

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [az Network LB Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create)paranccsal:

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP** protokoll.
* A **80**-es port figyelése.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom előtérbeli IP-konfigurációja.
* A háttérbeli IP-készlet a forgalom fogadásához.
* A szükséges forrás-és célport. 

Terheléselosztó-szabály létrehozása az [az Network LB Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create)paranccsal:

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd**.
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton** keresztül. 
* A Health mintavételi **myHealthProbe** használata.
* **TCP** protokoll.
* A **15 perc** üresjárati időkorlátja.
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

Ebben a szakaszban hozzon létre egy privát hivatkozás szolgáltatást, amely az előző lépésben létrehozott Azure Load Balancer használja.

Hozzon létre egy privát hivatkozási szolgáltatást a standard Load Balancer előtéri IP-konfiguráció használatával az [az Network Private-link-Service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create)paranccsal:

* Elnevezett **myPrivateLinkService**.
* A virtuális hálózat **myVNet**.
* A standard Load Balancer **myLoadBalancer** és a előtér-konfiguráció **myFrontEnd** van társítva.
* A **eastus2** helyen.
 
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

A magánhálózati kapcsolati szolgáltatás létrejött, és képes fogadni a forgalmat. Ha szeretné megtekinteni a forgalmi folyamatokat, konfigurálja az alkalmazást a standard Load Balancer mögött.


## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban a Private link Service-t egy privát végpontra képezi le. A virtuális hálózatok tartalmazzák a magánhálózati kapcsolat szolgáltatás privát végpontját. Ez a virtuális hálózat tartalmazza azokat az erőforrásokat, amelyek hozzáférnek a privát kapcsolati szolgáltatáshoz.

### <a name="create-private-endpoint-virtual-network"></a>Magánhálózati végpont virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal:

* Elnevezett **myVNetPE**.
* A **11.1.0.0/16** címnek az előtagja.
* **MySubnetPE** nevű alhálózat.
* A **11.1.0.0/24** alhálózati előtag.
* A **CreatePrivLinkService-RG** erőforráscsoporthoz.
* A **eastus2** helye.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Ha a magánhálózati végpontok hálózati házirendjeinek letiltásához szeretné frissíteni az alhálózatot, használja az [az Network vnet subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Végpont és a kapcsolatok létrehozása

* Használja az [az Network Private-link-Service show](/cli/azure/network/private-link-service#az_network_private_link_service_show) lehetőséget a privát kapcsolati szolgáltatás erőforrás-azonosítójának lekéréséhez. A parancs egy változóba helyezi az erőforrás-azonosítót későbbi használatra.

* Az az [Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) paranccsal hozza létre a saját végpontot a korábban létrehozott virtuális hálózaton.

* Elnevezett **MyPrivateEndpoint**.
* A **CreatePrivLinkService-RG** erőforráscsoporthoz.
* A kapcsolatok neve **myPEconnectiontoPLS**.
* A **eastus2** helye.
* A virtuális hálózati **myVNetPE** és az alhálózat **mySubnetPE**.

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

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal távolítsa el az erőforráscsoportot, a Private link Service-t, a Load balancert és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőket hajtja végre:

* Létrehozott egy virtuális hálózatot és belső Azure Load Balancer.
* Privát hivatkozás szolgáltatás létrehozva

Ha többet szeretne megtudni az Azure Private-végpontról, folytassa a következővel:
> [!div class="nextstepaction"]
> [Rövid útmutató: privát végpont létrehozása az Azure CLI-vel](create-private-endpoint-cli.md)
