---
title: A Java használata a Azure Data Lake Storage Gen2ban tárolt adatkezeléshez
description: A Javához készült Azure Storage-kódtárak segítségével kezelheti az olyan Storage-fiókokban lévő címtárakat és fájlokat, amelyeken engedélyezve van a hierarchikus névtér
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 10debe7bb870ddd9f8711e73ccb4b690d7011b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650186"
---
# <a name="use-java-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>A Java használata a könyvtárak és a fájlok kezeléséhez Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan hozhatja létre és kezelheti a Java-címtárakat és-fájlokat olyan Storage-fiókokban, amelyek hierarchikus névteret használnak.

A címtárak és fájlok hozzáférés-vezérlési listáinak (ACL) beszerzésével, beállításával és frissítésével kapcsolatos további információkért lásd: [használat. A Java-ban Azure Data Lake Storage Gen2 ACL-ek kezelése](data-lake-storage-acl-java.md).

[Csomag (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [API-referenciák](/java/api/overview/azure/storage-file-datalake-readme)  |  [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér. Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

## <a name="set-up-your-project"></a>A projekt beállítása

Első lépésként nyissa meg [ezt a lapot](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) , és keresse meg a Java-könyvtár legújabb verzióját. Ezután nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adjon hozzá egy függőségi elemet, amely hivatkozik erre a verzióra.

Ha az ügyfélalkalmazás Azure Active Directory (Azure AD) használatával történő hitelesítését tervezi, akkor vegyen fel egy függőséget az Azure Secret ügyféloldali függvénytárba. Lásd:  [a titkos ügyféloldali kódtár csomag hozzáadása a projekthez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Ezután adja hozzá ezeket az importálási utasításokat a programkódhoz.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre a fiók kulcsa alapján.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Kapcsolat Azure Active Directory (Azure AD) használatával

A [Javához készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) használatával hitelesítheti alkalmazását az Azure ad-vel.

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  Az értékek [lekéréséhez lásd: token beszerzése az Azure ad-ből az ügyfélalkalmazások kéréseinek engedélyezéséhez](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> További példákért tekintse meg a Java-dokumentációhoz készült [Azure Identity ügyféloldali kódtárat](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

## <a name="create-a-container"></a>Tároló létrehozása

A tároló fájlrendszerként működik a fájlok számára. A **DataLakeServiceClient. createFileSystem** metódus meghívásával létrehozhat egyet.

Ez a példa egy nevű tárolót hoz létre `my-file-system` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát a **DataLakeFileSystemClient. createDirectory** metódus meghívásával.

Ez a példa egy nevű könyvtárat helyez `my-directory` el egy tárolóhoz, majd hozzáadja a nevű alkönyvtárat `my-subdirectory` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a **DataLakeDirectoryClient. Rename** metódus meghívásával. Adja meg a kívánt könyvtár elérési útját (a paramétert). 

Ez a példa átnevez egy alkönyvtárat a névre `my-subdirectory-renamed` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

Ez a példa egy nevű könyvtárat helyez át egy nevű `my-subdirectory-renamed` könyvtár alkönyvtárába `my-directory-2` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Könyvtár törlése

A **DataLakeDirectoryClient. deleteWithResponse** metódus meghívásával törölje a könyvtárat.

Ez a példa törli a nevű könyvtárat `my-directory` .   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Először hozzon létre egy fájlt a célhelyen a **DataLakeFileClient** osztály egy példányának létrehozásával. Töltsön fel egy fájlt a **DataLakeFileClient. Append** metódus meghívásával. Ügyeljen arra, hogy a feltöltést a **DataLakeFileClient. FlushAsync** metódus meghívásával végezze el.

Ez a példa egy szövegfájlt tölt fel egy nevű könyvtárba `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> Ha a fájl mérete nagy, a kódnak több hívást is el kell végeznie a **DataLakeFileClient. Append** metódushoz. Használja helyette a **DataLakeFileClient. uploadFromFile** metódust. Így feltöltheti a teljes fájlt egyetlen hívással. 
>
> Tekintse meg a következő szakaszt egy példához.

## <a name="upload-a-large-file-to-a-directory"></a>Nagyméretű fájl feltöltése egy könyvtárba

A **DataLakeFileClient. uploadFromFile** metódus használatával nagyméretű fájlokat tölthet fel anélkül, hogy több hívást kellene tennie a **DataLakeFileClient. Append** metódushoz.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Letöltés egy címtárból

Először hozzon létre egy **DataLakeFileClient** -példányt, amely a letölteni kívánt fájlt jelöli. A fájl olvasásához használja a **DataLakeFileClient. Read** metódust. A .NET-fájlok feldolgozására szolgáló API-k használatával az adatfolyamból fájlba mentheti a bájtokat. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Ez a példa kinyomtatja a nevű könyvtárban található egyes fájlok nevét `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Lásd még

* [API-referenciadokumentáció](/java/api/overview/azure/storage-file-datalake-readme)
* [Csomag (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)