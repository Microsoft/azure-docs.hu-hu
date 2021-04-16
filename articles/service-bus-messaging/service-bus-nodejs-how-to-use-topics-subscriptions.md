---
title: Az azure/service-bus előzetes verziójú JavaScript használata témakörök és előfizetések használatával
description: Megtudhatja, hogyan írhat olyan JavaScript-programot, amely a csomag legújabb előzetes verzióját használja az üzenetek Service Bus-témakörbe való küldését és üzenetek fogadását egy előfizetésből @azure/service-bus a témakörbe.
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 59f1bb29099f2b921e687ca9de46365bc34f1b91
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537241"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Rövid útmutató: Service Bus és előfizetések Node.js az azure/service-bus előzetes verziójú csomaggal
Ez az oktatóanyag bemutatja, hogyan küldhet üzeneteket egy Service Bus-témakörbe a csomaggal egy JavaScript-programban, és hogyan fogadhat üzeneteket egy Service Bus-előfizetésből erre [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) a témakörre.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [MSDN-előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Kövesse a rövid útmutató lépéseit: A Azure Portal segítségével hozzon létre egy Service Bus témakört és előfizetéseket [a témakörre.](service-bus-quickstart-topics-subscriptions-portal.md) Jegyezze fel a kapcsolati sztringet, a témakör nevét és az előfizetés nevét. Ebben a rövid útmutatóban csak egy előfizetést fog használni. 

> [!NOTE]
> - Ez az oktatóanyag a Nodejs használatával másolhatja és futtathatja a [mintákat.](https://nodejs.org/) Útmutató Node.js-alkalmazás létrehozásához: Create and [deploy a Node.js application to an Azure Website](../app-service/quickstart-nodejs.md)(Node.js-alkalmazás létrehozása és üzembe helyezése egy Azure-webhelyen, [ vagyNode.js Cloud Service](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)a Windows PowerShell).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
A Service Bus npm-csomagjának telepítéséhez nyisson meg egy parancssort, amely az elérési útjában található, módosítsa a könyvtárat arra a mappára, ahol a mintákat szeretné, majd futtassa ezt a `npm` parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
A következő mintakód bemutatja, hogyan küldhet egy üzenetkötetet egy Service Bus témakörbe. A részletekért tekintse meg a kód megjegyzéseit. 

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code-et](https://code.visualstudio.com/)
2. Hozzon létre egy nevű `sendtotopic.js` fájlt, és illessze be az alábbi kódot. Ez a kód üzenetet küld a témakörbe.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
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
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
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
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
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
3. Cserélje le a helyére a saját Service Bus `<SERVICE BUS NAMESPACE CONNECTION STRING>` sztringet.
1. Cserélje `<TOPIC NAME>` le a helyére a témakör nevét. 
1. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához.

    ```console
    node sendtotopic.js 
    ```
1. A következő kimenetnek kell megjelennie.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása előfizetésből
1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code-dal](https://code.visualstudio.com/)
2. Hozzon létre egy **receivefromsubscription.js,** és illessze be a következő kódot. A részletekért tekintse meg a kód megjegyzéseit. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
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
3. Cserélje `<SERVICE BUS NAMESPACE CONNECTION STRING>` le a helyére a névtér kapcsolati sztringét. 
1. Cserélje `<TOPIC NAME>` le a helyére a témakör nevét. 
1. Cserélje `<SUBSCRIPTION NAME>` le a helyére a témakör előfizetésének nevét. 
1. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához.

    ```console
    node receivefromsubscription.js
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

A Azure Portal nyissa meg az Service Bus-névteret, és válassza ki a témakört az alsó panelen a **témakör Service Bus-témakör** lapját. Ezen az oldalon három bejövő és három kimenő üzenetet kell látnia az **Üzenetek diagramon.** 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Bejövő és kimenő üzenetek":::

Ha legközelebb csak a send alkalmazást futtatja, a **Service Bus-témakör** oldalon hat bejövő üzenet (3 új), de három kimenő üzenet látható. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Frissített témaköroldal":::

Ezen az oldalon, ha kiválaszt egy előfizetést, a Service Bus **lapjára** jut. Ezen az oldalon láthatja az aktív üzenetek számát, a nem működő üzenetek számát és sok más információt. Ebben a példában három aktív üzenet van, amelyet még nem kapott meg a fogadó. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Aktív üzenetek száma":::

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő dokumentációt és mintákat: 

- [Azure Service Bus Pythonhoz készült ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Példák:](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). A **javascript mappa** JavaScript-mintákat, a **typescript pedig** TypeScript-mintákat tartalmaz. 
- [az azure-servicebus referenciadokumentációja](/javascript/api/overview/azure/service-bus)
