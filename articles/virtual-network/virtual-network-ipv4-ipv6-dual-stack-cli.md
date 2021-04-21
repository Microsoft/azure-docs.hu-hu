---
title: Kettős vermű IPv6-alkalmazás üzembe helyezése – Alapszintű Load Balancer – CLI
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan helyezhet üzembe alapszintű szolgáltatásokkal egy kettős vermű (IPv4+ IPv6) alkalmazást az Azure CLI Load Balancer használatával.
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
ms.openlocfilehash: e6205b4c5428f03459bc75c6fbb2a40458f0f898
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773206"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Kettős vermű IPv6-alkalmazás üzembe helyezése alapszintű Load Balancer – CLI

Ez a cikk bemutatja, hogyan helyezhet üzembe egy kettős veremű (IPv4 + IPv6) alkalmazást alapszintű Load Balancer-vel az Azure CLI használatával, amely kettős vermű alhálózattal, alapszintű Load Balancer kettős (IPv4 + IPv6) előtér-konfigurációval, kettős IP-konfigurációval és kettős hálózati biztonsági csoport szabályaival és kettős nyilvános IP-címmel rendelkezik.

Kettős verem (IPV4 + IPv6) alkalmazás standard Load Balancer használatával való üzembe helyezéséről lásd: Kettős veremű [IPv6-alkalmazás](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)üzembe helyezése standard Load Balancer Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.49-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A kettős verem virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot [az az group create segítségével.](/cli/azure/group) Az alábbi példa egy *DsResourceGroup01* nevű erőforráscsoportot hoz létre az *eastus* helyen:

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>IPv4- és IPv6-alapú nyilvános IP-címek létrehozása a terheléselosztáshoz
Az IPv4- és IPv6-végpontok internetes eléréséhez IPv4- és IPv6-alapú nyilvános IP-címekre van szükség a terheléselosztáshoz. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal. A következő példa létrehozza az dsPublicIP_v4 és dsPublicIP_v6  nevű nyilvános  IPv4- és IPv6-IP-címet a *DsResourceGroup01* erőforráscsoportban:

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

Ahhoz, hogy távolról el tudja elérni a virtuális gépeket az interneten, IPv4 nyilvános IP-címekre van szüksége a virtuális gépekhez. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal.

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

Ebben a szakaszban két előtere IP-címet (IPv4 és IPv6) és a háttércímkészletet konfigurálja a terheléselosztáshoz, majd létrehoz egy alapszintű Load Balancer.

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozza létre az alapszintű Load Balancer-t az [az network lb create](/cli/azure/network/lb) nevű **dsLB-vel,** amely tartalmaz egy **dsLbFrontEnd_v4** nevű előterekészletet, egy **dsLbBackEndPool_v4** nevű háttérkészletet, amely az előző lépésben létrehozott IPv4 nyilvános IP-címhez **dsPublicIP_v4** van társítva. 

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

### <a name="create-ipv6-frontend"></a>IPv6-előtere létrehozása

Hozzon létre egy IPV6 előtere IP-címet [az az network lb frontend-ip create segítségével.](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) Az alábbi példa létrehoz egy dsLbFrontEnd_v6  nevű előtere *IP-konfigurációt,* és csatolja dsPublicIP_v6 címet:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>IPv6-háttércímkészlet konfigurálása

Hozzon létre egy IPv6-háttércímkészletet [az az network lb address-pool create segítségével.](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) Az alábbi példa egy dsLbBackEndPool_v6  nevű háttércímkészletet hoz létre az IPv6 nic-konfigurációval konfigurált virtuális gépekhez:

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

Hozzon létre egy terheléselosztási szabályt az [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) paranccsal. Az alábbi példa egy *dsLBrule_v4* nevű terheléselosztási szabályt hoz létre, *dsLBrule_v6* a *80-as* *TCP-port* forgalmát pedig az IPv4- és IPv6-alapú előtere IP-konfigurációk között egyensúlyba hozza:

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
Néhány virtuális gép üzembe helyezése előtt létre kell hoznia a támogató hálózati erőforrásokat – a rendelkezésre állási csoportot, a hálózati biztonsági csoportot, a virtuális hálózatot és a virtuális hálózati adaptereket. 
### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Az alkalmazás rendelkezésre állásának javítása érdekében helyezze a virtuális gépeket egy rendelkezésre állási készletbe.

Hozzon létre egy rendelkezésre állási készletet [az az vm availability-set create gombra.](/cli/azure/vm/availability-set) Az alábbi példa egy *dsAVset* nevű rendelkezésre állási készletet hoz létre:

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a virtuális hálózat bejövő és kimenő kommunikációját szabályozó szabályokhoz.

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hálózati biztonsági csoport létrehozása az [az network nsg create segítségével](/cli/azure/network/nsg#az_network_nsg_create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Hálózati biztonságicsoport-szabály létrehozása bejövő és kimenő kapcsolatokhoz

Hozzon létre egy hálózati biztonságicsoport-szabályt, amely engedélyezi az RDP-kapcsolatokat a 3389-es porton, az internetkapcsolatot a 80-as porton keresztül, valamint az [az network nsg rule create kimenő kapcsolatait.](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)

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

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Az alábbi példa egy *dsVNET* nevű virtuális hálózatot hoz létre a következő alhálózatokkal dsSubNET_v4 és *dsSubNET_v6:* 

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

Hozzon létre virtuális hálózati adaptereket minden virtuális géphez [az az network nic create segítségével.](/cli/azure/network/nic#az_network_nic_create) Az alábbi példa egy virtuális hálózati adaptert hoz létre az egyes virtuális gépekhez. Minden hálózati adapter két IP-konfigurációval rendelkezik (1 IPv4-konfiguráció, 1 IPv6-konfiguráció). Az IPV6-konfigurációt az [az network nic ip-config create segítségével hozhatja létre.](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create)

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

Hozza létre a virtuális gépeket az [az vm create gombra.](/cli/azure/vm#az_vm_create) Az alábbi példa két virtuális gépet és a szükséges virtuális hálózati összetevőket hozza létre, ha még nem léteznek. 

Hozza létre a *dsVM0 virtuális gépet* a következőképpen:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Hozza létre a *dsVM1 virtuális gépet* a következőképpen:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>A kettős veremŰ IPv6 virtuális hálózat megtekintése a Azure Portal
A kettős veremŰ IPv6 virtuális hálózatot a következő Azure Portal megtekintheti:
1. A portál keresősávjában írja be a *következőt: dsVnet.*
2. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki. Ezzel elindítja **a** *dsVnet* nevű kettős verem virtuális hálózat Áttekintés lapját. A kettős verem virtuális hálózatban látható a két hálózati adapter IPv4- és IPv6-konfigurációval, amelyek a *dsSubnet* nevű kettős verem alhálózatban találhatók.

  ![IPv6 kettős verem virtuális hálózat az Azure-ban](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozott egy alapszintű Load Balancer kettős előtere IP-konfigurációval (IPv4 és IPv6). Emellett létrehozott egy két virtuális gépet, amelyek kettős IP-konfigurációval (IPV4 + IPv6) is tartalmaztak hálózati címeket, amelyek fel vannak adva a terheléselosztás háttérkészletébe. További információ az Azure-beli virtuális hálózatok [IPv6-támogatásról: Mi az az IPv6 az Azure Virtual Network?](ipv6-overview.md)
