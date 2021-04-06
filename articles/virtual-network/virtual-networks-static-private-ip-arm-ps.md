---
title: Statikus magánhálózati IP-címmel rendelkező virtuális gép létrehozása – Azure PowerShell
description: Megtudhatja, hogyan hozhat létre egy magánhálózati IP-címmel rendelkező virtuális gépet a PowerShell használatával.
services: virtual-network
documentationcenter: na
author: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 43c2e2c8905ca60e59caed3fdf1b4920f509fd88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98783784"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Statikus magánhálózati IP-címmel rendelkező virtuális gép létrehozása a PowerShell használatával

A virtuális gépet (VM) statikus magánhálózati IP-címmel is létrehozhatja. Statikus magánhálózati IP-címet rendeljen hozzá, nem pedig dinamikus címet, ha ki szeretné választani, hogy egy alhálózat melyik címét rendeli hozzá a virtuális géphez. További információ a [statikus magánhálózati IP-címekről](./public-ip-addresses.md#allocation-method). Ha egy meglévő virtuális géphez hozzárendelt magánhálózati IP-címet szeretne módosítani dinamikusról statikusra, vagy nyilvános IP-címekkel szeretne dolgozni, tekintse meg az [IP-címek hozzáadása, módosítása vagy eltávolítása](virtual-network-network-interface-addresses.md)című témakört.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A következő lépéseket a helyi számítógépről vagy a Azure Cloud Shell használatával végezheti el. A helyi számítógép használatához győződjön meg arról, hogy telepítve van a [Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). A Azure Cloud Shell használatához válassza a **kipróbálás** elemet az alábbi parancsok bármelyikének jobb felső sarkában. A Cloud Shell aláírja az Azure-t.

1. Ha a Cloud Shell használja, ugorjon a 2. lépésre. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be az Azure-ba `Connect-AzAccount` .
2. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal. Az alábbi példa egy erőforráscsoportot hoz létre az USA keleti régiója Azure-régióban:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Hozzon létre egy alhálózati konfigurációt és egy virtuális hálózatot a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) és a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) paranccsal:

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Hozzon létre egy hálózati adaptert a virtuális hálózaton, és rendeljen hozzá egy magánhálózati IP-címet az alhálózatból a hálózati adapterhez a [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) és a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) paranccsal:

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Hozzon létre egy új VM-konfigurációt a [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig), majd hozza létre a virtuális gépet a [New-AzVM](/powershell/module/az.Compute/New-azVM). Ha a rendszer kéri, adjon meg egy felhasználónevet és egy jelszót a virtuális gép bejelentkezési hitelesítő adatainak használata esetén:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Bár a magánhálózati IP-címek beállításait az operációs rendszerhez adhatja, ezt a műveletet csak akkor javasoljuk, ha az olvasás után nem [ad hozzá magánhálózati IP-címet az operációs rendszerhez](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> A virtuális gép internetről való eléréséhez nyilvános IP-címet kell hozzárendelni a virtuális géphez. A dinamikus magánhálózati IP-címek hozzárendelését is módosíthatja statikus hozzárendelésre. További információ: [IP-címek hozzáadása vagy módosítása](virtual-network-network-interface-addresses.md). Emellett azt javasoljuk, hogy korlátozza a virtuális gép hálózati forgalmát úgy, hogy hálózati biztonsági csoportot társít a hálózati adapterhez, az alhálózatot, amelyet a hálózati adaptert vagy mindkettőt létrehozott. Részletekért lásd: [hálózati biztonsági csoportok kezelése](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

- További információk a [magánhálózati IP-címekről](./private-ip-addresses.md) és a [statikus magánhálózati IP-](virtual-network-network-interface-addresses.md#add-ip-addresses) címek egy Azure-beli virtuális géphez való hozzárendeléséről.
- További információ a [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a [Windows rendszerű](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek létrehozásáról.
