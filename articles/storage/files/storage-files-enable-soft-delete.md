---
title: A soft delete engedélyezése – Azure-fájlmegosztások
description: Megtudhatja, hogyan engedélyezheti a helyreállítható törlést az Azure-fájlmegosztáson az adatok helyreállításához és a véletlen törlés megakadályozásához.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: a0dff310ce4a40b7a66cc548f3c77213f4a10e00
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717022"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Az Azure-fájlmegosztások esetében a soft delete engedélyezése

Az Azure Storage helyreállítható törlést biztosít a fájlmegosztások számára, így könnyebben helyreállíthatja az adatokat, ha egy alkalmazás vagy más tárfiók-felhasználó véletlenül törölte őket. A törléssel kapcsolatos további információkért lásd: How to prevent accidental delete of Azure file shares (Az Azure-fájlmegosztások véletlen [törlésének megakadályozása).](storage-files-prevent-file-share-deletion.md)

A következő szakaszok azt mutatják be, hogyan engedélyezheti és használhatja az Azure-fájlmegosztásokat egy meglévő tárfiókon:

# <a name="portal"></a>[Portál](#tab/azure-portal)

## <a name="getting-started"></a>Első lépések

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Lépjen a tárfiókhoz, és válassza a **Fájlmegosztások lehetőséget** az **Adattároló alatt.**
1. Válassza **az Engedélyezve lehetőséget** a Soft delete **(Nem végleges törlés) mellett.**
1. Minden **fájlmegosztás** esetében válassza az Engedélyezve lehetőséget **a Soft delete (Soft delete) beállításhoz.**
1. Válassza **a Fájlmegosztás megőrzési ideje napokban lehetőséget,** és adja meg a kívánt számot.
1. Válassza **a Mentés lehetőséget** az adatmegőrzési beállítások megerősítéséhez.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="Képernyőkép a tárfiók &quot;soft delete settings&quot; panelről. A fájlmegosztások &quot;soft delete&quot; szakaszának kiemelése, a váltógomb engedélyezése, a megőrzési időtartam beállítása és a mentés. Ez engedélyezi a soft delete parancsot a tárfiókban található összes fájlmegosztáshoz.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A soft delete parancsmagok az Azure CLI modul 2.1.3-as és újabb [verzióiban érhetők el.](/cli/azure/install-azure-cli)

## <a name="getting-started-with-cli"></a>Ismerkedés a CLI-val

A soft delete engedélyezéséhez frissítenie kell a fájl ügyfélszolgáltatás-tulajdonságait. Az alábbi példa a tárfiókban található összes fájlmegosztás esetében engedélyezi a soft delete parancsot:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

A következő paranccsal ellenőrizheti, hogy a soft delete engedélyezve van-e, és megtekintheti annak adatmegőrzési szabályzatát:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Előfeltétel

A soft delete parancsmagok az Az.Storage modul 4.8.0-s és újabb verzióiban érhetők el. 

## <a name="getting-started-with-powershell"></a>Bevezetés a PowerShell használatába

A soft delete engedélyezéséhez frissítenie kell a fájl ügyfélszolgáltatás-tulajdonságait. Az alábbi példa a tárfiókban található összes fájlmegosztás esetében engedélyezi a soft delete parancsot:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

A következő paranccsal ellenőrizheti, hogy a soft delete engedélyezve van-e, és megtekintheti annak adatmegőrzési szabályzatát:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Helyreállíthatóan törölt fájlmegosztás visszaállítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Helyreállíthatóan törölt fájlmegosztás visszaállítása:

1. Lépjen a tárfiókhoz, és válassza a **Fájlmegosztások lehetőséget.**
1. A fájlmegosztás panelen engedélyezze a **Törölt** megosztások megjelenítése megjelenik a törölt megosztások megjelenítéséhez.

    Ez megjeleníti a jelenleg **Törölt** állapotban lévő megosztásokat.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Ha az állapotoszlop, a névoszlop melletti oszlop Deleted (Törölve) állapotú, akkor a fájlmegosztás törölt állapotban van. És a megadott megőrzési időszak után véglegesen törlődik.":::

1. Jelölje ki a megosztást, és **válassza a undelete** lehetőséget, ezzel visszaállítja a megosztást.

    Meggyőződhet arról, hogy a megosztás visszaállt, mivel az állapota Aktív **állapotúra vált.**

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Ha az állapotoszlop, a névoszlop melletti oszlop aktívra van állítva, akkor a fájlmegosztás vissza lett állítva.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A soft delete parancsmagok az Azure CLI 2.1.3-as verziójában érhetők el. Helyreállíthatóan törölt fájlmegosztás visszaállításához először le kell szereznie `--deleted-version` a megosztás értékét. Ezt az értéket a következő paranccsal használhatja a tárfiók összes törölt megosztásának listához:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Miután azonosította a visszaállítani szeretne megosztást, a következő paranccsal használhatja a visszaállításhoz:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A soft delete parancsmagok az Az.Storage modul 4.8.0-s és újabb verzióiban érhetők el. Helyreállíthatóan törölt fájlmegosztás visszaállításához először le kell szereznie `-DeletedShareVersion` a megosztás értékét. Ezt az értéket a következő paranccsal használhatja a tárfiók összes törölt megosztásának listához:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Miután azonosította a visszaállítani szeretne megosztást, a következő paranccsal használhatja a visszaállításhoz:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>A soft delete letiltása

Ha nem szeretné tovább használni a soft delete parancsot, kövesse ezeket az utasításokat. A törölt fájlmegosztás végleges törléséhez törölnie kell azt, le kell tiltania a törlést, majd újra törölnie kell. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Lépjen a tárfiókhoz, és válassza a **Fájlmegosztások lehetőséget** az **Adattároló alatt.**
1. Válassza a Soft delete (Nem végleges **törlés) melletti hivatkozást.**
1. Az **összes fájlmegosztás** esetében válassza a **Letiltva lehetőséget a Soft delete (Letiltva) beállításhoz.**
1. Válassza **a Mentés lehetőséget** az adatmegőrzési beállítások megerősítéséhez.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="A soft delete letiltásával azonnal és véglegesen törölheti a tárfiókban lévő összes fájlmegosztást.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A soft delete parancsmagok az Azure CLI 2.1.3-as verziójában érhetők el. A következő paranccsal letilthatja a tárfiókon a nem végleges törlést:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A soft delete parancsmagok az Az.Storage modul 4.8.0-s és újabb verzióiban érhetők el. A következő paranccsal letilthatja a tárfiókon a nem végleges törlést:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Következő lépések

Az adatvédelem és a helyreállítás egy másik formájával kapcsolatos további információkért tekintse meg a megosztási pillanatképek áttekintését a [Azure Files.](storage-snapshots-files.md)