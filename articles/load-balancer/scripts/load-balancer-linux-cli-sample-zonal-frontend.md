---
title: Virtuális gépek terheléselosztása zónán belül – Azure CLI
description: Ez az Azure CLI-példaszkript bemutatja, hogyan lehet elosztani a forgalom jelentette terhelést egy adott rendelkezésre állási zónában található virtuális gépek között
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: e0d43ae8f76e89ffe429ec1778922ee465ad7db4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696512"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-within-a-specific-availability-zone"></a>Azure CLI parancsfájl-példa: az adott rendelkezésre állási zónán belüli virtuális gépek forgalmának elosztása

A CLI-példaszkript minden összetevőt létrehoz, amelyre szükség van több, magas rendelkezésre állásra és terheléselosztásra konfigurált Ubuntu rendszerű virtuális gép futtatásához egy adott rendelkezésre állási zónán belül. A szkript futtatása után három virtuális géppel rendelkezik majd, amelyek egy régió ugyanazon rendelkezésre állási zónájában találhatók, és egy standard Azure Load Balanceren keresztül érhetők el. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
  #!/bin/bash

  # Create a resource group.
   az group create \
    --name myResourceGroup \
    --location westeurope

  # Create a virtual network.
   az network vnet create \
    --resource-group myResourceGroup \
    --location westeurope \
    --name myVnet \
    --subnet-name mySubnet

  # Create a zonal Standard public IP address.
   az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --sku Standard
    --zone 1

  # Create an Azure Load Balancer.
   az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --sku Standard

  # Creates an LB probe on port 80.
   az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80

  # Creates an LB rule for port 80.
   az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRuleWeb \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe

  # Create three NAT rules for port 22.
   for i in `seq 1 3`; do
    az network lb inbound-nat-rule create \
     --resource-group myResourceGroup \
     --lb-name myLoadBalancer \
     --name myLoadBalancerRuleSSH$i \
     --protocol tcp \
     --frontend-port 422$i \
     --backend-port 22 \
     --frontend-ip-name myFrontEndPool
   done

  # Create a network security group
   az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup

  # Create a network security group rule for port 22.
   az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*'  \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 1000

  # Create a network security group rule for port 80.
    az network nsg rule create \
     --resource-group myResourceGroup \
     --nsg-name myNetworkSecurityGroup \
     --name myNetworkSecurityGroupRuleHTTP \
     --protocol tcp \
     --direction inbound \
     --source-address-prefix '*' \
     --source-port-range '*' \
     --destination-address-prefix '*' \
     --destination-port-range 80 \
     --access allow \
     --priority 2000

  # Create three virtual network cards and associate with public IP address and NSG.
  for i in `seq 1 3`; do
   az network nic create \
     --resource-group myResourceGroup \
     --name myNic$i \
     --vnet-name myVnet \
     --subnet mySubnet \
     --network-security-group myNetworkSecurityGroup \
     --lb-name myLoadBalancer \
     --lb-address-pools myBackEndPool \
     --lb-inbound-nat-rules myLoadBalancerRuleSSH$i
  done

# Create three virtual machines, this creates SSH keys if not present.
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroup \
    --name myVM$i \
    --zone 1 \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
done

```

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép, egy rendelkezésre állási csoport, egy terheléselosztó és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) | Létrehoz egy nyilvános IP-címet egy statikus IP-címmel és egy hozzárendelt DNS-névvel. |
| [az network lb create](/cli/azure/network/lb#az-network-lb-create) | Azure-terheléselosztót hoz létre. |
| [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) | Terheléselosztói mintavételt hoz létre. A terheléselosztói mintavétel a terheléselosztó-készlet egyes virtuális gépeinek figyelésére használható. Ha valamelyik virtuális gép elérhetetlenné válik, a terheléselosztó nem irányít rá forgalmat. |
| [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Terheléselosztó-szabályt hoz létre. Ebben a példában egy, a 80-es portra vonatkozó szabály jön létre. A hálózati terheléselosztóra érkező HTTP-forgalom a terheléselosztó csoporthoz tartozó egyik virtuális gép 80-as portjára lesz irányítva. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Létrehoz egy terheléselosztó hálózati címfordítási (Network Address Translation, NAT) szabályt.  A NAT-szabályok a terheléselosztó egyik portját hozzárendelik egy virtuális gép valamelyik portjához. Ebben a példában egy NAT-szabályt hozunk létre az SSH-forgalomnak a terheléselosztó csoportba tartozó egyes virtuális gépekre történő irányítására.  |
| [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) | Létrehoz egy hálózati biztonsági csoportot (NSG), amely biztonsági határként szolgál az internet és a virtuális gép között. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Létrehoz egy NSG-szabályt a befelé irányuló forgalom engedélyezésére. Ebben a példában a 22-es portot nyitjuk meg az SSH-forgalom számára. |
| [az network nic create](/cli/azure/network/nic#az-network-nic-create) | Létrehoz egy virtuális hálózati kártyát, és csatlakoztatja a virtuális hálózathoz, az alhálózathoz és az NSG-hez. |
| [az vm create](/cli/azure/vm#az-vm-create) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure-hálózatkezelési CLI-példaszkripteket az [Azure-hálózatkezelés dokumentációjában](../cli-samples.md) talál.