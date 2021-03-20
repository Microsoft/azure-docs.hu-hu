---
title: Olyan fiók létrehozása, amely támogatja az ügyfél által felügyelt kulcsokat a táblákhoz és a várólistákhoz
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre olyan Storage-fiókot, amely támogatja az ügyfél által felügyelt kulcsok konfigurálását a táblákhoz és a várólistákhoz. Az Azure CLI-vel vagy egy Azure Resource Manager sablonnal hozzon létre egy Storage-fiókot, amely az Azure Storage-titkosításhoz használt fiók titkosítási kulcsán alapul. Ezután konfigurálhatja az ügyfél által felügyelt kulcsokat a fiókhoz.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8150375eff98374e21d200d98c04158b07f1c243
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92789692"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Olyan fiók létrehozása, amely támogatja az ügyfél által felügyelt kulcsokat a táblákhoz és a várólistákhoz

Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint a üzenetsor-tároló és a Table Storage olyan kulcsot használ, amely a szolgáltatásra vonatkozik, és amelyet a Microsoft kezel. Dönthet úgy is, hogy az ügyfél által felügyelt kulcsokat használja a várólista vagy a tábla adatai titkosításához. Ha az ügyfél által felügyelt kulcsokat várólistákkal és táblázatokkal szeretné használni, először létre kell hoznia egy olyan Storage-fiókot, amely a fiókra hatókörrel rendelkező titkosítási kulcsot használ, nem pedig a szolgáltatáshoz. Miután létrehozott egy fiókot, amely a fiók titkosítási kulcsát használja a várólista-és a tábla-adatbázishoz, konfigurálhatja az ügyfél által felügyelt kulcsokat a Storage-fiókhoz.

Ez a cikk azt ismerteti, hogyan hozható létre olyan Storage-fiók, amely a fiókra hatókörben lévő kulcsra támaszkodik. A fiók első létrehozásakor a Microsoft a fiók kulcsával titkosítja a fiókban lévő adatvédelmet, és a Microsoft kezeli a kulcsot. Ezt követően konfigurálhatja az ügyfél által felügyelt kulcsokat a fiók számára az előnyök kihasználása érdekében, beleértve a saját kulcsok megadásának lehetőségét, a kulcs verziójának frissítését, a kulcsok elforgatását és a hozzáférés-vezérlés visszavonását.

## <a name="about-the-feature"></a>Tudnivalók a szolgáltatásról

Ahhoz, hogy olyan Storage-fiókot hozzon létre, amely a várólista-és Table Storage-fiók titkosítási kulcsára támaszkodik, először regisztrálnia kell, hogy használhassa ezt a funkciót az Azure-ban. A korlátozott kapacitás miatt vegye figyelembe, hogy a hozzáférési kérelmek jóváhagyása több hónapig is eltarthat.

A következő régiókban hozhat létre olyan Storage-fiókot, amely a fiók titkosítási kulcsára támaszkodik a várólista és a tábla tárterülete számára:

- USA keleti régiója
- USA déli középső régiója
- USA 2. nyugati régiója  

### <a name="register-to-use-the-account-encryption-key"></a>Regisztráció a fiók titkosítási kulcsának használatára

A fiók titkosítási kulcsának a várólista vagy a Table Storage szolgáltatással való használatához a PowerShell vagy az Azure CLI használatával regisztrálhat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell-lel való regisztráláshoz hívja meg a [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsot.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való regisztrációhoz hívja meg az az [Feature Register](/cli/azure/feature#az-feature-register) parancsot.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

### <a name="check-the-status-of-your-registration"></a>A regisztráció állapotának ellenõrzése

A várólista-vagy table Storage-regisztráció állapotának ellenőrzését a PowerShell vagy az Azure CLI használatával teheti meg.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha ellenőriznie szeretné a regisztráció állapotát a PowerShell-lel, hívja meg a [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) parancsot.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való regisztráció állapotának megtekintéséhez hívja meg az az [Feature](/cli/azure/feature#az-feature-show) parancsot.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Regisztrálja újra az Azure Storage erőforrás-szolgáltatót

A regisztráció jóváhagyása után újra regisztrálnia kell az Azure Storage erőforrás-szolgáltatót. A PowerShell vagy az Azure CLI használatával regisztrálja újra az erőforrás-szolgáltatót.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha újra szeretné regisztrálni az erőforrás-szolgáltatót a PowerShell-lel, hívja meg a [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) parancsot.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az erőforrás-szolgáltató Azure CLI-vel való újbóli regisztrálásához hívja meg az az [Provider Register](/cli/azure/provider#az-provider-register) parancsot.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Fiók titkosítási kulcsát használó fiók létrehozása

Az új Storage-fiókot úgy kell konfigurálni, hogy a fiók titkosítási kulcsát használja a várólisták és táblák számára a Storage-fiók létrehozásakor. A titkosítási kulcs hatóköre a fiók létrehozása után nem módosítható.

A Storage-fióknak általános célú v2 típusúnak kell lennie. Létrehozhatja a Storage-fiókot, és konfigurálhatja úgy, hogy az az Azure CLI vagy egy Azure Resource Manager sablon használatával támaszkodjon a fiók titkosítási kulcsára.

> [!NOTE]
> A Storage-fiók létrehozásakor a rendszer csak a várólista-és Table Storage-t konfigurálhatja úgy, hogy a fiók titkosítási kulcsával titkosítsa az adatvédelmet. A blob Storage és a Azure Files mindig a fiók titkosítási kulcsát használja az adattitkosításhoz.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha a PowerShell használatával szeretne létrehozni egy olyan Storage-fiókot, amely a fiók titkosítási kulcsára támaszkodik, győződjön meg arról, hogy telepítette a Azure PowerShell modult (3.4.0 vagy újabb verzió). További információ: [a Azure PowerShell modul telepítése](/powershell/azure/install-az-ps).

Ezután hozzon létre egy általános célú v2 Storage-fiókot a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancs meghívásával a megfelelő paraméterekkel:

- Adja `-EncryptionKeyTypeForQueue` meg a kapcsolót, és állítsa be annak értékét úgy `Account` , hogy a fiók titkosítási kulcsát használja a várólista-tárolóban tárolt adattitkosításhoz.
- Adja `-EncryptionKeyTypeForTable` meg a kapcsolót, és állítsa be annak értékét úgy `Account` , hogy a fiók titkosítási kulcsát használja a Table Storage-ban tárolt adattitkosításhoz.

Az alábbi példa bemutatja, hogyan hozhat létre olyan általános célú v2-es Storage-fiókot, amely olvasási hozzáférésű geo-redundáns tárolóhoz (RA-GRS) van konfigurálva, és amely a fiók titkosítási kulcsát használja az üzenetsor és a tábla tárolásához szükséges adat titkosítására. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI-t szeretné használni egy olyan Storage-fiók létrehozásához, amely a fiók titkosítási kulcsára támaszkodik, győződjön meg arról, hogy telepítette az Azure CLI 2.0.80 vagy újabb verzióját. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Ezután hozzon létre egy általános célú v2-es Storage-fiókot az az [Storage Account Create](/cli/azure/storage/account#az-storage-account-create) parancs meghívásával a megfelelő paraméterekkel:

- Adja `--encryption-key-type-for-queue` meg a kapcsolót, és állítsa be annak értékét úgy `Account` , hogy a fiók titkosítási kulcsát használja a várólista-tárolóban tárolt adattitkosításhoz.
- Adja `--encryption-key-type-for-table` meg a kapcsolót, és állítsa be annak értékét úgy `Account` , hogy a fiók titkosítási kulcsát használja a Table Storage-ban tárolt adattitkosításhoz.

Az alábbi példa bemutatja, hogyan hozhat létre olyan általános célú v2-es Storage-fiókot, amely olvasási hozzáférésű geo-redundáns tárolóhoz (RA-GRS) van konfigurálva, és amely a fiók titkosítási kulcsát használja az üzenetsor és a tábla tárolásához szükséges adat titkosítására. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Sablon](#tab/template)

A következő JSON-példa egy általános célú v2-es Storage-fiókot hoz létre, amely olvasási hozzáférésű geo-redundáns tárolóhoz (RA-GRS) van konfigurálva, és amely a fiók titkosítási kulcsát használja a várólista és a tábla tárolásához szükséges összes adat titkosítására. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Miután létrehozott egy fiókot, amely a fiók titkosítási kulcsára támaszkodik, a Azure Key Vault vagy Key Vault felügyelt hardveres biztonsági modell (HSM) (előzetes verzió) szolgáltatásban tárolt ügyfelek által felügyelt kulcsokat konfigurálhatja. Az ügyfél által felügyelt kulcsok kulcstartóban való tárolásával kapcsolatban lásd: [a titkosítás konfigurálása a Azure Key Vaultban tárolt ügyfél által felügyelt kulcsokkal](customer-managed-keys-configure-key-vault.md). Az ügyfél által felügyelt kulcsok felügyelt HSM-ben való tárolásával kapcsolatban lásd: [a titkosítás konfigurálása Azure Key Vault felügyelt HSM-ben (előzetes verzió) tárolt, ügyfél által felügyelt kulcsokkal](customer-managed-keys-configure-key-vault-hsm.md).

## <a name="verify-the-account-encryption-key"></a>A fiók titkosítási kulcsának ellenőrzése

Annak ellenőrzéséhez, hogy a Storage-fiókban lévő szolgáltatás a fiók titkosítási kulcsát használja-e, hívja meg az Azure CLI az [Storage Account](/cli/azure/storage/account#az-storage-account-show) parancsot. Ez a parancs a Storage-fiók tulajdonságait és azok értékeit adja vissza. Keresse meg az `keyType` egyes szolgáltatások mezőjét a titkosítási tulajdonságon belül, és ellenőrizze, hogy az a értékre van-e állítva `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Annak ellenőrzéséhez, hogy a Storage-fiókban lévő szolgáltatás a fiók titkosítási kulcsát használja-e, hívja meg a [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) parancsot. Ez a parancs a Storage-fiók tulajdonságait és azok értékeit adja vissza. Keresse meg az `KeyType` egyes szolgáltatásokhoz tartozó mezőt a `Encryption` tulajdonságon belül, és ellenőrizze, hogy az értéke a következő: `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Annak ellenőrzéséhez, hogy a Storage-fiókban lévő szolgáltatás a fiók titkosítási kulcsát használja-e, hívja meg az az [Storage Account show](/cli/azure/storage/account#az-storage-account-show) parancsot. Ez a parancs a Storage-fiók tulajdonságait és azok értékeit adja vissza. Keresse meg az `keyType` egyes szolgáltatások mezőjét a titkosítási tulajdonságon belül, és ellenőrizze, hogy az a értékre van-e állítva `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
- [Mi az Azure Key Vault](../../key-vault/general/overview.md)?