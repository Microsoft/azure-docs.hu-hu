---
title: 'Gyors útmutató: Azure Blob Storage ügyféloldali kódtára a .NET-hez'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használható az Azure Blob Storage-ügyfél kódtára a .NET-hez tárolók és Blobok (Object) tárolók létrehozásához. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacf9c53715bf42c466aaf6a58938bd681630b9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280649"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Gyors útmutató: Azure Blob Storage ügyféloldali kódtár v11 a .NET-hez

Ismerkedés az Azure Blob Storage .NET-hez készült ügyféloldali kódtár-v11. Az Azure Blob Storage a Microsoft objektum-tárolási megoldás a felhőhöz. Kövesse a csomag telepítésének lépéseit, és próbálja ki például az alapszintű feladatokhoz tartozó kódot. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

> [!NOTE]
> Ez a rövid útmutató az Azure Blob Storage ügyféloldali függvénytárának örökölt verzióját használja. A legújabb verzió használatának megkezdéséhez lásd: gyors útmutató [: Azure Blob Storage ügyféloldali kódtára a .net-hez](storage-quickstart-blobs-dotnet.md).

Használja az Azure Blob Storage .NET-hez készült ügyféloldali kódtárat a következőhöz:

* Tároló létrehozása
* Engedélyek beállítása tárolón
* BLOB létrehozása az Azure Storage-ban
* A blob letöltése a helyi számítógépre
* Egy tároló összes blobjának listázása
* Tároló törlése

További források:

* [API-referenciadokumentáció](/dotnet/api/overview/azure/storage)
* [Könyvtár forráskódja](https://github.com/Azure/azure-storage-net/tree/master/Blob)
* [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)
* [Példák](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure Storage-fiók – [Storage-fiók létrehozása](../common/storage-account-create.md)
* Az operációs rendszer jelenlegi [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) . Győződjön meg arról, hogy az SDK-t és nem a futtatókörnyezetet kapja meg.

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti a projekt előkészítésének folyamatán az Azure Blob Storage .NET-hez készült ügyféloldali kódtár használatával.

### <a name="create-the-project"></a>A projekt létrehozása

Először hozzon létre egy *blob-Gyorsindítás* nevű .net Core-alkalmazást.

1. Egy konzolablak (például cmd, PowerShell vagy bash) használatával `dotnet new` hozzon létre egy új Console-alkalmazást a *blob-Gyorsindítás* néven. Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: *program. cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Váltson az újonnan létrehozott *blob-Gyorsindítás* mappára, és hozza létre az alkalmazást, és ellenőrizze, hogy minden rendben van-e.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

A Build várt kimenetének a következőhöz hasonlóan kell kinéznie:

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

Noha még mindig az alkalmazás könyvtárában van, telepítse az Azure Blob Storage .NET-csomaghoz készült ügyféloldali kódtárat a `dotnet add package` paranccsal.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Nyissa meg a *program. cs* fájlt a szerkesztőben
2. Az `Console.WriteLine` utasítás eltávolítása
3. `using`Irányelvek hozzáadása
4. Hozzon létre egy `ProcessAsync` metódust, ahol a példában a fő kód található.
5. A metódus aszinkron meghívása `ProcessAsync` innen: `Main`

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

Ha a minta alkalmazás az Azure Storage-ba irányuló kérést tesz elérhetővé, akkor azt engedélyezni kell. A kérések engedélyezéséhez adja hozzá a Storage-fiók hitelesítő adatait az alkalmazáshoz kapcsolódási karakterláncként. A tárfiók hitelesítő adatainak megtekintéséhez kövesse az alábbi lépéseket:

1. Navigáljon a [Azure Portal](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Itt megtekintheti a fiókhoz tartozó hozzáférési kulcsokat, valamint az egyes kulcsokhoz tartozó teljes kapcsolati sztringeket.
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

A kapcsolati sztring másolása után írja azt egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje le a- `<yourconnectionstring>` t a tényleges kapcsolatok karakterláncára.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Miután hozzáadta a környezeti változót a Windows rendszerben, el kell indítania a parancssorablak új példányát.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

A környezeti változó hozzáadását követően indítsa újra a futó programokat, amelyeknek olvasnia kell a környezeti változót. A folytatás előtt indítsa el például a fejlesztési környezetet vagy a szerkesztőt.

## <a name="object-model"></a>Objektummodell

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A blob Storage háromféle típusú erőforrást kínál:

* A Storage-fiók.
* Egy tároló a Storage-fiókban
* Egy tárolóban lévő blob

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![BLOB Storage-architektúra ábrája](./media/storage-quickstart-blobs-dotnet/blob1.png)

Használja az alábbi .NET-osztályokat a következő erőforrásokkal való interakcióhoz:

* [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): az `CloudStorageAccount` osztály az Azure Storage-fiókját jelöli. Ezt az osztályt használva engedélyezheti a blob Storage-hoz való hozzáférést a fiók hozzáférési kulcsainak használatával.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): az `CloudBlobClient` osztály a kódban lévő blob Service elérési pontját biztosítja.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): az `CloudBlobContainer` osztály a kódban szereplő BLOB-tárolót jelöli.
* [CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob): az `CloudBlockBlob` objektum a kódban található blokk-blobot jelöli. A blokkblobok önállóan felügyelhető adatblokkokból állnak.

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következőket a .NET-hez készült Azure Blob Storage ügyféloldali kódtára használatával:

   * [Az ügyfél hitelesítése](#authenticate-the-client)
   * [Tároló létrehozása](#create-a-container)
   * [Engedélyek beállítása tárolón](#set-permissions-on-a-container)
   * [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
   * [Tárolóban lévő blobok kilistázása](#list-the-blobs-in-a-container)
   * [Blobok letöltése](#download-blobs)
   * [Tároló törlése](#delete-a-container)

### <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az alábbi kód ellenőrzi, hogy a környezeti változó tartalmaz-e egy olyan kapcsolódási karakterláncot, amely elemezhető egy olyan [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount) objektum létrehozásához, amely a Storage-fiókra mutat. A kapcsolati sztring érvényességének ellenőrzéséhez alkalmazza a [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) metódust. Ha `TryParse` a sikeres, inicializálja a `storageAccount` változót, és visszatér `true` .

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
> A cikkben szereplő további műveletek elvégzéséhez cserélje le a `// ADD OTHER OPERATIONS HERE` fenti kódot az alábbi részekben található kódrészletekre.

### <a name="create-a-container"></a>Tároló létrehozása

Tároló létrehozásához előbb hozza létre a [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) objektum egy példányát, amely a tárfiókban található Blob Storage-ra mutat. Ezután hozza létre a [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) objektum egy példányát, majd a tárolót.

Ebben az esetben a kód meghívja a [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) metódust a tároló létrehozásához. A rendszer egy GUID azonosítót fűz a tároló nevének végéhez, hogy biztosítsa annak egyediségét. Éles környezetben általában a [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) metódus használata ajánlott a tároló létrehozásához, ha még nem létezik.

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

### <a name="set-permissions-on-a-container"></a>Engedélyek beállítása tárolón

Állítsa be a tároló engedélyeit úgy, hogy a tárolóban lévő Blobok nyilvánosak legyenek. A nyilvános blobok névtelenül elérhetők bármely ügyfél által.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

Az alábbi kódrészlet egy objektumra mutató hivatkozást kap, ha `CloudBlockBlob` meghívja a [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) metódust az előző szakaszban létrehozott tárolóban. Ezután a [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) metódus meghívásával feltölti a kijelölt helyi fájlt a blobba. Ez a metódus létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen.

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

A tárolóban lévő Blobok listázása a [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) metódus használatával. Ebben az esetben a tárolóhoz csak egy blob lett hozzáadva, így a listázási művelet csak ezt az egy blobot adja vissza.

Ha túl sok blobot szeretne visszaadni egy hívásban (alapértelmezés szerint több mint 5000), akkor a `ListBlobsSegmentedAsync` metódus a teljes eredményhalmaz egy szegmensét és egy folytatási tokent ad vissza. A blobhalmaz következő szeletének visszaadásához az előző hívás által visszaadott folytatási tokent kell megadnia. Egészen addig, amíg a folytatási token nullértékű nem lesz. A nullértékű folytatási token jelzi, hogy az összes blob le lett kérve. A kód bemutatja, hogyan használható a folytatási jogkivonat az ajánlott eljárások kedvéért.

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

Töltse le a korábban létrehozott blobot a helyi fájlrendszerbe a [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) metódus használatával. A példában szereplő kód hozzáadja a "_DOWNLOADED" utótagot a blob nevéhez, hogy mindkét fájl megjelenjen a helyi fájlrendszerben.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód megtisztítja az alkalmazás által létrehozott erőforrásokat, ha törli a teljes tárolót a [CloudBlobContainer. DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)használatával. Ha szeretné, a helyi fájlokat is törölheti.

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

Ez az alkalmazás létrehoz egy tesztoldalt a helyi *MyDocuments* mappában, és feltölti a blob Storage-ba. A példa ezután felsorolja a tárolóban lévő blobokat, és letölti a fájlt egy új névvel, hogy össze lehessen hasonlítani a régi és az új fájlokat.

Navigáljon az alkalmazás könyvtárába, majd hozza létre és futtassa az alkalmazást.

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

A következő témakörből megtudhatja, hogyan hozhat létre olyan webalkalmazást, amely feltölt egy rendszerképet a blob Storage-ba:

> [!div class="nextstepaction"]
> [Rendszerképek feltöltése és feldolgozása](storage-upload-process-images.md)

* A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
* Ha szeretne megtekinteni egy olyan mintaalkalmazást, amely a Windowshoz készült Visual Studióból helyezhető üzembe, tekintse meg [az Azure Blob Storage-et használó .NET fényképgaléria-mintawebalkalmazást](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).