---
title: Adatlemez csatlakoztatása egy Windows rendszerű virtuális géphez az Azure-ban a PowerShell használatával
description: Új vagy meglévő adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell és a Resource Manager-alapú üzemi modell használatával.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d5c638a63e4e8dc1a55c07f4bdf713fd36ca6b3d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550875"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell-lel

Ez a cikk bemutatja, hogyan csatlakoztathatja az új és a meglévő lemezeket egy Windows rendszerű virtuális géphez a PowerShell használatával. 

Először tekintse át a következő tippeket:

* A virtuális gép mérete határozza meg, hogy hány adatlemezt tud csatlakoztatni. További információ: [virtuális gépek méretei](../sizes.md).
* A prémium SSD-k használatához [prémium szintű Storage-kompatibilis virtuálisgép-típusra](../sizes-memory.md)van szükség, például a DS-sorozat vagy a GS sorozatú virtuális gép számára.

Ez a cikk a PowerShellt használja a [Azure Cloud Shellon](../../cloud-shell/overview.md)belül, amely folyamatosan frissül a legújabb verzióra. A Cloud Shell megnyitásához válassza a **kipróbálás** lehetőséget a kód bármely blokkjának elejéről.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Üres adatlemez hozzáadása egy virtuális géphez

Ebből a példából megtudhatja, hogyan adhat hozzá üres adatlemezt egy meglévő virtuális géphez.

### <a name="using-managed-disks"></a>Felügyelt lemezek használata

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Felügyelt lemezek használata rendelkezésre állási zónában

Ha lemezt szeretne létrehozni egy rendelkezésre állási zónában, használja a [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) `-Zone` paramétert. Az alábbi példa egy lemezt hoz létre az *1*. zónában.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>A lemez inicializálása

Ha üres lemezt ad hozzá, inicializálnia kell azt. A lemez inicializálásához bejelentkezhet egy virtuális gépre, és a Lemezkezelés szolgáltatással is használható. Ha a [rendszerfelügyeleti](/windows/desktop/winrm/portal) webszolgáltatások és a virtuális gép tanúsítványát engedélyezte a létrehozásakor, a távoli PowerShell használatával inicializálhatja a lemezt. Egyéni parancsfájl-bővítményt is használhat:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

A parancsfájl tartalmazhat kódot a lemezek inicializálásához, például:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Meglévő adatlemez csatolása egy virtuális géphez

Egy meglévő felügyelt lemezt adatlemezként is csatolhat egy virtuális géphez.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Következő lépések

A felügyelt lemezeket sablonok használatával is üzembe helyezheti. További információ: [Managed Disks használata Azure Resource Manager sablonokban](../using-managed-disks-template-deployments.md) vagy a gyors üzembe helyezési [sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) több adatlemez telepítéséhez.