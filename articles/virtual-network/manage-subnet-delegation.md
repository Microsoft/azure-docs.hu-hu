---
title: Alhálózatdelegálás hozzáadása vagy eltávolítása Azure-beli virtuális hálózatban
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan adhat hozzá vagy távolíthat el delegált alhálózatot egy szolgáltatáshoz az Azure-ban.
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
ms.openlocfilehash: 401124ed4b2794d891ca224ba3dc1c78edcae8d5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783412"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Alhálózatdelegálás hozzáadása vagy eltávolítása

Az alhálózat-delegálás explicit engedélyeket ad a szolgáltatásnak arra, hogy szolgáltatásspecifikus erőforrásokat hozzon létre az alhálózatban egy egyedi azonosító használatával a szolgáltatás üzembe helyezésekor. Ez a cikk azt ismerteti, hogyan adhat hozzá vagy távolíthat el delegált alhálózatot egy Azure-szolgáltatáshoz.

## <a name="portal"></a>Portál

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot és egy alhálózatot, amely később delegálva lesz egy Azure-szolgáltatásnak.

1. A képernyő bal felső részén válassza az **Erőforrás** létrehozása  >  **Hálózati virtuális** hálózat  >  **lehetőséget.**
1. A **Virtuális hálózat létrehozása részen** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be *a Következőt: MyVirtualNetwork.* |
    | Címtér | Írja be a *10.0.0.0/16 adhatja meg a 10.0.0.* |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza **az Új létrehozása lehetőséget,** írja be a *myResourceGroup gombra,* majd kattintson az **OK gombra.** |
    | Hely | Válassza az **EastUS lehetőséget.**|
    | Alhálózat – Név | Adja meg *a mySubnet alhálózatot.* |
    | Alhálózat – címtartomány | Adja meg *a 10.0.0.0/24- et.* |
    |||
1. Hagyja meg a többi alapértelmezett értéket, majd válassza a **Létrehozás lehetőséget.**

### <a name="permissions"></a>Engedélyek

Ha nem hozza létre azt az alhálózatot, amely egy Azure-szolgáltatásnak van delegálva, a következő engedélyre lesz szüksége: `Microsoft.Network/virtualNetworks/subnets/write` .

A beépített [Hálózati](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) közreműködő szerepkör a szükséges engedélyeket is tartalmazza.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Alhálózat delegálása Egy Azure-szolgáltatásnak

Ebben a szakaszban az előző szakaszban létrehozott alhálózatot delegálta egy Azure-szolgáltatásba.

1. A portál keresősávjában írja be a *myVirtualNetwork adhatja meg a következőt:*. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
2. A keresési eredmények között válassza a *myVirtualNetwork lehetőséget.*
3. Válassza **az Alhálózatok** lehetőséget a **BEÁLLÍTÁSOK alatt,** majd válassza ki a **mySubnet alhálózatot.**
4. A *mySubnet* lap Alhálózatdelegálás listájában válasszon az  Alhálózat delegálásának szolgáltatásba (például **Microsoft.DBforPostgreSQL/serversv2)** listában felsorolt szolgáltatások közül.   

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Alhálózat delegálásának eltávolítása egy Azure-szolgáltatásból

1. A portál keresősávjában írja be a *myVirtualNetwork adhatja meg a következőt:*. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
2. A keresési eredmények között válassza a *myVirtualNetwork lehetőséget.*
3. Válassza **az Alhálózatok** lehetőséget a **BEÁLLÍTÁSOK alatt,** majd válassza ki a **mySubnet alhálózatot.**
4. A *mySubnet lap*  Alhálózatdelegálás listájában válassza a **Nincs** lehetőséget az Alhálózat delegálásának szolgáltatásba listában felsorolt **szolgáltatások közül.** 

## <a name="azure-cli"></a>Azure CLI

A környezet előkészítése az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28-as vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroup** nevű erőforráscsoportot az **eastus** helyen:

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

Ha nem ön hozza létre azt az alhálózatot, amelyről delegálni szeretne egy Azure-szolgáltatásba, a következő engedélyre lesz szüksége: `Microsoft.Network/virtualNetworks/subnets/write` .

A beépített [Hálózati](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) közreműködő szerepkör a szükséges engedélyeket is tartalmazza.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Alhálózat delegálható egy Azure-szolgáltatásnak

Ebben a szakaszban az előző szakaszban létrehozott alhálózatot delegálta egy Azure-szolgáltatásba. 

Az [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) használatával frissítse a **mySubnet** nevű alhálózatot egy Azure-szolgáltatás delegálásával.  Ebben a példában **a Microsoft.DBforPostgreSQL/serversv2** a példa delegáláshoz használatos:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

A delegálás alkalmazását az [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show)használatával ellenőrizheti. Ellenőrizze, hogy a szolgáltatás delegálva van-e az alhálózatra a **serviceName tulajdonság alatt:**

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

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Alhálózatdelegálás eltávolítása egy Azure-szolgáltatásból

Az [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) használatával távolítsa el a delegálást a **mySubnet nevű alhálózatból:**

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
A delegálás eltávolítása az [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show)használatával ellenőrizhető. Ellenőrizze, hogy a szolgáltatás el lett-e távolítva az alhálózatról a **serviceName tulajdonság alatt:**

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
A parancs kimenete null szögletes zárójel:
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
Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup segítségével.](/cli/azure/group) Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Az alábbi példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy **myVnet** nevű virtuális hálózatot egy **mySubnet** nevű alhálózattal a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) használatával a **myResourceGroup** erőforráscsoportban a [New-AzVirtualNetwork használatával.](/powershell/module/az.network/new-azvirtualnetwork) A virtuális hálózat IP-címterülete **a következő: 10.0.0.0/16.** A virtuális hálózaton belüli alhálózat a **következő: 10.0.0.0/24.**  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Engedélyek

Ha nem hozza létre azt az alhálózatot, amely egy Azure-szolgáltatásnak van delegálva, a következő engedélyre lesz szüksége: `Microsoft.Network/virtualNetworks/subnets/write` .

A beépített [Hálózati](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) közreműködő szerepkör a szükséges engedélyeket is tartalmazza.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Alhálózat delegálása Egy Azure-szolgáltatásnak

Ebben a szakaszban az előző szakaszban létrehozott alhálózatot delegálhatja egy Azure-szolgáltatásba. 

Az [Add-AzDelegation](/powershell/module/az.network/add-azdelegation) használatával frissítheti a **mySubnet** nevű alhálózatot egy **myDelegation nevű delegálással** egy Azure-szolgáltatásba.  Ebben a példában **a Microsoft.DBforPostgreSQL/serversv2** a példadelegáláshoz használatos:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
A [delegálás ellenőrzéséhez használja a Get-AzDelegationt:](/powershell/module/az.network/get-azdelegation)

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
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Alhálózatdelegálás eltávolítása egy Azure-szolgáltatásból

A [Remove-AzDelegation](/powershell/module/az.network/remove-azdelegation) használatával távolítsa el a delegálást a **mySubnet nevű alhálózatból:**

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
A [Get-AzDelegation használatával ellenőrizze,](/powershell/module/az.network/get-azdelegation) hogy a delegálás el lett-e távolítva:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Következő lépések
- Ismerje meg, hogyan [kezelheti az alhálózatokat az Azure-ban.](virtual-network-manage-subnet.md)
