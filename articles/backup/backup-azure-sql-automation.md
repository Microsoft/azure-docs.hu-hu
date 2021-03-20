---
title: SQL-adatbázis az Azure virtuális gép biztonsági mentésében & visszaállítás a PowerShell használatával
description: Az SQL-adatbázisok biztonsági mentése és visszaállítása Azure-beli virtuális gépeken Azure Backup és PowerShell használatával.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 0a3467ffa3a67ac9ad593748948cea8da59e3e6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97734538"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>SQL-adatbázisok biztonsági mentése és visszaállítása az Azure-beli virtuális gépeken a PowerShell-lel

Ez a cikk azt ismerteti, hogyan használható a Azure PowerShell egy Azure-beli virtuális gépen található SQL-adatbázis biztonsági mentésére és helyreállítására [Azure Backup](backup-overview.md) Recovery Services-tároló használatával.

Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
>
> * A PowerShell beállítása és az Azure Recovery Services-szolgáltató regisztrálása.
> * Recovery Services-tároló létrehozása.
> * Az SQL-adatbázis biztonsági mentésének konfigurálása egy Azure-beli virtuális gépen.
> * Futtasson egy biztonsági mentési feladatot.
> * Biztonsági másolat készítése az SQL-ADATBÁZISról.
> * A biztonsági mentési és visszaállítási feladatok figyelése.

## <a name="before-you-start"></a>Előkészületek

* [További](backup-azure-recovery-services-vault-overview.md) információ a Recovery Services-tárolókkal kapcsolatban.
* További információ az [SQL-adatbázisok Azure-beli virtuális gépeken belüli biztonsági mentésének](backup-azure-sql-database.md#before-you-start)funkciói funkcióiról.
* Tekintse át Recovery Services PowerShell-objektumának hierarchiáját.

### <a name="recovery-services-object-hierarchy"></a>Recovery Services objektum-hierarchia

Az objektum-hierarchiát a következő ábra összegzi.

![Recovery Services objektum-hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Tekintse át az az **. recoveryservices szolgáltatónál** [parancsmag](/powershell/module/az.recoveryservices) -referenciát az Azure Library-ben.

### <a name="set-up-and-install"></a>Beállítás és telepítés

A PowerShell beállítása a következőképpen történik:

1. [Töltse le az az PowerShell legújabb verzióját](/powershell/azure/install-az-ps). A minimálisan szükséges verzió a 1.5.0.

2. Keresse meg a Azure Backup PowerShell-parancsmagokat a következő paranccsal:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Tekintse át a Azure Backup és a Recovery Services-tároló aliasneveit és parancsmagait. Íme egy példa arra, hogy mit láthat. Ez nem a parancsmagok teljes listája.

    ![Recovery Services-parancsmagok listája](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Jelentkezzen be az Azure-fiókjába a **AzAccount** használatával.
5. A megjelenő weblapon meg kell adnia a fiók hitelesítő adatait.

    * Másik lehetőségként megadhatja a fiók hitelesítő adatait a kapcsolat- **AzAccount** parancsmag és a **-hitelesítő adatok** paraméterei között.
    * Ha Ön egy bérlőhöz tartozó CSP-partner, adja meg az ügyfelet bérlőként a tenantID vagy a bérlő elsődleges tartománynevének használatával. Ilyen például a **AzAccount-bérlő** fabrikam.com.

6. Társítsa a fiókhoz használni kívánt előfizetést, mert egy fiók több előfizetéssel is rendelkezhet.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Ha első alkalommal használja a Azure Backup, a **Register-AzResourceProvider** parancsmag használatával regisztrálja az Azure Recovery Services-szolgáltatót az előfizetésében.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Ellenőrizze, hogy a szolgáltatók regisztrálása sikeres volt-e:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. A parancs kimenetében ellenőrizze, hogy a **RegistrationState** módosításai **regisztrálva** vannak-e. Ha nem, futtassa újra a **Register-AzResourceProvider** parancsmagot.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Recovery Services-tároló létrehozásához kövesse az alábbi lépéseket.

A Recovery Services-tároló Resource Manager-erőforrás, ezért egy erőforráscsoporthoz kell helyeznie. Használhat meglévő erőforráscsoportot is, vagy létrehozhat egy erőforráscsoportot a **New-AzResourceGroup** parancsmaggal. Ha létrehoz egy erőforráscsoportot, adja meg az erőforráscsoport nevét és helyét.

1. Egy tároló egy erőforráscsoporthoz kerül. Ha nem rendelkezik meglévő erőforráscsoporthoz, hozzon létre egy újat a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Ebben a példában egy új erőforráscsoportot hozunk létre az USA nyugati régiójában.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. A [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) parancsmag használatával hozza létre a tárolót. Azonos helyet kell megadnia a tárolóhoz az erőforráscsoport esetében.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Adja meg a tároló tárolásához használni kívánt redundancia típusát.

    * A [helyileg](../storage/common/storage-redundancy.md#locally-redundant-storage)redundáns tárolást, a [geo-redundáns](../storage/common/storage-redundancy.md#geo-redundant-storage) tárolást vagy a [zóna redundáns tárolását](../storage/common/storage-redundancy.md#zone-redundant-storage) is használhatja.
    * A következő példa beállítja a **-BackupStorageRedundancy** beállítást a [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd számára a **testvault** beállításnál a **GeoRedundant** értékre.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Az előfizetésben található tárolók megtekintése

Az előfizetés összes tárolójának megtekintéséhez használja a [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault).

```powershell
Get-AzRecoveryServicesVault
```

A kimenet az alábbihoz hasonló. A társított erőforráscsoport és hely van megadva.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>A tár környezetének beállítása

Tárolja a tároló objektumot egy változóban, és állítsa be a tár környezetét.

* Számos Azure Backup parancsmagnak bemenetként kell megkövetelni a Recovery Services-tároló objektumát, így kényelmes a tároló objektum tárolása egy változóban.
* A tárolási környezet a tár által védett adatok típusa. Állítsa be a [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). A környezet beállítása után az az összes további parancsmagra vonatkozik.

Az alábbi példa a **testvault** tároló környezetét állítja be.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>A tár AZONOSÍTÓjának beolvasása

A tár környezeti beállítását a Azure PowerShell irányelvek alapján tervezzük. Ehelyett a tár AZONOSÍTÓját tárolhatja vagy beolvashatja, és átadhatja a megfelelő parancsoknak, a következőképpen:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása

A biztonsági mentési szabályzat meghatározza a biztonsági mentések ütemezését, valamint azt, hogy mennyi ideig kell megőrizni a biztonsági mentési helyreállítási pontokat:

* A biztonsági mentési szabályzat legalább egy adatmegőrzési szabályzattal van társítva. Egy adatmegőrzési házirend határozza meg, hogy mennyi ideig tart a helyreállítási pont a törlése előtt.
* Tekintse meg az alapértelmezett biztonsági mentési szabályzatot a [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject)használatával.
* Tekintse meg az alapértelmezett biztonsági mentési szabályzatot a [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject)használatával.
* Új biztonsági mentési szabályzat létrehozásához használja a [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) parancsmagot. Megadhatja az ütemterv és adatmegőrzési szabályzat objektumait.

Alapértelmezés szerint a rendszer a kezdési időt határozza meg az ütemezett házirend objektumban. A következő példa használatával módosíthatja a kezdési időt a kívánt kezdési időpontra. A kívánt kezdési időpontnak UTC-ként is kell lennie. Az alábbi példa azt feltételezi, hogy a várt kezdési idő a napi biztonsági mentések 01:00-as UTC-értéke.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> A kezdési időt csak 30 percenként kell megadnia. A fenti példában csak "01:00:00" vagy "02:30:00" lehet. A kezdési időpont nem lehet "01:15:00".

A következő példa az ütemezett házirendet és a változókban tárolt adatmegőrzési szabályzatot tárolja. Ezután ezeket a változókat paraméterekként használja egy új szabályzathoz (**NewSQLPolicy**). A **NewSQLPolicy** naponta teljes biztonsági mentést készít, és 180 napig tart, és 2 óránként biztonsági másolatot készít a naplóról

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet az alábbihoz hasonló.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Biztonsági mentés engedélyezése

### <a name="registering-the-sql-vm"></a>Az SQL-alapú virtuális gép regisztrálása

Az Azure-beli virtuális gépek biztonsági mentései és Azure-fájlmegosztás esetén a Backup szolgáltatás képes csatlakozni ezekhez a Azure Resource Manager erőforrásokhoz, és beolvasni a vonatkozó adatokat. Mivel az SQL egy Azure-beli virtuális gépen belüli alkalmazás, a Backup szolgáltatásnak engedéllyel kell rendelkeznie az alkalmazás eléréséhez és a szükséges adatok beolvasásához. Ehhez *regisztrálnia* kell az Azure-beli virtuális gépet, amely tartalmazza az SQL-alkalmazást Recovery Services-tárolóval. Miután regisztrált egy SQL virtuális gépet egy tárolóval, csak az adott tárolóhoz biztosíthatja az SQL-adatbázisok védettségét. A virtuális gép regisztrálásához használja a [Register-AzRecoveryServicesBackupContainer PowerShell-](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) parancsmagot.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

A parancs visszaküldi az erőforrás biztonsági mentési tárolóját, és az állapot regisztrálva lesz

> [!NOTE]
> Ha a Force paraméter nincs megadva, a rendszer arra kéri, hogy erősítse meg a "szeretné letiltani a tároló védelmét" szöveggel. Hagyja figyelmen kívül ezt a szöveget, és mondja el, hogy erősítse meg az "Y" kifejezést. Ez egy ismert probléma, és dolgozunk a Force paraméter szövegének és követelményének eltávolításán.

### <a name="fetching-sql-dbs"></a>SQL-adatbázisok beolvasása

A regisztrációt követően a Backup szolgáltatás képes lesz a virtuális gépen található összes elérhető SQL-összetevő listázására. Az összes olyan SQL-összetevő megtekintése, amelyről biztonsági másolatot szeretne készíteni erre a tárolóra a [Get-AzRecoveryServicesBackupProtectableItem PowerShell-](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) parancsmag használatával

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

A kimenetben az összes nem védett SQL-összetevő megjelenik az összes olyan SQL-virtuális gépen, amely az adott tárolóhoz van regisztrálva, az elemtípus és a ServerName használatával. Az egyes SQL-alapú virtuális gépeket tovább szűrheti úgy, hogy átadja a "-Container" paramétert, vagy a "Name" és a "ServerName" kombinációját használja a ItemType jelzővel, hogy egy egyedi SQL-elemmel érkezzen.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Biztonsági mentés konfigurálása

Most, hogy rendelkezünk a szükséges SQL-ADATBÁZISsal és a szabályzattal, amelyről biztonsági mentést kell készíteni, az [enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) parancsmaggal konfigurálhatja az SQL-adatbázis biztonsági mentését.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

A parancs megvárja, amíg a biztonsági mentés be nem fejeződik, és a következő kimenetet adja vissza.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Új SQL-adatbázisok beolvasása

A gép regisztrálása után a Backup szolgáltatás beolvassa az elérhető adatbázisok részleteit. Ha az SQL-adatbázisok vagy SQL-példányok később hozzáadódnak a regisztrált géphez, manuálisan kell elindítania a Backup szolgáltatást egy friss "lekérdezés" elvégzéséhez, hogy az **összes** nem védett (beleértve az újonnan hozzáadott) adatbázisok újbóli lekérését. Egy új lekérdezés végrehajtásához használja az [inicializálás-AzRecoveryServicesBackupItem PowerShell-](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) parancsmagot az SQL virtuális gépen. A parancs addig vár, amíg a művelet be nem fejeződik. Később használja a [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell-parancsmagot a legújabb nem védett SQL-összetevők listájának lekéréséhez.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

A megfelelő védhető elemek beolvasása után engedélyezze a biztonsági mentéseket a [fenti szakaszban](#configuring-backup)leírtak szerint.
Ha nem szeretné, hogy az új adatbázisok manuálisan is észlelhetők legyenek, az [alábbiakban](#enable-autoprotection)ismertetett módon dönthetnek az automatikus védelemről.

## <a name="enable-autoprotection"></a>Autoprotection engedélyezése

A biztonsági mentést úgy is beállíthatja, hogy a jövőben hozzáadott összes adattároló automatikusan védve legyen egy bizonyos házirenddel. Az autoprotection engedélyezéséhez használja az [enable-AzRecoveryServicesBackupAutoProtection PowerShell-](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection) parancsmagot.

Mivel az utasítás az összes jövőbeli adatbázisok biztonsági mentését végzi, a művelet SQLInstance szinten történik.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Miután megadta az autoprotection-szándékot, az újonnan hozzáadott adatbázisok beolvasására irányuló lekérdezés 8 óránként, ütemezett háttérbeli feladatként zajlik.

## <a name="restore-sql-dbs"></a>SQL-adatbázisok visszaállítása

A Azure Backup az Azure-beli virtuális gépeken futó SQL Server-adatbázisokat a következőképpen állíthatja helyre:

* Állítsa vissza egy adott dátumra vagy időpontra (a másodikra) a tranzakciónaplók biztonsági másolatainak használatával. Azure Backup automatikusan meghatározza a megfelelő teljes különbözeti biztonsági mentést, valamint a visszaállításhoz szükséges naplók láncát, amely a kijelölt idő alapján állítható vissza.
* Egy adott helyreállítási pontra való visszaállításhoz állítson vissza egy adott teljes vagy különbözeti biztonsági másolatot.

Az SQL-adatbázisok visszaállítása előtt tekintse meg az [itt](restore-sql-database-azure-vm.md#restore-prerequisites) említett előfeltételeket.

Először a [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) PowerShell-parancsmag használatával olvassa be a megfelelő biztonsági mentést az SQL-adatbázishoz.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>A megfelelő visszaállítási idő beolvasása

A fentiekben leírtak szerint visszaállíthatja a biztonsági másolatban szereplő SQL-adatbázist egy teljes/különbözeti másolásra **vagy** egy napló időpontra.

#### <a name="fetch-distinct-recovery-points"></a>Különböző helyreállítási pontok beolvasása

A [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) használatával különböző (teljes/különbözeti) helyreállítási pontokat hívhat le egy biztonsági másolatba mentett SQL-adatbázishoz.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

A kimenet a következő példához hasonló.

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

A megfelelő helyreállítási pont beolvasásához használja az "RecoveryPointId" szűrőt vagy egy tömböt.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Időponthoz tartozó helyreállítási pont lekérése

Ha egy adott időpontra szeretné visszaállítani az adatbázist, használja a [Get-AzRecoveryServicesBackupRecoveryLogChain PowerShell-](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) parancsmagot. A parancsmag olyan dátumok listáját adja vissza, amelyek az SQL biztonsági mentési elemhez tartozó nem törött, folyamatos naplózási lánc kezdő és befejező időpontját jelölik. A kívánt időpontnak ezen a tartományon belül kell lennie.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -VaultId $targetVault.ID
```

A kimenet a következő példához hasonló lesz.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

A fenti kimenet azt jelenti, hogy a megjelenített kezdési időpont és a befejezési időpont között bármely időpontra vissza lehet állítani. Az időpontok UTC-ben vannak. Hozzon létre bármely olyan időpontot a PowerShellben, amely a fent látható tartományon belül van.

> [!NOTE]
> Ha a visszaállításhoz kiválasztott naplózási időpontot választja, nem kell megadnia a kiindulási pontot, azaz a teljes biztonsági mentést, amelyről az adatbázis vissza lesz állítva. Azure Backup szolgáltatás gondoskodik a teljes helyreállítási tervről, azaz a teljes biztonsági mentésről, a biztonsági másolatok által alkalmazandó naplókról és így tovább.

### <a name="determine-recovery-configuration"></a>Helyreállítási konfiguráció meghatározása

SQL DB-visszaállítás esetén a következő visszaállítási forgatókönyvek támogatottak.

* Az SQL-adatbázis biztonsági mentésének felülbírálása egy másik helyreállítási pontról – OriginalWorkloadRestore
* Az SQL-adatbázis visszaállítása új ADATBÁZISként ugyanabban az SQL-példányban – AlternateWorkloadRestore
* Az SQL-adatbázis visszaállítása új ADATBÁZISként másik SQL-példányban egy másik SQL-beli virtuális gépen – AlternateWorkloadRestore
* Az SQL-adatbázis visszaállítása. bak fájlokként – RestoreAsFiles

Miután beolvasta a megfelelő helyreállítási pontot (külön vagy a naplózási időpontot), a [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell-parancsmaggal beolvashatja a helyreállítási konfigurációs objektumot a kívánt helyreállítási tervnek megfelelően.

#### <a name="original-workload-restore"></a>Eredeti munkaterhelés visszaállítása

A biztonsági mentéssel ellátott ADATBÁZISnak a helyreállítási pontról származó adatokkal való felülbírálásához csak a megfelelő jelzőt és a megfelelő helyreállítási pontot kell megadnia, ahogy az az alábbi példában is látható.

##### <a name="original-restore-with-distinct-recovery-point"></a>Eredeti visszaállítás különálló helyreállítási ponttal

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Eredeti visszaállítás naplózási ponttal – időben

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Alternatív munkaterhelés visszaállítása

> [!IMPORTANT]
> Egy biztonsági másolattal rendelkező SQL-adatbázis visszaállítható új ADATBÁZISként egy másik SQLInstance, amely egy Azure-beli virtuális gépen van regisztrálva erre a tárba.

A fentiekben ismertetett módon, ha a cél SQLInstance egy másik Azure-beli virtuális gépen belül található, győződjön meg róla, hogy [regisztrálva](#registering-the-sql-vm) van a tárolóban, és hogy a megfelelő SQLInstance védhető elemként jelenik meg.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Ezután csak adja át a megfelelő helyreállítási pontot, és az SQL-példányt a jobb oldali jelölővel, az alább látható módon.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternatív visszaállítás különálló helyreállítási ponttal

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternatív visszaállítás a naplózási ponttal

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Visszaállítás fájlként

Ha egy adatbázis helyett. bak fájlként szeretné visszaállítani a biztonsági mentési adatmennyiséget, válassza a **visszaállítás fájlként** lehetőséget. A biztonsági mentés alatt álló SQL-adatbázis visszaállítható bármely, a tárolóban regisztrált virtuális gépre.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Visszaállítás fájlként különálló helyreállítási ponttal

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Visszaállítás fájlokként a legújabb teljes körű bejelentkezéssel

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Visszaállítás fájlokként egy megadott teljes időponttal

Ha a visszaállításhoz használni kívánt teljes méretet szeretne megadni, használja a következő parancsot:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

A [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell-parancsmagból beszerzett utolsó helyreállítási pont konfigurációs objektum rendelkezik a visszaállításhoz szükséges összes információval, és az alább látható.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

Szerkesztheti a visszaállított adatbázis nevét, a OverwriteWLIfpresent, a NoRecoveryMode és a targetPhysicalPath mezőket. További részleteket a célfájl elérési útjairól az alább látható módon talál.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Állítsa be a megfelelő PowerShell-tulajdonságokat karakterlánc-értékként az alább látható módon.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Győződjön meg arról, hogy a végső helyreállítási konfigurációs objektum rendelkezik az összes szükséges és megfelelő értékkel, mivel a visszaállítási művelet a konfigurációs objektumon alapul.

### <a name="restore-with-relevant-configuration"></a>Visszaállítás a megfelelő konfigurációval

Ha a megfelelő helyreállítási konfigurációs objektum beszerzése és ellenőrzése megtörtént, a visszaállítási folyamat elindításához használja a [Restore-AzRecoveryServicesBackupItem PowerShell-](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) parancsmagot.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

A visszaállítási művelet a nyomon követett feladatot adja vissza.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>SQL-biztonsági mentések kezelése

### <a name="on-demand-backup"></a>Igény szerinti biztonsági mentés

Miután engedélyezte a biztonsági mentést egy ADATBÁZIShoz, a [Backup-AzRecoveryServicesBackupItem PowerShell-](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) parancsmag használatával is aktiválhat egy igény szerinti biztonsági mentést az adatbázishoz. Az alábbi példa egy teljes biztonsági mentést indít egy SQL-ADATBÁZISon, amelyen engedélyezve van a tömörítés, és a teljes biztonsági mentést 60 napig kell megőrizni.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Az igény szerinti biztonsági mentési parancs a nyomon követett feladatot adja vissza.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Ha a kimenet elvész, vagy ha a megfelelő AZONOSÍTÓJÚ feladatot szeretné beszerezni, kérje le a Azure Backup szolgáltatásból származó [feladatok listáját](#track-azure-backup-jobs) , és kövesse nyomon a részleteket és annak részleteit.

### <a name="change-policy-for-backup-items"></a>Biztonsági mentési elemek szabályzatának módosítása

A biztonsági másolatban szereplő elem házirendjének módosítása a *Házirend1* és a *Policy2* között is megváltoztatható. Ha egy biztonsági másolati elemhez szeretne házirendeket váltani, olvassa be a vonatkozó házirendet, és készítsen biztonsági másolatot az elemről, és használja az [enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) parancsot a biztonsági mentési elemmel paraméterként.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

A parancs megvárja, amíg a biztonsági mentés be nem fejeződik, és a következő kimenetet adja vissza.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>Meglévő biztonsági mentési szabályzat szerkesztése

Meglévő szabályzat szerkesztéséhez használja a [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) parancsot.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

A hibák követéséhez egy idő elteltével a biztonsági mentési feladatokat tekintse át. Ha vannak ilyenek, ki kell javítania a problémákat. Ezután futtassa újra a szabályzat szerkesztése parancsot a **FixForInconsistentItems** paraméterrel, hogy újra megpróbálja szerkeszteni a házirendet az összes olyan biztonsági mentési elemnél, amelynél a művelet korábban meghiúsult.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>SQL virtuális gépek újbóli regisztrálása

> [!WARNING]
> Olvassa el ezt a [dokumentumot](backup-sql-server-azure-troubleshoot.md#re-registration-failures) , hogy megértse a hiba tüneteit és okait az ismételt regisztráció megkísérlése előtt

Az SQL virtuális gép újbóli regisztrálásának megkezdéséhez olvassa le a megfelelő biztonságimásolat-tárolót, és adja át a Register parancsmagnak.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>A védelem kikapcsolása

#### <a name="retain-data"></a>Adatok megőrzése

Ha le szeretné állítani a védelmet, használhatja a [disable-AzRecoveryServicesBackupProtection PowerShell-](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) parancsmagot. Ezzel leállítja az ütemezett biztonsági mentéseket, de a biztonsági mentés egészen addig, amíg a rendszer örökre megőrzi az adatokat.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

Ahhoz, hogy teljesen el lehessen távolítani a tárolt biztonsági mentési fájlokat a tárolóban, egyszerűen adja hozzá a "-RemoveRecoveryPoints" jelzőt, vagy váltson a ["letiltás" védelmi parancsra](#retain-data).

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Automatikus védelem letiltása

Ha az autoprotection konfigurálva van egy SQLInstance, letilthatja azt a [disable-AzRecoveryServicesBackupAutoProtection PowerShell-](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) parancsmag használatával.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>SQL-alapú virtuális gép regisztrációjának törlése

Ha az SQL Server összes [adatbázisa már nem védett, és nem áll rendelkezésre biztonsági másolat](#delete-backup-data), akkor az SQL virtuális gép regisztrációját törölheti ebből a tárból. Csak ezután lehet védelemmel ellátni egy másik tárolót az adatbázisok számára. Az SQL virtuális gép regisztrációjának megszüntetéséhez használja a [Regisztráció törlése-AzRecoveryServicesBackupContainer PowerShell-](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) parancsmagot.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Azure Backup feladatok nyomon követése

Fontos megjegyezni, hogy Azure Backup csak a felhasználó által aktivált feladatokat követi nyomon az SQL Backup szolgáltatásban. Az ütemezett biztonsági mentések (beleértve a naplók biztonsági mentését) nem láthatók a portálon vagy a PowerShellben. Ha azonban bármelyik ütemezett feladat meghiúsul, [biztonsági mentési riasztás](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) jön létre, és megjelenik a portálon. A [Azure monitor használatával](backup-azure-monitoring-use-azuremonitor.md) nyomon követheti az összes ütemezett feladatot és egyéb releváns információt.

A felhasználók nyomon követhetik az igény szerinti vagy a felhasználó által aktivált műveleteket az aszinkron feladatok (például biztonsági mentés) [kimenetében](#on-demand-backup) visszaadott JobID. A [Get-AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) PowerShell-parancsmag használatával nyomon követheti a feladatot és annak részleteit.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Az igény szerinti feladatok és a Azure Backup szolgáltatásból származó állapotok listájának lekéréséhez használja a [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) PowerShell-parancsmagot. A következő példa az összes folyamatban lévő SQL-feladatot adja vissza.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

A folyamatban lévő feladatok megszakításához használja a [stop-AzRecoveryServicesBackupJob PowerShell-](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) parancsmagot.

## <a name="managing-sql-always-on-availability-groups"></a>SQL always on rendelkezésre állási csoportok kezelése

Az SQL always on rendelkezésre állási csoportok esetében ügyeljen arra, hogy regisztrálja a rendelkezésre állási csoport (AG) [összes csomópontját](#registering-the-sql-vm) . Ha az összes csomóponton regisztrálva van, az SQL rendelkezésre állási csoport objektum logikailag jön létre a védhető elemek alatt. Az SQL AG alatti adatbázisok "SQLDatabase"-ként lesznek felsorolva. A csomópontok önálló példányként jelennek meg, és az ezek alá tartozó alapértelmezett SQL-adatbázisok is SQL-adatbázisként jelennek meg.

Tegyük fel például, hogy egy SQL AG két csomóponttal rendelkezik: *SQL-Server-0* és *SQL-Server-1* és 1 SQL AG db. Ha mindkét csomópont regisztrálva van, [a védhető elemek listája](#fetching-sql-dbs)a következő összetevőket sorolja fel

* Egy SQL AG objektum-Protected Item Type as SQLAvailabilityGroup
* Az SQL AG adatbázis-védelemmel ellátott elemtípus-típusa SQLDatabase
* SQL-Server-0 – Protected Item Type as SQLInstance
* SQL-Server-1 – Protected Item Type as SQLInstance
* Bármely alapértelmezett SQL-adatbázisok (Master, Model, msdb) az SQL-Server-0-Protected Item Type as SQLDatabase
* Bármely alapértelmezett SQL-adatbázisok (Master, Model, msdb) az SQL-Server-1 – Protected Item Type as SQLDatabase

az SQL-Server-0, SQL-Server-1 az "AzureVMAppContainer" néven jelenik meg a [biztonsági mentési tárolók listáján](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).

A [biztonsági mentés engedélyezéséhez](#configuring-backup) és az [igény szerinti biztonsági mentési](#on-demand-backup) és [visszaállítási PowerShell-parancsmagok](#restore-sql-dbs) megegyeznek a megfelelő adatbázis beolvasásával.
