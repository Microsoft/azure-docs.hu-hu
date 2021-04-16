---
title: Virtuálisgép-méretezési csoport által használt meglévő terheléselosztási rendszer frissítése vagy törlése
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Ez az azure-beli virtuálisgép-méretezési standard Load Balancer első lépések.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 2079eeb97ac935ba24c6ff0616a58fbb28a962f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480086"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoport által használt terheléselosztás frissítése vagy törlése

A virtuálisgép-méretezési készletekkel és a virtuálisgép Azure Load Balancer a következőkkel dolgozhat:

- Szabályok hozzáadása, frissítése és törlése.
- Konfigurációk hozzáadása.
- Törölje a terheléselosztást.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Terheléselosztás beállítása a virtuálisgép-méretezési készletek horizontális felskálzálhatósága érdekében

Győződjön meg arról, hogy a Azure Load Balancer egy bejövő [NAT-készlet](/cli/azure/network/lb/inbound-nat-pool) be van állítva, és hogy a virtuálisgép-méretezési készlet a terheléselosztás háttérkészletében található. Load Balancer automatikusan új bejövő NAT-szabályokat hoz létre a bejövő NAT-készletben, amikor új virtuálisgép-példányokat ad hozzá a virtuálisgép-méretezési csoporthoz.

A bejövő NAT-készlet megfelelő beállításának ellenőrzése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza a **Minden erőforrás lehetőséget.** Ezután válassza **ki a MyLoadBalancer ot** az erőforráslistából.
1. A **Beállítások alatt** válassza a Bejövő **NAT-szabályok lehetőséget.** Ha a jobb oldali panelen megjelenik a virtuálisgép-méretezési készletben az egyes példányok számára létrehozott szabályok listája, akkor bármikor felméretezést is lehet beállítani.

## <a name="add-inbound-nat-rules"></a>Bejövő NAT-szabályok hozzáadása

Egyéni bejövő NAT-szabályok nem hozzáadhatóak. A virtuálisgép-méretezési készlet összes példánya számára hozzáadhat azonban bejövő NAT-szabályokat meghatározott elő- és háttérporttartományokkal.

A virtuálisgép-méretezési készletek bejövő NAT-szabályainak egész készletének hozzáadásához először hozzon létre egy bejövő NAT-készletet a terheléselosztásban. Ezután hivatkozhat a bejövő NAT-készletre a virtuálisgép-méretezési készlet hálózati profiljából. Megjelenik egy teljes példa a CLI használatával.

Az új bejövő NAT-készlet porttartománya nem lehet átfedésben a meglévő bejövő NAT-készletekkel. A meglévő beállított bejövő NAT-készletek megtekintéséhez használja ezt a [CLI-parancsot:](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list)
  
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
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Bejövő NAT-szabályok frissítése

Az egyes bejövő NAT-szabályok nem frissíthetők. A bejövő NAT-szabályok készletét azonban frissítheti egy meghatározott előoldali porttartománysal és egy háttérporttal a virtuálisgép-méretezési készlet összes példánya számára.

A virtuálisgép-méretezési készletek bejövő NAT-szabályainak teljes készletének frissítéséhez frissítse a bejövő NAT-készletet a terheléselosztásban.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Bejövő NAT-szabályok törlése

Az egyes bejövő NAT-szabályok nem törölhetők, de a bejövő NAT-készlet törlésével törölheti a bejövő NAT-szabályok teljes készletét.

A NAT-készlet törléséhez először távolítsa el a méretezési csoportból. Itt látható egy teljes példa a CLI használatával:

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
       -â€“lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Több IP-konfiguráció hozzáadása

Több IP-konfiguráció hozzáadása:

1. A bal oldali menüben válassza a **Minden erőforrás lehetőséget.** Ezután válassza **ki a MyLoadBalancer** erőforráslistát.
1. A **Beállítások alatt** válassza az **Előtere IP-konfigurációja lehetőséget.** Ezután válassza a **Hozzáadás** elemet.
1. Az **Előtere IP-címének hozzáadása lapon** adja meg az értékeket, majd kattintson az **OK gombra.**
1. Ha [új terheléselosztási](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) szabályokra van szükség, kövesse az oktatóanyag [5. és 6.](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) lépését.
1. Hozzon létre új bejövő NAT-szabályokat az újonnan létrehozott előoldali IP-konfigurációk használatával, ha szükséges. Erre az előző szakaszban talál példát.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Egyetlen Virtual Machine Scale Sets mögött több Load Balancer

Hozzon létre bejövő NAT-készletet a Load Balancer, hivatkozik a bejövő NAT-készletre a virtuálisgép-méretezési csoport hálózati profiljában, és végül frissítse a példányokat, hogy a módosítások életbe lépnek. Ismételje meg a lépéseket minden Virtual Machine Scale Sets.

Mindenképpen hozzon létre külön bejövő NAT-készleteket, amelyek nem fedik át az előoldali porttartományokat.
  
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
          -â€“instance-ids *
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
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport által használt előoldali IP-konfiguráció törlése

A méretezési csoport által használt előoldali IP-konfiguráció törlése:

 1. Először törölje az előoldali IP-konfigurációra hivatkozó bejövő NAT-készletet (a bejövő NAT-szabályok készletét). A bejövő szabályok törlésére vonatkozó utasításokat az előző szakaszban talál.
 1. Törölje az előoldali IP-konfigurációra hivatkozó terheléselosztási szabályt.
 1. Törölje az előoldali IP-konfigurációt.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport által használt terheléselosztás törlése

A méretezési csoport által használt előoldali IP-konfiguráció törlése:

 1. Először törölje az előoldali IP-konfigurációra hivatkozó bejövő NAT-készletet (a bejövő NAT-szabályok készletét). A bejövő szabályok törlésére vonatkozó utasításokat az előző szakaszban talál.
 1. Törölje a virtuálisgép-méretezési készletet tartalmazó háttérkészletre hivatkozó terheléselosztási szabályt.
 1. Távolítsa el `loadBalancerBackendAddressPool` a hivatkozást a virtuálisgép-méretezési készlet hálózati profiljából.
 
 Itt látható egy teljes példa a CLI használatával:

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
Végül törölje a terheléselosztási erőforrást.
 
## <a name="next-steps"></a>Következő lépések

A virtuálisgép-méretezési Azure Load Balancer és a virtuálisgép-méretezési készletekkel kapcsolatos további információkért olvassa el a fogalmakat.

> [Azure Load Balancer virtuálisgép-méretezési készletekkel való kapcsolat](load-balancer-standard-virtual-machine-scale-sets.md)
