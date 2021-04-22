---
title: 'Rövid útmutató: Azure Queue Storage kódtár v12 - .NET'
description: Megtudhatja, hogyan használhatja Azure Queue Storage .NET-hez való 12-es vagy újabb ügyféloldali kódtárát üzenetsor létrehozására és üzenetek üzenetsorhoz való hozzáadására. Ezután megtudhatja, hogyan olvashatja és törölheti az üzeneteket az üzenetsorból. Azt is megtudhatja, hogyan törölhet egy üzenetsort.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: f0b03f8a0a9e3aa277ec940c8e65064ef0411ccc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867618"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Rövid útmutató: Azure Queue Storage .NET-hez való v12-es ügyféloldali kódtár létrehozása

A .NET-hez Azure Queue Storage ügyféloldali kódtár 12-es verziójának első lépések. Azure Queue Storage egy nagy számú üzenet tárolására szolgáló szolgáltatás későbbi lekéréshez és feldolgozáshoz. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz szükséges példakódot.

Használja a Azure Queue Storage .NET-hez való 12-es vagy esetben található ügyféloldali kódtárat a következőre:

- Üzenetsor létrehozása
- Üzenetek hozzáadása üzenetsorhoz
- Betekintés az üzenetsor üzeneteibe
- Üzenet frissítése egy üzenetsorban
- Üzenetek fogadása üzenetsorból
- Üzenetek törlése egy üzenetsorból
- Üzenetsor törlése

További források:

- [API-referenciadokumentáció](/dotnet/api/azure.storage.queues)
- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
- [Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
- [Példák](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés [– hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/)
- Azure Storage-fiók [– tárfiók létrehozása](../common/storage-account-create.md)
- Aktuális [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) az operációs rendszer számára. Ne a futásidőt, csak az SDK-t szerezze be.

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti egy projekt előkészítésén a .NET-hez készült Azure Queue Storage ügyféloldali kódtárával való munkához.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy nevű .NET `QueuesQuickstartV12` Core-alkalmazást.

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) az paranccsal hozzon létre egy új konzolalkalmazást `dotnet new` `QueuesQuickstartV12` néven. Ez a parancs egy egyszerű "hello world" C#-projektet hoz létre egyetlen, nevű `Program.cs` forrásfájllal.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Váltson az újonnan létrehozott `QueuesQuickstartV12` könyvtárra.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>A csomag telepítése

Az alkalmazás könyvtárában maradva telepítse a Azure Queue Storage .NET-csomaghoz az `dotnet add package` paranccsal.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projektkönyvtárból:

1. Nyissa meg `Program.cs` a fájlt a szerkesztőben
1. A utasítás `Console.WriteLine("Hello, World");` eltávolítása
1. `using`Direktíva hozzáadása
1. A `Main` metódusdeklaráció frissítése az [aszinkron kód támogatásához](/dotnet/csharp/whats-new/csharp-7#async-main)

A kód a következő:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. Az üzenetsor-üzenetek mérete legfeljebb 64 KB lehet. Az üzenetsorok több millió üzenetet tartalmazhatnak, a tárfiókok maximális kapacitásának korlátjig. Az üzenetsorokat gyakran használják hátralékos feladatok létrehozásához aszinkron feldolgozáshoz. Queue Storage háromféle erőforrást kínál:

- A tárfiók
- Üzenetsor a tárfiókban
- Üzenetsoron belüli üzenetek

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![A Queue Storage architektúrájának ábrája](./media/storage-queues-introduction/queue1.png)

Az alábbi .NET-osztályokkal használhatja ezeket az erőforrásokat:

- [`QueueServiceClient`](/dotnet/api/azure.storage.queues.queueserviceclient): A `QueueServiceClient` lehetővé teszi a tárfiókban lévő összes üzenetsor kezelését.
- [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient): A osztály lehetővé teszi az egyes üzenetsorok és üzeneteik `QueueClient` kezelését és kezelését.
- [`QueueMessage`](/dotnet/api/azure.storage.queues.models.queuemessage): A `QueueMessage` osztály az üzenetsoron való híváskor visszaadott [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) egyes objektumokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek a következő műveleteket mutatják be a .NET-hez Azure Queue Storage ügyféloldali kódtárával:

- [A kapcsolati sztring lekérése](#get-the-connection-string)
- [Üzenetsor létrehozása](#create-a-queue)
- [Üzenetek hozzáadása üzenetsorhoz](#add-messages-to-a-queue)
- [Betekintés az üzenetsor üzeneteibe](#peek-at-messages-in-a-queue)
- [Üzenet frissítése egy üzenetsorban](#update-a-message-in-a-queue)
- [Üzenetek fogadása üzenetsorból](#receive-messages-from-a-queue)
- [Üzenetek törlése egy üzenetsorból](#delete-messages-from-a-queue)
- [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A következő kód lekéri a tárfiók kapcsolati sztringét. A kapcsolati sztringet a rendszer a Tárterület kapcsolati sztring konfigurálása szakaszban létrehozott környezeti [változóban](#configure-your-storage-connection-string) tárolja.

Adja hozzá ezt a kódot a `Main` metódushoz:

```csharp
Console.WriteLine("Azure Queue Storage client library v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Üzenetsor létrehozása

Döntse el az új üzenetsor nevét. Az alábbi kód egy GUID-értéket fűz hozzá az üzenetsor nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> Az üzenetsorok nevei csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3–63 karakter hosszúságúnak kell lennie. További információ: [Üzenetsorok és metaadatok elnevezése.](/rest/api/storageservices/naming-queues-and-metadata)

Hozza létre a osztály egy [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) példányát. Ezután hívja meg a [`CreateAsync`](/dotnet/api/azure.storage.queues.queueclient.createasync) metódust az üzenetsor létrehozásához a tárfiókban.

Adja hozzá ezt a kódot a metódus `Main` véghez:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása üzenetsorhoz

Az alábbi kódrészlet aszinkron módon aszinkron módon ad üzeneteket az üzenetsorhoz a metódus [`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) hívásával. A hívásból visszaadott [`SendReceipt`](/dotnet/api/azure.storage.queues.models.sendreceipt) et is `SendMessageAsync` menti. A nyugta az üzenet frissítésére lesz használva a program későbbi részen.

Adja hozzá ezt a kódot a metódus `Main` véghez:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Betekintés az üzenetsor üzeneteibe

A metódus hívásával bepillantás az üzenetsor [`PeekMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) üzeneteibe. Ez a metódus egy vagy több üzenetet ad vissza az üzenetsor elejéről, de nem módosítja az üzenet láthatóságát.

Adja hozzá ezt a kódot a metódus `Main` véghez:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Üzenetsor üzenetének frissítése

Frissítse egy üzenet tartalmát a metódus [`UpdateMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) hívásával. Ez a metódus módosíthatja az üzenetek láthatósági időkorlátját és tartalmát. Az üzenet tartalmának UTF-8 kódolású sztringnek kell lennie, amely legfeljebb 64 KB méretű lehet. Az üzenet új tartalma mellett adja meg a kódban korábban mentett `SendReceipt` értékeket is. Az `SendReceipt` értékek azonosítják, hogy melyik üzenetet kell frissíteni.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása üzenetsorból

Töltse le a korábban hozzáadott üzeneteket a metódus [`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) hívásával.

Adja hozzá ezt a kódot a metódus `Main` véghez:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Üzenetek törlése egy üzenetsorból

A feldolgozás után törölje az üzeneteket az üzenetsorból. Ebben az esetben a feldolgozás csak az üzenetet jeleníti meg a konzolon.

Az alkalmazás a hívással szünetelteti a felhasználói bevitelt, mielőtt feldolgozza és törli `Console.ReadLine` az üzeneteket. A fiókban [Azure Portal,](https://portal.azure.com) hogy az erőforrások megfelelően, a törlésük előtt létrejöttek-e. A explicit módon nem törölt üzenetek végül ismét láthatóvá válnak az üzenetsorban, hogy egy másik lehetőség legyen azok feldolgozására.

Adja hozzá ezt a kódot a metódus `Main` véghez:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Üzenetsor törlése

A következő kód törli az alkalmazás által létrehozott erőforrásokat az üzenetsor a metódussal való [`DeleteAsync`](/dotnet/api/azure.storage.queues.queueclient.deleteasync) törlésével.

Adja hozzá ezt a kódot a metódus `Main` véghez:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre és ad hozzá egy Azure-üzenetsorhoz. A kód listázza az üzenetsorban lévő üzeneteket, majd lekéri és törli őket, mielőtt végül töröl egy üzenetsort.

A konzolablakban keresse meg az alkalmazás könyvtárát, majd készítse el és futtassa az alkalmazást.

```console
dotnet build
```

```console
dotnet run
```

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Queue Storage client library v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Amikor az alkalmazás szünetel, mielőtt üzeneteket kap, ellenőrizze a tárfiókot a [Azure Portal.](https://portal.azure.com) Ellenőrizze, hogy az üzenetek az üzenetsorban vannak-e.

Nyomja le `Enter` a billentyűt az üzenetek fogadására és törlésére. Amikor a rendszer kéri, nyomja le újra a billentyűt az üzenetsor `Enter` törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre üzenetsort, és hogyan adhat hozzá üzeneteket aszinkron .NET-kóddal. Ezután megtanulta, hogyan lehet betekintésbe, lekérni és törölni az üzeneteket. Végül megtanulta, hogyan törölhet üzenetsort.

Oktatóanyagok, minták, rövid útmutatók és egyéb dokumentáció:

> [!div class="nextstepaction"]
> [Azure .NET- és .NET Core-fejlesztőknek](/dotnet/azure/)

- További információ: [Azure Storage-kódtárak a .NET-hez.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage)
- További Azure Queue Storage a [.NET-mintákhoz Azure Queue Storage ügyféloldali kódtárban.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)
- A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro) ismertető szakaszt.
