---
title: 'Gyors útmutató: Azure Blob Storage Library V12 – Java'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használható a Java-hoz készült Azure Blob Storage Client Library 12-es verziója, és hogyan hozható létre tároló és blob (Object) tároló. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 12/01/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b5c34cea5d8222a246462bfadde66fd8a5ddbec7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054703"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Gyors útmutató: Blobok kezelése a Java V12 SDK-val

Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti a blobokat a Java használatával. A Blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatok tárolására képesek, beleértve a képeket, a dokumentumokat, a médiafolyamokat és az archiválási adatokhoz. Feltöltheti, letöltheti és listázhatja a blobokat, és létrehozhatja és törölheti a tárolókat.

További források:

* [API-referenciadokumentáció](/java/api/overview/azure/storage-blob-readme)
* [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob)
* [Csomag (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob)
* [Példák](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy Storage-fiókot](../common/storage-account-create.md).
- A [Java Development Kit (JDK)](/java/azure/jdk/) 8-as vagy újabb verziója.
- [Apache Maven](https://maven.apache.org/download.cgi).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti a projekt előkészítésének folyamatán a Javához készült Azure Blob Storage ügyféloldali kódtár V12-es verziójának használatával.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *blob-Gyorsindítás-V12* nevű Java-alkalmazást.

1. A konzol ablakban (például cmd, PowerShell vagy bash) a Maven használatával hozzon létre egy új, *blob-Gyorsindítás-V12* nevű Console-alkalmazást. A "Hello World!" létrehozásához írja be a következő **MVN** -parancsot: Java-projekt.

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.blobs.quickstart `
        --define artifactId=blob-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.blobs.quickstart \
        --define artifactId=blob-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. A projekt létrehozásának eredményét a következőhöz hasonló módon kell kinéznie:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. A *blob-Gyorsindítás-V12* könyvtárban hozzon létre egy másik könyvtárat, amely az *adat* nevű. A blob-adatfájlokat a rendszer létrehozza és tárolja.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá a függőségek csoportjához a következő függőségi elemet.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.6.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Navigáljon a */src/Main/Java/com/Blobs/Quickstart* könyvtárához
1. Nyissa meg az *app. Java* fájlt a szerkesztőben
1. Az `System.out.println("Hello world!");` utasítás törlése
1. `import`Irányelvek hozzáadása

A kód a következő:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A blob Storage háromféle típusú erőforrást kínál:

* A Storage-fiók
* Egy tároló a Storage-fiókban
* A tárolóban lévő blob

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![BLOB Storage-architektúra ábrája](./media/storage-blobs-introduction/blob1.png)

A következő Java-osztályok használhatók az alábbi erőforrásokkal való kommunikációhoz:

* [BlobServiceClient](/java/api/com.azure.storage.blob.blobserviceclient): a `BlobServiceClient` osztály lehetővé teszi az Azure Storage-erőforrások és a blob-tárolók kezelését. A Storage-fiók a legfelső szintű névteret biztosítja a Blob service számára.
* [BlobServiceClientBuilder](/java/api/com.azure.storage.blob.blobserviceclientbuilder): az `BlobServiceClientBuilder` osztály egy FLUENT Builder API-t biztosít az objektumok konfigurációjának és példányának támogatásához `BlobServiceClient` .
* [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient): a `BlobContainerClient` osztály lehetővé teszi az Azure Storage-tárolók és a Blobok kezelését.
* [BlobClient](/java/api/com.azure.storage.blob.blobclient): a `BlobClient` osztály lehetővé teszi az Azure Storage-Blobok kezelését.
* [Blobelemet](/java/api/com.azure.storage.blob.models.blobitem): az `BlobItem` osztály a [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs)hívása által visszaadott egyes blobokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan hajthatja végre a következőket az Azure Blob Storage Javához készült ügyféloldali kódtár használatával:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [Tárolóban lévő blobok kilistázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód a Storage- [kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból kéri le a Storage-fiókhoz tartozó kapcsolatok karakterláncát.

Adja hozzá ezt a kódot a `Main` metódushoz:

```java
System.out.println("Azure Blob Storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód egy UUID értéket fűz hozzá a tároló nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Ezután hozza létre a [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient) osztály egy példányát, majd hívja meg a [create](/java/api/com.azure.storage.blob.blobcontainerclient.create) metódust, hogy ténylegesen létrehozza a tárolót a Storage-fiókban.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. Létrehoz egy szövegfájlt *a helyi* adatkönyvtárban.
1. Beolvas egy [BlobClient](/java/api/com.azure.storage.blob.blobclient) objektumra mutató hivatkozást úgy, hogy meghívja a [getBlobClient](/java/api/com.azure.storage.blob.blobcontainerclient.getblobclient) metódust a tárolóban a [tároló létrehozása](#create-a-container) szakaszban.
1. A [uploadFromFile](/java/api/com.azure.storage.blob.blobclient.uploadfromfile) metódus meghívásával feltölti a helyi szövegfájlt a blobba. Ez a metódus létrehozza a blobot, ha még nem létezik, de nem írja felül, ha igen.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

A tárolóban lévő Blobok listázása a [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs) metódus meghívásával. Ebben az esetben a tárolóhoz csak egy blob lett hozzáadva, így a listázási művelet csak ezt az egy blobot adja vissza.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [downloadToFile](/java/api/com.azure.storage.blob.specialized.blobclientbase.downloadtofile) metódus meghívásával. A példában szereplő kód a "Letöltés" utótagot adja hozzá a fájl nevéhez, hogy mindkét fájl látható legyen a helyi fájlrendszerben.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód törli az alkalmazás által létrehozott erőforrásokat, ha eltávolítja a teljes tárolót a [delete](/java/api/com.azure.storage.blob.blobcontainerclient.delete) metódus használatával. Emellett törli az alkalmazás által létrehozott helyi fájlokat is.

Az alkalmazás a felhasználói bevitel szüneteltetését hívja `System.console().readLine()` meg, mielőtt törli a blobot, a tárolót és a helyi fájlokat. Ez jó eséllyel ellenőrizhető, hogy az erőforrások megfelelően lettek-e létrehozva, mielőtt törölné őket.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy tesztoldalt a helyi mappában, és feltölti azt a blob Storage-ba. A példa ezután felsorolja a tárolóban lévő blobokat, és letölti a fájlt egy új névvel, hogy össze lehessen hasonlítani a régi és az új fájlokat.

Navigáljon a *pom.xml* fájlt tartalmazó könyvtárra, és fordítsa le a projektet a következő `mvn` parancs használatával.

```console
mvn compile
```

Ezután hozza létre a csomagot.

```console
mvn package
```

Futtassa az alábbi `mvn` parancsot az alkalmazás végrehajtásához.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Blob Storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

A tisztítási folyamat megkezdése előtt tekintse *meg a két fájl adatmappáját* . Ha megnyitja őket, láthatja, hogy megegyeznek.

A fájlok ellenőrzése után nyomja le az **ENTER** billentyűt a tesztoldal törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhat blobokat a Java használatával.

A blob Storage-beli minták alkalmazásainak megtekintéséhez folytassa a következőket:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 Java-minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* További információért lásd a [Javához készült Azure SDK](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md)-t.
* Az oktatóanyagok, minták, rövid útmutatók és egyéb dokumentációk az Azure-ban [a Java Cloud Developers szolgáltatásban](/azure/developer/java/)találhatók meg.
