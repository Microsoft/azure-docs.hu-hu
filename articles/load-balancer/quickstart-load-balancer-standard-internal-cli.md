---
title: 'Rövid útmutató: Belső terheléselosztás létrehozása – Azure CLI'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre belső terheléselosztást az Azure CLI használatával.
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
ms.openlocfilehash: 2db30024b26352bcc038d606bcdc760b6350d413
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788834"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Rövid útmutató: Belső terheléselosztás létrehozása az Azure CLI használatával

Az azure Azure Load Balancer használatának első lépésekor hozzon létre egy belső terheléselosztást és három virtuális gépet az Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. Ha az alkalmazást használja Azure Cloud Shell a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport egy logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az erőforráscsoportnak adja a **CreateIntLBQS-rg nevet,** a helyet pedig **eastus néven.**

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**Standard termékváltozat**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Az éles számítási feladatokhoz standard termékváltozatú terheléselosztás ajánlott. A SKUS-okkal kapcsolatos további információkért lásd: **[Azure Load Balancer SKUs](skus.md)**.

Ebben a szakaszban egy terheléselosztási terheléselosztást fog létrehozni a virtuális gépek között. Belső terheléselosztás létrehozásakor egy virtuális hálózat lesz konfigurálva a terheléselosztás hálózataként. Az alábbi ábrán az ebben a rövid útmutatóban létrehozott erőforrások láthatóak:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="A rövid útmutatóhoz létrehozott standard terheléselosztási erőforrások." border="false":::

### <a name="configure-the-virtual-network"></a>A virtuális hálózat konfigurálása

A virtuális gépek üzembe helyezése és a terheléseltöltő üzembe helyezése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

#### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az network vnet create használatával.](/cli/azure/network/vnet#az_network_vnet_create) Adja meg az alábbiakat:

* **MyVNet nevű**
* A **10.1.0.0/16 címelőtagja**
* **MyBackendSubnet nevű alhálózat**
* A **10.1.0.0/24 alhálózati előtagja**
* A **CreateIntLBQS-rg erőforráscsoportban**
* Eastus **helye**

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

Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egy nyilvános IP-címet a Azure Bastion gazdagéphez. Adja meg az alábbiakat:

* Hozzon létre egy standard zónaredundáns nyilvános IP-címet **myBastionIP névvel**
* A **CreateIntLBQS-rg-ben**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Új Azure Bastion létrehozása

Az [az network vnet subnet create használatával](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) hozzon létre egy alhálózatot. Adja meg az alábbiakat:

* Neve: **AzureBastionSubnet**
* A **10.1.1.0/24 címelőtagja**
* A **myVNet** virtuális hálózatban
* A **CreateIntLBQS-rg erőforráscsoportban**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Azure Bastion-gazdagép létrehozása

Az [az network bastion create használatával](/cli/azure/network/bastion#az_network_bastion_create) hozzon létre egy gazdagépet. Adja meg az alábbiakat:

* **MyBastionHost nevű**
* A **CreateIntLBQS-rg-ben**
* A **myBastionIP nyilvános IP-címhez van társítva**
* A **myVNet** virtuális hálózathoz van társítva
* Az **eastus helyen**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

A gazdagép üzembe helyezése eltarthat néhány Azure Bastion.

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Standard terheléselosztás esetén győződjön meg arról, hogy a virtuális gépek hálózati biztonsági csoporthoz tartozó hálózati adapterekkel rendelkezik. Hozzon létre egy hálózati biztonsági csoportot [az az network nsg create használatával.](/cli/azure/network/nsg#az_network_nsg_create) Adja meg az alábbiakat:

* **MyNSG nevű**
* A **CreateIntLBQS-rg erőforráscsoportban**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonságicsoport-szabályt [az az network nsg rule create használatával.](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) Adja meg az alábbiakat:

* **MyNSGRuleHTTP nevű**
* Az előző lépésben létrehozott hálózati biztonsági csoportban: **myNSG**
* A **CreateIntLBQS-rg erőforráscsoportban**
* Protokoll **(*)**
* Bejövő **irány**
* Forrás **(*)**
* Cél **(*)**
* A **80-as célport**
* Hozzáférés **engedélyezése**
* **200-as prioritás**

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

Ebben a szakaszban a következőt hozza létre:

* Három hálózati adapter a virtuális gépekhez.
* Három virtuális gép, amelyek a terheléselosztás kiszolgálóiként szolgálnak.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert [az az network nic create használatával.](/cli/azure/network/nic#az_network_nic_create) Adja meg az alábbiakat:

* A **neve myNicVM1,** **myNicVM2**, és **myNicVM3**
* A **CreateIntLBQS-rg erőforráscsoportban**
* A **myVNet** virtuális hálózatban
* A **myBackendSubnet alhálózatban**
* A **myNSG** hálózati biztonsági csoportban

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

Hozza létre a virtuális gépeket az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Adja meg az alábbiakat:

* **MyVM1,** **myVM2** és **myVM3 névvel**
* A **CreateIntLBQS-rg erőforráscsoportban**
* Csatlakoztatva van a **myNicVM1,** **myNicVM2** és **myNicVM3 hálózati adapterhez**
* Virtuálisgép-rendszerkép **win2019datacenter**
* A **1. zóna**, **a 2. zóna** és a **3. zóna**

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

A virtuális gépek üzembe helyezése eltarthat néhány percig.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

* Egy IP-címkészlet, amely a terheléselosztáson fogadja a bejövő hálózati forgalmat.
* Egy második IP-címkészlet, amelyben az első készlet küldi az elosztott terhelésű hálózati forgalmat.
* Állapot-mintavétel, amely meghatározza a virtuálisgép-példányok állapotát.
* Egy terheléselosztási szabály, amely meghatározza, hogyan oszlik el a forgalom a virtuális gépek között.

#### <a name="create-the-load-balancer-resource"></a>A terheléselosztási erőforrás létrehozása

Hozzon létre egy nyilvános terheléselosztást [az az network lb create segítségével.](/cli/azure/network/lb#az_network_lb_create) Adja meg az alábbiakat:

* **MyLoadBalancer nevű**
* Egy **myFrontEnd nevű készlet**
* Egy **myBackEndPool nevű készlet**
* A **myVNet** virtuális hálózathoz van társítva
* A **myBackendSubnet alhálózathoz van társítva**

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

Az állapotfigyelő mintavétel ellenőrzi az összes virtuálisgép-példányt, hogy biztos legyen a hálózati forgalom elküldését. A rendszer eltávolít egy sikertelen mintavétel-ellenőrzéssel a virtuális gépet a terheléselosztásból. A virtuális gép a hiba megoldása után vissza lesz adva a terheléselosztásba.

Hozzon létre egy állapot-mintavételt [az az network lb probe create segítségével.](/cli/azure/network/lb/probe#az_network_lb_probe_create) Adja meg az alábbiakat:

* A virtuális gépek állapotát figyeli
* **MyHealthProbe nevű**
* TCP **protokoll**
* A **80-as port figyelése**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabályok a következőt határozzák meg:

* A bejövő forgalom IP-konfigurációja.
* A forgalom fogadására való IP-címkészlet.
* A szükséges forrás- és célport. 

Hozzon létre egy terheléselosztási szabályt az [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) paranccsal. Adja meg az alábbiakat:

* **MyHTTPRule nevű**
* Figyelés a **myFrontEnd** készlet **80-as** portján
* Elosztott terhelésű hálózati forgalom küldése a **myBackEndPool** címkészletbe a **80-as port használatával** 
* A **myHealthProbe állapot-mintavétel használata**
* TCP **protokoll**
* Üresjárati időkorlát **15 perc**
* A TCP alaphelyzetbe állításának engedélyezése

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

#### <a name="add-vms-to-the-load-balancer-pool"></a>Virtuális gépek hozzáadása a terheléselosztási készlethez

Adja hozzá a virtuális gépeket a háttérkészlethez az [az network nic ip-config address-pool add gombra.](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add) Adja meg az alábbiakat:

* A **myBackEndPool címkészletben**
* A **CreateIntLBQS-rg erőforráscsoportban**
* A **myNicVM1,** **myNicVM2** és **myNicVM3 hálózati adapterhez van társítva**
* A **myLoadBalancer terheléselosztáshoz van társítva**

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

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Az éles számítási feladatokhoz standard termékváltozatú terheléselosztás ajánlott. A SKUS-okkal kapcsolatos további információkért lásd: **[Azure Load Balancer SKUs](skus.md)**.

Ebben a szakaszban egy terheléselosztási terheléselosztást fog létrehozni a virtuális gépek között. Belső terheléselosztás létrehozásakor egy virtuális hálózat lesz konfigurálva a terheléselosztás hálózataként. Az alábbi ábrán az ebben a rövid útmutatóban létrehozott erőforrások láthatóak:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="A rövid útmutatóhoz létrehozott alapszintű terheléselosztási erőforrások." border="false":::

### <a name="configure-the-virtual-network"></a>A virtuális hálózat konfigurálása

A virtuális gépek üzembe helyezése és a terheléseltöltő üzembe helyezése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

#### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az network vnet create használatával.](/cli/azure/network/vnet#az_network_vnet_createt) Adja meg az alábbiakat:

* **MyVNet nevű**
* A **10.1.0.0/16 címelőtagja**
* **MyBackendSubnet nevű alhálózat**
* A **10.1.0.0/24 alhálózati előtagja**
* A **CreateIntLBQS-rg erőforráscsoportban**
* Eastus **helye**

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

Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egy nyilvános IP-címet a Azure Bastion gazdagéphez. Adja meg az alábbiakat:

* Hozzon létre egy standard zónaredundáns nyilvános IP-címet **myBastionIP névvel**
* A **CreateIntLBQS-rg-ben**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Új Azure Bastion létrehozása

Az [az network vnet subnet create használatával](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) hozzon létre egy alhálózatot. Adja meg az alábbiakat:

* Neve: **AzureBastionSubnet**
* A **10.1.1.0/24 címelőtagja**
* A **myVNet** virtuális hálózatban
* A **CreateIntLBQS-rg erőforráscsoportban**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Azure Bastion-gazdagép létrehozása

Az [az network bastion create használatával](/cli/azure/network/bastion#az_network_bastion_create) hozzon létre egy gazdagépet. Adja meg az alábbiakat:

* **MyBastionHost nevű**
* A **CreateIntLBQS-rg-ben**
* A **myBastionIP nyilvános IP-címhez van társítva**
* A **myVNet** virtuális hálózathoz van társítva
* Az **eastus helyen**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

A gazdagép üzembe helyezése eltarthat néhány Azure Bastion.

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Standard terheléselosztás esetén győződjön meg arról, hogy a virtuális gépek hálózati biztonsági csoporthoz tartozó hálózati adapterekkel rendelkezik. Hozzon létre egy hálózati biztonsági csoportot [az az network nsg create használatával.](/cli/azure/network/nsg#az_network_nsg_create) Adja meg az alábbiakat:

* **MyNSG nevű**
* A **CreateIntLBQS-rg erőforráscsoportban**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonságicsoport-szabályt [az az network nsg rule create használatával.](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) Adja meg az alábbiakat:

* **MyNSGRuleHTTP nevű**
* Az előző lépésben létrehozott hálózati biztonsági csoportban a **myNSG**
* A **CreateIntLBQS-rg erőforráscsoportban**
* Protokoll **(*)**
* Bejövő **irány**
* Forrás **(*)**
* Cél **(*)**
* A **80-as célport**
* Hozzáférés **engedélyezése**
* **200-as prioritás**

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

Ebben a szakaszban a következőt hozza létre:

* Három hálózati adapter a virtuális gépekhez.
* A virtuális gépek rendelkezésre állási készlete.
* Három virtuális gép, amelyek kiszolgálóként szolgálnak a terheléselosztáshoz.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert [az az network nic create használatával.](/cli/azure/network/nic#az_network_nic_create) Adja meg az alábbiakat:

* A **neve myNicVM1,** **myNicVM2**, és **myNicVM3**
* A **CreateIntLBQS-rg erőforráscsoportban**
* A **myVNet** virtuális hálózatban
* A **myBackendSubnet alhálózatban**
* A **myNSG** hálózati biztonsági csoportban

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

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>A virtuális gépek rendelkezésre állási készletének létrehozása

Hozza létre a rendelkezésre állási csoportokat [az az vm availability-set create gombra.](/cli/azure/vm/availability-set#az_vm_availability_set_create) Adja meg az alábbiakat:

* Neve: **myAvailabilitySet**
* A **CreateIntLBQS-rg erőforráscsoportban**
* Hely: **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>A virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Adja meg az alábbiakat:

* **MyVM1,** **myVM2 és** **myVM3 névvel**
* A **CreateIntLBQS-rg erőforráscsoportban**
* Csatlakoztatva van a **myNicVM1,** **myNicVM2** és **myNicVM3 hálózati adapterhez**
* Virtuálisgép-rendszerkép **win2019datacenter**
* A **következőben: myAvailabilitySet**


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
A virtuális gépek üzembe helyezése eltarthat néhány percig.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

* Egy IP-címkészlet, amely a terheléselosztáson fogadja a bejövő hálózati forgalmat.
* Egy második IP-címkészlet, ahol az első készlet küldi az elosztott terhelésű hálózati forgalmat.
* Egy állapot-mintavétel, amely meghatározza a virtuálisgép-példányok állapotát.
* Egy terheléselosztási szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

#### <a name="create-the-load-balancer-resource"></a>A terheléselosztási erőforrás létrehozása

Hozzon létre egy nyilvános terheléselosztást [az az network lb create segítségével.](/cli/azure/network/lb#az_network_lb_create) Adja meg az alábbiakat:

* **MyLoadBalancer nevű**
* Egy **myFrontEnd nevű készlet**
* Egy **myBackEndPool nevű készlet**
* A **myVNet** virtuális hálózathoz van társítva
* A **myBackendSubnet alhálózathoz van társítva**

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

Az állapotfigyelő mintavétel az összes virtuálisgép-példányt ellenőrzi, hogy biztos lehet-e a hálózati forgalom elküldését. A rendszer eltávolítja a virtuális gépet a terheléselosztásból, amelynél a mintavétel ellenőrzése sikertelen volt. A hiba megoldása után a virtuális gép vissza lesz adva a terheléselosztásba.

Hozzon létre egy állapot-mintavételt [az az network lb probe create segítségével.](/cli/azure/network/lb/probe#az_network_lb_probe_create) Adja meg az alábbiakat:

* A virtuális gépek állapotát figyeli
* **MyHealthProbe nevű**
* TCP **protokoll**
* A **80-as port figyelése**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabályok a következőt határozzák meg:

* A bejövő forgalom IP-konfigurációja.
* A forgalom fogadására való IP-címkészlet.
* A szükséges forrás- és célport. 

Hozzon létre egy terheléselosztási szabályt az [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) paranccsal. Adja meg az alábbiakat:

* **MyHTTPRule nevű**
* Figyelés a **myFrontEnd** készlet **80-as** portján
* Elosztott terhelésű hálózati forgalom küldése a **myBackEndPool** címkészletbe a **80-as port használatával** 
* A **myHealthProbe állapot-mintavétel használata**
* TCP **protokoll**
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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Virtuális gépek hozzáadása a terheléselosztási készlethez

Adja hozzá a virtuális gépeket a háttérkészlethez az [az network nic ip-config address-pool add gombra.](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add) Adja meg az alábbiakat:

* A **myBackEndPool címkészletben**
* A **CreateIntLBQS-rg erőforráscsoportban**
* A **myNicVM1,** **myNicVM2** és **myNicVM3 hálózati adapterhez van társítva**
* A **myLoadBalancer terheléselosztáshoz van társítva**

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
---
## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Hozza létre a hálózati adaptert [az az network nic create használatával.](/cli/azure/network/nic#az_network_nic_create) Adja meg az alábbiakat:

* **MyNicTestVM nevű**
* A **CreateIntLBQS-rg erőforráscsoportban**
* A **myVNet** virtuális hálózatban
* A **myBackendSubnet alhálózatban**
* A **myNSG** hálózati biztonsági csoportban

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Hozza létre a virtuális gépet az [az vm create gombra.](/cli/azure/vm#az_vm_create) Adja meg az alábbiakat:

* **MyTestVM nevű**
* A **CreateIntLBQS-rg erőforráscsoportban**
* Csatlakoztatva a **myNicTestVM hálózati adapterhez**
* Virtuálisgép-rendszerkép **Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Előfordulhat, hogy néhány percet várnia kell a virtuális gép üzembe helyezésére.

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
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Tesztelés

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Az Áttekintés **lapon** keresse meg a terheléselosztás magánhálózati IP-címét. A bal oldali menüben válassza a **Minden** szolgáltatás  >  **Minden erőforrás**  >  **myLoadBalancer elemet.**

3. A **myLoadBalancer áttekintésében** másolja ki a Magánhálózati **IP-cím melletti címet.**

4. A bal oldali menüben válassza a **Minden szolgáltatás Minden** erőforrás  >  **lehetőséget.** Az erőforrások listájában a **CreateIntLBQS-rg erőforráscsoportban** válassza ki a **myTestVM erőforráscsoportot.**

5. Az Áttekintés **lapon** válassza a **Bastion**  >  **csatlakoztatása lehetőséget.**

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. A **myTestVM-on** nyissa meg **a Internet Explorer.**

8. Írja be az előző lépésben használt IP-címet a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala megjelenik a böngészőben.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Képernyőkép az IP-címről a böngésző címsorában." border="true":::
   
Ahhoz, hogy a terheléselosztás mindhárom virtuális gép között elosztsa a forgalmat, testreszabhatja az egyes virtuális gépek IIS-webkiszolgálójának alapértelmezett lapját. Ezután frissítse manuálisan a webböngészőt az ügyfélszámítógépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforrásokra már nincs szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal távolítsa el az erőforráscsoportot, a terheléselosztást és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg egy áttekintést az Azure Load Balancerről.
> [!div class="nextstepaction"]
> [Mi az Azure Load Balancer?](load-balancer-overview.md)
