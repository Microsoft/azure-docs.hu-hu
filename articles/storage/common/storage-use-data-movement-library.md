---
title: Adatok átvitele a .NET-hez készült adatáthelyezési függvénytárral
titleSuffix: Azure Storage
description: Az adatátviteli függvénytár segítségével áthelyezheti vagy átmásolhatja az adatokat a blob-és fájl-tartalmakba. Adatok másolása az Azure Storage-ba helyi fájlokból, illetve adatok másolása a Storage-fiókokba vagy azok között. Egyszerűen migrálhatja adatait az Azure Storage-ba.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ec35a651d4d3ef9e0877463329a654bc7491f4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023414"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Adatok átvitele az adatátviteli kódtárral

Az Azure Storage adatátviteli könyvtára egy többplatformos nyílt forráskódú kódtár, amely nagy teljesítményű blobok és fájlok feltöltésére, letöltésére és másolására szolgál. Az adatáthelyezési függvénytár olyan kényelmes metódusokat biztosít, amelyek nem érhetők el az Azure Storage .NET-hez készült ügyféloldali kódtáraban. Ezekkel a módszerekkel beállíthatja a párhuzamos műveletek számát, nyomon követheti a folyamat előrehaladását, egyszerűen folytathatja a megszakított átvitelt, és még sok más lehetőséget is.

Ez a könyvtár a .NET Core-t is használja, ami azt jelenti, hogy Windows, Linux és macOS rendszerű .NET-alkalmazások létrehozásakor használható. Ha többet szeretne megtudni a .NET Core-ról, tekintse meg a [.net Core dokumentációját](https://dotnet.github.io/). Ez a könyvtár a Windows hagyományos .NET-keretrendszerbeli alkalmazásaihoz is használható.

Ebből a dokumentumból megtudhatja, hogyan hozhat létre Windows, Linux és macOS rendszeren futó .NET Core Console-alkalmazást, és hogyan hajtja végre az alábbi eseteket:

- Fájlok és könyvtárak feltöltése a Blob Storageba.
- A párhuzamos műveletek számának meghatározása az adatok átvitele során.
- Az adatátviteli folyamat nyomon követése.
- A megszakított adatátvitel folytatása.
- Fájl másolása URL-címről Blob Storagera.
- Másolás Blob Storageról Blob Storagera.

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Storage-fiók](storage-account-create.md)

## <a name="setup"></a>Beállítás

1. A .net Core telepítéséhez látogasson el a [.net Core telepítési útmutatóba](https://www.microsoft.com/net/core) . A környezet kiválasztásakor válassza a parancssori kapcsolót.
2. A parancssorból hozzon létre egy könyvtárat a projekthez. Navigáljon a címtárba, majd írja be a következőt: `dotnet new console -o <sample-project-name>` C# Console-projekt létrehozása.
3. Nyissa meg ezt a könyvtárat a Visual Studio Code-ban. Ez a lépés a parancssorban gyorsan elvégezhető a Windows beírásával `code .` .
4. Telepítse a [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a Visual Studio Code Piactérről. Indítsa újra a Visual Studio Code-ot.
5. Ekkor két kérdést kell látnia. Az egyik a "szükséges eszközök létrehozása és hibakeresése". Kattintson az Igen gombra. Egy másik kérdés a feloldatlan függőségek visszaállítása. Kattintson a visszaállítás gombra.
6. A `launch.json` alatt módosítsa `.vscode` a külső terminál használata konzolként lehetőséget. Ennek a beállításnak a következőképpen kell beolvasnia `"console": "externalTerminal"`
7. A Visual Studio Code lehetővé teszi a .NET Core-alkalmazások hibakeresését. `F5`Futtassa az alkalmazást, és ellenőrizze, hogy a telepítés működik-e. A "Hello World!" kifejezésnek kell megjelennie kinyomtatva a konzolra.

## <a name="add-the-data-movement-library-to-your-project"></a>Adatátviteli függvénytár hozzáadása a projekthez

1. Adja hozzá az adatátviteli függvénytár legújabb verzióját a `dependencies` fájl szakaszához `<project-name>.csproj` . Az írás időpontjában ez a verzió a következő lesz: `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. A projekt visszaállítására figyelmeztető üzenetnek kell megjelennie. Kattintson a visszaállítás gombra. A projektet a parancssorból is visszaállíthatja úgy, hogy beírja a parancsot a `dotnet restore` projekt könyvtára gyökerébe.

Módosítás `<project-name>.csproj` :

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

## <a name="set-up-the-skeleton-of-your-application"></a>Az alkalmazás csontvázának beállítása

Az első lépés az alkalmazás "csontváz" kódjának beállítása. Ez a kód a Storage-fiók nevét és a fiók kulcsát kéri, és ezeket a hitelesítő adatokat használja egy objektum létrehozásához `CloudStorageAccount` . Ez az objektum a Storage-fiókkal való interakcióra szolgál az összes adatátviteli helyzetben. A kód arra is felszólítja, hogy válassza ki a végrehajtani kívánt átviteli művelet típusát.

Módosítás `Program.cs` :

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

## <a name="upload-a-local-file-to-a-blob"></a>Helyi fájl feltöltése egy blobba

Adja hozzá a metódusokat és a következőt `GetSourcePath` `GetBlob` `Program.cs` :

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

Ez a kód egy helyi fájl elérési útját, egy új vagy egy meglévő tároló nevét, valamint egy új blob nevét kéri tőlünk. A `TransferManager.UploadAsync` metódus ezen információk alapján végzi a feltöltést.

`F5`Az alkalmazás futtatásának megtalálása. A feltöltési folyamat ellenőrzéséhez tekintse meg a Storage-fiókját a [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>A párhuzamos műveletek számának beállítása

Az adatátviteli függvénytár által kínált egyik funkció lehetővé teszi a párhuzamos műveletek számának beállítását az adatátvitel sebességének növelése érdekében. Alapértelmezés szerint az adatáthelyezési függvénytár beállítja a párhuzamos műveletek számát a gépen lévő magok számával.

Ne feledje, hogy az alacsony sávszélességű környezetekben számos párhuzamos művelet megterhelheti a hálózati kapcsolatát, és ténylegesen megakadályozhatja a műveletek teljes befejezését. Ezzel a beállítással kell kísérletezni annak meghatározásához, hogy mi működik a legjobban az elérhető hálózati sávszélesség alapján.

Vegyünk fel egy olyan kódot, amely lehetővé teszi a párhuzamos műveletek számának beállítását. Vegyünk fel olyan kódot is, amely az átvitel befejezéséhez szükséges időt veszi igénybe.

Metódus hozzáadása `SetNumberOfParallelOperations` a következőhöz `Program.cs` :

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

A `ExecuteChoice` használandó módszer módosítása `SetNumberOfParallelOperations` :

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

A `TransferLocalFileToAzureBlob` metódus módosítása időzítő használatára:

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

## <a name="track-transfer-progress"></a>Adatátviteli folyamat nyomon követése

Annak ismerete, hogy mennyi ideig tartott az adatok átvitele. Az átvitel *közbeni* előrehaladást azonban még jobbá teheti az átvitel során. Ennek a forgatókönyvnek a megvalósításához létre kell hoznia egy `TransferContext` objektumot. Az `TransferContext` objektum két formában érhető el: `SingleTransferContext` és `DirectoryTransferContext` . A korábbi egy egyetlen fájl átadására szolgál, és az utóbbi a fájlok könyvtárának továbbítására szolgál.

Adja hozzá a metódusokat és a következőt `GetSingleTransferContext` `GetDirectoryTransferContext` `Program.cs` :

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

A `TransferLocalFileToAzureBlob` használandó módszer módosítása `GetSingleTransferContext` :

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

Az adatátviteli függvénytár által kínált másik kényelmi funkció lehetővé teszi a megszakított átvitel folytatását. Vegyünk fel egy olyan kódot, amely lehetővé teszi, hogy az átvitelt átmenetileg megszakítsa a beírásával `c` , majd folytassa az átvitelt 3 másodperccel később.

Módosítás `TransferLocalFileToAzureBlob` :

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

Eddig az `checkpoint` érték mindig a következőre lett beállítva: `null` . Most, ha megszakítottuk az átvitelt, beolvasjuk az átvitel utolsó ellenőrzőpontját, majd ezt az új ellenőrzőpontot használjuk az adatátviteli kontextusban.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Helyi könyvtár átvitele a blob Storage-ba

Kiábrándító lenne, ha az adatátviteli függvénytár egyszerre csak egy fájlt tud átvinni. Szerencsére ez nem így van. Az adatáthelyezési függvénytár lehetővé teszi a fájlok és az alkönyvtárak könyvtárának átadását. Vegyünk fel egy olyan kódot, amely lehetővé teszi számunkra, hogy ezt megtegyük.

Először adja hozzá a metódust a következőhöz `GetBlobDirectory` `Program.cs` :

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

Ezután módosítsa a `TransferLocalDirectoryToAzureBlobDirectory` következőket:

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

A metódus és az egyetlen fájl feltöltési módszere között van néhány különbség. Most `TransferManager.UploadDirectoryAsync` a és a `getDirectoryTransferContext` korábban létrehozott metódust használjuk. Emellett a feltöltési művelethez is megadhatunk egy `options` értéket, ami lehetővé teszi számunkra, hogy a feltöltéshez alkönyvtárakat is szeretnénk foglalni.

## <a name="copy-a-file-from-url-to-a-blob"></a>Fájl másolása az URL-címről egy blobba

Most vegyünk fel egy olyan kódot, amely lehetővé teszi, hogy egy fájlt egy URL-címről egy Azure-Blobba másoljon.

Módosítás `TransferUrlToAzureBlob` :

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

Ennek a funkciónak az egyik fontos felhasználási esete, ha egy másik felhőalapú szolgáltatásból (pl. AWS) az Azure-ba kell áthelyeznie az adatátvitelt. Ha olyan URL-címmel rendelkezik, amely hozzáférést biztosít az erőforráshoz, egyszerűen áthelyezheti az erőforrást az Azure-Blobokra az `TransferManager.CopyAsync` metódus használatával. Ez a metódus egy új logikai paramétert is bevezet. Ha ezt a paramétert állítja be, az `true` azt jelzi, hogy aszinkron kiszolgálóoldali másolást szeretnénk végrehajtani. Ha ezt a paramétert úgy állítja be, hogy `false` szinkron másolást jelezzen, az erőforrást először a helyi gépre töltse le, majd feltölti az Azure-blobba. A szinkron másolás azonban jelenleg csak az egyik Azure Storage-erőforrásból a másikba való másoláshoz érhető el.

## <a name="copy-a-blob"></a>BLOB másolása

Az adatátviteli függvénytár által egyedileg biztosított másik funkció az egyik Azure Storage-erőforrásból a másikba való másolás lehetősége.

Módosítás `TransferAzureBlobToAzureBlob` :

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

Ebben a példában a értékben a logikai paramétert használjuk, hogy `TransferManager.CopyAsync` `false` jelezze, szinkron másolatot szeretne készíteni. Ez azt jelenti, hogy az erőforrást először a helyi gépre tölti le a rendszer, majd feltölti az Azure-Blobba. A szinkron másolási lehetőséggel biztosíthatja, hogy a másolási művelet konzisztens sebességgel legyen. Ezzel szemben az aszinkron kiszolgálóoldali másolás sebessége a kiszolgálón elérhető hálózati sávszélességtől függ, ami ingadozhat. A szinkron másolás azonban további kimenő forgalmat eredményezhet az aszinkron másoláshoz képest. Az ajánlott módszer az, ha szinkron másolást használ egy olyan Azure-beli virtuális gépen, amely ugyanabban a régióban található, mint a forrásként szolgáló Storage-fiók, hogy elkerülje a kimenő forgalom költségeit.

Az adatáthelyezési alkalmazás már befejeződött. [A teljes kód minta elérhető a githubon](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Következő lépések

[Az Azure Storage adatáthelyezési függvénytárának dokumentációja](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
