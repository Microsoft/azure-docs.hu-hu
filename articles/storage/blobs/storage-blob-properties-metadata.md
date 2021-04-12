---
title: Blobok tulajdonságainak és metaadatainak kezelése a .NET-Azure Storage használatával
description: Ismerje meg, hogyan állíthatja be és kérheti le a rendszertulajdonságokat, és hogyan tárolhatja az egyéni metaadatokat az Azure Storage-fiókban lévő blobokon a .NET ügyféloldali kódtár használatával
services: storage
author: twooley
ms.author: twooley
ms.date: 09/25/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 24ec646c2928570c67a7f71481f2ca0191f1c8b9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280207"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>BLOB-tulajdonságok és-metaadatok kezelése a .NET-tel

A bennük található adatokon kívül a Blobok támogatják a rendszer tulajdonságait és a felhasználó által definiált metaadatokat. Ez a cikk bemutatja, hogyan kezelheti a rendszertulajdonságokat és a felhasználó által definiált metaadatokat az [Azure Storage .net-hez készült ügyféloldali kódtár](/dotnet/api/overview/azure/storage)szolgáltatásával.

## <a name="about-properties-and-metadata"></a>A tulajdonságok és a metaadatok

- **Rendszertulajdonságok**: Rendszertulajdonságok találhatók az egyes blob Storage-erőforrásokon. Némelyikük olvasható vagy beállítható, míg mások csak olvashatók. A borítók alatt egyes Rendszertulajdonságok megfelelnek bizonyos szabványos HTTP-fejléceknek. Az Azure Storage .NET-hez készült ügyféloldali kódtára ezeket a tulajdonságokat tárolja.

- **Felhasználó által definiált metaadatok**: a felhasználó által definiált metaadatok egy vagy több, a blob Storage-erőforráshoz megadott név-érték párokból állnak. A metaadatok használatával további értékeket is tárolhat az erőforrással. A metaadatok értéke csak saját célra szolgál, és nem befolyásolja az erőforrás működésének módját.

> [!NOTE]
> A blob index címkéi lehetővé teszik a felhasználó által definiált kulcs/érték attribútumoknak az Azure Blob Storage-erőforrással együtt történő tárolását is. Míg a metaadatokhoz hasonló, csak a blob index címkéi automatikusan indexelve lesznek, és a natív blob szolgáltatás kereshetővé tesz. A metaadatokat nem lehet indexelni és lekérdezni, hacsak nem használ külön szolgáltatást, például Azure Search.
>
> A szolgáltatással kapcsolatos további információkért lásd: az [Azure Blob Storage-beli adatkezelés és-keresés a blob-indexszel (előzetes verzió)](storage-manage-find-blobs.md).

## <a name="set-and-retrieve-properties"></a>Tulajdonságok beállítása és lekérése

A következő kódrészlet a és a `ContentType` `ContentLanguage` rendszer tulajdonságait állítja be egy blobon.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A Blobok tulajdonságainak beállításához hívja meg a [SetHttpHeaders](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheaders) vagy a [SetHttpHeadersAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheadersasync). A nem explicit módon beállított tulajdonságok törlődnek. A következő kódrészlet először beolvassa a blob meglévő tulajdonságait, majd ezeket használja fel a nem frissítendő fejlécek feltöltéséhez.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_SetBlobProperties":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

A következő kódrészlet a blob rendszertulajdonságait és az egyes értékeket jeleníti meg.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobProperties":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

A blob Storage-erőforrások metaadatainak és tulajdonságértékek beolvasása egy kétlépéses folyamat. Ezeknek az értékeknek a beolvasása előtt explicit módon be kell olvasnia azokat a vagy metódus meghívásával `FetchAttributes` `FetchAttributesAsync` . A szabály alól kivételt képez, hogy a `Exists` és a `ExistsAsync` metódusok a megfelelő módszert hívják meg `FetchAttributes` a borítók alatt. Ha meghívja az egyik módszert, nem kell meghívnia a következőt: `FetchAttributes` .

> [!IMPORTANT]
> Ha azt tapasztalja, hogy a tárolási erőforráshoz tartozó tulajdonság vagy metaadatok értéke nem lett feltöltve, ellenőrizze, hogy a kód meghívja-e a `FetchAttributes` vagy a `FetchAttributesAsync` metódust.

A blob tulajdonságainak lekéréséhez hívja meg a (z) `FetchAttributes` vagy `FetchAttributesAsync` metódust a blobon a tulajdonság feltöltéséhez `Properties` .

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

## <a name="set-and-retrieve-metadata"></a>Metaadatok beállítása és lekérése

A metaadatokat egy vagy több név-érték párokkal is megadhatja blob vagy tároló erőforráson. A metaadatok beállításához adja hozzá a név-érték párokat az `Metadata` erőforrás gyűjteményéhez. Ezután hívja meg az alábbi módszerek egyikét az értékek írásához:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [SetMetadata](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadataasync)

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)
---

A metaadatok neve/értéke párok érvényes HTTP-fejlécek, és meg kell felelniük a HTTP-fejléceket szabályozó összes korlátozásnak. A metaadatok nevének érvényes HTTP-fejléc-névnek és érvényes C#-azonosítónak kell lennie, csak ASCII-karaktereket tartalmazhat, és a kis-és nagybetűket nem megkülönböztetőként kell kezelni. [Base64-kódolás](/dotnet/api/system.convert.tobase64string) vagy [URL-cím –](/dotnet/api/system.web.httputility.urlencode) nem ASCII karaktereket tartalmazó metaadat-értékek kódolása.

A metaadatok nevének meg kell felelnie a C# azonosítók elnevezési konvencióinak. A metaadatok nevei megőrzik a létrehozásuk során használt esetet, de a kis-és nagybetűk nem különböznek a beállítás vagy a beolvasás során. Ha legalább két metaadat-fejlécet használ ugyanazzal a névvel, az Azure Blob Storage a 400-as HTTP-hibakódot (hibás kérés) adja vissza.

A következő kódrészlet a Blobok metaadatait állítja be. Egy érték van beállítva a gyűjtemény `Add` metódusának használatával. A másik érték az implicit kulcs/érték szintaxis használatával van beállítva.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddBlobMetadata":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

A következő kódrészlet egy blob metaadatait olvassa be.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A metaadatok lekéréséhez hívja meg a [GetProperties](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getproperties) vagy a [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getpropertiesasync) metódust a blobon vagy a tárolón a [metaadatok](/dotnet/api/azure.storage.blobs.models.blobproperties.metadata) gyűjteményének feltöltéséhez, majd olvassa el az értékeket az alábbi példában látható módon. A **GetProperties** metódusok egyetlen hívásban lekérik a blob tulajdonságait és a metaadatokat. Ez különbözik a REST API-kkal, amelyek külön hívásokat igényelnek a [blob tulajdonságainak lekéréséhez](/rest/api/storageservices/get-blob-properties) és a [blob metaadatainak lekéréséhez](/rest/api/storageservices/get-blob-metadata).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobMetadata":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

A metaadatok lekéréséhez hívja `FetchAttributes` meg a vagy a `FetchAttributesAsync` metódust a blobon vagy a tárolón a gyűjtemény feltöltéséhez `Metadata` , majd olvassa el az értékeket az alábbi példában látható módon.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Lásd még

- [BLOB tulajdonságainak beállítása művelet](/rest/api/storageservices/set-blob-properties)
- [BLOB-tulajdonságok beolvasása művelet](/rest/api/storageservices/get-blob-properties)
- [BLOB metaadat-műveletének beállítása](/rest/api/storageservices/set-blob-metadata)
- [BLOB metaadatainak beolvasása művelet](/rest/api/storageservices/get-blob-metadata)