---
title: Az Azure/Service-Bus-várólisták használata a JavaScriptben
description: Megtudhatja, hogyan írhat olyan JavaScript-programot, amely a csomag legújabb verzióját használja az üzenetek @azure/service-bus küldésére és fogadására egy Service Bus-várólistából.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 3c499dcb5233cbf5cd4048c641d1b38e289cc35f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739712"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Üzenetek küldése és fogadása Azure Service Bus várólistákból (JavaScript)
Ebből az oktatóanyagból megtudhatja, hogyan használhatja a [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) csomagot egy JavaScript-programban egy Service Bus üzenetsor üzeneteinek küldéséhez és fogadásához.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik várólistával, hogy működjön a szolgáltatással, a várólista létrehozásához kövesse az [Azure Portal használata Service Bus üzenetsor létrehozásához](service-bus-quickstart-portal.md) című cikket. Jegyezze **fel a Service Bus névtér és a létrehozott** **várólista** nevét.

> [!NOTE]
> - Ez az oktatóanyag olyan példákkal működik, amelyeket a [NodeJS](https://nodejs.org/)használatával másolhat és futtathat. Node.js alkalmazások létrehozásával kapcsolatos utasításokért lásd: [Node.js alkalmazás létrehozása és telepítése Azure-webhelyre](../app-service/quickstart-nodejs.md), vagy [Node.js Cloud Service a Windows PowerShell használatával](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
A Service Bus NPM-csomagjának telepítéséhez nyisson meg egy parancssort, amely `npm` az elérési útjában található, módosítsa a könyvtárat arra a mappára, ahol a mintákat használni szeretné, majd futtassa ezt a parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Az alábbi mintakód bemutatja, hogyan küldhet üzenetet egy várólistába.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/)-ot.
2. Hozzon létre egy nevű fájlt `send.js` , és illessze be az alábbi kódot. Ez a kód üzenetet küld a várólistának. Az üzenet címkéje (tudós) és törzs (Einstein).

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
3. Cserélje le a `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` karakterláncot a Service Bus névtérhez tartozó kapcsolódási sztringre.
1. Cserélje le a `<QUEUE NAME>` nevet a várólista nevére. 
1. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához.

    ```console
    node send.js 
    ```
1. A következő kimenetnek kell megjelennie.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy nevű fájlt `receive.js` , és illessze be a következő kódot.

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
3. Cserélje le a `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` karakterláncot a Service Bus névtérhez tartozó kapcsolódási sztringre.
1. Cserélje le a `<QUEUE NAME>` nevet a várólista nevére. 
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

A Azure Portal Service Bus névterének **Áttekintés** lapján láthatók a **bejövő** és a **kimenő** üzenetek száma. Előfordulhat, hogy várnia kell egy percet, majd frissítenie kell a lapot a legfrissebb értékek megtekintéséhez. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Bejövő és kimenő üzenetek száma":::

Válassza ki a várólistát ezen az **áttekintő** lapon, és navigáljon a **Service Bus üzenetsor** lapra. Ezen a lapon a **bejövő** és a **kimenő** üzenetek száma is látható. Más információk is megjelennek, például a várólista **jelenlegi mérete** , a **maximális méret**, az **aktív üzenetek száma** és így tovább. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Üzenetsor részletei":::
## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő dokumentációt és mintákat: 

- [Az ügyféloldali kódtár Azure Service Bus a JavaScripthez](https://www.npmjs.com/package/@azure/service-bus)
- [JavaScript-minták](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Írógéppel minták](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [API-referenciadokumentáció](/javascript/api/overview/azure/service-bus)