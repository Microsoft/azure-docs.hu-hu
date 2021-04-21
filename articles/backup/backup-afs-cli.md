---
title: Azure-fájlmegosztások biztonsági mentése az Azure CLI használatával
description: Megtudhatja, hogyan használhatja az Azure CLI-t Az Azure-fájlmegosztások biztonsági mentése a Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: a5f7472c511a5a50415a6ceb47497dd6f4f1e60b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773620"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Azure-fájlmegosztások biztonsági mentése az Azure CLI használatával

Az Azure parancssori felület (CLI) parancssori felületet biztosít az Azure-erőforrások kezeléséhez. Nagyszerű eszköz az Azure-erőforrások használatára vonatkozó egyéni automatizálások létrehozásához. Ez a cikk részletesen bemutatja, hogyan lehet biztonsági mentése az Azure-fájlmegosztásokról az Azure CLI-val. Az [Azure PowerShell](./backup-azure-afs-automation.md) vagy az [Azure Portal](backup-afs.md) használatával is elvégezheti ezeket a lépéseket.

Az oktatóanyag végére megtanulja, hogyan végezheti el az alábbi műveleteket az Azure CLI használatával:

* Recovery Services-tároló létrehozása
* Azure-fájlmegosztások biztonsági mentésének engedélyezése
* Fájlmegosztások igény szerinti biztonsági mentésének aktiválása

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.18-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy olyan entitás, amely összevont nézetet és felügyeleti képességet biztosít az összes biztonsági mentési elemre. Amikor egy védett erőforrás biztonsági mentésének feladata fut, a rendszer egy helyreállítási pontot hoz létre a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

Recovery Services-tároló létrehozásához kövesse az alábbi lépéseket:

1. A tároló egy erőforráscsoportban van elhelyezve. Ha még nem létezik erőforráscsoport, hozzon létre egy újat [az az group create segítségével.](/cli/azure/group#az_group_create) Ebben az oktatóanyagban létrehozunk egy új *azurefiles erőforráscsoportot* az USA keleti régiójában.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. A tároló [létrehozásához használja](/cli/azure/backup/vault#az_backup_vault_create) az az backup vault create parancsmagot. Adja meg az erőforráscsoporthoz használt tároló helyét.

    Az alábbi példa létrehoz egy *azurefilesvault* nevű Recovery Services-tárolót az USA keleti régiójában.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Azure-fájlmegosztások biztonsági mentésének engedélyezése

Ez a szakasz feltételezi, hogy már rendelkezik olyan Azure-fájlmegosztásokkal, amelyekhez konfigurálni szeretné a biztonsági mentést. Ha még nem használja, hozzon létre egy Azure-fájlmegosztást [az az storage share create paranccsal.](/cli/azure/storage/share#az_storage_share_create)

A fájlmegosztások biztonsági mentésének engedélyezéséhez létre kell hoznia egy védelmi szabályzatot, amely meghatározza, hogy mikor futnak a biztonsági mentési feladat, és mennyi ideig tárolja a rendszer a helyreállítási pontokat. Biztonsági mentési szabályzatot az [az backup policy create parancsmag](/cli/azure/backup/policy#az_backup_policy_create) használatával hozhat létre.

Az alábbi példa az [az backup protection enable-for-azurefileshare](/cli/azure/backup/protection#az_backup_protection_enable_for_azurefileshare) parancsmagot használja az *afsaccount* tárfiókban található *azurefiles* fájlmegosztás biztonsági mentésének engedélyezéséhez az *1. ütemezési* biztonsági mentési szabályzat használatával:

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

A **kimenet Name** (Név) attribútuma megegyezik a biztonsági mentési szolgáltatás által az engedélyezéshez szükséges biztonsági mentési művelethez létrehozott feladat **nevével.** A feladat állapotának nyomon követéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Igény szerinti biztonsági mentés aktiválása fájlmegosztáshoz

Ha nem arra vár, hogy a biztonsági mentési házirend az ütemezett időpontban futtassa a feladatot, hanem igény szerinti biztonsági mentést szeretne készíteni a fájlmegosztáshoz, használja az [az backup protection backup-now](/cli/azure/backup/protection#az_backup_protection_backup_now) parancsmagot.

Az igény szerinti biztonsági mentés aktiválása érdekében a következő paramétereket kell meghatároznia:

* **A --container-name** a fájlmegosztást üzemeltető tárfiók neve. A tároló **nevének vagy** **rövid** nevének lekéréséhez használja az az backup [container list](/cli/azure/backup/container#az_backup_container_list) parancsot.
* **Az --item-name** annak a fájlmegosztásnak a neve, amelyhez igény szerinti biztonsági mentést kíván aktiválni. A biztonsági **mentési** elem **nevének** vagy rövid nevének lekéréséhez használja [az az backup item list](/cli/azure/backup/item#az_backup_item_list) parancsot.
* **--retain-until** – megadja a helyreállítási pont megőrzésének dátumát. Az értéket UTC időformátumban (dd-mm-yyyy) kell megadni.

Az alábbi példa elindít egy igény szerinti biztonsági mentést az *afsaccount* tárfiókban található *azurefiles* fájlmegosztásról *2020. 01. 01. 01-ig megőrzéssel.*

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

A **kimenet Name** (Név) attribútuma megegyezik a biztonsági mentési szolgáltatás által az igény szerinti biztonsági mentési művelethez létrehozott feladat nevével. A feladat állapotának nyomon követéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [állíthatók vissza Azure-fájlmegosztások a CLI használatával](restore-afs-cli.md)
* Megtudhatja, hogyan [kezelheti az Azure-fájlmegosztások biztonsági mentését a CLI-val](manage-afs-backup-cli.md)
