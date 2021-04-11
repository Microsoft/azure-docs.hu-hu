---
title: Blobok helyreállítható törlésének engedélyezése
titleSuffix: Azure Storage
description: A Blobok véletlen törlésének és felülírásának megakadályozása érdekében engedélyezze a Blobok adatainak tárolását.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11323f2aec05935b9dc45187ed54597e61af924d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554117"
---
# <a name="enable-soft-delete-for-blobs"></a>Blobok helyreállítható törlésének engedélyezése

A blob Soft delete szolgáltatás védi az egyes blobokat, azok verzióit, pillanatképeit és metaadatait a véletlen törlésből vagy felülírásokból azáltal, hogy a törölt adatokat egy adott időszakra vonatkozóan tartja karban. A megőrzési időszak alatt a blobot visszaállíthatja az állapotára törléskor. A megőrzési időszak lejárta után a blob véglegesen törölve lesz. A blob Soft delete szolgáltatással kapcsolatos további információkért lásd a Blobok helyreállítható [törlését](soft-delete-blob-overview.md)ismertető témakört.

A blob-törlés a blob-adatvédelemmel kapcsolatos átfogó adatvédelmi stratégia részét képezi. További információ a Microsoft adatvédelmi javaslatairól: [Adatvédelem – áttekintés](data-protection-overview.md).

## <a name="enable-blob-soft-delete"></a>BLOB törlésének engedélyezése

A blob Soft delete alapértelmezés szerint le van tiltva egy új Storage-fiókhoz. A Storage-fiókhoz a Azure Portal, a PowerShell vagy az Azure CLI használatával bármikor engedélyezheti vagy letilthatja a helyreállítható törlést.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépéseket követve engedélyezheti a Blobok törlését a Storage-fiókjához a Azure Portal használatával:

1. Az [Azure Portalon](https://portal.azure.com/) lépjen a tárfiókra.
1. Keresse meg az **Adatvédelem** lehetőséget a **blob Service** alatt.
1. A **helyreállítás** szakaszban jelölje be **a nem kötelező törlés bekapcsolása a blobokhoz** lehetőséget.
1. 1 és 365 nap közötti megőrzési időtartamot kell megadni. A Microsoft a minimális megőrzési időtartamot javasolja hét nap alatt.
1. Mentse a módosításokat.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Képernyőfelvétel: a Azure Portal a Soft delete engedélyezése":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha engedélyezni szeretné a blob törlését a PowerShell-lel, hívja meg az [enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) parancsot, és állítsa be a megőrzési időszakot napokban.

A következő példa engedélyezi a Blobok törlését, és a megőrzési időtartamot hét napig állítja be. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

A blob Soft delete jelenlegi beállításainak vizsgálatához hívja meg a [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) parancsot:

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-CLI)

Ha az Azure CLI-vel szeretné engedélyezni a Blobok törlését, hívja meg az az [Storage Account blob-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) parancsot, és határozza meg a megőrzési időtartamot napokban megadva.

A következő példa engedélyezi a Blobok törlését, és a megőrzési időtartamot hét napig állítja be. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Ha szeretné megtekinteni a blob-alapú törlés aktuális beállításait, hívja meg az az [Storage Account blob-Service-Properties show](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_show) parancsot:

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Következő lépések

- [Blobok helyreállítható törlése](soft-delete-blob-overview.md)
- [A nem törölt Blobok kezelése és visszaállítása](soft-delete-blob-manage.md)
