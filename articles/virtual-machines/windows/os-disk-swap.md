---
title: Azure-beli virtuális gép rendszerlemezének cseréje PowerShell-lel
description: Az Azure-beli virtuális gépek által használt operációsrendszer-lemez módosítása a PowerShell használatával.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: c96fa4c453911c4ca4b8cf6d8f74647b4532109f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84711544"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Azure-beli virtuális gép által használt operációsrendszer-lemez módosítása a PowerShell használatával

Ha rendelkezik meglévő virtuális géppel, de a lemezét egy biztonsági mentési lemez vagy egy másik operációsrendszer-lemez esetében szeretné cserélni, a Azure PowerShell használatával cserélheti le az operációsrendszer-lemezeket. Nem kell törölnie és újból létrehoznia a virtuális gépet. Akár felügyelt lemezt is használhat egy másik erőforráscsoporthoz, ha még nincs használatban.

 

A virtuális gépnek stopped\deallocated kell lennie, a felügyelt lemez erőforrás-AZONOSÍTÓját lecserélheti egy másik felügyelt lemez erőforrás-azonosítójával.

Győződjön meg arról, hogy a virtuális gép mérete és a tároló típusa kompatibilis-e a csatolni kívánt lemezzel. Ha például a használni kívánt lemez Premium Storage, akkor a virtuális gépnek képesnek kell lennie Premium Storage (például egy DS-sorozat méretének). Mindkét lemeznek azonos méretűnek kell lennie.
És ügyeljen arra, hogy ne keverje egy titkosított operációsrendszer-lemezzel nem titkosított virtuális gépet, ez nem támogatott. Ha a virtuális gép nem használja Azure Disk Encryption, akkor a cserébe becserélt operációsrendszer-lemez nem használható Azure Disk Encryption.

Egy erőforráscsoport lemezei listájának lekérése a [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) használatával

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Ha rendelkezik a használni kívánt lemez nevével, állítsa be, hogy a virtuális gép operációsrendszer-lemeze legyen. Ez a példa a *myVM* nevű virtuális gépet stop\deallocates, és az új operációsrendszer-lemezként rendeli hozzá a *newDisk* nevű lemezt. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Következő lépések**

Lemez másolatának létrehozásához tekintse meg a [lemez pillanatképe](snapshot-copy-managed-disk.md)című témakört.
