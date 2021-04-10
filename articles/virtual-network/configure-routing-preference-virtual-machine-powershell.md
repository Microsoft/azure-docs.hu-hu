---
title: Útválasztási beállítások konfigurálása egy virtuális gép számára – Azure PowerShell
description: Megtudhatja, hogyan hozhat létre egy nyilvános IP-címmel rendelkező virtuális gépet az útválasztási beállítások megválasztásával a Azure PowerShell használatával.
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
ms.openlocfilehash: 8ffa7ef7b3539e3cefc4e2f8621d7c67cd3b56b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672964"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Útválasztási beállítások konfigurálása virtuális géphez Azure PowerShell használatával

Ez a cikk bemutatja, hogyan konfigurálhatja a virtuális gépek útválasztási beállításait. A virtuális gépről érkező internetes forgalom az INTERNETSZOLGÁLTATÓ hálózatán keresztül lesz átirányítva, ha az **Internet** lehetőséget választja útválasztási preferenciaként. Az alapértelmezett útválasztás a Microsoft globális hálózatán keresztül történik.

Ebből a cikkből megtudhatja, hogyan hozhat létre egy nyilvános IP-címmel rendelkező virtuális gépet, amely úgy van beállítva, hogy Azure PowerShell használatával irányítsa át a forgalmat az ISP-hálózaton keresztül.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
1. Ha a Cloud Shell használja, ugorjon a 2. lépésre. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be az Azure-ba `Connect-AzAccount` .
2. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal. Az alábbi példa egy erőforráscsoportot hoz létre az USA keleti régiója Azure-régióban:

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A virtuális gépek internetről való eléréséhez nyilvános IP-címekre van szükség. Hozzon létre nyilvános IP [-címeket a New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). A következő példa létrehoz egy *MyPublicIP* útválasztási *preferencia típusú IPv4* nyilvános IP-címet az *USA keleti* régiójában az *MyResourceGroup* -erőforráscsoporthoz:

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

A virtuális gép üzembe helyezése előtt létre kell hoznia a támogató hálózati erőforrásokat – a hálózati biztonsági csoportot, a virtuális hálózatot és a virtuális hálózati ADAPTERt.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). A következő példa egy *myNSG* nevű NSG hoz létre.

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Az alábbi példa egy *myVNET* nevű virtuális hálózatot hoz létre a *mySubNet*:

### <a name="create-a-subnet"></a>Alhálózat létrehozása

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>Hálózati adapter létrehozása

Virtuális hálózati adapterek létrehozása a [New-AzNetworkInterface] (/PowerShell/Module/az.Network/New-aznetworkinterface. A következő példa egy virtuális hálózati adaptert hoz létre.

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

Most már létrehozhatja a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm)használatával. A következő példa két virtuális gépet hoz létre, és a szükséges virtuális hálózati összetevőket, ha azok még nem léteznek.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>A virtuális géphez való hálózati forgalom engedélyezése

Ahhoz, hogy az internetről csatlakozhasson a nyilvános IP-címhez, győződjön meg arról, hogy a szükséges portok nyitva vannak minden olyan hálózati biztonsági csoportban, amelyet a hálózati adapterhez, a hálózati adapterhez vagy mindkettőhöz kapcsolódott. A hálózati adapterek és az alhálózatok érvényes biztonsági szabályait a [portál](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), a [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)vagy a [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)használatával tekintheti meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához.

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

* További információ az [útválasztási beállításokról a nyilvános IP-címekben](routing-preference-overview.md).
* További információ az Azure [-beli nyilvános IP-címekről](./public-ip-addresses.md#public-ip-addresses) .
* További információ a [nyilvános IP-címek beállításairól](virtual-network-public-ip-address.md#create-a-public-ip-address).