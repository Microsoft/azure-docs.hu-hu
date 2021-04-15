---
title: A tárfiókhoz használt titkosításikulcs-modell meghatározása
titleSuffix: Azure Storage
description: A Azure Portal, a PowerShell vagy az Azure CLI használatával ellenőrizheti, hogyan kezelik a tárfiók titkosítási kulcsait. A kulcsokat a Microsoft (alapértelmezés) vagy az ügyfél is képes kezelni. A felhasználó által kezelt kulcsokat a következő tárolóban Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: ef0f32ecc59bea6ee7a0f7ff12083fd2358c223c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478913"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>A tárfiókhoz használt Azure Storage titkosításikulcs-modell meghatározása

A tárfiókban tárolt adatokat az Azure Storage automatikusan titkosítja. Az Azure Storage Encryption két lehetőséget kínál a titkosítási kulcsok kezelésére a tárfiók szintjén:

- **Microsoft által felügyelt kulcsok.** Alapértelmezés szerint a Microsoft kezeli a tárfiók titkosításához használt kulcsokat.
- **Felhasználó által kezelt kulcsok.** Dönthet úgy is, hogy a tárfiók titkosítási kulcsait kezeli. A felhasználó által kezelt kulcsokat a következő tárolóban Azure Key Vault.

Emellett egyes Blob Storage-műveletekre vonatkozó egyedi kérések szintjén is meg lehet adni titkosítási kulcsot. Ha a kéréshez titkosítási kulcs van megadva, az felülírja a tárfiókon aktív titkosítási kulcsot. További információ: Ügyfél által megadott kulcs [megadása a Blob Storage-hoz való kéréshez.](../blobs/storage-blob-customer-provided-key.md)

További információ a titkosítási kulcsokról: [Azure Storage encryption for data at rest (Az Azure Storage titkosítása az adattároláshoz).](storage-service-encryption.md)

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>A tárfiók titkosítási kulcsmodellének ellenőrzése

Annak megállapításához, hogy egy tárfiók a Microsoft által felügyelt vagy az ügyfél által kezelt kulcsokat használ-e a titkosításhoz, használja az alábbi módszerek egyikét.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A tárfiók titkosítási modelljének ellenőrzéshez kövesse az Azure Portal lépéseket:

1. Az Azure Portalon lépjen a tárfiókra.
1. Válassza a **Titkosítás beállítást,** és jegyezze fel a beállítást.

Az alábbi képen egy, a Microsoft által felügyelt kulcsokkal titkosított tárfiók látható:

![A Microsoft által felügyelt kulcsokkal titkosított fiók megtekintése](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

Az alábbi képen egy ügyfél által kezelt kulcsokkal titkosított tárfiók látható:

![Képernyőkép a titkosítási kulcs beállításról a Azure Portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A tárfiók titkosítási modelljének PowerShell használatával való ellenőrzéshez hívja meg a [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) parancsot, majd ellenőrizze a fiók **KeySource** tulajdonságát.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Ha a **KeySource** tulajdonság értéke , akkor a fiók a Microsoft által felügyelt `Microsoft.Storage` kulcsokkal van titkosítva. Ha a **KeySource** tulajdonság értéke , akkor a fiók ügyfél által kezelt kulcsokkal `Microsoft.Keyvault` van titkosítva.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

A tárfiók titkosítási modelljének Azure CLI használatával való ellenőrzéshez hívja meg az [az storage account show](/cli/azure/storage/account#az-storage-account-show) parancsot, majd ellenőrizze a fiók **keySource** tulajdonságát.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Ha a **keySource** tulajdonság értéke , akkor a fiók a Microsoft által felügyelt `Microsoft.Storage` kulcsokkal van titkosítva. Ha a **keySource** tulajdonság értéke , akkor a fiók ügyfél által kezelt kulcsokkal `Microsoft.Keyvault` van titkosítva.

---

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Felhasználó által kezelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
