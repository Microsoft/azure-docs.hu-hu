---
title: 'Gyors útmutató: Azure Blob Storage ügyféloldali kódtára a Javához'
description: Létre fog hozni egy tárfiókot és egy tárolót egy objektumtárban (Blobtárban). Ezután használja az Azure Storage ügyféloldali kódtárat a Javához, és töltsön fel egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b3bb8736eb7a8e24f47812fc4feecbf96628dfb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98599810"
---
# <a name="quickstart-manage-blobs-with-java-v8-sdk"></a>Gyors útmutató: Blobok kezelése a Java V8 SDK-val

Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti a blobokat a Java használatával. A Blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatok tárolására képesek, beleértve a képeket, a dokumentumokat, a médiafolyamokat és az archiválási adatokhoz. A blobokat feltöltheti, letöltheti és listázhatja. Emellett létrehozza, beállítja az engedélyeket és törli a tárolókat is.

> [!NOTE]
> Ez a rövid útmutató az Azure Blob Storage ügyféloldali függvénytárának örökölt verzióját használja. A legújabb verzió használatának megkezdéséhez lásd: gyors útmutató [: Blobok kezelése a Java V12 SDK-val](storage-quickstart-blobs-java.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy Storage-fiókot](../common/storage-account-create.md).
- Maven-integrációt tartalmazó IDE. Ez az útmutató az [Eclipse](https://www.eclipse.org/downloads/) -t használja az "Eclipse ide for Java Developers" konfigurációval.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A [minta alkalmazás](https://github.com/Azure-Samples/storage-blobs-java-quickstart) egy alapszintű konzolos alkalmazás.

A [git](https://git-scm.com/) használatával letöltheti az alkalmazás egy példányát a fejlesztői környezetbe.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A projekt megnyitásához indítsa el az Eclipse-t, és zárja be az üdvözlőképernyőt. Kattintson a **File** (Fájl), majd az **Open Projects from File System** (Projekt megnyitása fájlrendszerből) lehetőségre. Ügyeljen arra, hogy a **Detect and configure project natures** (Projektek természetének észlelése és konfigurálása) lehetőség be legyen jelölve. Válassza a **Directory** (Könyvtár) lehetőséget, majd lépjen oda, ahová a klónozott adattárat mentette. A klónozott adattárban válassza a **blobAzureApp** mappát. Győződjön meg arról, hogy a **blobAzureApp** projekt megjelenik Eclipse-projektként, majd válassza a **Finish** (Befejezés) lehetőséget.

Miután a projekt befejeződik, nyissa meg a **AzureApp. Java** (amely a **blobQuickstart. blobAzureApp** mappában található a **src/Main/Java**-ban), és cserélje le a `accountname` `accountkey` karakterláncot a és a belső helyére `storageConnectionString` . Ezután futtassa az alkalmazást. Az ezen feladatok elvégzésére vonatkozó utasítások leírását a következő szakaszok tartalmazzák.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati sztringet. Nyissa meg az **AzureApp.Java** fájlt. Keresse meg a `storageConnectionString` változót, és illessze be az előző szakaszban másolt kapcsolati sztring értéket. A `storageConnectionString` változó az alábbi példakódhoz hasonlóan néz ki:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Minta futtatása

Ez a minta alkalmazás létrehoz egy tesztoldalt az alapértelmezett címtárban (*C:\Users \<user> \AppData\Local\Temp*, Windows-felhasználók számára), feltölti a blob Storage-ba, felsorolja a tárolóban lévő blobokat, majd letölti a fájlt egy új névvel, hogy össze tudja hasonlítani a régi és az új fájlokat.

A minta futtatásához használja a Mavent a parancssorban. Nyisson meg egy rendszerhéjat, és navigáljon a **blobAzureApp** elemre a klónozott könyvtárában. Ezután írja be az `mvn compile exec:java` parancsot.

A következő példában látható kimenet az alkalmazás Windows rendszeren történő futtatásának eredményét mutatja.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

A folytatás előtt tekintse meg az alapértelmezett címtárat (*C:\Users \<user> \AppData\Local\Temp* Windows-felhasználók számára) a szövegfájlhoz. Másolja ki a blob URL-címét a konzolablakból, és másolja be egy böngészőbe a fájl tartalmának blobtárolóban való megtekintéséhez. Ha összehasonlítja a könyvtárban lévő mintafájlt és a Blob Storage-ben tárolt tartalmakat, azt tapasztalja, hogy ezek megegyeznek.

  >[!NOTE]
  >Az [Azure Storage Explorert](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy egy ahhoz hasonló eszközt is használhat, ha szeretné a fájlt megtekinteni a blobtárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat.

A fájlok ellenőrzése után nyomja le az **ENTER** billentyűt a bemutató befejezéséhez és a tesztelési fájlok törléséhez. Most, hogy tudja, hogy működik a minta, nyissa meg az **AzureApp.java** fájlt, és tekintse meg a kódot.

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Az első teendő a referenciák létrehozása a Blob-tárolóhoz való hozzáféréshez és kezeléséhez használt objektumokhoz. Ezek az objektumok egymásra épülnek – mindegyiket a listában utánuk következő használja.

* Hozza létre a [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) objektum egy példányát, amely a tárfiókra mutat.

    A **CloudStorageAccount** objektum az Ön tárfiókját képviseli. Ezzel az objektummal állíthatja be és érheti el a programozási környezetből a tárfiók tulajdonságait. A **CloudStorageAccount** objektum használatával létrehozhatja a **CloudBlobClient** példányát, amely a blobszolgáltatás eléréséhez szükséges.

* Hozza létre a **CloudBlobClient** objektum egy példányát, amely a Storage-fiókjában lévő [blob Service](/java/api/com.microsoft.azure.storage.blob.cloudblobclient) mutat.

    A **CloudBlobClient** hozzáférési lehetőséget biztosít a Blob szolgáltatáshoz, amellyel a programozási környezetből állíthatja be és érheti el a Blob Storage tulajdonságait. A **CloudBlobClient** objektum használatával létrehozhatja a **CloudBlobContainer** példányát, amely a tárolók létrehozásához szükséges.

* Hozza létre a [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer) objektum egy példányát. Ez azt a tárolót képviseli, amelyhez Ön hozzáfér. A blobok a tárolókkal rendszerezhetők, hasonlóan ahhoz, ahogyan a számítógépen mappákba rendszerezi a fájlokat.

    Ha már rendelkezik a **CloudBlobContainer**, létrehozhat egy [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob) objektum egy példányát, amely arra az adott blobra mutat, amelyre kíváncsi, és elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveletet.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolókkal kapcsolatos további információkért tekintse meg a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával foglalkozó cikket](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Tároló létrehozása

Ebben a szakaszban létre fogja hozni az objektumok egy-egy példányát és egy új tárolót, majd beállítja annak engedélyeit úgy, hogy a blobok nyilvánosak és mindössze egy URL-cím alapján elérhetőek legyenek. A tároló neve **quickstartcontainer**.

Ez a példa a [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists) metódust használja, mert a minta futtatásakor mindig új tárolót szeretnénk létrehozni. Éles környezetben, ahol ugyanazt a tárolót használja egy alkalmazásban, jobb eljárás a **CreateIfNotExists** egyszeri hívása. Azt is megteheti, hogy előre létrehozza a tárolót, hogy ne a kódban kelljen létrehoznia.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

Egy fájl egy blokk blobba való feltöltéséhez szerezzen be egy hivatkozást a blobra a tárolóban. Ha megszerezte a blobhivatkozást, adatokat a [CloudBlockBlob.Upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload) használatával tölthet fel rá. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen.

A mintakód létrehoz egy, a fel- és letöltéshez használatos helyi fájlt, és úgy tárolja, hogy az **source** néven és a blob nevével elnevezve legyen feltöltve a **blob** helyre. A következő példa feltölti a fájlt a **quickstartcontainer** nevű tárolóba.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

A Blob Storage-hoz többféle `upload` metódus használható, ilyen például az [upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload), az [uploadBlock](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadblock), az [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadfullblob), az [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadstandardblobtier) és az [uploadText](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadtext). Ha például sztringje van, használhatja az `UploadText` metódust az `Upload` helyett.

A blokkblobok akármilyen típusú szöveges vagy bináris fájlok lehetnek. A lapblobok elsősorban az IaaS virtuális gépek biztonsági mentéséhez szükséges VHD-fájlokhoz használhatók. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt objektumok a legtöbb esetben blokkblobok.

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

A tárolóban található fájlok listáját a [CloudBlobContainer.ListBlobs](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobs) paranccsal szerezheti be. A következő kód lekéri a blobok listáját, majd végighalad rajtuk, és megjeleníti a talált blobok URI-ját. Az URI-t kimásolhatja a parancsablakból, és a fájl megtekintéséhez beillesztheti egy böngészőbe.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Blobok letöltése

Blobokat a helyi lemezre a [CloudBlob.DownloadToFile](/java/api/com.microsoft.azure.storage.blob.cloudblob.downloadtofile) metódussal tölthet le.

A következő kód letölti az útmutató korábbi fejezetében feltöltött blobot, és hozzáadja a „_DOWNLOADED” (Letöltve) utótagot a nevéhez, így mindkét fájlt láthatja majd a helyi lemezen.

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and
// haven't changed the blob we're interested in, so we can reuse it.
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a feltöltött blobokra, a teljes tárolót törölheti a [CloudBlobContainer. deleteifexists paranccsal](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)használatával. Ez a metódus a tárolóban található fájlokat is törli.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();

if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a Java használatával. Ha bővebb információra van szüksége a Java használatával kapcsolatban, lépjen tovább a GitHub-forráskódadattárba.

> [!div class="nextstepaction"]
> [Java API-referenciák](/java/api/overview/azure/storage?view=azure-java-legacy&preserve-view=true) 
>  [Programkód-minták a Javához](../common/storage-samples-java.md)