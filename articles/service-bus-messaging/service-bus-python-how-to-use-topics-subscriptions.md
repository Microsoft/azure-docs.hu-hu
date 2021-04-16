---
title: A Azure Service Bus és előfizetések használata a Python azure-servicebus csomag 7.0.0-s verziójával
description: Ez a cikk bemutatja, hogyan küldhet üzeneteket egy témakörbe a Python használatával, és hogyan fogadhat üzeneteket az előfizetésből.
documentationcenter: python
author: spelluru
ms.author: spelluru
ms.date: 11/18/2020
ms.topic: quickstart
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 49e80e277c6df5372341293861d5bda0580f3e8c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537160"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Üzenetek küldése egy Azure Service Bus témakörbe és üzenetek fogadása az előfizetésekből a témakörbe (Python)
Ez a cikk bemutatja, hogyan használhatja a Pythont egy Service Bus üzenettémához, és hogyan fogadhat üzeneteket egy előfizetésből a témakörbe. 

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Aktiválhatja a Visual Studio [MSDN-előfizetői](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) előnyeit, vagy regisztrálhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Kövesse a rövid útmutató lépéseit: A Azure Portal segítségével hozzon létre egy Service Bus témakört és előfizetéseket [a témakörre.](service-bus-quickstart-topics-subscriptions-portal.md) Jegyezze fel a kapcsolati sztringet, a témakör nevét és az előfizetés nevét. Ebben a rövid útmutatóban csak egy előfizetést fog használni. 
- Python 2.7 vagy újabb, telepített [Azure Python SDK][Azure Python package] csomaggal. További információkért lásd a Python telepítési [útmutatóját.](/azure/developer/python/azure-sdk-install)

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe

1. Adja hozzá a következő importálási utasítást. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Adja hozzá a következő állandókat. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - Cserélje `<NAMESPACE CONNECTION STRING>` le a helyére a névtér kapcsolati sztringjét.
    > - Cserélje `<TOPIC NAME>` le a helyére a témakör nevét.
    > - Cserélje `<SUBSCRIPTION NAME>` le a helyére a témakör előfizetésének nevét. 
3. Adjon hozzá egy metódust, amely egyetlen üzenetet küld.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    A küldő egy objektum, amely a létrehozott témakör ügyfeleként működik. Később létre fogja hozni, és argumentumként el fogja küldeni ennek a függvénynek. 
4. Adjon hozzá egy metódust, amely egy üzenetlistát küld.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
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
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Hozzon létre egy Service Bus, majd egy témakör küldőobjektumát az üzenetek küldését.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
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
 
## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása előfizetésből
Adja hozzá a következő kódot a print utasítás után. Ez a kód folyamatosan fogadja az új üzeneteket, amíg nem kap új üzeneteket 5 ( `max_wait_time` ) másodpercig. 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Teljes kód

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

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
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>Az alkalmazás futtatása
Az alkalmazás futtatásakor a következő kimenetnek kell lennie: 

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

Az alsó panelen válassza ki a témakört, hogy Service Bus **témakört** tartalmazó lapját. Ezen az oldalon három bejövő és három kimenő üzenetet kell látnia az **Üzenetek diagramon.** 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Bejövő és kimenő üzenetek":::

Ezen a lapon, ha kiválaszt egy előfizetést, a Service Bus **lapjára** jut. Ezen az oldalon láthatja az aktív üzenetek számát, a holtbetűs üzenetek számát és sok más stb. Ebben a példában minden üzenet beérkezett, így az aktív üzenetek száma nulla. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Aktív üzenetek száma":::

Ha megjegyzésbe fűzi a fogadási kódot, az aktív üzenetek száma 16 lesz. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Aktív üzenetek száma – nincs fogadás":::

## <a name="next-steps"></a>Következő lépések
Tekintse meg az alábbi dokumentációt és mintákat: 

- [Azure Service Bus Pythonhoz készült ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Példák:](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - A **sync_samples** mappában olyan minták találhatók, amelyek megmutatják, hogyan Service Bus szinkron módon a Service Bus műveleteket. Ebben a rövid útmutatóban ezt a metódust használta. 
    - A **async_samples** mappában minták találhatók, amelyekből aszinkron módon Service Bus műveleteket. 
- [az azure-servicebus referenciadokumentációja](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)
