---
title: Azure Service Bus témakörök és előfizetések használata a Python Azure-servicebus csomag verziójának 7.0.0
description: Ez a cikk bemutatja, hogyan küldhet üzeneteket egy témakörbe és fogadhat üzeneteket az előfizetésből a Python használatával.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: 4eba3ea055e78888d482927fa6eed5c7d41fa0ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98630047"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Üzenetek küldése egy Azure Service Bus témakörnek, és üzenetek fogadása az előfizetésből a témakörbe (Python)
Ez a cikk bemutatja, hogyan lehet a Python használatával üzeneteket küldeni egy Service Bus témakörből, és üzeneteket fogadni egy előfizetésből a témakörbe. 

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Kövesse a rövid útmutató lépéseit [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörbe](service-bus-quickstart-topics-subscriptions-portal.md). Jegyezze fel a kapcsolatok karakterláncát, a témakör nevét és az előfizetés nevét. Ehhez a rövid útmutatóhoz csak egy előfizetést fog használni. 
- Python 2,7 vagy újabb verzió, az [Azure Python SDK] [Azure Python Package] csomaggal. További információ: a [Python telepítési útmutatója](/azure/developer/python/azure-sdk-install).

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
    > - Cserélje le a `<NAMESPACE CONNECTION STRING>` karakterláncot a névtérhez tartozó kapcsolatok karakterlánccá.
    > - Cserélje le a `<TOPIC NAME>` nevet a témakör nevére.
    > - A helyére írja `<SUBSCRIPTION NAME>` be az előfizetés nevét a témakörre. 
3. Adjon hozzá egy metódust egyetlen üzenet küldéséhez.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    A küldő egy olyan objektum, amely ügyfélként funkcionál a létrehozott témakörhöz. Később létrehozhatja és elküldheti a függvény argumentumaként. 
4. Adjon hozzá egy metódust az üzenetek listájának elküldéséhez.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
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
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Hozzon létre egy Service Bus ügyfelet, majd egy témakör küldő objektumát üzenetek küldéséhez.

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
 
## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
Adja hozzá a következő kódot a Print utasítás után. Ez a kód folyamatosan fogad új üzeneteket, amíg az új üzenetek nem kapnak 5 ( `max_wait_time` ) másodpercet. 

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

Válassza ki a témakört az alsó ablaktáblán, és tekintse meg a témakör **Service Bus témakör** lapját. Ezen az oldalon három bejövő és három kimenő üzenetet kell látnia az **üzenetek** diagramon. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Bejövő és kimenő üzenetek":::

Ezen az oldalon, ha kijelöl egy előfizetést, a **Service Bus előfizetés** oldalra kerül. Ezen az oldalon megtekintheti az aktív üzenetek darabszámát, a kézbesítetlen üzenetek darabszámát és további információt. Ebben a példában az összes üzenet érkezett, így az aktív üzenetek száma nulla. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Aktív üzenetek száma":::

Ha kiírja a fogadási kódot, az aktív üzenetek száma 16. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Aktív üzenetek száma – nincs fogadás":::

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő dokumentációt és mintákat: 

- [Azure Service Bus a Pythonhoz készült ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - A **sync_samples** mappa olyan mintákat tartalmaz, amelyek bemutatják, hogyan kezelhető a Service Bus szinkron módon. Ebben a rövid útmutatóban ezt a módszert használta. 
    - A **async_samples** mappa olyan mintákat tartalmaz, amelyek bemutatják, hogyan lehet aszinkron módon kommunikálni a Service Busokkal. 
- [Az Azure-servicebus dokumentációja](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)