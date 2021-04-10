---
title: Alhálózati delegálás hozzáadása vagy eltávolítása egy Azure-beli virtuális hálózaton
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan adhat hozzá vagy távolíthat el egy delegált alhálózatot egy szolgáltatáshoz az Azure-ban.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 2bb80ba421617d5fd1699826deda00e56f1e43af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943665"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Alhálózati delegálás hozzáadása vagy eltávolítása

Az alhálózati delegálás explicit jogosultságokat biztosít a szolgáltatás számára, hogy a szolgáltatás telepítésekor egyedi azonosítóval hozzon létre szolgáltatás-specifikus erőforrásokat az alhálózatban. Ez a cikk azt ismerteti, hogyan lehet delegált alhálózatot hozzáadni vagy eltávolítani egy Azure-szolgáltatáshoz.

## <a name="portal"></a>Portál

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot és azt az alhálózatot, amelyet később egy Azure-szolgáltatáshoz delegál.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **virtuális hálózat** lehetőséget.
1. A **virtuális hálózat létrehozása** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a *MyVirtualNetwork*. |
    | Címtér | Adja meg a *10.0.0.0/16* értéket. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza az **új létrehozása** elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Hely | Válassza a **EastUS** lehetőséget.|
    | Alhálózat – név | Adja meg a *mySubnet*. |
    | Alhálózat – címtartomány | Adja meg a *10.0.0.0/24* értéket. |
    |||
1. Hagyja a többi értéket alapértelmezettként, majd válassza a **Létrehozás** lehetőséget.

### <a name="permissions"></a>Engedélyek

Ha nem hozta létre azt az alhálózatot, amelyet delegálni szeretne egy Azure-szolgáltatáshoz, a következő engedélyre van szüksége: `Microsoft.Network/virtualNetworks/subnets/write` .

A beépített [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör a szükséges engedélyeket is tartalmazza.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Alhálózat delegálása Azure-szolgáltatásba

Ebben a szakaszban az előző szakaszban létrehozott alhálózatot delegálja egy Azure-szolgáltatáshoz.

1. A portál keresési sávján adja meg a *myVirtualNetwork*. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
2. A keresési eredmények között válassza a *myVirtualNetwork* lehetőséget.
3. Válassza az **alhálózatok** lehetőséget, a **Beállítások** területen, majd válassza a **mySubnet** lehetőséget.
4. A *mySubnet* lapon az **alhálózati delegálás** listáról válassza ki az **alhálózat delegálása szolgáltatáshoz** (például **Microsoft. DBforPostgreSQL/serversv2**) felsorolt szolgáltatásokat.  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Alhálózati delegálás eltávolítása egy Azure-szolgáltatásból

1. A portál keresési sávján adja meg a *myVirtualNetwork*. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
2. A keresési eredmények között válassza a *myVirtualNetwork* lehetőséget.
3. Válassza az **alhálózatok** lehetőséget, a **Beállítások** területen, majd válassza a **mySubnet** lehetőséget.
4. A *mySubnet* lapon az **alhálózati delegálás** listához válassza a **nincs** lehetőséget az **alhálózat delegálása szolgáltatásban** listában felsorolt szolgáltatások közül. 

## <a name="azure-cli"></a>Azure CLI

Készítse elő a környezetet az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroup** nevű erőforráscsoportot a **eastus** helyen:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Az [az network vnet create](/cli/azure/network/vnet) paranccsal hozzon létre a **myResourceGroup** erőforráscsoportban egy **myVnet** nevű virtuális hálózatot egy **mySubnet** nevű alhálózattal.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Engedélyek

Ha nem hozta létre azt az alhálózatot, amelyet delegálni szeretne egy Azure-szolgáltatáshoz, a következő engedélyre van szüksége: `Microsoft.Network/virtualNetworks/subnets/write` .

A beépített [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör a szükséges engedélyeket is tartalmazza.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Alhálózat delegálása Azure-szolgáltatásba

Ebben a szakaszban az előző szakaszban létrehozott alhálózatot delegálja egy Azure-szolgáltatáshoz. 

Az [az Network vnet subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) paranccsal frissítse a **mySubnet** nevű alhálózatot egy Azure-szolgáltatás delegálásával.  Ebben a példában a **Microsoft. DBforPostgreSQL/serversv2** a példában szereplő delegáláshoz van használatban:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

A delegálás ellenőrzéséhez használja az [az Network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show). Ellenőrizze, hogy a szolgáltatás delegálva van-e az alhálózatnak a **szolgáltatásnév** tulajdonság alatt:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Alhálózati delegálás eltávolítása egy Azure-szolgáltatásból

Az az [Network vnet subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) paranccsal távolítsa el a delegálást a **mySubnet** nevű alhálózatból:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
A delegálás ellenőrzéséhez használja az [az Network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show). Ellenőrizze, hogy a szolgáltatás el lett-e távolítva az alhálózatból a **szolgáltatásnév** tulajdonság alatt:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
A parancs kimenete null értékű zárójel:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/cli/azure/group). Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy **myVnet** nevű virtuális hálózatot egy **mySubnet** nevű alhálózattal a New [-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) használatával a **myResourceGroup** [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)használatával. A virtuális hálózat IP-címe **10.0.0.0/16**. A virtuális hálózaton belüli alhálózat **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Engedélyek

Ha nem hozta létre azt az alhálózatot, amelyet delegálni szeretne egy Azure-szolgáltatáshoz, a következő engedélyre van szüksége: `Microsoft.Network/virtualNetworks/subnets/write` .

A beépített [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör a szükséges engedélyeket is tartalmazza.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Alhálózat delegálása Azure-szolgáltatásba

Ebben a szakaszban az előző szakaszban létrehozott alhálózatot delegálja egy Azure-szolgáltatáshoz. 

Az [Add-AzDelegation](/powershell/module/az.network/add-azdelegation) használatával frissítse a **mySubnet** nevű alhálózatot egy **MyDelegation** nevű delegálással egy Azure-szolgáltatáshoz.  Ebben a példában a **Microsoft. DBforPostgreSQL/serversv2** a példában szereplő delegáláshoz van használatban:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
A [Get-AzDelegation](/powershell/module/az.network/get-azdelegation) használatával ellenőrizze a delegálást:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Alhálózati delegálás eltávolítása egy Azure-szolgáltatásból

A [Remove-AzDelegation](/powershell/module/az.network/remove-azdelegation) használatával távolítsa el a delegálást a **mySubnet** nevű alhálózatból:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
A [Get-AzDelegation](/powershell/module/az.network/get-azdelegation) használatával ellenőrizze, hogy a delegálás el lett-e távolítva:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Következő lépések
- Ismerje meg, hogyan [kezelheti az alhálózatokat az Azure-ban](virtual-network-manage-subnet.md).
