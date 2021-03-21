---
title: Soft delete – Azure-fájlmegosztás engedélyezése
description: Megtudhatja, hogyan engedélyezheti az Azure-fájlmegosztás helyreállítható törlését az adathelyreállításhoz, és megakadályozhatja a véletlen törlést.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/01/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 77381700f4257006b50e56ab7ffc037ef99d297c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218553"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>A Soft delete engedélyezése az Azure-fájlmegosztás esetében

Az Azure Storage helyreállítható törlést biztosít a fájlmegosztás számára, így könnyebben állíthatja helyre az adatokat, amikor egy alkalmazás vagy más Storage-fiók felhasználója véletlenül törölte azt. További információ a Soft delete szolgáltatásról: az [Azure-fájlmegosztás véletlen törlésének megakadályozása](storage-files-prevent-file-share-deletion.md).

A következő részekben bemutatjuk, hogyan engedélyezheti és használhatja a Soft delete használatát az Azure-fájlmegosztás számára egy meglévő Storage-fiókban:

# <a name="portal"></a>[Portál](#tab/azure-portal)

## <a name="getting-started"></a>Első lépések

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Navigáljon a Storage-fiókjához, és válassza a **Soft delete** elemet a **file Service** lehetőségnél.
1. Válassza az **engedélyezve** lehetőséget a fájlmegosztás helyreállítható **törléséhez**.
1. Válassza ki a **fájlmegosztás megőrzési időtartamát napokban** , és adja meg a választott számot.
1. A **Mentés** gombra kattintva erősítse meg az adatmegőrzési beállításokat.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Képernyőfelvétel a Storage-fiók Soft delete Settings panelről. A fájlmegosztás kiemelése szakasz, a váltás engedélyezése, a megőrzési időtartam beállítása és a mentés. Ezzel a megoldással engedélyezheti a Storage-fiókban lévő összes fájlmegosztás helyreállítható törlését.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A Soft delete parancsmagok az [Azure CLI-moduljának](/cli/azure/install-azure-cli)2.1.3-es és újabb verzióiban érhetők el.

## <a name="getting-started-with-cli"></a>Első lépések a CLI-vel

A Soft delete engedélyezéséhez frissítenie kell egy fájl ügyfél szolgáltatásának tulajdonságait. A következő példa egy Storage-fiókban lévő összes fájlmegosztás esetében lehetővé teszi a Soft delete használatát:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Ellenőrizheti, hogy engedélyezve van-e a Soft DELETE, és megtekintheti az adatmegőrzési szabályzatot a következő paranccsal:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Előfeltétel

A Soft delete parancsmagok az az. Storage modul 4.8.0 és újabb verzióiban érhetők el. 

## <a name="getting-started-with-powershell"></a>Bevezetés a PowerShell használatába

A Soft delete engedélyezéséhez frissítenie kell egy fájl ügyfél szolgáltatásának tulajdonságait. A következő példa egy Storage-fiókban lévő összes fájlmegosztás esetében lehetővé teszi a Soft delete használatát:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Ellenőrizheti, hogy engedélyezve van-e a Soft DELETE, és megtekintheti az adatmegőrzési szabályzatot a következő paranccsal:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Helyreállítható törölt fájlmegosztás visszaállítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

A törölt fájlmegosztás visszaállítása:

1. Navigáljon a Storage-fiókjához, és válassza a **fájlmegosztás** lehetőséget.
1. A fájlmegosztás panelen engedélyezze a **törölt megosztások megjelenítése** lehetőséget a törölt megosztások megjelenítéséhez.

    Ez megjeleníti a jelenleg **törölt** állapotban lévő megosztásokat is.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Ha az Állapot oszlop, a Name (név) oszlop melletti oszlop törölve értékre van állítva, akkor a fájlmegosztás nem megfelelő törlésű állapotban van. És véglegesen törölve lesz a megadott megőrzési idő után.":::

1. Válassza ki a megosztást, és válassza a **Törlés** visszavonása lehetőséget, ezzel visszaállítja a megosztást.

    Megerősítheti a megosztás visszaállítását, mivel az állapota **aktívra** vált.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Ha az Állapot oszlopban a Name (név) oszlop melletti oszlop aktív értékre van állítva, akkor a fájlmegosztás vissza lett állítva.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A Soft delete parancsmagok az Azure CLI 2.1.3-es verziójában érhetők el. A törölt fájlmegosztás visszaállításához először le kell kérnie a `--deleted-version` megosztás értékét. Az érték beszerzéséhez használja a következő parancsot a Storage-fiók összes törölt megosztásának listázásához:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Miután azonosította a visszaállítani kívánt megosztást, a következő paranccsal állíthatja vissza:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A Soft delete parancsmagok az az. Storage modul 4.8.0 és újabb verzióiban érhetők el. A törölt fájlmegosztás visszaállításához először le kell kérnie a `-DeletedShareVersion` megosztás értékét. Az érték beszerzéséhez használja a következő parancsot a Storage-fiók összes törölt megosztásának listázásához:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Miután azonosította a visszaállítani kívánt megosztást, a következő paranccsal állíthatja vissza:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Nem kötelező törlés letiltása

Ha szeretné leállítani a Soft delete használatát, vagy véglegesen törölni szeretne egy fájlmegosztást, kövesse az alábbi utasításokat:

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Navigáljon a Storage-fiókjához, és válassza a **Beállítások** alatt a **Soft Delete (Törlés** ) lehetőséget.
1. A **fájlmegosztás területen válassza a** **Letiltva** lehetőséget a fájlmegosztás helyreállítható **törléséhez**.
1. A **Mentés** gombra kattintva erősítse meg az adatmegőrzési beállításokat.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="A Soft delete letiltásával azonnal és véglegesen törölheti a Storage-fiókban lévő összes fájlmegosztást a szabadidőben.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A Soft delete parancsmagok az Azure CLI 2.1.3-es verziójában érhetők el. A következő parancs használatával tilthatja le a helyreállítható törlést a Storage-fiókjában:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A Soft delete parancsmagok az az. Storage modul 4.8.0 és újabb verzióiban érhetők el. A következő parancs használatával tilthatja le a helyreállítható törlést a Storage-fiókjában:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Következő lépések

Az adatvédelem és a helyreállítás más formájával kapcsolatos további információkért tekintse meg a [Azure Files-Pillanatképek megosztásának áttekintését](storage-snapshots-files.md)ismertető cikket.