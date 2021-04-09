---
title: Nem csatolt, Azure által felügyelt és nem felügyelt lemezek keresése és törlése
description: A nem csatolt Azure felügyelt és nem felügyelt (VHD-/blob-) lemezek megkeresése és törlése Azure PowerShell használatával.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 66a54ea74fcc6d8d354f5adbffe214c34b4c20d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554378"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Nem csatolt, Azure által felügyelt és nem felügyelt lemezek keresése és törlése

Amikor töröl egy virtuális gépet (VM) az Azure-ban, alapértelmezés szerint a virtuális géphez csatolt összes lemez nem törlődik. Ez a funkció segít megakadályozni az adatvesztést a virtuális gépek véletlen törlése miatt. A virtuális gép törlése után továbbra is fizetnie kell a nem csatlakoztatott lemezekre. Ez a cikk bemutatja, hogyan keresheti meg és törölheti a nem csatolt lemezeket, és csökkentheti a szükségtelen költségeket.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Felügyelt lemezek: nem csatolt lemezek keresése és törlése

A következő parancsfájl a nem csatlakoztatott [felügyelt lemezeket](../managed-disks-overview.md) keresi a **többé** tulajdonság értékének vizsgálatával. Ha egy felügyelt lemez egy virtuális géphez van csatlakoztatva, a **többé** tulajdonság tartalmazza a virtuális gép erőforrás-azonosítóját. Ha egy felügyelt lemez nincs csatlakoztatva, a **többé** tulajdonság null értékű. A parancsfájl megvizsgálja az Azure-előfizetések összes felügyelt lemezét. Ha a parancsfájl egy olyan felügyelt lemezt keres, amelynek **többé** tulajdonsága NULL értékűre van állítva, akkor a parancsfájl megállapítja, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a szkriptet úgy, hogy a **deleteUnattachedDisks** változót 0-ra állítja. Ezzel a művelettel megkeresheti és megtekintheti az összes nem csatlakoztatott felügyelt lemezt.
>
>Miután átvizsgálta az összes nem csatlakoztatott lemezt, futtassa újra a szkriptet, és állítsa az **deleteUnattachedDisks** változót 1-re. Ez a művelet lehetővé teszi az összes nem csatlakoztatott felügyelt lemez törlését.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nem felügyelt lemezek: nem csatolt lemezek keresése és törlése

A nem felügyelt lemezek az [Azure Storage-fiókokban](../../storage/common/storage-account-overview.md) [blobként](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) tárolt VHD-fájlok. A következő parancsfájl a nem csatolt nem felügyelt lemezeket (blobokat) keresi a **LeaseStatus** tulajdonság értékének vizsgálatával. Ha egy nem felügyelt lemez egy virtuális géphez van csatlakoztatva, a **LeaseStatus** tulajdonság **zárolt** értékre van állítva. Ha egy nem felügyelt lemez nincs csatlakoztatva, a **LeaseStatus** tulajdonság **zárolása zárolva** értékre van állítva. A parancsfájl megvizsgálja az Azure-előfizetések összes Azure Storage-fiókjában lévő összes nem felügyelt lemezt. Ha a parancsfájl egy nem felügyelt lemezt keres, amelynek **LeaseStatus** tulajdonsága **zárolva** értékre van állítva, akkor a parancsfájl megállapítja, hogy a lemez nincs csatlakoztatva.

>[!IMPORTANT]
>Először futtassa a szkriptet úgy, hogy a **deleteUnattachedVHDs** változót állítja be `$false` . Ezzel a művelettel megkeresheti és megtekintheti a nem csatolt nem felügyelt virtuális merevlemezeket.
>
>Miután átvizsgálta az összes nem csatlakoztatott lemezt, futtassa újra a szkriptet, és állítsa a **deleteUnattachedVHDs** változót a következőre: `$true` . Ezzel a művelettel törölheti az összes nem csatolt nem felügyelt virtuális merevlemezt.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=$true if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=$false if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=$false
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

További információ: Storage- [fiók törlése](../../storage/common/storage-account-create.md#delete-a-storage-account) és [árva lemezek azonosítása a PowerShell használatával](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)