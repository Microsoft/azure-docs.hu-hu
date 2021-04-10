---
title: 'Gyors útmutató: Azure Queue Storage Client Library V12 – JavaScript'
description: Megtudhatja, hogyan hozhat létre várólistát, és hogyan adhat hozzá üzeneteket a JavaScripthez készült Azure Queue Storage-ügyfél kódtár használatával Ebből a cikkből megtudhatja, hogyan olvashatja és törölheti az üzeneteket a várólistából. Azt is megtudhatja, hogyan törölhet egy várólistát.
author: twooley
ms.author: twooley
ms.date: 12/13/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-js
ms.openlocfilehash: 628ce97b9f14290caf3f41b16cda93f4f47566f7
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276161"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Gyors útmutató: Azure Queue Storage Client Library V12 a JavaScripthez

Ismerkedjen meg a JavaScripthez készült Azure Queue Storage ügyfél-kódtár V12-es verziójának használatába. Az Azure Queue Storage egy olyan szolgáltatás, amely nagy mennyiségű üzenetet tárol a későbbi lekérésekhez és feldolgozáshoz. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja a programkódot az alapszintű feladatokhoz.

A JavaScripthez készült Azure Queue Storage ügyféloldali kódtár a következőre használható:

- Üzenetsor létrehozása
- Üzenetek hozzáadása egy várólistához
- Üzenetek betekintése egy várólistába
- Üzenetsor frissítése
- Üzenetek fogadása egy várólistából
- Üzenetek törlése egy várólistából
- Üzenetsor törlése

További források:

- [API-referenciadokumentáció](/javascript/api/@azure/storage-queue/)
- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [Csomag (NPM)](https://www.npmjs.com/package/@azure/storage-queue)
- [Példák](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- Azure Storage-fiók – [Storage-fiók létrehozása](../common/storage-account-create.md)
- Az operációs rendszer jelenlegi [Node.js](https://nodejs.org/en/download/) .

## <a name="setting-up"></a>Beállítás

Ebből a szakaszból megtudhatja, hogyan készít elő egy projektet a JavaScripthez készült Azure Queue Storage ügyfél-kódtár V12-es verziójának működéséhez.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy Node.js nevű alkalmazást `queues-quickstart-v12`

1. A konzol ablakban (például cmd, PowerShell vagy bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Váltson az újonnan létrehozott `queues-quickstart-v12` címtárra.

    ```console
    cd queues-quickstart-v12
    ```

1. Hozzon létre egy nevű új szövegfájlt `package.json` . Ez a fájl határozza meg a Node.js projektet. Mentse ezt a fájlt a `queues-quickstart-v12` könyvtárba. A fájl tartalma:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Ha szeretné, saját nevet is helyezhet a `author` mezőbe.

### <a name="install-the-package"></a>A csomag telepítése

Miközben a `queues-quickstart-v12` címtárban marad, telepítse az Azure Queue Storage ügyféloldali kódtárat a JavaScript-csomaghoz a `npm install` parancs használatával.

```console
npm install
```

Ez a parancs beolvassa a `package.json` fájlt, és telepíti az Azure Queue Storage ügyféloldali kódtárat a JavaScript-csomaghoz, és az összes olyan kódtárat, amelytől függ.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Nyisson meg egy másik új szövegfájlt a Kódszerkesztőben
1. `require`Hívások hozzáadása az Azure és Node.js modulok betöltéséhez
1. A program struktúrájának létrehozása, beleértve a nagyon egyszerű kivételek kezelését

    A kód a következő:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue Storage client library v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Mentse az új fájlt `queues-quickstart-v12.js` a `queues-quickstart-v12` könyvtárba.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra. A Queue Storage háromféle típusú erőforrást kínál:

- A Storage-fiók
- A Storage-fiókban lévő üzenetsor
- Üzenetek a várólistán belül

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Üzenetsor-tárolási architektúra ábrája](./media/storage-queues-introduction/queue1.png)

Használja az alábbi JavaScript-osztályokat a következő erőforrásokkal való interakcióhoz:

- [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient): A `QueueServiceClient` lehetővé teszi a Storage-fiók összes várólistájának kezelését.
- [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient): Az `QueueClient` osztály segítségével kezelheti és kezelheti az egyes várólistákat és az üzeneteiket.
- [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage): Az `QueueMessage` osztály a várólista hívásakor visszaadott egyedi objektumokat jelöli [`ReceiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) .

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan hajthatja végre a következő műveleteket a JavaScript Azure Queue Storage ügyféloldali kódtár használatával:

- [A kapcsolati sztring lekérése](#get-the-connection-string)
- [Üzenetsor létrehozása](#create-a-queue)
- [Üzenetek hozzáadása egy várólistához](#add-messages-to-a-queue)
- [Üzenetek betekintése egy várólistába](#peek-at-messages-in-a-queue)
- [Üzenetsor frissítése](#update-a-message-in-a-queue)
- [Üzenetek fogadása egy várólistából](#receive-messages-from-a-queue)
- [Üzenetek törlése egy várólistából](#delete-messages-from-a-queue)
- [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A következő kód lekéri a Storage-fiókhoz tartozó, a [tárolási kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból származó kapcsolatok karakterláncát.

Adja hozzá ezt a kódot a `main` függvényhez:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Üzenetsor létrehozása

Döntse el az új üzenetsor nevét. A következő kód egy UUID értéket fűz hozzá a várólista neveként, hogy az egyedi legyen.

> [!IMPORTANT]
> A várólisták nevei csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3 – 63 karakter hosszúnak kell lennie. További információ: a [várólisták és a metaadatok elnevezése](/rest/api/storageservices/naming-queues-and-metadata).

Hozza létre a osztály egy példányát [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) . Ezután hívja meg a [`create`](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) metódust az üzenetsor létrehozásához a Storage-fiókban.

Adja hozzá ezt a kódot a függvény végéhez `main` :

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása egy várólistához

A következő kódrészlet üzeneteket hoz létre a várólistához a metódus meghívásával [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) . Emellett a [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage) harmadik hívásból is menti a visszaadott értéket `sendMessage` . A visszaadott érték az `sendMessageResponse` üzenet tartalmának későbbi, a programban való frissítésére szolgál.

Adja hozzá ezt a kódot a függvény végéhez `main` :

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Üzenetek betekintése egy várólistába

A metódus meghívásával megtekintheti a várólistában lévő üzeneteket [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) . Ez a metódus egy vagy több üzenetet kér le a várólista elejéről, de nem módosítja az üzenet láthatóságát.

Adja hozzá ezt a kódot a függvény végéhez `main` :

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Üzenetsor frissítése

Az üzenet tartalmának frissítéséhez hívja meg a [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) metódust. Ez a metódus módosíthatja az üzenet láthatósági időtúllépését és tartalmát. Az üzenet tartalmának UTF-8 kódolású karakterláncnak kell lennie, amely akár 64 KB méretű is lehet. Az új tartalommal együtt továbbítja a `messageId` kódot a `popReceipt` korábban mentett válasznak. A `sendMessageResponse` tulajdonságok határozzák meg, hogy melyik üzenetet kell frissíteni.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából

A korábban hozzáadott üzenetek letöltése a metódus meghívásával [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) . A `numberOfMessages` mezőben adja meg a híváshoz fogadott üzenetek maximális számát.

Adja hozzá ezt a kódot a függvény végéhez `main` :

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Üzenetek törlése egy várólistából

Üzenetek törlése a sorból a fogadás és a feldolgozás után. Ebben az esetben a feldolgozás csak az üzenet megjelenítését jeleníti meg a konzolon.

Az üzenetek törléséhez hívja meg a [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) metódust. A nem explicit módon törölt üzenetek később ismét láthatóvá válnak a várólistában, hogy egy másik lehetőség is feldolgozza őket.

Adja hozzá ezt a kódot a függvény végéhez `main` :

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Üzenetsor törlése

A következő kód törli az alkalmazás által létrehozott erőforrásokat az üzenetsor törlésével a [`delete`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) metódus használatával.

Adja hozzá ezt a kódot a függvény végéhez `main` , és mentse a fájlt:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre és tesz hozzá egy Azure-várólistához. A kód felsorolja a várólistán lévő üzeneteket, majd lekéri és törli őket, mielőtt véglegesen törölné a várólistát.

A konzol ablakban navigáljon a fájlt tartalmazó könyvtárhoz `queues-quickstart-v12.js` , majd futtassa az alábbi parancsot az `node` alkalmazás futtatásához.

```console
node queues-quickstart-v12.js
```

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Queue Storage client library v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Lépjen be a hibakeresőbe, és vizsgálja meg a [Azure Portal](https://portal.azure.com) a folyamat során. Ellenőrizze a Storage-fiókját, hogy az üzenetek a várólistán legyenek létrehozva és törölve.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre várólistát, és hogyan adhat hozzá üzeneteket a JavaScript-kód használatával. Ezután megtanulta az üzenetek betekintését, beolvasását és törlését. Végezetül megtanulta, hogyan törölhet egy üzenetsor-várólistát.

Oktatóanyagok, minták, gyors indítás és egyéb dokumentáció:

> [!div class="nextstepaction"]
> [Azure a JavaScripthez – dokumentáció](/azure/developer/javascript/)

- További információ: [Azure Queue Storage ügyféloldali kódtár a javascripthez](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
- További Azure Queue Storage-példákat a [JavaScript-példákat lásd: azure Queue Storage ügyféloldali kódtár V12](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
