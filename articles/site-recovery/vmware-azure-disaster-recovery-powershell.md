---
title: VMware vész-helyreállítás beállítása a PowerShell-lel az Azure site Revoing szolgáltatásban
description: Ismerje meg, hogyan állíthatja be a replikációt és a feladatátvételt az Azure-ba a VMware virtuális gépek vész-helyreállításához a Azure Site Recovery PowerShell használatával.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: de25a3f9df04b09a7337dc889a688a171d98db28
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86129906"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>VMware virtuális gépek Azure-ba történő vészhelyreállításának beállítása a PowerShell használatával

Ebből a cikkből megtudhatja, hogyan replikálhat és feladatátvételt hajthat végre a VMware virtuális gépeken az Azure-ban Azure PowerShell használatával.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - Hozzon létre egy Recovery Services-tárolót, és állítsa be a tár környezetét.
> - Érvényesítse a kiszolgáló regisztrációját a tárolóban.
> - Replikáció beállítása, beleértve a replikációs házirendet. Adja hozzá a vCenter-kiszolgálót, és fedezze fel a virtuális gépeket.
> - VCenter-kiszolgáló hozzáadása és felderítése
> - Hozzon létre Storage-fiókokat a replikációs naplók vagy az adattároláshoz, és replikálja a virtuális gépeket.
> - Feladatátvétel végrehajtása. A feladatátvételi beállítások konfigurálása, a virtuális gépek replikálására vonatkozó beállítások végrehajtása.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](vmware-azure-architecture.md).
- Tekintse át az összes összetevő [támogatási követelményeit](./vmware-physical-azure-support-matrix.md) .
- A Azure PowerShell `Az`  modul. Ha Azure PowerShellt kell telepítenie vagy frissítenie, a [Azure PowerShell telepítéséhez és konfigurálásához](/powershell/azure/install-az-ps)kövesse az alábbi útmutatót.

## <a name="log-into-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a Connect-AzAccount parancsmag használatával:

```azurepowershell
Connect-AzAccount
```
Válassza ki azt az Azure-előfizetést, amelyre a VMware virtuális gépeket replikálni szeretné. Használja a Get-AzSubscription parancsmagot azon Azure-előfizetések listájának lekéréséhez, amelyekhez hozzáfér. Válassza ki azt az Azure-előfizetést, amellyel dolgozni szeretne a Select-AzSubscription parancsmag használatával.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Helyreállítási tár beállítása

1. Hozzon létre egy erőforráscsoportot, amelyben létre kívánja hozni a Recovery Services-tárolót. Az alábbi példában az erőforráscsoport neve VMwareDRtoAzurePS, és a Kelet-Ázsia régióban jön létre.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Hozzon létre egy Recovery Services-tárolót. Az alábbi példában a Recovery Services-tároló neve VMwareDRToAzurePs, és a Kelet-Ázsia régióban és az előző lépésben létrehozott erőforráscsoporthoz jön létre.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. Töltse le a tároló regisztrációs kulcsát. A tároló regisztrációs kulcsa a helyszíni konfigurációs kiszolgáló regisztrálása a tárolóban. A regisztráció a konfigurációs kiszolgáló szoftvertelepítési folyamatának részét képezi.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Használja a letöltött tároló regisztrációs kulcsát, és kövesse az alábbi cikkekben ismertetett lépéseket a konfigurációs kiszolgáló telepítésének és regisztrációjának befejezéséhez.
   - [Válassza ki a védelmi célokat](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [A forráskörnyezet beállítása](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>A tár környezetének beállítása

A tároló környezetének beállítása a Set-ASRVaultContext parancsmaggal. A beállítás után a rendszer a PowerShell-munkamenet következő Azure Site Recovery műveleteit a kijelölt tároló környezetében hajtja végre.

> [!TIP]
> A Azure Site Recovery PowerShell-modul (az. Recoveryservices szolgáltatónál modul) a legtöbb parancsmaghoz egyszerűen használható aliasokat tartalmaz. A modulban lévő parancsmagok az űrlapot *\<Operation> - **AzRecoveryServicesAsr** \<Object>* , és egyenértékű aliasokkal rendelkeznek, amelyek az *\<Operation> - **ASR** \<Object>*-űrlapot fogadják. A egyszerű használat érdekében lecserélheti a parancsmagok aliasait.

Az alábbi példában a $vault változóból származó tár adatait a rendszer a PowerShell-munkamenethez tartozó tár környezetének megadására használja.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Az Set-ASRVaultContext parancsmag alternatívájaként az egyik a Import-AzRecoveryServicesAsrVaultSettingsFile parancsmagot is használhatja a tár környezetének beállításához. Itt adhatja meg azt az elérési utat, ahol a tároló regisztrációs kulcsa a-Path paraméterként található a Import-AzRecoveryServicesAsrVaultSettingsFile parancsmaghoz. Például:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
A cikk következő fejezetei azt feltételezik, hogy az Azure Site Recovery-műveletekhez tartozó tár környezete be lett állítva.

## <a name="validate-vault-registration"></a>Tár regisztrációjának ellenőrzése

Ebben a példában a következőkkel rendelkezünk:

- Egy konfigurációs kiszolgáló (**ConfigurationServer**) regisztrálva van a tárolóban.
- Egy további Process Server (**horizontális felskálázás-ProcessServer**) regisztrálva van a *ConfigurationServer*
- A fiókokat (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) beállították a konfigurációs kiszolgálón. Ezek a fiókok a vCenter-kiszolgáló hozzáadásához, a virtuális gépek felderítéséhez, illetve a replikálni kívánt Windows-és Linux-kiszolgálókon történő leküldéshez használhatók.

1. A regisztrált konfigurációs kiszolgálókat Site Recoveryban található Fabric-objektum képviseli. Szerezze be a háló objektumok listáját a tárolóban, és azonosítsa a konfigurációs kiszolgálót.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Azonosítsa a gépek replikálásához használható folyamat-kiszolgálókat.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   A fenti kimenetből ***$ProcessServers [0]** _ a _horizontális felskálázás-ProcessServer * és a ***$ProcessServers [1]**_ értéknek felel meg a folyamat kiszolgálói szerepkörének _ConfigurationServer *

3. Azonosítsa a konfigurációs kiszolgálón beállított fiókokat.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   A fenti kimenetből ***$AccountHandles [0]** _ a ( _vCenter_account *, ***$AccountHandles [1]**_ fióknak felel meg a _WindowsAccount *, és ***$AccountHandles [2]**_ fiókhoz _LinuxAccount *

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

Ebben a lépésben két replikációs házirend jön létre. Egy házirend a VMware virtuális gépek Azure-ba történő replikálásához, a másikat pedig az Azure-ban futó virtuális gépek feladatátvételére a helyszíni VMware-helyre.

> [!NOTE]
> A legtöbb Azure Site Recovery művelet aszinkron módon fut. Amikor műveletet kezdeményez, egy Azure Site Recovery feladatot küld, és a rendszer visszaadja a feladatok követésére szolgáló objektumot. Ez a feladatok követésére szolgáló objektum használható a művelet állapotának figyelésére.

1. Hozzon létre egy *ReplicationPolicy* nevű replikációs házirendet a VMWare virtuális gépek Azure-ba történő replikálásához a megadott tulajdonságokkal.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Hozzon létre egy replikációs házirendet, amelyet az Azure-ból a helyszíni VMware-helyre történő feladat-visszavételhez kíván használni.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   A művelet befejezésének nyomon követéséhez használja a *$Job _FailbackPolicyCreate* található feladatok részleteit.

   * Hozzon létre egy védelmi tároló hozzárendelést a replikációs házirendek a konfigurációs kiszolgálóval való leképezéséhez.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>VCenter-kiszolgáló hozzáadása és virtuális gépek felderítése

Adjon hozzá egy vCenter Server IP-cím vagy állomásnév alapján. A **-port** paraméter határozza meg, hogy a vCenter-kiszolgáló melyik porton csatlakozik a (z) **-Name** paraméterhez a vCenter-kiszolgálóhoz való csatlakozáshoz használt felhasználóbarát név, a  **-Account** paraméter pedig a vCenter-kiszolgáló által kezelt virtuális gépek felderítéséhez használt fiók leíróját adja meg a konfigurációs kiszolgálón.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Storage-fiókok létrehozása replikáláshoz

**A felügyelt lemezre való íráshoz használja a [PowerShellt az. recoveryservices szolgáltatónál modul 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) -től kezdődően.** Csak a log Storage-fiók létrehozását igényli. Ajánlott szabványos fióktípus és LRS redundancia használata, mivel csak ideiglenes naplók tárolására szolgál. Győződjön meg arról, hogy a Storage-fiók ugyanabban az Azure-régióban lett létrehozva, mint a tároló.

Ha az az. Recoveryservices szolgáltatónál modul 2.0.0-nál régebbi verzióját használja, a következő lépésekkel hozhatja létre a Storage-fiókokat. Ezek a Storage-fiókok később a virtuális gépek replikálásához használatosak. Győződjön meg arról, hogy a Storage-fiókok ugyanabban az Azure-régióban jönnek létre, mint a tároló. Ezt a lépést kihagyhatja, ha egy meglévő Storage-fiókot kíván használni a replikáláshoz.

> [!NOTE]
> Ha a helyszíni virtuális gépeket prémium szintű Storage-fiókba replikálja, meg kell adnia egy további szabványos Storage-fiókot (log Storage-fiók). A log Storage-fiók közbenső tárolóként tárolja a replikálási naplókat, amíg a naplók nem alkalmazhatók a Premium Storage-célra.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>VMware virtuális gépek replikálása

Körülbelül 15-20 percet vesz igénybe, hogy a rendszer felderítse a virtuális gépeket a vCenter-kiszolgálóról. A felderítést követően a rendszer létrehoz egy Protected Item objektumot az egyes felderített virtuális gépek Azure Site Recoveryban. Ebben a lépésben a felderített virtuális gépek közül három replikálódik az előző lépésben létrehozott Azure Storage-fiókokra.

A felderített virtuális gépek elleni védelemhez a következő adatokra lesz szüksége:

* A replikálható védett elemek.
* A virtuális gép (csak a Storage-fiókba való replikálás esetén) replikálására szolgáló Storage-fiók. 
* A virtuális gépek Premium Storage-fiókba vagy felügyelt lemezre való védeleméhez naplófájlra van szükség.
* A replikáláshoz használandó folyamat-kiszolgáló. A rendszer lekérte és mentette a rendelkezésre álló folyamat-kiszolgálók listáját a ***$ProcessServers [0]** _ _(horizontális felskálázás-ProcessServer) * és ***$ProcessServers [1]**_ _ (ConfigurationServer) * változóban.
* Az a fiók, amellyel leküldheti a mobilitási szolgáltatás szoftverét a gépekre. Az elérhető fiókok listája a ***$AccountHandles*** változóban lett beolvasva és tárolva.
* A replikáláshoz használni kívánt replikációs házirend védelmi tárolójának leképezése.
* Az az erőforráscsoport, amelyben a virtuális gépeket létre kell hozni a feladatátvétel során.
* Opcionálisan azt az Azure-beli virtuális hálózatot és alhálózatot, amelyhez a feladatátvételi virtuális gépet csatlakoztatni kell.

Most replikálja a következő virtuális gépeket az ebben a táblázatban megadott beállítások használatával.


|Virtuális gép  |Kiszolgáló feldolgozása        |Tárfiók              |Log Storage-fiók  |Szabályzat           |Fiók a mobilitási szolgáltatás telepítéséhez|Cél-erőforráscsoport  | Célként megadott virtuális hálózat  |Célként megadott alhálózat  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |N/A| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR – vnet                 |Alhálózat – 1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR – vnet                 |Alhálózat – 1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| N/A                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR – vnet                 |Alhálózat – 1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Miután a replikálás engedélyezése művelet sikeresen befejeződik, a rendszer elindítja a kezdeti replikálást a virtuális gépeken. A kezdeti replikálás a replikálható adatmennyiségtől és a replikáláshoz rendelkezésre álló sávszélességtől függően eltarthat egy ideig. A kezdeti replikálás befejeződése után a virtuális gép védett állapotba kerül. Miután a virtuális gép elérte a védett állapotot, elvégezheti a virtuális gép feladatátvételi tesztjét, hozzáadhatja azt helyreállítási tervekhez stb.

A Get-ASRReplicationProtectedItem parancsmaggal ellenőrizhető a virtuális gép replikációs állapotának és replikálásának állapota.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Feladatátvételi beállítások konfigurálása

A védett gépek feladatátvételi beállításai a Set-ASRReplicationProtectedItem parancsmag használatával frissíthetők. A parancsmaggal frissíthető beállítások némelyike a következő lehet:
* A feladatátvételkor létrehozandó virtuális gép neve
* A feladatátvételkor létrehozandó virtuális gép VM-mérete
* Azure-beli virtuális hálózat és alhálózat, amely a virtuális gép hálózati adaptereit csatlakoztatni kell a feladatátvételhez
* Feladatátvétel felügyelt lemezekre
* Azure Hybrid Use Benefit alkalmazása
* Rendeljen hozzá egy statikus IP-címet a cél virtuális hálózatból, amelyet a virtuális géphez szeretne rendelni a feladatátvételkor.

Ebben a példában a virtuális gép *Win2K12VM1* a feladatátvételen létrehozandó virtuális gép virtuálisgép-méretét frissítjük, és azt, hogy a virtuális gép felügyelt lemezeket használjon a feladatátvétel során.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. Futtasson egy DR-részletezést (feladatátvételi tesztet) a következőképpen:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Miután a feladatátvételi teszt sikeresen befejeződik, megfigyelheti, hogy egy *"-test"* utótagú virtuális gép (ebben az esetben Win2K12VM1-test) a neve az Azure-ban jön létre.
3. Most már csatlakozhat a teszthez a virtuális gépen, és ellenőrizheti a feladatátvételi tesztet.
4. Törölje a feladatátvételi tesztet a Start-ASRTestFailoverCleanupJob parancsmag használatával. Ez a művelet törli a feladatátvételi teszt részeként létrehozott virtuális gépet.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Feladatátvétel az Azure-ba

Ebben a lépésben a virtuális gép Win2K12VM1 egy adott helyreállítási pontra hajtjuk végre.

1. A feladatátvételhez használható elérhető helyreállítási pontok listájának beolvasása:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. A feladatátvétel sikeres végrehajtása után véglegesítheti a feladatátvételi műveletet, és visszaállíthatja a visszirányú replikálást az Azure-ból a helyszíni VMware-helyre.

## <a name="next-steps"></a>Következő lépések
További feladatok automatizálása a [Azure site Recovery PowerShell-hivatkozás](/powershell/module/Az.RecoveryServices)használatával.
