---
title: Titkosítási hatókörök létrehozása és kezelése
description: Megtudhatja, hogyan hozhat létre titkosítási hatókört a blobadatok tároló- vagy blobszinten való elkülönítéséhez.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 656443b0bc9d0e45f43634b1b4c21145de7a5bb5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792542"
---
# <a name="create-and-manage-encryption-scopes"></a>Titkosítási hatókörök létrehozása és kezelése

A titkosítási hatókörök lehetővé teszik a titkosítás egyéni blob vagy tároló szintjén történő kezelését. Titkosítási hatókörök használatával biztonságos határokat hozhat létre az egy tárfiókban található, de különböző ügyfelekhez tartozó adatok között. További információ a titkosítási hatókörökről: A Blob Storage [titkosítási hatókörei.](encryption-scope-overview.md)

Ez a cikk bemutatja, hogyan hozhat létre titkosítási hatókört. Azt is bemutatja, hogyan kell megadni egy titkosítási hatókört blob vagy tároló létrehozásakor.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Titkosítási hatókör létrehozása

Létrehozhat olyan titkosítási hatókört, amely egy Microsoft által felügyelt kulccsal vagy egy ügyfél által felügyelt kulccsal van védve, amely egy Azure Key Vault-ban vagy egy Azure Key Vault Managed Hardware Security Model (HSM) (előzetes verzió) szolgáltatásban van tárolva. Ha ügyfél által felügyelt kulccsal szeretne titkosítási hatókört létrehozni, először létre kell hoznia egy kulcstartót vagy egy felügyelt HSM-et, és hozzá kell adni a hatókörhöz használni kívánt kulcsot. A kulcstartónak vagy a felügyelt HSM-nek engedélyezve kell lennie a végleges kiürítés elleni védelemnek, és ugyanabban a régióban kell lennie, mint a tárfióknak.

A titkosítási hatókör automatikusan engedélyezve van a létrehozásakor. A titkosítási hatókör létrehozása után megadhatja azt a blob létrehozásakor. A tároló létrehozásakor megadhat egy alapértelmezett titkosítási hatókört is, amely automatikusan vonatkozik a tárolóban lévő összes blobra.

# <a name="portal"></a>[Portál](#tab/portal)

Titkosítási hatókör létrehozásához a Azure Portal kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Válassza a **Titkosítás** beállítást.
1. Válassza a **Titkosítási hatókörök lapot.**
1. Új **titkosítási hatókör hozzáadásához** kattintson a Hozzáadás gombra.
1. A **Titkosítási hatókör létrehozása panelen** adja meg az új hatókör nevét.
1. Válassza ki a titkosítási kulcs támogatásának kívánt típusát, a Microsoft által felügyelt **kulcsokat** vagy a **Felhasználó által kezelt kulcsokat.**
    - Ha a Microsoft által felügyelt **kulcsokat választotta,** kattintson a **Létrehozás gombra** a titkosítási hatókör létrehozásához.
    - Ha a Felhasználó által kezelt **kulcsok** lehetőséget választotta, válasszon ki egy előfizetést, és adjon meg egy kulcstartót vagy egy felügyelt HSM-et, valamint egy kulcsot, amely ehhez a titkosítási hatókörhöz lesz használva, ahogyan az alábbi képen látható.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Képernyőkép a titkosítási hatókör létrehozásáról a Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Titkosítási hatókör PowerShell használatával való létrehozásához telepítse az [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) PowerShell-modul 3.4.0-s vagy újabb verzióját.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>A Microsoft által felügyelt kulcsokkal védett titkosítási hatókör létrehozása

A Microsoft által felügyelt kulcsokkal védett új titkosítási hatókör létrehozásához hívja meg a **New-AzStorageEncryptionScope** parancsot a `-StorageEncryption` paraméterrel.

Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Ügyfél által kezelt kulcsokkal védett titkosítási hatókör létrehozása

A kulcstartóban vagy a felügyelt HSM-ben tárolt, ügyfél által kezelt kulcsokkal védett új titkosítási hatókör létrehozásához először konfigurálja az ügyfél által kezelt kulcsokat a tárfiókhoz. Hozzá kell rendelnie egy felügyelt identitást a tárfiókhoz, majd a felügyelt identitással konfigurálnia kell a kulcstartó vagy a felügyelt HSM hozzáférési szabályzatát, hogy a tárfiók hozzáféréssel rendelkezik.

Az ügyfél által kezelt kulcsok titkosítási hatókörrel való használatra való konfiguráláshoz engedélyezni kell a végleges kiürítés elleni védelmet a kulcstartón vagy a felügyelt HSM-en. A kulcstartónak vagy a felügyelt HSM-nek ugyanabban a régióban kell lennie, mint a tárfióknak.

Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri>"
$scopeName2 = "customer2scope"

# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Ezután hívja meg a **New-AzStorageEncryptionScope** parancsot a paraméterrel, és adja meg a kulcs `-KeyvaultEncryption` URI-ját. Nem kötelező megadni a kulcsverziót a kulcs URI-ján. Ha kihagyja a kulcsverziót, a titkosítási hatókör automatikusan a legújabb kulcsverziót fogja használni. Ha a kulcsverziót is tartalmazza, akkor a kulcsverziót manuálisan kell frissítenie, hogy egy másik verziót használjon.

Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Titkosítási hatókör Azure CLI használatával való létrehozásához először telepítse az Azure CLI 2.20.0-s vagy újabb verzióját.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>A Microsoft által felügyelt kulcsokkal védett titkosítási hatókör létrehozása

A Microsoft által felügyelt kulcsokkal védett új titkosítási hatókör létrehozásához hívja meg az [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) parancsot, és adja meg a `--key-source` paramétert a következőként: `Microsoft.Storage` . Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Ügyfél által kezelt kulcsokkal védett titkosítási hatókör létrehozása

A Microsoft által felügyelt kulcsokkal védett új titkosítási hatókör létrehozásához hívja meg az [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) parancsot, és adja meg a `--key-source` paramétert a következőként: `Microsoft.Storage` . Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire:

Ha ügyfél által felügyelt kulcsokkal védett új titkosítási hatókört hoz létre egy kulcstartóban vagy felügyelt HSM-ben, először konfigurálja az ügyfél által kezelt kulcsokat a tárfiókhoz. Felügyelt identitást kell hozzárendelnie a tárfiókhoz, majd a felügyelt identitással konfigurálnia kell a kulcstartó hozzáférési szabályzatát, hogy a tárfiók hozzáféréssel rendelkezik. További információ: Ügyfél által [kezelt kulcsok az Azure Storage-titkosításhoz.](../common/customer-managed-keys-overview.md)

Az ügyfél által kezelt kulcsok titkosítási hatókörrel való használatra való konfiguráláshoz engedélyezni kell a végleges kiürítés elleni védelmet a kulcstartón vagy a felügyelt HSM-en. A kulcstartónak vagy a felügyelt HSM-nek ugyanabban a régióban kell lennie, mint a tárfióknak.

Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Ezután hívja meg az **az storage account encryption-scope create parancsot** a `--key-uri` paraméterrel, és adja meg a kulcs URI-ját. Nem kötelező megadni a kulcsverziót a kulcs URI-ján. Ha kihagyja a kulcsverziót, a titkosítási hatókör automatikusan a legújabb kulcsverziót fogja használni. Ha a kulcsverziót is tartalmazza, akkor a kulcsverziót manuálisan kell frissítenie egy másik verzió használatára.

Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Az Azure Storage-titkosítás kulcstartókban vagy felügyelt HSM-beli, felhasználó által kezelt kulcsokkal való konfigurálásával kapcsolatos információkért tekintse meg a következő cikkeket:

- [Titkosítás konfigurálása az Azure Key Vaultban tárolt, ügyfél által kezelt kulcsokkal](../common/customer-managed-keys-configure-key-vault.md)
- [Konfigurálja a titkosítást a Managed HSM (előzetes verzió)](../common/customer-managed-keys-configure-key-vault-hsm.md)Azure Key Vault tárolt, ügyfél által kezelt kulcsokkal.

## <a name="list-encryption-scopes-for-storage-account"></a>A tárfiók titkosítási hatókörének felsorolása

# <a name="portal"></a>[Portál](#tab/portal)

A tárfiók titkosítási hatókörének megtekintéséhez a Azure Portal a tárfiók **Titkosítási** hatókörök beállítását. Ezen a panelen engedélyezheti vagy letilthatja a titkosítási hatókört, vagy módosíthatja a titkosítási hatókör kulcsát.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Képernyőkép a titkosítási hatókörök listájáról a Azure Portal":::

Az ügyfél által felügyelt kulcs részleteinek megtekintéséhez, beleértve a kulcs URI-ját és verzióját, valamint azt, hogy a kulcsverzió automatikusan frissül-e, kövesse a **Kulcs oszlopban található** hivatkozást.

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="A titkosítási hatókörrel használt kulcs részleteit bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A tárfiókhoz elérhető titkosítási hatókörök PowerShell-rel való listához hívja meg a **Get-AzStorageEncryptionScope** parancsot. Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Az erőforráscsoportban található összes titkosítási hatókör tárfiók szerint való listához használja a folyamat szintaxisát:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

A tárfiókhoz elérhető titkosítási hatókörök Azure CLI-val való listához hívja meg [az az storage account encryption-scope list parancsot.](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_list) Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Tároló létrehozása alapértelmezett titkosítási hatókörrel

Tároló létrehozásakor megadhat egy alapértelmezett titkosítási hatókört. A tárolóban lévő blobok alapértelmezés szerint ezt a hatókört használják.

Egyéni blobok saját titkosítási hatókörrel is létrehozhatóak, kivéve, ha a tároló úgy van konfigurálva, hogy minden blob az alapértelmezett hatókört használja. További információ: [Tárolók és blobok titkosítási hatókörei.](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs)

# <a name="portal"></a>[Portál](#tab/portal)

Ha alapértelmezett titkosítási hatókörrel kell létrehoznia egy tárolót a Azure Portal, először hozza létre a titkosítási hatókört a Titkosítási hatókör [létrehozásacímű leírásban leírtak szerint.](#create-an-encryption-scope) A tároló létrehozásához kövesse az alábbi lépéseket:

1. Lépjen a tárfiókban található tárolók listájára, és válassza a Hozzáadás gombot egy új tároló létrehozásához. 
1. Bontsa **ki a Speciális** beállítások gombra az Új tároló **panelen.**
1. A **Titkosítási hatókör** legördülő menüben válassza ki a tároló alapértelmezett titkosítási hatókörét.
1. Ha meg kell követelni, hogy a tárolóban lévő összes blob az alapértelmezett titkosítási hatókört használja, jelölje be A titkosítási hatókör használata a tárolóban lévő **összes blobhoz jelölőnégyzetet.** Ha ez a jelölőnégyzet be van jelölve, akkor a tárolóban lévő egyes blobok nem bírálhatják felül az alapértelmezett titkosítási hatókört.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Képernyőkép az alapértelmezett titkosítási hatókörű tárolóról":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha alapértelmezett titkosítási hatókörrel hoz létre tárolót a PowerShell használatával, hívja meg a [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) parancsot, és adja meg a paraméter `-DefaultEncryptionScope` hatókörét. Ha a tárolóban lévő összes blobot a tároló alapértelmezett hatókörének használatára kényszeríti, állítsa a `-PreventEncryptionScopeOverride` paramétert a következőre: `true` .

```powershell
$containerName1 = "container1"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a container with a default encryption scope that cannot be overridden.
New-AzStorageContainer -Name $containerName1 `
    -Context $ctx `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Ha alapértelmezett titkosítási hatókörrel hoz létre tárolót az Azure CLI használatával, hívja meg az [az storage container create](/cli/azure/storage/container#az_storage_container_create) parancsot, és adja meg a paraméter `--default-encryption-scope` hatókörét. Ha a tárolóban lévő összes blobot a tároló alapértelmezett hatókörének használatára kényszeríti, állítsa a `--prevent-encryption-scope-override` paramétert a következőre: `true` .

Az alábbi példa az Ön Azure AD-fiókjával engedélyezi a tároló létrehozására szolgáló műveletet. A fiók hozzáférési kulcsát is használhatja. További információ: Blob- vagy üzenetsoradatok elérésének [jogosultsága az Azure CLI használatával.](./authorize-data-operations-cli.md)

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Ha egy ügyfél megpróbál hatókört megadni, amikor egy blobot egy alapértelmezett titkosítási hatókörrel rendelkezik tárolóba tölt fel, és a tároló úgy van konfigurálva, hogy megakadályozza, hogy a blobok felülbírálják az alapértelmezett hatókört, akkor a művelet meghiúsul egy üzenettel, amely jelzi, hogy a kérést a tárolótitkosítási házirend tiltotta.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Titkosítási hatókörrel tartozó blob feltöltése

Blob feltöltésekor megadhat egy titkosítási hatókört az adott blobhoz, vagy használhatja a tároló alapértelmezett titkosítási hatókörét, ha van ilyen.

# <a name="portal"></a>[Portál](#tab/portal)

Ha titkosítási hatókörrel fel kell töltenie egy blobot a Azure Portal, először hozza létre a titkosítási hatókört a Titkosítási hatókör [létrehozásacímű dokumentumban leírtak szerint.](#create-an-encryption-scope) Ezután kövesse az alábbi lépéseket a blob létrehozásához:

1. Keresse meg azt a tárolót, amelybe fel szeretné tölteni a blobot.
1. Válassza a **Feltöltés gombot,** és keresse meg a feltölteni kívánt blobot.
1. Bontsa **ki a Speciális** beállításokat a Blob **feltöltése panelen.**
1. Keresse meg **a Titkosítási hatókör** legördülő szakaszt. Alapértelmezés szerint a blob a tároló alapértelmezett titkosítási hatókörével jön létre, ha van ilyen. Ha a tároló megköveteli, hogy a blobok az alapértelmezett titkosítási hatókört használják, ez a szakasz le van tiltva.
1. Ha másik hatókört kíván megadni a feltöltött blobhoz, válassza a **Meglévő** hatókör kiválasztása lehetőséget, majd válassza ki a kívánt hatókört a legördülő menüből.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Képernyőkép a titkosítási hatókörrel feltöltött blobról":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha a PowerShellen keresztül fel kell töltenie egy titkosítási hatókörű blobot, hívja meg a [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) parancsot, és adja meg a blob titkosítási hatókörét.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx

# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx `
    -Container $containerName2 `
    -File $localSrcFile `
    -Blob "helloworld.txt" `
    -BlobType Block `
    -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Ha az Azure CLI-n keresztül titkosítási hatókörrel tölt fel egy blobot, hívja meg az [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) parancsot, és adja meg a blob titkosítási hatókörét.

Ha a könyvtárat Azure Cloud Shell, kövesse [a](storage-quickstart-blobs-cli.md#upload-a-blob) Blob feltöltése fájl létrehozásához a gyökérkönyvtárban található fájl létrehozásáhozcímeket. Ezt a fájlt ezután feltöltheti egy blobba az alábbi minta használatával.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Hatókör titkosítási kulcsának módosítása

Ha módosítania kell a titkosítási hatókört védő kulcsot a Microsoft által felügyelt kulcsról egy ügyfél által felügyelt kulcsra, először győződjön meg arról, hogy engedélyezte az ügyfél által kezelt kulcsokat az Azure Key Vault vagy Key Vault HSM használatával a tárfiókhoz. További információkért lásd: [Titkosítás](../common/customer-managed-keys-configure-key-vault.md) konfigurálása az ügyfél által felügyelt kulcsokkal a Azure Key Vault-ban tárolt kulcsokkal vagy Titkosítás konfigurálása az ügyfél által kezelt kulcsokkal a [Azure Key Vault.](../common/customer-managed-keys-configure-key-vault.md)

# <a name="portal"></a>[Portál](#tab/portal)

A hatókört védő kulcs a következő Azure Portal módosíthatja:

1. Lépjen a **Titkosítási hatókörök** lapra a tárfiók titkosítási hatókörei listájának megtekintéséhez.
1. Válassza **a módosítani** kívánt hatókör mellett található További gombra.
1. A **Titkosítási hatókör szerkesztése** panelen módosíthatja a titkosítás típusát a Microsoft által felügyelt kulcsról az ügyfél által felügyelt kulcsra, vagy fordítva.
1. Új, ügyfél által felügyelt kulcs kiválasztásához válassza az **Új kulcs** használata lehetőséget, és adja meg a kulcstartót, a kulcsot és a kulcsverziót.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha módosítania kell a titkosítási hatókört védő kulcsot egy ügyfél által felügyelt kulcsról egy Microsoft által felügyelt kulcsra a PowerShell használatával, hívja meg az **Update-AzStorageEncryptionScope** parancsot, és adja át a `-StorageEncryption` paramétert:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Ezután hívja meg az **Update-AzStorageEncryptionScope** parancsot, és adja meg a `-KeyUri` és `-KeyvaultEncryption` paramétereket:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Ha módosítania kell a titkosítási hatókört védő kulcsot egy ügyfél által felügyelt kulcsról egy Microsoft által felügyelt kulcsra az Azure CLI használatával, hívja meg az [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) parancsot, és adja át a paramétert a következő értékkel: `--key-source` `Microsoft.Storage`

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Ezután hívja meg az **az storage account encryption-scope update** parancsot, adja át a paramétert, és adja át a paramétert a következő `--key-uri` `--key-source` `Microsoft.KeyVault` értékkel:

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Titkosítási hatókör letiltása

Ha egy titkosítási hatókör le van tiltva, a továbbiakban nem számítunk fel érte számlát. Tiltsa le a szükségtelen költségek elkerülése érdekében nem szükséges titkosítási hatókört. További információkért lásd: [Az Azure Storage titkosítása az adattároláshoz.](../common/storage-service-encryption.md)

# <a name="portal"></a>[Portál](#tab/portal)

Ha le szeretne tiltani egy titkosítási  hatókört a Azure Portal, navigáljon a tárfiók Titkosítási hatókörök beállításhoz, válassza ki a kívánt titkosítási hatókört, majd válassza a **Letiltás lehetőséget.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha le szeretné tiltani a titkosítási hatókört a PowerShell használatával, hívja meg a Update-AzStorageEncryptionScope parancsot, és foglalja bele a paramétert értékkel, ahogy az alábbi `-State` `disabled` példában látható. A titkosítási hatókör újra engedélyezéséhez hívja meg ugyanazt a parancsot, és a `-State` paraméter értéke `enabled` . Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Ha le szeretné tiltani a titkosítási hatókört az Azure CLI-val, hívja meg [az az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) parancsot, és foglalja bele a paramétert értékkel az alábbi `--state` `Disabled` példában látható módon. A titkosítási hatókör újra engedélyezéséhez hívja meg ugyanezt a parancsot, és a `--state` paraméter értéke `Enabled` . Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> Titkosítási hatókör nem törölhető. A váratlan költségek elkerülése érdekében tiltsa le a jelenleg nem szükséges titkosítási hatókört.

---

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](../common/storage-service-encryption.md)
- [A Blob Storage titkosítási hatókörei](encryption-scope-overview.md)
- [Felhasználó által kezelt kulcsok az Azure Storage-titkosításhoz](../common/customer-managed-keys-overview.md)