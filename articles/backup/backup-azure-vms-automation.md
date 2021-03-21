---
title: Azure-beli virtuális gépek biztonsági mentése és helyreállítása a PowerShell-lel
description: Az Azure-beli virtuális gépek biztonsági mentését és helyreállítását ismerteti a PowerShell-lel Azure Backup használatával
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: f59c18aecf577bc7f7d0b1360dd36504305af893
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633189"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Azure-beli virtuális gépek biztonsági mentése és visszaállítása a PowerShell-lel

Ez a cikk az Azure-beli virtuális gépek biztonsági mentését és visszaállítását ismerteti egy [Azure Backup](backup-overview.md) Recovery Services-tárolóban PowerShell-parancsmagok használatával.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Hozzon létre egy Recovery Services-tárolót, és állítsa be a tár környezetét.
> * Biztonsági mentési szabályzat meghatározása
> * A biztonsági mentési szabályzat alkalmazása több virtuális gép védelme érdekében
> * Egy igény szerinti biztonsági mentési feladatot indít el a védett virtuális gépek számára, mielőtt biztonsági másolatot készít (vagy véd) egy virtuális gépet, el kell végeznie az [előfeltételeket](backup-azure-arm-vms-prepare.md) , hogy előkészítse a környezetet a virtuális gépek védelmére.

## <a name="before-you-start"></a>Előkészületek

* [További](backup-azure-recovery-services-vault-overview.md) információ a Recovery Services-tárolókkal kapcsolatban.
* [Tekintse át](backup-architecture.md#architecture-built-in-azure-vm-backup) az Azure-beli virtuális gépek biztonsági mentésének architektúráját, [Ismerkedjen meg](backup-azure-vms-introduction.md) a biztonsági mentési folyamattal, és [tekintse át](backup-support-matrix-iaas.md) a támogatás, a korlátozások és az Előfeltételek
* Tekintse át Recovery Services PowerShell-objektumának hierarchiáját.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services objektum-hierarchia

Az objektum-hierarchiát a következő ábra összegzi.

![Recovery Services objektum-hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Tekintse át az az **. recoveryservices szolgáltatónál** [parancsmag](/powershell/module/az.recoveryservices/) -referenciát az Azure Library-ben.

## <a name="set-up-and-register"></a>Beállítás és regisztrálás

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A kezdéshez:

1. [A PowerShell legújabb verziójának letöltése](/powershell/azure/install-az-ps)

2. Keresse meg a rendelkezésre álló Azure Backup PowerShell-parancsmagokat a következő parancs beírásával:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Megjelenik a Azure Backup, Azure Site Recovery és a Recovery Services tároló aliasai és parancsmagjai. Az alábbi ábrán egy példa látható, amit látni fog. Nem a parancsmagok teljes listája.

    ![Recovery Services listája](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Jelentkezzen be az Azure-fiókjába a **AzAccount** használatával. Ez a parancsmag egy weblapot hoz létre a fiók hitelesítő adatainak megadásához:

    * Másik lehetőségként a fiók hitelesítő adatait a **kapcsolat-AzAccount** parancsmag paraméterként is hozzáadhatja a **-hitelesítőadat** paraméter használatával.
    * Ha Ön egy bérlő nevében dolgozó CSP-partner, adja meg az ügyfelet bérlőként a tenantID vagy a bérlő elsődleges tartománynevének használatával. Például: **kapcsolat-AzAccount-bérlő "fabrikam.com"**

4. Társítsa a fiókhoz használni kívánt előfizetést, mivel egy fiók több előfizetéssel is rendelkezhet:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Ha első alkalommal használja a Azure Backup, a **[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** parancsmaggal regisztrálnia kell az Azure Recovery szolgáltatást az előfizetésében.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. A következő parancsokkal ellenőrizheti, hogy a szolgáltatók sikeresen regisztráltak-e:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    A parancs kimenetében a **RegistrationState** a **regisztrált** értékre kell váltania. Ha nem, egyszerűen futtassa újra a **[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** parancsmagot.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A következő lépések végigvezetik a Recovery Services-tároló létrehozásának lépésein. Egy Recovery Services-tár nem azonos a Backup-tárolóval.

1. A Recovery Services-tároló Resource Manager-erőforrás, ezért egy erőforráscsoporthoz kell helyeznie. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy erőforráscsoportot a **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** parancsmaggal. Erőforráscsoport létrehozásakor adja meg az erőforráscsoport nevét és helyét.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. A [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) parancsmag használatával hozza létre a Recovery Services-tárolót. Ügyeljen arra, hogy ugyanazt a helyet határozza meg a tárolóhoz, mint amelyet az erőforráscsoport használ.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Adja meg a használandó tárolási redundancia típusát. A [helyileg redundáns tárolást (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), a [geo-redundáns tárolást (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)vagy a [Zone-redundáns tárolást (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage)is használhatja. Az alábbi példa a **-BackupStorageRedundancy** beállítást mutatja be a *testvault* beállításnál a **GeoRedundant** értékre.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ezért célszerű a Backup Recovery Services Vault-objektumot tárolni egy változóban.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Az előfizetésben található tárolók megtekintése

Az előfizetés összes tárolójának megtekintéséhez használja a [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

A kimenet a következő példához hasonló, figyelje meg, hogy a társított ResourceGroupName és hely van megadva.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Azure-beli virtuális gépek biztonsági mentése

Használjon Recovery Services tárolót a virtuális gépek biztonságának biztosítása érdekében. A védelem alkalmazása előtt állítsa be a tár környezetét (a tárolóban védett adattípust), és ellenőrizze a védelmi házirendet. A védelmi házirend a biztonsági mentési feladatok futtatása és az egyes biztonsági mentési Pillanatképek megtartásának időpontja.

### <a name="set-vault-context"></a>Tár környezetének beállítása

A virtuális gépek védelmének engedélyezése előtt a [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) paranccsal állítsa be a tár környezetét. A tárolási környezet beállítását követően az minden további parancsmagra érvényes lesz. Az alábbi példa a tároló környezetét állítja be a *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>A tár AZONOSÍTÓjának beolvasása

A tár környezeti beállítását a Azure PowerShell irányelvek alapján tervezzük. Ehelyett tárolhatja vagy beolvashatja a tár AZONOSÍTÓját, és átadhatja azokat a megfelelő parancsoknak. Tehát ha még nem állította be a tár környezetét, vagy egy adott tárolóhoz szeretne futtatni egy parancsot, adja át a tároló AZONOSÍTÓját "-vaultID" értékként az összes vonatkozó parancsra az alábbiak szerint:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Vagy

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Tárolási replikálási beállítások módosítása

A [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) parancs használata a tároló tárolási replikációs konfigurációjának beállításához a LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> A tárterület-redundancia csak akkor módosítható, ha nincsenek védett biztonsági mentési elemek a tárolóban.

### <a name="create-a-protection-policy"></a>Védelmi szabályzat létrehozása

Helyreállítási tár létrehozásakor a tár alapértelmezett védelmi és megőrzési szabályzatokkal rendelkezik. Az alapértelmezett védelmi szabályzat naponta egyszer, adott időben aktivál egy biztonsági mentési feladatot. Az alapértelmezett megőrzési szabályzat 30 napig őrzi meg a napi helyreállítási pontokat. Az alapértelmezett szabályzat használatával gyorsan védetté teheti a virtuális gépet, és szerkesztheti a szabályzatot később, különböző részletekkel.

A **[Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** használatával megtekintheti a tárolóban elérhető védelmi szabályzatokat. Ezzel a parancsmaggal kérhet le egy adott szabályzatot, vagy megtekintheti a munkaterhelés-típushoz társított házirendeket. Az alábbi példa lekéri a számítási feladatok típusát, a AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

A kimenet a következő példához hasonló:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> A PowerShell BackupTime mezőjének időzónája UTC. Ha azonban a biztonsági mentés ideje megjelenik a Azure Portalban, az idő a helyi időzónára van igazítva.
>
>

A biztonsági mentési védelmi szabályzat legalább egy adatmegőrzési szabályzattal van társítva. Egy adatmegőrzési házirend határozza meg, hogy mennyi ideig tart a helyreállítási pont a törlése előtt.

* A [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) használatával megtekintheti az alapértelmezett adatmegőrzési szabályt.
* Hasonlóképpen használhatja a [Get-AzRecoveryServicesBackupSchedulePolicyObject-](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) t az alapértelmezett ütemezett házirend beszerzéséhez.
* A [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) parancsmag egy PowerShell-objektumot hoz létre, amely a biztonsági mentési szabályzat adatait tárolja.
* Az ütemezett és adatmegőrzési házirend objektumok a New-AzRecoveryServicesBackupProtectionPolicy parancsmag bemenetként használatosak.

Alapértelmezés szerint a rendszer a kezdési időt határozza meg az ütemezett házirend objektumban. A következő példa használatával módosíthatja a kezdési időt a kívánt kezdési időpontra. A kívánt kezdési időpontnak UTC-ként is kell lennie. Az alábbi példa azt feltételezi, hogy a várt kezdési idő a napi biztonsági mentések 01:00-as UTC-értéke.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> A kezdési időt csak 30 percenként kell megadnia. A fenti példában csak "01:00:00" vagy "02:30:00" lehet. A kezdési időpont nem lehet "01:15:00"

A következő példa az ütemezett házirendet és a változókban tárolt adatmegőrzési szabályzatot tárolja. A példa ezeket a változókat használja a *NewPolicy* védelmi szabályzat létrehozásakor használt paraméterek definiálásához.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

A kimenet a következő példához hasonló:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Védelem engedélyezése

A védelmi házirend meghatározása után továbbra is engedélyeznie kell egy elem házirendjét. A védelem engedélyezéséhez használja az [enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) . A védelem engedélyezéséhez két objektum szükséges – az elem és a házirend. Miután a házirend társítva lett a tárolóhoz, a biztonsági mentési munkafolyamat a házirend-ütemtervben meghatározott időpontban aktiválódik.

> [!IMPORTANT]
> Míg a PowerShell használatával egyszerre több virtuális gép biztonsági mentését is lehetővé teszi, győződjön meg arról, hogy egyetlen házirendhez nincs több, mint 100 virtuális gép társítva. Ez az [ajánlott eljárás](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy). Jelenleg a PowerShell-ügyfél nem blokkolja explicit módon, ha több mint 100 virtuális gép van, de az ellenőrzési terv a jövőben is felvehető.

Az alábbi példák lehetővé teszik a V2VM, az NewPolicy-t használó elem védelmét. A példák attól függően különböznek, hogy a virtuális gép titkosítva van-e, és milyen típusú titkosítást tartalmaz.

A védelem engedélyezése a **nem titkosított Resource Manager-alapú virtuális gépeken**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Ha engedélyezni szeretné a védelem használatát a titkosított virtuális gépeken (a BEK és a KEK használatával titkosítva), meg kell adnia a Azure Backup szolgáltatás számára a kulcsok és titkos kulcsok olvasását a kulcstartóból.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Ha engedélyezni szeretné a védelmet a **titkosított virtuális gépeken (csak a csak BEK használatával titkosítva)**, meg kell adnia a Azure Backup szolgáltatás számára a titkos kulcsok olvasására vonatkozó engedélyt.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Ha a Azure Government-felhőt használja, használja a `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` **ServicePrincipalName** paraméter értékét a [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmagban.
>

Ha szelektíven szeretne biztonsági mentést készíteni néhány lemezről, és nem szeretné kizárni másokat az [ilyen forgatókönyvekben](selective-disk-backup-restore.md#scenarios)említettek szerint, akkor a védelmet és a biztonsági mentést csak a megfelelő lemezekről tekintheti [meg.](selective-disk-backup-restore.md#enable-backup-with-powershell)

## <a name="monitoring-a-backup-job"></a>Biztonsági mentési feladatok figyelése

A hosszú ideig futó műveleteket, például a biztonsági mentési feladatokat a Azure Portal használata nélkül is figyelheti. A folyamatban lévő feladatok állapotának lekéréséhez használja a [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) parancsmagot. Ez a parancsmag egy adott tár biztonsági mentési feladatait olvassa be, és azt a tároló környezetében adja meg. A következő példa lekérdezi a folyamatban lévő feladatok állapotát tömbként, és a $joblist változóban tárolja az állapotot.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

A kimenet a következő példához hasonló:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

A feladatok befejezésének lekérdezése helyett – ami szükségtelen további kódokat használ – a [WAIT-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmagot használja. Ez a parancsmag addig szünetelteti a végrehajtást, amíg a feladat be nem fejeződik, vagy elérte a megadott időtúllépési értéket.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Azure-beli virtuális gépek biztonsági mentésének kezelése

### <a name="modify-a-protection-policy"></a>Védelmi szabályzat módosítása

A védelmi szabályzat módosításához a [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) használatával módosítsa a SchedulePolicy vagy a RetentionPolicy objektumokat.

#### <a name="modifying-scheduled-time"></a>Ütemezett idő módosítása

Védelmi szabályzat létrehozásakor a rendszer alapértelmezés szerint egy kezdési időpontot rendel hozzá. Az alábbi példák bemutatják, hogyan módosíthatja a védelmi szabályzatok kezdési idejét.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Megőrzés módosítása

A következő példa a helyreállítási pont megőrzését 365 napra módosítja.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Az azonnali visszaállítás pillanatkép-megőrzésének konfigurálása

> [!NOTE]
> A Azure PowerShell verzió 1.6.0 kezdve az egyik a PowerShell használatával frissítheti az azonnali visszaállítás pillanatképének megőrzési időtartamát a szabályzatban

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

Az alapértelmezett érték 2 lesz. Az értéket beállíthatja legalább 1 értékkel és legfeljebb 5-öt. A heti biztonsági mentési házirendek esetében az időszak értéke 5, és nem módosítható.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Azure Backup erőforráscsoport létrehozása a pillanatképek megőrzése során

> [!NOTE]
> Azure PowerShell 3.7.0-verziótól kezdődően az egyik létrehozható és szerkeszthető az azonnali Pillanatképek tárolására létrehozott erőforráscsoport.

Ha többet szeretne megtudni az erőforráscsoport-létrehozási szabályokról és az egyéb kapcsolódó részletekről, tekintse meg a Virtual Machines dokumentációjának [Azure Backup erőforráscsoportot](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines) .

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="exclude-disks-for-a-protected-vm"></a>Lemezek kizárása egy védett virtuális géphez

Az Azure virtuális gépek biztonsági mentése lehetővé teszi, hogy szelektíven kizárjon vagy tartalmazzon olyan lemezeket, amelyek hasznosak lehetnek [ezekben a forgatókönyvekben](selective-disk-backup-restore.md#scenarios). Ha a virtuális gépet már az Azure-beli virtuális gép biztonsági mentése védi, és ha az összes lemezről biztonsági mentés készül, akkor a védelem módosításával szelektíven belefoglalhatja vagy kizárhatja a lemezeket az [itt](selective-disk-backup-restore.md#modify-protection-for-already-backed-up-vms-with-powershell)említettek szerint.

### <a name="trigger-a-backup"></a>Biztonsági mentés indítása

Biztonsági mentési feladatok elindításához használja a [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) . Ha ez a kezdeti biztonsági mentés, a teljes biztonsági mentés. A későbbi biztonsági másolatok növekményes másolatot készítenek. A következő példa a virtuális gép biztonsági mentését 60 napig őrzi meg.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

A kimenet a következő példához hasonló:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> A PowerShell kezdési és befejezési mezőinek időzónája UTC. Ha azonban az idő megjelenik a Azure Portalban, az idő a helyi időzónára van igazítva.
>
>

### <a name="change-policy-for-backup-items"></a>Biztonsági mentési elemek szabályzatának módosítása

Módosíthatja a meglévő szabályzatot, vagy megváltoztathatja a biztonsági másolatban szereplő elem házirendjét a Házirend1 és a Policy2 között. Ha egy biztonsági másolati elemhez szeretne házirendeket váltani, olvassa be a vonatkozó házirendet, és készítsen biztonsági másolatot az elemről, és használja az [enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) parancsot a biztonsági mentési elemmel paraméterként.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

A parancs megvárja, amíg a biztonsági mentés be nem fejeződik, és a következő kimenetet adja vissza.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>A védelem kikapcsolása

#### <a name="retain-data"></a>Adatok megőrzése

Ha le szeretné állítani a védelmet, használhatja a [disable-AzRecoveryServicesBackupProtection PowerShell-](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) parancsmagot. Ezzel leállítja az ütemezett biztonsági mentéseket, de a biztonsági mentés egészen addig, amíg a rendszer örökre megőrzi az adatokat.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

Ha teljesen el szeretné távolítani a tárolt biztonsági mentési adattárakat, adja hozzá a "-RemoveRecoveryPoints" jelzőt, vagy váltson a ["letiltás" védelmi parancsra](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Azure-beli virtuális gép visszaállítása

Fontos különbség a virtuális gép visszaállítása a Azure Portal használatával és a virtuális gép visszaállítása a PowerShell használatával. A PowerShell-lel a visszaállítási művelet akkor fejeződik be, ha a helyreállítási pontról a lemezek és a konfigurációs adatok jönnek létre. A visszaállítási művelet nem hozza létre a virtuális gépet. A virtuális gép lemezről történő létrehozásával kapcsolatban tekintse meg a virtuális gép [létrehozása visszaállított lemezekről](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)című szakaszt. Ha nem szeretné visszaállítani a teljes virtuális gépet, de szeretné visszaállítani vagy helyreállítani néhány fájlt egy Azure-beli virtuális gép biztonsági másolatából, tekintse meg a [Fájl-helyreállítási szakaszt](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> A visszaállítási művelet nem hozza létre a virtuális gépet.
>
>

Az alábbi ábrán a RecoveryServicesVault található objektum-hierarchia látható a BackupRecoveryPoint.

![BackupContainer mutató Recovery Services objektum-hierarchia](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

A biztonsági másolatok állapotának visszaállításához azonosítsa a biztonsági másolatban szereplő elemeket, valamint az időponthoz tartozó adatelemet tartalmazó helyreállítási pontot. A [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) használatával állítsa vissza az adatait a tárból a fiókjába.

Az Azure-beli virtuális gépek visszaállításának alapvető lépései a következők:

* Válassza ki a virtuális gépet.
* Válasszon egy helyreállítási pontot.
* Állítsa vissza a lemezeket.
* Hozza létre a virtuális gépet a tárolt lemezekről.

### <a name="select-the-vm-when-restoring-files"></a>Válassza ki a virtuális gépet (fájlok visszaállításakor)

A megfelelő biztonsági mentési elemet azonosító PowerShell-objektum beszerzéséhez indítsa el a tárolóban található tárolóból, és az objektum-hierarchiában dolgozzon végig. A virtuális gépet jelölő tároló kiválasztásához használja a [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) parancsmagot és a pipe-ot a [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) parancsmaghoz.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>Helyreállítási pont kiválasztása (fájlok visszaállításakor)

A [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) parancsmag használatával listázhatja a biztonsági mentési elemek összes helyreállítási pontját. Ezután válassza ki a visszaállítani kívánt helyreállítási pontot. Ha nem tudja biztosan, hogy melyik helyreállítási pontot szeretné használni, érdemes kiválasztani a legutóbbi RecoveryPointType = AppConsistent pontot a listában.

A következő szkriptben a **$RP** változó a kiválasztott biztonsági mentési elemhez tartozó helyreállítási pontok tömbje az elmúlt hét napban. A tömb a 0. indexben szereplő legutóbbi helyreállítási ponttal fordított sorrendben van rendezve. A helyreállítási pont kiválasztásához használja a PowerShell-tömb szabványos indexelését. A példában a $rp [0] kiválasztja a legutóbbi helyreállítási pontot.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

A kimenet a következő példához hasonló:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>A lemezek visszaállítása

A [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) parancsmag használatával állítsa vissza a biztonsági másolati elemek beállításait és konfigurációját egy helyreállítási pontra. Miután azonosította a helyreállítási pontot, használja a **-RecoveryPoint** paraméter értékeként. A fenti mintában **$RP [0]** volt a használni kívánt helyreállítási pont. A következő mintakód esetében **$RP [0]** a lemez visszaállításához használandó helyreállítási pont.

A lemezek és a konfigurációs adatok visszaállítása:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Felügyelt lemezek visszaállítása

> [!NOTE]
> Ha a háttérbe állított virtuális gép felügyelt lemezekkel rendelkezik, és felügyelt lemezként szeretné visszaállítani őket, akkor a Azure PowerShell RM modul v 6.7.0 funkcióját is bevezette. től.
>
>

Adjon meg egy további paramétert, amely meghatározza, hogy a rendszer mely **TargetResourceGroupName** adja vissza a felügyelt lemezeket.

> [!IMPORTANT]
> Erősen ajánlott a **TargetResourceGroupName** paraméter használata a felügyelt lemezek visszaállítására, mivel jelentős teljesítménybeli javítást eredményez. Ha ez a paraméter nincs megadva, akkor nem használhatja ki az azonnali visszaállítás funkció előnyeit, és a visszaállítási művelet lassabban lesz az összehasonlításban. Ha a felügyelt lemezeket nem felügyelt lemezként szeretné visszaállítani, ne adja meg ezt a paramétert, és törölje a kívánt célt a paraméter megadásával `-RestoreAsUnmanagedDisks` . A `-RestoreAsUnmanagedDisks` paraméter Azure PowerShell 3.7.0 és újabb verziókon érhető el. A későbbi verziókban a megfelelő visszaállítási élmény érdekében kötelező megadni a paraméterek egyikét.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

A rendszer visszaállítja a fájlVMConfig.JSa Storage-fiókba, és a felügyelt lemezeket a rendszer visszaállítja a megadott cél RG- **ra** .

A kimenet a következő példához hasonló:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

A [WAIT-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmag használatával várjon, amíg a visszaállítási feladatok befejeződik.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

A visszaállítási feladatok befejezését követően a [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmaggal kérheti le a visszaállítási művelet részleteit. A JobDetails tulajdonság a virtuális gép újraépítéséhez szükséges adatokat tartalmaz.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

#### <a name="restore-selective-disks"></a>Szelektív lemezek visszaállítása

A felhasználók a teljes biztonsági mentés helyett több lemezt is Visszaállíthatnak. Adja meg a szükséges lemezes logikai egységeket paraméterként, hogy a teljes készlet helyett csak az [itt](selective-disk-backup-restore.md#restore-selective-disks-with-powershell)leírt módon állítsa vissza őket.

> [!IMPORTANT]
> Az egyiknek a lemezek szelektív biztonsági mentését kell végeznie a lemezek szelektív visszaállításához. További részletek [itt](selective-disk-backup-restore.md#selective-disk-restore)találhatók.

A lemezek visszaállítása után lépjen a következő szakaszra a virtuális gép létrehozásához.

#### <a name="restore-disks-to-a-secondary-region"></a>Lemezek visszaállítása másodlagos régióba

Ha a régiók közötti visszaállítás engedélyezve van azon a tárolón, amellyel a virtuális gépeket védett, a biztonsági mentési adatait a rendszer a másodlagos régióba replikálja. A biztonsági mentési adatok használatával visszaállítást végezhet. A következő lépések végrehajtásával aktiválja a visszaállítást a másodlagos régióban:

1. [A tároló azonosítójának beolvasása](#fetch-the-vault-id) , amellyel a virtuális gépek védve vannak.
1. Válassza ki a [helyreállítani kívánt biztonsági mentési elemet](#select-the-vm-when-restoring-files).
1. Válassza ki a megfelelő helyreállítási pontot abban a másodlagos régióban, amelyet a visszaállítás végrehajtásához használni kíván.

    A lépés elvégzéséhez futtassa a következő parancsot:

    ```powershell
    $rp=Get-AzRecoveryServicesBackupRecoveryPoint -UseSecondaryRegion -Item $backupitem -VaultId $targetVault.ID
    $rp=$rp[0]
    ```

1. Futtassa a [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) parancsmagot a `-RestoreToSecondaryRegion` paraméterrel, hogy aktiválja a visszaállítást a másodlagos régióban.

    A lépés elvégzéséhez futtassa a következő parancsot:

    ```powershell
    $restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -VaultLocation $targetVault.Location -RestoreToSecondaryRegion -RestoreOnlyOSDisk
    ```

    A kimenet az alábbi példához hasonló lesz:

    ```output
    WorkloadName     Operation             Status              StartTime                 EndTime          JobID
    ------------     ---------             ------              ---------                 -------          ----------
    V2VM             CrossRegionRestore   InProgress           4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
    ```

1. Futtassa a [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) parancsmagot a (z `-UseSecondaryRegion` ) paraméterrel a visszaállítási feladatok figyeléséhez.

    A lépés elvégzéséhez futtassa a következő parancsot:

    ```powershell
    Get-AzRecoveryServicesBackupJob -From (Get-Date).AddDays(-7).ToUniversalTime() -To (Get-Date).ToUniversalTime() -UseSecondaryRegion -VaultId $targetVault.ID
    ```

    A kimenet az alábbi példához hasonló lesz:

    ```output
    WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
    ------------     ---------            ------               ---------                 -------                   -----
    V2VM             CrossRegionRestore   InProgress           2/8/2021 4:24:57 PM                                 2d071b07-8f7c-4368-bc39-98c7fb2983f7
    ```

## <a name="replace-disks-in-azure-vm"></a>Lemezek cseréje az Azure-beli virtuális gépen

A lemezek és a konfigurációs adatok cseréjéhez hajtsa végre a következő lépéseket:

* 1. lépés: [a lemezek visszaállítása](backup-azure-vms-automation.md#restore-the-disks)
* 2. lépés: [az adatlemez leválasztása a PowerShell használatával](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* 3. lépés: [adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell-](../virtual-machines/windows/attach-disk-ps.md) lel

## <a name="create-a-vm-from-restored-disks"></a>Virtuális gép létrehozása visszaállított lemezekről

A lemezek visszaállítása után a következő lépésekkel hozza létre és konfigurálja a virtuális gépet a lemezről.

> [!NOTE]
>
> 1. A AzureAz modul 3.0.0 vagy újabb verziója szükséges. <br>
> 2. Ahhoz, hogy titkosított virtuális gépeket hozzon létre a visszaállított lemezekről, az Azure-szerepkörnek engedéllyel kell rendelkeznie a művelet elvégzéséhez, Microsoft. kulcstartó/tárolók/ **üzembe helyezés/művelet**. Ha a szerepkör nem rendelkezik ezzel az engedéllyel, hozzon létre egy egyéni szerepkört ehhez a művelethez. További információ: [Egyéni Azure-szerepkörök](../role-based-access-control/custom-roles.md). <br>
> 3. A lemezek visszaállítása után már beszerezheti a központi telepítési sablont, amelyet közvetlenül használhat új virtuális gép létrehozásához. Nem kell más PowerShell-parancsmagokkal felügyelt/nem felügyelt virtuális gépeket létrehozni, amelyek titkosított/nem titkosítottak.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Virtuális gép létrehozása a telepítési sablonnal

Az eredő feladatok részletei megadják a sablon URI-JÁT, amely lekérdezhető és telepíthető.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

A sablon nem érhető el közvetlenül, mert az ügyfél Storage-fiókja és a megadott tároló alatt van. Ehhez a sablonhoz a teljes URL-címet (valamint egy ideiglenes SAS-tokent) kell elérni.

1. Először bontsa ki a sablon nevét a templateBlobURI. A formátum alább látható. A PowerShell felosztási műveletével kibonthatja a sablon utolsó nevét ebből az URL-címről.

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. Ezután a teljes URL-cím az [itt](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment)leírtak alapján hozható létre.

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. A sablon üzembe helyezésével hozzon létre egy új virtuális gépet az [itt](../azure-resource-manager/templates/deploy-powershell.md)leírtak szerint.

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI
    ```

### <a name="create-a-vm-using-the-config-file"></a>Virtuális gép létrehozása a konfigurációs fájllal

A következő szakasz azokat a lépéseket sorolja fel, amelyek szükségesek a virtuális gépek "VMConfig" fájl használatával történő létrehozásához.

> [!NOTE]
> Javasoljuk, hogy a fent ismertetett központi telepítési sablon használatával hozzon létre egy virtuális gépet. Ez a szakasz (1-6-es pont) hamarosan elavulttá válik.

1. A visszaállított lemez tulajdonságainak lekérdezése a feladatok részleteihez.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Állítsa be az Azure Storage-környezetet, és állítsa vissza a JSON konfigurációs fájlt.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. A virtuális gép konfigurációjának létrehozásához használja a JSON konfigurációs fájlt.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Csatlakoztassa az operációsrendszer-lemezt és az adatlemezeket. Ez a lépés példákat tartalmaz a különböző felügyelt és titkosított virtuálisgép-konfigurációkra. Használja a virtuális gép konfigurációjának megfelelő példát.

    * **Nem felügyelt és nem titkosított virtuális gépek** – a következő mintát használja a nem felügyelt, nem titkosított virtuális gépekhez.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
            $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Nem felügyelt és titkosított virtuális gépek az Azure ad-vel (csak BEK esetén)** – nem felügyelt, titkosított virtuális gépekhez az Azure ad-vel (csak a BEK használatával), a lemezek csatlakoztatása előtt vissza kell állítania a titkos kulcsot a kulcstartóhoz. További tudnivalókért tekintse meg a [titkosított virtuális gép visszaállítása Azure Backup helyreállítási pontról](backup-azure-restore-key-secret.md)című témakört. Az alábbi minta azt mutatja be, hogyan lehet az operációs rendszer és az adatlemezek csatlakoztatását a titkosított virtuális gépekhez. Az operációsrendszer-lemez beállításakor ügyeljen rá, hogy megemlíti a megfelelő operációsrendszer-típust.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Nem felügyelt és titkosított virtuális gépek az Azure ad-vel (BEK és KEK)** – nem felügyelt, titkosított virtuális gépekhez az Azure ad-vel (a BEK és KEK használatával titkosított), a lemezek csatlakoztatása előtt állítsa vissza a kulcsot és a titkos kulcsot a kulcstartóhoz. További információ: [titkosított virtuális gép visszaállítása Azure Backup helyreállítási pontról](backup-azure-restore-key-secret.md). Az alábbi minta azt mutatja be, hogyan lehet az operációs rendszer és az adatlemezek csatlakoztatását a titkosított virtuális gépekhez.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Nem felügyelt és titkosított virtuális gépek az Azure ad nélkül (csak BEK esetén)** – nem felügyelt, az Azure ad-t nem használó, titkosított virtuális gépek esetében (csak BEK használatával), ha a forrás kulcstartója **/titkos kulcsa nem érhető el** a kulcsok a Key vaultba való visszaállításához, a [nem titkosított virtuális gépek Azure Backup helyreállítási pontról történő visszaállításának](backup-azure-restore-key-secret.md)eljárása alapján. Ezután hajtsa végre a következő parancsfájlokat a visszaállított operációsrendszer-blob titkosítási adatainak beállításához (ez a lépés nem szükséges az adatblobok esetében). A $dekurl a visszaállított kulcstartóból hívható le.

    A következő szkriptet csak akkor kell végrehajtani, ha a forrás kulcstartó/titkos kulcs nem érhető el.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Ha a **titkok elérhetők** , és a titkosítási adatok is be vannak állítva az operációs rendszer blobján, csatolja a lemezeket az alábbi parancsfájl használatával.

    Ha a forrás-és a titkos kulcs már elérhető, akkor a fenti szkriptet nem kell végrehajtani.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Nem felügyelt és titkosított virtuális gépek az Azure ad nélkül (BEK és KEK)** – nem felügyelt, titkosított virtuális gépekhez az Azure ad nélkül (a BEK & KEK használatával titkosított) – Ha a forrás kulcstartó **/kulcs/titok nem érhető el** , állítsa vissza a kulcsot és a titkos kulcsokat a key vaultba egy [Azure Backup helyreállítási pontról](backup-azure-restore-key-secret.md). Ezután hajtsa végre a következő parancsfájlokat a visszaállított operációsrendszer-blob titkosítási adatainak beállításához (ez a lépés nem szükséges az adatblobok esetében). A $dekurl és $kekurl a visszaállított kulcstartóból hívható le.

    Az alábbi szkriptet csak akkor kell végrehajtani, ha a forrás kulcstartó/kulcs/titok nem érhető el.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Ha a **kulcs/titkos kulcsok elérhetők** , és a titkosítási adatok az operációs rendszer blobján vannak beállítva, csatolja a lemezeket az alább megadott parancsfájllal.

    Ha a forrás kulcstartó/kulcs/titkos kódok elérhetők, akkor a fenti szkriptet nem kell végrehajtani.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Felügyelt és nem titkosított virtuális gépek** – felügyelt, nem titkosított virtuális gépekhez csatolja a visszaállított felügyelt lemezeket. Részletes információk: [adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md).

    * **Felügyelt és titkosított virtuális gépek az Azure ad-vel (csak BEK)** – felügyelt titkosított virtuális gépekhez az Azure ad-vel (csak a BEK használatával), csatolja a visszaállított felügyelt lemezeket. Részletes információk: [adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md).

    * **Felügyelt és titkosított virtuális gépek az Azure ad-vel (BEK és KEK)** – felügyelt titkosított virtuális gépekhez az Azure ad-vel (a BEK és a KEK használatával titkosítva) csatlakoztassa a visszaállított felügyelt lemezeket. Részletes információk: [adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md).

    * **Felügyelt és titkosított virtuális gépek az Azure ad nélkül (csak BEK)** – felügyelt, titkosított virtuális gépek Azure ad nélkül (csak BEK-vel titkosítva), ha a forrás kulcstartója **/titkos kulcsa nem érhető el** a Key Vault számára a [nem titkosított virtuális gépek visszaállítása Azure Backup helyreállítási pontról](backup-azure-restore-key-secret.md)című eljárásban leírtak szerint. Ezután hajtsa végre a következő parancsfájlokat a visszaállított operációsrendszer-lemez titkosítási adatainak beállításához (ez a lépés nem szükséges az adatlemezek esetében). A $dekurl a visszaállított kulcstartóból hívható le.

    Az alábbi szkriptet csak akkor kell végrehajtani, ha a forrás kulcstartó/titkos kulcs nem érhető el.  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Ha a titkok elérhetők, és a titkosítási adatok az operációsrendszer-lemezen vannak beállítva, a visszaállított felügyelt lemezek csatlakoztatásáról lásd: [adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md).

    * **Felügyelt és titkosított virtuális gépek az Azure ad nélkül (BEK és KEK)** – felügyelt, titkosított virtuális gépekhez az Azure ad nélkül (a BEK & KEK használatával titkosított) – Ha a forrás kulcstartó **/kulcs/titkos kód nem érhető el** , állítsa vissza a kulcsot és a titkokat a Key vaultba a [nem titkosított virtuális gép helyreállítása egy Azure Backup helyreállítási pontról](backup-azure-restore-key-secret.md)című eljárás segítségével. Ezután hajtsa végre a következő parancsfájlokat a visszaállított operációsrendszer-lemez titkosítási adatainak beállításához (ez a lépés nem szükséges az adatlemezek esetében). A $dekurl és $kekurl a visszaállított kulcstartóból hívható le.

    A következő szkriptet csak akkor kell végrehajtani, ha a forrás kulcstartó/kulcs/titok nem érhető el.

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Ha a kulcs/titkos kulcsok elérhetők, és a titkosítási adatok az operációsrendszer-lemezen vannak beállítva, a visszaállított felügyelt lemezek csatlakoztatásáról lásd: [adatlemez csatolása Windows rendszerű virtuális géphez a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md).

5. Adja meg a hálózati beállításokat.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Hozza létre a virtuális gépet.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Leküldéses ADE-bővítmény.
   Ha az ade-bővítmények nincsenek leküldve, akkor az adatlemezek titkosítatlan lesznek megjelölve, ezért az alábbi lépések végrehajtásához kötelező:

   * **Virtuális gép az Azure ad-vel** – a következő parancs használatával manuálisan engedélyezheti az adatlemezek titkosítását  

     **Csak BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK és KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * Az **Azure ad** -t nem használó virtuális gépek esetén a következő paranccsal manuálisan engedélyezheti az adatlemezek titkosítását.

     Ha a parancs végrehajtása során AADClientID kér, akkor frissítenie kell a Azure PowerShell.

     **Csak BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK és KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Győződjön meg arról, hogy manuálisan törli a titkosított virtuális gép visszaállítási lemezének folyamata során létrehozott JASON-fájlokat.

## <a name="restore-files-from-an-azure-vm-backup"></a>Fájlok visszaállítása Azure-beli virtuális gépek biztonsági másolatából

A lemezek visszaállítása mellett az egyes fájlokat is visszaállíthatja egy Azure-beli virtuális gép biztonsági másolatából. A fájlok visszaállítása funkció lehetővé teszi a helyreállítási pont összes fájljának elérését. A fájlokat a Fájlkezelőben kezelheti a normál fájlok esetében.

A fájlok Azure-beli virtuális gépekről történő biztonsági mentésének alapszintű lépései a következők:

* Válassza ki a virtuális gépet
* Helyreállítási pont kiválasztása
* A helyreállítási pont lemezének csatlakoztatása
* A szükséges fájlok másolása
* Lemez leválasztása

### <a name="select-the-vm-when-restoring-the-vm"></a>Válassza ki a virtuális gépet (a virtuális gép visszaállításakor)

A megfelelő biztonsági mentési elemet azonosító PowerShell-objektum beszerzéséhez indítsa el a tárolóban található tárolóból, és az objektum-hierarchiában dolgozzon végig. A virtuális gépet jelölő tároló kiválasztásához használja a [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) parancsmagot és a pipe-ot a [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) parancsmaghoz.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>Helyreállítási pont kiválasztása (a virtuális gép visszaállításakor)

A [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) parancsmag használatával listázhatja a biztonsági mentési elemek összes helyreállítási pontját. Ezután válassza ki a visszaállítani kívánt helyreállítási pontot. Ha nem tudja biztosan, hogy melyik helyreállítási pontot szeretné használni, érdemes kiválasztani a legutóbbi RecoveryPointType = AppConsistent pontot a listában.

A következő szkriptben a **$RP** változó a kiválasztott biztonsági mentési elemhez tartozó helyreállítási pontok tömbje az elmúlt hét napban. A tömb a 0. indexben szereplő legutóbbi helyreállítási ponttal fordított sorrendben van rendezve. A helyreállítási pont kiválasztásához használja a PowerShell-tömb szabványos indexelését. A példában a $rp [0] kiválasztja a legutóbbi helyreállítási pontot.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

A kimenet a következő példához hasonló:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>A helyreállítási pont lemezének csatlakoztatása

A [Get-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) parancsmaggal kérheti le a helyreállítási pont összes lemezének csatlakoztatására szolgáló parancsfájlt.

> [!NOTE]
> A lemezek iSCSI-csatlakoztatott lemezként vannak csatlakoztatva ahhoz a géphez, amelyen a parancsfájl fut. A csatlakoztatás azonnal történik, és nem számítunk fel díjat.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

A kimenet a következő példához hasonló:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Futtassa a parancsfájlt azon a gépen, amelyen helyre kívánja állítani a fájlokat. A szkript végrehajtásához meg kell adnia a megadott jelszót. A lemezek csatolása után a Windows fájlkezelővel keresse meg az új köteteket és fájlokat. További információ: Backup (biztonsági mentés) című cikk, [fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Lemezek leválasztása

A szükséges fájlok másolását követően a [disable-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) paranccsal válassza le a lemezeket. Győződjön meg arról, hogy leválasztja a lemezeket, így a helyreállítási pont fájljaihoz való hozzáférés el lesz távolítva.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Következő lépések

Ha inkább a PowerShell-lel szeretné használni az Azure-erőforrásokat, tekintse meg a PowerShell-cikket, a [Windows Server biztonsági mentésének központi telepítését és kezelését](backup-client-automation.md)ismertető témakört. Ha DPM biztonsági mentéseket kezel, tekintse [meg a DPM biztonsági mentésének üzembe helyezése és kezelése](backup-dpm-automation.md)című cikket.
