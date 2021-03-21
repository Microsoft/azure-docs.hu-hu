---
title: Azure rövid útmutató – Blob létrehozása objektumtárban Go használatával | Microsoft Docs
description: Ebben a rövid útmutatóban egy tárfiókot és egy tárolót hoz létre egy objektumtárban (Blob Storage-fiókban). Majd a Storage Góhoz készült ügyféloldali kódtára segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: bf0e868e9ee746da1dfe1b03403d21f7edb3bd5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95544649"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása a Go használatával

A rövid útmutató azt ismerteti, hogyan használható a Go programnyelv blokkblobok feltöltésére, letöltésére és listázására az Azure Blob Storage-ban található tárolókban. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Győződjön meg arról, hogy a következő további előfeltételek vannak telepítve:
 
* [Ugrás 1,8 vagy újabb verzióra](https://golang.org/dl/)
* [Azure Storage blob SDK for go](https://github.com/azure/azure-storage-blob-go/), a következő parancs használatával:

    ```
    go get -u github.com/Azure/azure-storage-blob-go/azblob
    ``` 

    > [!NOTE]
    > Az SDK használatakor ügyeljen arra, hogy az URL-cím alapján kihasználja az `Azure` esetekkel kapcsolatos importálási problémákat. Az `Azure` importálási utasításokban is kihasználható.
    
## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése
A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) egy egyszerű Go-alkalmazás.  

A [git](https://git-scm.com/) használatával letöltheti az alkalmazás egy példányát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Ez a parancs a helyi git mappába klónozza az adattárat. A Blob Storage-hoz készült Go-mintakód megnyitásához kéresse meg a storage-quickstart.go fájlt.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása
Ehhez a megoldáshoz a tárfióknevét és -kulcsát biztonságosan legyen tárolnia helyileg, a mintakódot futtató gép környezeti változóiban. A környezeti változók létrehozásához kövesse az alábbi példák egyikét az operációs rendszerének megfelelően.

# <a name="linux"></a>[Linux](#tab/linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windows"></a>[Windows](#tab/windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Minta futtatása
Ez a minta létrehoz egy tesztfájlt az aktuális mappában, feltölti a tesztfájlt a Blob Storage-tárolóba, listázza a tárolóban lévő blobokat, és letölti fájlt a pufferbe. 

A mintakód futtatásához adja ki a következő parancsot: 

```go run storage-quickstart.go```

Az alábbi kimenet példa az alkalmazás futtatásakor kapott kimenetre:
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Amikor lenyomja a billentyűt a folytatáshoz, a mintaprogram törli a Storage-tárolót és a fájlokat. 

> [!TIP]
> Az [Azure Storage Explorert](https://storageexplorer.com) vagy egy ahhoz hasonló eszközt is használhat, ha szeretné a fájlt megtekinteni a blobtárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat. 
>

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="create-containerurl-and-bloburl-objects"></a>ContainerURL és BlobURL objektumok létrehozása
Az első teendő a referenciák létrehozása a Blob Storage-tárolóhoz való hozzáféréshez és kezeléséhez használt ContainerURL és BlobURL objektumokhoz. Ezek az objektumok olyan alacsony szintű API-kat kínálnak REST API-k kiadására, mint a Create, az Upload és a Download.

* A hitelesítő adatok tárolásához használja a [**SharedKeyCredential**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#SharedKeyCredential) struktúrát. 

* Hozzon létre egy [**Folyamatot**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#NewPipeline) a hitelesítő adatok és beállítások segítségével. A folyamat meghatározza többek között az újrapróbálkozási szabályzatokat, a naplózást, a HTTP-válaszként küldött hasznos adatok deszerializálását és egyebeket.  

* Példányosítson egy új [**ContainerURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#ContainerURL) és egy új [**BlobURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#BlobURL) objektumot a műveletek tárolón (Create) és blobokon (Upload és Download) való futtatásához.


A ContainerURL létrehozása után létrehozhatja a **BlobURL** objektum egy blobra mutató példányát, és elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveleteket.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos részletekért lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

Ebben a szakaszban egy új tárolót hoz létre. A tároló neve **quickstartblobs-[véletlen sztring]**. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential, err := azblob.NewSharedKeyCredential(accountName, accountKey)
if err != nil {
    log.Fatal("Invalid credentials with error: " + err.Error())
}
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err = containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A leggyakrabban használt elemek a blokkblobok, és ez a rövid útmutató is ezeket használja.  

Ha fel szeretne tölteni egy fájlt egy blobba, nyissa meg a fájlt az **os.Open** használatával. Ezután feltöltheti a fájlt a megadott útvonalra a következő REST API-k egyikével: Upload (PutBlob), StageBlock/CommitBlockList (PutBlock/PutBlockList). 

Másik lehetőségként az SDK az alacsony szintű REST API-kra épülő [magas szintű API-kat](https://github.com/Azure/azure-storage-blob-go/blob/master/azblob/highlevel.go) is kínál. Az ***UploadFileToBlockBlob*** függvény például StageBlock (PutBlock) műveletekkel több darabban, egyszerre tölt fel egy fájlt az adatátvitel optimalizálása érdekében. Ha a fájl 256 MB-nál kisebb, a StageBlock (PutBlock) műveletek helyett az Upload (PutBlob) műveletet használja, és egy tranzakcióban hajtja végre az átvitelt.

A következő példa feltölti a fájlt a **quickstartblobs-[véletlen sztring]** nevű tárolóba.

```go
// Create a file to test the upload and download.
fmt.Printf("Creating a dummy file to test the upload and download\n")
data := []byte("hello world this is a blob\n")
fileName := randomString()
err = ioutil.WriteFile(fileName, data, 0700)
handleErrors(err)

// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level Upload (PutBlob) API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that Upload can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// To upload more than 256MB, use StageBlock (PutBlock) and CommitBlockList (PutBlockList) functions. 
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.Upload(ctx, file, azblob.BlobHTTPHeaders{ContentType: "text/plain"}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls StageBlock/CommitBlockList for files larger 256 MBs, and calls Upload for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

A tárolóban található fájlok listájának lekéréséhez használja a **ListBlobs** metódust a **ContainerURL** objektumon. A ListBlob egy szegmensben adja vissza a blobokat (akár 5000-et) a megadott **jelölőtől** kezdve. Ha az elejétől szeretné kezdeni, használjon üres jelölőt. A blobneveket a rendszer szótári sorrendben adja vissza. Ha kapott egy szegmenst, dolgozza fel, majd hívja meg újra a ListBlobs metódust a korábban visszaadott jelölővel.  

```go
// List the container that we have created above
fmt.Println("Listing the blobs in the container:")
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobsFlatSegment(ctx, marker, azblob.ListBlobsSegmentOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Segment.BlobItems {
        fmt.Print(" Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>A blob letöltése

A blobok letöltéséhez használja a **Download** alacsony szintű függvényt a BlobURL-eken. A függvény egy **DownloadResponse** struktúrát ad vissza. A **Body** függvény a struktúrán való futtatásával egy **RetryReader** streamet kap az adatok olvasásához. Ha egy kapcsolat nem sikerül az olvasás során, további kérelmeket küld a kapcsolat újbóli létrehozásához és az olvasás folytatásához. Ha a RetryReaderOption MaxRetryRequests értékét 0-ra állítja (ez az alapértelmezett érték), a rendszer az eredeti kéréstörzset adja vissza, és nem próbálkozik újra. Másik megoldásként használhatja a magas szintű **DownloadBlobToBuffer** vagy **DownloadBlobToFile** API-t is a kód egyszerűsítése érdekében.

Az alábbi kód a **Download** függvény használatával tölti le a blobot. A blob tartalmát a rendszer egy pufferbe írja, és megjeleníti a konzolon.

```go
// Here's how to download the blob
downloadResponse, err := blobURL.Download(ctx, 0, azblob.CountToEnd, azblob.BlobAccessConditions{}, false)

// NOTE: automatically retries are performed if the connection fails
bodyStream := downloadResponse.Body(azblob.RetryReaderOptions{MaxRetryRequests: 20})

// read the body into a buffer
downloadedData := bytes.Buffer{}
_, err = downloadedData.ReadFrom(bodyStream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban feltöltött blobokra, a teljes tárolót törölheti a **Delete** metódussal. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>Blobokkal rendelkező Go-alkalmazások fejlesztéséhez használható forrásanyagok

Blob Storage-tárolókat alkalmazó Go-alkalmazások fejlesztéséhez tekintse át az alábbi további forrásanyagokat is:

- Az Azure Storage-hoz készült [Go ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-blob-go) megtekintheti és telepítheti a GitHubról.
- Tekintse át a Go ügyféloldali kódtár használatával írt [Blob Storage-mintákat](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#pkg-examples).

## <a name="next-steps"></a>Következő lépések
 
Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a Go használatával. Az Azure Storage Blob SDK-ról a [Forráskód](https://github.com/Azure/azure-storage-blob-go/) és [API-referencia](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob) szakaszban talál további információt.