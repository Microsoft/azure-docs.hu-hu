---
title: IPv6 hozzáadása IPv4-alkalmazáshoz az Azure Virtual Networkben – Azure CLI
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan helyezhet üzembe IPv6-címeket egy meglévő Azure Virtual Network-alkalmazásban az Azure CLI használatával.
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
ms.openlocfilehash: 9a321687a755f8a3d6e6d9139138d61c58764ef4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932597"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>IPv6 hozzáadása IPv4-alkalmazáshoz az Azure Virtual Networkben – Azure CLI

Ez a cikk bemutatja, hogyan adhat IPv6-címeket olyan alkalmazáshoz, amely IPv4 nyilvános IP-címet használ egy Azure-beli virtuális hálózaton egy standard Load Balancer az Azure CLI használatával. A helyben történő frissítés magában foglalja a virtuális hálózatot és az alhálózatot standard Load Balancer, az IPv4 + IPV6-alapú előtér-konfigurációval rendelkező virtuális gépeket, valamint az IPv4 + IPv6-konfigurációval, a hálózati biztonsági csoporttal és a nyilvános IP-címmel rendelkező virtuális gépeket

## <a name="prerequisites"></a>Előfeltételek

- Ez a cikk azt feltételezi, hogy üzembe helyezett egy standard Load Balancert a gyors útmutató [: standard Load Balancer létrehozása – Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md)című témakörben leírtak szerint.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-ipv6-addresses"></a>IPv6-címek létrehozása

Hozzon létre nyilvános IPv6-címet az az [Network Public-IP Create](/cli/azure/network/public-ip) paranccsal a standard Load Balancerhoz. Az alábbi példa egy *PublicIP_v6* nevű IPv6 nyilvános IP-címet hoz létre a *myresourcegroupslb erőforráscsoportban* erőforráscsoporthoz:

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Az IPv6 Load Balancer felületének konfigurálása

Konfigurálja a Load balancert az új IPv6 IP-címmel az [az Network LB frontend-IP Create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) paranccsal:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>IPv6 Load Balancer backend-készlet konfigurálása

Hozza létre az IPv6-címekkel rendelkező hálózati adapterek háttér-készletét az [az Network LB Address-Pool Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) paranccsal a következőképpen:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>IPv6 Load Balancer-szabályok konfigurálása

Hozzon létre IPv6 Load Balancer-szabályokat az [az Network LB Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create)paranccsal.

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>IPv6-címtartományok hozzáadása

Adja hozzá az IPv6-címtartományt a virtuális hálózathoz és a terheléselosztó-t futtató alhálózathoz az alábbiak szerint:

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>IPv6-konfiguráció hozzáadása hálózati adapterekhez

Konfigurálja a virtuális hálózati adaptereket egy IPv6-címmel az [az Network NIC IP-config Create](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create) paranccsal:

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>IPv6-alapú kettős verem virtuális hálózatának megtekintése Azure Portal

Az IPv6 kettős verem virtuális hálózatát a következőképpen tekintheti meg Azure Portalban:
1. A portál keresési sávján adja meg a *myVnet*.
2. Ha a **myVnet** megjelenik a keresési eredmények között, válassza ki. Ez elindítja a *myVNet* nevű kettős verem virtuális hálózat **Áttekintés** lapját. A kettős verem virtuális hálózata a három hálózati adaptert jeleníti meg, amelyek IPv4-és IPv6-konfigurációval rendelkeznek, amelyek a *mySubnet* nevű kettős verem alhálózatában találhatók.

  ![IPv6-alapú kettős verem virtuális hálózata az Azure-ban](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben frissített egy meglévő standard Load Balancer IPv4-es előtér-IP-konfigurációval egy kettős verem (IPv4 és IPv6) konfigurációra. Az IPv6-konfigurációkat a háttér-készletben található virtuális gépek hálózati adapterei számára is felvette. További információ az Azure-beli virtuális hálózatok IPv6-támogatásáról: [Mi az IPv6 for azure Virtual Network?](ipv6-overview.md)
