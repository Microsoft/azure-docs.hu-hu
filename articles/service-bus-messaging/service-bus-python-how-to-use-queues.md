---
title: Azure Service Bus Queues használata a Python Azure-servicebus csomag verziójával 7.0.0
description: Ebből a cikkből megtudhatja, hogyan küldhet üzeneteket és fogadhat üzeneteket Azure Service Bus várólistákból a Python használatával.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 0553062032a58ec9eb9cf3c474ee7c8f19fc544d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98631555"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Üzenetek küldése és fogadása Azure Service Bus várólistákból (Python)
Ebből a cikkből megtudhatja, hogyan küldhet üzeneteket és fogadhat üzeneteket Azure Service Bus várólistákból a Python használatával. 

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik várólistával, hogy működjön a szolgáltatással, a várólista létrehozásához kövesse az [Azure Portal használata Service Bus üzenetsor létrehozásához](service-bus-quickstart-portal.md) című cikket. Jegyezze **fel a Service Bus névtér és a létrehozott** **várólista** nevét.
- Python 2,7 vagy újabb verzió, telepített [python Azure Service Bus](https://pypi.python.org/pypi/azure-servicebus) csomaggal. További információ: a [Python telepítési útmutatója](/azure/developer/python/azure-sdk-install). 

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba

1. Adja hozzá a következő importálási utasítást. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Adja hozzá a következő állandókat. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - Cserélje le a helyére `<NAMESPACE CONNECTION STRING>` a Service Bus névtérhez tartozó kapcsolatok karakterláncát.
    > - Cserélje le a `<QUEUE NAME>` nevet a várólista nevére. 
3. Adjon hozzá egy metódust egyetlen üzenet küldéséhez.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    A küldő egy olyan objektum, amely ügyfélként funkcionál a létrehozott üzenetsor számára. Később létrehozhatja és elküldheti a függvény argumentumaként. 
4. Adjon hozzá egy metódust az üzenetek listájának elküldéséhez.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Adjon hozzá egy metódust egy köteg üzenet küldéséhez.

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Hozzon létre egy Service Bus ügyfelet, majd egy üzenetsor-küldő objektumot az üzenetek küldéséhez.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából
Adja hozzá a következő kódot a Print utasítás után. Ez a kód folyamatosan fogad új üzeneteket, amíg az új üzenetek nem kapnak 5 ( `max_wait_time` ) másodpercet. 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Teljes kód

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>Az alkalmazás futtatása
Az alkalmazás futtatásakor a következő kimenetnek kell megjelennie: 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

A Azure Portal navigáljon a Service Bus-névtérhez. Az **Áttekintés** oldalon ellenőrizze, hogy a **bejövő** és a **kimenő** üzenetek száma 16. Ha nem látja a számlálást, frissítse az oldalt néhány perc várakozás után. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Bejövő és kimenő üzenetek száma":::

Válassza ki a várólistát ezen az **áttekintő** lapon, és navigáljon a **Service Bus üzenetsor** lapra. Ezen a lapon a **bejövő** és a **kimenő** üzenetek száma is látható. Más információk is megjelennek, például a várólista **jelenlegi mérete** és az **aktív üzenetek száma**. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="Üzenetsor részletei":::


## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő dokumentációt és mintákat: 

- [Azure Service Bus a Pythonhoz készült ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - A **sync_samples** mappa olyan mintákat tartalmaz, amelyek bemutatják, hogyan kezelhető a Service Bus szinkron módon. Ebben a rövid útmutatóban ezt a módszert használta. 
    - A **async_samples** mappa olyan mintákat tartalmaz, amelyek bemutatják, hogyan lehet aszinkron módon kommunikálni a Service Busokkal. 
- [Az Azure-servicebus dokumentációja](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)