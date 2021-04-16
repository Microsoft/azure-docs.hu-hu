---
title: 'Rövid útmutató: Azure Queue Storage kódtár v12 - JavaScript használata'
description: Megtudhatja, hogyan használhatja Azure Queue Storage JavaScripthez Azure Queue Storage ügyféloldali kódtárat üzenetsor létrehozására és üzenetek hozzáadására. Ezután megtudhatja, hogyan olvashatja és törölheti az üzeneteket az üzenetsorból. Azt is megtudhatja, hogyan törölhet egy üzenetsort.
author: twooley
ms.author: twooley
ms.date: 12/13/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 943678debc32116ff7a2e54810c4edcf2d331bd6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534421"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Rövid útmutató: Azure Queue Storage JavaScripthez való v12-es ügyféloldali kódtár létrehozása

A JavaScripthez Azure Queue Storage v12 ügyféloldali kódtár első lépések. Azure Queue Storage egy nagy számú üzenet tárolására szolgáló szolgáltatás későbbi lekéréshez és feldolgozáshoz. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz szükséges példakódot.

Használja a Azure Queue Storage JavaScripthez való v12-es ügyféloldali kódtárát a következőre:

- Üzenetsor létrehozása
- Üzenetek hozzáadása üzenetsorhoz
- Betekintés az üzenetsor üzeneteibe
- Üzenet frissítése egy üzenetsorban
- Üzenetek fogadása üzenetsorból
- Üzenetek törlése egy üzenetsorból
- Üzenetsor törlése

További források:

- [API-referenciadokumentáció](/javascript/api/@azure/storage-queue/)
- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [Csomag (npm)](https://www.npmjs.com/package/@azure/storage-queue)
- [Példák](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés [– hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/)
- Azure Storage-fiók [– tárfiók létrehozása](../common/storage-account-create.md)
- Aktuális [Node.js](https://nodejs.org/en/download/) az operációs rendszer számára.

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti egy projekt előkészítésén, hogy az működjön a Azure Queue Storage JavaScripthez készült v12 ügyféloldali kódtárával.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre Node.js nevű alkalmazás `queues-quickstart-v12`

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Váltson az újonnan létrehozott `queues-quickstart-v12` könyvtárra.

    ```console
    cd queues-quickstart-v12
    ```

1. Hozzon létre egy nevű új `package.json` szövegfájlt. Ez a fájl határozza meg a Node.js projektet. Mentse ezt a fájlt a `queues-quickstart-v12` könyvtárba. Itt látható a fájl tartalma:

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

    Ha szeretné, a saját nevét is meg is `author` használhatja a mezőben.

### <a name="install-the-package"></a>A csomag telepítése

A könyvtárban maradva telepítse a Azure Queue Storage JavaScript-ügyféloldali `queues-quickstart-v12` kódtárat az `npm install` paranccsal.

```console
npm install
```

Ez a parancs beolvassa a fájlt, és telepíti Azure Queue Storage JavaScripthez Azure Queue Storage-es ügyfélkódtár v12-es csomagját és az összes kódtárat, `package.json` amelyektől függ.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projektkönyvtárból:

1. Nyisson meg egy másik új szövegfájlt a kódszerkesztőben
1. Hívások `require` hozzáadása az Azure és a Node.js betöltéséhez
1. A program struktúrájának létrehozása, beleértve a nagyon egyszerű kivételkezelést

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

1. Mentse az új fájlt a `queues-quickstart-v12.js` `queues-quickstart-v12` könyvtárban a következőként: .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. Az üzenetsor-üzenetek mérete legfeljebb 64 KB lehet. Az üzenetsorok több millió üzenetet tartalmazhatnak, a tárfiókok maximális kapacitásán belül. Az üzenetsorokat gyakran használják hátralékok létrehozásához aszinkron feldolgozáshoz. Queue Storage háromféle erőforrást kínál:

- A tárfiók
- Üzenetsor a tárfiókban
- Üzenetsoron belüli üzenetek

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![A Queue Storage architektúrájának ábrája](./media/storage-queues-introduction/queue1.png)

Az alábbi JavaScript-osztályokkal használhatja ezeket az erőforrásokat:

- [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient): A `QueueServiceClient` lehetővé teszi a tárfiókban lévő összes üzenetsor kezelését.
- [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient): A osztály lehetővé teszi az egyes üzenetsorok és üzeneteik `QueueClient` kezelését és kezelését.
- [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage): A `QueueMessage` osztály az üzenetsoron való híváskor visszaadott [`ReceiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) egyes objektumokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek azt mutatják be, hogyan lehet a következő műveleteket Azure Queue Storage JavaScript ügyféloldali kódtárával:

- [A kapcsolati sztring lekérése](#get-the-connection-string)
- [Üzenetsor létrehozása](#create-a-queue)
- [Üzenetek hozzáadása üzenetsorhoz](#add-messages-to-a-queue)
- [Betekintés az üzenetsor üzeneteibe](#peek-at-messages-in-a-queue)
- [Üzenet frissítése egy üzenetsorban](#update-a-message-in-a-queue)
- [Üzenetek fogadása üzenetsorból](#receive-messages-from-a-queue)
- [Üzenetek törlése egy üzenetsorból](#delete-messages-from-a-queue)
- [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A következő kód lekéri a tárfiók kapcsolati sztringét a Configure [your storage connection string](#configure-your-storage-connection-string) (A storage kapcsolati sztring konfigurálása) szakaszban létrehozott környezeti változóból.

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

Döntse el az új üzenetsor nevét. Az alábbi kód egy UUID-értéket fűz hozzá az üzenetsor nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> Az üzenetsorok nevei csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3–63 karakter hosszúságúnak kell lennie. További információ: [Üzenetsorok és metaadatok elnevezése.](/rest/api/storageservices/naming-queues-and-metadata)

Hozza létre a osztály egy [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) példányát. Ezután hívja meg a [`create`](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) metódust az üzenetsor létrehozásához a tárfiókban.

Adja hozzá ezt a kódot a függvény `main` véghez:

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

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása üzenetsorhoz

Az alábbi kódrészlet üzeneteket ad hozzá az üzenetsorhoz a metódus [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) hívásával. A harmadik hívás által [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage) visszaadott et is `sendMessage` menti. A `sendMessageResponse` visszaadott érték az üzenet tartalmának frissítésére lesz használva a program későbbi részen.

Adja hozzá ezt a kódot a függvény `main` véghez:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Betekintés az üzenetsor üzeneteibe

A metódus hívásával bepillantás az üzenetsor [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) üzeneteibe. Ez a metódus egy vagy több üzenetet ad vissza az üzenetsor elejéről, de nem módosítja az üzenet láthatóságát.

Adja hozzá ezt a kódot a függvény `main` véghez:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Üzenetsor üzenetének frissítése

Frissítse egy üzenet tartalmát a metódus [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) hívásával. Ez a metódus módosíthatja az üzenetek láthatósági időkorlátját és tartalmát. Az üzenet tartalmának UTF-8 kódolású sztringnek kell lennie, amely legfeljebb 64 KB méretű lehet. Az új tartalom mellett adja meg a és a adatokat a kódban `messageId` `popReceipt` korábban mentett válaszból. A `sendMessageResponse` tulajdonságok azonosítják, hogy melyik üzenetet kell frissíteni.

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

### <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása üzenetsorból

Töltse le a korábban hozzáadott üzeneteket a metódus [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) hívásával. A mezőben adja meg a híváshoz fogadott üzenetek `numberOfMessages` maximális számát.

Adja hozzá ezt a kódot a függvény `main` véghez:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Üzenetek törlése egy üzenetsorból

Üzenetek törlése az üzenetsorból azok fogadása és feldolgozása után. Ebben az esetben a feldolgozás csak az üzenetet jeleníti meg a konzolon.

Törölje az üzeneteket a metódus [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) hívásával. A explicit módon nem törölt üzenetek végül ismét láthatóvá válnak az üzenetsorban, hogy egy másik lehetőség legyen azok feldolgozására.

Adja hozzá ezt a kódot a függvény `main` véghez:

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

A következő kód törli az alkalmazás által létrehozott erőforrásokat az üzenetsor a metódussal való [`delete`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) törlésével.

Adja hozzá ezt a kódot a függvény `main` véghez, és mentse a fájlt:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre és ad hozzá egy Azure-üzenetsorhoz. A kód listázza az üzenetsorban lévő üzeneteket, majd lekéri és törli őket, mielőtt végül töröl egy üzenetsort.

A konzolablakban keresse meg a fájlt tartalmazó könyvtárat, majd futtassa az alkalmazást a következő `queues-quickstart-v12.js` `node` paranccsal.

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

Lépjen végig a hibakeresőben található kódon, és ellenőrizze Azure Portal [folyamat](https://portal.azure.com) során. Ellenőrizze a tárfiókban, hogy az üzenetsorban lévő üzenetek létrejöttek-e és törölve vannak-e.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre üzenetsort, és hogyan adhat hozzá üzeneteket JavaScript-kód használatával. Ezután megtanulta, hogyan lehet betekintésbe, lekérni és törölni az üzeneteket. Végül megtanulta, hogyan törölhet üzenetsort.

Az oktatóanyagokért, mintákért, rövid útmutatókért és egyéb dokumentációkért látogasson el a következő webhelyre:

> [!div class="nextstepaction"]
> [Az Azure for JavaScript dokumentációja](/azure/developer/javascript/)

- További tudnivalókért tekintse meg a [JavaScripthez Azure Queue Storage ügyféloldali kódtárat.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- További Azure Queue Storage lásd: [Azure Queue Storage JavaScripthez](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples)Azure Queue Storage kódtár – minták.
