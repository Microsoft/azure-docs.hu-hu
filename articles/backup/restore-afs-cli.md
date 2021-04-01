---
title: Azure-fájlmegosztás visszaállítása az Azure CLI-vel
description: Ismerje meg, hogyan állíthatja vissza az Azure-fájlmegosztás biztonsági mentését az Azure CLI használatával a Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: a025de7bfb9db037b2008d69be7782feabb482f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94562321"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Azure-fájlmegosztás visszaállítása az Azure CLI-vel

Az Azure CLI parancssori felületet biztosít az Azure-erőforrások kezeléséhez. Ez nagyszerű eszköz az Azure-erőforrások használatára szolgáló egyéni automatizálás kialakításához. Ez a cikk azt ismerteti, hogyan lehet visszaállítani egy teljes fájlmegosztást vagy adott fájlokat egy [Azure Backup](./backup-overview.md) által létrehozott visszaállítási pontról az Azure CLI használatával. Az [Azure PowerShell](./backup-azure-afs-automation.md) vagy az [Azure Portal](backup-afs.md) használatával is elvégezheti ezeket a lépéseket.

A cikk végén megtudhatja, hogyan hajthatja végre a következő műveleteket az Azure CLI-vel:

* Egy biztonsági másolattal rendelkező Azure-fájlmegosztás visszaállítási pontjainak megtekintése.
* Teljes Azure-fájlmegosztás visszaállítása.
* Egyéni fájlok vagy mappák visszaállítása.

>[!NOTE]
> Azure Backup mostantól támogatja több fájl vagy mappa visszaállítását az eredeti vagy egy másik helyre az Azure CLI használatával. További információért tekintse meg a [több fájl vagy mappa visszaállítása a jelen dokumentum eredeti vagy másodlagos helyre](#restore-multiple-files-or-folders-to-original-or-alternate-location) című szakaszát.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már rendelkezik egy Azure-fájlmegosztás Azure Backup által készített biztonsági mentéssel. Ha még nem rendelkezik ilyennel, tekintse meg [Az Azure-fájlmegosztás biztonsági mentése a parancssori](backup-afs-cli.md) felülettel című témakört a fájlmegosztás biztonsági mentésének konfigurálásához. Ebben a cikkben a következő erőforrásokat használja:

| Fájlmegosztás | Tárfiók | Region | Részletek |
|---|---|---|---|
| *azurefiles* | *afsaccount* | EastUS | Az eredeti forrás biztonsági másolata Azure Backup használatával |
| *azurefiles1* | *afaccount1* | EastUS | Másodlagos hely helyreállításához használt cél forrás |

A fájlmegosztás hasonló struktúrájának használatával kipróbálhatja az ebben a cikkben ismertetett visszaállítások különböző típusait.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.18 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Helyreállítási pontok beolvasása az Azure-fájlmegosztás számára

A biztonsági másolatban szereplő fájlmegosztás összes helyreállítási pontjának listázásához használja az az [Backup recoverypoint List](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) parancsmagot.

A következő példa beolvassa a helyreállítási pontok listáját a *azurefiles* -fájlmegosztás számára a *afsaccount* Storage-fiókban.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Az előző parancsmagot az alábbi két további paraméter megadásával is futtathatja a tároló és az elemek rövid nevével:

* **--Backup-Management-Type**: *azurestorage*
* **--munkaterhelés-Type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Az eredményhalmaz azon helyreállítási pontok listája, amelyekben az egyes visszaállítási pontokhoz tartozó idő-és konzisztencia-adatok szerepelnek.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

A kimenetben található **Name** attribútum a helyreállítási pont neveként a helyreállítási műveletekben a **--RP-Name** paraméter értékeként használható.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Teljes megosztás helyreállítása az Azure CLI használatával

Ezzel a visszaállítási lehetőséggel visszaállíthatja a teljes fájlmegosztást az eredeti vagy egy másik helyen.

Adja meg a következő paramétereket a visszaállítási műveletek elvégzéséhez:

* **--Container-Name**: annak a Storage-fióknak a neve, amely az eredeti fájlmegosztás biztonsági másolatát tárolja. A tároló nevének vagy rövid nevének lekéréséhez használja az az [Backup Container List](/cli/azure/backup/container#az-backup-container-list) parancsot.
* **--Item-Name**: a visszaállítási művelethez használni kívánt biztonsági másolatban szereplő eredeti fájlmegosztás neve. A biztonsági másolatban szereplő elem nevének vagy rövid nevének lekéréséhez használja az az [Backup Item List](/cli/azure/backup/item#az-backup-item-list) parancsot.

### <a name="restore-a-full-share-to-the-original-location"></a>Teljes megosztás visszaállítása az eredeti helyre

Ha eredeti helyre állítja vissza, nem kell megadnia a célként kapcsolódó paramétereket. Csak a **feloldási ütközést** kell megadni.

Az alábbi példa az az [Backup Restore Restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) parancsmagot használja a Restore Mode set to *originallocation* értékre, hogy visszaállítsa a *azurefiles* -fájlmegosztást az eredeti helyen. A 932883129628959823-es helyreállítási pontot kell használnia, amelyet az [Azure-fájlmegosztás helyreállítási pontjainak beolvasása](#fetch-recovery-points-for-the-azure-file-share)során kapott:

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

A kimenetben szereplő **Name** attribútum a visszaállítási művelethez a Backup szolgáltatás által létrehozott feladatok neve. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Teljes megosztás visszaállítása egy másik helyre

Ezzel a lehetőséggel visszaállíthat egy fájlmegosztást egy másik helyre, és megtarthatja az eredeti fájlmegosztást. A másodlagos hely helyreállításához a következő paramétereket kell megadni:

* **--Target-Storage-Account**: az a Storage-fiók, amelyhez a biztonsági másolat tartalma visszaállítható. A célként megadott Storage-fióknak a tárolóval megegyező helyen kell lennie.
* **--Target-file-share**: a célként megadott Storage-fiókon belüli fájlmegosztás, amelyhez a biztonsági másolat tartalma vissza lesz állítva.
* **--Target-Folder**: az a fájlmegosztás alatt lévő mappa, amelybe az adat vissza lett állítva. Ha a biztonsági másolatban lévő tartalmat vissza szeretné állítani egy gyökérkönyvtárba, adja meg a célmappa értékeit üres karakterláncként.
* **--hárítsa-Conflict**: utasítás, ha ütközik a visszaállított adattal. A **felülírás** vagy **kihagyás** elfogadása.

Az alábbi példa az [az Backup Restore Restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) és a Restore Mode *alternatelocation* használatával állítja vissza a *azurefiles* fájlmegosztást a *afsaccount* Storage-fiókban a azurefiles1 *"* fájlmegosztás a *afaccount1* Storage-fiókban.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

A kimenetben szereplő **Name** attribútum a visszaállítási művelethez a Backup szolgáltatás által létrehozott feladatok neve. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

## <a name="item-level-recovery"></a>Elemszintű helyreállítás

Ezzel a visszaállítási lehetőséggel visszaállíthatja az egyes fájlokat vagy mappákat az eredeti vagy egy másik helyen.

Adja meg a következő paramétereket a visszaállítási műveletek elvégzéséhez:

* **--Container-Name**: annak a Storage-fióknak a neve, amely az eredeti fájlmegosztás biztonsági másolatát tárolja. A tároló nevének vagy rövid nevének lekéréséhez használja az az [Backup Container List](/cli/azure/backup/container#az-backup-container-list) parancsot.
* **--Item-Name**: a visszaállítási művelethez használni kívánt biztonsági másolatban szereplő eredeti fájlmegosztás neve. A biztonsági másolatban szereplő elem nevének vagy rövid nevének lekéréséhez használja az az [Backup Item List](/cli/azure/backup/item#az-backup-item-list) parancsot.

A helyreállítani kívánt elemekhez a következő paramétereket kell megadni:

* **SourceFilePath**: a fájl abszolút elérési útja, amelyet a fájlmegosztás keretén belül helyre kell állítani karakterláncként. Ez az elérési út az az [Storage File Download](/cli/azure/storage/file#az-storage-file-download) vagy az [Storage file](/cli/azure/storage/file#az-storage-file-show) CLI parancsokban használt elérési út.
* **SourceFileType**: válassza ki, hogy ki van-e választva egy könyvtár vagy fájl. **Címtár** vagy **fájl** elfogadása.
* **ResolveConflict**: utasítás, ha a visszaállított adattal ütközik. A **felülírás** vagy **kihagyás** elfogadása.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Egyéni fájlok vagy mappák visszaállítása az eredeti helyre

Az az [Backup Restore Restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) parancsmagot használja a Restore Mode set to *originallocation* értékre, hogy meghatározott fájlokat vagy mappákat állítson vissza az eredeti helyükre.

A következő példa visszaállítja a *RestoreTest.txt* fájlt az eredeti helyére: a *azurefiles* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

A kimenetben szereplő **Name** attribútum a visszaállítási művelethez a Backup szolgáltatás által létrehozott feladatok neve. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Egyes fájlok vagy mappák visszaállítása másik helyre

Ha meghatározott fájlokat vagy mappákat kíván visszaállítani egy másik helyre, használja az az Backup Restore [Restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) parancsmagot a Restore Mode (visszaállítási mód) beállítással a *alternatelocation* értékre, és adja meg a következő célként kapcsolódó paramétereket:

* **--Target-Storage-Account**: az a Storage-fiók, amelyhez a biztonsági másolat tartalma visszaállítható. A célként megadott Storage-fióknak a tárolóval megegyező helyen kell lennie.
* **--Target-file-share**: a célként megadott Storage-fiókon belüli fájlmegosztás, amelyhez a biztonsági másolat tartalma vissza lesz állítva.
* **--Target-Folder**: az a fájlmegosztás alatt lévő mappa, amelybe az adat vissza lett állítva. Ha a biztonsági másolatban lévő tartalmat vissza szeretné állítani egy gyökérkönyvtárba, adja meg a célmappa értékét üres karakterláncként.

A következő példa visszaállítja a *azurefiles* -fájlmegosztás eredetileg létező *RestoreTest.txt* fájlját egy másik helyre: a *azurefiles1* -fájlmegosztás *restoredata* mappáját, amely a *afaccount1* Storage-fiókban található.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

A kimenetben szereplő **Name** attribútum a visszaállítási művelethez a Backup szolgáltatás által létrehozott feladatok neve. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Több fájl vagy mappa visszaállítása eredetire vagy másik helyre

Több elem visszaállításának végrehajtásához adja át a **forrás-fájl-elérésiút** paraméter értékét az összes visszaállítani kívánt fájl vagy mappa **szóközzel elválasztott** elérési útjának.

Az alábbi példa visszaállítja a *Restore.txt* és az *AFS tesztelési Report.docx* fájljait az eredeti helyükre.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

A kimenet az alábbihoz hasonló lesz:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

A kimenetben szereplő **Name** attribútum a visszaállítási művelethez a Backup szolgáltatás által létrehozott feladatok neve. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

Ha több elemet szeretne visszaállítani egy másik helyre, használja a fenti parancsot a célként kapcsolódó paraméterek megadásával az [egyes fájlok vagy mappák visszaállítása másik helyre](#restore-individual-files-or-folders-to-an-alternate-location) című szakaszban leírtak szerint.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [kezelheti az Azure-fájlmegosztás biztonsági másolatait az Azure CLI-vel](manage-afs-backup-cli.md).
