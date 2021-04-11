---
title: 'Rövid útmutató: belső terheléselosztó létrehozása – Azure CLI'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozható létre belső terheléselosztó az Azure CLI használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: f728e1f1e2186188135666ed54e02c9ed3507509
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056538"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Rövid útmutató: belső terheléselosztó létrehozása az Azure CLI használatával

A Azure Load Balancer használatának első lépései az Azure CLI használatával belső terheléselosztó és három virtuális gép létrehozásához.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Ha Azure Cloud Shell használ, a legújabb verzió már telepítve van.

>[!NOTE]
>Az éles számítási feladatokhoz ajánlott a Azure Load Balancer standard. Ez a cikk a Azure Load Balancer standard, valamint a Azure Load Balancer alapszintű információkat tartalmazza. További információ az SKU-ról: [Azure Load Balancer SKU](skus.md)-ban.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelyben üzembe helyezheti és kezelheti Azure-erőforrásait.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Nevezze el az erőforráscsoportot **CreateIntLBQS-RG** néven, és adja meg a helyet a **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

## <a name="azure-load-balancer-standard"></a>Azure Load Balancer standard

Ebben a szakaszban létrehoz egy terheléselosztó, amely terheléselosztást végez a virtuális gépeken. Belső terheléselosztó létrehozásakor a rendszer a terheléselosztó hálózatként konfigurálja a virtuális hálózatot. Az alábbi ábrán az ebben a rövid útmutatóban létrehozott erőforrások láthatók:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="A gyors üzembe helyezéshez létrehozott Standard Load Balancer-erőforrások." border="false":::

### <a name="configure-the-virtual-network"></a>A virtuális hálózat konfigurálása

A virtuális gépek üzembe helyezése és a terheléselosztó üzembe helyezése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

#### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myVNet**
* A **10.1.0.0/16** címe
* **MyBackendSubnet** nevű alhálózat
* **10.1.0.0/24** alhálózati előtag
* A **CreateIntLBQS-RG** erőforráscsoporthoz
* A **eastus** helye

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az az [Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy nyilvános IP-címet az Azure Bastion-gazdagéphez. Adja meg az alábbiakat:

* Hozzon létre egy standard zóna – redundáns nyilvános IP-cím nevű **myBastionIP**
* **CreateIntLBQS – RG**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Azure-beli megerősített alhálózat létrehozása

Alhálózat létrehozásához használja [az az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) lehetőséget. Adja meg az alábbiakat:

* Elnevezett **AzureBastionSubnet**
* A **10.1.1.0/24** előtagjának címe
* Virtuális hálózati **myVNet**
* Az erőforráscsoport **CreateIntLBQS – RG**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Azure Bastion-gazdagép létrehozása

A gazdagép létrehozásához használja [az az Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) paranccsal. Adja meg az alábbiakat:

* Elnevezett **myBastionHost**
* **CreateIntLBQS – RG**
* Nyilvános IP- **myBastionIP** társítva
* Virtuális hálózati **myVNet** társítva
* A **eastus** helyen

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Az Azure Bastion-gazdagép üzembe helyezése néhány percet is igénybe vehet.

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Standard Load Balancer esetén győződjön meg arról, hogy a virtuális gépek olyan hálózati adapterekkel rendelkeznek, amelyek hálózati biztonsági csoporthoz tartoznak. Hozzon létre egy hálózati biztonsági csoportot az [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myNSG**
* Az erőforráscsoport **CreateIntLBQS – RG**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myNSGRuleHTTP**
* Az előző lépésben létrehozott hálózati biztonsági csoport **myNSG**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Protokoll **(*)**
* Irány **bejövő**
* Forrás **(*)**
* Cél **(*)**
* Célport **portja 80**
* Hozzáférés **engedélyezése**
* Prioritás **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban a következőket hozza létre:

* Három hálózati adapter a virtuális gépekhez.
* Három virtuális gép, amelyet kiszolgálóként kell használni a terheléselosztó számára.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myNicVM1**, **myNicVM2** és **myNicVM3**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Virtuális hálózati **myVNet**
* Az alhálózat **myBackendSubnet**
* A hálózati biztonsági csoport **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-virtual-machines"></a>A virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. Adja meg az alábbiakat:

* Elnevezett **myVM1**, **myVM2** és **myVM3**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Csatolva a hálózati adapter **myNicVM1**, **myNicVM2** és **myNicVM3**
* Virtuális gép rendszerképének **win2019datacenter**
* **1. zóna**, **2. zóna** és **3. zóna**

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

A virtuális gépek üzembe helyezése néhány percet is igénybe vehet.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

* A terheléselosztó bejövő hálózati forgalmát fogadó IP-készlet.
* Egy második IP-készlet, amelyben az első készlet elküldi a terheléselosztásos hálózati forgalmat.
* Egy állapot-mintavétel, amely meghatározza a virtuálisgép-példányok állapotát.
* Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

#### <a name="create-the-load-balancer-resource"></a>A terheléselosztó erőforrásának létrehozása

Hozzon létre egy nyilvános Load balancert az [az Network LB Create](/cli/azure/network/lb#az-network-lb-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myLoadBalancer**
* Egy **myFrontEnd** nevű készlet
* Egy **myBackEndPool** nevű készlet
* A virtuális hálózat **myVNet** társítva
* Az alhálózat **myBackendSubnet** társítva

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Hozzon létre egy állapot-mintavételt az [az Network LB Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create)paranccsal. Adja meg az alábbiakat:

* A virtuális gépek állapotának figyelése
* Elnevezett **myHealthProbe**
* Protokoll **TCP**
* Figyelési **Port 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom IP-konfigurációja.
* A forgalmat fogadó IP-készlet.
* A szükséges forrás-és célport. 

Hozzon létre egy terheléselosztási szabályt az [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) paranccsal. Adja meg az alábbiakat:

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a készlet **myFrontEnd**
* Elosztott terhelésű hálózati forgalom küldése a címkészlet **myBackEndPool** a 80-es **port** használatával 
* Health mintavételi **myHealthProbe** használata
* Protokoll **TCP**
* Üresjárati időkorlát **15 perc**
* TCP-visszaállítás engedélyezése

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
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

#### <a name="add-vms-to-the-load-balancer-pool"></a>Virtuális gépek hozzáadása a terheléselosztó-készlethez

Adja hozzá a virtuális gépeket a háttér-készlethez az [az Network NIC IP-config cím-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)paranccsal. Adja meg az alábbiakat:

* A címkészlet **myBackEndPool**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Hálózati adapter **myNicVM1**, **myNicVM2** és **myNicVM3** társítva
* A terheléselosztó **myLoadBalancer** társítva

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

## <a name="azure-load-balancer-basic"></a>Alapszintű Azure Load Balancer

Ebben a szakaszban létrehoz egy terheléselosztó, amely terheléselosztást végez a virtuális gépeken. Belső terheléselosztó létrehozásakor a rendszer a terheléselosztó hálózatként konfigurálja a virtuális hálózatot. Az alábbi ábrán az ebben a rövid útmutatóban létrehozott erőforrások láthatók:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="A gyors üzembe helyezéshez létrehozott alapszintű terheléselosztó-erőforrások." border="false":::

### <a name="configure-the-virtual-network"></a>A virtuális hálózat konfigurálása

A virtuális gépek üzembe helyezése és a terheléselosztó üzembe helyezése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

#### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-createt)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myVNet**
* A **10.1.0.0/16** címe
* **MyBackendSubnet** nevű alhálózat
* **10.1.0.0/24** alhálózati előtag
* A **CreateIntLBQS-RG** erőforráscsoporthoz
* A **eastus** helye

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az az [Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy nyilvános IP-címet az Azure Bastion-gazdagéphez. Adja meg az alábbiakat:

* Hozzon létre egy standard zóna – redundáns nyilvános IP-cím nevű **myBastionIP**
* **CreateIntLBQS – RG**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Azure-beli megerősített alhálózat létrehozása

Alhálózat létrehozásához használja [az az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) lehetőséget. Adja meg az alábbiakat:

* Elnevezett **AzureBastionSubnet**
* A **10.1.1.0/24** előtagjának címe
* Virtuális hálózati **myVNet**
* Az erőforráscsoport **CreateIntLBQS – RG**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Azure Bastion-gazdagép létrehozása

A gazdagép létrehozásához használja [az az Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) paranccsal. Adja meg az alábbiakat:

* Elnevezett **myBastionHost**
* **CreateIntLBQS – RG**
* Nyilvános IP- **myBastionIP** társítva
* Virtuális hálózati **myVNet** társítva
* A **eastus** helyen

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Az Azure Bastion-gazdagép üzembe helyezése néhány percet is igénybe vehet.

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Standard Load Balancer esetén győződjön meg arról, hogy a virtuális gépek olyan hálózati adapterekkel rendelkeznek, amelyek hálózati biztonsági csoporthoz tartoznak. Hozzon létre egy hálózati biztonsági csoportot az [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myNSG**
* Az erőforráscsoport **CreateIntLBQS – RG**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myNSGRuleHTTP**
* Az előző lépésben létrehozott hálózati biztonsági csoport **myNSG**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Protokoll **(*)**
* Irány **bejövő**
* Forrás **(*)**
* Cél **(*)**
* Célport **portja 80**
* Hozzáférés **engedélyezése**
* Prioritás **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban a következőket hozza létre:

* Három hálózati adapter a virtuális gépekhez.
* A virtuális gépek rendelkezésre állási csoportja.
* Három virtuális gép, amelyet kiszolgálóként kell használni a terheléselosztó számára.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myNicVM1**, **myNicVM2** és **myNicVM3**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Virtuális hálózati **myVNet**
* Az alhálózat **myBackendSubnet**
* A hálózati biztonsági csoport **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>A virtuális gépek rendelkezésre állási csoportjának létrehozása

Hozza létre a rendelkezésre állási készletet az [az VM rendelkezésre állása-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myAvailabilitySet**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Hely **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>A virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. Adja meg az alábbiakat:

* Elnevezett **myVM1**, **myVM2** és **myVM3**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Csatolva a hálózati adapter **myNicVM1**, **myNicVM2** és **myNicVM3**
* Virtuális gép rendszerképének **win2019datacenter**
* A **myAvailabilitySet**


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
A virtuális gépek üzembe helyezése néhány percet is igénybe vehet.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

* A terheléselosztó bejövő hálózati forgalmát fogadó IP-készlet.
* Egy második IP-készlet, amelyben az első készlet elküldi a terheléselosztásos hálózati forgalmat.
* Egy állapot-mintavétel, amely meghatározza a virtuálisgép-példányok állapotát.
* Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

#### <a name="create-the-load-balancer-resource"></a>A terheléselosztó erőforrásának létrehozása

Hozzon létre egy nyilvános Load balancert az [az Network LB Create](/cli/azure/network/lb#az-network-lb-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myLoadBalancer**
* Egy **myFrontEnd** nevű készlet
* Egy **myBackEndPool** nevű készlet
* A virtuális hálózat **myVNet** társítva
* Az alhálózat **myBackendSubnet** társítva

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Hozzon létre egy állapot-mintavételt az [az Network LB Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create)paranccsal. Adja meg az alábbiakat:

* A virtuális gépek állapotának figyelése
* Elnevezett **myHealthProbe**
* Protokoll **TCP**
* Figyelési **Port 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom IP-konfigurációja.
* A forgalmat fogadó IP-készlet.
* A szükséges forrás-és célport. 

Hozzon létre egy terheléselosztási szabályt az [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) paranccsal. Adja meg az alábbiakat:

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a készlet **myFrontEnd**
* Elosztott terhelésű hálózati forgalom küldése a címkészlet **myBackEndPool** a 80-es **port** használatával 
* Health mintavételi **myHealthProbe** használata
* Protokoll **TCP**
* Üresjárati időkorlát **15 perc**

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Virtuális gépek hozzáadása a terheléselosztó-készlethez

Adja hozzá a virtuális gépeket a háttér-készlethez az [az Network NIC IP-config cím-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)paranccsal. Adja meg az alábbiakat:

* A címkészlet **myBackEndPool**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Hálózati adapter **myNicVM1**, **myNicVM2** és **myNicVM3** társítva
* A terheléselosztó **myLoadBalancer** társítva

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Hozza létre a hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myNicTestVM**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Virtuális hálózati **myVNet**
* Az alhálózat **myBackendSubnet**
* A hálózati biztonsági csoport **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Hozza létre a virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal. Adja meg az alábbiakat:

* Elnevezett **myTestVM**
* Az erőforráscsoport **CreateIntLBQS – RG**
* Csatolva a hálózati adapter **myNicTestVM**
* Virtuális gép rendszerképének **Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Előfordulhat, hogy néhány percet várnia kell, amíg a virtuális gép üzembe helyezése megtörténik.

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
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Tesztelés

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Az **Áttekintés** oldalon keresse meg a terheléselosztó magánhálózati IP-címét. A bal oldali menüben válassza a **minden szolgáltatás**  >  **minden erőforrás**  >  **myLoadBalancer** lehetőséget.

3. A **myLoadBalancer** áttekintésében másolja a címet a **magánhálózati IP-cím** mellett.

4. A bal oldali menüben válassza a **minden szolgáltatás**  >  **minden erőforrás** elemet. Az erőforrások listájában, a **CreateIntLBQS-RG** erőforráscsoporthoz válassza a **myTestVM** lehetőséget.

5. Az **Áttekintés** lapon válassza a **kapcsolati**  >  **megerősített** szolgáltatás lehetőséget.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. A **myTestVM** nyissa meg az **Internet Explorert**.

8. Az előző lépésben adja meg az IP-címet a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett lapja megjelenik a böngészőben.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Képernyőkép az IP-címről a böngésző címsorában." border="true":::
   
Ha látni szeretné, hogy a terheléselosztó a három virtuális gép között osztja el a forgalmat, testreszabhatja az egyes virtuális gépek IIS-webkiszolgálójának alapértelmezett lapját. Ezután frissítse manuálisan a webböngészőt az ügyfélszámítógépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforrásokra már nincs szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal távolíthatja el az erőforráscsoportot, a terheléselosztó és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg egy áttekintést az Azure Load Balancerről.
> [!div class="nextstepaction"]
> [Mi az Azure Load Balancer?](load-balancer-overview.md)
