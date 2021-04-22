---
title: 'Rövid útmutató: Üzenetsor kezelése az Azure Storage v11 for .NET használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure Storage .NET-hez használható ügyféloldali kódtárát üzenetsor létrehozására és üzenetek hozzáadására. A következő lépés az üzenetek olvasása és feldolgozása az üzenetsorból.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: df2f3593f904c5c6c6c9af0ae635a2e152f9eb82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871074"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>Rövid útmutató: Üzenetsor kezelése az Azure Storage SDK v11 for .NET használatával

Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure Storage .NET-hez használható ügyféloldali kódtárának v11-et egy üzenetsor létrehozásához és üzenetek hozzáadásához. A következő lépés az üzenetek olvasása és feldolgozása az üzenetsorból.

> [!NOTE]
> Ez a rövid útmutató az ügyféloldali kódtár Azure Queue Storage verzióját használja. A legújabb verzióval való első lépésekért lásd: Rövid útmutató: Azure Queue Storage [.NET-es ügyféloldali kódtár 12-es verziójának létrehozása.](storage-quickstart-queues-dotnet.md)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ezután töltse le és telepítse a .NET Core 2.0 az operációs rendszernek megfelelő verzióját. Ha Windows rendszert használ, igény szerint telepítheti a Visual Studiót és használhatja a .NET-keretrendszert is. Emellett telepíthet egy, az operációs rendszerrel használható szerkesztőprogramot is.

### <a name="windows"></a>Windows

- Telepítse .NET Core SDK Windowshoz vagy a .NET-keretrendszer [(a](https://dotnet.microsoft.com/download/dotnet-framework) [Windowshoz](https://dotnet.microsoft.com/download) Visual Studio tartalmazza)
- Telepítse [a Windowshoz készült Visual Studio programot](https://www.visualstudio.com/). Ha .NET Core keretrendszert használ, a Visual Studio telepítése nem kötelező.

A .NET Core és a .NET-keretrendszer közötti választással kapcsolatos további információért lásd a [.NET Core és a .NET-keretrendszer közötti, kiszolgálóalkalmazások esetén való választásról](/dotnet/standard/choosing-core-framework-server) szóló részt.

### <a name="linux"></a>Linux

- Linux [.NET Core SDK telepítése](/dotnet/core/install/linux)
- Emellett telepítheti [a Visual Studio Code programot](https://www.visualstudio.com/) és a [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is.

### <a name="macos"></a>macOS

- Telepítse [a .NET Core SDK macOS-hez.](https://dotnet.microsoft.com/download)
- Emellett telepítheti [a Visual Studio for Mac programot](https://www.visualstudio.com/vs/visual-studio-mac/) is.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt mintaalkalmazás egy egyszerű konzolalkalmazás. A mintaalkalmazást megismerheti a [GitHubon](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

A [Git](https://git-scm.com/) használatával töltse le az alkalmazás egy példányát a fejlesztési környezetbe.

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Ez a parancs klónozza az adattárat a helyi Git-mappába. A Visual Studio megnyitásához keresse meg a mappát, nyissa meg, majd kattintson duplán `storage-queues-dotnet-quickstart` `storage-queues-dotnet-quickstart.sln` a elemre.

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

A mintaalkalmazás létrehoz egy üzenetsort, és hozzáad egy üzenetet. Az alkalmazás először bepillant az üzenetbe anélkül, hogy eltávolítaná az üzenetsorból, majd lekéri az üzenetet, és törli az üzenetsorból.

### <a name="windows"></a>Windows

Ha szerkesztőként Visual Studio, a billentyű lenyomásával `F5` futtathatja a következőt: .

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

A minta először ellenőrzi, hogy a környezeti változó tartalmaz-e kapcsolati sztringet, amely elemezve létrehoz egy objektumot, amely a [`CloudStorageAccount`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) tárfiókra mutat. A kapcsolati sztring érvényességének ellenőrzéshez a minta a metódust [`TryParse`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) használja. Ha `TryParse` a sikeres, inicializálja a változót, `storageAccount` és a értéket adja `true` vissza.

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

Először a minta létrehoz egy üzenetsort, és hozzáad egy üzenetet.

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name
// is unique in your storage account.
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Üzenet hozzáadása

Ezután a minta hozzáad egy üzenetet az üzenetsor hátához.

Az üzeneteknek UTF-8 kódolású XML-kérések formátumában kell szerepelniük, és legfeljebb 64 KB méretűnek kell lennie. Ha egy üzenet bináris adatokat tartalmaz, javasoljuk, hogy base64 kódolással kódolja az üzenetet.

Alapértelmezés szerint az üzenetek maximális időtartamának beállítása 7 nap. Az üzenethez bármilyen pozitív számot megadhat az üzenethez.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Ha nem jár le, használja a következőt `Timespan.FromSeconds(-1)` a hívásában: [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) .

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Üzenet betekintése az üzenetsorból

A minta bemutatja, hogyan lehet betekintés egy üzenetsorból. Amikor bepillant egy üzenetbe, elolvashatja az üzenet tartalmát. Az üzenet azonban látható marad más ügyfelek számára, így egy másik ügyfél később lekérheti és feldolgozhatja az üzenetet.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's
// visibility, so that another client can still retrieve and process it.
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Üzenet eltávolítása az üzenetsorból

A minta azt is bemutatja, hogyan lehet üzeneteket a sorból kivenni. Amikor leküld egy üzenetet, lekéri az üzenetet az üzenetsor elejéről, és ideiglenesen láthatatlanná teszi más ügyfelek számára. Alapértelmezés szerint az üzenetek 30 másodpercig láthatatlanok maradnak. Ez idő alatt a kód feldolgozhatja az üzenetet. Az üzenet törlésének befejezéséhez közvetlenül a feldolgozás után törölnie kell az üzenetet, hogy egy másik ügyfél ne törölje ugyanazt az üzenetet.

Ha a kód hardver- vagy szoftverhiba miatt nem tudja feldolgozni az üzenetet, akkor az üzenet a láthatósági időszak megszakadása után ismét láthatóvá válik. Egy másik ügyfél lekérheti ugyanazt az üzenetet, és újra próbálkozhat.

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

A minta törli a létrehozott erőforrásokat az üzenetsor törlésével. Az üzenetsor törlésével az üzenetek is törölve vannak.

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

## <a name="resources-for-developing-net-applications-with-queues"></a>Üzenetsorokkal használható .NET-alkalmazások fejlesztéséhez használható források

Az alábbi források a .NET-fejlesztéshez a következő Azure Queue Storage:

### <a name="binaries-and-source-code"></a>Bináris fájlok és forráskód

- Töltse le a NuGet-csomagokat az Azure Storage .NET-hez elérhető [ügyféloldali kódtárának legújabb verziójához](/dotnet/api/overview/azure/storage)
  - [Közös](https://www.nuget.org/packages/microsoft.azure.storage.common/)
  - [Üzenetsorok](https://www.nuget.org/packages/Azure.Storage.Queues/)
- A [.NET ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-net) a GitHubon tekintheti meg.

### <a name="azure-storage-client-library-reference-and-samples"></a>Az Azure Storage ügyféloldali kódtárának referenciái és mintái

- A [.NET-ügyfélkódtárakkal](/dotnet/api/overview/azure/storage) kapcsolatos további információkért tekintse meg az Azure Storage .NET-ügyfélkódtárait.
- Megismerheti Queue Storage .NET [ügyféloldali](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) kódtár használatával írt mintákat.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan adhat hozzá üzeneteket egy üzenetsorhoz, hogyan lehet üzeneteket bepillantásni egy üzenetsorba, illetve hogyan lehet az üzeneteket a .NET használatával levenni és feldolgozni.

> [!div class="nextstepaction"]
> [Alkalmazások közötti kommunikáció Azure Queue Storage](/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro) ismertető szakaszt.
