---
title: Felügyelt lemezek tárterületének konvertálása a különböző típusú lemezek között Azure PowerShell használatával
description: Az Azure Managed Disks konvertálása a különböző lemezek típusai között Azure PowerShell használatával.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 1d1c191c746d6853f922302d74c6eefcba547f80
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519745"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Felügyelt lemez tárolási típusának frissítése

Az Azure Managed Disks négyféle típusú lemezből áll: az Azure Ultra Disks, a Premium SSD, a standard SSD és a standard HDD. A teljesítmény igényei alapján válthat a prémium szintű SSD, a standard SSD és a standard HDD között. Még nem tud váltani egy ultra-lemezre, és egy újat kell telepítenie.

A nem felügyelt lemezek esetében ez a funkció nem támogatott. A nem [felügyelt lemezeket azonban könnyedén átalakíthatja egy felügyelt lemezre](convert-unmanaged-to-managed-disks.md) , hogy képes legyen váltani a lemezek típusai között.



## <a name="before-you-begin"></a>Előkészületek

* Mivel a konverzióhoz szükség van a virtuális gép (VM) újraindítására, a lemezes tárolás áttelepítését egy már létező karbantartási időszakban kell ütemeznie.
* Ha a lemez nem felügyelt, először [alakítsa át felügyelt lemezre](convert-unmanaged-to-managed-disks.md) , hogy át lehessen váltani a tárolási lehetőségek között.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Egy virtuális gép összes felügyelt lemezének átváltása egyik fiókról a másikra

Ebből a példából megtudhatja, hogyan alakíthatja át a virtuális gépek lemezeit a Premium Storage szolgáltatásba. A példában szereplő $storageType változó módosításával azonban a virtuális gép lemezeit szabványos SSD-re vagy standard HDD-re konvertálhatja. A prémium szintű felügyelt lemezek használatához a virtuális gépnek olyan virtuálisgép- [méretet](../sizes.md) kell használnia, amely támogatja a Premium Storage-ot. Ez a példa a Premium Storage-t támogató méretre is vált:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSDD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Egyéni felügyelt lemezek váltása a standard és a prémium között

A fejlesztési és tesztelési feladatok esetében érdemes lehet a standard és a prémium szintű lemezek kombinációját használni a költségek csökkentése érdekében. Dönthet úgy is, hogy csak a jobb teljesítményt igénylő lemezeket frissíti. Ebből a példából megtudhatja, hogyan alakíthat át egyetlen VM-lemezt a standard és a Premium Storage között. A példában szereplő $storageType változó módosításával azonban a virtuális gép lemezeit szabványos SSD-re vagy standard HDD-re konvertálhatja. A prémium szintű felügyelt lemezek használatához a virtuális gépnek olyan virtuálisgép- [méretet](../sizes.md) kell használnia, amely támogatja a Premium Storage-ot. Ez a példa azt is bemutatja, hogyan válthat a Premium Storage-t támogató méretre:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Felügyelt lemezek átváltása egyik lemezről a másikra

Kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a virtuális gépet a **virtuális gépek** listájából.
3. Ha a virtuális gép nem áll le, válassza a **Leállítás** elemet a virtuális gép **Áttekintés** paneljének tetején, és várjon, amíg a virtuális gép leáll.
4. A virtuális gép ablaktábláján válassza a menü **lemezek** elemét.
5. Válassza ki az átalakítani kívánt lemezt.
6. Válassza a **konfiguráció** lehetőséget a menüből.
7. Módosítsa a **fiók típusát** az eredeti lemez típusától a kívánt lemez típusra.
8. Válassza a **Mentés** lehetőséget, majd a lemez ablaktábla bezárásához.

A lemez típusának átalakítása azonnali. Az átalakítás után elindíthatja a virtuális gépet.

## <a name="next-steps"></a>Következő lépések

Készítse el a virtuális gép írásvédett másolatát egy [Pillanatkép](snapshot-copy-managed-disk.md)használatával.