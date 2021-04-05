---
title: IPv6 Dual stack-alkalmazás üzembe helyezése – alapszintű Load Balancer – parancssori felület
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan helyezhet üzembe egy alapszintű Load Balancer-alapú kettős verem-alkalmazást az Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: e9bb0f499e8df712107a6fcdbff14ee367a02bf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934154"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>IPv6 Dual stack-alkalmazás üzembe helyezése alapszintű Load Balancer-CLI használatával

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy alapszintű Load Balancerű kettős verem-(IPv4-és IPv6-) alkalmazást az Azure CLI-vel, amely egy kettős veremből álló alhálózattal rendelkező kettős veremből álló virtuális hálózatot tartalmaz, egy alapszintű Load Balancer kettős (IPv4 + IPv6) előtér-konfigurációval, valamint két IP-konfigurációval, kettős hálózati biztonsági csoporttal

Ha a kettős verem (IPV4 + IPv6) alkalmazást standard Load Balancer használatával szeretné üzembe helyezni, tekintse meg a következő témakört: [IPv6 Dual stack-alkalmazás üzembe helyezése standard Load Balancer az Azure CLI használatával](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.49 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A kettős veremből álló virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot az [az Group Create](/cli/azure/group)paranccsal. A következő példában létrehozunk egy *DsResourceGroup01* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>IPv4-és IPv6-alapú nyilvános IP-címek létrehozása a Load Balancerhez
Az IPv4-és IPv6-végpontok internetre való eléréséhez IPv4-és IPv6-alapú nyilvános IP-címek szükségesek a terheléselosztó számára. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal. Az alábbi példa létrehoz egy *dsPublicIP_v4* nevű IPv4-és IPv6 nyilvános IP-címet, és *dsPublicIP_v6* a *DsResourceGroup01* erőforráscsoporthoz:

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Nyilvános IP-címek létrehozása virtuális gépekhez

Ha távolról szeretné elérni a virtuális gépeket az interneten, IPv4 nyilvános IP-címeket kell használnia a virtuális gépekhez. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal.

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Ebben a szakaszban két előtérbeli IP-címet (IPv4 és IPv6) és a terheléselosztó háttér-címkészletet konfigurálja, majd létrehoz egy alapszintű Load Balancer.

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozza létre az alapszintű Load Balancer az [az Network LB Create](/cli/azure/network/lb) nevű **dsLB** , amely tartalmaz egy **dsLbFrontEnd_v4** nevű előtér-készletet, amely az előző lépésben létrehozott IPv4 nyilvános IP-címhez **dsPublicIP_v4** **dsLbBackEndPool_v4** nevű háttér-készlet. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>IPv6-előtérbeli felület létrehozása

Hozzon létre egy IPV6-előtérbeli IP-címet az [az Network LB frontend-IP Create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create)paranccsal. A következő példa létrehoz egy *dsLbFrontEnd_v6* nevű ELŐTÉRBELI IP-konfigurációt, és csatolja a *dsPublicIP_v6* címet:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>IPv6-alapú háttérbeli címkészlet konfigurálása

Hozzon létre egy IPv6-alapú háttér-címkészletet az [az Network LB cím-Pool Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create)paranccsal. Az alábbi példa egy *dsLbBackEndPool_v6*  nevű háttér-címkészletet hoz létre, amely IPv6 hálózati adapter-konfigurációval rendelkező virtuális gépeket tartalmaz:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Állapotminta létrehozása
Hozzon létre egy állapotmintát az [az network lb probe create](/cli/azure/network/lb/probe) paranccsal a virtuális gépek állapotának monitorozásához. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. 

Hozzon létre egy terheléselosztási szabályt az [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) paranccsal. Az alábbi példa létrehozza a *dsLBrule_v4* nevű terheléselosztó-szabályokat, és *dsLBrule_v6* és kiegyensúlyozza az *80* -as *TCP* -port forgalmát az IPv4-és IPv6-előtérbeli IP-konfigurációkhoz:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Néhány virtuális gép üzembe helyezése előtt létre kell hoznia a támogató hálózati erőforrásokat – rendelkezésre állási csoportot, a hálózati biztonsági csoportot, a virtuális hálózatot és a virtuális hálózati adaptereket. 
### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Az alkalmazás rendelkezésre állásának javításához helyezze a virtuális gépeket egy rendelkezésre állási csoportba.

Hozzon létre egy rendelkezésre állási készletet az [az VM rendelkezésre állása-set Create](/cli/azure/vm/availability-set)paranccsal. A következő példa egy *dsAVset* nevű rendelkezésre állási készletet hoz létre:

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a VNET bejövő és kimenő kommunikációját szabályozó szabályokhoz.

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hálózati biztonsági csoport létrehozása az [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Hálózati biztonsági csoport szabályának létrehozása a bejövő és kimenő kapcsolatokhoz

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt, amely engedélyezi az RDP-kapcsolatokat az 3389-as porton keresztül, az internetkapcsolatot a 80-es porton keresztül, valamint az [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)paranccsal

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) paranccsal. Az alábbi példa egy *dsVNET* nevű virtuális hálózatot hoz létre alhálózatokkal *dsSubNET_v4* és *dsSubNET_v6*:

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Hálózati adapterek létrehozása

Hozzon létre virtuális hálózati adaptereket minden virtuális GÉPHEZ az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create)paranccsal. Az alábbi példa egy virtuális hálózati adaptert hoz létre minden egyes virtuális GÉPHEZ. Minden hálózati adapter két IP-konfigurációval rendelkezik (1 IPv4-konfiguráció, 1 IPv6-konfiguráció). Az IPV6-konfigurációt az [az Network NIC IP-config Create paranccsal](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create)hozhatja létre.

```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal. A következő példa két virtuális gépet hoz létre, és a szükséges virtuális hálózati összetevőket, ha azok még nem léteznek. 

A következőképpen hozhat létre virtuális gépeket a *dsVM0* :

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

A következőképpen hozhat létre virtuális gépeket a *dsVM1* :

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>IPv6-alapú kettős verem virtuális hálózatának megtekintése Azure Portal
Az IPv6 kettős verem virtuális hálózatát a következőképpen tekintheti meg Azure Portalban:
1. A portál keresési sávján adja meg a *dsVnet*.
2. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki. Ez elindítja a *dsVnet* nevű kettős verem virtuális hálózat **Áttekintés** lapját. A kettős verem virtuális hálózata két hálózati adaptert jelenít meg, amelyek IPv4-és IPv6-konfigurációval rendelkeznek, amelyek a *dsSubnet* nevű kettős verem alhálózatában találhatók.

  ![IPv6-alapú kettős verem virtuális hálózata az Azure-ban](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy alapszintű Load Balancer hozott létre egy kettős előtér-IP-konfigurációval (IPv4 és IPv6). Létrehozott két virtuális gépet is, amelyek a terheléselosztó háttér-készletéhez hozzáadott kettős IP-konfigurációval (IPV4 + IPv6) rendelkező hálózati adaptereket tartalmaznak. További információ az Azure-beli virtuális hálózatok IPv6-támogatásáról: [Mi az IPv6 for azure Virtual Network?](ipv6-overview.md)
