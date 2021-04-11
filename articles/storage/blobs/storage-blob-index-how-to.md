---
title: Az Azure-Blob Storageban tárolt adatkezelési és-keresési blob-indexelési címkék használata
description: Tekintse át, hogyan használhat blob-indexelési címkéket a blob-objektumok kategorizálásához, kezeléséhez és lekérdezéséhez.
author: twooley
ms.author: twooley
ms.date: 03/05/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 6f7ae7af0f2764bd3f157ad5ca3166c9989cd9bf
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276790"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Az Azure-beli blob-indexek (előzetes verzió) használatával kezelhetők és megkereshetők az Azure Blob Storage

A blob index címkéje a Storage-fiókban lévő, kulcs-érték címkével rendelkező attribútumokkal kategorizálja az adatait. Ezek a címkék automatikusan indexelve vannak, és egy kereshető többdimenziós indexként jelennek meg, így könnyen megkereshetők az adatkeresések. Ebből a cikkből megtudhatja, hogyan állíthat be, kérhet le és kereshet meg a blob-indexek címkéi használatával.

> [!IMPORTANT]
> A blob index címkék jelenleg **előzetes** verzióban érhetők el, és az összes nyilvános régióban elérhetők. Tekintse meg az Azure-szolgáltatásokra vonatkozó, a bétaverzióban, az előzetes verzióban, vagy más módon még nem közzétett, általánosan elérhetővé vált jogi feltételekhez tartozó [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

Ha többet szeretne megtudni erről a szolgáltatásról, valamint az ismert problémákról és korlátozásokról, tekintse meg az [Azure Blob-adatkezelés és-keresés a blob-index címkékkel (előzetes verzió)](storage-manage-find-blobs.md)című témakört

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

- A blob index előzetes verziójához való hozzáféréshez regisztrált és jóváhagyott Azure-előfizetés
- Hozzáférés a [Azure Portal](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)

Mivel a blob-index előzetes verzióban érhető el, a .NET Storage-csomag megjelenik az előzetes verziójú NuGet-hírcsatornában. Ez a tár az előzetes verzió időtartama alatt változhat.

1. Állítsa be a Visual Studio-projektet, és ismerkedjen meg az Azure Blob Storage a .NET-hez készült ügyféloldali kódtáraval. További információ: [.net](storage-quickstart-blobs-dotnet.md) gyors útmutató

2. A NuGet csomagkezelő eszközben keresse meg az **Azure. Storage. Blobs** csomagot, és telepítse a **12.7.0-preview. 1** vagy újabb verziót a projekthez. A PowerShell-parancsot is futtathatja: `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

   További információt a [csomagok keresése és telepítése](/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)című témakörben talál.

3. Adja hozzá a következő using utasításokat a fájl elejéhez.

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>Új blob feltöltése tárgymutató-címkékkel

Ezt a feladatot a [Storage blob-adatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) vagy egy olyan rendszerbiztonsági tag hajthatja végre, amely `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` egy egyéni Azure-szerepkörön keresztül engedélyt kapott az [Azure erőforrás-szolgáltatói műveletre](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) .

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Storage-fiókját. 

2. Navigáljon a containers ( **tárolók** ) lehetőségre **blob Service** alatt, és válassza ki a tárolót

3. Jelölje be a **feltöltés** gombot, és tallózással keresse meg a helyi fájlrendszert, és keresse meg a feltöltendő fájlt a blokk blobként.

4. Bontsa ki a **speciális** legördülő menüt, és nyissa meg a **blob-index címkék** szakaszt.

5. Adja meg az adatokra alkalmazni kívánt kulcs/érték blob-index címkéit

6. A blob feltöltéséhez kattintson a **feltöltés** gombra.

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Képernyőkép a Azure Portalről, amely bemutatja, hogyan tölthet fel egy blobot index címkékkel.":::

# <a name="net"></a>[.NET](#tab/net)

Az alábbi példa bemutatja, hogyan hozhat létre egy hozzáfűzési blobot a létrehozás során létrehozott címkékkel.

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>BLOB-index címkék beolvasása, beállítása és frissítése

A blob-indexek címkéit a [Storage blob-adattulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) vagy egy olyan rendszerbiztonsági tag hajthatja végre, amely `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` egy egyéni Azure-szerepkörön keresztül engedélyt kapott az [Azure erőforrás-szolgáltatói műveletre](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) .

A blob-indexek beállítását és frissítését a [Storage blob-adattulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) vagy egy olyan rendszerbiztonsági tag hajthatja végre, amely `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` egy egyéni Azure-szerepkörön keresztül engedélyt kapott az [Azure erőforrás-szolgáltatói műveletre](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) .

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Storage-fiókját. 

2. Navigáljon a containers ( **tárolók** ) lehetőségre a **blob Service** alatt, válassza ki a tárolót

3. Válassza ki a blobot a kiválasztott tárolóban lévő Blobok listájából

4. A blob áttekintése lapon megjelenik a blob tulajdonságai, beleértve a **blob-index címkéit** is

5. A blobhoz tartozó kulcs/érték indexek címkéit lekérheti, beállíthatja, módosíthatja vagy törölheti.

6. Kattintson a **Save (Mentés** ) gombra a blob frissítéseinek megerősítéséhez

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Képernyőkép a Azure Portalről, amely bemutatja, hogyan lehet beolvasni, beállítani, frissíteni és törölni a Blobok indexelési címkéit.":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Az Adatszűrés és-keresés a blob-index címkékkel

Ezt a feladatot a [Storage blob-adatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) vagy egy olyan rendszerbiztonsági tag hajthatja végre, amely `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` egy egyéni Azure-szerepkörön keresztül engedélyt kapott az [Azure erőforrás-szolgáltatói műveletre](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) .

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portalon belül a blob-index címkék szűrő automatikusan alkalmazza a `@container` paramétert a kiválasztott tároló hatókörére. Ha a teljes Storage-fiókban szeretné szűrni és megtalálni a címkézett adatait, használja a REST API, SDK-kat vagy eszközöket.

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Storage-fiókját. 

2. Navigáljon a containers ( **tárolók** ) lehetőségre **blob Service** alatt, és válassza ki a tárolót

3. Válassza a **blob index címkék szűrő** gombot a kiválasztott tárolón belüli szűréshez

4. Adja meg a blob index címke kulcsát és a címke értékét

5. A **blob index címkék szűrő** gombjának kiválasztásával további címke-szűrőket adhat hozzá (legfeljebb 10)

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Képernyőkép az Azure Portalről, amely bemutatja, hogyan szűrheti és keresheti meg a címkézett blobokat az indexelési címkék használatával":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Életciklus-kezelés blob-index címkékkel

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Storage-fiókját. 

2. Navigáljon az **életciklus-kezelési** lehetőségre a **blob Service** alatt

3. Válassza a *szabály hozzáadása* lehetőséget, majd töltse ki a műveleti készlet űrlap mezőket.

4. Válassza ki a **szűrő** beállítása lehetőséget, ha opcionális szűrőt szeretne hozzáadni az előtag egyeztetéséhez és a blob-index egyeztetéséhez.

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Képernyőkép a Azure Portalről, amely bemutatja, hogyan adhat hozzá tárgymutató-címkéket az életciklus-kezeléshez.":::

5. A szabály beállításainak áttekintéséhez válassza a **felülvizsgálat + Hozzáadás** elemet.

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Képernyőkép a Azure Portalről, amely egy életciklus-kezelési szabályt mutat be a blob index címkék szűrővel példa":::

6. Válassza a **Hozzáadás** lehetőséget az új szabály életciklus-kezelési házirendre való alkalmazásához

# <a name="net"></a>[.NET](#tab/net)

Az [életciklus-kezelési](storage-lifecycle-management-concepts.md) szabályzatok minden egyes Storage-fiókra vonatkoznak a vezérlési sík szintjén. A .NET esetében telepítse a [Microsoft Azure felügyeleti tár](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) 16.0.0 vagy újabb verzióját.

---

## <a name="next-steps"></a>Következő lépések

 - További információ a blob-indexek címkékkel kapcsolatban: [Azure-Blobok adatainak kezelése és keresése blob-index címkékkel (előzetes verzió)](storage-manage-find-blobs.md )
 - További információ az életciklus-kezelésről: [Az Azure Blob Storage életciklusának kezelése](storage-lifecycle-management-concepts.md)
