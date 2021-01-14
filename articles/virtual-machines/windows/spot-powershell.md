---
title: Azure spot virtuális gépek üzembe helyezése a PowerShell használatával
description: Megtudhatja, hogyan helyezhet üzembe helyszíni virtuális gépeket a költségek csökkentése érdekében a Azure PowerShell használatával.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0ca3c99aed8160161c125a89da3cb176c6e745f6
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202062"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>Helyszíni virtuális gépek üzembe helyezése Azure PowerShell használatával


A [helyszíni virtuális gépek](../spot-vms.md) használata lehetővé teszi, hogy a kihasználatlan kapacitást jelentős költségmegtakarítással használja. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni virtuális gépeket, amikor az Azure-nak szüksége van a kapacitásra. Ezért a helyszíni virtuális gépek kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A helyszíni virtuális gépek díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). A maximális ár beállításával kapcsolatos további információkért lásd: [virtuális gépek – díjszabás](../spot-vms.md#pricing).

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. A helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, akár 5 tizedesjegyet is igénybe vehet. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta.


## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozzon létre egy spotVM a [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) használatával a konfiguráció létrehozásához. Adja `-Priority Spot` meg a következőt `-MaxPrice` :
- `-1` így a virtuális gép árát a rendszer nem zárja ki.
- a dollár mennyisége, legfeljebb 5 számjegy. Például `-MaxPrice .98765` azt jelenti, hogy a virtuális gép fel lesz osztva, ha egy spotVM ára körülbelül 98765 USD-t vesz igénybe.


Ez a példa olyan spotVM hoz létre, amely nem lesz kiosztva a díjszabás alapján (csak akkor, ha az Azure-ban a kapacitás vissza van rendelve). A kizárási szabályzat úgy van beállítva, hogy felszabadítsa a virtuális gépet, hogy később újra lehessen indítani. Ha törölni szeretné a virtuális gépet és a mögöttes lemezt a virtuális gép kizárásakor, állítsa a `-EvictionPolicy` `Delete` következőre: `New-AzVMConfig` .


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

A virtuális gép létrehozása után a lekérdezéssel megtekintheti az erőforráscsoport összes virtuális gépe maximális árát.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Kizárás szimulálása

A Direktszínű virtuális gépek [kizárását szimulálhatja](/rest/api/compute/virtualmachines/simulateeviction) , így tesztelheti, hogy az alkalmazás milyen jól fogja kizárni a hirtelen kizárást. 

Cserélje le a következőt az adataira: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Következő lépések

Helyszíni virtuális gépet az [Azure CLI](../linux/spot-cli.md)-vel, a [portálral](../spot-portal.md) vagy a [sablonnal](../linux/spot-template.md)is létrehozhat.

Az aktuális díjszabási információkat az [Azure kiskereskedelmi díjszabás API](/rest/api/cost-management/retail-prices/azure-retail-prices) használatával kérdezheti le a helyszíni díjszabással kapcsolatban. A `meterName` és `skuName` mindkettő tartalmazni fogja `Spot` .

Ha hibát tapasztal, tekintse meg a [hibakódokat](../error-codes-spot.md).