---
title: Ismerkedés a üzenetsor-tárolással a Visual Studio használatával (Cloud Services)
description: Az Azure üzenetsor-tárolás használatának első lépései a Visual Studióban lévő Cloud Service-projektekben a Visual Studio csatlakoztatott szolgáltatásainak használatával való csatlakozást követően
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 94f248edfebd6c6fedb78a54eee220c0ef38b4ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95545864"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Ismerkedés az Azure Queue Storage-hez és a Visual Studióhoz kapcsolódó szolgáltatásokkal (felhőszolgáltatás-projektek)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogyan kezdheti el az Azure üzenetsor-tárolás használatát a Visual Studióban, miután létrehozott vagy hivatkozott egy Azure Storage-fiókot a Cloud Services-projektben a Visual Studio **csatlakoztatott szolgáltatások hozzáadása** párbeszédpanel használatával.

Bemutatjuk, hogyan hozhat létre egy várólistát a kódban. Azt is bemutatjuk, hogyan hajthat végre olyan alapszintű üzenetsor-műveleteket, mint például az üzenetsor-üzenetek hozzáadása, módosítása, olvasása és eltávolítása. A minták C#-kódban vannak megírva, és a [.net-hez készült Microsoft Azure Storage ügyféloldali kódtárat](/previous-versions/azure/dn261237(v=azure.100))használják.

A **csatlakoztatott szolgáltatások hozzáadása** művelet telepíti a megfelelő NuGet-csomagokat az Azure Storage-ba a projektben, és hozzáadja a Storage-fiókhoz tartozó kapcsolati karakterláncot a projekt konfigurációs fájljaihoz.

* Lásd: az [Azure Queue Storage használatának első lépései a .NET használatával](../storage/queues/storage-dotnet-how-to-use-queues.md) további információ a várólisták a kódban történő kezeléséről.
* Az Azure Storage szolgáltatással kapcsolatos általános információkért lásd a [Storage dokumentációját](https://azure.microsoft.com/documentation/services/storage/) .
* Az Azure Cloud Services általános információit [Cloud Services dokumentációban](https://azure.microsoft.com/documentation/services/cloud-services/) találja.
* A ASP.NET-alkalmazások programozásával kapcsolatos további információkért lásd: [ASP.net](https://www.asp.net) .

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat.

## <a name="access-queues-in-code"></a>Hozzáférési sorok a kódban
Ha a várólistákat a Visual Studióban Cloud Services projektekben szeretné elérni, a következő elemeket kell tartalmaznia minden olyan C#-forrásfájl számára, amely hozzáfér az Azure Queue Storage szolgáltatáshoz.

1. Győződjön meg arról, hogy a C#-fájl tetején található névtér-deklarációk tartalmazzák **az alábbi utasításokat** .
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
2. Szerezzen be egy **CloudStorageAccount** -objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával kérheti le a Storage-beli kapcsolódási karakterláncot és a Storage-fiók adatait az Azure-szolgáltatás konfigurációjában.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
3. Szerezzen be egy **CloudQueueClient** -objektumot, amely a Storage-fiókban található üzenetsor-objektumokra hivatkozik.  
   
    ```csharp
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
4. Egy adott várólistára hivatkozó **CloudQueue** objektum beolvasása.
   
    ```csharp
    // Get a reference to a queue named "messageQueue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");
    ```

**Megjegyzés:** Használja a fenti kódot a kód elé a következő mintákban.

## <a name="create-a-queue-in-code"></a>Várólista létrehozása a kódban
Ha a várólistát a kódban szeretné létrehozni, csak vegyen fel egy hívást a **createifnotexists metódust**.

```csharp
// Create the CloudQueue if it does not exist
messageQueue.CreateIfNotExists();
```

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása egy várólistához
Ha egy üzenetet egy meglévő várólistába szeretne beszúrni, hozzon létre egy új **CloudQueueMessage** -objektumot, majd hívja meg a **AddMessage** metódust.

**CloudQueueMessage** objektum hozható létre egy sztringből (UTF-8 formátumban) vagy egy bájtos tömbből.

Íme egy példa, amely beszúrja a "Hello, World" üzenetet.

```csharp
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
messageQueue.AddMessage(message);
```

## <a name="read-a-message-in-a-queue"></a>Üzenet elolvasása egy várólistában
Egy üzenetsor elején található üzenetbe anélkül is bepillanthat, hogy eltávolítaná az üzenetsorból. Ehhez hívja meg a **PeekMessage** módszert.

```csharp
// Peek at the next message
CloudQueueMessage peekedMessage = messageQueue.PeekMessage();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Üzenetsor beolvasása és eltávolítása
A kód képes eltávolítani (de-üzenetsor) egy üzenetet egy várólistából két lépésben.

1. Hívja meg a **GetMessage** -t, hogy beolvassa a következő üzenetet egy várólistában. A **GetMessage** módszerrel lekért üzenet láthatatlanná válik az adott üzenetsorban található üzeneteket olvasó többi kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan.
2. Az üzenetnek a sorból való eltávolításának befejezéséhez hívja meg a **DeleteMessage**.

Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. Az alábbi kód a **DeleteMessage** közvetlenül az üzenet feldolgozását követően hívja meg.

```csharp
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

// Process the message in less than 30 seconds

// Then delete the message.
await messageQueue.DeleteMessage(retrievedMessage);
```


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>További beállítások használata üzenetsor-üzenetek feldolgozásához és eltávolításához
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.

* Lekérheti az üzenetek kötegét (legfeljebb 32).
* Megadhat egy hosszabb vagy rövidebb láthatósági időkorlátot, így a kód több vagy kevesebb időt vehet igénybe az egyes üzenetek teljes feldolgozásához. Az alábbi példakód a **GetMessages** módszer segítségével egyszerre 20 üzenetet kér le. Ezután minden üzenetet feldolgoz egy **foreach** hurok segítségével. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az 5 perc minden üzenetnél ugyanakkor kezdődik, tehát 5 perccel a **GetMessages** hívása után a nem törölt üzenetek újra láthatóvá válnak.

Bemutatunk egy példát:

```csharp
foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.

    // Then delete the message after processing
    messageQueue.DeleteMessage(message);

}
```

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése
Megbecsülheti egy üzenetsorban található üzenetek számát. A **FetchAttributes** módszer lekéri a Queue szolgáltatásból az üzenetsorra vonatkozó attribútumokat, amelyek között megtalálható az üzenetek száma is. A **ApproximateMethodCount** tulajdonság a **FetchAttributes** metódus által lekért utolsó értéket adja vissza a Queue szolgáltatás hívása nélkül.

```csharp
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Az Async-Await minta használata a közös Azure üzenetsor API-kkal
Ez a példa azt szemlélteti, hogyan használható a Async-Await minta az általános Azure üzenetsor API-kkal. A minta meghívja az egyes metódusok aszinkron verzióját, ezt az egyes módszerek **aszinkron** utólagos javításával is megtekintheti. Aszinkron módszer használata esetén az aszinkron várakozási minta a helyi végrehajtás felfüggesztését hajtja végre, amíg a hívás be nem fejeződik. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más munkát végezzen, ami segít elkerülni a teljesítmény szűk keresztmetszetét, és javítja az alkalmazás általános reagálását. További információk az Async-Await mintázat használatáról .NET-keretrendszerben: [Async and Await (C# and Visual Basic)](/previous-versions/hh191443(v=vs.140)) (Async és Await (C# és Visual Basic)).

```csharp
// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Add the message asynchronously
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Delete the message asynchronously
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Üzenetsor törlése
Egy üzenetsor és az összes benne foglalt üzenet törléséhez hívja meg a **Delete** módszert az üzenetsor-objektumhoz.

```csharp
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]