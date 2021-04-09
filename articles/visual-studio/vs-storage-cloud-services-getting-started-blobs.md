---
title: A blob Storage használatának első lépései a Visual Studióval (Cloud Services)
description: Az Azure Blob Storage használatának első lépései a Cloud Service-projektben a Visual Studióban, miután kapcsolódott egy Storage-fiókhoz a Visual Studio Connected Services használatával
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89e0d6873ebfd8f8396c36185730c57a66af0dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96007033"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Ismerkedés az Azure Blob Storage-hez és a Visual Studióhoz kapcsolódó szolgáltatásokkal (felhőszolgáltatás-projektek)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogyan kezdheti meg az Azure Blob Storaget az Azure Storage-fiók létrehozása után, ha a Visual Studio Cloud Services-projekthez **csatlakoztatott szolgáltatások hozzáadása** párbeszédablakot használja. Bemutatjuk, hogyan férhet hozzá és hozhat létre blob-tárolókat, és hogyan végezhet el olyan gyakori feladatokat, mint például a Blobok feltöltése, listázása és letöltése. A mintákat C nyelven írták \# , és a [.net-hez készült Microsoft Azure Storage ügyféloldali kódtárat](/previous-versions/azure/dn261237(v=azure.100))használják.

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására szolgál, amelyek HTTP-vagy HTTPS-kapcsolaton keresztül bárhonnan elérhetők a világon. Egyetlen blob lehet bármilyen méretű. A Blobok olyan dolgok, mint például a képek, a hang-és videofájlok, a nyers adatok és a dokumentumok fájljai.

Ahogy a mappákban élő fájlok, a Storage-Blobok élő tárolókban. A tároló létrehozása után létre kell hoznia egy vagy több tárolót a tárolóban. Például egy "scrapbook" nevű tárolóban létrehozhat tárolókat a "lemezképek" nevű tárolóban a képek tárolásához, és egy másik "hang" nevű fájlt a hangfájlok tárolásához. A tárolók létrehozása után egyéni blob-fájlokat is feltölthet rájuk.

* A Blobok programozott kezelésével kapcsolatos további információkért lásd: [Az Azure Blob Storage használatának első lépései a .NET használatával](../storage/blobs/storage-quickstart-blobs-dotnet.md).
* Az Azure Storage szolgáltatással kapcsolatos általános információkért lásd a [Storage dokumentációját](https://azure.microsoft.com/documentation/services/storage/).
* Az Azure Cloud Servicesával kapcsolatos általános információkért tekintse meg [Cloud Services dokumentációját](https://azure.microsoft.com/documentation/services/cloud-services/).
* További információ a ASP.NET-alkalmazások programozásáról: [ASP.net](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>BLOB-tárolók elérése a kódban
A Cloud Service-projektekben lévő Blobok programozott eléréséhez hozzá kell adnia a következő elemeket, ha még nincsenek jelen.

1. Adja hozzá a következő kód névtér-deklarációkat az összes olyan C#-fájl elejéhez, amelyben programozottan szeretné elérni az Azure Storage-t.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Szerezzen be egy **CloudStorageAccount** -objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával kérheti le a Storage-beli kapcsolódási karakterláncot és a Storage-fiók adatait az Azure-szolgáltatás konfigurációjában.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
    ```
3. Szerezzen be egy **CloudBlobClient** objektumot, amely a Storage-fiókban található meglévő tárolóra hivatkozik.
   
    ```csharp
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```
4. **CloudBlobContainer** -objektum lekérése egy adott blob-tárolóra való hivatkozáshoz.
   
    ```csharp
    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

> [!NOTE]
> Használja az előző eljárásban látható összes kódot az alábbi szakaszokban látható kód elé.
> 
> 

## <a name="create-a-container-in-code"></a>Tároló létrehozása a kódban
> [!NOTE]
> Egyes API-k, amelyek az Azure Storage-ba irányuló hívásokat hajtanak végre a ASP.NET-ben aszinkron módon. Lásd: [aszinkron programozás aszinkron módon, és](/previous-versions/hh191443(v=vs.140)) további információra számíthat. Az alábbi példában szereplő kód azt feltételezi, hogy aszinkron programozási módszereket használ.
> 
> 

Ahhoz, hogy tárolót hozzon létre a Storage-fiókban, mindössze annyit kell tennie, hogy felvesz egy hívást a **CreateIfNotExistsAsync** -be a következő kódban:

```csharp
// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```


Ha a tárolóban lévő fájlokat mindenki számára elérhetővé szeretné tenni, beállíthatja, hogy a tároló nyilvános legyen a következő kód használatával.

```csharp
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```


Az interneten bárki láthatja a blobokat egy nyilvános tárolóban, de csak akkor módosíthatja vagy törölheti őket, ha rendelkezik a megfelelő hozzáférési kulccsal.

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
Az Azure Storage támogatja a blobok és a Blobok blokkolását. A legtöbb esetben a blokkblobok használata javasolt.

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, és annak segítségével kérje le a blokkblob hivatkozását. Ha megszerezte a blobhivatkozást, akkor az **UploadFromStream** metódus hívásával bármilyen streamet feltölthet. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen. Az alábbi példák azt mutatják be, hogyan tölthetők fel blobok egy tárolóba, és feltételezik, hogy a tároló már létre lett hozva.

```csharp
// Retrieve a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása
A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. Ezt követően a tároló **ListBlobs** metódusának segítéségével lekérheti a benne lévő blobokat és/vagy könyvtárakat. Ha a tulajdonságok és metódusok gazdag készletét szeretné elérni egy visszaadott **IListBlobItem**, azt egy **CloudBlockBlob**, **CloudPageBlob** vagy **CloudBlobDirectory** objektumra kell átadnia. Ha a típus ismereten, akkor a típusellenőrzés segítségével határozza meg, hogy milyen típussá kell átalakítani. Az alábbi kód bemutatja, hogyan kérhető le és küldhető el a **photos** tárolóban lévő egyes elemek URI azonosítója:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
{
    if (item.GetType() == typeof(CloudBlockBlob))
    {
        CloudBlockBlob blob = (CloudBlockBlob)item;

        Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

    }
    else if (item.GetType() == typeof(CloudPageBlob))
    {
        CloudPageBlob pageBlob = (CloudPageBlob)item;

        Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

    }
    else if (item.GetType() == typeof(CloudBlobDirectory))
    {
        CloudBlobDirectory directory = (CloudBlobDirectory)item;

        Console.WriteLine("Directory: {0}", directory.Uri);
    }
}
```

Ahogy az előző kódrészletben is látható, a blob szolgáltatás a tárolókban található címtárak fogalmát is megjeleníti. Így a Blobok a mappákhoz hasonló struktúrában rendezhetők. Vegyük példaként az alábbi blokkblobokat a **photos** nevű tárolóban:

```output
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

Ha meghívja a **ListBlobs** a tárolón (ahogy az előző példában is látható), a visszaadott gyűjtemény a legfelső szinten található címtárakat és blobokat jelképező **CloudBlobDirectory** -és **CloudBlockBlob** -objektumokat tartalmazza. Az eredményül kapott kimenet:

```output
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```


Beállíthatja a **ListBlobs** metódus **UseFlatBlobListing** paraméterét **true** értékre. Ennek eredményeképpen minden blob **CloudBlockBlob**-ként lett visszaadva, a címtártól függetlenül. Itt látható a **ListBlobs** meghívása:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

az eredmények a következők:

```output
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

További információ: [CloudBlobContainer. ListBlobs](/rest/api/storageservices/List-Blobs).

## <a name="download-blobs"></a>Blobok letöltése
Blobok letöltéséhez először kérjen le egy blobhivatkozást, majd hívja a **DownloadToStream** metódust. A következő példa a **DownloadToStream** metódus segítségével helyezi át a blob tartalmát egy stream objektumra, amelyet egy helyi fájlban megőrizhet.

```csharp
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

A **DownloadToStream** metódussal a blob tartalmát szöveges sztringként is letöltheti.

```csharp
// Get a reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Blobok törlése
BLOB törléséhez először szerezzen be egy blob-hivatkozást, majd hívja meg a **delete** metódust.

```csharp
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```


## <a name="list-blobs-in-pages-asynchronously"></a>Blobok listázása több oldalon aszinkron módon
Nagyszámú blob listázásakor, vagy ha szabályozni szeretné a listázási művelet által megjelenített eredmények számát, a blobokat több eredményoldalra is listázhatja. Ez a példa bemutatja, hogyan kérhetők le eredmények több oldalon aszinkron módon úgy, hogy amíg egy nagy eredménykészletre várakozik, a rendszer ne tiltsa le a végrehajtást.

Ez a lista egy strukturálatlan bloblistát mutat be, de hierarchikus listát is kérhet, ha a **useFlatBlobListing** paramétert a **ListBlobsSegmentedAsync** metódusban átállítja **false** értékre.

Mivel a mintametódus aszinkrón metódust használ, előtagként az **async** kulcsszót kell megadni, és **Task** objektumot kell visszaadnia. A **ListBlobsSegmentedAsync** metódushoz megadott await kulcsszó a listázási feladat befejezéséig felfüggeszti a mintametódus futtatását.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    // List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    // When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        // or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]