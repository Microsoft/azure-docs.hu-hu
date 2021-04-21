---
title: Fejlesztés Azure Files Java-| Microsoft Docs
description: Megtudhatja, hogyan fejleszthet olyan Java-alkalmazásokat és -szolgáltatásokat, amelyek Azure Files a fájlok adatainak tárolására.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 115c55a5833906aa0dcc616a5b1b659468647282
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814549"
---
# <a name="develop-for-azure-files-with-java"></a>Fejlesztés az Azure Files szolgáltatáshoz Javával

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Ismerje meg az adatok tárolására Azure Files Java-alkalmazások fejlesztésének alapjait. Hozzon létre egy konzolalkalmazást, és ismerje meg az alapszintű műveleteket Azure Files API-k használatával:

- Azure-fájlmegosztások létrehozása és törlése
- Könyvtárak létrehozása és törlése
- Azure-fájlmegosztás fájljainak és könyvtárának számbavétele
- Fájl feltöltése, letöltése és törlése

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása

A minták felépítéséhez szüksége lesz a Java fejlesztői készletre (JDK) és a Javához készült [Azure Storage SDK-ra.](https://github.com/azure/azure-sdk-for-java) Emellett létre kellett hoznunk egy Azure Storage-fiókot is.

## <a name="set-up-your-application-to-use-azure-files"></a>Az alkalmazás beállítása a Azure Files

A Azure Files API-k futtatásához adja hozzá a következő kódot a Java-fájl tetejéhez, ahonnan hozzá szeretne férni Azure Files.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage kapcsolati sztring beállítása

A Azure Files azure-tárfiókhoz kell csatlakoznia. Konfigurálja a kapcsolati sztringet, és használja a tárfiókhoz való csatlakozáshoz. Határozzon meg egy statikus változót a kapcsolati sztringhez.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Cserélje *\<storage_account_name\>* le a és a *\<storage_account_key\>* értékeket a tárfiók tényleges értékeire.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Cserélje *your_storage_account_name* és *your_storage_account_key* a tárfiók tényleges értékeire.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-an-azure-file-share"></a>Azure-fájlmegosztás elérése

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

A hozzáférés Azure Files hozzon létre egy [ShareClient objektumot.](/java/api/com.azure.storage.file.share.shareclient) Új ShareClient objektum létrehozása a [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) **osztállyal.**

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

A tárfiók eléréséhez használja a **CloudStorageAccount** objektumot, és továbbadja a kapcsolati sztringet az **elemzési metódusnak.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**A CloudStorageAccount.parse** invalidKeyException adatokat ad vissza, ezért egy try/catch blokkba kell tenni.

---

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

A rendszer a Azure Files összes fájlját és könyvtárát egy megosztás nevű tárolóban tárolja.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

A [ShareClient.create](/java/api/com.azure.storage.file.share.shareclient.create) metódus kivételt ad vissza, ha a megosztás már létezik. Helyezze a **létrehozási** hívást egy `try/catch` blokkba, és kezelje a kivételt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

A megosztáshoz és annak tartalmához való hozzáféréshez hozzon létre egy Azure Files ügyfelet.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

A Azure Files használatával beszerezheti a megosztásra való hivatkozást.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

A megosztás létrehozásához használja a **CloudFileShare** objektum **createIfNotExists** metódusát.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Ezen a ponton a **megosztás** egy minta-megosztás nevű megosztásra **hivatkozik.**

---

## <a name="delete-a-file-share"></a>Fájlmegosztás törlése

Az alábbi mintakód egy fájlmegosztást töröl.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Megosztás törléséhez hívja meg a [ShareClient.delete metódust.](/java/api/com.azure.storage.file.share.shareclient.delete)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Megosztás törléséhez hívja meg a **deleteIfExists** metódust egy **CloudFileShare objektumon.**

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

Rendszerezze a tárolót úgy, hogy a fájlokat alkönyvtárakba rendezi ahelyett, hogy mindegyik a gyökérkönyvtárban található volna.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

A következő kód egy könyvtárat hoz létre a [ShareDirectoryClient.create hívása segítségével.](/java/api/com.azure.storage.file.share.sharedirectoryclient.create) A példa metódus egy értéket `Boolean` ad vissza, amely jelzi, hogy sikeresen létrehozta-e a címtárat.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

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

A címtár törlése egyszerű feladat. Nem törölhet olyan könyvtárat, amely még tartalmaz fájlokat vagy alkönyvtárakat.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

A [ShareDirectoryClient.delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) metódus kivételt ad vissza, ha a könyvtár nem létezik vagy nem üres. Helyezze a törlési **hívást** egy `try/catch` blokkba, és kezelje a kivételt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure-fájlmegosztás fájljainak és könyvtárának számbavétele

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

A fájlok és könyvtárak listájának lehívásához hívja meg a [ShareDirectoryClient.listFilesAndDirectories parancsokat.](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories) A metódus visszaadja azon [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) objektumok listáját, amelyeken iterálhat. A következő kód felsorolja a *dirName* paraméter által megadott könyvtárban található fájlokat és könyvtárakat.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

A fájlok és könyvtárak listájának lehívásához hívja meg a **listFilesAndDirectories** referenciát egy **CloudFileDirectory hivatkozáson.** A metódus a **ListFileItem** objektumok listáját adja vissza, amelyeken iterálhat. Az alábbi kód felsorolja a gyökérkönyvtárban található fájlokat és könyvtárakat.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Fájl feltöltése

Megtudhatja, hogyan tölthet fel fájlokat a helyi tárolóból.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

A következő kód feltölt egy helyi fájlt az Azure File Storage-ba a [ShareFileClient.uploadFromFile metódus hívásával.](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) Az alábbi példa metódus egy értéket `Boolean` ad vissza, amely jelzi, hogy sikeresen feltöltötte-e a megadott fájlt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

A fájl feltöltési könyvtárának hivatkozásához hívja meg a **getRootDirectoryReference** metódust a megosztási objektumon.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Most, hogy már van hivatkozása a megosztás gyökérkönyvtárára, feltölthet rá egy fájlt az alábbi kóddal.

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>Fájl letöltése

Az egyik gyakoribb művelet a fájlok letöltése egy Azure-fájlmegosztásból.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Az alábbi példa letölti a megadott fájlt a *destDir paraméterben* megadott helyi könyvtárba. A példában használt metódus egyedivé teszi a letöltött fájlnevet a dátum és az idő előzetes bekért értékével.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Az alábbi példa letölti a SampleFile.txt és megjeleníti annak tartalmát.

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

Egy másik gyakori Azure Files a fájltörlés.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

A következő kód törli a megadott fájlt. A példa először létrehoz egy [ShareDirectoryClient dirName](/java/api/com.azure.storage.file.share.sharedirectoryclient) paraméter alapján.  Ezután a kód lekért egy [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) ügyfelet a könyvtár-ügyfélből a *fileName paraméter* alapján. Végül a példa metódus a [ShareFileClient.delete metódust](/java/api/com.azure.storage.file.share.sharefileclient.delete) hívja meg a fájl törléséhez.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

A következő kód töröl egy nevű SampleFile.txt, amely egy sampledir nevű **könyvtárban van tárolva.**

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

Ha többet szeretne megtudni más Azure Storage API-król, kövesse az alábbi hivatkozásokat.

- [Azure Java-fejlesztőknek](/azure/developer/java)
- [Javához készült Azure SDK](https://github.com/azure/azure-sdk-for-java)
- [Androidhoz készült Azure SDK](https://github.com/azure/azure-sdk-for-android)
- [A Java SDK-hoz készült Azure-fájlmegosztás ügyféloldali kódtára – referencia](/java/api/overview/azure/storage-file-share-readme)
- [Az Azure Storage-szolgáltatások REST API-ja](/rest/api/storageservices/)
- [Az Azure Storage csapat blogja](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Adatok átvitele az AzCopy Command-Line segédprogrammal](../common/storage-use-azcopy-v10.md)
- [Azure Files-problémák hibaelhárítása – Windows](storage-troubleshoot-windows-file-connection-problems.md)
