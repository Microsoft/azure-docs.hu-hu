---
title: A JavaScript (Node.js) használatával kezelheti az adatAzure Data Lake Storage Gen2
description: Az Azure Storage Data Lake ügyféloldali függvénytárának használatával kezelheti a JavaScript-könyvtárakat és-fájlokat olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 678af3e2fb4111593ece0cc2cdf3811cf0e793a8
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774762"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>A Node.js a JavaScript SDK-t használhatja a könyvtárak és fájlok kezeléséhez Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet olyan könyvtárakat és fájlokat, amelyek hierarchikus névtérrel rendelkeznek a Node.js használatával.

A címtárak és fájlok hozzáférés-vezérlési listái (ACL) beszerzésével, beállításával és frissítésével kapcsolatos további információkért lásd: a [JavaScript SDK használata a Node.jsban az ACL-ek Azure Data Lake Storage Gen2-ban való kezeléséhez](data-lake-storage-acl-javascript.md).

[Csomag (node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Példák](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér. Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

- Ha ezt a csomagot egy Node.js alkalmazásban használja, Node.js 8.0.0 vagy újabb verzióra van szüksége.

## <a name="set-up-your-project"></a>A projekt beállítása

Telepítse Data Lake ügyféloldali kódtárat a JavaScripthez egy terminál ablak megnyitásával, majd írja be a következő parancsot.

```javascript
npm install @azure/storage-file-datalake
```

Importálja a `storage-file-datalake` csomagot úgy, hogy a fájl elejére helyezi ezt az utasítást. 

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz 

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre a fiók kulcsa alapján.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```

> [!NOTE]
> Ez az engedélyezési módszer csak Node.js alkalmazásokhoz működik. Ha a kódot egy böngészőben szeretné futtatni, akkor a Azure Active Directory (Azure AD) használatával engedélyezheti.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Kapcsolat Azure Active Directory (Azure AD) használatával

Az [Azure Identity Client Library for js](https://www.npmjs.com/package/@azure/identity) használatával hitelesítheti alkalmazását az Azure ad-vel.

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  Az értékek [lekéréséhez lásd: token beszerzése az Azure ad-ből az ügyfélalkalmazások kéréseinek engedélyezéséhez](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> További példákért tekintse meg az [Azure Identity Client Library for js](https://www.npmjs.com/package/@azure/identity) dokumentációját.

## <a name="create-a-container"></a>Tároló létrehozása

A tároló fájlrendszerként működik a fájlok számára. Hozzon létre egyet egy **FileSystemClient** -példány beszerzésével, majd hívja meg a **FileSystemClient. Create** metódust.

Ez a példa egy nevű tárolót hoz létre `my-file-system` . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát egy **DirectoryClient** -példány beszerzésével, majd hívja meg a **DirectoryClient. Create** metódust.

Ez a példa egy nevű könyvtárat helyez `my-directory` el egy tárolóhoz. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a **DirectoryClient. Rename** metódus meghívásával. Adja meg a kívánt könyvtár elérési útját (a paramétert). 

Ez a példa átnevez egy alkönyvtárat a névre `my-directory-renamed` .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Ez a példa egy nevű könyvtárat helyez át egy nevű `my-directory-renamed` könyvtár alkönyvtárába `my-directory-2` . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Könyvtár törlése

A **DirectoryClient. Delete** metódus meghívásával törölhet egy könyvtárat.

Ez a példa törli a nevű könyvtárat `my-directory` .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Először olvassa el a fájlt. Ez a példa a Node.js `fs` modult használja. Ezután hozzon létre egy fájlt a **FileClient** -példány létrehozásával, majd hívja meg a **FileClient. Create** metódust. Töltsön fel egy fájlt a **FileClient. Append** metódus meghívásával. Ügyeljen arra, hogy a feltöltést a **FileClient. flush** metódus meghívásával végezze el.

Ez a példa egy szövegfájlt tölt fel egy nevű könyvtárba `my-directory` . "

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="download-from-a-directory"></a>Letöltés egy címtárból

Először hozzon létre egy **FileSystemClient** -példányt, amely a letölteni kívánt fájlt jelöli. A fájl olvasásához használja a **FileSystemClient. Read** metódust. Ezután írja be a fájlt. Ebben a példában a Node.js `fs` modult használja. 

> [!NOTE]
> A fájlok letöltésének módja csak Node.js alkalmazásokhoz használható. Ha azt tervezi, hogy futtatja a kódot egy böngészőben, tekintse meg az [Azure Storage-fájl data Lake az ügyféloldali kódtár JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) -fájlhoz című témakört, amelyből megtudhatja, hogyan teheti ezt meg egy böngészőben.

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Ez a példa a nevű könyvtárban található minden könyvtár és fájl nevét kiírja `my-directory` .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Lásd még

- [Csomag (node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Példák](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-java/issues)