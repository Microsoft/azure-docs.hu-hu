---
title: Biztonsági mentési adatok titkosítása ügyfelek által felügyelt kulcsok használatával
description: Megtudhatja, Azure Backup lehetővé teszi a biztonsági mentési adatok ügyfél által kezelt kulcsokkal (CMK) való titkosítását.
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: bd51be06e707674f3e35b3478d7f99d096be912a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718772"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Biztonsági mentési adatok titkosítása ügyfelek által felügyelt kulcsok használatával

Azure Backup lehetővé teszi, hogy az alapértelmezés szerint engedélyezett, platform által kezelt kulcsok használata helyett ügyfél által kezelt kulcsokkal (CMK) titkosítsa a biztonsági mentési adatokat. A biztonsági mentési adatok titkosításához használt kulcsokat a következő fájlban [kell Azure Key Vault.](../key-vault/index.yml)

A biztonsági másolatok titkosításához használt titkosítási kulcs különbözhet a forráshoz használttól. Az adatok védelme AES 256-alapú adattitkosítási kulccsal (DEK), amely pedig a kulcsok (KEK) használatával van védve. Így teljes körűen szabályozhatja az adatokat és a kulcsokat. A titkosítás engedélyezése érdekében a Recovery Services-tárolónak hozzáférést kell biztosítani a titkosítási kulcshoz a Azure Key Vault. A kulcsot szükség szerint módosíthatja.

Ez a cikk a következőket ismerteti:

- Recovery Services-tároló létrehozása
- A Recovery Services-tároló konfigurálása a biztonsági mentési adatok ügyfél által kezelt kulcsokkal való titkosítására
- Ügyfél által kezelt kulcsokkal titkosított tárolókba való biztonsági mentés végrehajtása
- Adatok visszaállítása biztonsági másolatból

## <a name="before-you-start"></a>Előkészületek

- Ez a funkció csak az új **Recovery Services-tárolók titkosítását teszi lehetővé.** Azok a tárolók, amelyek meglévő elemeket tartalmaznak, vagy amelyekre regisztrálni próbálnak, nem támogatottak.

- A Recovery Services-tároló engedélyezése után az ügyfél által kezelt kulcsokkal való titkosítás nem lehet visszaállni a platform által kezelt kulcsok használatára (alapértelmezés). A titkosítási kulcsokat az igényeinek megfelelően módosíthatja.

- Ez a **funkció jelenleg nem támogatja** a MARS-ügynökkel való biztonsági mentést, és előfordulhat, hogy nem fog tudni CMK-titkosítású tárolót használni. A MARS-ügynök felhasználói jelszóalapú titkosítást használ. Ez a funkció nem támogatja a klasszikus virtuális gépek biztonsági mentését sem.

- Ez a funkció nem kapcsolódik a [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)szolgáltatáshoz, amely a virtuális gép lemezei vendégalapú titkosítását használja a BitLocker (Windows esetén) és a DM-Crypt (Linux esetén) használatával

- A Recovery Services-tároló csak az ugyanabban a régióban Azure Key Vault tárolóban tárolt **kulcsokkal titkosítható.** Emellett a kulcsoknak **csak RSA-kulcsoknak** kell lennie, és engedélyezett **állapotban kell** lennie.

- A CMK által titkosított Recovery Services-tárolók erőforráscsoportok és előfizetések közötti mozgatása jelenleg nem támogatott.
- Ha egy ügyfél által felügyelt kulcsokkal már titkosított Helyreállítási tárat áthelyez egy új bérlőbe, frissítenie kell a Recovery Services-tárolót, hogy újra létre tudja hozni és újrakonfigurálni tudja a tároló felügyelt identitását és CMK-ját (amelynek az új bérlőben kell lennie). Ha ez nem történik meg, a biztonsági mentési és visszaállítási műveletek sikertelenek lesznek. Emellett az előfizetésben beállított szerepköralapú hozzáférés-vezérlési (RBAC-) engedélyeket újra kell konfigurálni.

- Ez a szolgáltatás a powershell-Azure Portal konfigurálható.

    >[!NOTE]
    >Az Az 5.3.0-s vagy annál nagyobb modul használatával ügyfél által felügyelt kulcsokat használhat a helyreállítási tárban található biztonsági másolatok készítéséhez.
    
    >[!Warning]
    >Ha a PowerShellt használja a biztonsági mentés titkosítási kulcsának kezeléséhez, nem javasoljuk, hogy frissítse a kulcsokat a portálról.<br>Ha a portálról frissíti a kulcsot, nem használhatja a PowerShellt a titkosítási kulcs további frissítéséhez, amíg elérhetővé nem válik az új modellt támogató PowerShell-frissítés. A kulcsot azonban továbbra is frissítheti a Azure Portal.

Ha még nem hozta létre és konfigurálta a Recovery Services-tárolót, ezt itt [olvashatja el.](backup-create-rs-vault.md)

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Tároló konfigurálása ügyfél által kezelt kulcsokkal való titkosításhoz

Ez a szakasz a következő lépésekből áll:

1. Felügyelt identitás engedélyezése a Recovery Services-tárolóhoz

1. Rendeljen engedélyeket a tárolóhoz a titkosítási kulcs eléréséhez a Azure Key Vault

1. A törlés és végleges törlés elleni védelem engedélyezése a Azure Key Vault

1. A titkosítási kulcs hozzárendelése a Recovery Services-tárolóhoz

A kívánt eredmények eléréséhez a fenti sorrendben kell követni ezeket a lépéseket. Az egyes lépéseket az alábbiakban részletesen tárgyaljuk.

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Felügyelt identitás engedélyezése a Recovery Services-tárolóhoz

Azure Backup rendszer által hozzárendelt felügyelt identitások és felhasználó által hozzárendelt felügyelt identitások használatával hitelesíti a Recovery Services-tárolót a tárolóban tárolt titkosítási kulcsok Azure Key Vault. Ha engedélyezni szeretné a felügyelt identitást a Recovery Services-tárolóhoz, kövesse az alábbi lépéseket.

>[!NOTE]
>Az engedélyezése után a felügyelt identitást **nem lehet** letiltani (még ideiglenesen sem). A felügyelt identitás letiltása inkonzisztens viselkedéshez vezethet.

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése a tárolóhoz

**A portálon:**

1. Ugrás a Recovery Services-tárolóra – > **Identity**

    ![Identitásbeállítások](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. Lépjen a Rendszer által **hozzárendelt lapra.**

1. Módosítsa az **Állapotot Be** **állapotra.**

1. Kattintson **a Mentés gombra** a tároló identitásának engedélyezéséhez.

Létrejön egy objektumazonosító, amely a tároló rendszer által hozzárendelt felügyelt identitása.

>[!NOTE]
>Ha engedélyezve van, a felügyelt identitást nem lehet letiltani (még ideiglenesen sem). A felügyelt identitás letiltása inkonzisztens viselkedéshez vezethet.


**A PowerShell használatával:**

Az [Update-AzRecoveryServicesVault paranccsal](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) engedélyezheti a rendszer által hozzárendelt felügyelt identitást a Recovery Services-tárolóhoz.

Példa:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Kimenet:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-user-assigned-managed-identity-to-the-vault"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése a tárolóhoz

A felhasználó által hozzárendelt felügyelt identitás helyreállítási tárhoz való hozzárendelését a következő lépésekkel végezheti el:

1.  Ugrás a Recovery Services-tároló -> **Identityre**

    ![Felhasználó által hozzárendelt felügyelt identitás hozzárendelése a tárolóhoz](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  Lépjen a Felhasználó **által hozzárendelt lapra.**

1.  Felhasználó **által hozzárendelt** felügyelt identitás hozzáadásához kattintson a +Hozzáadás gombra.

1.  A **megnyíló Felhasználó által hozzárendelt felügyelt** identitás hozzáadása panelen válassza ki az identitás előfizetését.

1.  Válassza ki az identitást a listából. Az identitás vagy az erőforráscsoport neve alapján is szűrhet.

1.  Ha végzett, kattintson a **Hozzáadás gombra** az identitás hozzárendelésének befejezéséhez.

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Rendeljen engedélyeket a Recovery Services-tárolóhoz a titkosítási kulcs eléréséhez a Azure Key Vault

>[!Note]
>Ha felhasználó által hozzárendelt identitásokat használ, ugyanezeket az engedélyeket hozzá kell rendelni a felhasználó által hozzárendelt identitáshoz.

Most engedélyt kell adnunk a Recovery Services-tárolónak, hogy hozzáférjen Azure Key Vault titkosítási kulcsot tartalmazó tárolóhoz. Ez úgy történik, hogy lehetővé teszi, hogy a Recovery Services-tároló felügyelt identitása hozzáférjen a Key Vault.

**A portálon:**

1. A hozzáférési szabályzatok Azure Key Vault -> **meg.** Folytassa a **+Hozzáférési szabályzatok hozzáadása szaksokkal.**

    ![Hozzáférési szabályzatok hozzáadása](./media/encryption-at-rest-with-cmk/access-policies.png)

1. A **Kulcsengedélyek alatt válassza** a **Get**, **List**, **Unwrap Key** and Wrap Key operations (Kulcs kiírása és kulcs burkbaása) műveleteket.  Ez határozza meg a kulcson engedélyezett műveleteket.

    ![Kulcsengedélyek hozzárendelése](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Válassza a **Rendszerbiztonsági tag kiválasztása** lehetőséget, és keresse meg a tárolót a keresőmezőben a nevével vagy felügyelt identitásával. Miután megjelenik, válassza ki  a tárolót, majd válassza a Panel alján található Kijelölés lehetőséget.

    ![Rendszerbiztonsági tag kiválasztása](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Ha végzett, válassza a **Hozzáadás lehetőséget** az új hozzáférési szabályzat hozzáadásához.

1. A **hozzáférési szabályzatban** végrehajtott módosítások mentéshez válassza a Mentés Azure Key Vault.

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>A törlés és a végleges törlés elleni védelem engedélyezése a Azure Key Vault

Engedélyeznie kell **a titkosítási kulcsot** tároló Azure Key Vault és végleges törlés elleni védelmet. Ezt az alábbi módon Azure Key Vault felhasználói felületen. (Másik lehetőségként ezek a tulajdonságok is beállíthatók a Key Vault). Ezekről a tulajdonságokról további Key Vault itt [olvashat.](../key-vault/general/soft-delete-overview.md)

![Helyreállítható törlés és végleges törlés elleni védelem engedélyezése](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

A törlés és végleges törlés elleni védelmet a PowerShellen keresztül is engedélyezheti az alábbi lépések segítségével:

1. Jelentkezzen be az Azure-fiókjába.

    ```azurepowershell
    Login-AzAccount
    ```

1. Válassza ki a tárolót tartalmazó előfizetést.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Helyreállítható törlés engedélyezése

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Véglegesen kiürítés elleni védelem engedélyezése

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>Titkosítási kulcs hozzárendelése az RS-tárolóhoz

>[!NOTE]
> Mielőtt továbblépne, győződjön meg a következőkről:
>
> - A fent említett összes lépés sikeresen befejeződött:
>   - A Recovery Services-tároló felügyelt identitása engedélyezve van, és hozzá lett rendelve a szükséges engedélyekhez
>   - A Azure Key Vault a soft-delete és a purge-protection engedélyezve van
> - Az a Recovery Services-tároló, amelyhez  engedélyezni szeretné a CMK-titkosítást, nem rendelkezik egyetlen védett vagy regisztrált elemet sem

Ha a fentiek már biztosítottak, folytassa a tároló titkosítási kulcsának kiválasztásával.

### <a name="to-assign-the-key-in-the-portal"></a>A kulcs hozzárendelése a portálon

1. Ugrás a Recovery Services-tárolóra – > **tulajdonságok**

    ![Titkosítási beállítások](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. A **Titkosítási** beállítások **alatt válassza a Frissítés lehetőséget.**

1. A Titkosítási beállítások panelen válassza a **Saját** kulcs használata lehetőséget, és folytassa a kulcs megadását az alábbi módszerek egyikével. **Győződjön meg arról, hogy a használni kívánt kulcs egy RSA 2048-as kulcs, amely engedélyezett állapotban van.**

    1. Adja meg **a kulcs URI-ját,** amellyel a helyreállítási tár adatait titkosítani szeretné. Azt az előfizetést is meg kell adnia, amelyben a Azure Key Vault (amely tartalmazza ezt a kulcsot) jelen van. Ezt a kulcs URI-t a megfelelő kulcsból lehet beszerezni a Azure Key Vault. Győződjön meg arról, hogy a kulcs URI-ját helyesen másolódtak. Javasoljuk, hogy használja a **kulcsazonosítóhoz megadott Másolás** a vágólapra gombot.

        >[!NOTE]
        >Ha a kulcs URI-ját használva adja meg a titkosítási kulcsot, a kulcs nem lesz automatikusan elforgatva. Ezért a kulcsfrissítéseket manuálisan kell elvégezni az új kulcs megadásával, ha szükséges.

        ![Adja meg a kulcs URI-ét](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Tallózással válassza ki a kulcsot a Key Vault a kulcsválasztó panelen.

        >[!NOTE]
        >Amikor a kulcsválasztó panelen adja meg a titkosítási kulcsot, a kulcs automatikusan el lesz forgatva, amikor a kulcs új verziója engedélyezve van. [További információ a](#enabling-auto-rotation-of-encryption-keys) titkosítási kulcsok automatikus rotációja engedélyezéséről.

        ![Kulcs kiválasztása a Key Vaultból](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Kattintson a **Mentés** gombra.

1. **A titkosítási kulcs frissítésének** előrehaladása és állapota: A titkosítási  kulcs hozzárendelésének előrehaladását és állapotát a bal oldali navigációs sáv Biztonsági mentési feladatok nézetében követheti nyomon. Az állapotnak hamarosan **Completed (Befejezve) állapotra kell változnia.** A tároló most a megadott kulccsal fogja titkosítani az összes adatot KEK-ként.

    ![Állapot befejezve](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    A titkosítási kulcs frissítéseit a tároló tevékenységnaplójában is naplózza a rendszer.

    ![Tevékenységnapló](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>A kulcs hozzárendelése a PowerShell-rel

A [Set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) paranccsal engedélyezheti az ügyfél által kezelt kulcsokkal való titkosítást, és hozzárendelheti vagy frissítheti a használni szükséges titkosítási kulcsot.

Példa:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Kimenet:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> Ez a folyamat változatlan marad, ha frissíteni vagy módosítani szeretné a titkosítási kulcsot. Ha egy másik alkalmazás kulcsát szeretné frissíteni és Key Vault (az aktuálisan használttól eltérő kulcsot), győződjön meg a következőről:
>
> - A kulcstartó ugyanabban a régióban található, mint a Recovery Services-tároló
>
> - A kulcstartón engedélyezve van a soft-delete és a végleges törlés elleni védelem
>
> - A Recovery Services-tároló rendelkezik a kulcstartó eléréséhez szükséges engedélyekkel.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Biztonságimentés ügyfél által kezelt kulcsokkal titkosított tárolóba

A védelem konfigurálása előtt határozottan javasoljuk, hogy kövesse az alábbi ellenőrzőlistát. Ez azért fontos, mert miután konfigurálta az adott elemet úgy, hogy egy nem CMK-titkosítású tárolóba biztonságiítsa az elemet, a felhasználó által kezelt kulcsokkal való titkosítás nem engedélyezhető, és továbbra is platform által kezelt kulcsokat fog használni.

>[!IMPORTANT]
> Mielőtt továbblépne a védelem  konfigurálásán, sikeresen el kell végrehajtania a következő lépéseket:
>
>1. Létrehozta a Backup-tárolót
>1. Engedélyezte a Recovery Services-tároló rendszer által hozzárendelt felügyelt identitását, vagy hozzárendelt egy felhasználó által hozzárendelt felügyelt identitást a tárolóhoz
>1. Engedélyeket rendelt a Backup-tárolóhoz (vagy a felhasználó által hozzárendelt felügyelt identitáshoz) a titkosítási kulcsok eléréséhez a Key Vault
>1. Engedélyezett a törlés és a végleges törlés elleni védelem a Key Vault
>1. Érvényes titkosítási kulcs hozzárendelése a Backup-tárolóhoz
>
>Ha a fenti lépések mindegyikét megerősítette, csak akkor folytassa a biztonsági mentés konfigurálását.

A felhasználó által kezelt kulcsokkal titkosított Recovery Services-tárolók biztonsági mentésének és biztonsági mentésének folyamata megegyezik a platform által felügyelt kulcsokat használó tárolóval, a felhasználói élmény **változtatása nélkül.** Ez igaz az Azure-beli virtuális gépek biztonsági mentésére, valamint a virtuális gépen futó számítási feladatok (például SAP HANA [,](./tutorial-backup-sap-hana-db.md) [](./quick-backup-vm-portal.md) [SQL Server](./tutorial-sql-backup.md) biztonsági mentésére).

## <a name="restoring-data-from-backup"></a>Adatok visszaállítása biztonsági másolatból

### <a name="vm-backup"></a>Virtuális gép biztonsági mentése

A Recovery Services-tárolóban tárolt adatok az itt leírt lépések szerint állíthatók [vissza.](./backup-azure-arm-restore-vms.md) Amikor felhasználó által kezelt kulcsokkal titkosított helyreállítási tárból állítja vissza a visszaállítást, választhatja a visszaállított adatok titkosítását lemeztitkosítási készlettel (DES).

#### <a name="restoring-vm--disk"></a>Virtuális gép/lemez visszaállítása

1. Amikor helyreállítja a lemezt/virtuális gépet egy "pillanatkép" helyreállítási pontról, a visszaállított adatok titkosítása a forrás virtuális gép lemezének titkosításához használt DES-sel történik.

1. Ha olyan helyreállítási pontról állítja vissza a lemezt/virtuális gépet, amely helyreállítási típusa "Tároló", választhatja a visszaállított adatok DES használatával való titkosítását, amely a visszaállításkor van megadva. Másik lehetőségként folytathatja az adatok visszaállítását DES megadása nélkül is, amely esetben az adatok a Microsoft által felügyelt kulcsokkal lesznek titkosítva.

A visszaállított lemezt/virtuális gépet a visszaállítás befejezése után titkosíthatja, a visszaállítás elindításakor kiválasztott beállításoktól függetlenül.

![Visszaállítási pontok](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Lemeztitkosítási készlet kiválasztása a tároló helyreállítási pontjáról való visszaállítás során

**A portálon:**

A Lemeztitkosítási készlet a Visszaállítás panel Titkosítási beállítások panelén van megadva, az alábbi módon:

1. A Encrypt **disk(s) using your key (Lemezek titkosítása a kulccsal) beállításban válassza** az **Igen lehetőséget.**

1. A legördülő menüből válassza ki a visszaállított lemez(ök)hez használni kívánt DES-t. **Győződjön meg arról, hogy rendelkezik hozzáféréssel a DES-hez.**

>[!NOTE]
>A DES kiválasztása visszaállítás közben nem érhető el, ha olyan virtuális gépet állítja vissza, amely a Azure Disk Encryption.

![Lemez titkosítása a kulccsal](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**A PowerShell használatával:**

Használja a [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) parancsot a [ ] paraméterrel a visszaállított lemez titkosításához használni kívánt `-DiskEncryptionSetId <string>` [DES](/powershell/module/az.compute/get-azdiskencryptionset) megadásához. A lemezek virtuális gépek biztonsági másolatából való visszaállításával kapcsolatos további információkért tekintse meg [ezt a cikket.](./backup-azure-vms-automation.md#restore-an-azure-vm)

Példa:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>Fájlok visszaállítása

Fájl-visszaállítás végrehajtásakor a visszaállított adatok a célhely titkosításához használt kulccsal lesznek titkosítva.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Virtuális SAP HANA/SQL-adatbázisok visszaállítása

Egy Azure-beli virtuális gépen futó biztonsági SAP HANA/SQL-adatbázisból való visszaállításkor a visszaállított adatok titkosítása a célhelyen használt titkosítási kulccsal történik. Ez lehet egy ügyfél által felügyelt kulcs vagy egy platform által felügyelt kulcs, amely a virtuális gép lemezének titkosítására használatos.

## <a name="additional-topics"></a>További témakörök

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>Titkosítás engedélyezése ügyfél által kezelt kulcsokkal a tároló létrehozásakor (előzetes verzióban)

>[!NOTE]
>Az ügyfél által felügyelt kulcsokkal való kulcstartó-létrehozáskor a titkosítás engedélyezése korlátozott nyilvános előzetes verzióban érhető el, és lehetővé teszi az előfizetések listázását. Az előzetes verzióra való regisztrációhoz töltse ki az [űrlapot,](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) és írjon nekünk a következő e-képernyőn: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

Ha az előfizetése fel van sorolva, megjelenik a Backup Encryption (Biztonsági **mentés titkosítása)** lap. Ez lehetővé teszi a titkosítás engedélyezését az ügyfél által kezelt kulcsokkal a biztonsági másolaton egy új Recovery Services-tároló létrehozása során. A titkosítás engedélyezéséhez hajtsa végre a következő lépéseket:

1. Az Alapvető **beállítások lap** Biztonsági  mentés titkosítása lapján adja meg a titkosításhoz használni kívánt titkosítási kulcsot és identitást.

   ![Titkosítás engedélyezése tárolószinten](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >A beállítások csak a Biztonsági mentésre vonatkoznak, és nem kötelezőek.

1. Válassza **a Felhasználó által kezelt kulcs használata** titkosítási típust.

1. A titkosításhoz használni kívánt kulcs megadásához válassza a megfelelő beállítást.

   Meg kell adnia a titkosítási kulcs URI-ját, vagy tallózással kiválaszthatja a kulcsot. Ha a Select **the Key Vault beállítással** adja meg a kulcsot, a titkosítási kulcs automatikus rotációja automatikusan engedélyezi. [További információ az automatikus rotációról.](#enabling-auto-rotation-of-encryption-keys) 

1. Adja meg a felhasználó által hozzárendelt felügyelt identitást a felhasználó által kezelt kulcsokkal való titkosítás kezeléséhez. Kattintson **a Kijelölés gombra** a szükséges identitás tallózásához és kiválasztásához.

1. Ha végzett, adja hozzá a Címkéket (nem kötelező), és folytassa a tároló létrehozását.

### <a name="enabling-auto-rotation-of-encryption-keys"></a>A titkosítási kulcsok automatikus rotációja

Ha megadja a felhasználó által kezelt kulcsot, amely a biztonsági másolatok titkosításához használatos, a következő módszerekkel adhatja meg:

- Adja meg a kulcs URI-ét
- Válasszon a Key Vault

A Kiválasztás **a Key Vault** lehetőséggel engedélyezheti az automatikus rotációt a kiválasztott kulcshoz. Így nincs szükség a következő verzióra való manuális frissítésre. Ha azonban ezt a lehetőséget használja:
- A kulcsverzió frissítése akár egy órát is kihathat az életbe.
- A kulcs új verziójának életbe léptét követően a régi verziónak elérhetőnek kell lennie (engedélyezett állapotban) legalább egy további biztonsági mentési feladathoz a kulcsfrissítés életbe lépte után.

### <a name="using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview"></a>Az Azure-szabályzatok használata az ügyfél által kezelt kulcsokat használó titkosítás naplózásához és érvényesítéséhez (előzetes verzió)

Azure Backup lehetővé teszi az Azure-házirendek használatát a Recovery Services-tárolóban található adatok ügyfél által kezelt kulcsokkal való titkosításának naplózására és kényszerítésre. Az Azure-szabályzatok használata:

- A naplózási szabályzat használható a 2021. 04. 01. után engedélyezett, felhasználó által kezelt kulcsokat használó titkosítással rendelkező tárolók naplózása során. Az ezen dátum előtt engedélyezett CMK-titkosítással rendelkező tárolók esetében előfordulhat, hogy a házirend alkalmazása sikertelen, vagy téves negatív eredményeket mutat (vagyis előfordulhat, hogy ezek a tárolók nem megfelelőként lesznek jelentve, annak ellenére, hogy a **CMK-titkosítás** engedélyezve van).
- Ha 2021. 04. 01. előtt engedélyezni kell a **CMK-titkosítást** engedélyező tárolók naplózási házirendet, használja a Azure Portal titkosítási kulcs frissítéséhez. Ez segít az új modellre való frissítésben. Ha nem szeretné módosítani a titkosítási kulcsot, adja meg újra ugyanazt a kulcsot a kulcs URI-ján vagy a kulcsválasztási beállításon keresztül. 

   >[!Warning]
    >Ha a PowerShellt használja a biztonsági mentés titkosítási kulcsának kezeléséhez, nem javasoljuk, hogy frissítse a kulcsokat a portálról.<br>Ha a portálról frissíti a kulcsot, nem használhatja a PowerShellt a titkosítási kulcs további frissítéséhez, amíg elérhetővé nem válik az új modellt támogató PowerShell-frissítés. A kulcsot azonban továbbra is frissítheti a Azure Portal.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Titkosít tudok egy meglévő Backup-tárolót ügyfél által felügyelt kulcsokkal?

Nem, a CMK-titkosítás csak új tárolókhoz engedélyezhető. Így a tárolónak soha nem kellett védett elemet tartalmazni. Valójában nem kell megkísérelni a tároló elemeinek védelmét, mielőtt engedélyezték volna az ügyfél által kezelt kulcsokkal való titkosítást.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Megpróbáltam megvédeni egy elemet a tárolóm számára, de sikertelen volt, és a tároló még mindig nem tartalmaz védett elemeket. Engedélyezem a CMK-titkosítást ehhez a tárolóhoz?

Nem, a tárolónak korábban nem volt kísérlete a rá irányuló elemek védelmére.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Van egy CMK-titkosítást használó tárolóm. Később vissza tudokállni a titkosításra platform által felügyelt kulcsokkal, még akkor is, ha a tárolóban vannak védett biztonsági másolati elemek?

Nem, ha engedélyezte a CMK-titkosítást, nem lehet visszaállni a platform által kezelt kulcsok használatára. A használt kulcsokat az igényeinek megfelelően módosíthatja.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>A cmk-titkosítás Azure Backup vonatkozik a Azure Site Recovery?

Nem, ez a cikk csak a biztonsági mentési adatok titkosítását ismerteti. A Azure Site Recovery a tulajdonságot külön kell beállítania a szolgáltatásból elérhetőként.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Kihagytam a cikk egyik lépését, és az adatforrásom védelme érdekében maradtak. Továbbra is használhatok CMK-titkosítást?

Ha nem követi a cikkben található lépéseket, és nem folytatja az elemek védelmét, előfordulhat, hogy a tároló nem tudja használni a titkosítást az ügyfél által kezelt kulcsokkal. Ezért javasoljuk, hogy [](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) az elemek védelme előtt tekintse meg ezt az ellenőrzőlistát.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>A CMK-titkosítás használata hozzáadja a biztonsági másolatok költségét?

A CMK-titkosítás használata a biztonsági mentéshez nem jár többletköltségekkel. Előfordulhat azonban, hogy továbbra is költségekkel jár a Azure Key Vault, ahol a kulcs tárolva van.

## <a name="next-steps"></a>Következő lépések

- [A biztonsági szolgáltatások áttekintése a Azure Backup](security-overview.md)
