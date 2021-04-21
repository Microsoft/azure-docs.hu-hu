---
title: Tárfiók létrehozása engedélyezett infrastruktúra-titkosítással az adatok kettős titkosítása érdekében
titleSuffix: Azure Storage
description: Azok az ügyfelek, akiknek magasabb szintű biztonságra van szükségük az adataik biztonságának biztosítása érdekében, 256 bites AES-titkosítást is engedélyezhet az Azure Storage infrastruktúra szintjén. Ha engedélyezve van az infrastruktúra-titkosítás, a tárfiókban található adatok két különböző titkosítási algoritmussal és két különböző kulccsal vannak titkosítva.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 23b3ca919be030490cca06f31dac623d7f80be44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790382"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Tárfiók létrehozása engedélyezett infrastruktúra-titkosítással az adatok kettős titkosítása érdekében

Az Azure Storage a szolgáltatás szintjén automatikusan titkosítja a tárfiókban található összes adatot 256 bites AES-titkosítással, amely az egyik legerősebb elérhető blokktitkosítás, és FIPS 140-2 szabványnak megfelelő. Azok az ügyfelek, akiknek magasabb szintű biztonságra van szükségük az adataik biztonságának biztosítása érdekében, 256 bites AES-titkosítást is engedélyezhet az Azure Storage infrastruktúra szintjén. Ha engedélyezve van az infrastruktúra-titkosítás, a rendszer a tárfiókban található adatokat kétszer titkosítja a szolgáltatás szintjén, egyszer pedig az infrastruktúra szintjén két különböző titkosítási algoritmussal és &mdash; &mdash; két különböző kulccsal. Az Azure Storage-adatok kettős titkosítása védelmet nyújt az olyan forgatókönyvek ellen, ahol az egyik titkosítási algoritmus vagy kulcs biztonsága sérülhet. Ebben a forgatókönyvben a további titkosítási réteg továbbra is védi az adatokat.

A szolgáltatásszintű titkosítás támogatja a Microsoft által felügyelt vagy az ügyfél által felügyelt kulcsok használatát az Azure Key Vault vagy Key Vault Managed Hardware Security Model (HSM) (előzetes verzió) használatával. Az infrastruktúraszintű titkosítás a Microsoft által felügyelt kulcsokra támaszkodik, és mindig külön kulcsot használ. További információ az Azure Storage-titkosítással való kulcskezelésről: [Tudnivalók a titkosítási kulcskezelésről.](storage-service-encryption.md#about-encryption-key-management)

Az adatok kétszeres titkosításához először létre kell hoznia egy infrastruktúra-titkosításra konfigurált tárfiókot. Ez a cikk azt ismerteti, hogyan hozhat létre olyan tárfiókot, amely lehetővé teszi az infrastruktúra titkosítását.

## <a name="register-to-use-infrastructure-encryption"></a>Regisztrálás infrastruktúra-titkosítás használatára

Olyan tárfiók létrehozásához, amely esetében engedélyezve van az infrastruktúra-titkosítás, először regisztrálnia kell a funkció Azure-ral való használatához a PowerShell vagy az Azure CLI használatával.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell-regisztrációhoz hívja meg [a Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsot.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

A PowerShell-regisztráció állapotának ellenőrzéshez hívja meg a [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) parancsot.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

A regisztráció jóváhagyása után újra regisztrálnia kell az Azure Storage erőforrás-szolgáltatót. Ha újra regisztrálnia kell az erőforrás-szolgáltatót a PowerShellben, hívja meg a [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) parancsot.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-regisztrációhoz hívja az [az feature register](/cli/azure/feature#az_feature_register) parancsot.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Az Azure CLI-regisztráció állapotának ellenőrzéshez hívja meg az [az feature](/cli/azure/feature#az_feature_show) parancsot.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

A regisztráció jóváhagyása után újra regisztrálnia kell az Azure Storage erőforrás-szolgáltatót. Ha újra regisztrálnia kell az erőforrás-szolgáltatót az Azure CLI-hez, hívja meg [az az provider register](/cli/azure/provider#az_provider_register) parancsot.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Fiók létrehozása engedélyezett infrastruktúra-titkosítással

A tárfiókot úgy kell konfigurálnia, hogy a fiók létrehozásakor infrastruktúra-titkosítást használjon. A tárfióknak általános célú v2 típusúnak kell lennie.

A fiók létrehozása után az infrastruktúra-titkosítás nem engedélyezhető vagy tiltható le.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha a PowerShell használatával olyan tárfiókot hoz létre, amelynél engedélyezve van az infrastruktúra-titkosítás, kövesse az alábbi lépéseket:

1. A Azure Portal lépjen a **Tárfiókok lapra.**
1. Új általános **célú** v2-tárfiók hozzáadásához kattintson a Hozzáadás gombra.
1. A Speciális **lapon keresse** meg az **Infrastruktúra-titkosítás** adatokat, és válassza az **Engedélyezve lehetőséget.**
1. A **tárfiók létrehozásának befejezéséhez** válassza az Áttekintés + létrehozás lehetőséget.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Az infrastruktúra-titkosítás fiók létrehozásakor való engedélyezését bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha a PowerShell használatával olyan tárfiókot hoz létre, amely esetében engedélyezve van az infrastruktúra-titkosítás, győződjön meg arról, hogy telepítette az [Az.Storage PowerShell-modul](https://www.powershellgallery.com/packages/Az.Storage)2.2.0-s vagy újabb verzióját. További információ: [Install Azure PowerShell.](/powershell/azure/install-az-ps)

Ezután hozzon létre egy általános célú v2-tárfiókot a [New-AzStorageAccount parancs hívása](/powershell/module/az.storage/new-azstorageaccount) segítségével. Engedélyezze az `-RequireInfrastructureEncryption` infrastruktúra-titkosítást.

Az alábbi példa bemutatja, hogyan hozhat létre egy általános célú v2-tárfiókot, amely olvasási hozzáférésű georedundáns tárolásra (RA-GRS) van konfigurálva, és az adatok kettős titkosítására engedélyezve van az infrastruktúra-titkosítás. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI használatával olyan tárfiókot hoz létre, amely esetében engedélyezve van az infrastruktúra-titkosítás, győződjön meg arról, hogy az Azure CLI 2.8.0-s vagy újabb verzióját telepítette. További információ: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

Ezután hozzon létre egy általános célú v2-tárfiókot [az az storage account create](/cli/azure/storage/account#az_storage_account_create) paranccsal, és az paranccsal engedélyezze az `--require-infrastructure-encryption option` infrastruktúra-titkosítást.

Az alábbi példa bemutatja, hogyan hozhat létre egy általános célú v2-tárfiókot, amely olvasási hozzáférésű georedundáns tárolásra (RA-GRS) van konfigurálva, és az adatok kettős titkosítására engedélyezve van az infrastruktúra-titkosítás. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Sablon](#tab/template)

A következő JSON-példa egy általános célú v2-tárfiókot hoz létre, amely írási hozzáférésű georedundáns tárolásra (RA-GRS) van konfigurálva, és az adatok kettős titkosítására engedélyezve van az infrastruktúra-titkosítás. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Az infrastruktúra titkosításának engedélyezése

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Annak ellenőrzéséhez, hogy az infrastruktúra-titkosítás engedélyezve van-e a tárfiókon a Azure Portal kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. A Beállítások **alatt** válassza a Titkosítás **lehetőséget.**

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Képernyőkép annak ellenőrzésével, hogy az infrastruktúra titkosítása engedélyezve van-e a fiókhoz":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) paranccsal ellenőrizheti, hogy engedélyezve van-e az infrastruktúra-titkosítás egy tárfiókhoz a PowerShell használatával. Ez a parancs a tárfiók tulajdonságainak és azok értékeinek egy halmazát adja vissza. Olvassa be `RequireInfrastructureEncryption` a tulajdonságban `Encryption` található mezőt, és ellenőrizze, hogy a beállítása `True` .

Az alábbi példa a tulajdonság értékét olvassa `RequireInfrastructureEncryption` be. Ne felejtse el lecserélni a szögletes zárójelben lévő helyőrzőket a saját értékeire:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Annak ellenőrzéséhez, hogy az infrastruktúra-titkosítás engedélyezve van-e egy tárfiókhoz az Azure CLI-val, hívja meg [az az storage account show](/cli/azure/storage/account#az_storage_account_show) parancsot. Ez a parancs a tárfiók tulajdonságainak és azok értékeinek egy halmazát adja vissza. Keresse meg a `requireInfrastructureEncryption` tulajdonságban található mezőt, `encryption` és ellenőrizze, hogy a beállítása `true` .

Az alábbi példa a tulajdonság értékét olvassa `requireInfrastructureEncryption` be. Ne felejtse el lecserélni a szögletes zárójelben lévő helyőrzőket a saját értékeire:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Felhasználó által kezelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
