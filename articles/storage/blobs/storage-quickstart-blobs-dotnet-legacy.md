---
title: 'Rövid útmutató: Az Azure Blob Storage .NET-hez való ügyféloldali kódtára'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure Blob Storage .NET-hez használható ügyféloldali kódtárát egy tároló és egy blob létrehozására a Blob (objektum) tárolóban. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 631b01dadacf81d66c3f42dc1401d2cf492316b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869238"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Rövid útmutató: Azure Blob Storage ügyféloldali kódtár v11 for .NET

Első lépések a Azure Blob Storage .NET-hez Azure Blob Storage ügyféloldali kódtárával. Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. Kövesse a csomag telepítésének lépéseit, és próbálja ki az alapszintű feladatokhoz szükséges példakódot. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

> [!NOTE]
> Ez a rövid útmutató az Azure Blob Storage ügyféloldali kódtárának örökölt verzióját használja. A legújabb verzió használatának első lépésekhez lásd: Rövid útmutató: Azure Blob Storage ügyféloldali [kódtár v12 for .NET.](storage-quickstart-blobs-dotnet.md)

Használja a Azure Blob Storage .NET-hez használható ügyféloldali kódtárát a következőre:

* Tároló létrehozása
* Tároló engedélyeinek beállítása
* Blob létrehozása az Azure Storage-ban
* A blob letöltése a helyi számítógépre
* A tárolóban lévő összes blob felsorolása
* Tároló törlése

További források:

* [API-referenciadokumentáció](/dotnet/api/overview/azure/storage)
* [Kódtár forráskódja](https://github.com/Azure/azure-storage-net/tree/master/Blob)
* [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)
* [Példák](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/)
* Azure Storage-fiók [– tárfiók létrehozása](../common/storage-account-create.md)
* Aktuális [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) az operációs rendszer számára. Ne a futásidőt, csak az SDK-t szerezze be.

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti egy projekt előkészítésén a .NET-hez készült Azure Blob Storage ügyféloldali kódtárával való munkához.

### <a name="create-the-project"></a>A projekt létrehozása

Először hozzon létre egy *blob-quickstart* nevű .NET Core-alkalmazást.

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) az paranccsal hozzon létre egy blob-quickstart nevű új `dotnet new` *konzolalkalmazást.* Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Váltson az újonnan létrehozott *blob-quickstart* mappára, és készítse el az alkalmazást annak ellenőrzéséhez, hogy minden rendben van-e.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

A build várt kimenetének a következő módon kell kinéznie:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>A csomag telepítése

Az alkalmazás könyvtárában maradva telepítse a Azure Blob Storage .NET-csomaghoz ügyféloldali kódtárát az `dotnet add package` paranccsal.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projektkönyvtárból:

1. Nyissa meg *a Program.cs* fájlt a szerkesztőben
2. A utasítás `Console.WriteLine` eltávolítása
3. `using`Direktíva hozzáadása
4. Hozzon létre egy metódust, amelyben a példa fő kódja `ProcessAsync` található
5. Aszinkron módon hívja meg a `ProcessAsync` metódust a következőből: `Main`

A kód a következő:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static async Task Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            await ProcessAsync();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

Amikor a mintaalkalmazás kérést tesz az Azure Storage-hoz, azt engedélyezni kell. A kérelem hitelesítéshez adja hozzá a tárfiók hitelesítő adatait az alkalmazáshoz kapcsolati sztringként. A tárfiók hitelesítő adatainak megtekintéséhez kövesse az alábbi lépéseket:

1. Lépjen a [Azure Portal.](https://portal.azure.com)
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Itt megtekintheti a fiókhoz tartozó hozzáférési kulcsokat, valamint az egyes kulcsokhoz tartozó teljes kapcsolati sztringeket.
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

A kapcsolati sztring másolása után írja azt egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje `<yourconnectionstring>` le a helyére a tényleges kapcsolati sztringet.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Miután hozzáadta a környezeti változót a Windowsban, el kell kezdenie a parancsablak egy új példányát.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

A környezeti változó hozzáadása után indítsa újra az összes olyan futó programot, amely beolvassa a környezeti változót. A folytatás előtt például indítsa újra a fejlesztési környezetet vagy a szerkesztőt.

## <a name="object-model"></a>Objektummodell

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A Blob Storage háromféle erőforrástípust kínál:

* A tárfiók.
* Egy tároló a tárfiókban
* Blob egy tárolóban

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![A Blob Storage architektúráját szemléltető diagram](./media/storage-quickstart-blobs-dotnet/blob1.png)

Az alábbi .NET-osztályokkal használhatja ezeket az erőforrásokat:

* [CloudStorageAccount:](/dotnet/api/microsoft.azure.storage.cloudstorageaccount)A `CloudStorageAccount` osztály az Ön Azure Storage-fiókját jelöli. Ezzel az osztálysal engedélyezheti a Blob Storage-hoz való hozzáférést a fiók hozzáférési kulcsával.
* [CloudBlobClient:](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)A osztály hozzáférési pontot `CloudBlobClient` biztosít a Blob service kódban.
* [CloudBlobContainer:](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)A `CloudBlobContainer` osztály egy blobtárolót képvisel a kódban.
* [CloudBlockBlob:](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)A `CloudBlockBlob` objektum egy blokkblobot képvisel a kódban. A blokkblobok önállóan felügyelhető adatblokkokból állnak.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek megmutatják, hogyan hajthatja végre a következőket a .NET-hez elérhető Azure Blob Storage ügyféloldali kódtárával:

   * [Az ügyfél hitelesítése](#authenticate-the-client)
   * [Tároló létrehozása](#create-a-container)
   * [Tároló engedélyeinek beállítása](#set-permissions-on-a-container)
   * [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
   * [Tárolóban lévő blobok kilistázása](#list-the-blobs-in-a-container)
   * [Blobok letöltése](#download-blobs)
   * [Tároló törlése](#delete-a-container)

### <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az alábbi kód ellenőrzi, hogy a környezeti változó tartalmaz-e kapcsolati sztringet, amely elemezve létrehozhat egy [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount) objektumot, amely a tárfiókra mutat. A kapcsolati sztring érvényességének ellenőrzéséhez alkalmazza a [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) metódust. Ha `TryParse` a sikeres, inicializálja a változót, `storageAccount` és a értéket adja `true` vissza.

Adja hozzá ezt a kódot a `ProcessAsync` metódushoz:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'AZURE_STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> A cikkben található további műveletek végrehajtásához a fenti kódban cserélje le a helyére a következő szakaszokban található `// ADD OTHER OPERATIONS HERE` kódrészleteket.

### <a name="create-a-container"></a>Tároló létrehozása

Tároló létrehozásához előbb hozza létre a [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) objektum egy példányát, amely a tárfiókban található Blob Storage-ra mutat. Ezután hozza létre a [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) objektum egy példányát, majd a tárolót.

Ebben az esetben a kód a [CreateAsync metódust](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) hívja meg a tároló létrehozásához. A rendszer egy GUID azonosítót fűz a tároló nevének végéhez, hogy biztosítsa annak egyediségét. Éles környezetben gyakran előnyösebb a [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) metódussal létrehozni egy tárolót, ha az még nem létezik.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Tároló engedélyeinek beállítása

Állítsa be a tároló engedélyeinek beállítását úgy, hogy a tárolóban lévő blobok nyilvánosak. A nyilvános blobok névtelenül elérhetők bármely ügyfél által.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

Az alábbi kódrészlet lehívja a `CloudBlockBlob` [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) metódust az előző szakaszban létrehozott tárolón egy objektumra való hivatkozással. Ezután feltölti a kiválasztott helyi fájlt a blobba az [UploadFromFileAsync metódus hívásával.](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) Ez a metódus létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

Listzza a tárolóban lévő blobokat a [ListBlobsSegmentedAsync metódussal.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) Ebben az esetben csak egy blob lett hozzáadva a tárolóhoz, így a listázási művelet csak ezt az egy blobot adja vissza.

Ha túl sok blobot kell visszaadni egy hívásban (alapértelmezés szerint több mint 5000), akkor a metódus a teljes eredményhalmaz egy szegmensét és egy folytatási tokent `ListBlobsSegmentedAsync` ad vissza. A blobhalmaz következő szeletének visszaadásához az előző hívás által visszaadott folytatási tokent kell megadnia. Egészen addig, amíg a folytatási token nullértékű nem lesz. A nullértékű folytatási token jelzi, hogy az összes blob le lett kérve. A kód bemutatja, hogyan használhatja a folytatási tokent az ajánlott eljárások érdekében.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null.

```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a helyi fájlrendszerbe [a DownloadToFileAsync metódussal.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) A példakód egy "_DOWNLOADED" utótagot ad a blob nevéhez, így mindkét fájlt láthatja a helyi fájlrendszerben.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Tároló törlése

Az alábbi kód törli az alkalmazás által létrehozott erőforrásokat a teljes tároló [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)használatával való törlésével. Ha szeretné, a helyi fájlokat is törölheti.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy tesztfájlt a *helyi MyDocuments mappában,* és feltölti azt a Blob Storage-ba. A példa ezután listázza a tárolóban lévő blobokat, és letölti a fájlt egy új névvel, hogy összehasonlítsa a régi és az új fájlokat.

Navigáljon az alkalmazás könyvtárához, majd készítse el és futtassa az alkalmazást.

```console
dotnet build
```

```console
dotnet run
```

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

Az **Enter** billentyű lenyomása után az alkalmazás törli a Storage-tárolót és a fájlokat. Mielőtt törölné ezeket, ellenőrizze a két fájlt a *MyDocuments* mappában. Ha megnyitja őket, láthatja, hogy megegyeznek. Másolja ki a blob URL-címét a konzolablakból, és másolja be egy böngészőbe a blob tartalmának a megtekintéséhez.

A fájlok ellenőrzése után nyomja le bármelyik billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez.

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megismerkedett a blobok a .NET használatával való fel- és letöltésével, valamint kilistázásával.

Ha meg szeretne tudni egy képet a Blob Storage-be feltöltő webalkalmazás létrehozásáról, folytassa a következővel:

> [!div class="nextstepaction"]
> [Rendszerképek feltöltése és feldolgozása](storage-upload-process-images.md)

* A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro) ismertető szakaszt.
* Ha szeretne megtekinteni egy olyan mintaalkalmazást, amely a Windowshoz készült Visual Studióból helyezhető üzembe, tekintse meg [az Azure Blob Storage-et használó .NET fényképgaléria-mintawebalkalmazást](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).