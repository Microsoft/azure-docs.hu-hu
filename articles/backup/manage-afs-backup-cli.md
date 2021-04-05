---
title: Azure-fájlmegosztás biztonsági másolatainak kezelése az Azure CLI-vel
description: Megtudhatja, hogyan kezelheti és figyelheti Azure Backup által készített Azure-fájlmegosztás felügyeletét és figyelését az Azure CLI használatával.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5a8a785016845b836a102663a959e4b2f28696b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566452"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Azure-fájlmegosztás biztonsági másolatainak kezelése az Azure CLI-vel

Az Azure CLI parancssori felületet biztosít az Azure-erőforrások kezeléséhez. Ez nagyszerű eszköz az Azure-erőforrások használatára szolgáló egyéni automatizálás kialakításához. Ez a cikk bemutatja, hogyan végezheti el a [Azure Backup](./backup-overview.md)által készített biztonsági mentést végző Azure-fájlmegosztás felügyeletére és figyelésére vonatkozó feladatokat. Ezeket a lépéseket a [Azure Portal](https://portal.azure.com/)is végrehajthatja.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már rendelkezik [Azure Backup](./backup-overview.md)által készített Azure-fájlmegosztás biztonsági mentésével. Ha még nem rendelkezik ilyennel, tekintse meg [Az Azure-fájlmegosztás biztonsági mentése a parancssori](backup-afs-cli.md) felülettel című témakört a fájlmegosztás biztonsági mentésének konfigurálásához. Ebben a cikkben a következő erőforrásokat használja:
   -  **Erőforráscsoport**: *azurefiles*
   -  **RecoveryServicesVault**: *azurefilesvault*
   -  **Storage-fiók**: *afsaccount*
   -  **Fájlmegosztás**: *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Az oktatóanyaghoz az Azure CLI 2.0.18 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="monitor-jobs"></a>Feladatok figyelése

A biztonsági mentési vagy visszaállítási műveletek indításakor a Backup szolgáltatás létrehoz egy feladatot a követéshez. A befejezett vagy jelenleg futó feladatok figyeléséhez használja az az [Backup Job List](/cli/azure/backup/job#az-backup-job-list) parancsmagot. A CLI-vel [egy jelenleg futó feladatot is felfüggesztheti](/cli/azure/backup/job#az-backup-job-stop) , vagy [megvárhatja, amíg a feladatok befejeződik](/cli/azure/backup/job#az-backup-job-wait).

A következő példa a *azurefilesvault* Recovery Services-tároló biztonsági mentési feladatainak állapotát jeleníti meg:

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

## <a name="modify-policy"></a>Házirend módosítása

A biztonsági mentési szabályzat módosításával módosíthatja a biztonsági mentési gyakoriságot vagy a megőrzési tartományt az [az Backup Item set-Policy](/cli/azure/backup/item#az-backup-item-set-policy)paranccsal.

A szabályzat módosításához adja meg a következő paramétereket:

* **--Container-Name**: a fájlmegosztást tároló Storage-fiók neve. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](/cli/azure/backup/container#az-backup-container-list) parancsot.
* **--Name**: annak a fájlmegosztásnak a neve, amelyre módosítani szeretné a szabályzatot. A biztonsági másolatban szereplő elem **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Item List](/cli/azure/backup/item#az-backup-item-list) parancsot.
* **--Policy-Name**: a fájlmegosztás számára beállítani kívánt biztonsági mentési szabályzat neve. A tár összes szabályzatának megtekintéséhez használja az [az biztonsági mentési](/cli/azure/backup/policy#az-backup-policy-list) szabályzatok listáját.

A következő példa a afsaccount *schedule2* található *azurefiles* -fájlmegosztás biztonsági mentési szabályzatát állítja be  .

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Az előző parancsot az alábbi két további paraméter megadásával is futtathatja a tároló és az elemek felhasználóbarát neveivel:

* **--Backup-Management-Type**: *azurestorage*
* **--munkaterhelés-Type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

A kimenet **Name** attribútuma a Change Policy művelethez a Backup szolgáltatás által létrehozott feladatok neve. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

## <a name="stop-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének leállítása

Az Azure-fájlmegosztások védelmét kétféle módon szüntetheti meg:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és *törölje* az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, de *hagyja* meg a helyreállítási pontokat.

Előfordulhat, hogy a tárolóban lévő helyreállítási pontok elhagyása után a rendszer megőrzi a Azure Backup által létrehozott mögöttes pillanatképeket. Ha szeretné, a helyreállítási pontok elhagyása lehetőséggel később is visszaállíthatja a fájlmegosztást. A helyreállítási pontok elhagyása költségével kapcsolatos további információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/storage/files). Ha úgy dönt, hogy törli az összes helyreállítási pontot, a fájlmegosztás nem állítható vissza.

A fájlmegosztás védelmének leállításához adja meg a következő paramétereket:

* **--Container-Name**: a fájlmegosztást tároló Storage-fiók neve. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](/cli/azure/backup/container#az-backup-container-list) parancsot.
* **--Item-Name**: azon fájlmegosztás neve, amelynek a védelmét le szeretné állítani. A biztonsági másolatban szereplő elem **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Item List](/cli/azure/backup/item#az-backup-item-list) parancsot.

### <a name="stop-protection-and-retain-recovery-points"></a>Védelem leállítása és helyreállítási pontok megőrzése

Ha le szeretné állítani a védelmet az adatmegőrzés során, használja az az [Backup Protection disable](/cli/azure/backup/protection#az-backup-protection-disable) parancsmagot.

A következő példa leállítja a *azurefiles* -fájlmegosztás védelmét, de megőrzi az összes helyreállítási pontot.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Az előző parancsot az alábbi két további paraméter megadásával is futtathatja a tároló és az elemek rövid nevével:

* **--Backup-Management-Type**: *azurestorage*
* **--munkaterhelés-Type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

A kimenetben található **Name** attribútum a stop Protection művelethez tartozó Backup szolgáltatás által létrehozott feladatokhoz tartozó névvel egyezik. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

### <a name="stop-protection-without-retaining-recovery-points"></a>A védelem leállítása a helyreállítási pontok megőrzése nélkül

A védelem a helyreállítási pontok megőrzése nélkül történő leállításához használja az az [Backup Protection disable](/cli/azure/backup/protection#az-backup-protection-disable) parancsmagot a **delete-Backup-retain (Törlés – biztonsági mentés** ) beállítással az **igaz** értékre.

A következő példa leállítja a *azurefiles* -fájlmegosztás védelmét a helyreállítási pontok megőrzése nélkül.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Az előző parancsot az alábbi két további paraméter megadásával is futtathatja a tároló és az elemek rövid nevével:

* **--Backup-Management-Type**: *azurestorage*
* **--munkaterhelés-Type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének folytatása

Ha leállította egy Azure-fájlmegosztás védelmét, de megtartotta a helyreállítási pontokat, később is folytathatja a védelmet. Ha nem tartja meg a helyreállítási pontokat, nem folytathatja a védelmet.

A fájlmegosztás védelmének folytatásához adja meg a következő paramétereket:

* **--Container-Name**: a fájlmegosztást tároló Storage-fiók neve. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](/cli/azure/backup/container#az-backup-container-list) parancsot.
* **--Item-Name**: azon fájlmegosztás neve, amelynek a védelmét folytatni kívánja. A biztonsági másolatban szereplő elem **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Item List](/cli/azure/backup/item#az-backup-item-list) parancsot.
* **--Policy-Name**: annak a biztonsági mentési házirendnek a neve, amelyre a fájlmegosztás védelmét folytatni kívánja.

Az alábbi példa az az [Backup Protection Resume](/cli/azure/backup/protection#az-backup-protection-resume) parancsmagot használja a *azurefiles* -fájlmegosztás védelmének folytatásához a *schedule1* biztonsági mentési szabályzatának használatával.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Az előző parancsot az alábbi két további paraméter megadásával is futtathatja a tároló és az elemek rövid nevével:

* **--Backup-Management-Type**: *azurestorage*
* **--munkaterhelés-Type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

A kimenetben a **Name** attribútum a Backup szolgáltatás által a védelmi művelet folytatásához létrehozott feladatok neve. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

## <a name="unregister-a-storage-account"></a>Storage-fiók regisztrációjának törlése

Ha egy adott Storage-fiókban lévő fájlmegosztást egy másik Recovery Services-tárolóval szeretne védelemmel ellátni, először állítsa le a Storage-fiókban lévő [összes fájlmegosztás védelmét](#stop-protection-on-a-file-share) . Ezután törölje a fiók regisztrációját a védelemhez jelenleg használt Recovery Services-tárból.

Meg kell adnia egy tároló nevét a Storage-fiók regisztrációjának megszüntetéséhez. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](/cli/azure/backup/container#az-backup-container-list) parancsot.

Az alábbi példa megszünteti a *afsaccount* Storage-fiók *azurefilesvault* való regisztrációját az az [Backup Container unregister](/cli/azure/backup/container#az-backup-container-unregister) parancsmag használatával.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Az előző parancsmagot a tároló rövid nevével is futtathatja a következő további paraméter megadásával:

* **--Backup-Management-Type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Következő lépések

További információ: az [Azure-fájlmegosztás biztonsági mentésének hibája](troubleshoot-azure-files.md).
