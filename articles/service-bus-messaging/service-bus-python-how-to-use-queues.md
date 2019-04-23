---
title: Az Azure Service Bus-üzenetsorok használata pythonnal |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Service Bus-üzenetsorok, a Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 622b1f6f6a852251c07c5576ed10cd76adbf5231
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795015"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Service Bus-üzenetsorok használata pythonnal

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ebben az oktatóanyagban megismerheti, hogyan hozhat létre a Python-alkalmazások üzeneteket küldeni, illetve üzeneteket fogadhat a Service Bus-üzenetsorba. 

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja a [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Kövesse lépéseket a [egy Service Bus-üzenetsor létrehozása az Azure portal](service-bus-quickstart-portal.md) cikk.
    1. Olvassa el a gyors **áttekintése** Service Bus **üzenetsorok**. 
    2. Hozzon létre egy Service Bus **névtér**. 
    3. Első a **kapcsolati karakterlánc**. 

        > [!NOTE]
        > Létrehozhat egy **várólista** a Service Bus névterében ebben az oktatóanyagban a Python használatával. 
1. Telepítse a Pythont vagy a [Python Azure Service Bus-csomag][Python Azure Service Bus package], tekintse meg a [Python-telepítési útmutató](../python-how-to-install.md). Tekintse meg a Service Bus Python SDK teljes dokumentációját [Itt](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A **ServiceBusClient** objektum lehetővé teszi, hogy az üzenetsorok. Adja hozzá a következő kódot bármely Python-fájlt, amelyben a kívánt programozott módon érheti el a Service Bus tetején:

```python
from azure.servicebus import ServiceBusClient
```

Az alábbi kód létrehoz egy **ServiceBusClient** objektum. Cserélje le `mynamespace`, `sharedaccesskeyname`, és `sharedaccesskey` a névtér, közös hozzáférésű jogosultságkód (SAS-) kulcs nevét és értékét.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Tekintheti meg az értékeket a SAS-kulcs nevét és értékét az [az Azure portal] [ Azure portal] kapcsolati információkat, vagy a Visual Studio **tulajdonságok** panelen, amikor a szolgáltatás kiválasztása A Server Explorer (ahogyan az előző szakaszban látható) Bus-névteret.

```python
sb_client.create_queue("taskqueue")
```

A `create_queue` módszer is támogatja a további lehetőségeket, amelyek segítségével felülírhatja az alapértelmezett várólista beállításait, például az élettartam (TTL) vagy a várólista maximális hossza üzenet ideje engedélyezése. Az alábbi példa a várólista maximális mérete 5 GB-os, és az élettartam értéke 1 percre állítja be:

```python
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

sb_client.create_queue("taskqueue", queue_options)
```

További információkért lásd: [Azure Service Bus Python-dokumentáció](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Üzenet küldése a Service Bus-üzenetsorba, az alkalmazás meghívja a `send` metódust a `ServiceBusClient` objektum.

Az alábbi példa bemutatja, hogyan tesztüzenet küldése az üzenetsorba nevű `taskqueue` használatával `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(Message("Message"))
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Kvóták kapcsolatos további információkért lásd: [Service Bus-kvóták][Service Bus quotas].

További információkért lásd: [Azure Service Bus Python-dokumentáció](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Egy üzenetsor használatával fogadása a `get_receiver` metódust a `ServiceBusService` objektum:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

## Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

További információkért lásd: [Azure Service Bus Python-dokumentáció](/python/api/overview/azure/servicebus?view=azure-python).


Üzenetek törlése az üzenetsorból, mikor olvasott a paraméter `peek_lock` értékre van állítva **hamis**. Olvashat (betekintési) és az üzenet zárolása törlése az üzenetsorból paraméterének beállításával nélkül `peek_lock` való **igaz**.

Olvasása és törlése a fogadás művelet részeként viselkedését a legegyszerűbb modell, és a leginkább forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet egy hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet, jelölte, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

Ha a `peek_lock` paraméter értéke **igaz**, a receive két szakaszból álló művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második fázisa meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), a **törlése** metódust a **üzenet** az objektum. A **törlése** módszert fog jelölje meg az üzenetet, és távolítsa el az üzenetsorból.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az **feloldásához** metódust a **üzenet** objektum. Ennek hatására a Service Bus feloldja az az üzenetsorban lévő üzenet zárolását, és tegye elérhetővé számára az azonos fogyasztó alkalmazás általi vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy zárolva van, az üzenetsorban lévő üzenethez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását lesz, és adja meg elérhető az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a **törlése** módszert hívja meg, akkor az üzenet újból kézbesítve lesz az alkalmazás amikor újraindul. Ezt gyakran nevezik **feldolgozása során legalább egyszer**, vagyis minden üzenetet legalább egyszer dolgozza, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran az üzenet **MessageId** tulajdonságával érhető el, amely állandó marad a kézbesítési kísérletek során.

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, tekintse meg ezeket a további cikkeket.

* [Üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

