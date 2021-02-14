---
title: Ügyfél által felügyelt kulcsok konfigurálása a Azure Batch-fiókhoz Azure Key Vault és felügyelt identitással
description: Megtudhatja, hogyan titkosíthatja a Batch-információkat az ügyfél által felügyelt kulcsokkal.
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: d3f10436b95aaeb5eb35a873c2a3862c1492bd47
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385064"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Ügyfél által felügyelt kulcsok konfigurálása a Azure Batch-fiókhoz Azure Key Vault és felügyelt identitással

Alapértelmezés szerint a Azure Batch platform által felügyelt kulcsokkal titkosítja az Azure Batch szolgáltatásban tárolt összes ügyféladatokat, például a tanúsítványokat, a feladatok/feladatok metaadatait. Igény szerint használhatja a saját kulcsait, például az ügyfél által felügyelt kulcsokat a Azure Batchban tárolt adattitkosításhoz.

Az Ön által megadott kulcsokat [Azure Key Vault](../key-vault/general/basic-concepts.md)kell létrehoznia, és az [Azure-erőforrások felügyelt identitásával](../active-directory/managed-identities-azure-resources/overview.md)kell elérni őket.

A felügyelt identitások két típusa létezik: [ *rendszerhez rendelt* és *felhasználó által hozzárendelt*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

Létrehozhatja a Batch-fiókját a rendszer által hozzárendelt felügyelt identitással, vagy létrehozhat egy külön felhasználó által hozzárendelt felügyelt identitást, amely hozzáfér az ügyfél által felügyelt kulcsokhoz. Az [összehasonlítási táblázat](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) áttekintésével megismerheti a különbségeket, és megvizsgálhatja, hogy melyik lehetőség a legmegfelelőbb a megoldásához. Ha például ugyanazt a felügyelt identitást szeretné használni több Azure-erőforrás eléréséhez, a felhasználó által hozzárendelt felügyelt identitásra lesz szükség. Ha nem, akkor lehet, hogy a Batch-fiókhoz társított, rendszerhez rendelt felügyelt identitás elegendő. A felhasználó által hozzárendelt felügyelt identitás használatával a Batch-fiókok létrehozásakor is kikényszerítheti az ügyfél által felügyelt kulcsokat [az alábbi példában](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys)látható módon.

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Batch-fiók létrehozása rendszer által hozzárendelt felügyelt identitással

Ha nincs szüksége külön felhasználó által hozzárendelt felügyelt identitásra, a Batch-fiók létrehozásakor engedélyezheti a rendszerhez rendelt felügyelt identitást.

### <a name="azure-portal"></a>Azure Portal

Ha a [Azure Portal](https://portal.azure.com/)batch-fiókokat hoz létre, válassza a **speciális** lapon az Identity (identitás) elemnél **hozzárendelt rendszer** kiválasztása értéket.

![Képernyőfelvétel egy új batch-fiókról a rendszerhez rendelt identitás típussal.](./media/batch-customer-managed-key/create-batch-account.png)

A fiók létrehozása után egy egyedi GUID-azonosítót talál a **Tulajdonságok** szakasz **identitás egyszerű azonosító** mezőjében. Megjelenik az **identitás típusa** `System assigned` .

![Képernyőfelvétel: egyedi GUID-azonosító az identitás elsődleges azonosítója mezőben.](./media/batch-customer-managed-key/linked-batch-principal.png)

Erre az értékre szüksége lesz ahhoz, hogy a Batch-fiók hozzáférhessen a Key Vaulthoz.

### <a name="azure-cli"></a>Azure CLI

Új batch-fiók létrehozásakor meg kell adni `SystemAssigned` a `--identity` paramétert.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

A fiók létrehozása után ellenőrizheti, hogy engedélyezve van-e a rendszerhez rendelt felügyelt identitás ezen a fiókon. Ügyeljen arra, hogy a `PrincipalId` , mivel ez az érték szükséges ahhoz, hogy a Batch-fiók hozzáférhessen a Key Vaulthoz.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> A Batch-fiókban létrehozott, rendszerhez rendelt felügyelt identitás csak az ügyfél által felügyelt kulcsok lekérésére szolgál a Key Vault. Ez az identitás nem érhető el a Batch-készleteken. Ha felhasználó által hozzárendelt felügyelt identitást szeretne használni a készletben, lásd: [felügyelt identitások konfigurálása a Batch-készletekben](managed-identity-pools.md).

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Ha szeretné, [létrehozhat egy felhasználó által hozzárendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) , amely használható az ügyfél által felügyelt kulcsok eléréséhez.

Ahhoz, hogy hozzáférhessen a Key Vaulthoz, szüksége lesz az identitás **ügyfél-azonosító** értékére.

## <a name="configure-your-azure-key-vault-instance"></a>Az Azure Key Vault-példány konfigurálása

A kulcsok generálásának Azure Key Vault a Batch-fiókkal megegyező bérlőn kell létrehozni. Nem kell ugyanabban az erőforráscsoporthoz lennie, vagy akár ugyanahhoz az előfizetéshez sem kell lennie.

### <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

Ha Azure Batch ügyfél által felügyelt kulcsokkal rendelkező [Azure Key Vault-példányt hoz létre](../key-vault/general/quick-create-portal.md) , győződjön meg arról, hogy a helyreállítható **törlési** és **törlési védelem** egyaránt engedélyezve van.

![Képernyőkép a Key Vault létrehozási képernyőjéről.](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Hozzáférési szabályzat hozzáadása az Azure Key Vault-példányhoz

A Key Vault létrehozása után a Azure Portal a **hozzáférési házirendben** a **beállítás** területen adja hozzá a Batch-fiókhoz való hozzáférést a felügyelt identitás használatával. A **kulcs engedélyei** területen válassza a **beolvasás**, **kulcs** és **kicsomagolás** kulcs elemet.

![A hozzáférési házirend hozzáadása képernyőt ábrázoló képernyőfelvétel.](./media/batch-customer-managed-key/key-permissions.png)

Az **elsődleges** területen a **Select (kiválasztás** ) mezőben adja meg a következők egyikét:

- Rendszer által hozzárendelt felügyelt identitás esetén: adja meg a `principalId` korábban lekért vagy a Batch-fiók nevét.
- Felhasználó által hozzárendelt felügyelt identitás esetén: adja meg a korábban beolvasott **ügyfél-azonosítót** vagy a felhasználó által hozzárendelt felügyelt identitás nevét.

![Képernyőfelvétel a fő képernyőről.](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Kulcs létrehozása Azure Key Vault

A Azure Portal lépjen a **kulcs** szakaszban található Key Vault példányra, majd válassza a **Létrehozás/importálás** lehetőséget. Válassza ki a kívánt **kulcsot** , `RSA` és az **RSA-kulcs méretének** legalább `2048` bitenek kell lennie. `EC` a Key types jelenleg nem támogatott ügyfél által felügyelt kulcsként egy batch-fiókban.

![Kulcs létrehozása](./media/batch-customer-managed-key/create-key.png)

A kulcs létrehozása után kattintson az újonnan létrehozott kulcsra és a jelenlegi verzióra, másolja a **kulcs azonosítóját** a **Properties (Tulajdonságok** ) szakaszban.  Győződjön meg róla, hogy az **engedélyezett műveletek** alatt a **wrap Key** és a **dewrap Key** is be van jelölve.

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Ügyfél által felügyelt kulcsok engedélyezése batch-fiókon

Ha követte a fenti lépéseket, engedélyezheti az ügyfél által felügyelt kulcsokat a Batch-fiókjában.

### <a name="azure-portal"></a>Azure Portal

A [Azure Portal](https://portal.azure.com/)nyissa meg a Batch-fiók lapot. A **titkosítás** szakaszban engedélyezze az **ügyfél által felügyelt kulcsot**. Közvetlenül használhatja a kulcs azonosítóját, vagy kiválaszthatja a kulcstárolót, majd kattintson a **kulcstartó és-kulcs kiválasztása** elemre.

![A titkosítási szakaszt és az ügyfél által felügyelt kulcs engedélyezését bemutató képernyőkép](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

Miután létrehozta a Batch-fiókot a rendszerhez rendelt felügyelt identitással, és megadta a hozzáférést a Key Vaulthoz, frissítse a Batch-fiókot a `{Key Identifier}` paraméter alatt található URL-címmel `keyVaultProperties` . A **encryption_key_source** is állítsa be `Microsoft.KeyVault` .

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>Batch-fiók létrehozása felhasználó által hozzárendelt felügyelt identitással és az ügyfél által felügyelt kulcsokkal

A Batch Management .NET-ügyfél használatával létrehozhat egy batch-fiókot, amely egy felhasználó által hozzárendelt felügyelt identitással és az ügyfél által felügyelt kulcsokkal fog rendelkezni.

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>Az ügyfél által felügyelt kulcs verziójának frissítése

A kulcs új verziójának létrehozásakor frissítse a Batch-fiókot az új verzió használatára. Kövesse az alábbi lépéseket:

1. Nyissa meg Azure Portal a Batch-fiókját, és jelenítse meg a titkosítási beállításokat.
2. Adja meg az új kulcs verziójának URI azonosítóját. Másik lehetőségként kiválaszthatja a Key Vault és a kulcsot is a verzió frissítéséhez.
3. Mentse a módosításokat.

A verzió frissítéséhez használhatja az Azure CLI-t is.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>Más kulcs használata a Batch encryption szolgáltatáshoz

A Batch-titkosításhoz használt kulcs módosításához kövesse az alábbi lépéseket:

1. Nyissa meg a Batch-fiókját, és jelenítse meg a titkosítási beállításokat.
2. Adja meg az új kulcs URI-JÁT. Másik lehetőségként kiválaszthatja a Key Vault, és kiválaszthat egy új kulcsot.
3. Mentse a módosításokat.

Az Azure CLI-t másik kulcs használatára is használhatja.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

- **Támogatottak-e az ügyfél által felügyelt kulcsok a meglévő batch-fiókok esetében?** Nem. Az ügyfél által felügyelt kulcsok csak új batch-fiókok esetén támogatottak.
- **Kiválaszthatom az 2048 bitenél nagyobb RSA-kulcsok méretét?** Igen, az RSA-kulcsok `3072` és a `4096` BITS mérete is támogatott.
- **Milyen műveletek érhetők el az ügyfél által felügyelt kulcs visszavonása után?** Az egyetlen engedélyezett művelet a fiók törlése, ha a Batch elveszti a hozzáférést az ügyfél által felügyelt kulcshoz.
- **Hogyan kell visszaállítani a Batch-fiókhoz való hozzáférést, ha véletlenül törölem a Key Vault kulcsot?** Mivel a kiürítési védelem és a helyreállítható törlés engedélyezve van, visszaállíthatja a meglévő kulcsokat. További információ: [Azure Key Vault helyreállítása](../key-vault/general/key-vault-recovery.md).
- **Letiltható az ügyfél által felügyelt kulcsok?** A Batch-fiók titkosítási típusát bármikor visszaállíthatja a "Microsoft Managed Key" értékre. Ezt követően ingyenesen törölheti vagy módosíthatja a kulcsot.
- **Hogyan lehet elforgatni a kulcsokat?** Az ügyfél által felügyelt kulcsok nem lesznek automatikusan elforgatva. A kulcs elforgatásához frissítse a fiókhoz társított kulcs azonosítóját.
- **Miután visszaállítottam a hozzáférést, mennyi ideig tart a Batch-fiók újbóli működése?** Akár 10 percet is igénybe vehet, amíg a fiók újra elérhető lesz a hozzáférés visszaállítása után.
- **Amíg a Batch-fiók nem érhető el, mi történik az erőforrásokkal?** Az ügyfél által felügyelt kulcsokhoz való batch-hozzáférés megszakadását futtató készletek továbbra is futnak. A csomópontok azonban elérhetetlenné válnak, és a tevékenységek nem fognak futni (és újra kell őket várólistára állítani). A hozzáférés visszaállítása után a csomópontok újra elérhetővé válnak, és a feladatok újra lesznek indítva.
- **A titkosítási mechanizmus a Batch-készletben lévő virtuálisgép-lemezekre vonatkozik?** Nem. A Cloud Service-konfigurációs készletek esetében az operációs rendszer és az ideiglenes lemez titkosítása nem lesz alkalmazva. A virtuális gépek konfigurációs készletei esetében az operációs rendszer és a megadott adatlemezek alapértelmezés szerint a Microsoft platform által felügyelt kulccsal lesznek titkosítva. Jelenleg nem adhat meg saját kulcsot ezekhez a lemezekhez. A Microsoft platform által felügyelt kulccsal rendelkező batch-készletekhez tartozó virtuális gépek ideiglenes lemezének titkosításához engedélyeznie kell a [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) tulajdonságot a [virtuálisgép-konfigurációs](/rest/api/batchservice/pool/add#virtualmachineconfiguration) készletben. A fokozottan bizalmas környezetek esetében ajánlott engedélyezni az ideiglenes lemezek titkosítását, és elkerülni az operációs rendszer és az adatlemezek bizalmas adatok tárolására szolgáló adatokat. További információ: [készlet létrehozása lemezes titkosítással engedélyezve](./disk-encryption.md)
- **A rendszer által hozzárendelt felügyelt identitás a számítási csomópontokon elérhető batch-fiókban?** Nem. A rendszer által hozzárendelt felügyelt identitás jelenleg csak az ügyfél által felügyelt kulcs Azure Key Vault elérésére szolgál. Ha felhasználó által hozzárendelt felügyelt identitást szeretne használni a számítási csomópontokon, tekintse meg a [felügyelt identitások konfigurálása a Batch-készletekben](managed-identity-pools.md)című témakört

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure batch biztonsági eljárásairól](security-best-practices.md).
- További információ a[Azure Key Vaultról](../key-vault/general/basic-concepts.md).
