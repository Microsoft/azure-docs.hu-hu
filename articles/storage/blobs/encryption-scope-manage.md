---
title: Titkosítási hatókörök létrehozása és kezelése (előzetes verzió)
description: Megtudhatja, hogyan hozhat létre titkosítási hatókört a blob-adattárolók és a Blobok szintjének elkülönítéséhez.
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d5590ff275ce821c81f5751f4d92972c49adaafc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209591"
---
# <a name="create-and-manage-encryption-scopes-preview"></a>Titkosítási hatókörök létrehozása és kezelése (előzetes verzió)

A titkosítási hatókörök (előzetes verzió) lehetővé teszik a titkosítás kezelését egy adott blob vagy tároló szintjén. A titkosítási hatókör elkülöníti a blobadatokat egy biztonságos enklávéban a tárfiókon belül. A titkosítási hatókörök használatával biztonságos határokat hozhat létre az azonos Storage-fiókban található, de különböző ügyfelekhez tartozó adategységek között. A titkosítási hatókörökkel kapcsolatos további információkért lásd: [titkosítási hatókörök a blob Storage-hoz (előzetes verzió)](encryption-scope-overview.md).

Ebből a cikkből megtudhatja, hogyan hozhat létre titkosítási hatókört. Azt is bemutatja, hogyan adhat meg titkosítási hatókört Blobok vagy tárolók létrehozásakor.

> [!IMPORTANT]
> A titkosítási hatókörök jelenleg **előzetes** verzióban érhetők el. Tekintse meg az Azure-szolgáltatásokra vonatkozó, a bétaverzióban, az előzetes verzióban, vagy más módon még nem közzétett, általánosan elérhetővé vált jogi feltételekhez tartozó [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .
>
> A váratlan költségek elkerülése érdekében ügyeljen arra, hogy letiltsa a jelenleg nem szükséges titkosítási hatóköröket.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Titkosítási hatókör létrehozása

Létrehozhat egy Microsoft által felügyelt kulccsal rendelkező titkosítási hatókört vagy egy Azure Key Vault vagy Azure Key Vault felügyelt hardveres biztonsági modellben (HSM) (előzetes verzió) tárolt ügyfél által felügyelt kulccsal. Ha ügyfél által felügyelt kulccsal szeretne titkosítási hatókört létrehozni, először létre kell hoznia egy Key vaultot vagy egy felügyelt HSM-t, és hozzá kell adnia a hatókörhöz használni kívánt kulcsot. A Key vaultnak vagy a felügyelt HSM-nek engedélyezve kell lennie a kiürítési védelemmel, és a Storage-fiókkal azonos régióban kell lennie.

A létrehozáskor a titkosítási hatókör automatikusan engedélyezve lesz. A titkosítási hatókör létrehozása után megadhatja azt a blob létrehozásakor. A tároló létrehozásakor megadhat egy alapértelmezett titkosítási hatókört is, amely automatikusan a tárolóban lévő összes blobra vonatkozik.

# <a name="portal"></a>[Portál](#tab/portal)

Ha titkosítási hatókört szeretne létrehozni a Azure Portalban, kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Válassza ki a **titkosítási** beállítást.
1. Válassza a **titkosítási hatókörök** fület.
1. Új titkosítási hatókör hozzáadásához kattintson a **Hozzáadás** gombra.
1. A **titkosítási hatókör** létrehozása panelen adja meg az új hatókör nevét.
1. Válassza ki a titkosítás típusát, vagy a **Microsoft által felügyelt kulcsokat** vagy az **ügyfél által felügyelt kulcsokat**.
    - Ha a **Microsoft által felügyelt kulcsokat** választotta, kattintson a **Létrehozás** gombra a titkosítási hatókör létrehozásához.
    - Ha az **ügyfél által felügyelt kulcsokat** választotta, akkor a titkosítási hatókörhöz használandó Key vaultot vagy felügyelt HSM, kulcs és kulcs verziót kell megadnia, ahogy az az alábbi képen is látható.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Képernyőfelvétel: titkosítási hatókör létrehozása a Azure Portalban":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Titkosítási hatókör PowerShell-lel történő létrehozásához először telepítse az az. Storage Preview modul verzióját. A legújabb előzetes verzió használata ajánlott, de a titkosítási hatókörök a 1.13.4-Preview és újabb verziókban is támogatottak. Távolítsa el az az. Storage modul bármely más verzióját.

A következő parancs az az. Storage [2.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) modult telepíti:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Microsoft által felügyelt kulcsokkal védett titkosítási hatókör létrehozása

A Microsoft által felügyelt kulcsok által védett új titkosítási hatókör létrehozásához hívja meg a **New-AzStorageEncryptionScope** parancsot a `-StorageEncryption` paraméterrel.

Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok által védett titkosítási hatókör létrehozása

A Key vaultban vagy a Managed HSM-ben tárolt, ügyfél által felügyelt kulcsok által védett új titkosítási hatókör létrehozásához először konfigurálja az ügyfél által felügyelt kulcsokat a Storage-fiókhoz. Felügyelt identitást kell rendelnie a Storage-fiókhoz, majd a felügyelt identitás használatával konfigurálnia kell a Key Vault vagy a felügyelt HSM hozzáférési házirendjét, hogy a Storage-fiók jogosult legyen az elérésére.

Az ügyfél által felügyelt kulcsok titkosítási hatókörrel való használatához engedélyezni kell a kiürítési védelmet a Key vaulton vagy a felügyelt HSM-ben. A Key vaultnak vagy a Managed HSM-nek ugyanabban a régióban kell lennie, mint a Storage-fióknak.

Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri-with-version>"
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

Ezután hívja meg a **New-AzStorageEncryptionScope** parancsot a `-KeyvaultEncryption` paraméterrel, és határozza meg a kulcs URI-ját. Ügyeljen arra, hogy tartalmazza a fő verziót a kulcs URI-n. Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Az Azure CLI-vel való titkosítási hatókör létrehozásához először telepítse az Azure CLI-verziót 2.4.0 vagy újabb verzióra.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Microsoft által felügyelt kulcsokkal védett titkosítási hatókör létrehozása

A Microsoft által felügyelt kulcsok által védett új titkosítási hatókör létrehozásához hívja meg az az [Storage Account encryption-scope Create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) parancsot, és a paramétert a következő módon kell megadni: `--key-source` `Microsoft.Storage` . Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok által védett titkosítási hatókör létrehozása

A Microsoft által felügyelt kulcsok által védett új titkosítási hatókör létrehozásához hívja meg az az [Storage Account encryption-scope Create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) parancsot, és a paramétert a következő módon kell megadni: `--key-source` `Microsoft.Storage` . Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire:

Ha az ügyfél által felügyelt kulcsokkal védett új titkosítási hatókört szeretne létrehozni egy Key vaultban vagy felügyelt HSM-ben, először konfigurálja az ügyfél által felügyelt kulcsokat a Storage-fiókhoz. Felügyelt identitást kell rendelnie a Storage-fiókhoz, majd a felügyelt identitás használatával kell konfigurálnia a kulcstartó hozzáférési házirendjét, hogy a Storage-fiók jogosult legyen az elérésére. További információ: [ügyfél által felügyelt kulcsok az Azure Storage encryption szolgáltatáshoz](../common/customer-managed-keys-overview.md).

Az ügyfél által felügyelt kulcsok titkosítási hatókörrel való használatához engedélyezni kell a kiürítési védelmet a Key vaulton vagy a felügyelt HSM-ben. A Key vaultnak vagy a Managed HSM-nek ugyanabban a régióban kell lennie, mint a Storage-fióknak.

Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire:

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

Ezután hívja meg az az **Storage Account encryption-scope Create** parancsot a `--key-uri` paraméterrel, és határozza meg a kulcs URI-ját. Ügyeljen arra, hogy tartalmazza a fő verziót a kulcs URI-n. Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Ha meg szeretné tudni, hogyan konfigurálhatja az Azure Storage-titkosítást az ügyfél által felügyelt kulcsokkal a kulcstartóban, tekintse meg a [titkosítás konfigurálása a Azure Key Vaultban tárolt ügyfél által felügyelt kulcsokkal](../common/customer-managed-keys-configure-key-vault.md)témakört Az ügyfél által felügyelt kulcsok felügyelt HSM-ben való konfigurálásával kapcsolatban lásd: [a titkosítás konfigurálása a Azure Key Vault felügyelt HSM-ben (előzetes verzió) tárolt, ügyfél által felügyelt kulcsokkal](../common/customer-managed-keys-configure-key-vault-hsm.md).

## <a name="list-encryption-scopes-for-storage-account"></a>A Storage-fiók titkosítási hatókörének listázása

# <a name="portal"></a>[Portál](#tab/portal)

A Azure Portal lévő Storage-fiókok titkosítási hatókörének megtekintéséhez navigáljon a Storage-fiók **titkosítási hatókörök** beállításához. Ebből a panelből engedélyezheti vagy letilthatja a titkosítási hatókört, vagy módosíthatja a titkosítási hatókör kulcsát.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="A Azure Portal titkosítási hatóköreit bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell-lel rendelkező Storage-fiókok számára elérhető titkosítási hatókörök listázásához hívja meg a **Get-AzStorageEncryptionScope** parancsot. Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Egy erőforráscsoport összes titkosítási hatókörének a Storage-fiók alapján való listázásához használja a folyamat szintaxisát:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Az Azure CLI-vel rendelkező Storage-fiókhoz elérhető titkosítási hatókörök listázásához hívja meg az az [Storage Account encryption-scope List](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) parancsot. Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Alapértelmezett titkosítási hatókörrel rendelkező tároló létrehozása

Tároló létrehozásakor megadhat egy alapértelmezett titkosítási hatókört. A tárolóban lévő Blobok alapértelmezés szerint a hatókört fogják használni.

Egyéni blob hozható létre a saját titkosítási hatókörével, kivéve, ha a tároló úgy van konfigurálva, hogy az összes blob használja az alapértelmezett hatókörét.

# <a name="portal"></a>[Portál](#tab/portal)

Ha a Azure Portal egy alapértelmezett titkosítási hatókörrel rendelkező tárolót szeretne létrehozni, először hozza létre a titkosítási hatókört a [titkosítási hatókör létrehozása](#create-an-encryption-scope)című témakörben leírtak szerint. Ezután kövesse az alábbi lépéseket a tároló létrehozásához:

1. Navigáljon a Storage-fiókban található tárolók listájához, és kattintson a **Hozzáadás** gombra egy új tároló létrehozásához.
1. Bontsa ki a **speciális** beállításokat az **új tároló** ablaktáblán.
1. A **titkosítási hatókör** legördülő menüben válassza ki a tároló alapértelmezett titkosítási hatókörét.
1. Ha szeretné megkövetelni, hogy a tárolóban lévő összes blob az alapértelmezett titkosítási hatókört használja, jelölje be a jelölőnégyzetet, hogy **ezt a titkosítási hatókört használja a tárolóban lévő összes blobhoz**. Ha a jelölőnégyzet be van jelölve, akkor a tárolóban lévő egyes Blobok nem tudják felülbírálni az alapértelmezett titkosítási hatókört.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Az alapértelmezett titkosítási hatókörű tárolót bemutató képernyőfelvétel":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha egy alapértelmezett titkosítási hatókörrel rendelkező tárolót szeretne létrehozni a PowerShell-lel, hívja meg a [New-AzRmStorageContainer](/powershell/module/az.storage/new-azrmstoragecontainer) parancsot a paraméter hatókörének megadásával `-DefaultEncryptionScope` . A **New-AzRmStorageContainer** parancs egy tárolót hoz létre az Azure Storage erőforrás-szolgáltató használatával, amely lehetővé teszi a titkosítási hatókörök és egyéb erőforrás-kezelési műveletek konfigurálását.

Ha egy tárolóban lévő összes blobot szeretné kényszeríteni a tároló alapértelmezett hatókörének használatára, állítsa a paramétert a következőre: `-PreventEncryptionScopeOverride` `true` .

```powershell
$containerName1 = "container1"
$containerName2 = "container2"

# Create a container with a default encryption scope that cannot be overridden.
New-AzRmStorageContainer -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Name $containerName1 `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Ha egy alapértelmezett titkosítási hatókörrel rendelkező tárolót szeretne létrehozni az Azure CLI-vel, hívja meg az az [Storage Container Create](/cli/azure/storage/container#az-storage-container-create) parancsot, és határozza meg a paraméter hatókörét `--default-encryption-scope` . Ha egy tárolóban lévő összes blobot szeretné kényszeríteni a tároló alapértelmezett hatókörének használatára, állítsa a paramétert a következőre: `--prevent-encryption-scope-override` `true` .

A következő példa az Azure AD-fiókját használja, hogy engedélyezze a műveletet a tároló létrehozásához. Használhatja a fiók elérési kulcsát is. További információ: a [blob-vagy üzenetsor-adatokhoz való hozzáférés engedélyezése az Azure CLI-vel](./authorize-data-operations-cli.md).

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

Ha az ügyfél a Blobok alapértelmezett titkosítási hatókörű tárolóba való feltöltésekor megpróbál hatókört megadni, és a tároló úgy van konfigurálva, hogy megakadályozza a Blobok felülbírálását az alapértelmezett hatókörön, akkor a művelet meghiúsul, és egy üzenet jelzi, hogy a tároló titkosítási házirendje tiltja a kérést.

## <a name="upload-a-blob-with-an-encryption-scope"></a>BLOB feltöltése titkosítási hatókörrel

BLOB feltöltésekor megadhatja a blob titkosítási hatókörét, vagy használhatja a tároló alapértelmezett titkosítási hatókörét, ha van ilyen.

# <a name="portal"></a>[Portál](#tab/portal)

Ha fel szeretne tölteni egy blobot a Azure Portalban megadott titkosítási hatókörrel, először hozza létre a titkosítási hatókört a [titkosítási hatókör létrehozása](#create-an-encryption-scope)című témakörben leírtak szerint. Ezután kövesse az alábbi lépéseket a blob létrehozásához:

1. Navigáljon ahhoz a tárolóhoz, amelyhez fel kívánja tölteni a blobot.
1. Kattintson a **feltöltés** gombra, és keresse meg a feltölteni kívánt blobot.
1. Bontsa ki a **speciális** beállításokat a **blob feltöltése** ablaktáblán.
1. Keresse meg a **titkosítási hatókör** legördülő szakaszt. Alapértelmezés szerint a blob a tároló alapértelmezett titkosítási hatókörével jön létre, ha van ilyen. Ha a tároló megköveteli, hogy a Blobok az alapértelmezett titkosítási hatókört használják, ez a szakasz le van tiltva.
1. Ha másik hatókört szeretne megadni a feltöltött blobhoz, válassza a **meglévő hatókör kiválasztása** lehetőséget, majd válassza ki a kívánt hatókört a legördülő menüből.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="A Blobok titkosítási hatókörrel való feltöltését bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell használatával megadott titkosítási hatókörrel rendelkező Blobok feltöltéséhez hívja meg a [set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) parancsot, és adja meg a blob titkosítási hatókörét.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = (Get-AzStorageAccount -ResourceGroupName $rgName -StorageAccountName $accountName).Context

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx
# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx -Container $containerName2 -File $localSrcFile -Blob "helloworld.txt" -BlobType Block -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Az Azure CLI-vel megadott titkosítási hatókörrel rendelkező blob feltöltéséhez hívja meg az az [Storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload) parancsot, és adja meg a blob titkosítási hatókörét.

Ha Azure Cloud Shell használ, kövesse a [blob feltöltése egy](storage-quickstart-blobs-cli.md#upload-a-blob) fájl létrehozása a gyökérkönyvtárban című témakörben ismertetett lépéseket. Ezután feltöltheti ezt a fájlt egy blobba a következő minta használatával.

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

Ha a Microsoft által felügyelt kulcstól egy ügyfél által felügyelt kulcsra vonatkozó titkosítási hatókört védő kulcsot szeretne módosítani, először győződjön meg arról, hogy engedélyezte az ügyfél által felügyelt kulcsokat Azure Key Vault vagy Key Vault HSM-sel a Storage-fiókhoz. További információ: [a titkosítás konfigurálása az Azure Key Vault tárolt, ügyfél által felügyelt kulcsokkal](../common/customer-managed-keys-configure-key-vault.md) vagy a [titkosítás konfigurálása a Azure Key Vaultban tárolt ügyfél által felügyelt kulcsokkal](../common/customer-managed-keys-configure-key-vault.md).

# <a name="portal"></a>[Portál](#tab/portal)

A Azure Portal hatókörét védő kulcs módosításához kövesse az alábbi lépéseket:

1. A **titkosítási hatókörök** lapon megtekintheti a Storage-fiók titkosítási hatóköreit tartalmazó listát.
1. Kattintson a módosítani kívánt hatókör melletti **további** gombra.
1. A **titkosítási hatókör szerkesztése** panelen módosíthatja a titkosítás típusát a Microsoft által felügyelt kulcsból az ügyfél által felügyelt kulcsra, vagy fordítva.
1. Új ügyfél által felügyelt kulcs kiválasztásához válassza az **új kulcs használata** lehetőséget, és adja meg a Key Vault, a kulcs és a kulcs verziószámát.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha módosítani szeretné a titkosítási hatókört egy ügyfél által felügyelt kulcsból egy Microsoft által felügyelt kulcsra a PowerShell használatával, hívja meg az **Update-AzStorageEncryptionScope** parancsot, és adja meg a következő `-StorageEncryption` paramétert:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Ezután hívja meg az **Update-AzStorageEncryptionScope** parancsot, és adja meg a `-KeyUri` és a `-KeyvaultEncryption` paramétereket:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Ha módosítani szeretné a titkosítási hatókört egy ügyfél által felügyelt kulcsból egy Microsoft által felügyelt kulcsra az Azure CLI használatával, hívja meg az az [Storage Account encryption-scope Update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) parancsot, és adja meg a `--key-source` paramétert a következő értékkel `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Ezután hívja meg az az **Storage Account encryption-scope Update** parancsot, adja át a `--key-uri` paramétert, és adja át a `--key-source` paramétert a következő értékkel `Microsoft.KeyVault` :

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

Ha a titkosítási hatókör le van tiltva, már nem számítunk fel díjat. Tiltsa le azokat a titkosítási hatóköröket, amelyek nem szükségesek a szükségtelen költségek elkerülése érdekében. További információ: az [Azure Storage titkosítása inaktív adatokhoz](../common/storage-service-encryption.md).

# <a name="portal"></a>[Portál](#tab/portal)

A Azure Portal titkosítási hatókörének letiltásához navigáljon a Storage-fiók **titkosítási hatókörök** beállítására, válassza ki a kívánt titkosítási hatókört, és válassza a **Letiltás** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha le szeretné tiltani egy titkosítási hatókört a PowerShell-lel, hívja meg a Update-AzStorageEncryptionScope parancsot, és adja meg a `-State` paraméter értékét a `disabled` következő példában látható módon:. A titkosítási hatókör újbóli engedélyezéséhez hívja meg ugyanazt a parancsot, és `-State` állítsa be a paramétert `enabled` . Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Ha le szeretné tiltani egy titkosítási hatókört az Azure CLI-vel, hívja meg az az [Storage Account encryption-scope Update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) parancsot, és adja meg a `--state` paraméter értékét a `Disabled` következő példában látható módon:. A titkosítási hatókör újbóli engedélyezéséhez hívja meg ugyanazt a parancsot, és `--state` állítsa be a paramétert `Enabled` . Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

---

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](../common/storage-service-encryption.md)
- [BLOB Storage titkosítási hatókörök (előzetes verzió)](encryption-scope-overview.md)
- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz](../common/customer-managed-keys-overview.md)