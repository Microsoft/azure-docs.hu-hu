---
title: A Blobok Soft delete engedélyezése és kezelése
titleSuffix: Azure Storage
description: A Blobok törlésének engedélyezésével könnyebben lehet helyreállítani az adatokat, ha azok hibásan lettek módosítva vagy törölve lettek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: c89e42736f5b8de65ed93ccb57f8d034d4240bc8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729082"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>A Blobok Soft delete engedélyezése és kezelése

A blob Soft delete védelmet biztosít az adatok véletlen vagy helytelen módosításának vagy törlésének. Ha a blob-törlés engedélyezve van egy Storage-fiókhoz, a Blobok, a blob-verziók és a pillanatképek a Storage-fiókban, a megadott megőrzési időtartamon belül helyreállíthatók.

Ha egy alkalmazás vagy egy másik Storage-fiók felhasználója véletlenül nem módosíthatja vagy törölheti az adatait, a Microsoft javasolja a blobos törlés bekapcsolását. Ez a cikk bemutatja, hogyan engedélyezhető a Blobok lágy törlése. A Blobok törlésével kapcsolatos további részletekért lásd a Blobok helyreállítható [törlését](soft-delete-blob-overview.md)ismertető témakört.

A tárolók helyreállítható törlésének engedélyezéséről a [tárolók helyreállítható törlésének engedélyezése és kezelése](soft-delete-container-enable.md)című témakörben olvashat.

## <a name="enable-blob-soft-delete"></a>BLOB törlésének engedélyezése

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Blobok törlésének engedélyezése a Storage-fiókban Azure Portal használatával:

1. Az [Azure Portalon](https://portal.azure.com/) lépjen a tárfiókra.
1. Keresse meg az **Adatvédelem** lehetőséget a **blob Service** alatt.
1. Állítsa *engedélyezve* értékre a **blob törlési** tulajdonságát.
1. Az **adatmegőrzési szabályzatok** területen határozza meg, hogy az Azure Storage mennyi ideig őrizze meg a Soft-Deleted blobokat.
1. Mentse a módosításokat.

![Képernyőkép az Azure Portalról a megválasztott adatvédelmi blob-szolgáltatással.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

A nem kötelezően törölt Blobok megtekintéséhez jelölje be a **törölt Blobok megjelenítése** jelölőnégyzetet.

![Képernyőfelvétel: az adatvédelem blob szolgáltatás lapja, a törölt Blobok megjelenítése lehetőség kiemelve.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Egy adott blobhoz tartozó, törölt Pillanatképek megtekintéséhez válassza ki a blobot, majd kattintson a **Pillanatképek megtekintése** elemre.

![Képernyőfelvétel: az adatvédelem blob szolgáltatás lapja, a pillanatképek megtekintése lehetőség kiemelve.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Győződjön meg arról, hogy a **törölt Pillanatképek megjelenítése** jelölőnégyzet be van jelölve.

![Képernyőkép a pillanatképek megtekintése oldalon a törölt Blobok megjelenítése lehetőség kiemelve.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Ha a törölt blobra vagy pillanatképre kattint, figyelje az új blob tulajdonságait. Ezek azt jelzik, hogy mikor lett törölve az objektum, és hány nap van hátra, amíg a blob vagy a blob pillanatképe véglegesen lejár. Ha a helyreállított törölt objektum nem pillanatkép, akkor lehetősége van törölni a törlést is.

![Képernyőkép a nem kötelezően törölt objektumok részleteiről.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

Ne feledje, hogy a Blobok törlésének visszavonása törli az összes társított pillanatképet is. Ha törölni szeretné a nem törölt pillanatképeket egy aktív blobhoz, kattintson a blobra, és válassza az **összes pillanatkép törlésének** visszavonása lehetőséget.

![Képernyőkép a helyreállítható törölt Blobok részleteiről.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Miután visszavonta egy blob pillanatképének törlését, az **előléptetés** gombra kattintva másolhatja a pillanatképet a gyökérkönyvtárba, így visszaállíthatja a blobot a pillanatképre.

![Képernyőkép a pillanatképek megtekintése oldalon, az előléptetés lehetőség kiemelve.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait. A következő példa lehetővé teszi a Soft delete használatát egy előfizetésben lévő fiókok egy részhalmaza számára:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

A következő parancs használatával ellenőrizheti, hogy a Soft delete be van-e kapcsolva:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

A véletlenül törölt Blobok helyreállításához hívja meg a **blob törlését** az adott blobokon. Ne feledje, hogy az aktív és a nem törölt Blobok **törlésének** visszavonása művelettel az összes társított helyreállított pillanatkép aktív állapotú lesz. A következő példa meghívja a **blob törlését** a tároló összes lágy törölt és aktív blobján:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
A következő parancs használatával megkeresheti az aktuális helyreállítható törlési szabályzatot:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[Parancssori felület](#tab/azure-CLI)

A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

A Soft delete bekapcsolásának ellenőrzéséhez használja a következő parancsot: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

A véletlenül törölt Blobok helyreállításához hívja meg a törlést a blobokon. Ne feledje, hogy a **Törlés** visszahívása az aktív és a nem törölt Blobok esetében is megtörténik, és az összes társított helyreállított pillanatkép aktív állapotba kerül. A következő példa meghívja a törlést a tároló összes lágy törölt és aktív blobján:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Egy adott blob-verzióra történő helyreállításhoz először hívja meg a törlést egy blobon, majd másolja a kívánt pillanatképet a blobon. A következő példa egy blokk blobot állít be a legutóbb generált pillanatképre:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

A Soft delete engedélyezéséhez frissítse a blob-ügyfél szolgáltatásának tulajdonságait:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

A véletlenül törölt Blobok helyreállításához hívja meg a **blob törlését** az adott blobokon. Ne feledje, hogy az aktív és a nem törölt Blobok **törlésének** visszavonása művelettel az összes társított helyreállított pillanatkép aktív állapotú lesz. A következő példa meghívja a **blob törlését** a tároló összes lágyan törölt és aktív blobján:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Ha egy adott blob-verzióra kíván helyreállítani, először hívja meg a **blob delete** műveletét, majd másolja a kívánt pillanatképet a blobon. A következő példa egy blokk blobot állít be a legutóbb generált pillanatképre:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>Következő lépések

- [A blob Storage-hoz készült Soft delete](./soft-delete-blob-overview.md)
- [BLOB verziószámozása](versioning-overview.md)
