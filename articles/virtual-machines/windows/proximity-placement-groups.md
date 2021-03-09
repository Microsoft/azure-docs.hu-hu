---
title: Közelségi elhelyezési csoport létrehozása Azure PowerShell használatával
description: További információ a közelségi csoportok létrehozásáról és használatáról Azure PowerShell használatával.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 26921b3d102032cb36f47c3be7a79c2b596a1d0c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503674"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-powershell"></a>Virtuális gépek üzembe helyezése a közvetlen elhelyezési csoportokba Azure PowerShell használatával


Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legkevesebb késést kell megvalósítania, egy [közelségi elhelyezési csoporton](../co-location.md#proximity-placement-groups)belül kell telepítenie.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.


## <a name="create-a-proximity-placement-group"></a>Közelségi elhelyezési csoport létrehozása
Hozzon létre egy közelségi elhelyezési csoportot a [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) parancsmag használatával. 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Proximity elhelyezési csoportok listázása

A [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) parancsmag használatával listázhatja az összes közelségi elhelyezési csoportot.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a közelségi elhelyezési csoportban a használatával `-ProximityPlacementGroup $ppg.Id` , hogy a virtuális gép létrehozásához használja a [AzVM](/powershell/module/az.compute/new-azvm) .

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

A virtuális gépet a [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)használatával tekintheti meg az Elhelyezés csoportban.

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Meglévő virtuális gép áthelyezése egy Proximity elhelyezési csoportba

Meglévő virtuális gépet is hozzáadhat egy közelségi elhelyezési csoporthoz. Először stop\deallocate kell a virtuális gépet, majd frissítenie kell a virtuális gépet, majd újra kell indítania.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Meglévő virtuális gép áthelyezése egy közelségi elhelyezési csoportból

Ahhoz, hogy egy virtuális gépet el lehessen távolítani egy közelségi elhelyezési csoportból, először stop\deallocate kell a virtuális gépet, majd frissítenie kell a virtuális gépet, majd újra kell indítania.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A közelségi elhelyezési csoportban is létrehozhat egy rendelkezésre állási csoportot. Használja ugyanazt a `-ProximityPlacementGroup` paramétert a [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) parancsmaggal, és hozzon létre egy rendelkezésre állási csoportot, és a rendelkezésre állási csoportban létrehozott összes virtuális gép ugyanazon a közelségi elhelyezési csoportban is létre lesz hozva.

Ha egy meglévő rendelkezésre állási csoportot szeretne hozzáadni vagy eltávolítani egy közelségi elhelyezési csoporthoz, először le kell állítania az összes virtuális gépet a rendelkezésre állási csoportban. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Meglévő rendelkezésre állási csoport áthelyezése egy Proximity elhelyezési csoportba

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Meglévő rendelkezésre állási csoport áthelyezése egy közelségi elhelyezési csoportból

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Méretezési csoportok

Létrehozhat egy méretezési csoportot is a közelségi elhelyezési csoportban. A `-ProximityPlacementGroup` [New-AzVmss](/powershell/module/az.compute/new-azvmss) ugyanezzel a paraméterrel hozzon létre egy méretezési csoportot, és az összes példány ugyanabban a közelségi elhelyezési csoportban lesz létrehozva.


Meglévő méretezési csoport hozzáadásához vagy eltávolításához először le kell állítania a méretezési csoportot. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Meglévő méretezési csoport áthelyezése egy közelségi elhelyezési csoportba

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Meglévő méretezési csoport áthelyezése egy közelségi elhelyezési csoportból

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Következő lépések

Az [Azure CLI](../linux/proximity-placement-groups.md) -vel közelségi elhelyezési csoportokat is létrehozhat.