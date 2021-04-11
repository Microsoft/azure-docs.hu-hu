---
title: 'Gyors útmutató: az Azure Storage v11 használata a .NET-hez egy üzenetsor kezeléséhez'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a .NET-hez készült Azure Storage ügyféloldali kódtárat üzenetsor létrehozására és az ahhoz tartozó üzenetek hozzáadására. Ezután megtudhatja, hogyan olvashatja és dolgozhatja fel az üzeneteket a várólistából.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 10ab1c487095fe1587a3c665d8a06e0d5cbab7e0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275889"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>Rövid útmutató: a .NET-hez készült Azure Storage SDK v11 használatával kezelheti a várólistákat

Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a .NET-hez készült Azure Storage ügyféloldali kódtár-v11 egy üzenetsor létrehozásához és az üzenetek hozzáadásához. Ezután megtudhatja, hogyan olvashatja és dolgozhatja fel az üzeneteket a várólistából.

> [!NOTE]
> Ez a rövid útmutató az Azure Queue Storage ügyféloldali kódtár örökölt verzióját használja. A legújabb verzió használatának megkezdéséhez tekintse meg a következőt: gyors útmutató [: Azure Queue Storage a .net-hez készült ügyféloldali kódtár](storage-quickstart-queues-dotnet.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ezután töltse le és telepítse a .NET Core 2.0 az operációs rendszernek megfelelő verzióját. Ha Windows rendszert használ, igény szerint telepítheti a Visual Studiót és használhatja a .NET-keretrendszert is. Emellett telepíthet egy, az operációs rendszerrel használható szerkesztőprogramot is.

### <a name="windows"></a>Windows

- A [.NET Core for Windows](https://www.microsoft.com/net/download/windows) vagy a [.NET-keretrendszer](https://www.microsoft.com/net/download/windows) telepítése (a Windows rendszerhez készült Visual Studio része)
- Telepítse [a Windowshoz készült Visual Studio programot](https://www.visualstudio.com/). Ha .NET Core keretrendszert használ, a Visual Studio telepítése nem kötelező.

A .NET Core és a .NET-keretrendszer közötti választással kapcsolatos további információért lásd a [.NET Core és a .NET-keretrendszer közötti, kiszolgálóalkalmazások esetén való választásról](/dotnet/standard/choosing-core-framework-server) szóló részt.

### <a name="linux"></a>Linux

- Telepítse a [.NET Core for Linux keretrendszert](https://www.microsoft.com/net/download/linux).
- Emellett telepítheti [a Visual Studio Code programot](https://www.visualstudio.com/) és a [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is.

### <a name="macos"></a>macOS

- Telepítse a [.NET Core for macOS keretrendszert](https://www.microsoft.com/net/download/macos).
- Emellett telepítheti [a Visual Studio for Mac programot](https://www.visualstudio.com/vs/visual-studio-mac/) is.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt mintaalkalmazás egy egyszerű konzolalkalmazás. A mintaalkalmazást megismerheti a [GitHubon](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

A [git](https://git-scm.com/) használatával letöltheti az alkalmazás egy példányát a fejlesztői környezetbe.

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Ez a parancs klónozott a tárházat a helyi git-mappába. A Visual Studio-megoldás megnyitásához keresse meg a `storage-queues-dotnet-quickstart` mappát, nyissa meg, majd kattintson duplán a elemre `storage-queues-dotnet-quickstart.sln` .

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

Az alkalmazás futtatásához meg kell adnia a tárfiókjához tartozó kapcsolati sztringet. A mintaalkalmazás beolvassa a kapcsolati sztringet egy környezeti változóból, és annak használatával engedélyezi az Azure Storage számára küldött kéréseket.

A kapcsolati sztring másolása után írja azt egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje le a `<yourconnectionstring>` kifejezést a tényleges kapcsolati sztringre:

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Miután hozzáadta a környezeti változót, előfordulhat, hogy újra kell indítania minden futó programot, amelynek szüksége lehet a környezeti változó beolvasására, beleértve a konzolablakot is. Ha például Visual Studiót használt szerkesztőként, indítsa újra a minta futtatása előtt.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

### <a name="macos"></a>macOS

Módosítsa a .bash_profile fájlt, és adja hozzá a környezeti változót:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

## <a name="run-the-sample"></a>Minta futtatása

A minta alkalmazás létrehoz egy várólistát, és felvesz egy üzenetet. Az alkalmazás először az üzenetbe kerül a várólistából való eltávolítás nélkül, majd lekéri az üzenetet, és törli azt a várólistából.

### <a name="windows"></a>Windows

Ha szerkesztőként használja a Visual studiót, kattintson `F5` a Futtatás gombra.

Máskülönben lépjen az alkalmazás könyvtárába, majd futtassa az alkalmazást a `dotnet run` paranccsal.

```
dotnet run
```

### <a name="linux"></a>Linux

Lépjen az alkalmazás könyvtárába, majd futtassa az alkalmazást a `dotnet run` paranccsal.

```
dotnet run
```

### <a name="macos"></a>macOS

Lépjen az alkalmazás könyvtárába, majd futtassa az alkalmazást a `dotnet run` paranccsal.

```
dotnet run
```

A mintaalkalmazás kimenete az alábbihoz hasonló lesz:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="try-parsing-the-connection-string"></a>Kapcsolati sztring elemzése

A minta először ellenőrzi, hogy a környezeti változó tartalmaz-e egy olyan kapcsolódási karakterláncot, amely elemezhető egy olyan objektum létrehozásához, amely [`CloudStorageAccount`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) a Storage-fiókra mutat. Annak ellenőrzéséhez, hogy a kapcsolódási karakterlánc érvényes-e, a minta a [`TryParse`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) metódust használja. Ha `TryParse` a sikeres, inicializálja a `storageAccount` változót, és visszatér `true` .

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Az üzenetsor létrehozása

Először a minta létrehoz egy várólistát, és felvesz egy üzenetet.

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name
// is unique in your storage account.
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Üzenet hozzáadása

Ezután a minta egy üzenetet hoz létre a várólista hátoldalához.

Az üzenetnek olyan formátumúnak kell lennie, amely UTF-8 kódolású XML-kérelemben is szerepelhet, és akár 64 KB méretű is lehet. Ha egy üzenet bináris adatfájlt tartalmaz, azt javasoljuk, hogy Base64 kódolással kódolja az üzenetet.

Alapértelmezés szerint az üzenetekhez tartozó maximális élettartam 7 nap. Megadhatja az üzenet élettartamának pozitív számát.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Ha nem lejáró üzenetet szeretne hozzáadni, használja a `Timespan.FromSeconds(-1)` hívást a következőre: [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) .

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Üzenet betekintése a sorból

A minta bemutatja, hogyan lehet üzeneteket betekintést elküldeni egy várólistából. Amikor betekintést küld egy üzenetbe, elolvashatja az üzenet tartalmát. Az üzenet azonban továbbra is látható marad a többi ügyfél számára, így egy másik ügyfél később is lekérheti és feldolgozhatja az üzenetet.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's
// visibility, so that another client can still retrieve and process it.
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Üzenet eltávolítása az üzenetsorból

A minta azt is bemutatja, hogyan lehet egy üzenetet elválasztani. Amikor elvégez egy üzenetet, a várólista elejéről lekéri az üzenetet, és ideiglenesen láthatatlanná teszi a többi ügyfél számára. Alapértelmezés szerint egy üzenet 30 másodpercig láthatatlan marad. Ebben az időszakban a kód képes feldolgozni az üzenetet. Az üzenet eltávolításának befejezéséhez a feldolgozás után azonnal törölni kell az üzenetet, hogy egy másik ügyfél ne távolítsa el ugyanazt az üzenetet.

Ha a kód hardver vagy szoftver meghibásodása miatt nem tud feldolgozni egy üzenetet, akkor az üzenet ismét láthatóvá válik a láthatatlanság időszakának lejárta után. Egy másik ügyfél lekérheti ugyanazt az üzenetet, és próbálkozzon újra.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A minta törli a várólista törlésével létrehozott erőforrásokat. A várólista törlése a benne található összes üzenetet is törli.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Erőforrások várólistákkal rendelkező .NET-alkalmazások fejlesztéséhez

Tekintse meg ezeket a további forrásokat a .NET-fejlesztéshez az Azure Queue Storage használatával:

### <a name="binaries-and-source-code"></a>Bináris fájlok és forráskód

- A .NET-hez készült [Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage) legújabb verziójához tartozó NuGet-csomagok letöltése
  - [Közös](https://www.nuget.org/packages/microsoft.azure.storage.common/)
  - [Üzenetsorok](https://www.nuget.org/packages/Azure.Storage.Queues/)
- A [.NET ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-net) a GitHubon tekintheti meg.

### <a name="azure-storage-client-library-reference-and-samples"></a>Az Azure Storage ügyféloldali kódtár referenciái és mintái

- A .NET-ügyfél kódtárakkal kapcsolatos további információkért tekintse meg az [Azure Storage ügyféloldali kódtárai](/dotnet/api/overview/azure/storage) című témakört.
- Ismerkedjen meg Queue Storage a .NET ügyféloldali kódtár használatával írt [mintákkal](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) .

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan adhat hozzá üzeneteket egy várólistához, hogyan szúrhat be üzeneteket egy várólistából, és hogyan dolgozza fel az üzeneteket a .NET használatával.

> [!div class="nextstepaction"]
> [Az alkalmazások közötti kommunikáció az Azure Queue Storage](/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
