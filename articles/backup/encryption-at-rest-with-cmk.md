---
title: Biztonsági mentési adatok titkosítása ügyfelek által felügyelt kulcsok használatával
description: Megtudhatja, hogyan titkosíthatja a biztonsági mentési adatait az ügyfél által felügyelt kulcsokkal (CMK) a Azure Backup segítségével.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: b6cb1a288d0052b39bbeb52ed9fd20e68a6427ed
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167890"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Biztonsági mentési adatok titkosítása ügyfelek által felügyelt kulcsok használatával

Azure Backup lehetővé teszi, hogy az ügyfél által felügyelt kulcsok (CMK-EK) használatával Titkosítsa a biztonsági mentési adatait a platform által felügyelt kulcsok használata helyett, amely alapértelmezés szerint engedélyezve van. A biztonsági mentési adatai titkosításához használt kulcsokat [Azure Key Vault](../key-vault/index.yml)kell tárolni.

A biztonsági mentések titkosításához használt titkosítási kulcs különbözhet a forráshoz használttól. Az adatait egy AES 256-alapú adattitkosítási kulccsal (ADATTITKOSÍTÁSI kulcsot) védik, amely viszont a kulcsok (KEK) használatával védett. Ez teljes körű irányítást biztosít az adat és a kulcsok felett. A titkosítás engedélyezéséhez szükség van arra, hogy a Recovery Services-tároló hozzáférhessen a Azure Key Vault található titkosítási kulcshoz. A kulcsot igény szerint módosíthatja.

Ez a cikk a következőket ismerteti:

- Recovery Services-tároló létrehozása
- A Recovery Services-tároló konfigurálása a biztonsági másolatok titkosítására az ügyfél által felügyelt kulcsok használatával
- Az ügyfél által felügyelt kulcsok használatával titkosított biztonsági mentés végrehajtása a tárolók számára
- Adatok visszaállítása biztonsági másolatokból

## <a name="before-you-start"></a>Előkészületek

- Ez a funkció lehetővé teszi az **új Recovery Services**-tárolók titkosítását. Azok a tárolók, amelyek már regisztrálva vannak, vagy amelyeket regisztrálni próbáltak, nem támogatottak.

- Ha engedélyezte a Recovery Services-tárolót, az ügyfél által felügyelt kulcsokkal történő titkosítás nem állítható vissza a platform által felügyelt kulcsok használatára (alapértelmezett). A titkosítási kulcsokat a követelményeinek megfelelően módosíthatja.

- Ez a funkció jelenleg nem **támogatja a Mars-ügynök használatával történő biztonsági mentést**, és előfordulhat, hogy nem fogja tudni használni a CMK-titkosítású tárolót. A MARS-ügynök felhasználói jelszó-alapú titkosítást használ. A szolgáltatás emellett nem támogatja a klasszikus virtuális gépek biztonsági mentését.

- Ez a funkció nem kapcsolódik a [Azure Disk Encryptionhoz](../security/fundamentals/azure-disk-encryption-vms-vmss.md), amely a virtuális gép lemezei a BitLocker (Windows) és a DM-Crypt (Linux rendszerhez) rendszerhez készült vendég-alapú titkosítását használják.

- Az Recovery Services-tároló csak az **ugyanabban a régióban** található, Azure Key Vaultban tárolt kulcsokkal titkosítható. Emellett a kulcsoknak csak **RSA-kulcsoknak** kell lenniük, és **engedélyezve** állapotban kell lenniük.

- Az CMK titkosított Recovery Services tárolójának áthelyezése az erőforráscsoportok és az előfizetések között jelenleg nem támogatott.
- Ha olyan Recovery Services-tárolót helyez át, amely már titkosítva van az ügyfél által felügyelt kulcsokkal egy új bérlő számára, akkor frissítenie kell a Recovery Services-tárolót a tár felügyelt identitásának és CMK újbóli létrehozásához és újrakonfigurálásához (amelynek az új Bérlőnek kell lennie). Ha ez nem történik meg, a biztonsági mentési és visszaállítási műveletek sikertelenek lesznek. Az előfizetésen belül beállított szerepköralapú hozzáférés-vezérlési (RBAC) engedélyeket is újra kell konfigurálni.

- Ez a funkció a Azure Portal és a PowerShell használatával is konfigurálható.

    >[!NOTE]
    >Az az Module 5.3.0 vagy a more paranccsal használhatja az ügyfél által felügyelt kulcsokat a Recovery Services-tárolóban található biztonsági mentésekhez.
    
    >[!Warning]
    >Ha a PowerShellt használja a biztonsági mentéshez használt titkosítási kulcsok kezelésére, nem javasoljuk, hogy frissítse a kulcsokat a portálról.<br></br>Ha frissíti a kulcsot a portálról, a PowerShell használatával nem frissítheti tovább a titkosítási kulcsot, amíg az új modellt támogató PowerShell-frissítés elérhetővé válik. Azonban továbbra is frissítheti a kulcsot a Azure Portal.

Ha még nem hozta létre és konfigurálta a Recovery Services-tárolót, [olvassa el a következő témakört](backup-create-rs-vault.md):.

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Tároló konfigurálása az ügyfél által felügyelt kulcsok használatával történő titkosításhoz

Ez a szakasz a következő lépésekkel jár:

1. Felügyelt identitás engedélyezése a Recovery Services-tárolóban

1. Engedélyek kiosztása a tárolóhoz a Azure Key Vault található titkosítási kulcs eléréséhez

1. A Azure Key Vault eltávolításának és törlésének engedélyezése

1. A titkosítási kulcs kiosztása a Recovery Services-tárolóhoz

Szükség van arra, hogy az összes fenti lépést a fent említett sorrendben végezze el a kívánt eredmények eléréséhez. Az alábbiakban részletesen ismertetjük az egyes lépéseket.

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Felügyelt identitás engedélyezése a Recovery Services-tárolóban

A Azure Backup rendszerhez rendelt felügyelt identitásokat és felhasználó által hozzárendelt felügyelt identitásokat használ a Recovery Services-tároló hitelesítéséhez a Azure Key Vault tárolt titkosítási kulcsok eléréséhez. Az Recovery Services-tároló felügyelt identitásának engedélyezéséhez kövesse az alábbi lépéseket.

>[!NOTE]
>Ha engedélyezve van, a felügyelt identitás **nem** tiltható le (akár átmenetileg is). A felügyelt identitás letiltása inkonzisztens viselkedést eredményezhet.

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>A rendszerhez rendelt felügyelt identitás engedélyezése a tárolóhoz

**A portálon:**

1. Ugrás a Recovery Services-tárolóra – > **identitás**

    ![Identitás beállításai](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. Navigáljon a **rendszerhez rendelt** lapra.

1. Módosítsa az **állapotot** **a** következőre:.

1. A tároló identitásának engedélyezéséhez kattintson a **Mentés** gombra.

Létrejön egy objektumazonosító, amely a tároló rendszer által hozzárendelt felügyelt identitása.

>[!NOTE]
>Ha engedélyezve van, a felügyelt identitás nem tiltható le (akár átmenetileg is). A felügyelt identitás letiltása inkonzisztens viselkedést eredményezhet.


**PowerShell-lel:**

Az [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) parancs használatával engedélyezheti a rendszerhez rendelt felügyelt identitást a Recovery Services-tárolóhoz.

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

A Recovery Services-tárolóhoz tartozó, felhasználóhoz rendelt felügyelt identitás hozzárendeléséhez hajtsa végre a következő lépéseket:

1.  Ugrás a Recovery Services-tárolóra – > **identitás**

    ![Felhasználó által hozzárendelt felügyelt identitás hozzárendelése a tárolóhoz](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  Navigáljon a **felhasználóhoz rendelt** lapra.

1.  A felhasználó által hozzárendelt felügyelt identitás hozzáadásához kattintson a **+ Hozzáadás** gombra.

1.  A megnyíló **felhasználóhoz rendelt felügyelt identitás hozzáadása** panelen válassza ki az identitásához tartozó előfizetést.

1.  Válassza ki az identitást a listából. Azt is megteheti, hogy az identitás vagy az erőforráscsoport neve alapján szűri.

1.  Ha elkészült, kattintson a **Hozzáadás** gombra az identitás hozzárendelésének befejezéséhez.

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Engedélyek kiosztása a Recovery Services-tárolóhoz a titkosítási kulcs eléréséhez a Azure Key Vault

>[!Note]
>Ha felhasználó által hozzárendelt identitásokat használ, ugyanazokat az engedélyeket kell hozzárendelni a felhasználó által hozzárendelt identitáshoz.

Most engedélyeznie kell a Recovery Services-tárolónak a titkosítási kulcsot tartalmazó Azure Key Vault elérését. Ezt úgy teheti meg, hogy engedélyezi a Recovery Services tár felügyelt identitását a Key Vault eléréséhez.

**A portálon**:

1. Nyissa meg a Azure Key Vault > **hozzáférési szabályzatokat**. Folytassa a **+ hozzáférési szabályzatok hozzáadásával**.

    ![Hozzáférési szabályzatok hozzáadása](./media/encryption-at-rest-with-cmk/access-policies.png)

1. A **legfontosabb engedélyek** területen válassza a **beolvasás**, **Listázás**, **kicsomagolás kulcs** és **betakarás** kulcs művelet lehetőséget. Ez határozza meg a kulcs engedélyezett műveleteit.

    ![Kulcs engedélyeinek kiosztása](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Lépjen a **rendszerbiztonsági tag kiválasztása elemre** , és keresse meg a tárolót a keresőmezőbe a nevét vagy a felügyelt identitását használva. Ha megjelenik, válassza ki a tárolót, és válassza a **kiválasztás** elemet a panel alján.

    ![Rendszerbiztonsági tag kiválasztása](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Ha elkészült, válassza a **Hozzáadás** lehetőséget az új hozzáférési szabályzat hozzáadásához.

1. Válassza a **Mentés** lehetőséget a Azure Key Vault hozzáférési házirendjében történt módosítások mentéséhez.

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>A Azure Key Vault eltávolításának és törlésének engedélyezése

Engedélyeznie kell a **Soft delete és Purge Protection** szolgáltatást a titkosítási kulcsot tároló Azure Key Vault. Ezt az alább látható Azure Key Vault felhasználói felületen végezheti el. (Másik lehetőségként ezek a tulajdonságok a Key Vault létrehozásakor is megadhatók.) További információt ezekről a Key Vault tulajdonságokról [itt](../key-vault/general/soft-delete-overview.md)talál.

![Helyreállítható törlés és végleges törlés elleni védelem engedélyezése](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

A következő lépésekkel engedélyezheti a helyreállítható törlési és-kiürítési védelmet is a PowerShell használatával:

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

1. Törlési védelem engedélyezése

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>Titkosítási kulcs kiosztása az RS-tárolóhoz

>[!NOTE]
> A folytatás előtt győződjön meg a következőkről:
>
> - A fent említett lépések sikeresen befejeződtek:
>   - Az Recovery Services-tár felügyelt identitása engedélyezve lett, és hozzá van rendelve a szükséges engedélyek
>   - A Azure Key Vault a Soft-delete és a Purge-Protection engedélyezve van
> - Az a Recovery Services-tároló, amelynek engedélyezni szeretné a CMK titkosítását, **nem** rendelkezik védett vagy regisztrált elemmel

A fentiek betartását követően folytassa a tároló titkosítási kulcsának kiválasztásával.

### <a name="to-assign-the-key-in-the-portal"></a>A kulcs kiosztása a portálon

1. Ugrás a Recovery Services-tárolóra – > **tulajdonságai**

    ![Titkosítási beállítások](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Válassza a **frissítés** lehetőséget a **titkosítási beállítások** területen.

1. A titkosítási beállítások panelen válassza a **saját kulcs használata** lehetőséget, és folytassa a kulcs megadásával a következő módszerek egyikével. **Győződjön meg arról, hogy a használni kívánt kulcs egy RSA 2048-kulcs, amely engedélyezett állapotban van.**

    1. Adja meg azt a **kulcs-URI** -t, amellyel titkosítani szeretné a Recovery Services-tárolóban lévő összes értéket. Meg kell adnia azt az előfizetést is, amelyben a Azure Key Vault (amely tartalmazza ezt a kulcsot) megtalálható. Ezt a kulcs URI-t a Azure Key Vault tartozó kulcsból lehet beszerezni. Győződjön meg arról, hogy a kulcs URI-ja megfelelően lett másolva. Javasoljuk, hogy használja a másolás a **vágólapra** gombot a kulcs-azonosítóval.

        >[!NOTE]
        >A kulcs URI azonosítójának használatával a titkosítási kulcs megadásakor a kulcs nem lesz automatikusan elforgatva. A legfontosabb frissítéseket manuálisan kell elvégezni, ha szükség van az új kulcs megadásával.

        ![Adja meg a kulcs URI-JÁT](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Tallózással keresse meg és válassza ki a kulcsot a Key Vault a Key Picker ablaktáblán.

        >[!NOTE]
        >Ha a Key Picker panelen megadja a titkosítási kulcsot, a rendszer automatikusan elforgatja a kulcsot, amikor a kulcs új verziója engedélyezve van. [További információ](#enabling-auto-rotation-of-encryption-keys) a titkosítási kulcsok automatikus rotációjának engedélyezéséről.

        ![Kulcs kiválasztása a Key vaultból](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Kattintson a **Mentés** gombra.

1. A **titkosítási kulcs frissítésének előrehaladása és állapota**: a titkosítási kulcs hozzárendelésének előrehaladását és állapotát a bal oldali navigációs sávon a **biztonsági mentési feladatok** nézet használatával követheti nyomon. Az állapotnak hamarosan **Befejezettre** kell váltania. A tár mostantól a megadott kulccsal titkosítja az összes adathalmazt.

    ![Az állapot befejeződött](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    A titkosítási kulcs frissítéseit a rendszer a tár tevékenységi naplójában is naplózza.

    ![Tevékenységnapló](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>A kulcs társítása a PowerShell-lel

A [set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) parancs használatával engedélyezheti a titkosítást az ügyfél által felügyelt kulcsokkal, valamint a használni kívánt titkosítási kulcs hozzárendelését vagy frissítését.

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
> Ez a folyamat változatlan marad, ha frissíteni vagy módosítani szeretné a titkosítási kulcsot. Ha egy másik Key Vault (az éppen jelenleg használttól eltérő) kulcsát szeretné frissíteni és használni, ügyeljen rá, hogy:
>
> - A Key Vault ugyanabban a régióban található, mint a Recovery Services-tároló
>
> - A Key vaultban engedélyezve van a Soft-delete és a Purge Protection
>
> - A Recovery Services-tároló rendelkezik a kulcstartó eléréséhez szükséges engedélyekkel.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsokkal titkosított tárolóba való biztonsági mentés

Mielőtt továbblép a védelem konfigurálására, javasoljuk, hogy gondoskodjon a következő ellenőrzőlista betartásáról. Ez azért fontos, mert ha egy elem úgy lett konfigurálva, hogy biztonsági mentést lehessen készíteni (vagy konfigurálták azt) a nem CMK titkosított tárolóra, az ügyfél által felügyelt kulcsokat használó titkosítás nem engedélyezhető rajta, és továbbra is a platform által felügyelt kulcsokat fogja használni.

>[!IMPORTANT]
> A védelem konfigurálásának folytatása előtt **sikeresen** végre kell hajtania a következő lépéseket:
>
>1. A Backup-tároló létrehozása
>1. Engedélyezte a Recovery Services tár rendszer által hozzárendelt felügyelt identitását, vagy hozzárendelt egy felhasználó által hozzárendelt felügyelt identitást a tárolóhoz
>1. Hozzárendelt engedélyek a Backup-tárolóhoz (vagy a felhasználóhoz rendelt felügyelt identitáshoz) a Key Vault található titkosítási kulcsok eléréséhez
>1. A Key Vault törlésének engedélyezése és a védelem kiürítése
>1. Érvényes titkosítási kulcsot rendelt a Backup-tárolóhoz
>
>Ha a fenti lépések mindegyikét megerősítettük, csak ezután folytassa a biztonsági mentés konfigurálását.

Az ügyfél által felügyelt kulcsokkal titkosított Recovery Services-tárolóra vonatkozó biztonsági másolatok konfigurálásának és végrehajtásának folyamata megegyezik a platform által felügyelt kulcsokat használó tárolóval, és **nem változik a felhasználói élmény**. Ez igaz az Azure-beli [virtuális gépek biztonsági mentésére](./quick-backup-vm-portal.md) , valamint a virtuális gépen futó munkaterhelések biztonsági mentésére (például [SAP HANA](./tutorial-backup-sap-hana-db.md), [SQL Server](./tutorial-sql-backup.md) adatbázisokra).

## <a name="restoring-data-from-backup"></a>Adatok visszaállítása biztonsági másolatból

### <a name="vm-backup"></a>Virtuális gép biztonsági mentése

Az Recovery Services-tárolóban tárolt információk az [itt](./backup-azure-arm-restore-vms.md)ismertetett lépések szerint állíthatók vissza. Az ügyfél által felügyelt kulcsokkal titkosított Recovery Services-tárolóból történő visszaállítás esetén dönthet úgy, hogy titkosítja a visszaállított adatok lemezes titkosítási készlettel (DES).

#### <a name="restoring-vm--disk"></a>Virtuális gép/lemez visszaállítása

1. Amikor helyreállítja a lemez/virtuális gépet egy "pillanatkép" helyreállítási pontról, a visszaállított adatok titkosítva lesznek a forrás virtuális gép lemezének titkosításához használt DES-mel.

1. Ha a lemez/virtuális gép helyreállítási pontról a "Vault" helyreállítási típussal van helyreállítva, beállíthatja, hogy a visszaállított adatok a visszaállítás időpontjában megadott DES használatával legyenek titkosítva. Másik lehetőségként dönthet úgy is, hogy folytatja az adatvisszaállítást a DES megadása nélkül, ebben az esetben a Microsoft által felügyelt kulcsokkal titkosítva lesz.

A visszaállítás befejezése után titkosíthatja a visszaállított lemezt/virtuális gépet, függetlenül attól, hogy milyen kijelölés történt a visszaállítás kezdeményezése során.

![Visszaállítási pontok](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Lemezes titkosítási csoport kiválasztása a tár helyreállítási pontjából való visszaállításkor

**A portálon**:

A lemez titkosítási készletét a visszaállítás ablaktábla titkosítási beállítások területén, az alábbi ábrán látható módon kell megadni:

1. A **lemez (ek) titkosítása a kulcs használatával** válassza az **Igen** lehetőséget.

1. A legördülő listából válassza ki a visszaállított lemez (ek) hez használni kívánt DES-t. **Győződjön meg arról, hogy rendelkezik hozzáféréssel a DES-hez.**

>[!NOTE]
>A DES kiválasztásának lehetősége nem érhető el, ha Azure Disk Encryptiont használó virtuális gépet állít helyre.

![Lemez titkosítása a kulcs használatával](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**PowerShell**-lel:

Használja a [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) parancsot a (z) [] paraméterrel a `-DiskEncryptionSetId <string>` visszaállított lemez TITKOSÍTÁSához használandó [des megadásához](/powershell/module/az.compute/get-azdiskencryptionset) . További információ a virtuális gépek biztonsági mentéséről a lemezek visszaállításáról: [ebben a cikkben](./backup-azure-vms-automation.md#restore-an-azure-vm).

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

A fájlok visszaállításának végrehajtásakor a visszaállított adatok a célhely titkosításához használt kulccsal lesznek titkosítva.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>SAP HANA/SQL-adatbázisok visszaállítása Azure-beli virtuális gépeken

Ha egy Azure-beli virtuális gépen futó biztonsági másolatból SAP HANA/SQL-adatbázisból végez visszaállítást, a visszaállított adatok titkosítása a cél tároló helyén használt titkosítási kulcs használatával történik. Lehet, hogy egy ügyfél által felügyelt kulcs vagy egy platform által felügyelt kulcs, amely a virtuális gép lemezének titkosítására szolgál.

## <a name="additional-topics"></a>További témakörök

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>Titkosítás engedélyezése az ügyfél által felügyelt kulcsok használatával a tár létrehozásakor (előzetes verzió)

>[!NOTE]
>Az ügyfél által felügyelt kulcsokkal rendelkező tárolók létrehozásakor a titkosítás engedélyezése korlátozott nyilvános előzetes verzióban érhető el, és engedélyezni kell az előfizetések listáját. Az előzetes verzióra való feliratkozáshoz töltse ki az [űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) , és írjon nekünk a következő címen: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

Ha az előfizetés engedélyezett, a **biztonsági mentés titkosítása** lap jelenik meg. Ez lehetővé teszi, hogy a biztonsági mentés titkosítását az ügyfél által felügyelt kulcsok használatával engedélyezze új Recovery Services-tároló létrehozásakor. A titkosítás engedélyezéséhez hajtsa végre a következő lépéseket:

1. Az **alapbeállítások** lap **biztonsági másolat titkosítása** lapján határozza meg a titkosítási kulcsot és a titkosításhoz használandó identitást.

   ![Titkosítás engedélyezése a tár szintjén](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >A beállítások csak a biztonsági mentésre vonatkoznak, és nem kötelező.

1. Válassza az **ügyfél által felügyelt kulcs használata** titkosítási típusként lehetőséget.

1. A titkosításhoz használandó kulcs megadásához válassza ki a megfelelő beállítást.

   Megadhatja a titkosítási kulcs URI-JÁT, vagy megkeresheti és kiválaszthatja a kulcsot. Amikor megadja a kulcsot a **Key Vault kiválasztása** lehetőséggel, a titkosítási kulcs automatikus rotációja automatikusan engedélyezve lesz. [További információ az automatikus rotációs](#enabling-auto-rotation-of-encryption-keys)szolgáltatásról. 

1. Az ügyfél által felügyelt kulcsokkal történő titkosítás kezeléséhez válassza a felhasználó által hozzárendelt felügyelt identitást. Kattintson a **kiválasztás** elemre a kívánt identitás tallózásához és kiválasztásához.

1. Ha elkészült, folytassa a címkék hozzáadásával (nem kötelező) és a tár létrehozásával.

### <a name="enabling-auto-rotation-of-encryption-keys"></a>Titkosítási kulcsok automatikus rotációjának engedélyezése

Ha a biztonsági másolatok titkosításához használni kívánt ügyfél által felügyelt kulcsot ad meg, a következő módszerekkel adhatja meg:

- Adja meg a kulcs URI-JÁT
- Kiválasztás Key Vault

A **select from Key Vault** lehetőség használatával engedélyezheti a kijelölt kulcs automatikus elforgatását. Ezzel kiküszöbölheti a manuális erőfeszítést a következő verzióra való frissítéshez. Azonban használja ezt a lehetőséget:
- A kulcs verziófrissítése akár egy órát is igénybe vehet.
- Ha a kulcs új verziója lép érvénybe, a régi verziónak (engedélyezett állapotban) is elérhetőnek kell lennie legalább egy további biztonsági mentési feladatokhoz a kulcs frissítésének érvénybe lépését követően.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Titkosíthatók a meglévő Backup-tárolók az ügyfél által felügyelt kulcsokkal?

Nem, a CMK-titkosítás csak az új tárolók esetében engedélyezhető. Így a tárolónak soha nem volt védett eleme. Valójában az ügyfél által felügyelt kulcsokkal történő titkosítás engedélyezése előtt nem kell megadnia a tár összes elemének védelmi kísérletét.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Megpróbáltam védelemmel ellátni egy elemet a tárolóban, de nem sikerült, és a tár továbbra sem tartalmaz védett elemeket. Engedélyezhető a CMK titkosítás ehhez a tárolóhoz?

Nem, a tárolónak még nem volt lehetősége arra, hogy a múltban egyetlen elemet se lehessen védelemmel ellátni.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Van egy CMK-titkosítást használó tároló. Később is visszaállíthatók a titkosítás a platform által felügyelt kulcsokkal, még akkor is, ha a biztonsági másolati elemek védettek a tárolóban?

Nem, miután engedélyezte a CMK titkosítást, nem lehet visszaállítani a platform által felügyelt kulcsok használatára. Az igényeinek megfelelően módosíthatja a kulcsok használatát.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>A CMK titkosítása Azure Backup is vonatkozik a Azure Site Recoveryra?

Nem, ez a cikk a biztonsági mentési adatmennyiség titkosítását tárgyalja. Azure Site Recovery esetén a tulajdonságot külön kell beállítani a szolgáltatásban elérhetőként.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Kihagytam a cikkben szereplő lépések egyikét, és folytattam az adatforrások védelme érdekében. Továbbra is használhatom a CMK titkosítást?

Nem követi a cikkben szereplő lépéseket, és az elemek védelemének folytatása azt eredményezheti, hogy a tároló nem tudja használni a titkosítást az ügyfél által felügyelt kulcsok használatával. Ezért javasoljuk, hogy tekintse meg [ezt az ellenőrzőlistát](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) , mielőtt továbblép az elemek védelemmel.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>A CMK-titkosítást használ a biztonsági mentések költségeit?

A CMK titkosítás használata a biztonsági mentéshez nem jár további költségekkel. Azonban továbbra is felmerülhetnek a költségek arra, hogy a Azure Key Vault, ahol a kulcsot tárolják.

## <a name="next-steps"></a>Következő lépések

- [A Azure Backup biztonsági funkcióinak áttekintése](security-overview.md)
