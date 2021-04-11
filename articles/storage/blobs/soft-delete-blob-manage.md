---
title: A nem törölt Blobok kezelése és visszaállítása
titleSuffix: Azure Storage
description: A Soft-Deleted blobok és Pillanatképek kezelése és visszaállítása a Azure Portal vagy az Azure Storage ügyféloldali kódtárakkal.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d5ef85d947ae999fd94ba5a6e9cdb00baec9786
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556153"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>A nem törölt Blobok kezelése és visszaállítása

A blob Soft delete szolgáltatás védi az egyes blobokat, azok verzióit, pillanatképeit és metaadatait a véletlen törlésből vagy felülírásokból azáltal, hogy a törölt adatokat egy adott időszakra vonatkozóan tartja karban. A megőrzési időszak alatt a blobot visszaállíthatja az állapotára törléskor. A megőrzési időszak lejárta után a blob véglegesen törölve lesz. A blob Soft delete szolgáltatással kapcsolatos további információkért lásd a Blobok helyreállítható [törlését](soft-delete-blob-overview.md)ismertető témakört.

A blob-törlés a blob-adatvédelemmel kapcsolatos átfogó adatvédelmi stratégia részét képezi. További információ a Microsoft adatvédelmi javaslatairól: [Adatvédelem – áttekintés](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>A Soft Deleted Blobok kezelése a Azure Portal

A Azure Portal használatával megtekintheti és visszaállíthatja a nem törölt blobokat és pillanatképeket.

### <a name="view-deleted-blobs"></a>Törölt Blobok megtekintése

Ha a Blobok törölve lettek, alapértelmezés szerint a Azure Portal láthatatlanok. A lágyan törölt Blobok megtekintéséhez navigáljon a tároló **Áttekintés** lapjára, és kapcsolja be a **törölt Blobok megjelenítése** beállítást. A nem törölt Blobok a **törölt** állapottal jelennek meg.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Képernyőfelvétel: a Azure Portalban található, lágyan törölt Blobok listázása":::

Ezután válassza ki a törölt blobot a Blobok listájából a tulajdonságainak megjelenítéséhez. Figyelje meg, hogy az **Áttekintés** lapon a blob állapota **törölt** értékre van állítva. A portál megjeleníti azon napok számát is, ameddig a blob véglegesen nem törlődik.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Képernyőfelvétel: a Azure Portalban található, lágyan törölt blob tulajdonságai":::

### <a name="view-deleted-snapshots"></a>Törölt Pillanatképek megtekintése

A Blobok törlésekor a blobhoz társított összes pillanatkép is törlődik. Ha a törölt Blobok pillanatképekkel rendelkeznek, a törölt Pillanatképek a portálon is megjeleníthetők. Jelenítse meg a törölt Blobok tulajdonságait, majd navigáljon a **Pillanatképek** lapra, és kapcsolja be a **törölt Pillanatképek megjelenítése** beállítást.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Képernyőfelvétel a következőről ":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>A nem törölt objektumok visszaállítása a verziószámozás letiltásakor

Ha a blob verziószámozása nincs engedélyezve, a Azure Portal helyreállítható blobot kell visszaállítani, először a blob tulajdonságait kell megjelenítenie, majd az **Áttekintés** lapon kattintson a **Törlés** visszavonása gombra. A Blobok visszaállítása azt is visszaállítja, hogy a rendszer törli az összes olyan pillanatképet, amely törölve lett a törlési megőrzési időszak során.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="A helyreállítható Blobok visszaállítását bemutató képernyőkép Azure Portal":::

A helyreállítható Pillanatképek alapszintű blobba való előléptetéséhez először győződjön meg arról, hogy a blob törölt pillanatképei vissza lettek állítva. A **Törlés** visszavonása gomb kiválasztásával visszaállíthatja a Blobok nem törölt pillanatképeit, még akkor is, ha maga az alap blob nem lett törölve. Ezután válassza ki az előléptetni kívánt pillanatképet, és használja a **Pillanatkép előléptetése** gombot, hogy felülírja az alap blobot a pillanatkép tartalmával.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="A pillanatképek alap blobba való előléptetését bemutató képernyőkép":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>A nem törölt Blobok visszaállítása a verziószámozás engedélyezésekor

Ha a Verziószámozás engedélyezve van a Azure Portalban, válassza a Soft-Deleted blobot a tulajdonságok megjelenítéséhez, majd válassza a **verziók** lapot. Válassza ki az előléptetni kívánt verziót az aktuális verzióra, majd válassza az **aktuális verzió létrehozása** lehetőséget.  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Képernyőfelvétel: egy verzió előléptetése a blob visszaállításához Azure Portal":::

Ha a verziószámozás engedélyezése után szeretné visszaállítani a törölt verziókat vagy pillanatképeket, jelenítse meg a blob tulajdonságait, majd kattintson a **Törlés** visszavonása gombra az **Áttekintés** lapon.

> [!NOTE]
> Ha a Verziószámozás engedélyezve van, a törölt Blobok **Törlés** gombjának kiválasztásával visszaállíthatja az esetlegesen törölt verziókat vagy pillanatképeket, de nem állítja vissza az alap blobot. Az alap blob visszaállításához előléptetni kell egy korábbi verziót.

## <a name="manage-soft-deleted-blobs-with-code"></a>Soft-Deleted Blobok kezelése kóddal

Az Azure Storage ügyféloldali kódtárai segítségével helyreállíthatja a törölt blobokat vagy pillanatképeket. Az alábbi példák bemutatják, hogyan használható a .NET ügyféloldali kódtár.

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>A nem törölt objektumok visszaállítása a verziószámozás letiltásakor

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A törölt Blobok visszaállításához, ha a verziószámozás nincs engedélyezve, hívja meg a [Blobok törlésének](/rest/api/storageservices/undelete-blob) visszavonása műveletet. A **blob törlésének** visszavonása művelet visszaállítja a törölt blobokat és az azokhoz társított törölt pillanatképeket.

A nem törölt Blobok törlésének meghívása nincs hatással a nem törölt **blobokra** . A következő példa meghívja a **blob törlését** a tároló összes blobján, és visszaállítja a nem törölt blobokat és azok pillanatképeit:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Egy adott verzió visszaállításához először hívja meg az alapszintű blobon vagy verzión a **blob törlésének** visszavonása műveletet, majd másolja a kívánt verziót az alap blob fölé. A következő példa visszaállítja a blokk blobot a legutóbb mentett verzióra:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

A törölt Blobok visszaállításához, ha a verziószámozás nincs engedélyezve, hívja meg a [Blobok törlésének](/rest/api/storageservices/undelete-blob) visszavonása műveletet. A **blob törlésének** visszavonása művelet visszaállítja a törölt blobokat és az azokhoz társított törölt pillanatképeket.

A nem törölt Blobok törlésének meghívása nincs hatással a nem törölt **blobokra** . A következő példa meghívja a **blob törlését** a tároló összes blobján, és visszaállítja a nem törölt blobokat és azok pillanatképeit:

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Egy adott pillanatkép visszaállításához először hívja meg a **blob törlésének** visszavonása műveletet az alap blobon, majd másolja a kívánt pillanatképet az alap blob fölé. Az alábbi példa visszaállít egy blokk-blobot a legutóbb generált pillanatképre:

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>A nem törölt Blobok visszaállítása a verziószámozás engedélyezésekor

Ha a Verziószámozás engedélyezve van, egy helyreállított blob visszaállításához másolja a korábbi verziót az alap blobba egy [másolási blobtal](/rest/api/storageservices/copy-blob) , vagy [másolja a BLOBOT az URL-](/rest/api/storageservices/copy-blob-from-url) műveletből.  

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Nem alkalmazható. A blob verziószámozása csak az Azure Storage ügyféloldali kódtárak 12. x vagy újabb verziója esetén támogatott.

---

## <a name="next-steps"></a>Következő lépések

- [A blob Storage-hoz készült Soft delete](./soft-delete-blob-overview.md)
- [Blobok helyreállítható törlésének engedélyezése](soft-delete-blob-enable.md)
- [BLOB verziószámozása](versioning-overview.md)
