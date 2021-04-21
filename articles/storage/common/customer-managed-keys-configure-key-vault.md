---
title: Titkosítás konfigurálása az Azure Key Vaultban tárolt, ügyfél által kezelt kulcsokkal
titleSuffix: Azure Storage
description: Megtudhatja, hogyan konfigurálhatja az Azure Storage-titkosítást a Azure Key Vault által felügyelt kulcsokkal a Azure Portal, a PowerShell vagy az Azure CLI használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 77a01a270f47ddacb71962188e7fedd0a0a9f6d0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790436"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Titkosítás konfigurálása az Azure Key Vaultban tárolt, ügyfél által kezelt kulcsokkal

Az Azure Storage egy tárfiókban tárolt összes adatot titkosítja. Alapértelmezés szerint az adatok titkosítása Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozása érdekében kezelheti a saját kulcsait. Az ügyfél által felügyelt kulcsokat a felügyelt Azure Key Vault (HSM) Key Vault (előzetes verzió) szolgáltatásban kell tárolni.

Ez a cikk bemutatja, hogyan konfigurálhatja a titkosítást a kulcstartóban tárolt, ügyfél által kezelt kulcsokkal a Azure Portal, a PowerShell vagy az Azure CLI használatával. Ha meg szeretne ismerkedni a titkosítás felügyelt HSM-beli, ügyfél által kezelt kulcsokkal való konfigurálásával, olvassa el a Titkosítás konfigurálása az Azure Key Vault Managed HSM -ban [(előzetes verzió)](customer-managed-keys-configure-key-vault-hsm.md)tárolt ügyfél által kezelt kulcsokkal.

> [!NOTE]
> Azure Key Vault és Azure Key Vault Managed HSM ugyanazon API-kat és felügyeleti felületeket támogatja a konfiguráláshoz.

## <a name="configure-a-key-vault"></a>Kulcstartó konfigurálása

Az ügyfél által kezelt kulcsok tárolására használhat új vagy meglévő kulcstartót. A tárfióknak és a kulcstartónak ugyanabban a régióban kell lennie, de eltérő előfizetésben is lehetnek.

Az ügyfél által felügyelt kulcsok Azure Storage-titkosítással való használatához engedélyezni kell a kulcstartóban a törlés és a végleges törlés védelmét is. A soft delete alapértelmezés szerint engedélyezve van egy új kulcstartó létrehozásakor, és nem tiltható le. A véglegesen kiürítés elleni védelmet a kulcstartó létrehozásakor vagy annak létrehozása után engedélyezheti.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha meg szeretne ismerkedni a kulcstartók Azure Portal kulcstartók használatával való létrehozásáról, olvassa el a Gyors útmutató: Kulcstartó létrehozása [a Azure Portal.](../../key-vault/general/quick-create-portal.md) A kulcstartó létrehozásakor válassza a **Végleges** kiürítés elleni védelem engedélyezése lehetőséget az alábbi képen látható módon.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Képernyőkép a végleges kiürítés elleni védelem kulcstartó létrehozásakor való engedélyezéséről":::

A véglegesen kiürítés elleni védelem meglévő kulcstartón való engedélyezéséhez kövesse az alábbi lépéseket:

1. Lépjen a kulcstartóhoz a Azure Portal.
1. A **Beállítások alatt** válassza a Tulajdonságok **lehetőséget.**
1. A Végleges **védelem szakaszban** válassza a **Végleges kiürítés elleni védelem engedélyezése lehetőséget.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Új kulcstartó PowerShell használatával való létrehozásához telepítse az [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell-modul 2.0.0-s vagy újabb verzióját. Ezután a [New-AzKeyVault hívásával](/powershell/module/az.keyvault/new-azkeyvault) hozzon létre egy új kulcstartót. Az Az.KeyVault modul 2.0.0-s és újabb verzióiban a soft delete alapértelmezés szerint engedélyezve van egy új kulcstartó létrehozásakor.

Az alábbi példa egy új kulcstartót hoz létre, engedélyezett soft delete és purge protection beállítással. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Ha meg szeretné tudni, hogyan engedélyezheti a végleges törlés elleni védelmet egy meglévő kulcstartón a PowerShell használatával, olvassa el a How [to use soft-delete with PowerShell (A soft-delete használata a PowerShell-sel)](../../key-vault/general/key-vault-recovery.md)

Ezután rendeljen hozzá egy rendszer által hozzárendelt felügyelt identitást a tárfiókhoz. Ezzel a felügyelt identitással adhat engedélyeket a tárfióknak a kulcstartó eléréséhez. A rendszer által hozzárendelt felügyelt identitásokkal kapcsolatos további információkért lásd: Mik azok az [Azure-erőforrások felügyelt identitások?](../../active-directory/managed-identities-azure-resources/overview.md).

Ha felügyelt identitást szeretne hozzárendelni a PowerShell használatával, hívja meg a [Set-AzStorageAccount parancsot:](/powershell/module/az.storage/set-azstorageaccount)

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Végül konfigurálja a kulcstartó hozzáférési szabályzatát úgy, hogy a tárfiók hozzáféréssel rendelkezik. Ebben a lépésben a tárfiókhoz korábban hozzárendelt felügyelt identitást fogja használni.

A kulcstartó hozzáférési szabályzatának beállítását a [Set-AzKeyVaultAccessPolicy hívásával állíthatja be:](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Új kulcstartó Azure CLI használatával való létrehozásához hívja meg [az az keyvault create hívást.](/cli/azure/keyvault#az_keyvault_create) Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Ha szeretné megtudni, hogyan engedélyezheti a végleges törlés elleni védelmet egy meglévő kulcstartón az Azure CLI-val, olvassa el a How to use soft-delete with CLI (A [soft-delete használata a CLI-val) (](../../key-vault/general/key-vault-recovery.md)

Ezután rendeljen hozzá egy rendszer által hozzárendelt felügyelt identitást a tárfiókhoz. Ezzel a felügyelt identitással adhat engedélyeket a tárfióknak a kulcstartó eléréséhez. A rendszer által hozzárendelt felügyelt identitásokkal kapcsolatos további információkért lásd: Mik azok az [Azure-erőforrások felügyelt identitások?](../../active-directory/managed-identities-azure-resources/overview.md).

Ha felügyelt identitást szeretne hozzárendelni az Azure CLI használatával, hívja meg [az az storage account update hívást:](/cli/azure/storage/account#az_storage_account_update)

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Végül konfigurálja a kulcstartó hozzáférési szabályzatát úgy, hogy a tárfiók hozzáféréssel rendelkezik. Ebben a lépésben a tárfiókhoz korábban hozzárendelt felügyelt identitást fogja használni.

A kulcstartó hozzáférési szabályzatának beállítását az [az keyvault set-policy hívásával állíthatja be:](/cli/azure/keyvault#az_keyvault_set_policy)

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

Ezután adjon hozzá egy kulcsot a kulcstartóhoz.

Az Azure Storage-titkosítás a 2048-as, 3072-es és 4096-os RSA- és RSA-HSM-kulcsokat támogatja. A kulcsokkal kapcsolatos további információkért lásd: [About keys (Tudnivalók a kulcsokról).](../../key-vault/keys/about-keys.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha meg szeretne ismerkedni azzal, hogyan adhat hozzá kulcsot a Azure Portal, olvassa el a gyorsútmutató: Kulcs beállítása és lekérése a Azure Key Vault [használatával Azure Portal.](../../key-vault/keys/quick-create-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kulcs PowerShell-sel való hozzáadásához hívja meg az [Add-AzKeyVaultKey parancsot.](/powershell/module/az.keyvault/add-azkeyvaultkey) Ne felejtse el lecserélni a zárójelben lévő helyőrzőértékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha kulcsot szeretne hozzáadni az Azure CLI-hez, hívja az [az keyvault key create gombra.](/cli/azure/keyvault/key#az_keyvault_key_create) Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Ügyfél által felügyelt kulcsokkal végzett titkosítás konfigurálása

Ezután konfigurálja Azure Storage-fiókját úgy, hogy az ügyfél által felügyelt kulcsokat Azure Key Vault, majd adja meg a tárfiókhoz társítható kulcsot.

Amikor ügyfél által kezelt kulcsokkal konfigurálja a titkosítást, dönthet úgy, hogy automatikusan frissíti az Azure Storage-titkosításhoz használt kulcsverziót, amikor új verzió érhető el a társított kulcstartóban. Azt is megadhatja, hogy mely kulcsverziót kell használni a titkosításhoz, amíg a kulcsverziót manuálisan nem frissíti.

> [!NOTE]
> A kulcs elforgatása: hozza létre a kulcs új verzióját a Azure Key Vault. Az Azure Storage nem kezeli a kulcs rotációját a Azure Key Vault, ezért manuálisan kell elforgatnia a kulcsot, vagy létre kell hoznia egy függvényt az ütemezés szerinti rotációhoz.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Titkosítás konfigurálása a kulcsverziók automatikus frissítéséhez

Az Azure Storage képes automatikusan frissíteni az ügyfél által felügyelt kulcsot, amely a titkosításhoz használatos a legújabb kulcsverzió használatára. Amikor az ügyfél által felügyelt kulcsot a Azure Key Vault váltja, az Azure Storage automatikusan megkezdi a kulcs legújabb verziójának titkosítását.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha az ügyfél által kezelt kulcsokat a kulcsverzió automatikus frissítésével konfigurálja a Azure Portal, kövesse az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
1. A **tárfiók Beállítások** panelen kattintson a Titkosítás **elemre.** Alapértelmezés szerint a kulcskezelés a **Microsoft Managed Keys (Microsoft által** kezelt kulcsok) beállításra van állítva, ahogyan az az alábbi képen látható.

    ![Képernyőkép a portál titkosítási lehetőségről](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Válassza a **Felhasználó által kezelt kulcsok** lehetőséget.
1. Válassza a **Kijelölés a Key Vault** lehetőséget.
1. Válassza **a Kulcstartó és kulcs kiválasztása lehetőséget.**
1. Válassza ki a használni kívánt kulcsot tartalmazó kulcstartót.
1. Válassza ki a kulcsot a kulcstartóból.

   ![Képernyőkép a kulcstartó és kulcs kiválasztásáról](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Mentse a módosításokat.

Miután meghatározta a kulcsot, a Azure Portal jelzi, hogy a kulcsverzió automatikus frissítése engedélyezve van, és megjeleníti a titkosításhoz jelenleg használt kulcsverziót.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Képernyőkép a kulcsverzió automatikus frissítéséről":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha az ügyfél által kezelt kulcsokat a kulcsverzió PowerShell-rel való automatikus frissítésével konfigurálja, telepítse az [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) modul 2.0.0-s vagy újabb verzióját.

Ha automatikusan frissítenie kell egy ügyfél által felügyelt kulcs kulcsverzióját, akkor ne használja a kulcsverziót, amikor ügyfél által kezelt kulcsokkal konfigurálja a titkosítást a tárfiókhoz. Hívja [meg a Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot a tárfiók titkosítási beállításainak frissítéséhez az alábbi példában látható módon, és a **-KeyvaultEncryption** beállítással engedélyezze a felhasználó által kezelt kulcsokat a tárfiókhoz.

Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha ügyfél által kezelt kulcsokat az Azure CLI-ben a kulcsverzió automatikus frissítésével konfigurál, telepítse az [Azure CLI 2.4.0-s](/cli/azure/release-notes-azure-cli#april-21-2020) vagy újabb verzióját. További információ: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

Ha automatikusan frissítenie kell egy ügyfél által felügyelt kulcs kulcsverzióját, akkor ne használja a kulcsverziót, amikor ügyfél által kezelt kulcsokkal konfigurálja a titkosítást a tárfiókhoz. Hívja [meg az az storage account update](/cli/azure/storage/account#az_storage_account_update) parancsot a tárfiók titkosítási beállításainak frissítéséhez az alábbi példában látható módon. Adja hozzá a paramétert, és állítsa a következőre: , hogy engedélyezze az ügyfél által kezelt `--encryption-key-source` `Microsoft.Keyvault` kulcsokat a fiókhoz.

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

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Titkosítás konfigurálása a kulcsverziók manuális frissítéséhez

Ha manuálisan szeretné frissíteni a kulcsverziót, akkor explicit módon adja meg azt a verziót, amikor az ügyfél által kezelt kulcsokkal való titkosítást konfigurálja. Ebben az esetben az Azure Storage nem frissíti automatikusan a kulcsverziót, amikor új verziót hoz létre a kulcstartóban. Új kulcsverzió használata érdekében manuálisan kell frissítenie az Azure Storage-titkosításhoz használt verziót.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha az ügyfél által kezelt kulcsokat a kulcsverzió manuális frissítésével konfigurálja a Azure Portal, adja meg a kulcs URI-ját, beleértve a verziót is. A kulcs URI-ként való megadásához kövesse az alábbi lépéseket:

1. A kulcs URI-ját úgy keresheti meg a Azure Portal, hogy megkeresi a kulcstartót, és kiválasztja a **Kulcsok** beállítást. Válassza ki a kívánt kulcsot, majd kattintson rá a verziószámok megtekintéséhez. Válasszon ki egy kulcsverziót az adott verzió beállításainak megtekintéséhez.
1. Másolja ki a Kulcsazonosító **mező értékét,** amely az URI-t biztosítja.

    ![Képernyőkép a Key Vault-kulcs URI-jról](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. A **tárfiók titkosítási kulcsának** beállításainál válassza az **Enter key URI (Kulcs URI megadása)** lehetőséget.
1. Illessze be a vágólapra másolt URI-t a **Kulcs URI mezőbe.** A kulcsverzió automatikus frissítésének engedélyezéséhez kihagyja a kulcsverziót az URI-ból.

   ![Képernyőkép a kulcs URI-ének beíratásról](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Adja meg a kulcstartót tartalmazó előfizetést.
1. Mentse a módosításokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha az ügyfél által kezelt kulcsokat a kulcsverzió manuális frissítésével konfigurálja, explicit módon adja meg a kulcs verzióját a tárfiók titkosításának konfigurálásakor. Hívja [meg a Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot a tárfiók titkosítási beállításainak frissítéséhez az alábbi példában látható módon, és a **-KeyvaultEncryption** beállítással engedélyezze a felhasználó által kezelt kulcsokat a tárfiókhoz.

Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Amikor manuálisan frissíti a kulcsverziót, frissítenie kell a tárfiók titkosítási beállításait az új verzió használatára. Először hívja meg a [Get-AzKeyVaultKey hívását](/powershell/module/az.keyvault/get-azkeyvaultkey) a kulcs legújabb verziójának lehívásával. Ezután hívja meg [a Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot a tárfiók titkosítási beállításainak a kulcs új verziójának használatára való frissítéséhez az előző példában látható módon.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az ügyfél által kezelt kulcsokat a kulcsverzió manuális frissítésével konfigurálja, explicit módon adja meg a kulcsverziót a tárfiók titkosításának konfigurálásakor. Hívja [meg az az storage account update](/cli/azure/storage/account#az_storage_account_update) parancsot a tárfiók titkosítási beállításainak frissítéséhez az alábbi példában látható módon. Adja hozzá a paramétert, és állítsa a következőre: , hogy engedélyezze a felhasználó által kezelt `--encryption-key-source` `Microsoft.Keyvault` kulcsokat a fiókhoz.

Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire.

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

Amikor manuálisan frissíti a kulcsverziót, frissítenie kell a tárfiók titkosítási beállításait az új verzió használatára. Először a key vault URI-ját kell lekérdezni [az az keyvault show](/cli/azure/keyvault#az_keyvault_show)hívásával, a kulcsverzióhoz pedig az az [keyvault key list-versions hívásával.](/cli/azure/keyvault/key#az_keyvault_key_list-versions) Ezután hívja [meg az az storage account update](/cli/azure/storage/account#az_storage_account_update) parancsot a tárfiók titkosítási beállításainak a kulcs új verziójának használatára való frissítéséhez, ahogy az előző példában is látható.

---

## <a name="change-the-key"></a>Kulcs módosítása

Az Azure Storage-titkosításhoz használt kulcsot bármikor módosíthatja.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha módosítania kell a kulcsot a Azure Portal, kövesse az alábbi lépéseket:

1. Lépjen a tárfiókra, és jelenítse meg a **Titkosítási** beállításokat.
1. Válassza ki a kulcstartót, és válasszon egy új kulcsot.
1. Mentse a módosításokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha módosítania kell a kulcsot a PowerShell használatával, hívja [](#configure-encryption-with-customer-managed-keys) meg a [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot a Titkosítás konfigurálása ügyfél által kezelt kulcsokkal oldalon látható módon, és adja meg az új kulcsnevet és -verziót. Ha az új kulcs egy másik kulcstartóban van, akkor frissítenie kell a kulcstartó URI-ját is.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha módosítania kell a kulcsot az Azure [CLI-val,](#configure-encryption-with-customer-managed-keys) hívja meg [az az storage account update(Az](/cli/azure/storage/account#az_storage_account_update) ügyfél által kezelt kulcsokkal való titkosítás konfigurálása) oldalon látható módon, és adja meg az új kulcsnevet és -verziót. Ha az új kulcs egy másik kulcstartóban van, akkor frissítenie kell a kulcstartó URI-ját is.

---

## <a name="revoke-customer-managed-keys"></a>Felhasználó által kezelt kulcsok visszavonása

Az ügyfél által felügyelt kulcs visszatartódása megszünteti a tárfiók és a kulcstartó közötti társítást.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha az ügyfél által felügyelt kulcsokat a következő Azure Portal szeretné visszavonni, tiltsa le a kulcsot a Felhasználó által kezelt [kulcsok letiltása.](#disable-customer-managed-keys)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az ügyfél által kezelt kulcsokat a kulcstartó hozzáférési szabályzatának eltávolításával vonhatja vissza. Ha egy felhasználó által felügyelt kulcsot szeretne visszavonni a PowerShell használatával, hívja meg a [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) parancsot az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrzőértékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az ügyfél által kezelt kulcsokat a kulcstartó hozzáférési szabályzatának eltávolításával vonhatja vissza. Ha vissza szeretne vonni egy ügyfél által felügyelt kulcsot az Azure CLI használatával, hívja meg az [az keyvault delete-policy](/cli/azure/keyvault#az_keyvault_delete_policy) parancsot az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrzőértékeket a saját értékeire, és az előző példákban definiált változókat használni.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Felhasználó által kezelt kulcsok letiltása

Ha letiltja az ügyfél által kezelt kulcsokat, a tárfiókja ismét a Microsoft által kezelt kulcsokkal lesz titkosítva.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha le szeretné tiltani az ügyfél által kezelt kulcsokat a Azure Portal kövesse az alábbi lépéseket:

1. Lépjen a tárfiókra, és jelenítse meg a **Titkosítási** beállításokat.
1. Törölje a Saját kulcs használata beállítás melletti **jelölőnégyzet jelölését.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha le szeretné tiltani az ügyfél által kezelt kulcsokat a PowerShell használatával, hívja meg a [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot a kapcsolóval, ahogy az az alábbi `-StorageEncryption` példában látható. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha le szeretné tiltani az ügyfél által kezelt kulcsokat az Azure CLI-val, hívja meg [az az storage account update](/cli/azure/storage/account#az_storage_account_update) hívást, és állítsa a et a következőre, ahogyan az alábbi `--encryption-key-source parameter` `Microsoft.Storage` példában látható. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire, és az előző példákban definiált változókat használni.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Felhasználó által kezelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
- [Titkosítás konfigurálása a Managed HSM-Azure Key Vault tárolt, ügyfél által kezelt kulcsokkal (előzetes verzió)](customer-managed-keys-configure-key-vault-hsm.md)
