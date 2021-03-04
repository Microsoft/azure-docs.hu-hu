---
title: Az Azure dedikált gazdagépek üzembe helyezése a Azure PowerShell használatával
description: A virtuális gépeket a Azure PowerShell használatával dedikált gazdagépekre helyezheti üzembe.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/12/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: ed6319d5374db56cfe85e7ef9413480e523d9a34
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050885"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Virtuális gépek üzembe helyezése dedikált gazdagépeken a Azure PowerShell használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre egy dedikált Azure- [gazdagépet](../dedicated-hosts.md) a virtuális gépek (VM-EK) üzemeltetéséhez. 

Győződjön meg arról, hogy telepítette a Azure PowerShell 2.8.0-as vagy újabb verzióját, és bejelentkezett egy Azure-fiókba a-ben `Connect-AzAccount` . 

## <a name="limitations"></a>Korlátozások

- A dedikált gazdagépek számára elérhető méretek és hardver típusok régiónként eltérőek. További információért tekintse meg a gazdagép [díjszabását ismertető oldalt](https://aka.ms/ADHPricing) .

## <a name="create-a-host-group"></a>Gazda csoport létrehozása

A **gazda-csoport** egy olyan erőforrás, amely dedikált gazdagépek gyűjteményét jelöli. Egy adott régióban és egy rendelkezésre állási zónában hozhat létre egy gazdagépet, és hozzáadhat gazdagépeket. A magas rendelkezésre állás tervezése során további lehetőségek is rendelkezésre állnak. A dedikált gazdagépekhez a következő lehetőségek közül választhat: 
- Több rendelkezésre állási zónára kiterjedő span. Ebben az esetben minden használni kívánt zónában rendelkeznie kell egy gazdagép-csoporttal.
- Több tartalék tartomány között, amelyek fizikai állványokra vannak leképezve. 
 
Mindkét esetben meg kell adnia a gazdagép-csoport tartalék tartományának darabszámát. Ha nem szeretné a csoportban lévő tartalék tartományokat kivonni, használja az 1. számú tartalék tartományt. 

Dönthet úgy is, hogy a rendelkezésre állási zónákat és a tartalék tartományokat is használja. Ez a példa egy gazdagép csoportot hoz létre az 1. zónában, 2 tartalék tartománnyal. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


Adja hozzá a `-SupportAutomaticPlacement true` paramétert, hogy a virtuális gépek és a méretezési csoport példányai automatikusan a gazdagépekre kerüljenek a gazdagépen belül. További információ: manuális és [automatikus elhelyezés ](../dedicated-hosts.md#manual-vs-automatic-placement).


## <a name="create-a-host"></a>Gazdagép létrehozása

Most hozzon létre egy dedikált gazdagépet a gazdagép csoportban. A gazdagép neve mellett meg kell adnia a gazdagéphez tartozó SKU-t is. A gazdagép SKU rögzíti a támogatott virtuálisgép-sorozatot, valamint a dedikált gazdagép hardveres generációját.

A gazdagép SKU-ról és a díjszabásról további információt az [Azure dedikált gazdagép díjszabása](https://aka.ms/ADHPricing)című témakörben talál.

Ha a gazdagéphez a tartalék tartományokat állítja be, a rendszer megkéri, hogy adja meg a gazdagép tartalék tartományát. Ebben a példában a gazdagép tartalék tartományát 1-re állítja.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a dedikált gazdagépen. 

Ha a rendelkezésre állási zónát a gazda csoport létrehozásakor adta meg, akkor ugyanazt a zónát kell használnia a virtuális gép létrehozásakor. Ebben a példában, mert a gazda csoport az 1. zónában van, létre kell hoznia a virtuális gépet az 1. zónában.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Ha olyan gazdagépen hoz létre virtuális gépet, amely nem rendelkezik elegendő erőforrással, a virtuális gép hibás állapotban lesz létrehozva. 

## <a name="check-the-status-of-the-host"></a>A gazdagép állapotának keresése

Megtekintheti a gazdagép állapotának állapotát, valamint azt, hogy hány virtuális gépet telepíthet a gazdagépre a [GetAzHost](/powershell/module/az.compute/get-azhost) használatával a `-InstanceView` paraméterrel.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

A kimenet a következőhöz hasonlóan fog kinézni:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása 

Méretezési csoport telepítésekor meg kell adnia a gazdagépet.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

Ha manuálisan szeretné kiválasztani a méretezési csoport üzembe helyezéséhez szükséges gazdagépet, adja hozzá a `--host` nevet és a gazdagép nevét.



## <a name="add-an-existing-vm"></a>Meglévő virtuális gép hozzáadása 

Hozzáadhat egy meglévő virtuális gépet egy dedikált gazdagéphez, de a virtuális gépnek először Stop\Deallocated. kell lennie. Mielőtt áthelyezi a virtuális gépet egy dedikált gazdagépre, győződjön meg arról, hogy a virtuális gép konfigurációja támogatott:

- A virtuális gép méretének azonos méretűnek kell lennie, mint a dedikált gazdagépnek. Ha például a dedikált gazdagép DSv3, akkor a virtuális gép mérete Standard_D4s_v3, de nem lehet Standard_A4_v2. 
- A virtuális gépnek ugyanabban a régióban kell lennie, ahol a dedikált gazdagép található.
- A virtuális gép nem lehet a közelségi elhelyezési csoport része. A dedikált gazdagépre való áthelyezés előtt távolítsa el a virtuális gépet a közelségi csoportból. További információ: [virtuális gép áthelyezése a közelségi csoportból](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- A virtuális gép nem lehet rendelkezésre állási készletben.
- Ha a virtuális gép egy rendelkezésre állási zónában van, akkor a gazdagép-csoporttal megegyező rendelkezésre állási zónának kell lennie. A virtuális gép rendelkezésre állási zónájának beállításai és a gazdagép csoportjának egyeznie kell.

Cserélje le a változók értékeit a saját adataira.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A dedikált gazdagépekre akkor is díjat számítunk fel, ha nincsenek virtuális gépek üzembe helyezése. Minden olyan gazdagépet törölni kell, amelyet jelenleg nem használ a költségek megtakarítására.  

Csak akkor törölhet egy gazdagépet, ha már nem használja a virtuális gépeket. Törölje a virtuális gépeket a [Remove-AzVM](/powershell/module/az.compute/remove-azvm)használatával.

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

A virtuális gépek törlése után törölheti a gazdagépet a [Remove-AzHost](/powershell/module/az.compute/remove-azhost)használatával.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Miután törölte az összes gazdagépet, törölheti a gazdagépet a [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)használatával. 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

A [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)használatával egyetlen parancsban is törölheti a teljes erőforráscsoportot. Ezzel törli a csoportban létrehozott összes erőforrást, beleértve az összes virtuális gépet, gazdagépet és gazdagépet is.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Következő lépések

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.

- A [Azure Portal](../dedicated-hosts-portal.md)használatával dedikált gazdagépeket is üzembe helyezhet.
