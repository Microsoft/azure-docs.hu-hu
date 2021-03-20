---
title: Olyan Storage-fiók létrehozása, amely lehetővé teszi az infrastruktúra-titkosítást az adatmennyiség kettős titkosításához
titleSuffix: Azure Storage
description: Azok az ügyfelek, akik magasabb szintű garanciát igényelnek, hogy az adatvédelmek biztonságosak, az Azure Storage-infrastruktúra szintjén is engedélyezhetik a 256 bites AES-titkosítást. Ha engedélyezve van az infrastruktúra-titkosítás, a Storage-fiókban tárolt adattitkosítás két különböző titkosítási algoritmussal és két különböző kulccsal történik.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 612ba18ba71a22ad6c346b26008e688195c1d1e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746578"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Olyan Storage-fiók létrehozása, amely lehetővé teszi az infrastruktúra-titkosítást az adatmennyiség kettős titkosításához

Az Azure Storage 256 bites AES-titkosítással automatikusan titkosítja a Storage-fiókban tárolt összes adatmennyiséget, az egyik legerősebb blokk titkosítási algoritmust, és az FIPS 140-2-kompatibilis. Azok az ügyfelek, akik magasabb szintű garanciát igényelnek, hogy az adatvédelmek biztonságosak, az Azure Storage-infrastruktúra szintjén is engedélyezhetik a 256 bites AES-titkosítást. Ha az infrastruktúra-titkosítás engedélyezve van, a Storage-fiókban tárolt adatforgalom kétszer, &mdash; a szolgáltatási szinten, az infrastruktúra szintjén pedig &mdash; két különböző titkosítási algoritmussal és két különböző kulccsal van titkosítva. Az Azure Storage-beli adattárolók kettős titkosítása védelmet nyújt olyan forgatókönyvek esetében, amelyekben az egyik titkosítási algoritmus vagy kulcs sérülhet. Ebben az esetben a további titkosítási réteg továbbra is védi az adatait.

A szolgáltatási szintű titkosítás támogatja a Microsoft által felügyelt kulcsok vagy az ügyfél által felügyelt kulcsok használatát Azure Key Vault vagy Key Vault felügyelt hardveres biztonsági modellel (HSM) (előzetes verzió). Az infrastruktúra-szintű titkosítás a Microsoft által felügyelt kulcsokra támaszkodik, és mindig külön kulcsot használ. További információ az Azure Storage-titkosítással való kulcskezelő szolgáltatásról: [a titkosítási kulcsok kezelésének ismertetése](storage-service-encryption.md#about-encryption-key-management).

Az adattitkosítás megkettőzéséhez először létre kell hoznia egy infrastruktúra-titkosításhoz konfigurált Storage-fiókot. Ez a cikk azt ismerteti, hogyan hozhat létre olyan Storage-fiókot, amely lehetővé teszi az infrastruktúra titkosítását.

## <a name="register-to-use-infrastructure-encryption"></a>Regisztrálás az infrastruktúra-titkosítás használatára

Ha olyan Storage-fiókot szeretne létrehozni, amelynél engedélyezve van az infrastruktúra-titkosítás, először regisztrálnia kell ezt a funkciót az Azure-ban a PowerShell vagy az Azure CLI használatával.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell-lel való regisztráláshoz hívja meg a [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsot.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Ha ellenőriznie szeretné a regisztráció állapotát a PowerShell-lel, hívja meg a [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) parancsot.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

A regisztráció jóváhagyása után újra regisztrálnia kell az Azure Storage erőforrás-szolgáltatót. Ha újra szeretné regisztrálni az erőforrás-szolgáltatót a PowerShell-lel, hívja meg a [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) parancsot.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való regisztrációhoz hívja meg az az [Feature Register](/cli/azure/feature#az-feature-register) parancsot.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Az Azure CLI-vel való regisztráció állapotának megtekintéséhez hívja meg az az [Feature](/cli/azure/feature#az-feature-show) parancsot.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

A regisztráció jóváhagyása után újra regisztrálnia kell az Azure Storage erőforrás-szolgáltatót. Az erőforrás-szolgáltató Azure CLI-vel való újbóli regisztrálásához hívja meg az az [Provider Register](/cli/azure/provider#az-provider-register) parancsot.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Olyan fiók létrehozása, amelyen engedélyezve van az infrastruktúra-titkosítás

A fiók létrehozásakor konfigurálnia kell egy Storage-fiókot az infrastruktúra-titkosítás használatára. A Storage-fióknak általános célú v2 típusúnak kell lennie.

Az infrastruktúra titkosítása nem engedélyezhető vagy tiltható le a fiók létrehozása után.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha a PowerShell használatával olyan Storage-fiókot szeretne létrehozni, amelyen engedélyezve van az infrastruktúra-titkosítás, kövesse az alábbi lépéseket:

1. A Azure Portal navigáljon a Storage- **fiókok** lapra.
1. Válassza a **Hozzáadás** gombot egy új általános célú v2 Storage-fiók hozzáadásához.
1. A **speciális** lapon keresse meg az **infrastruktúra** titkosítása elemet, majd kattintson az **engedélyezve** lehetőségre.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a Storage-fiók létrehozásának befejezéséhez.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="A fiók létrehozásakor az infrastruktúra titkosításának engedélyezését bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha engedélyezve van az infrastruktúra-titkosítással rendelkező Storage-fiók létrehozása a PowerShell használatával, akkor győződjön meg arról, hogy telepítette az az [. Storage PowerShell-modult](https://www.powershellgallery.com/packages/Az.Storage)a 2.2.0 vagy újabb verzióra. További információ: [Install Azure PowerShell](/powershell/azure/install-az-ps).

Ezután hozzon létre egy általános célú v2 Storage-fiókot a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancs meghívásával. Adja `-RequireInfrastructureEncryption` meg az infrastruktúra-titkosítás engedélyezésének lehetőségét.

Az alábbi példa bemutatja, hogyan hozhat létre olyan általános célú v2-es Storage-fiókot, amely olvasási hozzáférésű geo-redundáns tárolóhoz (RA-GRS) van konfigurálva, és az infrastruktúra titkosítása engedélyezve van az adattitkosításhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI-t olyan Storage-fiók létrehozására szeretné használni, amelyen engedélyezve van az infrastruktúra-titkosítás, győződjön meg arról, hogy telepítette az Azure CLI-es vagy újabb verzióját. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Ezután hozzon létre egy általános célú v2-es Storage-fiókot az az [Storage Account Create](/cli/azure/storage/account#az-storage-account-create) parancs meghívásával, és `--require-infrastructure-encryption option` engedélyezze az infrastruktúra titkosítását.

Az alábbi példa bemutatja, hogyan hozhat létre olyan általános célú v2-es Storage-fiókot, amely olvasási hozzáférésű geo-redundáns tárolóhoz (RA-GRS) van konfigurálva, és az infrastruktúra titkosítása engedélyezve van az adattitkosításhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

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

A következő JSON-példa egy általános célú v2-es Storage-fiókot hoz létre, amely olvasási hozzáférésű geo-redundáns tárolóhoz (RA-GRS) van konfigurálva, és az infrastruktúra titkosítása engedélyezve van az adattitkosításhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

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

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Annak ellenőrzése, hogy engedélyezve van-e az infrastruktúra-titkosítás

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Az alábbi lépéseket követve ellenőrizheti, hogy engedélyezve van-e az infrastruktúra-titkosítás a Azure Portal Storage-fiókhoz:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. A **Beállítások** területen válassza a **titkosítás** lehetőséget.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Képernyőkép arról, hogyan ellenőrizheti, hogy az infrastruktúra-titkosítás engedélyezve van-e a fiókhoz":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Annak ellenőrzéséhez, hogy az infrastruktúra-titkosítás engedélyezve van-e egy Storage-fiókhoz a PowerShell használatával, hívja meg a [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) parancsot. Ez a parancs a Storage-fiók tulajdonságait és azok értékeit adja vissza. Kérje le a `RequireInfrastructureEncryption` mezőt a `Encryption` tulajdonságon belül, és ellenőrizze, hogy be van-e állítva `True` .

A következő példa a tulajdonság értékét kérdezi le `RequireInfrastructureEncryption` . Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Annak ellenőrzéséhez, hogy az infrastruktúra-titkosítás engedélyezve van-e egy Storage-fiókhoz az Azure CLI-vel, hívja meg az az [Storage Account show](/cli/azure/storage/account#az-storage-account-show) parancsot. Ez a parancs a Storage-fiók tulajdonságait és azok értékeit adja vissza. Keresse meg a `requireInfrastructureEncryption` mezőt a `encryption` tulajdonságon belül, és ellenőrizze, hogy be van-e állítva `true` .

A következő példa a tulajdonság értékét kérdezi le `requireInfrastructureEncryption` . Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

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
- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
