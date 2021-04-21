---
title: Ügyfél által kezelt kulcsokat támogató fiók létrehozása táblákhoz és üzenetsorokhoz
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre olyan tárfiókot, amely támogatja az ügyfelek által kezelt kulcsok táblákhoz és üzenetsorokhoz való konfigurálását. Az Azure CLI vagy egy Azure Resource Manager sablon használatával hozzon létre egy tárfiókot, amely az Azure Storage-titkosításhoz használt fióktitkosítási kulcsra támaszkodik. Ezután konfigurálhatja az ügyfél által kezelt kulcsokat a fiókhoz.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4c86811ee72d2713fced6320a17d1ccde1866d99
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769948"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Ügyfél által kezelt kulcsokat támogató fiók létrehozása táblákhoz és üzenetsorokhoz

Az Azure Storage a tárfiókban tárolt összes adatot titkosítja. Alapértelmezés szerint a Queue Storage és a Table Storage olyan kulcsot használ, amely a szolgáltatásra terjed ki, és a Microsoft kezeli. Dönthet úgy is, hogy az ügyfél által kezelt kulcsokat használja az üzenetsor- vagy táblaadatok titkosításához. Ha az ügyfél által kezelt kulcsokat üzenetsorokkal és táblákkal is használni tudja, először létre kell hoznia egy tárfiókot, amely a szolgáltatás helyett a fiókra vonatkozó titkosítási kulcsot használ. Miután létrehozott egy fiókot, amely a fiók titkosítási kulcsát használja az üzenetsor- és táblaadatokhoz, konfigurálhatja az ügyfél által kezelt kulcsokat az adott tárfiókhoz.

Ez a cikk bemutatja, hogyan hozhat létre olyan tárfiókot, amely a fiókra vonatkozó kulcsra támaszkodik. A fiók első létrehozásakor a Microsoft a fiókkulcs segítségével titkosítja a fiókban tárolt adatokat, a kulcsot pedig a Microsoft kezeli. Ezután konfigurálhatja az ügyfél által kezelt kulcsokat a fiókhoz, hogy kihasználja ezeket az előnyöket, beleértve a saját kulcsok biztosítanak, a kulcsverzió frissítése, a kulcsok váltása és a hozzáférés-vezérlés visszavonása lehetőséget.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>A fiók titkosítási kulcsát használó fiók létrehozása

Konfigurálnia kell egy új tárfiókot, hogy a tárfiók létrehozásakor a fiók titkosítási kulcsát használja az üzenetsorokhoz és táblákhoz. A titkosítási kulcs hatóköre a fiók létrehozása után nem módosítható.

A tárfióknak általános célú v2 típusúnak kell lennie. Létrehozhatja a tárfiókot, és konfigurálhatja úgy, hogy a fiók titkosítási kulcsát használja az Azure CLI vagy egy Azure Resource Manager használatával.

> [!NOTE]
> A tárfiók létrehozásakor csak a Queue és a Table Storage konfigurálható úgy, hogy a fiók titkosítási kulcsával titkosítsa az adatokat. A Blob Storage és Azure Files mindig a fiók titkosítási kulcsát használják az adatok titkosításához.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha a PowerShell használatával olyan tárfiókot hoz létre, amely a fiók titkosítási kulcsát használja, győződjön meg arról, hogy telepítette a Azure PowerShell 3.4.0-s vagy újabb verzióját. További információ: Install the Azure PowerShell module (A [Azure PowerShell modul telepítése).](/powershell/azure/install-az-ps)

Ezután hozzon létre egy általános célú v2-tárfiókot a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancs hívásával a megfelelő paraméterekkel:

- Adja meg a beállítást, és állítsa a értékét értékre, hogy a fiók titkosítási kulcsát használja a `-EncryptionKeyTypeForQueue` `Account` Queue Storage-ban lévő adatok titkosításához.
- Adja meg a beállítást, és állítsa a értékét értékre, hogy a fiók titkosítási kulcsát használja a `-EncryptionKeyTypeForTable` `Account` Table Storage-ban tárolt adatok titkosításához.

Az alábbi példa bemutatja, hogyan hozhat létre egy írási hozzáférésű georedundáns tárolásra (RA-GRS) konfigurált általános célú v2-tárfiókot, amely a fiók titkosítási kulcsát használja a Queue és a Table Storage adatainak titkosításához. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

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

Ha az Azure CLI használatával olyan tárfiókot hoz létre, amely a fiók titkosítási kulcsát használja, győződjön meg arról, hogy az Azure CLI 2.0.80-as vagy újabb verzióját telepítette. További információ: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

Ezután hozzon létre egy általános célú v2-tárfiókot [az az storage account create](/cli/azure/storage/account#az_storage_account_create) parancs hívásával a megfelelő paraméterekkel:

- Adja meg a beállítást, és állítsa a értékét értékre, hogy a fiók titkosítási kulcsát használja a `--encryption-key-type-for-queue` `Account` Queue Storage-ban lévő adatok titkosításához.
- Adja meg a beállítást, és állítsa a értékét értékre, hogy a fiók titkosítási kulcsát használja a `--encryption-key-type-for-table` `Account` Table Storage-ban tárolt adatok titkosításához.

Az alábbi példa bemutatja, hogyan hozhat létre egy írási hozzáférésű georedundáns tárolásra (RA-GRS) konfigurált általános célú v2-tárfiókot, amely a fiók titkosítási kulcsát használja a Queue és a Table Storage adatainak titkosításához. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire:

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

Az alábbi JSON-példa egy általános célú v2-tárfiókot hoz létre, amely írási hozzáférésű georedundáns tárolásra (RA-GRS) van konfigurálva, és amely a fiók titkosítási kulcsával titkosítja az adatokat mind a Queue, mind a Table Storage szolgáltatásban. Ne felejtse el lecserélni a szögletes zárójelben lévő helyőrzőket a saját értékeire:

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

Miután létrehozott egy fiókot, amely a fiók titkosítási kulcsát használja, konfigurálhatja az Azure Key Vault-ban vagy az Key Vault Managed Hardware Security Model (HSM) (előzetes verzió) szolgáltatásban tárolt, ügyfél által kezelt kulcsokat. Az ügyfél által felügyelt kulcsok kulcstartóban való tárolásával kapcsolatos további információkért lásd: Titkosítás konfigurálása az ügyfél által kezelt kulcsokkal, amelyek a következő [Azure Key Vault.](customer-managed-keys-configure-key-vault.md) Az ügyfél által felügyelt kulcsok felügyelt HSM-beli tárolásával kapcsolatos további információkért lásd: Titkosítás konfigurálása az Azure Key Vault [Managed HSM (előzetes verzió)](customer-managed-keys-configure-key-vault-hsm.md)szolgáltatásban tárolt ügyfél által kezelt kulcsokkal.

## <a name="verify-the-account-encryption-key"></a>A fiók titkosítási kulcsának ellenőrzése

Annak ellenőrzéséhez, hogy egy tárfiók egyik szolgáltatása használja-e a fiók titkosítási kulcsát, hívja meg az Azure CLI [az storage account](/cli/azure/storage/account#az_storage_account_show) parancsot. Ez a parancs a tárfiók tulajdonságainak és azok értékeinek egy halmazát adja vissza. Keresse meg az egyes szolgáltatások mezőjét a `keyType` titkosítási tulajdonságban, és ellenőrizze, hogy a beállítása `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Annak ellenőrzéséhez, hogy a tárfiókban egy szolgáltatás használja-e a fiók titkosítási kulcsát, hívja meg a [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) parancsot. Ez a parancs a tárfiók tulajdonságainak és azok értékeinek egy halmazát adja vissza. Keresse meg az egyes szolgáltatások mezőjét a tulajdonságon belül, és ellenőrizze, hogy `KeyType` `Encryption` a beállítása `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Annak ellenőrzéséhez, hogy egy tárfiók egyik szolgáltatása használja-e a fiók titkosítási kulcsát, hívja meg [az az storage account show](/cli/azure/storage/account#az_storage_account_show) parancsot. Ez a parancs a tárfiók tulajdonságainak és azok értékeinek egy halmazát adja vissza. Keresse meg az egyes szolgáltatások mezőjét a `keyType` titkosítási tulajdonságban, és ellenőrizze, hogy a beállítása `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="pricing-and-billing"></a>Árak és számlázás

A fiókra vonatkozó titkosítási kulcs használatára létrehozott tárfiók számlázása a Table Storage-kapacitásért és a tranzakciókért az alapértelmezett szolgáltatásra vonatkozó kulcstól eltérő sebességgel történik. Részletekért lásd: [Az Azure Table Storage díjszabása.](https://azure.microsoft.com/pricing/details/storage/tables/)

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Felhasználó által kezelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
- [Mi az a Azure Key Vault?](../../key-vault/general/overview.md)