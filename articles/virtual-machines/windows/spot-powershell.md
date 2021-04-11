---
title: Az Azure spot Virtual Machines üzembe helyezése a PowerShell használatával
description: Megtudhatja, hogyan használhatja a Azure PowerShellt az Azure spot-Virtual Machines üzembe helyezéséhez a költségek megtakarítása érdekében.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802743"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Az Azure spot Virtual Machines üzembe helyezése Azure PowerShell használatával


Az [Azure Spot Virtual Machines](../spot-vms.md) használatával jelentős költségmegtakarítás mellett kihasználhatja a fel nem használt kapacitás előnyeit. Az Azure-infrastruktúra minden olyan időpontban kizárja az Azure spot Virtual Machinest, amikor az Azure-nak szüksége van a kapacitásra. Ezért az Azure spot Virtual Machines nagyszerűek olyan munkaterhelésekhez, amelyek képesek kezelni a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

Az Azure spot Virtual Machines díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). A maximális ár beállításával kapcsolatos további információkért lásd: [Azure Spot Virtual Machines – díjszabás](../spot-vms.md#pricing).

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. Az Azure-beli helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, legfeljebb 5 tizedesjegyet használva. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta.


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

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

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

A REST, a PowerShell vagy a CLI használatával szimulálhatja az Azure-beli direktszínű virtuális gépek kizárását annak teszteléséhez, hogy az alkalmazás milyen jól reagáljon a hirtelen kizárásra.

A legtöbb esetben érdemes a REST API [Virtual Machines szimulálni a kizárást](/rest/api/compute/virtualmachines/simulateeviction) , hogy segítsen az alkalmazások automatizált tesztelésében. A REST esetében `Response Code: 204` az azt jelenti, hogy a szimulált kizárás sikeres volt. A szimulált kizárásokat kombinálhatja az [ütemezett esemény szolgáltatással](scheduled-events.md), így automatizálhatja, hogy az alkalmazás hogyan reagáljon a virtuális gép kizárásakor.

Az ütemezett események működés közbeni megtekintéséhez tekintse meg az [Azure Friday – azure Scheduled Events használatával készítse elő a virtuális gépek karbantartását](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Gyorsteszt

Ha egy gyors teszttel bemutatjuk, hogyan fog működni egy szimulált kizárás, lássuk végig az ütemezett esemény szolgáltatás lekérdezését, hogy meglássuk, mi úgy néz ki, mint amikor szimulál egy kizárást a PowerShell használatával.

Az ütemezett esemény szolgáltatás engedélyezve van a szolgáltatáshoz, amikor az első alkalommal kérelmet küld az eseményekre. 

Távolról a virtuális gépre, majd nyisson meg egy parancssort. 

A virtuális gépen a parancssorba írja be a következőt:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Ez az első válasz akár 2 percet is igénybe vehet. Mostantól szinte azonnal meg kell jelennie a kimenetnek.

Egy olyan számítógépről, amelyen telepítve van az az PowerShell-modul (például a helyi gép), szimuláljon egy kizárást a [set-AzVM](https://docs.microsoft.com/powershell/module/az.compute/set-azvm)használatával. Cserélje le az erőforráscsoport nevét és a virtuális gép nevét a saját nevére. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

Ha a kérés sikeresen létrejött, a válasz kimenete lesz `Status: Succeeded` .

Lépjen vissza a helyszíni virtuális géphez való távoli kapcsolódásra, és ismételje meg a Scheduled Events-végpont lekérdezését. Ismételje meg a következő parancsot, amíg olyan kimenetet kap, amely további információkat tartalmaz:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Ha az ütemezett esemény szolgáltatás lekéri a kizárási értesítést, a következőhöz hasonló választ kap:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Láthatja, hogy az `"EventType":"Preempt"` erőforrás a virtuális gép erőforrása `"Resources":["myspotvm"]` . 

Azt is láthatja, hogy a virtuális gép ki lesz-e zárva az érték ellenőrzésével `"NotBefore"` . A virtuális gépet nem lehet kizárni a-ben megadott idő előtt `NotBefore` , hogy az alkalmazása megfelelően kizárja az alkalmazást.


## <a name="next-steps"></a>Következő lépések

Azure-beli helyszíni virtuális gépet is létrehozhat az [Azure CLI](../linux/spot-cli.md), a [portál](../spot-portal.md) vagy egy [sablon](../linux/spot-template.md)használatával.

A jelenlegi díjszabási információkat az [Azure kiskereskedelmi díjszabás API](/rest/api/cost-management/retail-prices/azure-retail-prices) használatával kérdezheti le az Azure spot Virtual Machine díjszabásáról. A `meterName` és `skuName` mindkettő tartalmazni fogja `Spot` .

Ha hibát tapasztal, tekintse meg a [hibakódokat](../error-codes-spot.md).
