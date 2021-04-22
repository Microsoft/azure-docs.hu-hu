---
title: Adatok átvitele a .NET adatátviteli kódtárával
titleSuffix: Azure Storage
description: Az adatátviteli kódtár használatával blob- és fájltartalmakba vagy fájlokba másolhat vagy másolhat adatokat. Adatok másolása az Azure Storage-ba helyi fájlokból, vagy adatok másolása tárfiókon belül vagy között. Könnyedén mihelystheti az adatokat az Azure Storage-ba.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f87379f48f82757916aef0fa0d358835f48cb9a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875934"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Adatok átvitele az adatátviteli kódtárral

Az Azure Storage adatátviteli kódtár egy platformfüggetlen nyílt forráskódú kódtár, amely blobok és fájlok nagy teljesítményű feltöltésére, letöltésére és másolására lett tervezve. Az adatátviteli kódtár olyan kényelmes módszereket biztosít, amelyek nem érhetők el az Azure Storage .NET-hez elérhető ügyféloldali kódtárában. Ezek a metódusok lehetővé teszi a párhuzamos műveletek számának beállítását, az átviteli folyamat nyomon követését, a megszakított átvitelek egyszerű folytatását és még sok más műveletet.

Ez a kódtár a .NET Core-t is használja, ami azt jelenti, hogy a .NET-alkalmazások Windows, Linux és macOS rendszerekre való fejlesztésekor is használható. A .NET Core-ról a .NET Core dokumentációjában talál [további információt.](https://dotnet.github.io/) Ez a kódtár a windowsos hagyományos .NET-keretrendszer is működik.

Ez a dokumentum bemutatja, hogyan hozhat létre Egy Windows, Linux és macOS rendszeren futó .NET Core-konzolalkalmazást, és a következő forgatókönyveket végezheti el:

- Fájlok és könyvtárak feltöltése a Blob Storage.
- Határozza meg a párhuzamos műveletek számát az adatok átvitele során.
- Az adatátviteli folyamat nyomon követése.
- Folytathatja a megszakított adatátvitelt.
- Fájl másolása URL-címből Blob Storage.
- Másolja a Blob Storage a Blob Storage.

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio Code](https://code.visualstudio.com/)
- [Egy Azure Storage-fiók](storage-account-create.md)

## <a name="setup"></a>Beállítás

1. A [telepítéshez látogasson el a .NET Core](https://dotnet.microsoft.com/download) telepítési .NET Core SDK. A környezet kiválasztásakor válassza a parancssori lehetőséget.
2. A parancssorból hozzon létre egy könyvtárat a projekthez. Lépjen ebbe a könyvtárba, majd írja be `dotnet new console -o <sample-project-name>` a parancsot egy C#-konzolprojekt létrehozásához.
3. Nyissa meg ezt a könyvtárat Visual Studio Code-ban. Ezt a lépést gyorsan, a parancssorból is el lehet végezve, ha a `code .` Windowsba beírja a következőt: .
4. Telepítse a [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a Visual Studio Code Marketplace-ről. Indítsa újra Visual Studio Code-kódot.
5. Ezen a ponton két üzenetnek kell látsza. Az egyik a "buildhez és hibakereséshez szükséges eszközök" hozzáadása. Kattintson az "Igen" gombra. Egy másik kérés a feloldatlan függőségek visszaállítására. Kattintson a Visszaállítás gombra.
6. Módosítsa `launch.json` a `.vscode` gombra a alatt, hogy külső terminált használjon konzolként. Ennek a beállításnak a következőnek kell lennie: `"console": "externalTerminal"`
7. Visual Studio Code lehetővé teszi a .NET Core-alkalmazások hibakeresését. Nyomja `F5` le az gombra az alkalmazás futtatásához, és ellenőrizze, hogy a beállítás működik-e. A "Hello World!" a konzolon.

## <a name="add-the-data-movement-library-to-your-project"></a>Az adatátviteli kódtár hozzáadása a projekthez

1. Adja hozzá az adatátviteli kódtár legújabb verzióját `dependencies` a fájl `<project-name>.csproj` szakaszához. A cikk írásakor ez a verzió a következő lenne: `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Megjelenik egy üzenet a projekt visszaállításához. Kattintson a Visszaállítás gombra. A projektet a parancssorból is visszaállíthatja, ha beírja a parancsot `dotnet restore` a projektkönyvtár gyökérkönyvtárába.

`<project-name>.csproj`Módosítás:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Az alkalmazás vázának beállítása

Először be kell álltatjuk az alkalmazás "vázának" kódját. Ez a kód kéri a Tárfiók nevét és a fiókkulcsot, és ezeket a hitelesítő adatokat használja egy objektum `CloudStorageAccount` létrehozásához. Ez az objektum a Storage-fiókkal való interakcióra használható minden átviteli forgatókönyvben. A kód arra is felszólítja, hogy válassza ki az átviteli művelet típusát, amit végre szeretne hajtani.

`Program.cs`Módosítás:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Helyi fájl feltöltése blobba

Adja hozzá a és `GetSourcePath` a `GetBlob` metódust a `Program.cs` metódushoz:

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Módosítsa a `TransferLocalFileToAzureBlob` metódust:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Ez a kód egy helyi fájl elérési útját, egy új vagy meglévő tároló nevét és egy új blob nevét kéri. A `TransferManager.UploadAsync` metódus ezen információk alapján végzi el a feltöltést.

Az alkalmazás futtatásához nyomja le a `F5` et. A feltöltést úgy ellenőrizheti, hogy megtekinti a Tárfiókot a következő [Microsoft Azure Storage Explorer.](https://storageexplorer.com/)

## <a name="set-the-number-of-parallel-operations"></a>Párhuzamos műveletek számának beállítása

Az adatátviteli kódtár egyik funkciója a párhuzamos műveletek számának beállítása az adatátvitel átviteli sebességének növeléséhez. Alapértelmezés szerint az adatátviteli kódtár a párhuzamos műveletek számát 8*-ra állítja a számítógépen található magok számára.

Ne feledje, hogy alacsony sávszélességű környezetben számos párhuzamos művelet túlterhelheti a hálózati kapcsolatot, és ténylegesen megakadályozhatja a műveletek teljes befejezését. Ezzel a beállítással kísérletezni kell annak meghatározásához, hogy mi működik a legjobban az elérhető hálózati sávszélesség alapján.

Adjunk hozzá néhány kódot, amely lehetővé teszi a párhuzamos műveletek számának beállítását. Adjuk hozzá azt a kódot is, amely az átvitel befejezéséhez szükséges időt adja meg.

Adjon hozzá egy metódust a `SetNumberOfParallelOperations` `Program.cs` metódushoz:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Módosítsa a `ExecuteChoice` metódust a `SetNumberOfParallelOperations` használatára:

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Módosítsa a `TransferLocalFileToAzureBlob` metódust időzítő használatára:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Átviteli folyamat nyomon követése

Hasznos tudni, hogy mennyi ideig tartott az adatok átvitele. Az átvitel előrehaladásának az átadási művelet során *való* nyomon látnia azonban még jobb lenne. Ehhez létre kell hoznunk egy `TransferContext` objektumot. A `TransferContext` objektum két formában létezik: `SingleTransferContext` és `DirectoryTransferContext` . Az előbbi egyetlen fájl átvitelére, az utóbbi pedig fájlok könyvtárának átvitelére való.

Adja hozzá a és `GetSingleTransferContext` a `GetDirectoryTransferContext` metódust a `Program.cs` metódushoz:

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Módosítsa a `TransferLocalFileToAzureBlob` metódust a `GetSingleTransferContext` használatára:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Megszakított átvitel folytatása

Az adatátviteli kódtár egy másik kényelmes funkciója a megszakított átvitel folytatása. Adjunk hozzá egy kódot, amely lehetővé teszi az átvitel ideiglenes megszakítását a beírásával, majd az `c` átvitel folytatása 3 másodperccel később.

`TransferLocalFileToAzureBlob`Módosítás:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Eddig az érték mindig értékre `checkpoint` lett `null` állítva. Most, ha megszakítjuk az átvitelt, lekérjük az átvitel utolsó ellenőrzőpontját, majd ezt az új ellenőrzőpontot használjuk az átviteli környezetben.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Helyi könyvtár átvitele a Blob Storage-be

Rossz lenne, ha az adatátviteli kódtár egyszerre csak egy fájlt tudna áthozni. Szerencsére ez nem így van. Az adatátviteli kódtár lehetővé teszi fájlok és alkönyvtárak könyvtárának átvitelét. Adjunk hozzá néhány kódot, amely éppen ezt teszi lehetővé.

Először adja hozzá a metódust a `GetBlobDirectory` `Program.cs` metódushoz:

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Ezután módosítsa a `TransferLocalDirectoryToAzureBlobDirectory` következőt:

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Ez a metódus és az egyetlen fájl feltöltésének módszere között van néhány különbség. Most a és a `TransferManager.UploadDirectoryAsync` metódust `getDirectoryTransferContext` használjuk, amit korábban hoztunk létre. Emellett most értéket adtunk meg a feltöltési művelethez, amely lehetővé teszi annak jelzését, hogy alkönyvtárakat szeretnénk a `options` feltöltésbe foglalni.

## <a name="copy-a-file-from-url-to-a-blob"></a>Fájl másolása URL-címből blobba

Most adjunk hozzá egy kódot, amely lehetővé teszi egy fájl másolását egy URL-címből egy Azure-blobba.

`TransferUrlToAzureBlob`Módosítás:

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Ennek a funkciónak az egyik fontos esete az, amikor adatokat kell áthelyezni egy másik felhőszolgáltatásból (például az AWS-ről) az Azure-ba. Ha van egy URL-címe, amely hozzáférést biztosít az erőforráshoz, az erőforrást egyszerűen áthelyezheti az Azure Blobsba a `TransferManager.CopyAsync` metódussal. Ez a metódus egy új logikai paramétert is bevezet. A paraméter beállítása `true` jelzi, hogy aszinkron kiszolgálóoldali másolást szeretnénk. Ha ezt a paramétert úgy adja meg, hogy szinkron másolatot jelezzen, ami azt jelenti, hogy az erőforrás először a helyi gépre lesz letöltve, majd fel lesz töltve az `false` Azure Blobba. A szinkron másolás azonban jelenleg csak az egyik Azure Storage-erőforrásból egy másikba másolható.

## <a name="copy-a-blob"></a>Blob másolása

Az adatátviteli kódtár egy másik egyedi funkciója az egyik Azure Storage-erőforrásból a másikba való másolás képessége.

`TransferAzureBlobToAzureBlob`Módosítás:

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, CopyMethod.ServiceSideAsyncCopy, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Ebben a példában a logikai paramétert a értékére állítva jelezjük, hogy `TransferManager.CopyAsync` `false` szinkron másolatot szeretnénk tenni. Ez azt jelenti, hogy az erőforrás először a helyi gépre lesz letöltve, majd fel lesz töltve az Azure Blobba. A szinkron másolási lehetőséggel biztosíthatja, hogy a másolási művelet konzisztens sebességgel rendelkezik. Ezzel szemben az aszinkron kiszolgálóoldali másolat sebessége a kiszolgálón elérhető hálózati sávszélességtől függ, amely ingadozhat. A szinkron másolatok azonban az aszinkron másoláshoz képest további ki- és visszacsatoló költségeket okozhatnak. Az ajánlott módszer a szinkron másolat használata egy olyan Azure-beli virtuális gépen, amely ugyanabban a régióban van, mint a forrásként használt tárfiók, így elkerülheti a ki- és behozás költségeit.

Az adatátmozgatási alkalmazás elkészült. [A teljes kódminta elérhető a GitHubon.](https://github.com/azure-samples/storage-dotnet-data-movement-library-app)

## <a name="next-steps"></a>Következő lépések

[Az Azure Storage adatátviteli kódtárának referenciadokumentációja.](https://azure.github.io/azure-storage-net-data-movement)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
