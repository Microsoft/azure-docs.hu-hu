---
title: Blobok verziószámozásának engedélyezése és kezelése
titleSuffix: Azure Storage
description: Megtudhatja, hogyan engedélyezheti a blobok verziószámozását a Azure Portal vagy egy Azure Resource Manager használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f6a1d315342ea98ccaf1382630eccca876ada3f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870192"
---
# <a name="enable-and-manage-blob-versioning"></a>Blobok verziószámozásának engedélyezése és kezelése

A Blob Storage verziószámozásának engedélyezésével automatikusan megtarthatja a blobok korábbi verzióit annak módosításakor vagy törlésekor. Ha a blobok verziószámozása engedélyezve van, visszaállíthatja egy blob korábbi verzióját, hogy helyreállítsa az adatokat, ha azokat hibásan módosították vagy törölték.

Ez a cikk bemutatja, hogyan engedélyezheti vagy tilthatja le a tárfiók blobverzió-Azure Portal vagy egy Azure Resource Manager használatával. További információ a blobok verziószámozásról: [Blob verziószámozása.](versioning-overview.md)

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Blob verziószámozásának engedélyezése

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Blob verziószámozásának engedélyezése a tárfiókhoz a Azure Portal:

1. Keresse meg a tárfiókját a portálon.
1. A **Blob service** válassza az **Adatvédelem lehetőséget.**
1. A **Verziószámozás szakaszban** válassza az Engedélyezve **lehetőséget.**

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Képernyőkép a blobok verziószámozásának engedélyezéséről a Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha engedélyezni szeretné a blobok verziószámozását egy tárfiókhoz a PowerShell használatával, először telepítse az [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) modul 2.3.0-s vagy újabb verzióját. Ezután hívja meg az [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) parancsot a verziószámozás engedélyezéséhez, az alábbi példában látható módon. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha engedélyezni szeretné a blobok verziószámozását egy tárfiókhoz az Azure CLI használatával, először telepítse az Azure CLI 2.2.0-s vagy újabb verzióját. Ezután hívja meg [az az storage account blob-service-properties update parancsot](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) a verziószámozás engedélyezéséhez, az alábbi példában látható módon. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Sablon](#tab/template)

Ha engedélyezni szeretné a blobok sablonnal való verziószámozását, hozzon létre egy sablont true (igaz) értéken az **IsVersioningEnabled** **tulajdonsággal.** Az alábbi lépések azt ismertetik, hogyan hozhat létre sablont a Azure Portal.

1. A Azure Portal válassza az **Erőforrás létrehozása lehetőséget.**
1. A **Keresés a Marketplace-en mezőbe írja** be a sablon üzembe **helyezését,** majd nyomja le az ENTER **billentyűt.**
1. Válassza **Template deployment** lehetőséget, válassza a **Létrehozás,** majd a Saját sablon létrehozása lehetőséget a **szerkesztőben.**
1. A sablonszerkesztőben illessze be a következő JSON-t. Cserélje le `<accountName>` a helyőrzőt a tárfiókja nevére.
1. Mentse a sablont.
1. Adja meg a fiók erőforráscsoportját, majd kattintson a **Vásárlás** gombra a sablon üzembe helyezéséhez és a blob verziószámozásának engedélyezéséhez.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

További információ az erőforrások sablonokkal való üzembe helyezéséről a Azure Portal: Erőforrások üzembe helyezése [a Azure Portal.](../../azure-resource-manager/templates/deploy-portal.md)

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Blob módosítása új verzió aktiválása

Az alábbi példakód bemutatja, hogyan aktiválható egy új verzió létrehozása az Azure Storage .NET-hez való ügyféloldali [kódtárának 12.5.1-es](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) vagy újabb verziójával. A példa futtatása előtt győződjön meg arról, hogy engedélyezte a tárfiók verziószámozását.

A példa létrehoz egy blokkblobot, majd frissíti a blob metaadatait. A blob metaadatainak frissítése új verzió létrehozását váltja ki. A példa lekéri a kezdeti verziót és az aktuális verziót, és azt mutatja, hogy csak az aktuális verzió tartalmazza a metaadatokat.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Blobverziók listája

A blobverziók vagy pillanatképek .NET v12 ügyféloldali kódtára segítségével való listához adja meg a [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) paramétert **a Verzió mezővel.**

Az alábbi példakód bemutatja, hogyan listhatja a blobok verzióját a .NET-hez elérhető Azure Storage ügyféloldali kódtára [12.5.1-es](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) vagy újabb verziójával. A példa futtatása előtt győződjön meg arról, hogy engedélyezte a tárfiók verziószámozását.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Következő lépések

- [Blobok verziószámozása](versioning-overview.md)
- [Az Azure Storage-blobok helyreállítható törlése](./soft-delete-blob-overview.md)