---
title: Hyper-V virtuális gép vész-helyreállítás a Azure Site Recovery és a PowerShell használatával
description: Automatizálja a Hyper-V virtuális gépek vész-helyreállítását az Azure-ba az Azure Site Recovery szolgáltatással a PowerShell és a Azure Resource Manager használatával.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 548fa8181c4841d8f57de485c0a4e714b5e9321a
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863910"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Az Azure-ba irányuló vész-helyreállítás beállítása a Hyper-V virtuális gépekhez a PowerShell és a Azure Resource Manager használatával

A [Azure site Recovery](site-recovery-overview.md) az Azure Virtual Machines (VM) és a helyszíni virtuális gépek és a fizikai kiszolgálók replikálásának, feladatátvételének és helyreállításának összehangolása révén járul hozzá az üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégiához.

Ez a cikk a Windows PowerShell és a Azure Resource Manager együttes használatát ismerteti a Hyper-V virtuális gépek Azure-ba történő replikálásához. A cikkben használt példa bemutatja, hogyan replikálhat egy Hyper-V-gazdagépen futó egyetlen virtuális gépet az Azure-ba.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

A Azure PowerShell parancsmagokat biztosít az Azure kezeléséhez a Windows PowerShell használatával. Site Recovery PowerShell-parancsmagok, amelyek a Azure Resource Manager Azure PowerShell érhetők el, segítenek a kiszolgálók védelmében és helyreállításában az Azure-ban.

A cikk használatához nem kell PowerShell-szakértőnek lennie, de meg kell ismernie az alapvető fogalmakat, például a modulokat, a parancsmagokat és a munkameneteket. Olvassa el [a Windows PowerShell használatának első lépéseit](https://technet.microsoft.com/library/hh857337.aspx), és [használja a Azure PowerShellt a Azure Resource Manager használatával](../powershell-azure-resource-manager.md).

> [!NOTE]
> A Microsoft partnerei a Cloud Solution Provider (CSP) programban konfigurálhatják és kezelhetik az ügyfél-kiszolgálók védelmét a megfelelő CSP-előfizetésekkel (bérlői előfizetések).
>
>

## <a name="before-you-start"></a>Előkészületek
Győződjön meg arról, hogy rendelkezik az alábbi előfeltételekkel:

* Egy [Microsoft Azure](https://azure.microsoft.com/) -fiók. Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is. Emellett a [Azure site Recovery Manager díjszabásáról](https://azure.microsoft.com/pricing/details/site-recovery/)is olvashat.
* Azure PowerShell. További információ erről a kiadásról és annak telepítéséről: [install Azure PowerShell](/powershell/azure/install-az-ps).

Emellett a cikkben ismertetett példa a következő előfeltételeket ismerteti:

* Egy vagy több virtuális gépet tartalmazó Hyper-V-gazdagép, amely Windows Server 2012 R2 vagy Microsoft Hyper-V Server 2012 R2 rendszert futtat. A Hyper-V-kiszolgálóknak közvetlenül vagy proxyn keresztül kell csatlakozniuk az internethez.
* A replikálni kívánt virtuális gépeknek meg kell felelniük [ezeknek az előfeltételeknek](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>1\. lépés: Jelentkezzen be az Azure-fiókjába

1. Nyisson meg egy PowerShell-konzolt, és futtassa ezt a parancsot az Azure-fiókba való bejelentkezéshez. A parancsmag egy weboldalt nyit meg a fiók hitelesítő adatainak megadásához: **AzAccount**.
    - Másik lehetőségként a fiók hitelesítő adatait a **kapcsolat-AzAccount** parancsmag paraméterként is hozzáadhatja a **-hitelesítőadat** paraméter használatával.
    - Ha a CSP-partner bérlő nevében dolgozik, adja meg az ügyfelet bérlőként a tenantID vagy a bérlő elsődleges tartománynevének használatával. Például: **kapcsolat-AzAccount-bérlő "fabrikam.com"**
2. Társítsa a fiókhoz használni kívánt előfizetést, mivel egy fiók több előfizetéssel is rendelkezhet:

    `Select-AzSubscription -SubscriptionName $SubscriptionName`

3. A következő parancsokkal ellenőrizze, hogy az előfizetése regisztrálva van-e az Azure-szolgáltatók Recovery Services és Site Recovery használatához:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

4. Ellenőrizze, hogy a parancs kimenetében a **RegistrationState** van-e **regisztrálva**, folytassa a 2. lépéssel. Ha nem, akkor a következő parancsok futtatásával regisztrálja a hiányzó szolgáltatót az előfizetésében:

    `Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Ellenőrizze, hogy a szolgáltatók regisztrálása sikeresen megtörtént-e a következő parancsok használatával:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

## <a name="step-2-set-up-the-vault"></a>2\. lépés: a tároló beállítása

1. Hozzon létre egy Azure Resource Manager erőforráscsoportot, amelyben létre kívánja hozni a tárolót, vagy használjon egy meglévő erőforráscsoportot. Hozzon létre egy új erőforráscsoportot az alábbiak szerint. A $ResourceGroupName változó tartalmazza a létrehozni kívánt erőforráscsoport nevét, és a $Geo változó tartalmazza azt az Azure-régiót, amelyben létre kívánja hozni az erőforráscsoportot (például "Dél-Brazília").

    `New-AzResourceGroup -Name $ResourceGroupName -Location $Geo`

2. Az előfizetéshez tartozó erőforráscsoportok listájának beszerzéséhez futtassa a **Get-AzResourceGroup** parancsmagot.
2. Hozzon létre egy új Azure Recovery Services-tárolót az alábbiak szerint:

        $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    A **Get-AzRecoveryServicesVault** parancsmaggal lekérheti a meglévő tárolók listáját.


## <a name="step-3-set-the-recovery-services-vault-context"></a>3\. lépés: a Recovery Services-tároló környezetének beállítása

Állítsa be a tár környezetét az alábbiak szerint:

`Set-AsrVaultSettings -Vault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>4\. lépés: Hyper-V-hely létrehozása

1. Hozzon létre egy új Hyper-V-helyet a következők szerint:

        $sitename = "MySite"                #Specify site friendly name
        New-AsrFabric -Type HyperVSite -Name $sitename

2. Ez a parancsmag egy Site Recovery feladatot indít el a hely létrehozásához, és egy Site Recovery feladattípust ad vissza. Várjon, amíg a feladatok befejeződik, és ellenőrizze, hogy a művelet sikeresen befejeződött-e.
3. Használja a **Get-AsrJob parancsmagot**a feladatütemezés lekéréséhez, és tekintse meg a feladatok aktuális állapotát.
4. A webhelyhez tartozó regisztrációs kulcs létrehozása és letöltése a következő módon:

    ```
    $SiteIdentifier = Get-AsrFabric -Name $sitename | Select -ExpandProperty SiteIdentifier
    $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
    ```

5. Másolja a letöltött kulcsot a Hyper-V-gazdagépre. A Hyper-V-gazdagépnek a helyre való regisztrálásához szükség van a kulcsra.

## <a name="step-5-install-the-provider-and-agent"></a>5\. lépés: a szolgáltató és az ügynök telepítése

1. Töltse le a szolgáltató legújabb verziójához készült telepítőt a [Microsofttól](https://aka.ms/downloaddra).
2. Futtassa a telepítőt a Hyper-V-gazdagépen.
3. A telepítés végén folytassa a regisztrációs lépéssel.
4. Ha a rendszer kéri, adja meg a letöltött kulcsot, és fejezze be a Hyper-V-gazdagép regisztrációját.
5. A következő lépésekkel ellenőrizze, hogy a Hyper-V-gazdagép regisztrálva van-e a helyen:

        $server =  Get-AsrFabric -Name $siteName | Get-AsrServicesProvider -FriendlyName $server-friendlyname

Ha a Hyper-V Core-kiszolgálót futtatja, töltse le a telepítőfájlt, és kövesse az alábbi lépéseket:
1. Bontsa ki a fájlokat a AzureSiteRecoveryProvider. exe fájlból egy helyi könyvtárba a következő parancs futtatásával: ```AzureSiteRecoveryProvider.exe /x:. /q```
2. A Futtatás ```.\setupdr.exe /i``` találatok naplózása a%Programdata%\ASRLogs\DRASetupWizard.log.

3. Regisztrálja a kiszolgálót a következő parancs futtatásával:

    ```cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"```


## <a name="step-6-create-a-replication-policy"></a>6\. lépés: replikációs házirend létrehozása

Mielőtt elkezdené, vegye figyelembe, hogy a megadott Storage-fióknak ugyanabban az Azure-régióban kell lennie, mint a tárolónak, és engedélyezni kell a Geo-replikációt.

1. Hozzon létre egy replikációs házirendet a következőképpen:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Ellenőrizze a visszaadott feladatot, hogy a replikációs házirend létrehozása sikeres legyen.

3. Kérje le a helynek megfelelő védelmi tárolót a következő módon:

        $protectionContainer = Get-AsrProtectionContainer
3. Társítsa a védelmi tárolót a replikációs házirendhez a következőképpen:

        $Policy = Get-AsrPolicy -FriendlyName $PolicyName
        $associationJob  = New-AsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
4. Várjon, amíg a társítási feladatok sikeresen befejeződik.

5. A védelmi tároló leképezésének beolvasása.

        $ProtectionContainerMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $protectionContainer

## <a name="step-7-enable-vm-protection"></a>7\. lépés: a virtuális gépek védelmének engedélyezése

1. Kérje le a védelemmel ellátni kívánt virtuális géphez tartozó védhető elem beolvasását az alábbiak szerint:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $ProtectableItem = Get-AsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. A virtuális gép megóvása. Ha a védeni kívánt virtuális gépnek több lemeze is van, akkor az operációs rendszer lemezét a *OSDiskName* paraméter használatával kell megadni.

        $OSType = "Windows"                                 # "Windows" or "Linux"
        $DRjob = New-AsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID

3. Várja meg, hogy a virtuális gépek a kezdeti replikálás után elérjék a védett állapotot. Ez eltarthat egy ideig, attól függően, hogy milyen tényezőket kell replikálni, valamint az elérhető upstream sávszélességet az Azure-ba. Ha védett állapot van érvényben, a rendszer a következő módon frissíti a feladatok állapotát és a StateDescription:

        PS C:\> $DRjob = Get-AsrJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Frissítse a helyreállítási tulajdonságokat (például a virtuálisgép-szerepkör méretét), valamint azt az Azure-hálózatot, amelyhez a virtuális hálózati ADAPTERt a feladatátvételt követően csatlakoztatni szeretné.

        PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AsrReplicationProtectedItem --InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AsrJob -Job $UpdateJob

        PS C:\> $UpdateJob| select -ExpandProperty state
        Get-AsrJob -Job $job | select -ExpandProperty state

        Succeeded

> [!NOTE]
> Ha a CMK-kompatibilis felügyelt lemezeket az Azure-ban szeretné replikálni, hajtsa végre a következő lépéseket az az PowerShell 3.3.0-től kezdődően:
>
> 1. Feladatátvétel engedélyezése a felügyelt lemezeken a virtuális gép tulajdonságainak frissítésével
> 2. A Get-AsrReplicationProtectedItem parancsmag használatával beolvashatja a védett elemek lemezének AZONOSÍTÓját
> 3. Hozzon létre egy szótár objektumot a New-Object "System. Collections. Generic. Dictionary" "2 [System. string, System. string]" parancsmag használatával, hogy tartalmazza a lemez titkosítási készletének leképezését. Ezeket a lemezes titkosítási csoportokat előre létre kell hoznia a célként megadott régióban.
> 4. Frissítse a virtuális gép tulajdonságait a set-AsrReplicationProtectedItem parancsmaggal a DiskIdToDiskEncryptionSetMap paraméterben található szótár objektum átadásával.

## <a name="step-8-run-a-test-failover"></a>8\. lépés: feladatátvételi teszt futtatása
1. Futtasson egy feladatátvételi tesztet a következőképpen:

        $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        $TFjob =Start-AsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Győződjön meg arról, hogy a teszt virtuális gép az Azure-ban lett létrehozva. A teszt feladatátvételi feladatot a rendszer felfüggeszti a tesztelési virtuális gép Azure-ban való létrehozása után.
3. A feladatátvételi teszt elvégzéséhez futtassa a következő parancsot:

        $TFjob = Start-AsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"

## <a name="next-steps"></a>Következő lépések
[További](https://docs.microsoft.com/powershell/module/az.recoveryservices) információ a Azure site Recovery Azure Resource Manager PowerShell-parancsmagokkal.
