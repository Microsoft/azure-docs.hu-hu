---
title: Blobok helyreállítható törlésének engedélyezése
titleSuffix: Azure Storage
description: Engedélyezze a blobok soft delete funkcióját, hogy megvédje a blobadatokat a véletlen törléstől vagy felülírástól.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4a8d1f872ca042429276b8f0e1112bc5837d8e38
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869274"
---
# <a name="enable-soft-delete-for-blobs"></a>Blobok helyreállítható törlésének engedélyezése

A blobok és azok verziói, pillanatképei és metaadataik véletlen törlése és felülírása úgy védhető meg, hogy a törölt adatokat egy adott ideig megőrzik a rendszerben. A megőrzési időszak alatt visszaállíthatja a blobot a törléskor. A megőrzési időszak lejárta után a blob véglegesen törlődik. További információk a blobok soft delete (blobok soft delete ) törlésével kapcsolatban: Soft delete for blobs (Blobok soft [delete for blobok).](soft-delete-blob-overview.md)

A blobok soft delete (blobok soft delete) szolgáltatása a blobadatok átfogó adatvédelmi stratégiájának része. A Microsoft adatvédelmi javaslataival kapcsolatos további információkért lásd: [Adatvédelem áttekintése.](data-protection-overview.md)

## <a name="enable-blob-soft-delete"></a>Blobok soft delete funkció engedélyezése

A blobok soft delete (blobok soft delete) szolgáltatása alapértelmezés szerint le van tiltva az új tárfiókok esetén. A tárfiókok ideiglenes törlését bármikor engedélyezheti vagy letilthatja a Azure Portal, a PowerShell vagy az Azure CLI használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha engedélyezni szeretné a blobok blobok soft delete funkcióját a tárfiókhoz a Azure Portal kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/) lépjen a tárfiókra.
1. Keresse meg **az Adatvédelem lehetőséget** a Blob service. 
1. A Helyreállítás **szakaszban** válassza a Helyreállító törlés **bekapcsolás blobok számára lehetőséget.**
1. 1 és 365 nap közötti megőrzési idő megadása. A Microsoft a hétnapos minimális megőrzési megőrzési időszakot javasolja.
1. Mentse a módosításokat.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Képernyőkép a soft delete engedélyezéséről a Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A blobok a PowerShell használatával való törlésének engedélyezéséhez hívja meg az [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) parancsot, és adja meg a megőrzési megőrzési időszakot napokban.

Az alábbi példa engedélyezi a blobok soft delete funkcióját, és hét napra állítja a megőrzési megőrzési időszakot. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

A blobok aktuális soft delete beállításainak ellenőrzéséhez hívja meg a [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) parancsot:

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-CLI)

Ha engedélyezni szeretné a blobok soft delete parancsát az Azure CLI-val, hívja meg [az az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) parancsot, és adja meg a megőrzési megőrzési időszakot napokban.

Az alábbi példa engedélyezi a blobok soft delete funkcióját, és hét napra állítja a megőrzési megőrzési időszakot. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

A blobok jelenlegi soft delete beállításainak ellenőrzéséhez hívja meg [az az storage account blob-service-properties show](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show) parancsot:

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Következő lépések

- [Blobok helyreállítható törlése](soft-delete-blob-overview.md)
- [Helyreállíthatóan törölt blobok kezelése és visszaállítása](soft-delete-blob-manage.md)
