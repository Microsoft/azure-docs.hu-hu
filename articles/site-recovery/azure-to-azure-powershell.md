---
title: Vész-helyreállítás Azure-beli virtuális gépekhez Azure PowerShell és Azure Site Recovery használatával
description: Ismerje meg, hogyan állíthatja be a vész-helyreállítást az Azure-beli virtuális gépeken a Azure Site Recovery használatával Azure PowerShell segítségével.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 1570bd9dfa62caa749d5a3983b93c2555be058ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93348729"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Azure-beli virtuális gépek vészhelyreállításának beállítása az Azure PowerShell használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be és tesztelheti az Azure-beli virtuális gépek vész-helyreállítását Azure PowerShell használatával.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - Recovery Services-tároló létrehozása.
> - Állítsa be a tár környezetét a PowerShell-munkamenethez.
> - Készítse elő a tárolót az Azure-beli virtuális gépek replikálásának megkezdéséhez.
> - Hozzon létre hálózati leképezéseket.
> - Hozzon létre Storage-fiókokat a virtuális gépek a szolgáltatásba való replikálásához.
> - Azure-beli virtuális gépeket egy helyreállítási régióba replikálhat vész-helyreállítás céljából.
> - Végezzen feladatátvételi tesztet, érvényesítse és törölje a feladatátvételi tesztet.
> - Feladatátvétel a helyreállítási régióba.

> [!NOTE]
> Előfordulhat, hogy a portálon keresztül elérhető összes funkció nem érhető el Azure PowerShellon keresztül. A Azure PowerShell által jelenleg nem támogatott forgatókönyv-funkciók némelyike:
> - Annak a lehetősége, hogy a virtuális gép összes lemezét replikálni kell, anélkül, hogy explicit módon meg kellene adni a virtuális gép egyes lemezeit.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át az összes összetevő [támogatási követelményeit](azure-to-azure-support-matrix.md) .
- A Azure PowerShell `Az` modul. Ha Azure PowerShellt kell telepítenie vagy frissítenie, a [Azure PowerShell telepítéséhez és konfigurálásához](/powershell/azure/install-az-ps)kövesse az alábbi útmutatót.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be Microsoft Azure-előfizetésbe

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` parancsmaggal.

```azurepowershell
Connect-AzAccount
```

Válassza ki Azure-előfizetését. Használja a `Get-AzSubscription` parancsmagot azon Azure-előfizetések listájának lekéréséhez, amelyekhez hozzáfér. Válassza ki a parancsmag használatával dolgozni kívánt Azure-előfizetést `Set-AzContext` .

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>A replikálni kívánt virtuális gép adatainak beolvasása

Ebben a cikkben az USA keleti régiójában található virtuális gépeket az USA 2. nyugati régiójában replikáljuk és helyreállítjuk. A replikált virtuális gép operációsrendszer-lemezzel és egyetlen adatlemezzel rendelkezik. A példában használt virtuális gép neve `AzureDemoVM` .

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
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

A virtuális gép lemezei adatainak beolvasása. A lemez adatai később lesznek használva a virtuális gép replikálásának indításakor.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Hozzon létre egy erőforráscsoportot, amelyben létre kívánja hozni a Recovery Services-tárolót.

> [!IMPORTANT]
> * A Recovery Services-tárolónak és a védett virtuális gépeknek különböző Azure-helyen kell lenniük.
> * A Recovery Services-tároló erőforráscsoporthoz és a védett virtuális gépekhez különböző Azure-helyen kell tartoznia.
> * A Recovery Services-tároló és a hozzá tartozó erőforráscsoport lehet ugyanabban az Azure-helyen.

A cikkben szereplő példában a védett virtuális gép az USA keleti régiójában található. A vész-helyreállításra kijelölt helyreállítási régió az USA 2. nyugati régiója. A Recovery Services-tároló és a tároló erőforráscsoport mind a helyreállítási régióban, mind az USA 2. nyugati régiójában található.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Hozzon létre egy Recovery Services-tárolót. Ebben a példában egy nevű Recovery Services-tároló `a2aDemoRecoveryVault` jön létre az USA 2. nyugati régiójában.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>A tár környezetének beállítása

Állítsa be a tár környezetét a PowerShell-munkamenetben való használatra. A tár környezetének beállítása után a PowerShell-munkamenetben Azure Site Recovery műveleteket a kiválasztott tároló környezetében hajtja végre a rendszer.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Azure – Azure áttelepítéshez a tár környezetét beállíthatja az újonnan létrehozott tárolóra:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>A tároló előkészítése az Azure-beli virtuális gépek replikálásának megkezdéséhez

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Az elsődleges (forrás) régiót jelölő Site Recovery Fabric-objektum létrehozása

A tároló háló objektuma egy Azure-régiót jelöl. Az elsődleges háló objektum úgy jön létre, hogy az a tárolóhoz tartozó virtuális gépekhez tartozó Azure-régiót jelöli. A cikkben szereplő példában a védett virtuális gép az USA keleti régiójában található.

- Régiónként csak egy Fabric-objektum hozható létre.
- Ha korábban engedélyezte Site Recovery replikációt a Azure Portal egy virtuális géphez, a Site Recovery automatikusan létrehoz egy háló objektumot. Ha egy adott régióban létezik egy Fabric-objektum, nem hozhat létre újat.

Mielőtt elkezdené, Ismerje meg, hogy Site Recovery műveletek végrehajtása aszinkron módon történik. Amikor műveletet kezdeményez, egy Azure Site Recovery feladatot küld, és a rendszer visszaadja a feladatok követésére szolgáló objektumot. A feladatok követése objektum használatával lekérheti a feladatokhoz tartozó legújabb állapotot `Get-AzRecoveryServicesAsrJob` , és figyelheti a művelet állapotát.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Ha több Azure-régióból származó virtuális gép védelmét ugyanahhoz a tárolóhoz kívánja védeni, hozzon létre egy Fabric-objektumot mindegyik forrás Azure-régióban.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Site Recovery Fabric-objektum létrehozása a helyreállítási régió képviseletéhez

A helyreállítási háló objektum a helyreállítási Azure helyét jelöli. Feladatátvétel esetén a rendszer replikálja a virtuális gépeket, és helyreállítja a helyreállítási háló által képviselt helyreállítási régióban. Az ebben a példában használt helyreállítási Azure-régió az USA 2. nyugati régiója.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Site Recovery védelmi tároló létrehozása az elsődleges hálóban

A védelmi tároló egy olyan tároló, amely egy hálón belüli replikált elemek csoportosítására szolgál.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Site Recovery védelmi tároló létrehozása a helyreállítási hálóban

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

#### <a name="fabric-and-container-creation-when-enabling-zone-to-zone-replication"></a>A háló és a tároló létrehozása a zóna replikálásának engedélyezésekor

A zónák replikálásának engedélyezésekor a rendszer csak egy hálót hoz létre. Két tárolónak is lennie kell. Feltételezve, hogy a régió Nyugat-Európa, az elsődleges és a védelmi tárolók beszerzéséhez használja az alábbi parancsokat:

```azurepowershell
$primaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $fabric -Name "asr-a2a-default-westeurope-container"
$recoveryPprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $fabric -Name "asr-a2a-default-westeurope-t-container"
```

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Védelmi tárolók leképezésének létrehozása az elsődleges és a helyreállítási védelmi tároló között

A védelmi tárolók leképezése az elsődleges védelmi tárolót egy helyreállítási védelmi tárolóval és egy replikációs házirenddel társítja. Hozzon létre egy leképezést minden olyan replikációs házirendhez, amelyet a virtuális gépek védelmi tároló pár közötti replikálásához fog használni.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

#### <a name="protection-container-mapping-creation-when-enabling-zone-to-zone-replication"></a>Védelmi tároló hozzárendelésének létrehozása a zóna replikálásának engedélyezésekor

Amikor engedélyezi a zóna replikálását, az alábbi paranccsal hozhatja létre a védelmi tárolók leképezését. Feltételezve, hogy a régió Nyugat-Európa, a parancs a-

```azurepowershell
$protContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimprotectionContainer -Name "westeurope-westeurope-24-hour-retention-policy-s"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Védelmi tároló leképezésének létrehozása a feladat-visszavételhez (feladatátvétel utáni visszirányú replikálás)

Feladatátvétel után, amikor készen áll arra, hogy a feladatátviteli virtuális gépet vissza lehessen állítani az eredeti Azure-régióba, feladat-visszavételt hajt végre. A feladat-visszavételhez a feladatátvételen átadott virtuális gép visszafelé replikálódik a feladatátvételi régióról az eredeti régióra. A visszirányú replikáláshoz az eredeti régió szerepkörei és a helyreállítási régió kapcsolója. Az eredeti régió most az új helyreállítási régió lesz, és eredetileg a helyreállítási régió lesz az elsődleges régió. A védelmi tárolók fordított replikáláshoz való leképezése az eredeti és a helyreállítási régiók átváltott szerepköreinek felel meg.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Cache Storage-fiók és cél Storage-fiók létrehozása

A cache Storage-fiók egy szabványos Storage-fiók, amely ugyanabban az Azure-régióban található, mint a replikált virtuális gép. A cache Storage-fiók átmenetileg tárolja a replikálási módosításokat, mielőtt a módosítások átkerülnek a helyreállítási Azure-régióba. Dönthet úgy is, hogy a virtuális gép különböző lemezei esetében eltérő gyorsítótár-tárolási fiókokat határoz meg, de nem szükséges.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

A **felügyelt lemezeket nem használó** virtuális gépek esetén a cél Storage-fiók a helyreállítási régióban lévő Storage-fiók, amely a virtuális gép lemezeit replikálja. A célként megadott Storage-fiók lehet egy standard Storage-fiók vagy egy Premium Storage-fiók. Válassza ki a lemezekre vonatkozó adatváltozási arány (IO írási arány) alapján megkövetelt Storage-fiók típusát, valamint a tárolási típus Azure Site Recovery támogatott elváltozási korlátait.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Hálózati leképezések létrehozása

A hálózati leképezések az elsődleges régióban lévő virtuális hálózatokat a helyreállítási régióban lévő virtuális hálózatokra képezik le. A hálózati megfeleltetés megadja a helyreállítási régióban lévő Azure-beli virtuális hálózatot, hogy az elsődleges virtuális hálózatban lévő virtuális gépnek feladatátvételt kell átadnia. Egyetlen Azure-beli virtuális hálózat csak egyetlen Azure-beli virtuális hálózatra képezhető le egy helyreállítási régióban.

- Hozzon létre egy Azure-beli virtuális hálózatot a helyreállítási régióban a feladatátvételhez:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Az elsődleges virtuális hálózat beolvasása. Az a VNet, amelyhez a virtuális gép csatlakozik:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Hozzon létre hálózati leképezést az elsődleges virtuális hálózat és a helyreállítási virtuális hálózat között:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Hálózati leképezés létrehozása a fordított irányhoz (feladat-visszavételi feladat):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Azure-beli virtuális gép replikálása

Az Azure-beli virtuális gép replikálása **felügyelt lemezekkel**.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Az Azure-beli virtuális gép replikálása nem **felügyelt lemezekkel**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Miután a replikálás megkezdése sikeres volt, a rendszer replikálja a virtuális gépeket a helyreállítási régióba.

A replikálási folyamat kezdetben a helyreállítási régióban lévő virtuális gép replikáló lemezének egy másolatát indítja el. Ezt a fázist a kezdeti replikálási fázisnak nevezzük.

A kezdeti replikálás befejeződése után a replikálás a különbözeti szinkronizálási fázisba kerül. Ezen a ponton a virtuális gép védett, és tesztelhető feladatátvételi művelet is végezhető rajta. A virtuális gépet jelképező replikált elem replikációs állapota a kezdeti replikálás befejeződése után **védett** állapotba kerül.

A virtuális gép replikációs állapotának és replikálási állapotának figyeléséhez a hozzá tartozó replikációval védett elem részleteit kell beszereznie.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Feladatátvételi teszt, ellenőrzés és karbantartás tesztelése

Miután a virtuális gép replikálása elérte a védett állapotot, a virtuális gépen (a virtuális gép replikációval védett elemén) elvégezhető egy teszt feladatátvételi művelet.

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Végezzen feladatátvételi tesztet.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Várjon, amíg a feladatátvételi teszt művelet befejeződik.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

A teszt feladatátvételi feladatának sikeres befejezése után csatlakozhat a feladatátvételi teszt virtuális géphez, és ellenőrizheti a feladatátvételi tesztet.

Ha a tesztelés befejeződött a feladatátvételi teszt során a virtuális gépen, törölje a teszt másolatát a feladatátvételi teszt feladatátvételi műveletének elindításával. Ez a művelet törli a feladatátvételi teszt által létrehozott virtuális gép tesztelési példányát.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Feladatátvétel az Azure-ba

A virtuális gép feladatátvétele egy adott helyreállítási pontra.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Ha a feladatátvételi művelet sikeres, véglegesítheti a feladatátvételi műveletet.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Ismételt védelem és feladat-visszavétel a forrás régióba

Feladatátvétel után, amikor készen áll arra, hogy visszalépjen az eredeti régióba, a parancsmag használatával indítsa el a replikációval védett elem visszirányú replikálását `Update-AzRecoveryServicesAsrProtectionDirection` .

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Az ismételt védelem befejeződése után fordított irányban, az USA keleti régiójában, és a forrásoldali régióba való visszatérés után visszahelyezheti a feladatátvételt.

## <a name="disable-replication"></a>A replikálás letiltása

A replikációt letilthatja a `Remove-AzRecoveryServicesAsrReplicationProtectedItem` parancsmaggal.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicationProtectedItem
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [Azure site Recovery PowerShell-hivatkozást](/powershell/module/az.RecoveryServices) , amelyből megtudhatja, hogyan végezhet el további feladatokat, például helyreállítási terveket hozhat létre, és tesztelheti a helyreállítási tervek feladatátvételét a PowerShell használatával.
