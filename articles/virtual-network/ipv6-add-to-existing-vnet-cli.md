---
title: IPv6 hozzáadása IPv4-alkalmazáshoz az Azure-beli virtuális hálózaton – Azure CLI
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan helyezhet üzembe IPv6-címeket egy meglévő azure-beli virtuális hálózaton az Azure CLI használatával.
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
ms.openlocfilehash: 5835ea4d80f9c4111b76672facc4a0250ae0079a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769858"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>IPv6 hozzáadása IPv4-alkalmazáshoz az Azure-beli virtuális hálózaton – Azure CLI

Ez a cikk bemutatja, hogyan adhat IPv6-címeket egy olyan alkalmazáshoz, amely IPv4 nyilvános IP-címet használ egy Azure-beli virtuális hálózatban egy standard Load Balancer Azure CLI használatával. A helyi frissítés tartalmaz egy virtuális hálózatot és alhálózatot, egy standard Load Balancer-t IPv4 + IPV6 előtér-konfigurációval, IPv4 + IPv6 konfigurációval és nyilvános IP-címekkel konfigurált hálózati adapterekkel.

## <a name="prerequisites"></a>Előfeltételek

- Ez a cikk feltételezi, hogy üzembe helyezett egy standard Load Balancer a Rövid útmutató: Új alkalmazás létrehozása [– Azure CLI standard Load Balancer című cikkben leírtak szerint.](../load-balancer/quickstart-load-balancer-standard-public-cli.md)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28-as vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-ipv6-addresses"></a>IPv6-címek létrehozása

Hozzon létre nyilvános IPv6-címet az [az network public-ip create segítségével](/cli/azure/network/public-ip) a standard Load Balancer. A következő példa létrehoz egy nyilvános IPv6 IP-címet *PublicIP_v6* a *myResourceGroupSLB* erőforráscsoportban:

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Az IPv6-terheléselosztás előtere konfigurálása

Konfigurálja a terheléselosztást az új IPv6 [IP-címmel az az network lb frontend-ip create használatával](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) az alábbiak szerint:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>IPv6-terheléselosztás háttérkészletének konfigurálása

Hozza létre a hálózati adapterek háttérkészletét IPv6-címekkel [az az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) használatával a következőképpen:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>IPv6-terheléselosztási szabályok konfigurálása

Hozzon létre IPv6-terheléselosztási szabályokat [az az network lb rule create segítségével.](/cli/azure/network/lb/rule#az_network_lb_rule_create)

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

Adja hozzá az IPv6-címtartományokat a terheléselosztást üzemeltető virtuális hálózathoz és alhálózathoz a következőképpen:

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

## <a name="add-ipv6-configuration-to-nics"></a>IPv6-konfiguráció hozzáadása hálózati számítógépekhez

Konfigurálja a virtuális gép hálózati adapterét [IPv6-címmel az az network nic ip-config create használatával](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) az alábbiak szerint:

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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>A kettős veremŰ IPv6 virtuális hálózat megtekintése a Azure Portal

A kettős veremŰ IPv6 virtuális hálózatot a következő Azure Portal megtekintheti:
1. A portál keresősávjában írja be a *következőt: myVnet.*
2. Amikor **a myVnet** megjelenik a keresési eredmények között, válassza ki. Ezzel elindítja **a** myVNet nevű kettős verem virtuális hálózat Áttekintés *lapját.* A kettős verem virtuális hálózat három, IPv4- és IPv6-konfigurációval is rendelkezik, amelyek a *mySubnet* nevű kettős verem alhálózatban találhatók.

  ![IPv6 kettős verem virtuális hálózat az Azure-ban](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy meglévő IPv4 standard Load Balancer IP-konfigurációt frissített kettős verem (IPv4 és IPv6) konfigurációra. Emellett IPv6-konfigurációkat is hozzáadott a háttérkészletben található virtuális gépek hálózati számítógépeihez. További információ az Azure-beli virtuális hálózatok [IPv6-támogatásról: Mi az az IPv6 az Azure Virtual Network?](ipv6-overview.md)
