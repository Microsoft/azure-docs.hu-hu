---
title: Közelségben lévő elhelyezési csoportokban futó Azure-beli virtuális gépek replikálása
description: Megtudhatja, hogyan replikálhatja az Azure-beli virtuális gépeket Azure Site Recovery használatával a közelségi elhelyezési csoportokban.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: a58ec80c13ee9ae0eceb019ab2fd7909fd6f369b
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889280"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Közelségi elhelyezési csoportokban futó, Azure-alapú virtuális gépek replikálása egy másik régióba

Ez a cikk azt ismerteti, hogyan replikálhatja, feladatátvételi és feladat-visszavételi pontokon futó virtuális gépeket egy másodlagos régióba helyezheti el egy közeli elhelyezési csoportban.

A [közelségi csoportok](../virtual-machines/windows/proximity-placement-groups-portal.md) egy Azure-beli virtuális gépek logikai csoportosítási funkciója, amellyel csökkentheti az alkalmazásaihoz tartozó, virtuális gépek közötti hálózati késést. Ha a virtuális gépek ugyanarra a közelségi elhelyezési csoportba vannak helyezve, fizikailag a lehető legközelebb helyezkednek el egymáshoz. A közelségi elhelyezési csoportok különösen hasznosak a késésre érzékeny munkaterhelések követelményeinek kielégítése érdekében.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Vész-helyreállítás földrajzi elhelyezési csoportokkal

Egy tipikus forgatókönyv esetében előfordulhat, hogy a virtuális gépek egy közeli elhelyezési csoportban futnak, hogy elkerülje az alkalmazás különböző szintjei közötti hálózati késést. Habár ez az alkalmazás optimális hálózati késését biztosítja, ezeket az alkalmazásokat a Site Recovery bármely régió szintű meghibásodás esetén szeretné biztosítani. Site Recovery replikálja az egyik régió adatait egy másik Azure-régióba, és felveszi a számítógépeket a vész-helyreállítási régióban a feladatátvételi esemény során.

## <a name="considerations"></a>Megfontolandó szempontok

- A legjobb megoldás az, ha a virtuális gépeket egy közelségi elhelyezési csoportba helyezi a feladatátvétel/feladat-visszavétel során. Ha azonban a virtuális gép a feladatátvétel/feladat-visszavétel során nem tud megjelenni a közelségben, akkor a feladatátvétel/feladat-visszavétel továbbra is megtörténik, és a virtuális gépeket a közelségi elhelyezési csoporton kívül hozza létre a rendszer.
- Ha egy rendelkezésre állási csoport egy közelségi helyhez van rögzítve, és a rendelkezésre állási csoportban lévő feladatátvételi/feladat-visszavételi virtuális gépeken foglalási korlátozás van megadva, akkor a virtuális gépek a rendelkezésre állási csoporton és a közelségi elhelyezési csoportban kívül is létrejönnek.
- Nem felügyelt lemezek esetén nem támogatott a közelségi csoportok Site Recovery.

> [!NOTE]
> A Azure Site Recovery nem támogatja a Hyper-V – Azure forgatókönyvek esetében a felügyelt lemezek feladat-visszavételét. Ezért az Azure-ból a Hyper-V-be történő, a közelségi elhelyezési csoportból történő feladat-visszavétel nem támogatott.

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-portal"></a>A virtuális gépek vész-helyreállításának beállítása a közeli elhelyezési csoportokban a portálon keresztül

### <a name="azure-to-azure-via-portal"></a>Azure-ról Azure-ra a portálon keresztül

Dönthet úgy, hogy engedélyezi a virtuális gépek replikálását a virtuális gép vész-helyreállítási oldalán, vagy egy előre létrehozott tárolóba lépjen, és navigáljon a Site Recovery szakaszhoz, majd engedélyezze a replikációt. Nézzük meg, hogyan állíthat be Site Recovery a PPG-n belüli virtuális gépekre mindkét megközelítéssel:

- Válassza ki a PPG elemet a DR régióban, miközben engedélyezi a replikációt a IaaS VM DR paneljén keresztül:
  1. Nyissa meg a virtuális gépet. A bal oldali panel Operations (műveletek) területén válassza a vész-helyreállítás lehetőséget.
  2. Az "alapismeretek" lapon válassza ki azt a DR régiót, amelyre a virtuális gépet replikálni szeretné. Ugrás a speciális beállítások menüpontra
  3. Itt láthatja a virtuális gép közelségét, valamint azt a lehetőséget, hogy a PPG a DR régióban legyen kiválasztva. A Site Recovery lehetővé teszi, hogy egy új, az általa létrehozott Proximity-elhelyezési csoportot használjon, ha ezt az alapértelmezett beállítást választja. Szabadon választhatja ki a kívánt közelségi csoportot, majd megtekintheti a "felülvizsgálat + replikáció indítása" lehetőséget, majd végül engedélyezheti a replikálást.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="Replikáció engedélyezése.":::

- A PPG kiválasztásához a DR régióban, miközben engedélyezi a replikációt a tár paneljén keresztül:
  1. Nyissa meg a Recovery Services-tárolót, és lépjen a Site Recovery lapra
  2. Kattintson a "+ engedélyezés Site Recovery" elemre, majd válassza az "1: replikáció engedélyezése" lehetőséget az Azure Virtual Machines szolgáltatásban (az Azure-beli virtuális gépek replikálásakor)
  3. Adja meg a kötelező mezőket a forrás lapon, és kattintson a Next (tovább) gombra.
  4. Válassza ki azoknak a virtuális gépeknek a listáját, amelyeken engedélyezni szeretné a replikálást a virtuális gépek lapon, és kattintson a Next (tovább) gombra.
  5. Itt megtekintheti a PPG kiválasztásának lehetőségét a DR régióban. A Site Recovery egy új, az általa létrehozott PPG használatát is lehetővé teszi, ha ezt az alapértelmezett beállítást választja. Szabadon kiválaszthatja a kívánt PPG-t, majd folytathatja a replikáció engedélyezését.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="Engedélyezze a replikálást a tárolón keresztül.":::

Vegye figyelembe, hogy a virtuális gép replikálásának engedélyezése után egyszerűen frissítheti a PPG kiválasztást a DR régióban.

1. Nyissa meg a virtuális gépet, és a bal oldali panelen, a "műveletek" területen válassza a "vész-helyreállítás" lehetőséget.
2. Lépjen a "számítás és hálózat" panelre, és kattintson a lap tetején található Szerkesztés gombra.
3. Megtekintheti a több cél beállításainak szerkesztésének lehetőségeit, beleértve a cél PPG-t is. Válassza ki azt a PPG-t, amelyre a virtuális gépet szeretné átvenni, majd kattintson a Save (Mentés) gombra.

### <a name="vmware-to-azure-via-portal"></a>VMware-ből az Azure-ba portálon keresztül

A virtuális gép replikálásának engedélyezése után beállítható a célként megadott virtuális gép közelségi csoportja. Kérjük, külön hozza létre a PPG-t a célként megadott régióban a követelménynek megfelelően. Ezt követően egyszerűen frissítheti a PPG-kiválasztást a DR régióban, miután engedélyezte a virtuális gép replikációját.

1. Válassza ki a virtuális gépet a tárolóból, és a bal oldali panelen, a műveletek területen válassza a "vész-helyreállítás" lehetőséget.
2. Lépjen a "számítás és hálózat" panelre, és kattintson a lap tetején található Szerkesztés gombra.
3. Megtekintheti a több cél beállításainak szerkesztésének lehetőségeit, beleértve a cél PPG-t is. Válassza ki azt a PPG-t, amelyre a virtuális gépet szeretné átvenni, majd kattintson a Save (Mentés) gombra.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="A PPG V2A frissítése":::

### <a name="hyper-v-to-azure-via-portal"></a>Hyper-V – Azure portálon keresztül

A virtuális gép replikálásának engedélyezése után beállítható a célként megadott virtuális gép közelségi csoportja. Kérjük, külön hozza létre a PPG-t a célként megadott régióban a követelménynek megfelelően. Ezt követően egyszerűen frissítheti a PPG-kiválasztást a DR régióban, miután engedélyezte a virtuális gép replikációját.

1. Válassza ki a virtuális gépet a tárolóból, és a bal oldali panelen, a műveletek területen válassza a "vész-helyreállítás" lehetőséget.
2. Lépjen a "számítás és hálózat" panelre, és kattintson a lap tetején található Szerkesztés gombra.
3. Megtekintheti a több cél beállításainak szerkesztésének lehetőségeit, beleértve a cél PPG-t is. Válassza ki azt a PPG-t, amelyre a virtuális gépet szeretné átvenni, majd kattintson a Save (Mentés) gombra.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="A PPG H2A frissítése":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>A virtuális gépek vész-helyreállításának beállítása a közeli elhelyezési csoportokban a PowerShell használatával

### <a name="prerequisites"></a>Előfeltételek 

1. Győződjön meg arról, hogy rendelkezik a Azure PowerShell az modulhoz. Ha Azure PowerShellt kell telepítenie vagy frissítenie, a [Azure PowerShell telepítéséhez és konfigurálásához](/powershell/azure/install-az-ps)kövesse az alábbi útmutatót.
2. A minimális Azure PowerShell az verziónak 4.1.0 kell lennie. Az aktuális verzió ellenõrzéséhez használja az alábbi parancsot:

    ```
    Get-InstalledModule -Name Az
    ```

### <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Virtual Machines Site Recovery beállítása a közelségi elhelyezési csoportban

> [!NOTE]
> Győződjön meg arról, hogy a cél közelségi csoportok egyedi azonosítója kéznél van. Ha új Proximity-elhelyezési csoportot hoz létre, akkor [itt](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) tekintse meg [a parancsot,](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups)és ha meglévő közelségi elhelyezési csoportot használ, használja a parancsot.

### <a name="azure-to-azure"></a>Azure – Azure

1. [Jelentkezzen](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription) be a fiókjába, és állítsa be az előfizetését.
2. Szerezze be a replikálni kívánt virtuális gép részleteit az [itt](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated)leírtak szerint.
3. [Hozza létre](./azure-to-azure-powershell.md#create-a-recovery-services-vault) a Recovery Services-tárolót, és [állítsa be](./azure-to-azure-powershell.md#set-the-vault-context) a tár környezetét.
4. Készítse elő a tárolót a replikálási virtuális gép elindításához. Ez magában foglalja a [Service Fabric-objektum](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) létrehozását az elsődleges és a helyreállítási régiók esetében is.
5. [Hozzon létre](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) egy site Recovery védelmi tárolót mind az elsődleges, mind a helyreállítási hálóhoz.
6. [Hozzon létre](./azure-to-azure-powershell.md#create-a-replication-policy) egy replikációs házirendet.
7. Hozzon létre egy védelmi tárolót az elsődleges és a helyreállítási védelmi tároló között az [alábbi](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) lépésekkel és egy védelmi tároló hozzárendelésével az [itt](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)említett feladat-visszavételhez.
8. A cache Storage-fiók létrehozásához kövesse [az](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account) alábbi lépéseket.
9. Hozza létre a szükséges hálózati leképezéseket az [itt](./azure-to-azure-powershell.md#create-network-mappings)leírtak szerint.
10. Az Azure-beli virtuális gép felügyelt lemezekkel való replikálásához használja az alábbi PowerShell-parancsmagot:

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Make sure to replace the variables $datadiskName with data disk name.

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Több adatlemez replikálásának engedélyezésekor használja az alábbi PowerShell-parancsmagot:

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig

#Data disk

# Add data disks
Foreach( $disk in $VM.StorageProfile.DataDisks)
{
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $dataDiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
    $diskconfigs += $DataDisk1ReplicationConfig
}

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Ha a zónában engedélyezi a zónák replikálását, a replikálás elindítására szolgáló parancs a PowerShell-parancsmaggal lesz kicserélve.

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

Miután a replikálás megkezdése sikeres volt, a rendszer replikálja a virtuális gépeket a helyreállítási régióba.

A replikálási folyamat kezdetben a helyreállítási régióban lévő virtuális gép replikáló lemezének egy másolatát indítja el. Ezt a fázist a kezdeti replikálási fázisnak nevezzük.

A kezdeti replikálás befejeződése után a replikálás a különbözeti szinkronizálási fázisba kerül. Ezen a ponton a virtuális gép védett, és egy teszt feladatátvételi művelet is elvégezhető rajta. A virtuális gépet jelképező replikált elem replikációs állapota a kezdeti replikálás befejeződése után védett állapotba kerül.

A virtuális gép replikációs állapotának és replikálási állapotának figyeléséhez a hozzá tartozó replikációval védett elem részleteit kell beszereznie.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. A feladatátvételi teszt végrehajtásához kövesse [az alábbi](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover) lépéseket.
12. A feladatátvételhez kövesse az [itt](./azure-to-azure-powershell.md#fail-over-to-azure)említett lépéseket.
13. Az alábbi PowerShell-parancsmag használatával a forrás-régióba történő ismételt védelemmel és feladat-visszavételsel

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. A replikáció letiltásához kövesse az [alábbi lépéseket.](./azure-to-azure-powershell.md#disable-replication)

### <a name="vmware-to-azure-via-powershell"></a>VMware-ből az Azure-ba a PowerShell használatával

1. Győződjön meg arról, hogy [előkészíti a helyszíni VMware-kiszolgálókat](./vmware-azure-tutorial-prepare-on-premises.md) az Azure-ba való vész-helyreállításra.
2. Jelentkezzen be a fiókjába, és állítsa be az előfizetését az [itt](./vmware-azure-disaster-recovery-powershell.md#log-into-azure)megadott módon.
3. [Hozzon](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault) létre egy Recovery Services-tárolót, és [állítsa be a tár környezetét](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context).
4. A tár regisztrációjának [ellenőrzése](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration) .
5. [Hozzon létre](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy) egy replikációs házirendet.
6. [Adjon hozzá](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) egy vCenter-kiszolgálót, és fedezze fel a virtuális gépeket, és [hozzon létre](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication) tárolási fiókokat a replikáláshoz
7. A VMware Virtual Machines replikálásához tekintse meg a részleteket, és kövesse az alábbi PowerShell-parancsmagot:

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. A Get-ASRReplicationProtectedItem parancsmaggal ellenőrizhető a virtuális gép replikációs állapotának és replikálásának állapota.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. A feladatátvételi beállítások konfigurálásához [kövesse az alábbi lépéseket.](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings)
10. [Futtasson](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover) feladatátvételi tesztet.
11. Feladatátvétel az Azure-ba az [alábbi](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure) lépésekkel.

### <a name="hyper-v-to-azure-via-powershell"></a>Hyper-V-ről Azure-ra a PowerShell használatával

1. Győződjön meg arról, hogy [előkészíti a helyszíni Hyper-V-kiszolgálókat](./hyper-v-prepare-on-premises-tutorial.md) a vész-helyreállításra az Azure-ban.
2. [Jelentkezzen](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account) be az Azure-ba.
3. [Állítsa](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault) be a tárolót, és [állítsa be](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context) a Recovery Services-tároló környezetét.
4. [Hozzon létre](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) egy Hyper-V-helyet.
5. [Telepítse](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent) a szolgáltatót és az ügynököt.
6. [Hozzon létre](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy) egy replikációs házirendet.
7. Engedélyezze a replikálást az alábbi lépések használatával – 
    
    a. Kérje le a védelemmel ellátni kívánt virtuális géphez tartozó védhető elem beolvasását az alábbiak szerint:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. A virtuális gép megóvása. Ha a védeni kívánt virtuális gépnek több lemeze is van, akkor az operációs rendszer lemezét a OSDiskName paraméter használatával kell megadni.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. Várja meg, hogy a virtuális gépek a kezdeti replikálás után elérjék a védett állapotot. Ez eltarthat egy ideig, attól függően, hogy milyen tényezőket kell replikálni, valamint az elérhető upstream sávszélességet az Azure-ba. Ha védett állapot van érvényben, a rendszer a következő módon frissíti a feladatok állapotát és a StateDescription: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Frissítse a helyreállítási tulajdonságokat (például a virtuálisgép-szerepkör méretét) és az Azure-hálózatot, amelyhez a virtuális gép hálózati adapterét a feladatátvételt követően csatlakoztatni szeretné.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Futtasson [feladatátvételi](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover)tesztet.


## <a name="next-steps"></a>Következő lépések

A VMware-ből az Azure-ba történő ismételt védelem és feladat-visszavétel végrehajtásához kövesse az [itt](./vmware-azure-prepare-failback.md)ismertetett lépéseket.

A Hyper-V-ről az Azure-ba történő feladatátvétel végrehajtásához kövesse az [itt](./site-recovery-failover.md) ismertetett lépéseket, és hajtsa végre a feladat-visszavételt, kövesse az [itt](./hyper-v-azure-failback.md)ismertetett lépéseket.

További információ: [feladatátvétel site Recoveryban](site-recovery-failover.md).
