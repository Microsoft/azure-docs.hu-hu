---
title: A Hyper-V (VMM) szolgáltatás vész-helyreállításának beállítása másodlagos helyre Azure Site Recovery/PowerShell-lel
description: Ismerteti, hogyan állítható be a VMM-felhőkben futó Hyper-V virtuális gépek vész-helyreállítási folyamata egy másodlagos VMM-helyre a Azure Site Recovery és a PowerShell használatával.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4f7d330db9a4a0d9b435ebe7527e55e37c254e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87086217"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>A Hyper-V virtuális gépek vész-helyreállításának beállítása másodlagos helyre a PowerShell használatával (Resource Manager)

Ez a cikk bemutatja, hogyan automatizálható a System Center Virtual Machine Manager-felhőben lévő Hyper-V virtuális gépek replikálása a Virtual Machine Manager felhőbe egy másodlagos helyszíni helyen a [Azure site Recovery](site-recovery-overview.md)használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át [a forgatókönyv-architektúrát és -összetevőket](hyper-v-vmm-architecture.md).
- Tekintse át az összes összetevő [támogatási követelményeit](./vmware-physical-secondary-support-matrix.md) .
- Győződjön meg arról, hogy a Virtual Machine Manager-kiszolgálók és a Hyper-V-gazdagépek megfelelnek a [támogatási követelményeknek](./vmware-physical-secondary-support-matrix.md).
- Győződjön meg arról, hogy a replikálni kívánt virtuális gépek megfelelnek a [replikált gépek támogatásának](./vmware-physical-secondary-support-matrix.md).

## <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése

[Hálózati leképezési](hyper-v-vmm-network-mapping.md) térképek a helyszíni Virtual Machine Manager virtuálisgép-hálózatok között a forrás-és a cél felhőkben. A leképezés a következőket hajtja végre:

- A feladatátvételt követően összekapcsolja a virtuális gépeket a megfelelő céloldali virtuálisgép-hálózatokkal.
- Optimális módon helyezi el a virtuális replikagépeket a céloldali Hyper-V gazdakiszolgálókon.
- Ha nem konfigurálja a hálózati leképezést, a replika virtuális gépek nem lesznek csatlakoztatva a virtuálisgép-hálózathoz a feladatátvételt követően.

A Virtual Machine Manager előkészítése a következőképpen történik:

- Győződjön meg arról, hogy rendelkezik [Virtual Machine Manager logikai hálózatokkal](/system-center/vmm/network-logical) a forrás és a cél Virtual Machine Manager kiszolgálókon:
  - A forrásoldali kiszolgálón található logikai hálózatnak ahhoz a forrásfelhőhöz kell tartoznia, amelyikben a Hyper-V gazdagépek találhatók.
  - A céloldali kiszolgálón található logikai hálózatnak a célfelhőhöz kell tartoznia.
- Győződjön meg arról, hogy rendelkezik virtuálisgép- [hálózatokkal](/system-center/vmm/network-virtual) a forrás-és a cél Virtual Machine Manager-kiszolgálókon. A virtuálisgép-hálózatokat minden helyen össze kell kapcsolni a logikai hálózattal.
- A forrásoldali Hyper-V gazdagépeken található virtuális gépeket a forrásoldali virtuálisgép-hálózathoz kell csatlakoztatni.

## <a name="prepare-for-powershell"></a>Felkészülés a PowerShellre

Győződjön meg arról, hogy Azure PowerShell készen áll:

- Ha már használja a PowerShellt, frissítsen a 0.8.10 vagy újabb verzióra. [További](/powershell/azure/) információ a PowerShell beállításáról.
- A PowerShell beállítása és konfigurálása után tekintse át a [szolgáltatás-parancsmagokat](/powershell/azure/).
- Ha többet szeretne megtudni a paraméterek értékeinek, bemenetének és kimenetének a PowerShellben való használatáról, olvassa el az [első lépések](/powershell/azure/get-started-azureps) útmutatóját.

## <a name="set-up-a-subscription"></a>Előfizetés beállítása

1. Jelentkezzen be az Azure-fiókjába a PowerShellből.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Az előfizetések listájának beolvasása az előfizetés-azonosítókkal. Jegyezze fel annak az előfizetésnek az AZONOSÍTÓját, amelyben létre szeretné hozni a Recovery Services-tárolót.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Állítsa be a tároló előfizetését.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Ha még nem rendelkezik ilyennel, hozzon létre egy Azure Resource Manager erőforráscsoportot.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Hozzon létre egy új Recovery Services-tárolót. Mentse a tároló objektumot egy változóban, amelyet később szeretne használni.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   A tároló objektumot a parancsmag használatával lehet lekérni, miután létrehozta azt `Get-AzRecoveryServicesVault` .

## <a name="set-the-vault-context"></a>A tár környezetének beállítása

1. Meglévő tár beolvasása.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Állítsa be a tár környezetét.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>A Site Recovery-szolgáltató telepítése

1. A Virtual Machine Manager gépen hozzon létre egy könyvtárat a következő parancs futtatásával:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Bontsa ki a fájlokat a letöltött szolgáltató telepítési fájljának használatával.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Telepítse a szolgáltatót, és várjon, amíg a telepítés befejeződik.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Regisztrálja a kiszolgálót a tárolóban.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Replikációs házirend létrehozása és hozzárendelése

1. Hozzon létre egy replikációs házirendet, ebben az esetben a Hyper-V 2012 R2 esetében a következő módon:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > A Virtual Machine Manager felhő tartalmazhat olyan Hyper-V-gazdagépeket, amelyek a Windows Server különböző verzióit futtatják, de a replikációs házirend az operációs rendszer egy adott verziójára vonatkozik. Ha különböző operációs rendszereken futó gazdagépekkel rendelkezik, hozzon létre külön replikációs házirendeket az egyes rendszerekhez. Ha például öt gazdagép fut a Windows Server 2012-on, a Windows Server 2012 R2-ben pedig három gazdagép fut, hozzon létre két replikációs házirendet. Mindegyik operációs rendszerhez létre kell hoznia egyet.

1. Kérje le az elsődleges védelmi tárolót (az elsődleges Virtual Machine Manager felhőt) és a helyreállítási védelmi tárolót (Recovery Virtual Machine Manager Cloud).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Kérje le a létrehozott replikációs szabályzatot a rövid név használatával.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Indítsa el a védelmi tároló (Virtual Machine Manager felhő) társítását a replikációs házirenddel.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Várjon, amíg a házirend-hozzárendelési feladatok befejeződik. A következő PowerShell-kódrészlettel ellenőrizze, hogy a feladatok befejeződtek-e:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Miután a folyamat befejezte a feldolgozást, futtassa a következő parancsot:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

A művelet befejezésének ellenőrzéséhez kövesse a [tevékenység figyelése](#monitor-activity)című témakör lépéseit.

##  <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása

1. Ezzel a paranccsal lekérheti a kiszolgálókat az aktuális tárolóhoz. A parancs a tömb változóban tárolja a Site Recovery kiszolgálókat `$Servers` .

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Futtassa ezt a parancsot a forrás Virtual Machine Manager-kiszolgáló és a cél Virtual Machine Manager-kiszolgáló hálózatának beolvasásához.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > A forrás Virtual Machine Manager kiszolgáló lehet az első vagy második a kiszolgáló tömbben. Győződjön meg arról, hogy Virtual Machine Manager a kiszolgálók neveit, és megfelelően kéri le a hálózatokat.

1. Ez a parancsmag létrehoz egy leképezést az elsődleges hálózat és a helyreállítási hálózat között. Az első elemeként megadja az elsődleges hálózatot `$PrimaryNetworks` . A helyreállítási hálózatot a első elemeként adja meg `$RecoveryNetworks` .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Virtuális gépek védelmének engedélyezése

Miután a kiszolgálók, a felhők és a hálózatok megfelelően vannak konfigurálva, engedélyezze a Felhőbeli virtuális gépek védelmét.

1. A védelem engedélyezéséhez futtassa a következő parancsot a védelmi tároló lekéréséhez:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Szerezze be a védelmi entitást (VM) a következőképpen:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Engedélyezze a virtuális gép replikálását.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Ha a CMK-kompatibilis felügyelt lemezeket az Azure-ban szeretné replikálni, hajtsa végre a következő lépéseket az az PowerShell 3.3.0-től kezdődően:
>
> 1. Feladatátvétel engedélyezése a felügyelt lemezeken a virtuális gép tulajdonságainak frissítésével
> 1. A `Get-AzRecoveryServicesAsrReplicationProtectedItem` következő parancsmaggal kérheti le a védett elemek lemezének azonosítóját:
> 1. Hozzon létre egy szótár objektumot a `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` parancsmag használatával, amely tartalmazza a lemezes titkosítási készlethez tartozó lemez-azonosító hozzárendelését. Ezeket a lemezes titkosítási csoportokat előre létre kell hoznia a célként megadott régióban.
> 1. Frissítse a virtuális gép tulajdonságait a `Set-AzRecoveryServicesAsrReplicationProtectedItem` parancsmag használatával a **DiskIdToDiskEncryptionSetMap** paraméterben található szótár objektum átadásával.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Az üzemelő példány teszteléséhez futtasson feladatátvételi tesztet egyetlen virtuális géphez. Létrehozhat egy olyan helyreállítási tervet is, amely több virtuális gépet is tartalmaz, és feladatátvételi tesztet futtat a csomaghoz. A feladatátvételi teszt segítségével elkülönített hálózatban próbálhatja ki a feladatátvételi és helyreállítási mechanizmusokat.

1. Kérje le azt a virtuális gépet, amelybe a virtuális gépek feladatátvételt hajtanak végre.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Végezzen feladatátvételi tesztet.

   Egyetlen virtuális gép esetén:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   Helyreállítási tervhez:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

A művelet befejezésének ellenőrzéséhez kövesse a [tevékenység figyelése](#monitor-activity)című témakör lépéseit.

## <a name="run-planned-and-unplanned-failovers"></a>Tervezett és nem tervezett feladatátvételek futtatása

1. Végezzen el egy tervezett feladatátvételt.

   Egyetlen virtuális gép esetén:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Helyreállítási tervhez:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Végezzen nem tervezett feladatátvételt.

   Egyetlen virtuális gép esetén:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Helyreállítási tervhez:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Tevékenység figyelése

A feladatátvételi tevékenység figyeléséhez használja a következő parancsokat. Várjon, amíg a feldolgozás befejeződik a feladatok között.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>Következő lépések

[További](/powershell/module/az.recoveryservices) információ a site Recovery Resource Manager PowerShell-parancsmagokkal.
