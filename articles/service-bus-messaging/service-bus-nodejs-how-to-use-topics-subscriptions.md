---
title: Az előnézeti JavaScript Azure/Service-Bus használata témakörökkel és előfizetésekkel
description: Megtudhatja, hogyan írhat egy olyan JavaScript-programot, amely a csomag legújabb előzetes verzióját használja @azure/service-bus , hogy üzeneteket küldjön egy Service Bus témakörnek, és üzeneteket fogadjon egy előfizetésből a témakörbe.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: a1afe4207ce3833f3bcb55bc7bc2e8e27f393f63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179996"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Gyors útmutató: Service Bus témakörök és előfizetések Node.js és az előzetes verziójú Azure/Service-Bus csomaggal
Ebből az oktatóanyagból megtudhatja, hogyan használhatja a [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) csomagot egy JavaScript-programban, hogy üzeneteket küldjön egy Service Bus témakörbe, és üzeneteket fogadjon egy Service Bus-előfizetésből az adott témakörbe.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Kövesse a rövid útmutató lépéseit [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörbe](service-bus-quickstart-topics-subscriptions-portal.md). Jegyezze fel a kapcsolatok karakterláncát, a témakör nevét és az előfizetés nevét. Ehhez a rövid útmutatóhoz csak egy előfizetést fog használni. 

> [!NOTE]
> - Ez az oktatóanyag olyan példákkal működik, amelyeket a [NodeJS](https://nodejs.org/)használatával másolhat és futtathat. Node.js alkalmazások létrehozásával kapcsolatos utasításokért lásd: [Node.js alkalmazás létrehozása és telepítése Azure-webhelyre](../app-service/quickstart-nodejs.md), vagy [Node.js Cloud Service a Windows PowerShell használatával](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával
A Service Bus NPM-csomagjának telepítéséhez nyisson meg egy parancssort, amely `npm` az elérési útjában található, módosítsa a könyvtárat arra a mappára, ahol a mintákat használni szeretné, majd futtassa ezt a parancsot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Az alábbi mintakód bemutatja, hogyan küldhet egy batch üzenetet egy Service Bus témakörbe. Részletekért lásd a kód megjegyzéseit. 

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy nevű fájlt `sendtotopic.js` , és illessze be az alábbi kódot. Ez a kód üzenetet küld a témakörnek.

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
3. Cserélje le a `<SERVICE BUS NAMESPACE CONNECTION STRING>` karakterláncot a Service Bus névtérhez tartozó kapcsolódási sztringre.
1. Cserélje le a `<TOPIC NAME>` nevet a témakör nevére. 
1. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához.

    ```console
    node sendtotopic.js 
    ```
1. A következő kimenetnek kell megjelennie.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy **receivefromsubscription.js** nevű fájlt, és illessze be a következő kódot. Részletekért lásd a kód megjegyzéseit. 

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
3. Cserélje le a `<SERVICE BUS NAMESPACE CONNECTION STRING>` karakterláncot a névtérhez tartozó kapcsolódási sztringre. 
1. Cserélje le a `<TOPIC NAME>` nevet a témakör nevére. 
1. A helyére írja `<SUBSCRIPTION NAME>` be az előfizetés nevét a témakörre. 
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

A Azure Portalban navigáljon a Service Bus névtérhez, és az alsó ablaktáblán kattintson a témakörre, és tekintse meg a témakör **Service Bus témakör** lapját. Ezen az oldalon három bejövő és három kimenő üzenetet kell látnia az **üzenetek** diagramon. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Bejövő és kimenő üzenetek":::

Ha a csak a Küldés alkalmazást legközelebb futtatja, a **Service Bus témakör** oldalon hat bejövő üzenet jelenik meg (3 új), de három kimenő üzenet. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Frissített témakör lap":::

Ezen az oldalon, ha kijelöl egy előfizetést, a **Service Bus előfizetés** oldalra kerül. Ezen az oldalon megtekintheti az aktív üzenetek darabszámát, a kézbesítetlen üzenetek darabszámát és további információt. Ebben a példában három olyan aktív üzenet van, amelyet a fogadó még nem fogadott el. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Aktív üzenetek száma":::

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő dokumentációt és mintákat: 

- [Azure Service Bus a Pythonhoz készült ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). A **JavaScript** -mappa JavaScript-példákkal rendelkezik, az **írógéppel** pedig írógéppel készített mintákat tartalmaz. 
- [Az Azure-servicebus dokumentációja](/javascript/api/overview/azure/service-bus)
