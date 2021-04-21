---
title: Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel | Microsoft Docs
description: Ismerje meg, hogyan fejleszthet olyan .NET-alkalmazásokat és -szolgáltatásokat, amelyek Azure Files az adatok tárolására.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c00f001ae3cba9420a137a42f9f696619584d50
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817376"
---
# <a name="develop-for-azure-files-with-net"></a>Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Ismerje meg az adatok tárolására Azure Files [.NET-alkalmazások](storage-files-introduction.md) fejlesztésének alapjait. Ez a cikk bemutatja, hogyan hozhat létre egy egyszerű konzolalkalmazást a következők létrehozására a .NET és a Azure Files:

- Le kell szereznie egy fájl tartalmát.
- Egy fájlmegosztás maximális méretének vagy kvótának a beállítása.
- Közös hozzáférésű jogosultság jogosultsága (SAS) létrehozása egy fájlhoz.
- Fájl másolása másik fájlba egy tárfiókon belül.
- Fájl másolása blobba egy tárfiókon belül.
- Pillanatkép készítése egy fájlmegosztásról.
- Fájl visszaállítása megosztási pillanatképből.
- Használja az Azure Storage-metrikákat a hibaelhárításhoz.

További információ a Azure Files: [Mi a Azure Files?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>A .NET API-k ismertetése

Az Azure Files két széleskörű megközelítést nyújt az ügyfélalkalmazásokhoz: az SMB protokollt és a REST-et. A .NET-en belül az `System.IO` és az `Azure.Storage.Files.Shares` API kivonatot ad ezeknek a megközelítésnek.

API | A következő esetekben használja | Megjegyzések
----|-------------|------
[System.IO](/dotnet/api/system.io) | Az alkalmazás/alkalmazásnak: <ul><li>Fájlokat kell olvasnia/írnia az SMB használatával</li><li>Olyan eszközön fut, amely a 445-ös porton keresztül éri el az Azure Files-fiókot.</li><li>Nem kell kezelnie a fájlmegosztás rendszergazdai beállításait.</li></ul> | Az SMB-Azure Files megvalósított fájl I/O általában megegyezik bármely hálózati fájlmegosztással vagy helyi tárolóeszközön található I/O-val. A .NET számos funkcióját, köztük a fájl I/O-t a Konzolalkalmazás [oktatóanyagban lehet](/dotnet/csharp/tutorials/console-teleprompter) bemutatni.
[Azure.Storage.Files.Shares](/dotnet/api/azure.storage.files.shares) | Az alkalmazás/alkalmazásnak: <ul><li>Tűzfal- vagy internetszolgáltatói Azure Files miatt nem lehet hozzáférni az SMB-hez a 445-ös porton</li><li>Rendszergazdai funkciókat igényel, például a fájlmegosztás kvótájának beállítását vagy közös hozzáférésű jogosultságkód létrehozásának lehetőségét.</li></ul> | Ez a cikk bemutatja, hogyan használható a fájl I/O-jában a REST az SMB helyett, és hogyan használható a `Azure.Storage.Files.Shares` fájlmegosztás.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>A konzolalkalmazás létrehozása és az összeállítás elérése

Az ügyféloldali Azure Files bármilyen típusú .NET-alkalmazásban használhatja. Ilyen alkalmazások például az Azure-felhő, a web, az asztali és a mobilalkalmazások. Ebben az útmutatóban az egyszerűség kedvéért létrehozunk egy konzolalkalmazást.

Hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban. A következő lépések azt mutatják be, hogyan hozhat létre konzolalkalmazást a 2019 Visual Studio ban. A lépések a Visual Studio más verziói esetén is hasonlók.

1. Indítsa Visual Studio, és válassza **az Új projekt létrehozása lehetőséget.**
1. Az **Új projekt létrehozása mezőben válassza a** Console App (.NET-keretrendszer) (Konzolalkalmazás **(.NET-keretrendszer)** lehetőséget a C#-hoz, majd kattintson a Next (Tovább) **gombra.**
1. Az **Új projekt konfigurálása mezőben adja** meg az alkalmazás nevét, majd válassza a Létrehozás **lehetőséget.**

Adja hozzá a cikkben található összes példakódot a `Program` osztályhoz a *Program.cs fájlban.*

## <a name="use-nuget-to-install-the-required-packages"></a>A szükséges csomagok telepítése a NuGettel

Tekintse meg ezeket a csomagokat a projektben:

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

- [Azure core kódtár a .NET-hez:](https://www.nuget.org/packages/Azure.Core/)Ez a csomag az Azure-ügyfél folyamatának implementációja.
- [Azure Storage Blob .NET ügyféloldali](https://www.nuget.org/packages/Azure.Storage.Blobs/)kódtára: Ez a csomag programozott hozzáférést biztosít a tárfiókban lévő blob-erőforrásokhoz.
- [Azure Storage Files ügyféloldali kódtár a](https://www.nuget.org/packages/Azure.Storage.Files.Shares/).NET-hez: Ez a csomag programozott hozzáférést biztosít a tárfiókban lévő fájlerőforrásokhoz.
- [A Konfigurációkezelő .NET-hez:](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/)Ez a csomag egy osztályt biztosít az értékek konfigurációs fájlban való tárolásához és leolvasásához.

A nuget használatával beszerezheti a csomagokat. Kövesse az alábbi lépéseket:

1. A **Megoldáskezelő** kattintson a jobb gombbal a projektre, és válassza a **Manage NuGet Packages (NuGet-csomagok kezelése) lehetőséget.**
1. A **NuGet-Csomagkezelő** válassza a Tallózás **lehetőséget.** Ezután keresse meg és válassza az **Azure.Core** lehetőséget, majd válassza a **Telepítés lehetőséget.**

   Ez a lépés telepíti a csomagot és annak függőségeit.

1. Keresse meg és telepítse az alábbi csomagokat:

   - **Azure.Storage.Blobs**
   - **Azure.Storage.Files.Shares**
   - **System.Configuration.ConfigurationManager**

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

- [Microsoft Azure Storage .NET közös](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)kódtára: Ez a csomag programozott hozzáférést biztosít a tárfiók gyakori erőforrásaihoz.
- [Microsoft Azure Storage Blob kódtár a .NET-hez:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)Ez a csomag programozott hozzáférést biztosít a tárfiókban lévő blob-erőforrásokhoz.
- [Microsoft Azure Storage .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)elérhető fájlkönyvtár: Ez a csomag programozott hozzáférést biztosít a tárfiókban található fájlerőforrásokhoz.
- [Microsoft Azure Konfigurációkezelő](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/).NET-kódtárhoz: Ez a csomag osztályt biztosít a kapcsolati sztringek konfigurációs fájlban való elemezéshez, bárhol is fut az alkalmazás.

A nuget használatával beszerezheti a csomagokat. Kövesse az alábbi lépéseket:

1. A **Megoldáskezelő** kattintson a jobb gombbal a projektre, és válassza a **Manage NuGet Packages (NuGet-csomagok kezelése) lehetőséget.**
1. A **NuGet-Csomagkezelő** válassza a Tallózás **lehetőséget.** Ezután keresse meg és válassza ki a **Microsoft.Azure.Storage.Blob** szolgáltatásokat, majd válassza a **Telepítés lehetőséget.**

   Ez a lépés telepíti a csomagot és annak függőségeit.
1. Keresse meg és telepítse az alábbi csomagokat:

   - **Microsoft.Azure.Storage.Common**
   - **Microsoft.Azure.Storage.File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Mentse a tárfiók hitelesítő adatait a App.config fájlba

Ezután mentse a hitelesítő adatokat  a projektApp.configfájljában. A **Megoldáskezelő** kattintson duplán a fájlra, és szerkessze úgy, hogy az az alábbi `App.config` példához hasonlítsa.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Cserélje `myaccount` le a helyére a tárfiók nevét, a `mykey` helyére pedig a tárfiók kulcsát.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Cserélje `myaccount` le a helyére a tárfiók nevét, a `StorageAccountKeyEndingIn==` helyére pedig a tárfiók kulcsát.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> Az Azurite Storage Emulator jelenleg nem támogatja a Azure Files. A kapcsolati sztringnek egy Azure-tárfiókot kell célozni a felhőben, hogy működjön Azure Files.

## <a name="add-using-directives"></a>Hozzáadás irányelvekkel

A **Megoldáskezelő** nyissa meg a *Program.cs* fájlt, és adja hozzá a következő using irányelveket a fájl tetejéhez.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>A fájlmegosztás szoftveres elérése

A *Program.cs fájlban* adja hozzá a következő kódot a fájlmegosztás programozott módon való eléréséhez.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Az alábbi metódus létrehoz egy fájlmegosztást, ha még nem létezik. A metódus egy [ShareClient](/dotnet/api/azure.storage.files.shares.shareclient) objektum kapcsolati sztringből való létrehozásával kezdődik. A minta ezután megkísérli letölteni a korábban létrehozott fájlt. Hívja meg ezt a metódust a `Main()` metódusból.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Ezután adja hozzá a következő tartalmat a metódushoz a fenti kód után `Main()` a kapcsolati sztring lekérése érdekében. Ez a kód lekért egy hivatkozást a korábban létrehozott fájlra, és kiírta annak tartalmát.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

A kimenet megtekintéséhez futtassa a konzolalkalmazást.

---

## <a name="set-the-maximum-size-for-a-file-share"></a>Egy fájlmegosztás maximális méretének beállítása

Az ügyféloldali kódtár 5.x Azure Files kezdve beállíthatja a fájlmegosztások kvótáját (maximális méretét). Azt is ellenőrizheti, hogy aktuálisan mennyi adatot tárol a fájlmegosztás.

Egy megosztás kvótája korlátozza a megosztáson tárolt fájlok teljes méretét. Ha a megosztásban lévő fájlok teljes mérete meghaladja a kvótát, az ügyfelek nem növelhetik a meglévő fájlok méretét. Az ügyfelek nem hozhatnak létre új fájlokat, kivéve, ha üresek.

Az alábbi példa bemutatja, hogyan ellenőrizheti egy megosztás aktuális kihasználását, és hogyan adhat meg hozzá kvótát.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Közös hozzáférésű jogosultságkód létrehozása egy fájlhoz vagy fájlmegosztáshoz

Az Azure Files-ügyféloldali kódtár 5.x-es verziójától kezdve létrehozhat egy közös hozzáférésű jogosultságjelet (SAS) egy fájlmegosztáshoz vagy egy különálló fájlhoz.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Az alábbi példa metódus egy SAS-t ad vissza a megadott megosztásban található fájlhoz.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Egy fájlmegosztáson tárolt hozzáférési szabályzatot is létrehozhat a közös hozzáférésű jogosultságok kezeléséhez. Javasoljuk, hogy hozzon létre egy tárolt hozzáférési szabályzatot, mert ez lehetővé teszi az SAS visszavonását, ha az biztonsága sérül. Az alábbi példa egy tárolt hozzáférési szabályzatot hoz létre egy megosztáson. A példa ezt a szabályzatot használja a megosztásban található fájl SAS-korlátozásának megszabadalmának biztosítanak.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

---

A közös hozzáférésű jogosultságok létrehozásáról és használatával kapcsolatos további információkért lásd: A közös hozzáférésű jogosultság [aláírásai működése.](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)

## <a name="copy-files"></a>Fájlok másolása

Az Azure Files ügyféloldali kódtár 5.x-es verziójától kezdve a fájlokat átmásolhatja egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba.

Az AzCopy használatával átmásolhat egy fájlt egy másikba, vagy másolhat egy blobot egy fájlba, vagy más módon. Lásd: [Az AzCopy – első lépések.](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

> [!NOTE]
> Ha egy blobot másol egy fájlba vagy egy fájlt egy blobba, akkor is közös hozzáférésű jogosultságkóddal (SAS) kell engedélyeznie a forrásobjektumhoz való hozzáférést, ha a másolás tárfiókon belül történik.

### <a name="copy-a-file-to-another-file"></a>Fájl másolása másik fájlba

Az alábbi példa megosztáson belül másol át egy fájlt egy másikba. A [másoláshoz megosztott kulcsos](/rest/api/storageservices/authorize-with-shared-key) hitelesítést is használhat, mivel ez a művelet átmásolja a fájlokat ugyanabban a tárfiókban.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

---

### <a name="copy-a-file-to-a-blob"></a>Fájl másolása blobba

Az alábbi példa létrehoz egy fájlt, és átmásolja egy ugyanabban a tárfiókban található blobba. A példa létrehoz a forrásfájlhoz egy SAS-t, amellyel a szolgáltatás engedélyezi a forrásfájlhoz való hozzáférést a másolási művelet alatt.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

---

Ugyanígy másolhat blobokat fájlokba. Ha a forrásobjektum egy blob, hozzon létre egy SAS-t, amely engedélyezi a blobhoz való hozzáférést a másolási művelet során.

## <a name="share-snapshots"></a>Pillanatképek megosztása

A Azure Files ügyféloldali kódtár 8.5-ös verziójától kezdve létrehozhat megosztási pillanatképet. Ezekből felsorolást is készíthet, tallózhat köztük, és törölheti is a megosztási pillanatképeket. A létrehozás után a megosztási pillanatképek csak olvashatók.

### <a name="create-share-snapshots"></a>Megosztási pillanatképek létrehozása

Az alábbi példával létrehozhat egy fájlmegosztási pillanatképet.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása

Az alábbi példa egy megosztás pillanatképét sorolja fel.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>Fájlok és könyvtárak listása megosztási pillanatképek között

Az alábbi példa a megosztási pillanatképek fájljait és könyvtárát böngészi.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>Fájlmegosztások vagy fájlok visszaállítása megosztási pillanatképből

A fájlmegosztás pillanatképének készítése lehetővé teszi az egyes fájlok vagy a teljes fájlmegosztás helyreállítását.

A fájlmegosztási pillanatképekből a fájlok a pillanatkép lekérdezésével állíthatók vissza. Ezután lekérhet egy adott megosztási pillanatképhez tartozó fájlt. Ezzel a verzióval közvetlenül olvashatja vagy visszaállíthatja a fájlt.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

---

### <a name="delete-share-snapshots"></a>Megosztási pillanatképek törlése

Az alábbi példával törölhet egy fájlmegosztási pillanatképet.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Hibaelhárítás Azure Files metrikák használatával<a name="troubleshooting-azure-files-using-metrics"></a>

Azure Storage Analytics támogatja a metrikákat a Azure Files. A metrikai adatok segítségével nyomon követheti a kéréseket, és diagnosztizálhatja a problémákat.

A metrikákat a következő Azure Files engedélyezheti: [Azure Portal .](https://portal.azure.com) A metrikákat programozott módon is engedélyezheti, ha a Set [File Service Properties](/rest/api/storageservices/set-file-service-properties) (Fájlszolgáltatás tulajdonságainak beállítása) műveletet a REST API vagy az ügyféloldali kódtár egyik Azure Files hívja meg.

Az alábbi példakód bemutatja, hogyan engedélyezheti a metrikákat a .NET ügyféloldali kódtár Azure Files.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Először adja hozzá a következő irányelveket a `using` *Program.cs fájlhoz* a fentiekben hozzáadott irányelvekkel együtt:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Bár az Azure Blobs, az Azure Tables és az Azure Queues a megosztott típust használja a névtérben, a Azure Files a saját típusát használja, a típust a `ServiceProperties` `Microsoft.Azure.Storage.Shared.Protocol` `FileServiceProperties` `Microsoft.Azure.Storage.File.Protocol` névtérben. Ahhoz azonban, hogy a következő kód lefordítva legyen, mindkét névtérre hivatkozni kell a kódból.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

---

Ha bármilyen problémába ütközik, tekintse meg a Windows [Azure Files hibaelhárítását bemutató témakört.](storage-troubleshoot-windows-file-connection-problems.md)

## <a name="next-steps"></a>Következő lépések

További információt a Azure Files az alábbi forrásokban:

### <a name="conceptual-articles-and-videos"></a>Elméleti cikkek és videók

- [Azure Files: zökkenőmentes felhőalapú SMB-fájlrendszer Windows és Linux rendszerekhez](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Az Azure Files használata Linux rendszeren](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage-eszköztámogatás

- [Bevezetés az AzCopy használatába](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [Azure Files-problémák hibaelhárítása Windowson](./storage-troubleshoot-windows-file-connection-problems.md)

### <a name="reference"></a>Referencia

- [Azure Storage API-k a .NET-hez](/dotnet/api/overview/azure/storage)
- [A File szolgáltatás REST API-ja](/rest/api/storageservices/File-Service-REST-API)