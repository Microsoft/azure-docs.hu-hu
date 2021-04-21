---
title: 'Rövid útmutató: Nyilvános terheléselosztás létrehozása – Azure CLI'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató ismerteti, hogyan hozható létre nyilvános Load Balancer az Azure CLI használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 9b332b18930d58ebb1d155c35a74eed69a90ce73
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788780"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Rövid útmutató: Nyilvános Load Balancer létrehozása a virtuális gépek terhelésének elosztásához az Azure CLI használatával

Az azure Azure Load Balancer használatának első lépésekor hozzon létre egy nyilvános terheléselosztást és három virtuális gépet az Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot [az az group create segítségével:](/cli/azure/group#az_group_create)

* Neve: **CreatePubLBQS-rg.** 
* Az **eastus helyen.**

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard termékváltozat**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Az éles számítási feladatokhoz standard termékváltozatú terheléselosztás ajánlott. A SKUS-okkal kapcsolatos további információkért lásd: **[Azure Load Balancer SKUs](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="A rövid útmutatóhoz létrehozott standard terheléselosztási erőforrások." border="false":::

## <a name="configure-virtual-network---standard"></a>Virtuális hálózat konfigurálása – Standard

A virtuális gépek üzembe helyezése és a terheléseltöltő tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az network vnet create használatával:](/cli/azure/network/vnet#az_network_vnet_createt)

* A **neve myVNet.**
* A **10.1.0.0/16 címelőtagja.**
* A **myBackendSubnet nevű alhálózat.**
* A **10.1.0.0/24 alhálózati előtag.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Az **eastus helye.**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egy nyilvános IP-címet a megerősített gazdagéphez:

* Hozzon létre egy standard zónaredundáns nyilvános IP-címet **myBastionIP névvel.**
* A **CCreatePubLBQS-rg csomópontban.**

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Megerősített alhálózat létrehozása

Az [az network vnet subnet create használatával](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) hozzon létre egy bástya-alhálózatot:

* Neve: **AzureBastionSubnet.**
* A **10.1.1.0/24 címelőtag.**
* A **myVNet virtuális hálózatban.**
* A **CreatePubLBQS-rg erőforráscsoportban.**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Megerősített gazdagép létrehozása

Az [az network bastion create használatával](/cli/azure/network/bastion#az_network_bastion_create) hozzon létre egy megerősített gazdagépet:

* A **neve myBastionHost.**
* A **CreatePubLBQS-rg alatt.**
* A **myBastionIP nyilvános IP-címhez van társítva.**
* A myVNet virtuális hálózathoz **van társítva.**
* Az **eastus helyen.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

A gazdagép üzembe helyezése eltarthat Azure Bastion néhány percig.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Standard terheléselosztás esetén a háttércímben a virtuális gépeknek hálózati biztonsági csoporthoz tartozó hálózati adapterekkel kell lenniük. 

Hozzon létre egy hálózati biztonsági csoportot [az az network nsg create használatával:](/cli/azure/network/nsg#az_network_nsg_create)

* **MyNSG nevű.**
* A **CreatePubLBQS-rg erőforráscsoportban.**

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonságicsoport-szabályt [az az network nsg rule create használatával:](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)

* **MyNSGRuleHTTP névvel.**
* Az előző lépésben létrehozott hálózati biztonsági csoportban: **myNSG**.
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Protokoll **(*)**.
* Direction **Inbound**.
* Forrás **(*)**.
* Cél **(*)**.
* **Célport: 80.**
* Hozzáférés **engedélyezése.**
* **200-as prioritás.**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Háttérkiszolgálók létrehozása – Standard

Ebben a szakaszban a következőt hozza létre:

* Három hálózati adapter a virtuális gépekhez.
* Három virtuális gép, amelyek háttérkiszolgálóként szolgálnak a terheléselosztáshoz.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert [az az network nic create használatával:](/cli/azure/network/nic#az_network_nic_create)

* A **neve myNicVM1,** **myNicVM2**, és **myNicVM3.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* A **myVNet virtuális hálózatban.**
* A **myBackendSubnet alhálózatban.**
* A **myNSG hálózati biztonsági csoportban.**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozza létre a virtuális gépeket [az az vm create segítségével:](/cli/azure/vm#az_vm_create)

### <a name="vm1"></a>VM1
* A **neve myVM1.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Csatlakoztatva van a **myNicVM1 hálózati adapterhez.**
* Virtuálisgép-rendszerkép **win2019datacenter**.
* A **következő 1. zóna:**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>VM2
* **MyVM2 nevű.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Csatlakoztatva van a **myNicVM2 hálózati adapterhez.**
* Virtuálisgép-rendszerkép **win2019datacenter**.
* A **következő 2. zóna:**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* A **neve myVM3.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Csatlakoztatva van a **myNicVM3 hálózati adapterhez.**
* Virtuálisgép-rendszerkép **win2019datacenter**.
* A **következő 3. zóna:**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
A virtuális gépek üzembe helyezése eltarthat néhány percig.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---standard"></a>Nyilvános IP-cím létrehozása – Standard

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. 

Az [az network public-ip create használatával:](/cli/azure/network/public-ip#az_network_public_ip_create)

* Hozzon létre egy standard zónaredundáns nyilvános IP-címet **myPublicIP névvel.**
* A **CreatePubLBQS-rg alatt.**

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Zónaredundáns nyilvános IP-cím létrehozása a 1. zóna:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Standard terheléselosztás létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtere IP-címkészlet, amely fogadja a bejövő hálózati forgalmat a terheléselosztáson.
  * Egy háttér-IP-címkészlet, ahol az előterekészlet küldi az elosztott terhelésű hálózati forgalmat.
  * Állapot-mintavétel, amely meghatározza a háttér virtuálisgép-példányok állapotát.
  * Egy terheléselosztási szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer-resource"></a>A terheléselosztási erőforrás létrehozása

Hozzon létre egy nyilvános terheléselosztást [az az network lb create segítségével:](/cli/azure/network/lb#az_network_lb_create)

* A **neve myLoadBalancer.**
* Egy **myFrontEnd nevű előterekészlet.**
* Egy **myBackEndPool nevű háttérkészlet.**
* Az előző lépésben létrehozott **myPublicIP** nyilvános IP-címhez van társítva. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapotfigyelő mintavétel ellenőrzi az összes virtuálisgép-példányt, hogy biztos legyen a hálózati forgalom elküldését. 

A rendszer eltávolít egy sikertelen mintavétel-ellenőrzéssel a virtuális gépet a terheléselosztásból. A virtuális gép a hiba megoldása után vissza lesz adva a terheléselosztásba.

Hozzon létre egy állapot-mintavételt [az az network lb probe create segítségével:](/cli/azure/network/lb/probe#az_network_lb_probe_create)

* Figyeli a virtuális gépek állapotát.
* A **neve myHealthProbe.**
* TCP **protokoll.**
* A **80-as port figyelése.**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztási szabályok a következőt határozzák meg:

* A bejövő forgalom előtere IP-konfigurációja.
* A forgalom fogadására a háttér-IP-címkészlet.
* A szükséges forrás- és célport. 

Hozzon létre egy terheléselosztási szabályt [az az network lb rule create segítségével:](/cli/azure/network/lb/rule#az_network_lb_rule_create)

* **MyHTTPRule nevű**
* A **myFrontEnd** **előterekészlet 80-as** portján figyel.
* Elosztott terhelésű hálózati forgalom küldése a **myBackEndPool** háttércímkészletbe a **80-as port használatával.** 
* Állapot-mintavétel **használata: myHealthProbe.**
* TCP **protokoll.**
* Üresjárati időkorlát **15 perc.**
* Engedélyezze a TCP alaphelyzetbe állítását.


```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true

```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuális gépek hozzáadása terheléselosztási háttérkészlethez

Adja hozzá a virtuális gépeket a háttérkészlethez [az az network nic ip-config address-pool add gombra:](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)

* A **háttércímkészletben: myBackEndPool.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* A **myLoadBalancer terheléselosztáshoz van társítva.**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>Kimenőszabály-konfiguráció létrehozása
A terheléselosztás kimenő szabályai konfigurálják a kimenő SNAT-t a háttérkészlet virtuális gépei számára. 

További információ a kimenő kapcsolatokról: Kimenő [kapcsolatok az Azure-ban.](load-balancer-outbound-connections.md)

A kimenő konfigurációhoz nyilvános IP-cím vagy előtag is használható.

### <a name="public-ip"></a>Nyilvános IP-cím

Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egyetlen IP-címet a kimenő kapcsolathoz.  

* A **neve myPublicIPOutbound.**
* A **CreatePubLBQS-rg alatt.**

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Zónaredundáns nyilvános IP-cím létrehozása a 1. zóna:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Nyilvános IP-előtag

Az [az network public-ip prefix create használatával](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) hozzon létre egy nyilvános IP-előtagot a kimenő kapcsolathoz.

* A **neve myPublicIPPrefixOutbound.**
* A **CreatePubLBQS-rg alatt.**
* Az előtag hossza **28.**

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Zónaredundáns nyilvános IP-előtag létrehozása a 1. zóna:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

A kimenő NAT és a kimenő kapcsolatok skálázával kapcsolatos további információkért lásd: [Kimenő NAT skálázása több IP-címmel.](load-balancer-outbound-connections.md)

### <a name="create-outbound-frontend-ip-configuration"></a>Kimenő előtere IP-konfigurációjának létrehozása

Hozzon létre egy új előtere IP-konfigurációt [az az network lb frontend-ip create segítségével: ](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create)

Válassza ki a nyilvános IP-cím vagy a nyilvános IP-előtag parancsokat az előző lépésben hozott döntés alapján.

#### <a name="public-ip"></a>Nyilvános IP-cím

* A **neve myFrontEndOutbound.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* A **myPublicIPOutbound nyilvános IP-címhez van társítva.**
* A **myLoadBalancer terheléselosztáshoz van társítva.**

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Nyilvános IP-előtag

* A **neve myFrontEndOutbound.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* A **myPublicIPPrefixOutbound** nyilvános IP-előtaggal van társítva.
* A **myLoadBalancer terheléselosztáshoz van társítva.**

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Kimenő készlet létrehozása

Hozzon létre egy új kimenő készletet [az az network lb address-pool create segítségével:](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create)

* A **neve myBackEndPoolOutbound.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* A **myLoadBalancer terheléselosztáshoz van társítva.**

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Kimenő szabály létrehozása

Hozzon létre egy új kimenő szabályt a kimenő háttérkészlethez [az az network lb outbound-rule create segítségével:](/cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_create)

* A **neve myOutboundRule.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* A **myLoadBalancer terheléselosztáshoz van társítva**
* Társítva a **myFrontEndOutbound előtere.**
* Minden **protokoll.**
* Üresjárati **időkorlát: 15.**
* **10000 kimenő** port.
* A **myBackEndPoolOutbound** háttérkészlethez van társítva.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Virtuális gépek hozzáadása kimenő készlethez

Adja hozzá a virtuális gépeket a kimenő készlethez [az az network nic ip-config address-pool add gombra:](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)


* A **háttércímkészletben: myBackEndPoolOutbound.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* A **myLoadBalancer** terheléselosztási höz van társítva.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>A standard termékváltozatú terheléselosztás éles számítási feladatokhoz ajánlott. A SKUS-okkal kapcsolatos további információkért lásd: **[Azure Load Balancer SKUS.](skus.md)**

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="A rövid útmutatóban létrehozott alapszintű terheléselosztási erőforrások." border="false"::: M

## <a name="configure-virtual-network---basic"></a>Virtuális hálózat konfigurálása – Alapszintű

A virtuális gépek üzembe helyezése és a terheléseltöltő tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az network vnet create használatával:](/cli/azure/network/vnet#az_network_vnet_create)

* **MyVNet névvel.**
* A **10.1.0.0/16 címelőtagja.**
* A **myBackendSubnet nevű alhálózat.**
* A **10.1.0.0/24 alhálózati előtag.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Az **eastus helye.**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egy nyilvános IP-címet a megerősített gazdagéphez:

* Hozzon létre egy standard zónaredundáns nyilvános IP-címet **myBastionIP névvel.**
* A **CreatePubLBQS-rg alatt.**

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Bástya-alhálózat létrehozása

Az [az network vnet subnet create használatával](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) hozzon létre egy bástya-alhálózatot:

* Neve: **AzureBastionSubnet.**
* A **10.1.1.0/24 címelőtagja.**
* A **myVNet virtuális hálózatban.**
* A **CreatePubLBQS-rg erőforráscsoportban.**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Megerősített gazdagép létrehozása

Az [az network bastion create használatával](/cli/azure/network/bastion#az_network_bastion_create) hozzon létre egy megerősített gazdagépet:

* A **neve myBastionHost.**
* A **CreatePubLBQS-rg alatt.**
* A **myBastionIP nyilvános IP-címhez van társítva.**
* A myVNet virtuális hálózathoz **van társítva.**
* Az **eastus helyen.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

A gazdagép üzembe helyezése eltarthat néhány Azure Bastion.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Standard terheléselosztás esetén a háttércímében a virtuális gépeknek hálózati biztonsági csoporthoz tartozó hálózati adapterekkel kell lenniük. 

Hozzon létre egy hálózati biztonsági csoportot [az az network nsg create használatával:](/cli/azure/network/nsg#az_network_nsg_create)

* A **neve myNSG.**
* A **CreatePubLBQS-rg erőforráscsoportban.**

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonságicsoport-szabályt [az az network nsg rule create használatával:](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)

* **MyNSGRuleHTTP névvel.**
* Az előző lépésben létrehozott hálózati biztonsági csoportban: **myNSG**.
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Protokoll **(*)**.
* Direction **Inbound**.
* Forrás **(*)**.
* Cél **(*)**.
* **Célport: 80.**
* Hozzáférés **engedélyezése.**
* **200-as prioritás.**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Háttérkiszolgálók létrehozása – Alapszintű

Ebben a szakaszban a következőt hozza létre:

* Három hálózati adapter a virtuális gépekhez.
* Rendelkezésre állási készlet a virtuális gépekhez
* Három virtuális gép, amelyek háttérkiszolgálóként szolgálnak a terheléselosztáshoz.


### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert [az az network nic create használatával:](/cli/azure/network/nic#az_network_nic_create)


* A **neve myNicVM1,** **myNicVM2**, és **myNicVM3.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* A **myVNet virtuális hálózatban.**
* A **myBackendSubnet alhálózatban.**
* A **myNSG hálózati biztonsági csoportban.**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>Rendelkezésre állási csoport létrehozása virtuális gépekhez

Hozza létre a rendelkezésre állási készletet [az az vm availability-set create gombra:](/cli/azure/vm/availability-set#az_vm_availability_set_create)

* **MyAvSet névvel.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Hely: **eastus.**

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozza létre a virtuális gépeket [az az vm create segítségével:](/cli/azure/vm#az_vm_create)

### <a name="vm1"></a>VM1
* A **neve myVM1.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Csatlakoztatva van a **myNicVM1 hálózati adapterhez.**
* Virtuálisgép-rendszerkép **win2019datacenter**.
* A **következő 1. zóna:**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>VM2
* **MyVM2 nevű.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Csatlakoztatva van a **myNicVM2 hálózati adapterhez.**
* Virtuálisgép-rendszerkép **win2019datacenter**.
* A **következő 2. zóna:**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>VM3
* A **neve myVM3.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* Csatlakoztatva van a **myNicVM3 hálózati adapterhez.**
* Virtuálisgép-rendszerkép **win2019datacenter.**
* A **következő 3. zóna:**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
A virtuális gépek üzembe helyezése eltarthat néhány percig.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---basic"></a>Nyilvános IP-cím létrehozása – Alapszintű

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. 

Az [az network public-ip create használatával:](/cli/azure/network/public-ip#az_network_public_ip_create)

* Hozzon létre egy standard zónaredundáns nyilvános IP-címet **myPublicIP névvel.**
* A **CreatePubLBQS-rg alatt.**

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Alapszintű terheléselosztás létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtere IP-címkészlet, amely fogadja a bejövő hálózati forgalmat a terheléselosztáson.
  * Egy háttér-IP-címkészlet, ahol az előterekészlet küldi az elosztott terhelésű hálózati forgalmat.
  * Állapot-mintavétel, amely meghatározza a háttér virtuálisgép-példányok állapotát.
  * Egy terheléselosztási szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer-resource"></a>A terheléselosztási erőforrás létrehozása

Hozzon létre egy nyilvános terheléselosztást [az az network lb create segítségével:](/cli/azure/network/lb#az_network_lb_create)

* A **neve myLoadBalancer.**
* Egy **myFrontEnd nevű előterekészlet.**
* Egy **myBackEndPool nevű háttérkészlet.**
* Az előző lépésben **létrehozott myPublicIP** nyilvános IP-címhez van társítva. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapotfigyelő mintavétel az összes virtuálisgép-példányt ellenőrzi, hogy biztos lehet-e a hálózati forgalom elküldését. 

A rendszer eltávolítja a virtuális gépet a terheléselosztásból, amelynél a mintavétel ellenőrzése sikertelen volt. A hiba megoldása után a virtuális gép vissza lesz adva a terheléselosztásba.

Hozzon létre egy állapot-mintavételt [az az network lb probe create segítségével:](/cli/azure/network/lb/probe#az_network_lb_probe_create)

* Figyeli a virtuális gépek állapotát.
* A **neve myHealthProbe.**
* TCP **protokoll.**
* A **80-as port figyelése.**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztási szabályok a következőt határozzák meg:

* A bejövő forgalom előtere IP-konfigurációja.
* A forgalom fogadására a háttér-IP-címkészlet.
* A szükséges forrás- és célport. 

Hozzon létre egy terheléselosztási szabályt [az az network lb rule create segítségével:](/cli/azure/network/lb/rule#az_network_lb_rule_create)

* **MyHTTPRule nevű**
* A **myFrontEnd** **előterekészlet 80-as** portján figyel.
* Elosztott terhelésű hálózati forgalom küldése a **myBackEndPool** háttércímkészletbe a **80-as port használatával.** 
* Állapot-mintavétel **használata: myHealthProbe.**
* TCP **protokoll.**
* Üresjárati időkorlát **15 perc.**

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuális gépek hozzáadása terheléselosztási háttérkészlethez

Adja hozzá a virtuális gépeket a háttérkészlethez [az az network nic ip-config address-pool add gombra:](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)

* A **háttércímkészletben: myBackEndPool.**
* A **CreatePubLBQS-rg erőforráscsoportban.**
* A **myLoadBalancer** terheléselosztási höz van társítva.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

## <a name="install-iis"></a>Az IIS telepítése

Az [az vm extension set használatával](/cli/azure/vm/extension#az_vm_extension_set) telepítse az IIS-t a virtuális gépekre, és állítsa az alapértelmezett webhelyet a számítógép nevére.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

A terheléselosztó nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) parancsot. 

Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="A terheléselosztó tesztelése" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete paranccsal](/cli/azure/group#az_group_delete) távolítsa el az erőforráscsoportot, a terheléselosztást és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban

* Létrehozott egy standard vagy nyilvános terheléselosztást
* Csatlakoztatott virtuális gépek. 
* Konfigurálta a terheléselosztási forgalmi szabályt és az állapot-mintavételt.
* Tesztelte a terheléseltöltőt.

Ha többet szeretne megtudni a Azure Load Balancer, folytassa a következővel:
> [!div class="nextstepaction"]
> [Mi az Azure Load Balancer?](load-balancer-overview.md)