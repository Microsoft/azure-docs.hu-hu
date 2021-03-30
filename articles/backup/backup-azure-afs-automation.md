---
title: Azure-fájlmegosztás biztonsági mentése a PowerShell használatával
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést egy Azure Files fájlmegosztást a Azure Backup szolgáltatás és a PowerShell használatával.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 948931764769bc967b88e7942b7e8384b0f93dff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87077007"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>Azure-fájlmegosztás biztonsági mentése a PowerShell használatával

Ez a cikk azt ismerteti, hogyan használható a Azure PowerShell egy Azure Files fájlmegosztás biztonsági mentésére egy [Azure Backup](backup-overview.md) Recovery Services-tárolón keresztül.

Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
>
> * Állítsa be a PowerShellt, és regisztrálja a Recovery Services szolgáltatót.
> * Recovery Services-tároló létrehozása.
> * Konfigurálja az Azure-fájlmegosztás biztonsági mentését.
> * Futtasson egy biztonsági mentési feladatot.

## <a name="before-you-start"></a>Előkészületek

* [További](backup-azure-recovery-services-vault-overview.md) információ a Recovery Services-tárolókkal kapcsolatban.
* Tekintse át az az. Recoveryservices szolgáltatónál [parancsmag](/powershell/module/az.recoveryservices) -referenciát az Azure Library-ben.
* Tekintse át a következő PowerShell-objektum hierarchiáját a Recovery Serviceshoz:

  ![Recovery Services objektum-hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>A PowerShell beállítása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell beállítása a következőképpen történik:

1. [Töltse le a Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps).

    > [!NOTE]
    > Az Azure-fájlmegosztás biztonsági mentéséhez szükséges minimális PowerShell-verzió az az. Recoveryservices szolgáltatónál 2.6.0. A legújabb verzió vagy legalább a minimális verzió használata segít elkerülni a meglévő parancsfájlokkal kapcsolatos problémákat. Telepítse a minimális verziót a következő PowerShell-parancs használatával:
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. Keresse meg a Azure Backup PowerShell-parancsmagokat a következő parancs használatával:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Tekintse át a Azure Backup, Azure Site Recovery és a Recovery Services-tároló aliasait és parancsmagait. Íme egy példa arra, hogy mit láthat. Ez nem a parancsmagok teljes listája.

    ![Recovery Services-parancsmagok listája](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Jelentkezzen be az Azure-fiókjába a **AzAccount** használatával.
5. A megjelenő weboldalon a rendszer kéri, hogy adja meg a fiók hitelesítő adatait.

    Azt is megteheti, hogy a fiók hitelesítő adatait paraméterként adja meg a **AzAccount** parancsmagban a **-hitelesítő adatok** használatával.

    Ha Ön egy bérlő nevében működő CSP-partner, akkor az ügyfelet bérlőként kell megadnia. Használja a bérlői azonosítót vagy a bérlő elsődleges tartománynevét. Ilyen például a **következő: kapcsolat-AzAccount-bérlő "fabrikam.com"**.

6. Társítsa a fiókhoz használni kívánt előfizetést, mert egy fiók több előfizetéssel is rendelkezhet:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Ha első alkalommal használja a Azure Backup, a **Register-AzResourceProvider** parancsmag használatával regisztrálja az Azure Recovery Services providert az előfizetésében:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Ellenőrizze, hogy a szolgáltatók regisztrálása sikeres volt-e:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. A parancs kimenetében ellenőrizze, hogy a **RegistrationState** módosításai **regisztrálva** vannak-e. Ha nem, futtassa újra a **Register-AzResourceProvider** parancsmagot.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló Resource Manager-erőforrás, ezért egy erőforráscsoporthoz kell helyeznie. Használhat meglévő erőforráscsoportot is, vagy létrehozhat egy erőforráscsoportot a **New-AzResourceGroup** parancsmag használatával. Ha létrehoz egy erőforráscsoportot, adja meg a nevét és helyét.

Recovery Services-tároló létrehozásához kövesse az alábbi lépéseket:

1. Ha nem rendelkezik meglévő erőforráscsoporthoz, hozzon létre egy újat a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Ebben a példában egy erőforráscsoportot hozunk létre az USA nyugati régiójában:

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. A [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) parancsmag használatával hozza létre a tárolót. Azonos helyet kell megadnia az erőforráscsoport számára használt tárolóhoz.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Az előfizetésben található tárolók megtekintése

Az előfizetés összes tárolójának megtekintéséhez használja a [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

A kimenet az alábbihoz hasonló. Vegye figyelembe, hogy a kimenet megadja a társított erőforráscsoportot és helyet.

```powershell
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

Számos Azure Backup parancsmagnak bemenetként kell megkövetelni a Recovery Services-tároló objektumát, így kényelmes a tároló objektum tárolása egy változóban.

A tárolási környezet a tár által védett adatok típusa. Állítsa be a [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext)használatával. A környezet beállítása után az az összes további parancsmagra vonatkozik.

Az alábbi példa a **testvault** tároló környezetét állítja be:

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>A tár AZONOSÍTÓjának beolvasása

Azt tervezzük, hogy a tár környezeti beállításait a Azure PowerShell irányelvek alapján kell érvényteleníteni. Ehelyett tárolhatja vagy beolvashatja a tár AZONOSÍTÓját, és átadhatja azokat a megfelelő parancsoknak. Ha nem állította be a tár környezetét, vagy egy adott tárolóhoz szeretne futtatni egy parancsot, adja át a tár AZONOSÍTÓját az `-vaultID` összes vonatkozó parancsnak a következő módon:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása

A biztonsági mentési szabályzat meghatározza a biztonsági mentések ütemezését, valamint azt, hogy mennyi ideig kell megőrizni a biztonsági másolatok helyreállítási pontjait.

A biztonsági mentési szabályzat legalább egy adatmegőrzési szabályzattal van társítva. Egy adatmegőrzési házirend határozza meg, hogy mennyi ideig tart a helyreállítási pont a törlése előtt. A biztonsági mentéseket napi, heti, havi vagy éves megőrzéssel állíthatja be.

Íme néhány parancsmag a biztonsági mentési házirendekhez:

* Tekintse meg az alapértelmezett biztonsági mentési szabályzatot a [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject)használatával.
* Tekintse meg az alapértelmezett biztonsági mentési házirend-ütemtervet a [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject)használatával.
* Hozzon létre egy új biztonsági mentési szabályzatot a [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)használatával. Adja meg az ütemezett és adatmegőrzési szabályzat objektumait bemenetként.

Alapértelmezés szerint a rendszer a kezdési időt határozza meg az ütemezett házirend objektumban. A következő példa használatával módosíthatja a kezdési időt a kívánt kezdési időpontra. A kívánt kezdési időpontnak az egyezményes világidő (UTC) szerint kell lennie. A példa feltételezi, hogy a várt kezdési idő a napi biztonsági másolatok 01:00-as UTC-értéke.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> A kezdési időt csak 30 percenként kell megadnia. Az előző példában csak "01:00:00" vagy "02:30:00" lehet. A kezdési időpont nem lehet "01:15:00".

A következő példa az ütemezett házirendet és a változókban tárolt adatmegőrzési szabályzatot tárolja. Ezután ezeket a változókat paraméterekként használja egy új szabályzathoz (**NewAFSPolicy**). A **NewAFSPolicy** napi biztonsági mentést készít, és 30 napig őrzi meg.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet a következő példához hasonló:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Biztonsági mentés engedélyezése

A biztonsági mentési szabályzat meghatározása után engedélyezheti az Azure-fájlmegosztás védelmét a szabályzat használatával.

### <a name="retrieve-a-backup-policy"></a>Biztonsági mentési szabályzat beolvasása

A megfelelő szabályzat objektum beolvasása a [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)használatával végezhető el. Ezzel a parancsmaggal megtekintheti a munkaterhelés-típushoz társított házirendeket, vagy egy adott szabályzatot.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Házirend beolvasása egy munkaterhelés-típushoz

A következő példa a **AzureFiles** számítási feladathoz tartozó szabályzatokat kérdezi le:

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A kimenet a következő példához hasonló:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> A PowerShell **BackupTime** mezőjének időzónája UTC szerint van elfoglalva. Ha a biztonsági mentés ideje megjelenik a Azure Portalban, az idő a helyi időzónára van igazítva.

#### <a name="retrieve-a-specific-policy"></a>Adott szabályzat beolvasása

A következő szabályzat lekéri a **dailyafs** nevű biztonsági mentési szabályzatot:

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>Védelem engedélyezése és a szabályzat alkalmazása

Engedélyezze a védelmet az [enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection)használatával. Miután a házirend társítva van a tárolóhoz, a biztonsági mentések a szabályzat ütemezésével összhangban lesznek aktiválva.

A következő példa lehetővé teszi az Azure fájlmegosztás **testAzureFileShare** való védelmét a Storage-fiók **testStorageAcct**, a szabályzat **dailyafs**:

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

A parancs addig várakozik, amíg a configure-Protection feladata be nem fejeződik, és az alábbi példához hasonló kimenetet ad hozzá:

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

A Storage-fiókhoz tartozó fájlmegosztás listájának beszerzésével kapcsolatos további információkért tekintse meg [ezt a cikket](/powershell/module/az.storage/get-azstorageshare).

## <a name="important-notice-backup-item-identification"></a>Fontos figyelmeztetés: biztonsági másolati elem azonosítása

Ez a szakasz az Azure-fájlmegosztás biztonsági mentésének fontos változásait ismerteti az általános elérhetőségre való felkészülés során.

Az Azure-fájlmegosztás biztonsági mentésének engedélyezésekor a felhasználó egy fájlmegosztás nevét adja meg az entitás neveként, és létrejön egy biztonságimásolat-elem. A biztonsági mentési elemek neve a Azure Backup szolgáltatás által létrehozott egyedi azonosító. Általában az azonosító a felhasználóbarát név. Ha azonban kezelni szeretné a helyreállítható törlési forgatókönyvet, amelyben egy fájlmegosztás törölhető, és egy másik fájlmegosztás is létrehozható ugyanazzal a névvel, az Azure-fájlmegosztás egyedi identitása már azonosító.

Az egyes elemek egyedi AZONOSÍTÓjának megismeréséhez futtassa a **Get-AzRecoveryServicesBackupItem** parancsot a **BackupManagementType** és a **WorkloadType** megfelelő szűrőinek használatával az összes releváns elem lekéréséhez. Ezután figyelje meg a visszaadott PowerShell objektum/válasz név mezőjét.

Azt javasoljuk, hogy listaelemeket adjon meg, majd a válasz név mezőjéből olvassa be az egyedi nevet. Ezzel az értékkel szűrheti az elemeket a *Name* paraméterrel. Ellenkező esetben használja a *FriendlyName* paramétert az elem azonosítóval való lekéréséhez.

> [!IMPORTANT]
> Győződjön meg arról, hogy a PowerShell frissítve lett az Azure-fájlmegosztás biztonsági mentéséhez szükséges minimális verzióra (az. Recoveryservices szolgáltatónál 2.6.0). Ebben a verzióban a **Get-AzRecoveryServicesBackupItem** parancshoz elérhető a *FriendlyName* szűrő.
>
> Adja meg az Azure-fájlmegosztás nevét a *FriendlyName* paraméternek. Ha átadja a fájlmegosztás nevét a *Name* paraméternek, ez a verzió figyelmeztetést küld a név átadására a *FriendlyName* paraméternek.
>
> A minimális verzió telepítése nem okozhatja a meglévő parancsfájlok meghibásodását. Telepítse a PowerShell minimális verzióját a következő parancs használatával:
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>Igény szerinti biztonsági mentés indítása

A [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) használatával igény szerinti biztonsági mentést futtathat egy védett Azure-fájlmegosztás számára:

1. Kérje le a Storage-fiókot abban a tárolóban, amely a biztonsági mentési adatait a [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)használatával tárolja.
2. A biztonsági mentési feladatok elindításához szerezze be az Azure-fájlmegosztás adatait a [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)használatával.
3. Futtasson egy igény szerinti biztonsági mentést a [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)használatával.

Futtassa az igény szerinti biztonsági mentést az alábbiak szerint:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

A parancs visszaadja a nyomon követett AZONOSÍTÓval rendelkező feladatot, ahogy az az alábbi példában is látható:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Az Azure-fájlmegosztás pillanatképeit a rendszer a biztonsági másolatok készítése során használja. Általában a művelet akkor fejeződik be, amikor a parancs visszaadja ezt a kimenetet.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók [a Azure Portal Azure Files biztonsági mentéséről](backup-afs.md).
* A biztonsági mentések ütemezéséhez tekintse [meg a githubon található minta parancsfájlt](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) egy Azure Automation runbook használatával.
