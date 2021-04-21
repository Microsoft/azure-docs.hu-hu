---
title: Azure-fájlmegosztások biztonsági mentésének kezelése az Azure CLI-val
description: Megtudhatja, hogyan használhatja az Azure CLI-t a biztonsági Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: e389f5cde12734ef4bf0be4ecfba69ba33f5e030
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773602"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Azure-fájlmegosztások biztonsági mentésének kezelése az Azure CLI-val

Az Azure CLI parancssori felületeket biztosít az Azure-erőforrások kezeléséhez. Nagyszerű eszköz az Azure-erőforrások használatára vonatkozó egyéni automatizálások létrehozásához. Ez a cikk azt ismerteti, hogyan végezheti el az azure-fájlmegosztások kezeléséhez és monitorozásához szükséges feladatokat, amelyekről biztonsági [Azure Backup.](./backup-overview.md) Ezeket a lépéseket a következővel is [Azure Portal.](https://portal.azure.com/)

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már rendelkezik egy azure-fájlmegosztásról, amelyről biztonsági [Azure Backup.](./backup-overview.md) Ha még nem rendelkezik ilyensel, tekintse meg az Azure-fájlmegosztások biztonsági mentése a [CLI-val](backup-afs-cli.md) a fájlmegosztások biztonsági mentésének konfigurálásával. Ehhez a cikkhez a következő erőforrásokat használja:
   -  **Erőforráscsoport:** *azurefiles*
   -  **RecoveryServicesVault:** *azurefilesvault*
   -  **Tárfiók:** *afsaccount*
   -  **Fájlmegosztás:** *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Ehhez az oktatóanyaghoz az Azure CLI 2.0.18-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="monitor-jobs"></a>Feladatok figyelése

Amikor biztonsági mentési vagy visszaállítási műveleteket indít el, a biztonsági mentési szolgáltatás létrehoz egy nyomkövetési feladatot. A befejezett vagy jelenleg futó feladatok monitorzához használja [az az backup job list](/cli/azure/backup/job#az_backup_job_list) parancsmagot. A CLI-val felfüggeszthet egy jelenleg futó feladatot, vagy megvárhatja, amíg [egy feladat befejeződik.](/cli/azure/backup/job#az_backup_job_wait) [](/cli/azure/backup/job#az_backup_job_stop)

Az alábbi példa az *azurefilesvault* Recovery Services-tároló biztonsági mentési feladatának állapotát jeleníti meg:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Szabályzat módosítása

A biztonsági mentési szabályzat módosításával módosíthatja a biztonsági mentés gyakoriságát vagy a megőrzési tartományt [az az backup item set-policy használatával.](/cli/azure/backup/item#az_backup_item_set_policy)

A szabályzatot a következő paraméterek meghatározásával módosíthatja:

* **--container-name:** A fájlmegosztást tároló tárfiók neve. A tároló **nevének vagy** **rövid** nevének lekéréséhez használja az az backup [container list](/cli/azure/backup/container#az_backup_container_list) parancsot.
* **--name:** Annak a fájlmegosztásnak a neve, amelyhez módosítani szeretné a házirendet. A biztonsági **mentési** elem **nevének** vagy rövid nevének lekéréséhez használja [az az backup item list](/cli/azure/backup/item#az_backup_item_list) parancsot.
* **--policy-name:** A fájlmegosztáshoz beállítani kívánt biztonsági mentési házirend neve. Az az [backup policy list](/cli/azure/backup/policy#az_backup_policy_list) használatával megtekintheti a tároló összes szabályzatát.

Az alábbi példa az *afsaccount* tárfiókban található *azurefiles* fájlmegosztás *schedule2* biztonsági mentési szabályzatát állítja be.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Az előző parancsot a tároló és az elem rövid nevének használatával is futtathatja a következő két paraméter megszabadításával:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

A **kimenet Name** attribútuma megegyezik a biztonsági mentési szolgáltatás által a módosítási szabályzat műveletéhez létrehozott feladat nevével. A feladat állapotának nyomon követéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

## <a name="stop-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének leállítása

Az Azure-fájlmegosztások védelmét kétféle módon szüntetheti meg:

* Állítsa le az összes jövőbeli biztonsági mentési feladat, *és törölje az* összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladat, de *hagyja meg a* helyreállítási pontokat.

Előfordulhat, hogy a helyreállítási pontok a tárolóban való meghagyása költségeket is jár, mert a rendszer megőrzi az Azure Backup által létrehozott mögöttes pillanatképeket. A helyreállítási pontok elhagyása a fájlmegosztás későbbi visszaállításának lehetősége, ha szeretné. A helyreállítási pontok elhagyási költségeivel kapcsolatos információkért tekintse meg a [díjszabás részleteit.](https://azure.microsoft.com/pricing/details/storage/files) Ha úgy dönt, hogy törli az összes helyreállítási pontot, nem tudja visszaállítani a fájlmegosztást.

A fájlmegosztás védelmének leállításhoz adja meg a következő paramétereket:

* **--container-name:** A fájlmegosztást tároló tárfiók neve. A tároló **nevének vagy** **rövid** nevének lekéréséhez használja az az backup [container list](/cli/azure/backup/container#az_backup_container_list) parancsot.
* **--item-name:** Annak a fájlmegosztásnak a neve, amelynek védelmét le szeretné állítani. A biztonsági **mentési** elem **nevének** vagy rövid nevének lekéréséhez használja [az az backup item list](/cli/azure/backup/item#az_backup_item_list) parancsot.

### <a name="stop-protection-and-retain-recovery-points"></a>Védelem leállítása és a helyreállítási pontok megőrzése

Ha le szeretné állítani a védelmet az adatok megőrzése közben, használja [az az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) parancsmagot.

Az alábbi példa leállítja az *azurefiles* fájlmegosztás védelmét, de megtartja az összes helyreállítási pontot.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Az előző parancsot a tároló és az elem rövid nevének használatával is futtathatja a következő két további paraméter megtételével:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

A **kimenet Name** attribútuma megegyezik a biztonsági mentési szolgáltatás által a leállítási védelmi művelethez létrehozott feladat nevével. A feladat állapotának nyomon követéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

### <a name="stop-protection-without-retaining-recovery-points"></a>Védelem leállítása a helyreállítási pontok megőrzése nélkül

Ha le szeretné állítani a védelmet a helyreállítási pontok megőrzése nélkül, használja [az az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) parancsmagot, és állítsa a **delete-backup-data** beállítást **true (igaz) értékre.**

Az alábbi példa leállítja az *azurefiles* fájlmegosztás védelmét a helyreállítási pontok megőrzése nélkül.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Az előző parancsot a tároló és az elem rövid nevének használatával is futtathatja a következő két további paraméter megtételével:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének folytatása

Ha leállította egy Azure-fájlmegosztás védelmét, de megőrizte a helyreállítási pontokat, később folytathatja a védelmet. Ha nem őrzi meg a helyreállítási pontokat, nem folytathatja a védelmet.

A fájlmegosztás védelmének folytatásához adja meg a következő paramétereket:

* **--container-name:** A fájlmegosztást tároló tárfiók neve. A tároló **nevének vagy** **rövid** nevének lekéréséhez használja az az backup [container list](/cli/azure/backup/container#az_backup_container_list) parancsot.
* **--item-name:** Annak a fájlmegosztásnak a neve, amelynek védelmét folytatni szeretné. A biztonsági **mentési** elem **nevének** vagy rövid nevének lekéréséhez használja [az az backup item list](/cli/azure/backup/item#az_backup_item_list) parancsot.
* **--policy-name:** Annak a biztonsági mentési házirendnek a neve, amelyhez folytatni szeretné a fájlmegosztás védelmét.

Az alábbi példa az [az backup protection resume](/cli/azure/backup/protection#az_backup_protection_resume) parancsmagot használja az *azurefiles* fájlmegosztás védelmének folytatásához a *schedule1* biztonsági mentési szabályzat használatával.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Az előző parancsot a tároló és az elem rövid nevének használatával is futtathatja a következő két további paraméter megtételével:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

A **kimenet Name** attribútuma megegyezik a biztonsági mentési szolgáltatás által a védelmi folytatási művelethez létrehozott feladat nevével. A feladat állapotának nyomon követéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

## <a name="unregister-a-storage-account"></a>Tárfiók regisztrációjának a regisztrációja

Ha egy adott tárfiókban található fájlmegosztásokat egy másik Recovery [](#stop-protection-on-a-file-share) Services-tárolóval szeretné védeni, először állítsa le a tárfiókban található összes fájlmegosztás védelmét. Ezután válassza ki a fiók regisztrációját a védelemhez jelenleg használt Recovery Services-tárolóból.

Meg kell adnia egy tárolónevet a tárfiók regisztrációjának a regisztrációjának a regisztrációja nélkül. A tároló **nevének vagy** **rövid** nevének lekéréséhez használja az az backup [container list](/cli/azure/backup/container#az_backup_container_list) parancsot.

Az alábbi példa az [az backup container unregister](/cli/azure/backup/container#az_backup_container_unregister) parancsmag használatával nem regisztrálja az *afsaccount* tárfiókot az *azurefilesvault* fájlból.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Az előző parancsmagot a tároló rövid nevének használatával is futtathatja a következő további paraméter megadva:

* **--backup-management-type:** *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Következő lépések

További információ: [Azure-fájlmegosztások biztonsági mentésének hibaelhárítása.](troubleshoot-azure-files.md)
