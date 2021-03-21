---
title: Titkosítás konfigurálása az Azure Key Vaultban tárolt, ügyfél által kezelt kulcsokkal
titleSuffix: Azure Storage
description: A Azure Portal, a PowerShell vagy az Azure CLI használatával megtudhatja, hogyan konfigurálhatja az Azure Storage-titkosítást Azure Key Vaultban tárolt, az ügyfél által felügyelt kulcsokkal.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 24fbe843986b732a04c9e356c54f3d768d6739be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558184"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Titkosítás konfigurálása az Azure Key Vaultban tárolt, ügyfél által kezelt kulcsokkal

Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további vezérléséhez a saját kulcsait is kezelheti. Az ügyfél által felügyelt kulcsokat Azure Key Vault vagy Key Vault felügyelt hardveres biztonsági modellben (HSM) (előzetes verzió) kell tárolni.

Ez a cikk bemutatja, hogyan konfigurálhatja a titkosítást a Key vaultban tárolt, ügyfél által felügyelt kulcsokkal a Azure Portal, a PowerShell vagy az Azure CLI használatával. A felügyelt HSM-ben tárolt, ügyfél által felügyelt kulcsokkal történő titkosítás konfigurálásával kapcsolatban lásd: a [titkosítás konfigurálása a Azure Key Vault Managed HSM-ben (előzetes verzió) tárolt, ügyfél által felügyelt kulcsokkal](customer-managed-keys-configure-key-vault-hsm.md).

> [!NOTE]
> A Azure Key Vault és Azure Key Vault felügyelt HSM a konfigurációhoz ugyanazokat az API-kat és felügyeleti interfészeket támogatja.

## <a name="configure-a-key-vault"></a>Key Vault konfigurálása

Az ügyfél által felügyelt kulcsok tárolására új vagy meglévő kulcstartót használhat. A Storage-fióknak és a Key vaultnak ugyanabban a régióban kell lennie, de különböző előfizetésekben is lehetnek.

Az ügyfél által felügyelt kulcsok Azure Storage-titkosítással való használata megköveteli, hogy a Key Vault esetében is engedélyezze a Soft delete és a kiürítés védelmét. Az új kulcstartó létrehozásakor alapértelmezés szerint engedélyezve van a Soft DELETE, és nem tiltható le. A kiürítési védelmet engedélyezheti a kulcstartó létrehozásakor vagy a létrehozása után is.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha meg szeretné tudni, hogyan hozhat létre kulcstartót a Azure Portalsal, tekintse meg a következőt: gyors útmutató [: kulcstartó létrehozása a Azure Portal használatával](../../key-vault/general/quick-create-portal.md). Amikor létrehozza a kulcstartót, válassza az alábbi ábrán látható módon az **eltávolítási védelem engedélyezése** lehetőséget.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Képernyőfelvétel, amely bemutatja, hogyan engedélyezhető a kiürítési védelem a kulcstartó létrehozásakor":::

A meglévő kulcstartók védelmének engedélyezéséhez kövesse az alábbi lépéseket:

1. A Azure Portalban navigáljon a kulcstartóhoz.
1. A **Beállítások** területen válassza a **Tulajdonságok** lehetőséget.
1. A **védelem kiürítése** szakaszban válassza a **védelem kiürítésének engedélyezése** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Új kulcstartó PowerShell-lel való létrehozásához telepítse az az [.](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) kulcstartó PowerShell-modul 2.0.0 vagy újabb verzióját. Ezután hívja a [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) új kulcstartó létrehozásához. Az az. kulcstartó modul 2.0.0 és újabb verziójával a Soft delete alapértelmezés szerint engedélyezve van, amikor új kulcstartót hoz létre.

Az alábbi példa egy új kulcstartót hoz létre, amely lehetővé teszi a Soft delete és a Purge Protection használatát. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Ha szeretné megtudni, hogyan engedélyezheti a kiürítést egy meglévő kulcstartón a PowerShell használatával, olvassa el a következő témakört: [a Soft-delete használata a PowerShell-](../../key-vault/general/key-vault-recovery.md)lel.

Ezután rendeljen hozzá egy rendszerhez rendelt felügyelt identitást a Storage-fiókhoz. Ezt a felügyelt identitást fogja használni a Storage-fiók engedélyeinek megadásához a kulcstartó eléréséhez. A rendszerhez rendelt felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitásai?](../../active-directory/managed-identities-azure-resources/overview.md).

Felügyelt identitás a PowerShell használatával történő hozzárendeléséhez hívja a [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount):

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Végül konfigurálja a kulcstartóhoz tartozó hozzáférési házirendet, hogy a Storage-fiók jogosult legyen az elérésére. Ebben a lépésben a korábban a Storage-fiókhoz rendelt felügyelt identitást fogja használni.

A Key Vault hozzáférési házirendjének beállításához hívja a [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy):

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha új kulcstartót szeretne létrehozni az Azure CLI használatával, hívja [az az kulcstartó Create](/cli/azure/keyvault#az-keyvault-create). Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Ha szeretné megtudni, hogyan engedélyezheti a kiürítést egy meglévő kulcstartóban az Azure CLI-vel, olvassa el a következő témakört: [a Soft-delete használata a CLI-vel](../../key-vault/general/key-vault-recovery.md).

Ezután rendeljen hozzá egy rendszerhez rendelt felügyelt identitást a Storage-fiókhoz. Ezt a felügyelt identitást fogja használni a Storage-fiók engedélyeinek megadásához a kulcstartó eléréséhez. A rendszerhez rendelt felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitásai?](../../active-directory/managed-identities-azure-resources/overview.md).

Felügyelt identitás az Azure CLI-vel való hozzárendeléséhez hívja [az az Storage Account Update](/cli/azure/storage/account#az-storage-account-update):

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Végül konfigurálja a kulcstartóhoz tartozó hozzáférési házirendet, hogy a Storage-fiók jogosult legyen az elérésére. Ebben a lépésben a korábban a Storage-fiókhoz rendelt felügyelt identitást fogja használni.

A Key Vault hozzáférési házirendjének beállításához hívja az [az kulcstartó set-Policy](/cli/azure/keyvault#az-keyvault-set-policy):

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>Kulcs hozzáadása

Ezután adjon hozzá egy kulcsot a Key vaultban.

Az Azure Storage encryption a 2048, 3072 és 4096 méretű RSA-és RSA-HSM-kulcsokat támogatja. A kulcsokkal kapcsolatos további információkért lásd: [a kulcsok ismertetése](../../key-vault/keys/about-keys.md).

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha meg szeretné tudni, hogyan adhat hozzá egy kulcsot a Azure Portalhoz, tekintse meg a gyors útmutató [: kulcs beállítása és lekérése Azure Key Vault használatával](../../key-vault/keys/quick-create-portal.md)című témakört a Azure Portal.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha a PowerShell-lel szeretne hozzáadni egy kulcsot, hívja a [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való kulcs hozzáadásához hívja [az az kulcstartó kulcs létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create)lehetőséget. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Ügyfél által felügyelt kulcsokkal végzett titkosítás konfigurálása

Ezután konfigurálja az Azure Storage-fiókot úgy, hogy az ügyfél által felügyelt kulcsokat Azure Key Vault használatával használja, majd adja meg a Storage-fiókhoz társítandó kulcsot.

Ha ügyfél által felügyelt kulcsokkal konfigurálja a titkosítást, beállíthatja, hogy automatikusan frissítse az Azure Storage-titkosításhoz használt kulcs verzióját, amikor új verzió érhető el a társított kulcstartóban. Másik lehetőségként explicit módon megadhatja a titkosításhoz használni kívánt verziót, amíg a kulcs verzióját manuálisan nem frissíti.

> [!NOTE]
> A kulcs elforgatásához hozza létre a kulcs új verzióját Azure Key Vault. Az Azure Storage nem kezeli a kulcs elforgatását Azure Key Vaultban, ezért manuálisan kell elforgatnia a kulcsot, vagy létre kell hoznia egy függvényt, amellyel elforgathatja azt ütemterv szerint.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Titkosítás konfigurálása a kulcsfontosságú verziók automatikus frissítéséhez

Az Azure Storage képes automatikusan frissíteni a titkosításhoz használt ügyfél által felügyelt kulcsot a legújabb kulcs verziójának használatára. Ha az ügyfél által felügyelt kulcsot Azure Key Vault elforgatják, az Azure Storage automatikusan a kulcs legújabb verzióját használja a titkosításhoz.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha az ügyfél által felügyelt kulcsokat a Azure Portal a kulcs verziójának automatikus frissítésével szeretné konfigurálni, kövesse az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
1. A Storage-fiók **Beállítások** paneljén kattintson a **titkosítás** elemre. Alapértelmezés szerint a kulcskezelő a **Microsoft által felügyelt kulcsok** értékre van állítva, ahogy az az alábbi képen is látható.

    ![A titkosítási beállítást megjelenítő portál képernyőképe](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Válassza ki az **ügyfél által felügyelt kulcsok** lehetőséget.
1. Válassza a **kiválasztás a Key Vault** lehetőséget.
1. Válassza ki **a Key Vault és a kulcs kiválasztása** lehetőséget.
1. Válassza ki a használni kívánt kulcsot tartalmazó kulcstartót.
1. Válassza ki a kulcsot a Key vaultból.

   ![A Key Vault és a kulcs kiválasztását bemutató képernyőkép](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Mentse a módosításokat.

A kulcs megadása után a Azure Portal azt jelzi, hogy a kulcs verziójának automatikus frissítése engedélyezve van, és megjeleníti a jelenleg a titkosításhoz használt kulcs verzióját.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Képernyőfelvétel, amely az engedélyezett kulcs verziójának automatikus frissítését mutatja":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha az ügyfél által felügyelt kulcsokat a PowerShell-lel automatikusan frissíti, telepítse az az [. Storage](https://www.powershellgallery.com/packages/Az.Storage) modult, a 2.0.0 vagy újabb verziót.

Az ügyfél által felügyelt kulcs verziószámának automatikus frissítéséhez hagyja ki a kulcs verzióját, ha a Storage-fiókhoz az ügyfél által felügyelt kulcsokkal konfigurálja a titkosítást. A [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a Storage-fiók titkosítási beállításainak frissítésére szolgál, ahogy az az alábbi példában is látható, és a **-KeyvaultEncryption** beállítással engedélyezheti az ügyfél által felügyelt kulcsokat a Storage-fiókhoz.

Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az ügyfél által felügyelt kulcsokat az Azure CLI-vel történő automatikus frissítéssel szeretné konfigurálni, telepítse az [Azure CLI 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) vagy újabb verzióját. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az ügyfél által felügyelt kulcs verziószámának automatikus frissítéséhez hagyja ki a kulcs verzióját, ha a Storage-fiókhoz az ügyfél által felügyelt kulcsokkal konfigurálja a titkosítást. Az alábbi példában látható módon frissítse a Storage-fiók titkosítási beállításait az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) paranccsal. Adja meg a `--encryption-key-source` paramétert, és állítsa be úgy, hogy `Microsoft.Keyvault` engedélyezze az ügyfél által felügyelt kulcsokat a fiókhoz.

Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Titkosítási konfiguráció konfigurálása a kulcsfontosságú verziók manuális frissítéséhez

Ha a kulcs verziójának manuális frissítését szeretné elvégezni, explicit módon adja meg azt a verziót, amikor az ügyfél által felügyelt kulcsokkal konfigurálja a titkosítást. Ebben az esetben az Azure Storage nem frissíti automatikusan a kulcs verzióját, amikor új verziót hoznak létre a Key vaultban. Új kulcs verziójának használatához manuálisan kell frissítenie az Azure Storage-titkosításhoz használt verziót.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha az ügyfél által felügyelt kulcsokat a Azure Portal kulcsának manuális frissítésével szeretné konfigurálni, adja meg a kulcs URI-JÁT, beleértve a verziószámot is. A kulcs URI-ként való megadásához kövesse az alábbi lépéseket:

1. Ha meg szeretné keresni a kulcs URI-JÁT a Azure Portalban, navigáljon a kulcstartóhoz, és válassza a **kulcsok** beállítást. Válassza ki a kívánt kulcsot, majd kattintson a kulcsra a verziók megtekintéséhez. Válassza ki a verziószámot az adott verzió beállításainak megtekintéséhez.
1. Másolja a **kulcs-azonosító** mező értékét, amely megadja az URI-t.

    ![A Key Vault kulcs URI-JÁT ábrázoló képernyőfelvétel](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. A Storage-fiók **titkosítási kulcs** beállításainál válassza a **kulcs URI-azonosítójának megadása** lehetőséget.
1. Illessze be a vágólapra a **kulcs URI** mezőjébe MÁSOLt URI-t. Hagyja ki a kulcs verzióját az URI-ból, hogy engedélyezze a kulcs verziójának automatikus frissítését.

   ![A kulcs URI-azonosítójának megadását bemutató képernyőkép](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Itt adhatja meg a kulcstárolót tartalmazó előfizetést.
1. Mentse a módosításokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha az ügyfél által felügyelt kulcsokat a kulcs verziószámának manuális frissítésével szeretné konfigurálni, explicit módon adja meg a kulcs verzióját a Storage-fiók titkosításának konfigurálásakor. A [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a Storage-fiók titkosítási beállításainak frissítésére szolgál, ahogy az az alábbi példában is látható, és a **-KeyvaultEncryption** beállítással engedélyezheti az ügyfél által felügyelt kulcsokat a Storage-fiókhoz.

Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Amikor manuálisan frissíti a kulcs verzióját, frissítenie kell a Storage-fiók titkosítási beállításait az új verzió használatára. Először hívja meg a [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) a kulcs legújabb verziójának beszerzéséhez. Ezután hívja meg a [set-AzStorageAccount-](/powershell/module/az.storage/set-azstorageaccount) t, hogy frissítse a Storage-fiók titkosítási beállításait a kulcs új verziójának használatára az előző példában látható módon.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az ügyfél által felügyelt kulcsokat a kulcs verziószámának manuális frissítésével szeretné konfigurálni, explicit módon adja meg a kulcs verzióját a Storage-fiók titkosításának konfigurálásakor. Az alábbi példában látható módon frissítse a Storage-fiók titkosítási beállításait az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) paranccsal. Adja meg a `--encryption-key-source` paramétert, és állítsa be úgy, hogy `Microsoft.Keyvault` engedélyezze az ügyfél által felügyelt kulcsokat a fiókhoz.

Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

Amikor manuálisan frissíti a kulcs verzióját, frissítenie kell a Storage-fiók titkosítási beállításait az új verzió használatára. Első lépésként a Key Vault URI-JÁT az [az kulcstartó show](/cli/azure/keyvault#az-keyvault-show)paranccsal hívhatja meg, és a kulcs verziószámát az az [kulcstartó Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions)paranccsal hívja meg. Ezt követően az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) paranccsal frissítse a Storage-fiók titkosítási beállításait a kulcs új verziójának használatára az előző példában látható módon.

---

## <a name="change-the-key"></a>A kulcs módosítása

Bármikor módosíthatja az Azure Storage-titkosításhoz használt kulcsot.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A Azure Portal kulcsának módosításához kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókjához, és jelenítse meg a **titkosítási** beállításokat.
1. Válassza ki a kulcstartót, és válasszon ki egy új kulcsot.
1. Mentse a módosításokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha módosítani szeretné a kulcsot a PowerShell-lel, hívja [meg a set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a [titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal](#configure-encryption-with-customer-managed-keys) , és adja meg az új kulcs nevét és verzióját. Ha az új kulcs egy másik kulcstartóban található, akkor a Key Vault URI-JÁT is frissítenie kell.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A kulcs az Azure CLI-vel való módosításához hívja az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) (a [titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal](#configure-encryption-with-customer-managed-keys) ) című részt, és adja meg az új kulcs nevét és verzióját. Ha az új kulcs egy másik kulcstartóban található, akkor a Key Vault URI-JÁT is frissítenie kell.

---

## <a name="revoke-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok visszavonása

Ügyfél által felügyelt kulcs visszavonása eltávolítja a Storage-fiók és a kulcstartó közötti társítást.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Az ügyfél által felügyelt kulcsok Azure Portal való visszavonásához tiltsa le a kulcsot az [ügyfél által felügyelt kulcsok letiltása](#disable-customer-managed-keys)című témakörben leírtak szerint.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az ügyfél által felügyelt kulcsok visszavonásához távolítsa el a Key Vault hozzáférési házirendjét. Az ügyfél által felügyelt kulcs PowerShell-sel való visszavonásához hívja meg a [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) parancsot az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az ügyfél által felügyelt kulcsok visszavonásához távolítsa el a Key Vault hozzáférési házirendjét. Ha egy ügyfél által felügyelt kulcsot szeretne visszavonni az Azure CLI-vel, hívja meg az az Key [Vault delete-Policy](/cli/azure/keyvault#az-keyvault-delete-policy) parancsot az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a Storage-fiók újból titkosítva lesz a Microsoft által felügyelt kulcsokkal.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha le szeretné tiltani az ügyfél által felügyelt kulcsokat a Azure Portalban, kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókjához, és jelenítse meg a **titkosítási** beállításokat.
1. Törölje a **saját kulcs használata** beállítás melletti jelölőnégyzet jelölését.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az ügyfél által felügyelt kulcsok PowerShell-lel való letiltásához hívja a [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) `-StorageEncryption` parancsot a kapcsolóval, ahogy az az alábbi példában is látható. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az ügyfél által felügyelt kulcsok Azure CLI-vel való letiltásához hívja meg az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) parancsot, és állítsa be a parancsot `--encryption-key-source parameter` `Microsoft.Storage` az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
- [A titkosítás konfigurálása Azure Key Vault felügyelt HSM-ben (előzetes verzió) tárolt, ügyfél által felügyelt kulcsokkal](customer-managed-keys-configure-key-vault-hsm.md)
