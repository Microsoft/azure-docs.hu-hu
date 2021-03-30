---
title: Azure VM-lemezek kizárása a replikációból Azure Site Recovery és Azure PowerShell
description: Ismerje meg, hogyan zárja ki az Azure-beli virtuális gépek lemezeit a Azure Site Recovery során Azure PowerShell használatával.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: a21460279420c46b11c43615ae5ecc7bfa81de4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135810"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Lemezek kizárása az Azure-beli virtuális gépek PowerShell-replikálásával

Ez a cikk bemutatja, hogyan zárhat ki lemezeket az Azure-beli virtuális gépek replikálása során. Előfordulhat, hogy kizárja a lemezeket a felhasznált replikációs sávszélesség vagy a lemezek által használt cél-erőforrások optimalizálásához. Ez a funkció jelenleg csak Azure PowerShellon keresztül érhető el.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

- Győződjön meg arról, hogy ismeri a vész [-helyreállítási architektúrát és összetevőket](azure-to-azure-architecture.md).
- Tekintse át az összes összetevő [támogatási követelményeit](azure-to-azure-support-matrix.md) .
- Győződjön meg arról, hogy az AzureRm PowerShell "az" modulja van. A PowerShell telepítéséhez vagy frissítéséhez tekintse meg [a Azure PowerShell modul telepítését](/powershell/azure/install-az-ps)ismertető témakört.
- Győződjön meg arról, hogy legalább egyszer létrehozta a Recovery Services-tárolót és a védett virtuális gépeket. Ha még nem tette meg ezeket a dolgokat, kövesse az [Azure-beli virtuális gépek vész-helyreállításának beállítása Azure PowerShell használatával](azure-to-azure-powershell.md)című eljárást.
- Ha további információt szeretne arról, hogyan adhat hozzá lemezeket egy replikációra alkalmas Azure-beli virtuális géphez, [tekintse át ezt a cikket](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Miért kell a lemezeket kizárni a replikációból
Előfordulhat, hogy ki kell zárnia a lemezeket a replikációból, mert:

- A virtuális gép elérte [Azure site Recovery korlátot az adatváltozások arányának replikálásához](./azure-to-azure-support-matrix.md).

- A kizárt lemezen lévő adatforgalom nem fontos, vagy nem kell replikálni.

- A tárterületet és a hálózati erőforrásokat szeretné menteni az adatreplikálás mellőzésével.

## <a name="how-to-exclude-disks-from-replication"></a>Lemezek kizárása a replikációból

A példánkban egy olyan virtuális gépet replikálunk, amely egy operációs rendszert és három adatlemezt tartalmaz az USA keleti régiójában, az USA 2. nyugati régiójában. A virtuális gép neve *AzureDemoVM*. Kizárjuk az 1. lemezt, és megtartjuk a 2. és a 3. lemezt.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>A replikálni kívánt virtuális gépek részleteinek beolvasása

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

A virtuális gép lemezei részletes adatainak beolvasása. Ezeket az információkat később a virtuális gép replikálásának megkezdése után fogjuk használni.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Azure-beli virtuális gép replikálása

A következő példában feltételezzük, hogy már rendelkezik gyorsítótár-tárolási fiókkal, replikációs házirenddel és leképezésekkel. Ha nem rendelkezik ezekkel a dolgokkal, kövesse az [Azure-beli virtuális gépek vész-helyreállításának beállítása Azure PowerShell használatával](azure-to-azure-powershell.md)című eljárást.

Azure-beli virtuális gép replikálása *felügyelt lemezekkel*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Ha a Start-Replication művelet sikeres, a rendszer replikálja a virtuálisgép-fájlokat a helyreállítási régióba.

Nyissa meg a Azure Portal, és tekintse meg a "replikált elemek" alatt található replikált virtuális gépeket.

A replikálási folyamat a helyreállítási régióban található virtuális gép replikáló lemezei másolatának beírásával kezdődik. Ezt a szakaszt a kezdeti replikálási fázisnak nevezzük.

A kezdeti replikálás befejeződése után a replikálás a különbözeti szinkronizálási fázisra lép. Ezen a ponton a virtuális gép védett. A védett virtuális gép kiválasztásával ellenőrizze, hogy a lemezek ki vannak-e zárva.

## <a name="next-steps"></a>Következő lépések

További információ [a feladatátvételi teszt futtatásáról](site-recovery-test-failover-to-azure.md).
