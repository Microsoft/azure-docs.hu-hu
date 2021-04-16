---
title: Üzenetsorok Azure Service Bus a Python azure-servicebus csomag 7.0.0-s verziójával
description: Ez a cikk bemutatja, hogyan küldhet üzeneteket a Pythonnal az üzenetsorok között, illetve fogadhat Azure Service Bus üzenetsorból.
documentationcenter: python
author: spelluru
ms.author: spelluru
ms.date: 11/18/2020
ms.topic: quickstart
ms.devlang: python
ms.custom:
- seo-python-october2019
- devx-track-python
- mode-api
ms.openlocfilehash: e982b3f36dbfe2144170d53d07473201abb014ef
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534974"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Üzenetek küldése és fogadása Azure Service Bus üzenetsorok között (Python)
Ez a cikk bemutatja, hogyan küldhet üzeneteket a Pythonnal az üzenetsorok között, illetve fogadhat Azure Service Bus üzenetsorból. 

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Aktiválhatja a Visual Studio [MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) előfizetői előnyeit, vagy regisztrálhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Ha nincs használható üzenetsora, kövesse az Üzenetsor létrehozása Azure Portal üzenetsor létrehozásához [Service Bus](service-bus-quickstart-portal.md) cikk lépéseit. Jegyezze fel **a névtérhez** Service Bus kapcsolati sztringet és a létrehozott **üzenetsor** nevét.
- Python 2.7-es vagy újabb verzió, telepített [Python Azure Service Bus](https://pypi.python.org/pypi/azure-servicebus) csomaggal. További információkért lásd a Python telepítési [útmutatóját.](/azure/developer/python/azure-sdk-install) 

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
    > - Cserélje `<NAMESPACE CONNECTION STRING>` le a helyére a saját Service Bus sztringet.
    > - Cserélje `<QUEUE NAME>` le a helyére az üzenetsor nevét. 
3. Adjon hozzá egy metódust, amely egyetlen üzenetet küld.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    A küldő egy objektum, amely a létrehozott üzenetsor ügyfeleként működik. Később létre fogja hozni, és argumentumként el fogja küldeni ennek a függvénynek. 
4. Adjon hozzá egy metódust, amely egy üzenetlistát küld.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Adjon hozzá egy metódust, amely egy üzenetkötetet küld.

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
6. Hozzon létre egy Service Bus, majd egy üzenetsor küldőobjektumát az üzenetek küldése számára.

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
 
## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása üzenetsorból
Adja hozzá a következő kódot a print utasítás után. Ez a kód folyamatosan fogadja az új üzeneteket, amíg nem kap új üzeneteket 5 ( `max_wait_time` ) másodpercig. 

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
Az alkalmazás futtatásakor a következő kimenetnek kell látsza lennie: 

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

A Azure Portal navigáljon a Service Bus névterhez. Az Áttekintés **lapon** ellenőrizze, hogy a **bejövő** és kimenő **üzenetek** száma 16-e. Ha nem látja a darabszámokat, néhány perc várakozás után frissítse az oldalt. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Bejövő és kimenő üzenetek száma":::

Az Áttekintés oldalon  válassza ki az üzenetsort, és lépjen a Service Bus **lapra.** Ezen az oldalon a **bejövő** és kimenő **üzenetek** száma is látható. Egyéb információkat is láthat,  például az üzenetsor aktuális méretét és az **aktív üzenetek számát.** 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="Üzenetsor részletei":::


## <a name="next-steps"></a>Következő lépések
Tekintse meg az alábbi dokumentációt és mintákat: 

- [Azure Service Bus Pythonhoz készült ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Példák:](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - A **sync_samples** mappában olyan minták találhatók, amelyek megmutatják, hogyan Service Bus szinkron módon a felhasználókkal. Ebben a rövid útmutatóban ezt a módszert használta. 
    - A **async_samples** mappában minták mutatják be, hogyan kommunikálhat Service Bus aszinkron módon. 
- [az azure-servicebus referenciadokumentációja](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)
