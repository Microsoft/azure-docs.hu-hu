---
title: Azure-fájlmegosztások visszaállítása az Azure CLI használatával
description: Megtudhatja, hogyan használhatja az Azure CLI-t a helyreállítási tárban található, biztonsági másolatban tárolt Azure-fájlmegosztások visszaállítására
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 2edc2281c29023bb8dfe0268f23eacfe081d413e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768509"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Azure-fájlmegosztások visszaállítása az Azure CLI használatával

Az Azure CLI parancssori felületeket biztosít az Azure-erőforrások kezeléséhez. Nagyszerű eszköz az Azure-erőforrások használatára vonatkozó egyéni automatizálások létrehozásához. Ez a cikk azt ismerteti, hogyan lehet visszaállítani egy [](./backup-overview.md) teljes fájlmegosztást vagy adott fájlokat egy, a Azure Backup által létrehozott visszaállítási pontból az Azure CLI használatával. Az [Azure PowerShell](./backup-azure-afs-automation.md) vagy az [Azure Portal](backup-afs.md) használatával is elvégezheti ezeket a lépéseket.

A cikk végére megtanulja, hogyan végezheti el a következő műveleteket az Azure CLI-val:

* Tekintse meg a biztonsági másolattal eltelő Azure-fájlmegosztások visszaállítási pontjait.
* Teljes Azure-fájlmegosztás visszaállítása.
* Egyes fájlok vagy mappák visszaállítása.

>[!NOTE]
> Azure Backup mostantól támogatja több fájl vagy mappa visszaállítását az eredeti vagy egy másik helyre az Azure CLI használatával. További információért tekintse meg a dokumentum [Több](#restore-multiple-files-or-folders-to-original-or-alternate-location) fájl vagy mappa visszaállítása az eredeti vagy másik helyre című szakaszát.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már rendelkezik olyan Azure-fájlmegosztásokkal, amelyekről biztonsági mentése Azure Backup. Ha még nem rendelkezik ilyensel, tekintse meg az Azure-fájlmegosztások biztonsági mentése a [CLI-val](backup-afs-cli.md) részt a fájlmegosztás biztonsági mentésének konfigurálásával. Ehhez a cikkhez a következő erőforrásokat használja:

| Fájlmegosztás | Tárfiók | Region | Részletek |
|---|---|---|---|
| *azurefiles* | *afsaccount* | EastUS | Az eredeti forrás biztonsági másolata a Azure Backup |
| *azurefiles1* | *afaccount1* | EastUS | Az alternatív helyre való helyreállításhoz használt célforrás |

A fájlmegosztások hasonló szerkezetét használva kipróbálhatja a cikkben leírtaknak megfelelő visszaállítási típusokat.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.18-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Az Azure-fájlmegosztás helyreállítási pontjainak beolvasása

Használja az [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) parancsmagot a biztonsági másolatban található fájlmegosztás összes helyreállítási pontának listához.

Az alábbi példa lekéri az *afsaccount* tárfiókban található *azurefiles* fájlmegosztás helyreállítási pontjainak listáját.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Az előző parancsmagot a tároló és az elem rövid nevének használatával is futtathatja a következő két további paraméter megszava használatával:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Az eredményhalmaz a helyreállítási pontok listája, amely az egyes visszaállítási pontok idő- és konzisztencia-adatait tartalmazza.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

A **kimenet Name** attribútuma megegyezik a helyreállítási pont nevével, amely a helyreállítási műveletekben az **--rp-name** paraméter értékeként használható.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Teljes megosztás helyreállítása az Azure CLI használatával

Ezzel a visszaállítási lehetőséggel visszaállíthatja a teljes fájlmegosztást az eredeti vagy egy másik helyen.

A visszaállítási műveletek végrehajtásához adja meg a következő paramétereket:

* **--container-name:** Annak a tárfióknak a neve, amely az eredeti fájlmegosztás biztonsági másolatát tárolja. A tároló nevének vagy rövid nevének lekéréséhez használja [az az backup container list](/cli/azure/backup/container#az_backup_container_list) parancsot.
* **--item-name:** A visszaállítási művelethez használni kívánt eredeti fájlmegosztás neve. A biztonsági mentési elem nevének vagy rövid nevének lekéréséhez használja [az az backup item list](/cli/azure/backup/item#az_backup_item_list) parancsot.

### <a name="restore-a-full-share-to-the-original-location"></a>Teljes megosztás visszaállítása az eredeti helyre

Az eredeti helyre való visszaállításkor nem kell megadnia a célhoz kapcsolódó paramétereket. Csak **az Ütközés feloldása** lehetőség legyen megszabadva.

A következő példa az [az backup restore-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) parancsmagot  használja az eredeti helyre beállított visszaállítási móddal az *azurefiles* fájlmegosztás eredeti helyen való visszaállításához. Használja a 932883129628959823 helyreállítási pontot, amelyet az Azure-fájlmegosztás helyreállítási pontjainak beolvasása során [szerzett be:](#fetch-recovery-points-for-the-azure-file-share)

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

A **kimenet Name** attribútuma megegyezik a visszaállítási művelet biztonsági mentési szolgáltatása által létrehozott feladat nevével. A feladat állapotának nyomon követéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Teljes megosztás visszaállítása másik helyre

Ezzel a beállítással visszaállíthat egy fájlmegosztást egy másik helyre, és megtarthatja az eredeti fájlmegosztást. Adja meg a következő paramétereket a másodlagos helyre való helyreállításhoz:

* **--target-storage-account:** Az a tárfiók, amelyre a biztonsági másolat tartalma vissza lesz állítani. A cél tárfióknak és a tárolónak ugyanazon a helyen kell lennie.
* **--target-file-share:** A cél tárfiókon belüli fájlmegosztás, amelyre a biztonsági másolat tartalma vissza lesz állítani.
* **--target-folder:** Annak a fájlmegosztásnak a mappája, amelybe az adatok visszaállnak. Ha a biztonsági másolat tartalmát vissza kell állítani egy gyökérmappába, üres sztringként adja meg a célmappa értékeit.
* **--resolve-conflict:** Utasítás a visszaállított adatokkal való ütközés esetén. Felülírja **vagy kihagyja** a **következőt:**.

Az alábbi példa az [az backup restore-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) visszaállítási módú visszaállítási módot használja alternatív helyként az *afsaccount* tárfiókban található *azurefiles* fájlmegosztás visszaállításához az *afaccount1* tárfiók  *azurefiles1 fájlmegosztásában.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

A **kimenet Name** (Név) attribútuma megegyezik a visszaállítási művelet biztonsági mentési szolgáltatása által létrehozott feladat nevével. A feladat állapotának nyomon követéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

## <a name="item-level-recovery"></a>Elemszintű helyreállítás

Ezzel a visszaállítási lehetőséggel visszaállíthat egyes fájlokat vagy mappákat az eredeti vagy egy másik helyen.

A visszaállítási műveletek végrehajtásához adja meg a következő paramétereket:

* **--container-name:** Annak a tárfióknak a neve, amely az eredeti fájlmegosztás biztonsági másolatát tárolja. A tároló nevének vagy rövid nevének lekéréséhez használja [az az backup container list](/cli/azure/backup/container#az_backup_container_list) parancsot.
* **--item-name:** A visszaállítási művelethez használni kívánt eredeti fájlmegosztás neve. A biztonsági mentési elem nevének vagy rövid nevének lekéréséhez használja [az az backup item list](/cli/azure/backup/item#az_backup_item_list) parancsot.

Adja meg a következő paramétereket a helyreállítani kívánt elemekhez:

* **SourceFilePath:** A fájlmegosztáson belül visszaállítani kívánt fájl abszolút elérési útja sztringként. Ez az elérési út megegyezik az [az storage file download vagy](/cli/azure/storage/file#az_storage_file_download) az az storage file [show](/cli/azure/storage/file#az_storage_file_show) CLI-parancsokban használt elérési útokkal.
* **SourceFileType**: Adja meg, hogy ki van-e választva könyvtár vagy fájl. Elfogadja a **könyvtárat vagy** **a fájlt.**
* **ResolveConflict:** Utasítás a visszaállított adatokkal való ütközés esetén. Felülírja **vagy kihagyja** a **következőt:**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Egyes fájlok vagy mappák visszaállítása az eredeti helyre

Használja az [az backup restore restore-azurefiles](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) parancsmagot az eredeti helyre beállított visszaállítási móddal adott fájlok vagy mappák eredeti helyükre való visszaállításához. 

Az alábbi példa visszaállítja *aRestoreTest.txt* fájlt az eredeti helyére: az *azurefiles* fájlmegosztást.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

A **kimenet Name** (Név) attribútuma megegyezik a visszaállítási művelet biztonsági mentési szolgáltatása által létrehozott feladat nevével. A feladat állapotának nyomon követéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Egyes fájlok vagy mappák visszaállítása egy másik helyre

Adott fájlok vagy mappák másik helyre történő visszaállításához használja az [az backup restore-azurefiles](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) parancsmagot úgy, hogy a visszaállítási mód alternatív helyre van *állítva,* és adja meg a következő célhoz kapcsolódó paramétereket:

* **--target-storage-account:** Az a tárfiók, amelyre a biztonsági másolat tartalma vissza lesz állítani. A cél tárfióknak és a tárolónak ugyanazon a helyen kell lennie.
* **--target-file-share:** A cél tárfiókon belüli fájlmegosztás, amelyre a biztonsági másolat tartalma vissza lesz állítani.
* **--target-folder:** Annak a fájlmegosztásnak a mappája, amelybe az adatok visszaállnak. Ha a biztonsági másolat tartalmát vissza kell állítani egy gyökérmappába, üres sztringként adja meg a célmappa értékét.

Az alábbi példa visszaállítja az eredetileg az *azurefiles* fájlmegosztásban található *RestoreTest.txt-fájlt* egy másik helyre: az *afaccount1* tárfiókban üzemeltetett *azurefiles1* fájlmegosztás *restoredata* mappáját.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

A **kimenet Name** attribútuma megegyezik a visszaállítási művelet biztonsági mentési szolgáltatása által létrehozott feladat nevével. A feladat állapotának nyomon követéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Több fájl vagy mappa visszaállítása az eredeti vagy másik helyre

Több elem visszaállításához adja át a **source-file-path**  paraméter értékét a visszaállítani kívánt fájlok vagy mappák szóközöktől elválasztott elérési útjaként.

Az alábbi példa visszaállítja a *Restore.txt* *AFS-Report.docx* a fájlokat az eredeti helyükre.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

A kimenet az alábbihoz hasonló lesz:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

A **kimenet Name** attribútuma megegyezik a visszaállítási művelet biztonsági mentési szolgáltatása által létrehozott feladat nevével. A feladat állapotának nyomon követéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

Ha több elemet szeretne visszaállítani egy másik helyre, használja a fenti parancsot a célhoz kapcsolódó paraméterek megadásával, az egyes fájlok vagy mappák visszaállítása egy másik helyre című szakaszban leírtak [szerint.](#restore-individual-files-or-folders-to-an-alternate-location)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [kezelheti az Azure-fájlmegosztások biztonsági másolatokat az Azure CLI-val.](manage-afs-backup-cli.md)
