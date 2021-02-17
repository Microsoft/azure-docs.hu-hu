---
title: VMware virtuális gépek migrálása az Azure-ba (ügynök nélkül) – PowerShell
description: Ismerje meg, hogyan futtathatja a VMware virtuális gépek ügynök nélküli áttelepítését Azure Migrate a PowerShell használatával.
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 006b2838a4e593397f8968e53ba2364d16753a40
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547060"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>VMware virtuális gépek migrálása az Azure-ba (ügynök nélkül) – PowerShell

Ebből a cikkből megtudhatja, hogyan telepítheti át a felderített VMware virtuális gépeket az ügynök nélküli módszerrel a [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool)Azure PowerShell használatával.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Felderített VMware virtuális gépek beolvasása egy Azure Migrate projektben.
> * Indítsa el a virtuális gépek replikálását.
> * A virtuális gépek replikálási tulajdonságainak frissítése.
> * Replikáció figyelése.
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Futtasson egy teljes körű virtuálisgép-áttelepítést.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="1-prerequisites"></a>1. előfeltételek

Az oktatóanyag elkezdése előtt:

1. Fejezze be a következő [oktatóanyagot: VMWare virtuális gépek felderítése kiszolgáló-értékeléssel](tutorial-discover-vmware.md) az Azure és a VMware áttelepítéshez való előkészítéséhez.
1. Fejezze be az [oktatóanyagot: VMWare virtuális gépek felmérése Azure-beli virtuális gépekre az](./tutorial-assess-vmware-azure-vm.md) Azure-ba való áttelepítés előtt.
1. [Az az PowerShell-modul telepítése](/powershell/azure/install-az-ps)

## <a name="2-install-azure-migrate-powershell-module"></a>2. Telepítse Azure Migrate PowerShell-modult

Az Azure Migrate PowerShell-modul előzetes verzióban érhető el. A PowerShell-modult a következő parancs használatával kell telepítenie.

```azurepowershell-interactive
Install-Module -Name Az.Migrate
```

## <a name="3-sign-in-to-your-microsoft-azure-subscription"></a>3. Jelentkezzen be a Microsoft Azure-előfizetésbe

Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal.

```azurepowershell
Connect-AzAccount
```

### <a name="select-your-azure-subscription"></a>Válassza ki az Azure-előfizetését

A [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) parancsmag használatával lekérheti azon Azure-előfizetések listáját, amelyekhez hozzáfér. Válassza ki azt az Azure-előfizetést, amelynek a Azure Migrate-projektje a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával működik.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="4-retrieve-the-azure-migrate-project"></a>4. a Azure Migrate projekt beolvasása

Azure Migrate-projekttel a rendszer az Ön által értékelt vagy áttelepíteni kívánt környezetből gyűjtött felderítési, értékelési és áttelepítési metaadatok tárolására szolgál.
A projektekben nyomon követheti a felderített eszközöket, összehangolhatja az értékeléseket, és elvégezheti az áttelepítést.

Az előfeltételek részeként már létrehozott egy Azure Migrate projektet. Használja a [Get-AzMigrateProject](/powershell/module/az.migrate/get-azmigrateproject) parancsmagot egy Azure Migrate-projekt részleteinek lekéréséhez. Meg kell adnia a Azure Migrate projekt ( `Name` ) nevét és a Azure Migrate Project () erőforráscsoport-csoportjának nevét `ResourceGroupName` .

```azurepowershell-interactive
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name MyResourceGroup

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name MyMigrateProject -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
Write-Output $MigrateProject
```

## <a name="5-retrieve-discovered-vms-in-an-azure-migrate-project"></a>5. felderített virtuális gépek beolvasása egy Azure Migrate-projektben

A Azure Migrate egy egyszerűsített [Azure Migrate berendezést](migrate-appliance-architecture.md)használ. Az előfeltételek részeként üzembe helyezte a Azure Migrate készüléket VMware virtuális gépként.

Egy Azure Migrate-projektben egy adott VMware virtuális gép lekéréséhez adja meg a Azure Migrate projekt () `ProjectName` , az Azure Migrate projekt () erőforráscsoport nevét `ResourceGroupName` és a virtuális gép nevét ( `DisplayName` ).

> [!IMPORTANT]
> **A virtuális gép neve ( `DisplayName` ) paraméter értéke megkülönbözteti a kis-és nagybetűket**.

```azurepowershell-interactive
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName MyTestVM

# View discovered server details
Write-Output $DiscoveredServer
```

Ennek az oktatóanyagnak a részeként áttelepíti a virtuális gépet.

A **projektnév** és a **ResourceGroupName** paraméterek használatával lekérheti az összes VMware virtuális gépet egy Azure Migrate-projektben.

```azurepowershell-interactive
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```

Ha egy Azure Migrate-projektben több készülék is található, a **projektnév**, a **ResourceGroupName** és a **ApplianceName** paraméterek használatával kérheti le az összes felderített virtuális gépet egy adott Azure Migrate berendezés használatával.

```azurepowershell-interactive
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName MyMigrateAppliance

```

## <a name="6-initialize-replication-infrastructure"></a>6. replikációs infrastruktúra inicializálása

[Azure Migrate: a kiszolgáló áttelepítése](migrate-services-overview.md#azure-migrate-server-migration-tool) több Azure-erőforrást használ a virtuális gépek áttelepítéséhez. A kiszolgáló áttelepítése a következő erőforrásokat a projekttel megegyező erőforráscsoporthoz helyezi el.

- **Service Bus**: a kiszolgáló áttelepítése a Service Bus használatával küldi el a replikációs előkészítési üzeneteket a berendezésnek.
- **Átjáró Storage-fiók**: a kiszolgáló áttelepítése az átjáró Storage-fiók használatával tárolja a replikált virtuális gépekre vonatkozó állapotinformációkat.
- **Log Storage-fiók**: a Azure Migrate készülék feltölti a virtuális gépek replikációs naplóit egy log Storage-fiókba. Azure Migrate a replikációs adatokat a replika által felügyelt lemezekre alkalmazza.
- **Key Vault**: a Azure Migrate készülék a kulcstartó használatával kezeli a Service Bus kapcsolati karakterláncait, valamint a replikáció során használt Storage-fiókok hozzáférési kulcsait.

Mielőtt replikálja az első virtuális gépet a Azure Migrate projektben, futtassa a következő szkriptet a replikációs infrastruktúra kiépítéséhez. Ez a szkript kiépíti és konfigurálja a fent említett erőforrásokat, így elkezdheti a VMware virtuális gépek áttelepítését.

> [!NOTE]
> Egy Azure Migrate projekt csak egy Azure-régióba való áttelepítést támogatja. A parancsfájl futtatása után nem módosíthatja azt a célként megadott régiót, amelyre a VMware virtuális gépeket át szeretné telepíteni.
> `Initialize-AzMigrateReplicationInfrastructure`Ha új készüléket állít be a Azure Migrate projektben, futtatnia kell a parancsfájlt.

A cikkben inicializáljuk a replikációs infrastruktúrát, hogy a virtuális gépek áttelepíthetők legyenek a `Central US` régióba. [A fájlt letöltheti](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) a GitHub-tárházból, vagy futtathatja a következő kódrészlet használatával.

```azurepowershell-interactive
# Download the script from Azure Migrate GitHub repository
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion CentralUS
```

## <a name="7-replicate-vms"></a>7. virtuális gépek replikálása

A felderítés és a replikációs infrastruktúra inicializálásának befejezése után megkezdheti a VMware virtuális gépek replikálását az Azure-ba. Egyszerre akár 300 replikálást is futtathat.

A replikálási tulajdonságokat a következőképpen adhatja meg.

- **Cél-előfizetés és erőforráscsoport** – adja meg azt az előfizetést és erőforráscsoportot, amelyre a virtuális gépet át kell telepíteni úgy, hogy a paraméterrel megadja az erőforráscsoport azonosítóját `TargetResourceGroupId` .
- **Cél virtuális hálózat és alhálózat** – adja meg az Azure-Virtual Network azonosítóját, valamint annak az alhálózatnak a nevét, amelyről a virtuális gépet át kell telepíteni a `TargetNetworkId` és a paraméterek használatával `TargetSubnetName` .
- **Cél virtuális gép neve** – adja meg a létrehozandó Azure-beli virtuális gép nevét a `TargetVMName` paraméter használatával.
- **Cél virtuális gép mérete** – Itt adhatja meg a replikáláshoz használandó Azure-beli virtuális gép méretét `TargetVMSize` paraméter használatával. Ha például egy virtuális gépet át szeretne telepíteni D2_v2 virtuális gépre az Azure-ban, a "Standard_D2_v2" értéket kell megadnia `TargetVMSize` .
- **Licenc** – Azure Hybrid Benefit használata az aktív frissítési garanciával vagy Windows Server-előfizetésekkel rendelkező Windows Server-gépekhez, a paraméter értékeként adja meg a `LicenseType` következőt: "windowsserver". Ellenkező esetben a paraméter értékét állítsa `LicenseType` "NoLicenseType" értékre.
- **Operációsrendszer-lemez** – Itt adhatja meg annak a lemeznek az egyedi azonosítóját, amely az operációs rendszer rendszerbetöltőjét és a telepítőjét tartalmazta. A használni kívánt lemez a parancsmag használatával beolvasott lemez egyedi azonosító (UUID) tulajdonsága `Get-AzMigrateServer` .
- **Lemez típusa** – adja meg a paraméter értékét az `DiskType` alábbiak szerint.
    - A prémium szintű felügyelt lemezek használatához a paraméter értékeként a "Premium_LRS" értéket kell megadnia `DiskType` .
    - A standard SSD-lemezek használatához a paraméter értékeként a "StandardSSD_LRS" értéket kell megadni `DiskType` .
    - A standard HDD-lemezek használatához a paraméter értékeként a "Standard_LRS" értéket kell megadni `DiskType` .
- **Infrastruktúra-redundancia** – az alábbi módon adhatja meg az infrastruktúra-redundancia beállítást.
    - Rendelkezésre állási zóna az áttelepített gép egy adott rendelkezésre állási zónába való rögzítéséhez a régióban. Ezzel a beállítással olyan kiszolgálókat oszthat szét, amelyek több csomópontos alkalmazási szintet alkotnak Availability Zoneson belül. Ez a beállítás csak akkor érhető el, ha az áttelepítéshez kiválasztott cél régió támogatja a Availability Zones. A rendelkezésre állási zónák használatához meg kell adnia a rendelkezésre állási zóna értékét a `TargetAvailabilityZone` paraméterhez.
    - Rendelkezésre állási csoport, amely az áttelepített gépet egy rendelkezésre állási csoportba helyezi. A választott erőforráscsoport-csoportnak legalább egy rendelkezésre állási készlettel kell rendelkeznie a beállítás használatához. A rendelkezésre állási csoport használatához adja meg a rendelkezésre állási csoport AZONOSÍTÓját a `TargetAvailabilitySet` paraméterhez.

### <a name="replicate-vms-with-all-disks"></a>Virtuális gépek replikálása az összes lemezzel

Ebben az oktatóanyagban a felderített virtuális gép összes lemezét replikáljuk, és új nevet kell megadni a virtuális géphez az Azure-ban. A felderített kiszolgáló első lemezét operációsrendszer-lemezként adjuk meg, és az összes lemezt standard HDD-ként migráljuk. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. A parancsmag olyan feladatot ad vissza, amely nyomon követhető a művelet állapotának figyeléséhez.

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType Standard_LRS -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>Virtuális gépek replikálása a kiválasztott lemezekkel

A [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) parancsmag használatával szelektíven replikálhatja a felderített virtuális gép lemezeit, és a [New-AzMigrateServerReplication](/powershell/module/az.migrate/new-azmigrateserverreplication) parancsmag **DiskToInclude** paraméterének bemenetként is megadhatja. A parancsmag használatával is `New-AzMigrateDiskMapping` megadhat különböző céllemez-típusokat a replikálni kívánt egyes lemezekhez.

Határozza meg a parancsmag következő paramétereinek értékeit `New-AzMigrateDiskMapping` .

- **Beskid** – Itt adhatja meg az áttelepítendő lemez egyedi azonosítóját. A használni kívánt lemez a parancsmag használatával beolvasott lemez egyedi azonosító (UUID) tulajdonsága `Get-AzMigrateServer` .
- **IsOSDisk** – az "igaz" értéket kell megadni, ha az áttelepítendő lemez a virtuális gép operációsrendszer-lemeze, más szóval "false".
- **Lemeztípus** – az Azure-ban használandó lemez típusának megadása.

A következő példában a rendszer csak a felderített virtuális gép két lemezét replikálja. Megadjuk az operációsrendszer-lemezt, és a replikálni kívánt lemezek különböző lemez-típusait használják. A parancsmag olyan feladatot ad vissza, amely nyomon követhető a művelet állapotának figyeléséhez.

```azurepowershell-interactive
# View disk details of the discovered server
Write-Output $DiscoveredServer.Disk

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType StandardSSD_LRS -IsOSDisk true
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType Premium_LRS -IsOSDisk false

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="8-monitor-replication"></a>8. a replikáció figyelése

A replikáció a következőképpen történik:

- Ha a replikáció elindítása feladatainak végrehajtása sikeresen befejeződött, a gépek megkezdik a kezdeti replikálást az Azure-ba.
- A kezdeti replikálás során létrejön egy virtuális gép pillanatképe. A pillanatképből származó lemezes adatok replikálódnak az Azure-ban található replika által felügyelt lemezekre.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replika-lemezekre.

A replikáció állapotának nyomon követéséhez használja a [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) parancsmagot.

> [!NOTE]
> A felderített virtuális gép azonosítója és a replikáló virtuális gép azonosítója két különböző egyedi azonosító. Ezek az azonosítók a replikáló kiszolgálók részleteinek beolvasására is használhatók.

### <a name="monitor-replication-using-discovered-vm-identifier"></a>Replikáció figyelése a felderített virtuális gép azonosítójával

```azurepowershell-interactive
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>Replikáció figyelése a virtuális gép azonosítójának replikálásával

```azurepowershell-interactive
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName |
                     Where-Object MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

Nyomon követheti az **áttelepítési állapot** és az **áttelepítési állapot leírásának** tulajdonságait a kimenetben.

- A kezdeti replikáláshoz az **áttelepítési állapot** és az **áttelepítési állapot leírása** tulajdonságok értéke a következő lesz: `InitialSeedingInProgress` és `Initial replication` .
- A különbözeti replikáció során az **áttelepítési állapot** és az **áttelepítési állapot leírásának** tulajdonságai a és a értékre kerülnek `Replicating` `Ready to migrate` .
- Az áttelepítés befejezése után az **áttelepítési állapot** és az **áttelepítési állapot leírásának** tulajdonságai a és a értékre kerülnek `Migration succeeded` `Migrated` .

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

A replikálás folyamatával kapcsolatos részletekért futtassa a következő parancsmagot.

```azurepowershell-interactive
Write-Output $replicatingserver.ProviderSpecificDetail
```

A kezdeti replikálási folyamat nyomon követhető a kimenet **kezdeti előkészítési folyamatának százalékos** tulajdonságaival.

```Output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

A replikáció a következőképpen történik:

- Ha a replikáció elindítása feladatainak végrehajtása sikeresen befejeződött, a gépek megkezdik a kezdeti replikálást az Azure-ba.
- A kezdeti replikálás során létrejön egy virtuális gép pillanatképe. A pillanatképből származó lemezes adatok replikálódnak az Azure-ban található replika által felügyelt lemezekre.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replika-lemezekre.

## <a name="9-retrieve-the-status-of-a-job"></a>9. a feladatok állapotának beolvasása

A [Get-AzMigrateJob](/powershell/module/az.migrate/get-azmigratejob) parancsmag használatával nyomon követheti a feladatok állapotát.

```azurepowershell-interactive
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="10-update-properties-of-a-replicating-vm"></a>10. a replikáló virtuális gép tulajdonságainak frissítése

[Azure Migrate: a kiszolgáló áttelepítése](migrate-services-overview.md#azure-migrate-server-migration-tool) lehetővé teszi a replikációs virtuális gép számára, hogy megváltoztassa a cél tulajdonságait, például a név, a méret, az erőforráscsoport, a NIC-konfiguráció és így tovább.

A virtuális gépek alábbi tulajdonságai frissíthetők.

- **Virtuális gép neve** – adja meg a létrehozandó Azure-beli virtuális gép nevét a **TargetVMName** paraméter használatával.
- Virtuálisgép- **méret** – Itt adhatja meg a replikáláshoz használandó Azure-beli virtuális gép méretét a **TargetVMSize** paraméter használatával. Ha például egy virtuális gépet át szeretne telepíteni D2_v2 virtuális gépre az Azure-ban, a **TargetVMSize** értéket kell megadnia `Standard_D2_v2` .
- **Virtual Network** – megadhatja annak az Azure Virtual Networknak az azonosítóját, amelyet a virtuális gépet a **TargetNetworkId** paraméterrel kell áttelepíteni a alkalmazásba.
- **Erőforráscsoport** – adja meg annak az erőforrásnak az azonosítóját, amelyet a virtuális gépnek át kell telepítenie a **TargetResourceGroupId** paraméter használatával az erőforráscsoport-azonosító megadásával.
- **Hálózati adapter** – a NIC-konfiguráció a [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping) parancsmag használatával adható meg. Az objektum ezután a [set-AzMigrateServerReplication](/powershell/module/az.migrate/set-azmigrateserverreplication) parancsmag **NicToUpdate** paraméteréhez ad át egy bemenetet.

    - **IP-kiosztás módosítása** – ha statikus IP-címet szeretne megadni egy hálózati adapterhez, adja meg a virtuális gép statikus IP-címeként használni kívánt IPv4-címet a **TargetNicIP** paraméter használatával. Egy hálózati adapter IP-címének dinamikus hozzárendeléséhez adja meg a `auto` **TargetNicIP** paraméter értékét.
    - Használja az `Primary` értékeket `Secondary` vagy `DoNotCreate` a **TargetNicSelectionType** paramétert annak megadásához, hogy a hálózati adapter elsődleges, másodlagos vagy nem hozható létre az áttelepített virtuális gépen. A virtuális gép elsődleges hálózati adaptere csak egy hálózati adaptert adhat meg.
    - Ahhoz, hogy elsődleges hálózati adaptert hozzon létre, meg kell adnia a többi olyan hálózati adaptert, amelyeknek másodlagosnak kell lenniük, vagy amelyeket nem kell létrehozni az áttelepített virtuális gépen.
    - A hálózati adapter alhálózatának módosításához adja meg az alhálózat nevét a **TargetNicSubnet** paraméter használatával.

 - **Rendelkezésre állási zóna** – a rendelkezésre állási zónák használatához meg kell adni a rendelkezésre állási zóna értékét a **TargetAvailabilityZone** paraméterhez.
 - **Rendelkezésre állási csoport** – a rendelkezésre állási csoport használata esetén adja meg a rendelkezésre állási csoport azonosítóját a **TargetAvailabilitySet** paraméterhez.

A `Get-AzMigrateServerReplication` parancsmag olyan feladatot ad vissza, amely nyomon követhető a művelet állapotának figyeléséhez.

```azurepowershell-interactive
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```

A következő példában a hálózati adapter konfigurációját úgy frissítjük, hogy az első NIC-t elsődlegesként és statikus IP-címet rendeli hozzá. elvetjük a második hálózati adaptert az áttelepítéshez, és frissíti a cél virtuális gép nevét és méretét.

```azurepowershell-interactive
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP ###.###.###.### -TargetNicSelectionType Primary
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType DoNotCreate

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize Standard_DS13_v2 -TargetVMName MyMigratedVM -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq 'InProgress') -or ($UpdateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $UpdateJob.State
```

Az Azure Migrate-projektben lévő összes replikálási kiszolgálót listázhatja is, majd a virtuális gép tulajdonságainak replikálásával frissítheti a virtuálisgép-tulajdonságokat.

```azurepowershell-interactive
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName |
                     Where-Object MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

## <a name="11-run-a-test-migration"></a>11. tesztelési áttelepítés futtatása

A különbözeti replikáció elkezdése előtt futtathatja a virtuális gépekre vonatkozó tesztelési áttelepítést, mielőtt teljes áttelepítést futtat az Azure-ba. Javasoljuk, hogy az áttelepítés előtt legalább egyszer végezze el a Migrálás tesztelését az egyes gépeken. A parancsmag olyan feladatot ad vissza, amely nyomon követhető a művelet állapotának figyeléséhez.

- Az áttelepítési teszt futtatása ellenőrzi, hogy az áttelepítés a várt módon fog-e működni. Az áttelepítés tesztelése nem érinti a helyszíni gépet, amely működőképes marad, és folytatja a replikálást.
- A tesztelési áttelepítés szimulálja az áttelepítést egy Azure-beli virtuális gép replikálási adataival történő létrehozásával (általában az Azure-előfizetése nem üzemi VNet való áttelepítéssel).
- A replikált Azure-beli virtuális gép használatával ellenőrizheti az áttelepítést, elvégezheti az alkalmazások tesztelését, és bármilyen problémát megtehet a teljes áttelepítés előtt.

Válassza ki a teszteléshez használni kívánt Azure-Virtual Network a virtuális hálózat AZONOSÍTÓjának megadásával a **TestNetworkID** paraméter használatával.

```azurepowershell-interactive
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq 'InProgress') -or ($TestMigrationJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $TestMigrationJob.State
```

A tesztelés befejezése után törölje a teszt áttelepítését a [Start-AzMigrateTestMigrationCleanup](/powershell/module/az.migrate/start-azmigratetestmigrationcleanup) parancsmag használatával. A parancsmag olyan feladatot ad vissza, amely nyomon követhető a művelet állapotának figyeléséhez.

```azurepowershell-interactive
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $CleanupTestMigrationJob.State
```

## <a name="12-migrate-vms"></a>12. virtuális gépek migrálása

Miután ellenőrizte, hogy a teszt áttelepítése a várt módon működik-e, áttelepítheti a replikálási kiszolgálót a következő parancsmag használatával. A parancsmag olyan feladatot ad vissza, amely nyomon követhető a művelet állapotának figyeléséhez.

Ha nem szeretné kikapcsolni a forráskiszolgáló kikapcsolását, ne használja a **TurnOffSourceServer** paramétert.

```azurepowershell-interactive
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="13-complete-the-migration"></a>13. az áttelepítés befejezése

1. Az áttelepítés befejezése után állítsa le a helyszíni gép replikálását, és törölje a replikálási állapotra vonatkozó adatokat a virtuális gép számára a következő parancsmag használatával. A parancsmag olyan feladatot ad vissza, amely nyomon követhető a művelet állapotának figyeléséhez.

   ```azurepowershell-interactive
   # Stop replication for a migrated server
   $StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer

   # Track job status to check for completion
   while (($StopReplicationJob.State -eq 'InProgress') -or ($StopReplicationJob.State -eq 'NotStarted')){
           #If the job hasn't completed, sleep for 10 seconds before checking the job status again
           sleep 10;
           $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
   }
   #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
   Write-Output $StopReplicationJob.State
   ```

1. Telepítse a [Linux](../virtual-machines/extensions/agent-linux.md) -ügynököt az áttelepített gépekre, ha a gépen van Linux operációs rendszer. Az áttelepítés során automatikusan telepíti a Windows rendszerű virtuális gépekhez készült virtuálisgép-ügynököt.
1. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
1. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
1. Az áttelepített Azure virtuálisgép-példány felé irányuló forgalom kivágása.
1. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
1. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
1. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével.

## <a name="14-post-migration-best-practices"></a>14. áttelepítés utáni ajánlott eljárások

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információ](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információ](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő adatforgalom elérését [Azure Security Center – igény szerinti felügyelettel](../security-center/security-center-just-in-time.md).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) használatával.
    - Az [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
-  Fontolja meg az [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.
