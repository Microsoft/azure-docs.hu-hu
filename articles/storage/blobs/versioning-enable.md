---
title: BLOB-verziószámozás engedélyezése és kezelése
titleSuffix: Azure Storage
description: Megtudhatja, hogyan engedélyezheti a Blobok verziószámozását a Azure Portal vagy egy Azure Resource Manager sablon használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5b6bd16eacf4b1bbb7b93f5500813e7fa9dc7eef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100095841"
---
# <a name="enable-and-manage-blob-versioning"></a>BLOB-verziószámozás engedélyezése és kezelése

A blob Storage verziószámozásának engedélyezésével automatikusan megtarthatja a blob korábbi verzióit a módosítás vagy a törlés után. Ha a blob verziószámozása engedélyezve van, akkor visszaállíthatja a blob egy korábbi verzióját az adatok helyreállításához, ha az hibásan van módosítva vagy törölve.

Ez a cikk bemutatja, hogyan engedélyezheti vagy tilthatja le a Blobok verziószámozását a Storage-fiókhoz a Azure Portal vagy egy Azure Resource Manager sablon használatával. A blob verziószámozásával kapcsolatos további tudnivalókért tekintse meg a [blob verziószámozását](versioning-overview.md)ismertető témakört.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Blob verziószámozásának engedélyezése

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A blob verziószámozásának engedélyezése egy Storage-fiókhoz a Azure Portalban:

1. Navigáljon a Storage-fiókjához a portálon.
1. Az **blob Service** területen válassza **az adatvédelem** elemet.
1. A **verziószámozás** szakaszban válassza az **engedélyezve** lehetőséget.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="A blob verziószámozásának engedélyezését bemutató képernyőkép Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha engedélyezni szeretné a Blobok verziószámozását a PowerShell-lel rendelkező Storage-fiókhoz, először telepítse az az [. Storage](https://www.powershellgallery.com/packages/Az.Storage) modul Version 2.3.0 vagy újabb verzióját. Ezután hívja meg az [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) parancsot a verziószámozás engedélyezéséhez, ahogy az az alábbi példában is látható. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

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

Ha az Azure CLI-vel szeretné engedélyezni a Blobok verziószámozását, először telepítse az Azure CLI-es vagy újabb verzióját. Ezután hívja meg az az [Storage Account blob-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) parancsot a verziószámozás engedélyezéséhez, az alábbi példában látható módon. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Sablon](#tab/template)

A Blobok verziószámozásának engedélyezéséhez sablon használatával hozzon létre egy sablont a **IsVersioningEnabled** tulajdonsággal az **igaz** értékre. A következő lépések azt ismertetik, hogyan lehet sablont létrehozni a Azure Portalban.

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
1. A **Keresés a piactéren** mezőbe írja be a **sablon központi telepítése** kifejezést, majd nyomja le az **ENTER** billentyűt.
1. Válassza a **template Deployment** lehetőséget, válassza a **Létrehozás** lehetőséget, majd **a szerkesztőben válassza a saját sablon** létrehozása lehetőséget.
1. A sablon szerkesztőjében illessze be a következő JSON-t. Cserélje le a `<accountName>` helyőrzőt a Storage-fiók nevére.
1. Mentse a sablont.
1. Adja meg a fiókhoz tartozó erőforráscsoportot, majd válassza a **vásárlás** gombot a sablon telepítéséhez és a blob verziószámozásának engedélyezéséhez.

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

Az erőforrások Azure Portal-sablonokkal való üzembe helyezésével kapcsolatos további információkért lásd: [erőforrások központi telepítése Azure Portalokkal](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>BLOB módosítása új verzió elindításához

Az alábbi mintakód bemutatja, hogyan indíthat el egy új verziót az Azure Storage ügyféloldali kódtára .NET, Version [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) vagy újabb verzióra. A példa futtatása előtt győződjön meg arról, hogy engedélyezte a verziószámozást a Storage-fiókhoz.

A példa létrehoz egy blokk blobot, majd frissíti a blob metaadatait. A blob metaadatainak frissítése elindítja egy új verzió létrehozását. A példa lekéri a kezdeti verziót és a jelenlegi verziót, és azt mutatja, hogy csak az aktuális verzió tartalmazza a metaadatokat.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>BLOB-verziók listázása

A .NET V12-es ügyféloldali függvénytárral rendelkező blob-verziók vagy-Pillanatképek listázásához a [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) paramétert a **Version (verzió** ) mezővel kell megadni.

A következő mintakód bemutatja, hogyan listázhatja a Blobok verzióját a .NET-hez készült Azure Storage ügyféloldali kódtára, a [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) vagy újabb verzióra. A példa futtatása előtt győződjön meg arról, hogy engedélyezte a verziószámozást a Storage-fiókhoz.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Következő lépések

- [BLOB verziószámozása](versioning-overview.md)
- [Az Azure Storage-blobok helyreállítható törlése](./soft-delete-blob-overview.md)