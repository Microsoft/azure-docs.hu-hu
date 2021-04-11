---
title: 'Rövid útmutató: nyilvános terheléselosztó létrehozása – Azure CLI'
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
ms.openlocfilehash: 1956d51cbcf962f7a73e970ca2cec858d9ec6579
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056521"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Rövid útmutató: Nyilvános Load Balancer létrehozása a virtuális gépek terhelésének elosztásához az Azure CLI használatával

Ismerkedjen meg Azure Load Balancer az Azure CLI használatával, és hozzon létre egy nyilvános Load balancert és három virtuális gépet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az-group-create)paranccsal:

* **CreatePubLBQS-RG** névvel ellátott. 
* A **eastus** helyen.

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard termékváltozat**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="A gyors üzembe helyezéshez létrehozott Standard Load Balancer-erőforrások." border="false":::

## <a name="configure-virtual-network---standard"></a>Virtuális hálózat konfigurálása – standard

A virtuális gépek üzembe helyezése és a terheléselosztó tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-createt)paranccsal:

* Elnevezett **myVNet**.
* A **10.1.0.0/16** címnek az előtagja.
* **MyBackendSubnet** nevű alhálózat.
* A **10.1.0.0/24** alhálózati előtag.
* A **CreatePubLBQS-RG** erőforráscsoporthoz.
* A **eastus** helye.

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

Az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy nyilvános IP-címet a megerősített gazdagép számára:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myBastionIP** néven.
* **CCreatePubLBQS – RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Megerősített alhálózat létrehozása

Az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) paranccsal hozzon létre egy megerősített alhálózatot:

* Elnevezett **AzureBastionSubnet**.
* A **10.1.1.0/24** címek előtagja.
* A virtuális hálózat **myVNet**.
* Az erőforráscsoport **CreatePubLBQS – RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Megerősített gazdagép létrehozása

Az [az Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) paranccsal hozzon létre egy megerősített gazdagépet:

* Elnevezett **myBastionHost**.
* **CreatePubLBQS – RG**.
* Nyilvános IP- **myBastionIP** társítva.
* Virtuális hálózati **myVNet** társítva.
* A **eastus** helyen.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Az Azure Bastion-gazdagép üzembe helyezése néhány percet is igénybe vehet.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A standard Load Balancer esetében a háttérbeli címen lévő virtuális gépeknek a hálózati biztonsági csoporthoz tartozó hálózati adapterekkel kell rendelkezniük. 

Hozzon létre egy hálózati biztonsági csoportot [az az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)paranccsal:

* Elnevezett **myNSG**.
* Az erőforráscsoport **CreatePubLBQS – RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt [az az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)paranccsal:

* Elnevezett **myNSGRuleHTTP**.
* Az előző lépésben létrehozott hálózati biztonsági csoport **myNSG**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Protokoll **(*)**.
* Irány **bejövő**.
* Forrás **(*)**.
* Cél **(*)**.
* Célport **portszáma 80**.
* Hozzáférés **engedélyezése**.
* **200** prioritás.

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

## <a name="create-backend-servers---standard"></a>Háttér-kiszolgálók létrehozása – standard

Ebben a szakaszban a következőket hozza létre:

* Három hálózati adapter a virtuális gépekhez.
* Három virtuális gép, amely háttér-kiszolgálóként használható a terheléselosztó számára.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create)paranccsal:

* Elnevezett **myNicVM1**, **myNicVM2** és **myNicVM3**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.

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

Hozza létre a virtuális gépeket az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal:

### <a name="vm1"></a>VM1
* Elnevezett **myVM1**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM1**.
* A virtuális gép rendszerképének **win2019datacenter**.
* **1. zóna**.

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
* Elnevezett **myVM2**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM2**.
* A virtuális gép rendszerképének **win2019datacenter**.
* **2. zóna**.

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
* Elnevezett **myVM3**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM3**.
* A virtuális gép rendszerképének **win2019datacenter**.
* **3. zóna**.

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
A virtuális gépek üzembe helyezése néhány percet is igénybe vehet.

## <a name="create-a-public-ip-address---standard"></a>Nyilvános IP-cím létrehozása – standard

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. 

Használja az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) to:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myPublicIP** néven.
* **CreatePubLBQS – RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Zóna nélküli redundáns nyilvános IP-cím létrehozása a 1. zónaban:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

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
* Az előző lépésben létrehozott nyilvános IP- **myPublicIP** társítva. 

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

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [az Network LB Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create)paranccsal:

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP** protokoll.
* A **80**-es port figyelése.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
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
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuális gépek hozzáadása a terheléselosztó háttérbeli készletéhez

Adja hozzá a virtuális gépeket a háttér-készlethez az [az Network NIC IP-config cím-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)paranccsal:

* A háttérbeli címkészlet **myBackEndPool**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A terheléselosztó **myLoadBalancer** van társítva.

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

## <a name="create-outbound-rule-configuration"></a>Kimenő szabály konfigurációjának létrehozása
A terheléselosztó kimenő szabályai a háttér-készletben lévő virtuális gépek kimenő SNAT konfigurálása. 

A kimenő kapcsolatokról a [Kimenő kapcsolatok az Azure-ban](load-balancer-outbound-connections.md)című témakörben olvashat bővebben.

A kimenő konfigurációhoz nyilvános IP-címet vagy előtagot lehet használni.

### <a name="public-ip"></a>Nyilvános IP-cím

Az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozhat létre egyetlen IP-címet a kimenő kapcsolathoz.  

* Elnevezett **myPublicIPOutbound**.
* **CreatePubLBQS – RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Zóna nélküli redundáns nyilvános IP-cím létrehozása a 1. zónaban:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Nyilvános IP-előtag

Az [az Network Public-IP előtag létrehozása](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) paranccsal hozzon létre egy nyilvános IP-előtagot a kimenő kapcsolathoz.

* Elnevezett **myPublicIPPrefixOutbound**.
* **CreatePubLBQS – RG**.
* Az előtag hossza **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Az 1. zóna-ben a zóna nélküli nyilvános IP-előtag létrehozása:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

A kimenő NAT és a kimenő kapcsolatok skálázásával kapcsolatos további információkért lásd: [kimenő NAT méretezése több IP-címmel](load-balancer-outbound-connections.md).

### <a name="create-outbound-frontend-ip-configuration"></a>Kimenő előtéri IP-konfiguráció létrehozása

Új előtérbeli IP-konfiguráció létrehozása az [az Network LB frontend-IP Create ](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create)paranccsal:

Válassza ki a nyilvános IP-cím vagy a nyilvános IP-előtag parancsait az előző lépésben hozott döntés alapján.

#### <a name="public-ip"></a>Nyilvános IP-cím

* Elnevezett **myFrontEndOutbound**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Nyilvános IP- **myPublicIPOutbound** társítva.
* A terheléselosztó **myLoadBalancer** van társítva.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Nyilvános IP-előtag

* Elnevezett **myFrontEndOutbound**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A nyilvános IP-előtag **myPublicIPPrefixOutbound** van társítva.
* A terheléselosztó **myLoadBalancer** van társítva.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Kimenő készlet létrehozása

Hozzon létre egy új kimenő készletet az [az Network LB-címkészlet Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create)paranccsal:

* Elnevezett **myBackEndPoolOutbound**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A terheléselosztó **myLoadBalancer** van társítva.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Kimenő szabály létrehozása

Hozzon létre egy új kimenő szabályt a kimenő háttérrendszer-készlethez az [az Network LB kimenő-Rule Create](/cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-create)paranccsal:

* Elnevezett **myOutboundRule**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A terheléselosztó **myLoadBalancer** társítva
* A frontend **myFrontEndOutbound** van társítva.
* **Minden** protokoll.
* A **15** üresjárati időkorlátja.
* **10000** kimenő portok.
* A háttér-készlet **myBackEndPoolOutbound** van társítva.

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
### <a name="add-virtual-machines-to-outbound-pool"></a>Virtuális gépek hozzáadása a kimenő készlethez

Adja hozzá a virtuális gépeket a kimenő készlethez az [az Network NIC IP-config cím-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)paranccsal:


* A háttérbeli címkészlet **myBackEndPoolOutbound**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A terheléselosztó **myLoadBalancer** van társítva.

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
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

A rövid útmutatóban :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="létrehozott alapszintű Load Balancer-erőforrások." border="false"::: m

## <a name="configure-virtual-network---basic"></a>Virtuális hálózat konfigurálása – alapszintű

A virtuális gépek üzembe helyezése és a terheléselosztó tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal:

* Elnevezett **myVNet**.
* A **10.1.0.0/16** címnek az előtagja.
* **MyBackendSubnet** nevű alhálózat.
* A **10.1.0.0/24** alhálózati előtag.
* A **CreatePubLBQS-RG** erőforráscsoporthoz.
* A **eastus** helye.

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

Az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy nyilvános IP-címet a megerősített gazdagép számára:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myBastionIP** néven.
* **CreatePubLBQS – RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Megerősített alhálózat létrehozása

Az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) paranccsal hozzon létre egy megerősített alhálózatot:

* Elnevezett **AzureBastionSubnet**.
* A **10.1.1.0/24** címek előtagja.
* A virtuális hálózat **myVNet**.
* Az erőforráscsoport **CreatePubLBQS – RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Megerősített gazdagép létrehozása

Az [az Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) paranccsal hozzon létre egy megerősített gazdagépet:

* Elnevezett **myBastionHost**.
* **CreatePubLBQS – RG**.
* Nyilvános IP- **myBastionIP** társítva.
* Virtuális hálózati **myVNet** társítva.
* A **eastus** helyen.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Az Azure Bastion-gazdagép üzembe helyezése néhány percet is igénybe vehet.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A standard Load Balancer esetében a háttérbeli címen lévő virtuális gépeknek a hálózati biztonsági csoporthoz tartozó hálózati adapterekkel kell rendelkezniük. 

Hozzon létre egy hálózati biztonsági csoportot [az az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)paranccsal:

* Elnevezett **myNSG**.
* Az erőforráscsoport **CreatePubLBQS – RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt [az az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)paranccsal:

* Elnevezett **myNSGRuleHTTP**.
* Az előző lépésben létrehozott hálózati biztonsági csoport **myNSG**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Protokoll **(*)**.
* Irány **bejövő**.
* Forrás **(*)**.
* Cél **(*)**.
* Célport **portszáma 80**.
* Hozzáférés **engedélyezése**.
* **200** prioritás.

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

## <a name="create-backend-servers---basic"></a>Háttér-kiszolgálók létrehozása – alapszintű

Ebben a szakaszban a következőket hozza létre:

* Három hálózati adapter a virtuális gépekhez.
* Rendelkezésre állási csoport a virtuális gépekhez
* Három virtuális gép, amely háttér-kiszolgálóként használható a terheléselosztó számára.


### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create)paranccsal:


* Elnevezett **myNicVM1**, **myNicVM2** és **myNicVM3**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.

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
### <a name="create-availability-set-for-virtual-machines"></a>Rendelkezésre állási csoport létrehozása a virtuális gépekhez

Hozza létre a rendelkezésre állási készletet az [az VM rendelkezésre állása-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create)paranccsal:

* Elnevezett **myAvSet**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Hely **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal:

### <a name="vm1"></a>VM1
* Elnevezett **myVM1**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM1**.
* A virtuális gép rendszerképének **win2019datacenter**.
* **1. zóna**.

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
* Elnevezett **myVM2**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM2**.
* A virtuális gép rendszerképének **win2019datacenter**.
* **2. zóna**.

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
* Elnevezett **myVM3**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM3**.
* A virtuális gép rendszerképének **win2019datacenter**.
* **3. zóna**.

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
A virtuális gépek üzembe helyezése néhány percet is igénybe vehet.

## <a name="create-a-public-ip-address---basic"></a>Nyilvános IP-cím létrehozása – alapszintű

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. 

Használja az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) to:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myPublicIP** néven.
* **CreatePubLBQS – RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

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
* Az előző lépésben létrehozott nyilvános IP- **myPublicIP** társítva. 

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

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [az Network LB Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create)paranccsal:

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP** protokoll.
* A **80**-es port figyelése.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
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

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuális gépek hozzáadása a terheléselosztó háttérbeli készletéhez

Adja hozzá a virtuális gépeket a háttér-készlethez az [az Network NIC IP-config cím-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)paranccsal:

* A háttérbeli címkészlet **myBackEndPool**.
* Az erőforráscsoport **CreatePubLBQS – RG**.
* A terheléselosztó **myLoadBalancer** van társítva.

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

Az [az VM Extension set](/cli/azure/vm/extension#az_vm_extension_set) paranccsal telepítse az IIS-t a virtuális gépekre, és állítsa az alapértelmezett webhelyet a számítógép nevére.

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

A terheléselosztó nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) parancsot. 

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

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal távolítsa el az erőforráscsoportot, a Load balancert és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban

* Létrehozta a standard vagy a nyilvános terheléselosztó
* Csatlakoztatott virtuális gépek. 
* Konfigurálta a terheléselosztó forgalmi szabályát és az állapot-mintavételt.
* Tesztelte a terheléselosztó.

Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a következővel:
> [!div class="nextstepaction"]
> [Mi az Azure Load Balancer?](load-balancer-overview.md)