---
title: Virtuálisgép-méretezési csoportok által használt meglévő Load Balancer frissítése vagy törlése
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Ezzel a cikkből megtudhatja, hogyan kezdheti el az Azure standard Load Balancer és a virtuálisgép-méretezési csoportokat.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 952889777e4236d7fa03fad5b1bdbf98499f7066
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721310"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportokkal használt terheléselosztó frissítése vagy törlése

Ha a virtuálisgép-méretezési csoportokkal és a Azure Load Balancer egy példányával dolgozik, a következőket teheti:

- Szabályok hozzáadása, frissítése és törlése.
- Konfigurációk hozzáadása.
- Törölje a Load balancert.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Terheléselosztó beállítása a virtuálisgép-méretezési csoportok horizontális felskálázásához

Győződjön meg arról, hogy a Azure Load Balancer példánya egy [bejövő NAT](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) -készlettel rendelkezik, és hogy a virtuálisgép-méretezési csoport a terheléselosztó backend-készletében van. A Load Balancer automatikusan új bejövő NAT-szabályokat hoz létre a bejövő NAT-készletben, amikor új virtuálisgép-példányok kerülnek a virtuálisgép-méretezési csoportba.

Annak ellenőrzését, hogy a bejövő NAT-készlet megfelelően van-e beállítva:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza a **minden erőforrás** elemet. Ezután válassza a **MyLoadBalancer** lehetőséget az erőforráslista listából.
1. A **Beállítások** területen válassza a **bejövő NAT-szabályok** elemet. Ha a jobb oldali ablaktáblában megjelenik a virtuálisgép-méretezési csoport minden egyes példányához létrehozott szabályok listája, akkor az összes beállítás a méretezéshez bármikor felhasználható.

## <a name="add-inbound-nat-rules"></a>Bejövő NAT-szabályok hozzáadása

Az egyes bejövő NAT-szabályok nem vehetők fel. A virtuálisgép-méretezési csoport összes példányához azonban hozzáadhat egy bejövő NAT-szabályt a definiált előtér-porttartomány és a háttér-port használatával.

A virtuálisgép-méretezési csoportokhoz tartozó bejövő NAT-szabályok teljes készletének hozzáadásához először hozzon létre egy bejövő NAT-készletet a terheléselosztó számára. Ezután hivatkozzon a bejövő NAT-készletre a virtuálisgép-méretezési csoport hálózati profiljából. Megjelenik egy teljes példa a CLI használatával.

Az új bejövő NAT-készletnek nem lehetnek átfedésben lévő előtér-porttartomány a meglévő bejövő NAT-készletekkel. A beállított meglévő bejövő NAT-készletek megtekintéséhez használja ezt a CLI- [parancsot](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list):
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Bejövő NAT-szabályok frissítése

Az egyes bejövő NAT-szabályok nem frissíthetők. A bejövő NAT-szabályok készletét azonban frissítheti egy meghatározott előtér-porttartomány és egy háttér-port használatával a virtuálisgép-méretezési csoport összes példányához.

A virtuálisgép-méretezési csoportokhoz tartozó bejövő NAT-szabályok teljes készletének frissítéséhez frissítse a bejövő NAT-készletet a terheléselosztó számára.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Bejövő NAT-szabályok törlése

Az egyes bejövő NAT-szabályok nem törölhetők, azonban a bejövő NAT-készlet törlésével törölheti a bejövő NAT-szabályok teljes készletét.

A NAT-készlet törléséhez először távolítsa el azt a méretezési csoportból. A CLI-t használó teljes példa itt látható:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Több IP-konfiguráció hozzáadása

Több IP-konfiguráció hozzáadása:

1. A bal oldali menüben válassza a **minden erőforrás** elemet. Ezután válassza a **MyLoadBalancer** lehetőséget az erőforráslista listából.
1. A **Beállítások** területen válassza a előtér **IP-konfiguráció** elemet. Ezután válassza a **Hozzáadás** elemet.
1. Az előtérbeli **IP-cím hozzáadása** lapon adja meg az értékeket, majd kattintson **az OK gombra**.
1. Kövesse az oktatóanyag [5. lépését](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) és a [6. lépését](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) , ha új terheléselosztási szabályokra van szükség.
1. Szükség esetén hozza létre a bejövő NAT-szabályok új készletét az újonnan létrehozott előtér-IP-konfigurációk használatával. Egy példa az előző szakaszban található.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Több Virtual Machine Scale Sets egyetlen Load Balancer mögött

Bejövő NAT-készlet létrehozása a Load Balancerban, a virtuálisgép-méretezési csoport hálózati profiljában a bejövő NAT-készletre hivatkozva, végül frissítse a példányokat a módosítások érvénybe léptetéséhez. Ismételje meg az összes Virtual Machine Scale Sets lépéseit.

Ügyeljen arra, hogy különálló bejövő NAT-készleteket hozzon létre, amelyek nem átfedésben vannak a frontend-tartományokkal.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport által használt előtér-IP-konfiguráció törlése

A méretezési csoport által használt előtér-IP-konfiguráció törlése:

 1. Először törölje a bejövő NAT-készletet (a bejövő NAT-szabályok készletét), amely az előtér-IP-konfigurációra hivatkozik. A bejövő szabályok törlésének utasításait az előző szakaszban találja.
 1. Törölje az előtér-IP-konfigurációra hivatkozó terheléselosztási szabályt.
 1. Törölje az előtér-IP-konfigurációt.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport által használt terheléselosztó törlése

A méretezési csoport által használt előtér-IP-konfiguráció törlése:

 1. Először törölje a bejövő NAT-készletet (a bejövő NAT-szabályok készletét), amely az előtér-IP-konfigurációra hivatkozik. A bejövő szabályok törlésének utasításait az előző szakaszban találja.
 1. Törölje a virtuálisgép-méretezési csoportját tartalmazó háttér-készletre hivatkozó terheléselosztási szabályt.
 1. Távolítsa el a `loadBalancerBackendAddressPool` hivatkozást a virtuálisgép-méretezési csoport hálózati profiljából.
 
 A CLI-t használó teljes példa itt látható:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
Végezetül törölje a terheléselosztó erőforrását.
 
## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Load Balancer és a virtuálisgép-méretezési csoportokról, olvassa el a fogalmakat ismertető témakört.

> [Azure Load Balancer a virtuálisgép-méretezési csoportokkal](load-balancer-standard-virtual-machine-scale-sets.md)
