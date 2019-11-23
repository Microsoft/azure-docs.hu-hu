---
title: Enable and create large file shares - Azure Files
description: In this article, you learn how to enable and create large file shares.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 34b8af56a8f2f108b96ca07fa73f90bb9eb5bf13
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422724"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>How to enable and create large file shares

Originally, standard file shares could only scale up to 5 TiB, now, with large file shares, they can scale up to 100 TiB. Premium file shares scale up to 100 TiB by default. 

In order to scale up to 100 TiB using standard file shares, you must enable your storage account to use large file shares. You can either enable an existing account or create a new account to use large file shares.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- If you intend to use Azure CLI, make sure you [install the latest version](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- If you intend to use Azure PowerShell, make sure you [install the latest version](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Korlátozások

Large file shares enabled accounts support LRS or ZRS. For now, large file shares enabled accounts do not support GZRS, GRS, or RA-GRS. Enabling large file shares on an account is an irreversible process, once it is enabled your account cannot be converted to GZRS, GRS, or RA-GRS.

## <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása

### <a name="portal"></a>Portál

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájába írja be a **Storage-fiókok** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Tárfiókok** lehetőséget.
1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.
1. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Az alábbi képen látható módon nevezze el az új erőforráscsoportot.

    ![Erőforráscsoport portálon való létrehozását bemutató képernyőkép](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
1. Select a location for your storage account, make sure it is [one of the regions supported for LFS](storage-files-planning.md#regional-availability).
1. Set the replication to either **Locally-redundant storage** or **Zone-redundant storage**.
1. Ne módosítsa a következő mezők alapértelmezett értékeit:

   |Mező  |Value (Díj)  |
   |---------|---------|
   |Üzemi modell     |Erőforrás-kezelő         |
   |Teljesítmény     |Standard         |
   |Fióktípus     |StorageV2 (általános célú v2)         |
   |Elérési szint     |Gyakori         |

1. Select **Advanced** and select **Enabled** for **Large file shares**.
1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.

    ![large-file-shares-advanced-enable.png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Kattintson a **Létrehozás** gombra.


### <a name="cli"></a>CLI

First, make sure you [install the latest version](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), so that you can enable this feature.

To create a storage account with large file shares enabled, replace `<yourStorageAccountName>`, `<yourResourceGroup>`, and `<yourDesiredRegion>` with your values, then use the following command:

```azurecli-interactive
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
az storage account create –name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> –sku Standard_LRS --kind StorageV2 –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

First, make sure you [install the latest version](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0), so that you can enable this feature.

To create a storage account with large file shares enabled, replace `<yourStorageAccountName>`, `<yourResourceGroup>`, and `<yourDesiredRegion>` with your values, then use the following command:

```PowerShell
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-on-existing-account"></a>Enable on existing account

### <a name="portal"></a>Portál

You can also enable large file shares on existing accounts. If you do this, then the account will no longer be able to convert to GZRS, GRS, or RA-GRS. This choice is irreversible on this account.

1. Open the [Azure portal](https://portal.azure.com) and navigate to the storage account you want to enable large file shares on.
1. Open the storage account and select **Configuration**.
1. Select **Enabled** on **Large file shares**, then select save.
1. Select **Overview** and select **Refresh**.

![enable-large-file-shares-on-existing.png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

You've now enabled large file shares on your storage account.

If you receive the following error: "Large file shares are not available for the account yet." You can either wait some time, as your region is likely in the middle of completing its roll out, or, if you have urgent needs, reach out to support.

### <a name="cli"></a>CLI

You can enable large file shares on your existing accounts. If you do this, then the account will no longer be able to convert to GZRS, GRS, or RA-GRS. This choice is irreversible on this account.

Replace `<yourStorageAccountName>` and `<yourResourceGroup>` in the following command, then use it to enable large file shares on your existing account:

```azurecli-interactive
az storage account update –name <yourStorageAccountName> -g <yourResourceGroup> –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

You can enable large file shares on your existing accounts. If you do this, then the account will no longer be able to convert to GZRS, GRS, or RA-GRS. This choice is irreversible on this account.

Replace `<yourStorageAccountName>` and `<yourResourceGroup>` in the following command, then use it to enable large file shares on your existing account:

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Create a large file share

### <a name="portal"></a>Portál

Creating a large file share is almost identical to creating a standard file share. The main difference is that you can set a quota up to 100 TiB.

1. From your storage account, select **File shares**.
1. Select **+ File Share**.
1. Enter a name for your file share and (optionally) the quota size you'd like, up to 100 TiB, then select **Create**. 

![large-file-shares-create-share.png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Once you've enabled large file shares on your storage account, you can create file shares in that account with higher quotas. Replace `<yourStorageAccountName>`, `<yourStorageAccountKey>`, and `<yourFileShareName>` in the following command with your values, then you can use it to create a large file share:

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Once you've enabled large file shares on your storage account, you can create file shares in that account with higher quotas. Replace `<YourStorageAccountName>`, `<YourStorageAccountKey>`, and `<YourStorageAccountFileShareName>` in the following command with your values, then you can use it to create a large file share:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Expand existing file shares

### <a name="portal"></a>Portál

Once you've enabled large file shares on your storage account, you can expand existing shares to the higher quota.

1. From your storage account, select **File shares**.
1. Right-click your file share and select **Quota**.
1. Enter the new size that you desire, then select **OK**.

![update-large-file-share-quota.png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Once you've enabled large file shares on your storage account, you can expand existing file shares in that account to the higher quotas. Replace `<yourStorageAccountName>`, `<yourStorageAccountKey>`, and `<yourFileShareName>` in the following command with your values, then you can use it to set the quota to the maximum size:

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Once you've enabled large file shares on your storage account, you can expand existing file shares in that account to the higher quotas. Replace `<YourStorageAccountName>`, `<YourStorageAccountKey>`, and `<YourStorageAccountFileShareName>` in the following command with your values, then you can use it to set the quota to the maximum size:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Következő lépések

* [Fájlmegosztás csatlakoztatása – Windows](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása – Linux](../storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása – macOS](storage-how-to-use-files-mac.md)