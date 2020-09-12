---
title: Biztonsági mentési adatai titkosítása az ügyfél által felügyelt kulcsokkal
description: Megtudhatja, hogyan titkosíthatja a biztonsági mentési adatait az ügyfél által felügyelt kulcsokkal (CMK) a Azure Backup segítségével.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 5c0bddc6cdb8ec150a031541ced1abf1ebfb6f0f
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378287"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Biztonsági mentési adatai titkosítása az ügyfél által felügyelt kulcsokkal

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

- Ez a funkció nem kapcsolódik a [Azure Disk Encryptionhoz](../security/fundamentals/azure-disk-encryption-vms-vmss.md), amely a virtuális gép lemezei számára a BitLocker (Windows rendszeren) és a dm-crypt (for Linux) rendszerhez készült vendég-alapú titkosítást használja.

- Az Recovery Services-tároló csak az **ugyanabban a régióban**található, Azure Key Vaultban tárolt kulcsokkal titkosítható. Emellett a kulcsok csak **RSA 2048 kulcsok** lehetnek, és **engedélyezve** állapotban kell lenniük.

- Az CMK titkosított Recovery Services tárolójának áthelyezése az erőforráscsoportok és az előfizetések között jelenleg nem támogatott.

- Ez a funkció jelenleg csak a Azure Portal konfigurálható.

Ha még nem hozta létre és konfigurálta a Recovery Services-tárolót, [olvassa el a következő témakört](backup-create-rs-vault.md):.

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Tároló konfigurálása az ügyfél által felügyelt kulcsok használatával történő titkosításhoz

Ez a szakasz a következő lépésekkel jár:

1. Felügyelt identitás engedélyezése a Recovery Services-tárolóban

1. Engedélyek kiosztása a tárolóhoz a Azure Key Vault található titkosítási kulcs eléréséhez

1. A Azure Key Vault eltávolításának és törlésének engedélyezése

1. A titkosítási kulcs kiosztása a Recovery Services-tárolóhoz

Szükség van arra, hogy az összes fenti lépést a fent említett sorrendben végezze el a kívánt eredmények eléréséhez. Az alábbiakban részletesen ismertetjük az egyes lépéseket.

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Felügyelt identitás engedélyezése a Recovery Services-tárolóban

Azure Backup a rendszerhez rendelt felügyelt identitás használatával hitelesíti a Recovery Services-tárolót a Azure Key Vault tárolt titkosítási kulcsok eléréséhez. Az Recovery Services-tároló felügyelt identitásának engedélyezéséhez kövesse az alábbi lépéseket.

>[!NOTE]
>Ha engedélyezve van, a felügyelt identitás **nem** tiltható le (akár átmenetileg is). A felügyelt identitás letiltása inkonzisztens viselkedést eredményezhet.

1. Ugrás a Recovery Services-tárolóra – > **identitás**

    ![Identitás beállításai](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. Módosítsa az **állapotot** **be értékre, majd** válassza a **Mentés**lehetőséget.

1. Létrejön egy objektumazonosító, amely a tároló rendszer által hozzárendelt felügyelt identitása.

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Engedélyek kiosztása a Recovery Services-tárolóhoz a titkosítási kulcs eléréséhez a Azure Key Vault

Most engedélyeznie kell a Recovery Services-tárolónak a titkosítási kulcsot tartalmazó Azure Key Vault elérését. Ezt úgy teheti meg, hogy engedélyezi a Recovery Services tár felügyelt identitását a Key Vault eléréséhez.

1. Nyissa meg a Azure Key Vault > **hozzáférési szabályzatokat**. Folytassa a **+ hozzáférési szabályzatok hozzáadásával**.

    ![Hozzáférési szabályzatok hozzáadása](./media/encryption-at-rest-with-cmk/access-policies.png)

1. A **legfontosabb engedélyek**területen válassza a **beolvasás**, **Listázás**, **kicsomagolás kulcs** és **betakarás** kulcs művelet lehetőséget. Ez határozza meg a kulcs engedélyezett műveleteit.

    ![Kulcs engedélyeinek kiosztása](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Lépjen a **rendszerbiztonsági tag kiválasztása elemre** , és keresse meg a tárolót a keresőmezőbe a nevét vagy a felügyelt identitását használva. Ha megjelenik, válassza ki a tárolót, és válassza a **kiválasztás** elemet a panel alján.

    ![Rendszerbiztonsági tag kiválasztása](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Ha elkészült, válassza a **Hozzáadás** lehetőséget az új hozzáférési szabályzat hozzáadásához.

1. Válassza a **Mentés** lehetőséget a Azure Key Vault hozzáférési házirendjében történt módosítások mentéséhez.

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>A Azure Key Vault eltávolításának és törlésének engedélyezése

Engedélyeznie kell a **Soft delete és Purge Protection** szolgáltatást a titkosítási kulcsot tároló Azure Key Vault. Ezt az alább látható Azure Key Vault felhasználói felületen végezheti el. (Másik lehetőségként ezek a tulajdonságok a Key Vault létrehozásakor is megadhatók.) További információt ezekről a Key Vault tulajdonságokról [itt](../key-vault/general/soft-delete-overview.md)talál.

![A Soft delete és a Purge Protection engedélyezése](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

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

### <a name="assign-encryption-key-to-the-rs-vault"></a>Titkosítási kulcs kiosztása az RS-tárolóhoz

>[!NOTE]
> A folytatás előtt győződjön meg a következőkről:
>
> - A fent említett lépések sikeresen befejeződtek:
>   - Az Recovery Services-tár felügyelt identitása engedélyezve lett, és hozzá van rendelve a szükséges engedélyek
>   - A Azure Key Vault a Soft-delete és a Purge-Protection engedélyezve van
> - Az a Recovery Services-tároló, amelynek engedélyezni szeretné a CMK titkosítását, **nem** rendelkezik védett vagy regisztrált elemmel

A fentiek betartását követően folytassa a tároló titkosítási kulcsának kiválasztásával.

A kulcs hozzárendeléséhez:

1. Ugrás a Recovery Services-tárolóra – > **tulajdonságai**

    ![Titkosítási beállítások](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Válassza a **frissítés** lehetőséget a **titkosítási beállítások**területen.

1. A titkosítási beállítások panelen válassza a **saját kulcs használata** lehetőséget, és folytassa a kulcs megadásával a következő módszerek egyikével. **Győződjön meg arról, hogy a használni kívánt kulcs egy RSA 2048-kulcs, amely engedélyezett állapotban van.**

    1. Adja meg azt a **kulcs-URI** -t, amellyel titkosítani szeretné a Recovery Services-tárolóban lévő összes értéket. Meg kell adnia azt az előfizetést is, amelyben a Azure Key Vault (amely tartalmazza ezt a kulcsot) megtalálható. Ezt a kulcs URI-t a Azure Key Vault tartozó kulcsból lehet beszerezni. Győződjön meg arról, hogy a kulcs URI-ja megfelelően lett másolva. Javasoljuk, hogy használja a másolás a **vágólapra** gombot a kulcs-azonosítóval.

        ![Adja meg a kulcs URI-JÁT](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Tallózással keresse meg és válassza ki a kulcsot a Key Vault a Key Picker ablaktáblán.

        ![Kulcs kiválasztása a Key vaultból](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Kattintson a **Mentés** gombra.

1. **A titkosítási kulcs frissítésének nyomon követési folyamata:** A kulcs hozzárendelésének előrehaladását a Recovery Services-tárolóban található **műveletnapló** használatával követheti nyomon. Az állapotnak hamarosan **sikeresre**kell váltania. A tár mostantól a megadott kulccsal titkosítja az összes adathalmazt.

    ![Előrehaladás követése a tevékenység naplójában](./media/encryption-at-rest-with-cmk/activity-log.png)

    ![Az állapot sikerült](./media/encryption-at-rest-with-cmk/status-succeeded.png)

>[!NOTE]
> Ez a folyamat változatlan marad, ha frissíteni vagy módosítani szeretné a titkosítási kulcsot. Ha egy másik Key Vault (az éppen jelenleg használttól eltérő) kulcsát szeretné frissíteni és használni, ügyeljen rá, hogy:
>
> - A Key Vault ugyanabban a régióban található, mint a Recovery Services-tároló
>
> - A Key vaultban engedélyezve van a Soft-delete és a Purge Protection
>
> - A Recovery Services-tároló rendelkezik a Key Vault eléréséhez szükséges engedélyekkel.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsokkal titkosított tárolóba való biztonsági mentés

Mielőtt továbblép a védelem konfigurálására, javasoljuk, hogy gondoskodjon a következő ellenőrzőlista betartásáról. Ez azért fontos, mert ha egy elem úgy lett konfigurálva, hogy biztonsági mentést lehessen készíteni (vagy konfigurálták azt) a nem CMK titkosított tárolóra, az ügyfél által felügyelt kulcsokat használó titkosítás nem engedélyezhető rajta, és továbbra is a platform által felügyelt kulcsokat fogja használni.

>[!IMPORTANT]
> A védelem konfigurálásának folytatása előtt **sikeresen** végre kell hajtania a következő lépéseket:
>
>1. Az előfizetés lehetővé teszi, hogy az ügyfél által felügyelt kulcsokat használja a Backup-tárolóhoz.
>1. A Backup-tároló létrehozása
>1. A biztonságimásolat-tároló rendszer által hozzárendelt felügyelt identitásának engedélyezése
>1. Hozzárendelt engedélyek a Backup-tárolóhoz a Key Vault lévő titkosítási kulcsok eléréséhez
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

A lemez titkosítási készletét a visszaállítás ablaktábla titkosítási beállítások területén, az alábbi ábrán látható módon kell megadni:

1. A **lemez (ek) titkosítása a kulcs használatával**válassza az **Igen**lehetőséget.

1. A legördülő listából válassza ki a visszaállított lemez (ek) hez használni kívánt DES-t. **Győződjön meg arról, hogy rendelkezik hozzáféréssel a DES-hez.**

>[!NOTE]
>A DES kiválasztásának lehetősége nem érhető el, ha Azure Disk Encryptiont használó virtuális gépet állít helyre.

![Lemez titkosítása a kulcs használatával](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

#### <a name="restoring-files"></a>Fájlok visszaállítása

A fájlok visszaállításának végrehajtásakor a visszaállított adatok a célhely titkosításához használt kulccsal lesznek titkosítva.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>SAP HANA/SQL-adatbázisok visszaállítása Azure-beli virtuális gépeken

Ha egy Azure-beli virtuális gépen futó biztonsági másolatból SAP HANA/SQL-adatbázisból végez visszaállítást, a visszaállított adatok titkosítása a cél tároló helyén használt titkosítási kulcs használatával történik. Lehet, hogy egy ügyfél által felügyelt kulcs vagy egy platform által felügyelt kulcs, amely a virtuális gép lemezének titkosítására szolgál.

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
