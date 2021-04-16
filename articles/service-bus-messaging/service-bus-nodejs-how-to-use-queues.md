---
title: How to use azure/service-bus queues in JavaScript
description: Megtudhatja, hogyan írhat olyan JavaScript-programot, amely a csomag legújabb verzióját használja üzenetek küldésére és fogadására egy Service Bus @azure/service-bus üzenetsorból.
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: aee67becf7519f03839eafbd897838f938871307
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537228"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Üzenetek küldése és fogadása Azure Service Bus üzenetsorból (JavaScript)
Ez az oktatóanyag bemutatja, hogyan használhatja a csomagot egy JavaScript-programban üzenetek küldésére és fogadására egy Service Bus [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) üzenetsorból.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [MSDN-előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Ha nincs használható üzenetsora, kövesse a Use [Azure Portal to create a Service Bus queue](service-bus-quickstart-portal.md) (Üzenetsor létrehozása üzenetsor létrehozásához) cikk lépéseit az üzenetsor létrehozásához. Jegyezze fel **a** Service Bus névterének kapcsolati sztringjét és a **létrehozott** üzenetsor nevét.

> [!NOTE]
> - Ez az oktatóanyag a Nodejs használatával másolhatja és futtathatja a [mintákat.](https://nodejs.org/) Útmutató Node.js-alkalmazás létrehozásához: Node.js-alkalmazás létrehozása és üzembe helyezése egy [Azure-webhelyen](../app-service/quickstart-nodejs.md)vagyNode.js-felhőszolgáltatásban a [Windows PowerShell.](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
A Service Bus npm-csomagjának telepítéséhez nyisson meg egy parancssort, amely az elérési útjában található, módosítsa a könyvtárat arra a mappára, ahol a mintákat szeretné, majd futtassa ezt a `npm` parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Az alábbi mintakód bemutatja, hogyan küldhet üzenetet egy üzenetsorba.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code-et.](https://code.visualstudio.com/)
2. Hozzon létre egy nevű `send.js` fájlt, és illessze be az alábbi kódot. Ez a kód üzenetet küld az üzenetsorba. Az üzenet egy címkével (Scientist, Scientist) és egy törzstel (Test) rendelkezik.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
    async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Cserélje le a helyére a saját Service Bus `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` sztringet.
1. Cserélje `<QUEUE NAME>` le a helyére az üzenetsor nevét. 
1. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához.

    ```console
    node send.js 
    ```
1. A következő kimenetnek kell megjelennie.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása üzenetsorból

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code-et](https://code.visualstudio.com/)
2. Hozzon létre egy nevű `receive.js` fájlt, és illessze be a következő kódot.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Cserélje le a helyére a saját Service Bus `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` sztringet.
1. Cserélje `<QUEUE NAME>` le a helyére az üzenetsor nevét. 
1. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához.

    ```console
    node receive.js
    ```
1. A következő kimenetnek kell megjelennie.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

A  Service Bus-névtér Áttekintés Azure Portal láthatja a bejövő és kimenő  **üzenetek** számát. Előfordulhat, hogy várnia kell egy percet, majd frissítenie kell az oldalt a legújabb értékekhez. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Bejövő és kimenő üzenetek száma":::

Az Áttekintés oldalon válassza ki az **üzenetsort,** és lépjen a Service Bus **lapra.** Ezen az oldalon **a bejövő** és **kimenő** üzenetek száma is megjelenik. Egyéb információkat is láthat,  például az üzenetsor aktuális **méretét,** a maximális méretet, az aktív **üzenetek számát** stb. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Üzenetsor részletei":::
## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő dokumentációt és mintákat: 

- [Azure Service Bus JavaScript ügyféloldali kódtára](https://www.npmjs.com/package/@azure/service-bus)
- [JavaScript-minták](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript-minták](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [API-referenciadokumentáció](/javascript/api/overview/azure/service-bus)
