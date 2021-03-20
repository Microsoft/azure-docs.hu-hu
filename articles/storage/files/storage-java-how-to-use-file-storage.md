---
title: Fejlesztés az Azure Files Javával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre olyan Java-alkalmazásokat és-szolgáltatásokat, amelyek a fájlok tárolásához Azure Files használnak.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024109"
---
# <a name="develop-for-azure-files-with-java"></a>Fejlesztés az Azure Files szolgáltatáshoz Javával

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Ismerkedjen meg az olyan Java-alkalmazások fejlesztésének alapjaival, amelyek az adattárolást Azure Files használják. Hozzon létre egy konzolszoftver-alkalmazást, és ismerkedjen meg az alapszintű műveletekkel Azure Files API-kkal

- Azure-fájlmegosztás létrehozása és törlése
- Könyvtárak létrehozása és törlése
- Fájlok és könyvtárak enumerálása Azure-fájlmegosztás esetén
- Fájl feltöltése, letöltése és törlése

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása

A minták létrehozásához szüksége lesz a Java Development Kit (JDK) és a [Javához készült Azure Storage SDK](https://github.com/azure/azure-sdk-for-java)-ra. Létre kell hoznia egy Azure Storage-fiókot is.

## <a name="set-up-your-application-to-use-azure-files"></a>Az alkalmazás beállítása Azure Files használatára

A Azure Files API-k használatához adja hozzá a következő kódot a Java-fájl elejéhez, ahonnan el szeretné érni a Azure Files.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage-beli kapcsolatok karakterláncának beállítása

Azure Files használatához csatlakoznia kell az Azure Storage-fiókjához. Konfigurálja a kapcsolati karakterláncot, és használja azt a Storage-fiókhoz való kapcsolódáshoz. Definiáljon egy statikus változót a kapcsolódási sztring tárolásához.

# <a name="java-v12"></a>[Java V12](#tab/java)

Cserélje *\<storage_account_name\>* le *\<storage_account_key\>* a és a értéket a Storage-fiókjához tartozó tényleges értékekre.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Cserélje le a *your_storage_account_name* és a *your_storage_account_keyt* a Storage-fiók tényleges értékeire.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Hozzáférés Azure Files tárolóhoz

# <a name="java-v12"></a>[Java V12](#tab/java)

Azure Files eléréséhez hozzon létre egy [ShareClient](/java/api/com.azure.storage.file.share.shareclient) objektumot. Új **ShareClient** -objektum létrehozásához használja a [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) osztályt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

A Storage-fiók eléréséhez használja a **CloudStorageAccount** objektumot, és adja át a kapcsolati karakterláncot az **elemzési** módszernek.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

A **CloudStorageAccount. Parse** egy InvalidKeyException dob, így azt egy try/catch blokkban kell elhelyeznie.

---

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Azure Files összes fájlját és könyvtárát egy megosztás nevű tároló tárolja.

# <a name="java-v12"></a>[Java V12](#tab/java)

A [ShareClient. Create](/java/api/com.azure.storage.file.share.shareclient.create) metódus kivételt vet fel, ha a megosztás már létezik. A hívást **hozzon létre** egy `try/catch` blokkban, és kezelje a kivételt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Egy megosztáshoz és annak tartalmához való hozzáféréshez hozzon létre egy Azure Files ügyfelet.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

A Azure Files ügyfél használatával megszerezheti a megosztásra mutató hivatkozást.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

A megosztás tényleges létrehozásához használja a **CloudFileShare** objektum **createifnotexists metódust** metódusát.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Ezen a ponton a **megosztás** egy **minta megosztás** nevű megosztásra mutató hivatkozást tárol.

---

## <a name="delete-a-file-share"></a>Fájlmegosztás törlése

A következő mintakód törli a fájlmegosztást.

# <a name="java-v12"></a>[Java V12](#tab/java)

A [ShareClient. Delete](/java/api/com.azure.storage.file.share.shareclient.delete) metódus meghívásával törölje a megosztást.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Egy megosztás törléséhez hívja meg a **deleteifexists paranccsal** metódust egy **CloudFileShare** objektumon.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## <a name="create-a-directory"></a>Könyvtár létrehozása

A tárterületet úgy szervezheti meg, hogy az alkönyvtárakon belül helyezi el a fájlokat, ahelyett, hogy mindegyiket a gyökérkönyvtárba helyezné.

# <a name="java-v12"></a>[Java V12](#tab/java)

A következő kód egy könyvtárat hoz létre a [ShareDirectoryClient. Create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create)parancs meghívásával. A példa metódus egy értéket ad vissza `Boolean` , amely azt jelzi, hogy sikeresen létrehozta-e a könyvtárat.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

A következő kód létrehoz egy **sampledir** nevű alkönyvtárat a gyökérkönyvtárban.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

---

## <a name="delete-a-directory"></a>Könyvtár törlése

A címtár törlése egyszerű feladat. Nem törölhet olyan könyvtárat, amely továbbra is tartalmaz fájlokat vagy alkönyvtárakat.

# <a name="java-v12"></a>[Java V12](#tab/java)

A [ShareDirectoryClient. Delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) metódus kivételt jelez, ha a könyvtár nem létezik vagy nem üres. Helyezze a **törlési** hívást egy `try/catch` blokkba, és kezelje a kivételt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és könyvtárak enumerálása Azure-fájlmegosztás esetén

# <a name="java-v12"></a>[Java V12](#tab/java)

A [ShareDirectoryClient. listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories)meghívásával lekérheti a fájlok és könyvtárak listáját. A metódus a [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) objektumok listáját adja vissza, amelyeken megismételhető. A következő kód felsorolja a *könyvtárnév* paraméter által megadott könyvtárban található fájlokat és könyvtárakat.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

A **ListFilesAndDirectories** **CloudFileDirectory** -hivatkozással való meghívásával lekérheti a fájlok és könyvtárak listáját. A metódus a **ListFileItem** objektumok listáját adja vissza, amelyeken megismételhető. A következő kód a gyökérkönyvtárban található fájlokat és könyvtárakat sorolja fel.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Fájl feltöltése

Megtudhatja, hogyan tölthet fel egy fájlt a helyi tárolóból.

# <a name="java-v12"></a>[Java V12](#tab/java)

A következő kód feltölt egy helyi fájlt az Azure file Storage-ba az [ShareFileClient. uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) metódus meghívásával. A következő példa egy értéket ad vissza `Boolean` , amely azt jelzi, hogy sikeresen feltöltötte-e a megadott fájlt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

A megosztás objektum **getRootDirectoryReference** metódusának meghívásával szerezze be azt a könyvtárat, ahová a fájl fel lesz töltve.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Most, hogy már van egy hivatkozása a megosztás gyökérkönyvtárára, feltöltheti a fájlt a következő kód használatával.

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>Fájl letöltése

Az egyik leggyakoribb művelet a fájlok Azure Files tárolóból való letöltése.

# <a name="java-v12"></a>[Java V12](#tab/java)

A következő példa letölti a megadott fájlt a *destDir* paraméterben megadott helyi könyvtárba. A példa metódus a letöltött fájlnevet a dátum és idő függvényében egyedivé teszi.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

A következő példa letölti SampleFile.txt és megjeleníti a tartalmát.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

---

## <a name="delete-a-file"></a>Fájl törlése

Egy másik gyakori Azure Files művelet a fájl törlése.

# <a name="java-v12"></a>[Java V12](#tab/java)

A következő kód törli a megadott fájlt. Először a példa egy [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) hoz létre a *könyvtárnév* paraméter alapján. Ezután a kód egy [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) kap a Directory-ügyféltől a *filename* paraméter alapján. Végül a példa metódus meghívja a [ShareFileClient. Delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) fájlt a fájl törléséhez.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

A következő kód törli a **sampledir** nevű könyvtárban tárolt SampleFile.txt nevű fájlt.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

---

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure Storage API-król, kövesse az alábbi hivatkozásokat.

- [Azure Java-fejlesztőknek](/azure/developer/java)
- [Javához készült Azure SDK](https://github.com/azure/azure-sdk-for-java)
- [Androidhoz készült Azure SDK](https://github.com/azure/azure-sdk-for-android)
- [Azure file share ügyféloldali kódtár a Java SDK-hoz – dokumentáció](/java/api/overview/azure/storage-file-share-readme)
- [Az Azure Storage-szolgáltatások REST API-ja](/rest/api/storageservices/)
- [Az Azure Storage csapat blogja](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Adatok átvitele a AzCopy Command-Line segédprogrammal](../common/storage-use-azcopy-v10.md)
- [Azure Files-problémák hibaelhárítása – Windows](storage-troubleshoot-windows-file-connection-problems.md)
