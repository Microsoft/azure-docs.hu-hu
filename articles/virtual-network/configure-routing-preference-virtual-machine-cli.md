---
title: Útválasztási beállítások konfigurálása virtuális géphez – Azure CLI
description: Megtudhatja, hogyan hozhat létre virtuális gépet nyilvános IP-címmel, útválasztási beállításokkal az Azure parancssori felület (CLI) használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9155c3114d5a5a1b8729351dc189bc1e5c22369
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764476"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Virtuális gép útválasztási beállításainak konfigurálása az Azure CLI használatával

Ez a cikk bemutatja, hogyan konfigurálhatja egy virtuális gép útválasztási beállításait. Ha útválasztási beállításként az Internet lehetőséget választja, a  virtuális gépről származó internetes forgalmat a rendszer az internetszolgáltatói hálózaton keresztül irányítja. Az alapértelmezett útválasztás a Microsoft globális hálózatán keresztül érhető el.

Ez a cikk bemutatja, hogyan hozhat létre olyan nyilvános IP-címmel virtuális gépet, amely a nyilvános interneten keresztüli forgalomirányításra van beállítva az Azure CLI használatával.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
1. Ha a Cloud Shell, ugorjon a 2. lépésre. Nyisson meg egy parancs-munkamenetet, és jelentkezzen be az Azure-ba a `az login` paranccsal.
2. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az alábbi példa létrehoz egy erőforráscsoportot az USA keleti Azure-régiójában:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
A virtuális gépek internetről való eléréséhez létre kell hoznia egy nyilvános IP-címet. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal. Az alábbi példa egy Internet útválasztási beállítástípus nyilvános IP-címét hozza létre *az* *USA keleti régiójában:*

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Virtuális gép üzembe helyezése előtt létre kell hoznia támogató hálózati erőforrásokat – hálózati biztonsági csoportot, virtuális hálózatot és virtuális hálózati adaptert.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Az [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) segítségével hozzon létre egy hálózati biztonsági csoportot a virtuális hálózat bejövő és kimenő kommunikációra vonatkozó szabályaihoz

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Az alábbi példa egy *mySubNet* alhálózatokkal létrehozott *myVNET* nevű virtuális hálózatot hoz létre:

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Hálózati adapter létrehozása

Hozzon létre egy virtuális hálózati adaptert a virtuális géphez [az az network nic create segítségével.](/cli/azure/network/nic#az_network_nic_create) Az alábbi példa egy virtuális hálózati adaptert hoz létre, amely a virtuális géphez lesz csatolva.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példa egy Windows Server 2019 rendszerű virtuális gépet és a szükséges virtuális hálózati összetevőket hozza létre, ha még nem léteznek.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

- További információ a nyilvános [IP-címek útválasztási beállítási beállításról.](routing-preference-overview.md)
- További információ az [Azure-beli nyilvános](./public-ip-addresses.md#public-ip-addresses) IP-címekről.
- További információ a [nyilvános IP-címek beállításairól.](virtual-network-public-ip-address.md#create-a-public-ip-address)
