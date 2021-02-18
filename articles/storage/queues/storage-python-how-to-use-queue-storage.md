---
title: Az Azure Queue Storage használata a Pythonból
description: Ismerje meg, hogyan hozhat létre és törölhet várólistákat az Azure Queue Storage Pythonból, és hogyan szúrhat be, kérhet le és törölhet üzeneteket.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 02/16/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 8c30d05cf49162155f84961e1fd8a32361444ba5
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653261"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Az Azure Queue Storage használata a Pythonból

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Áttekintés

Ez a cikk az Azure Queue Storage szolgáltatás használatának gyakori forgatókönyveit mutatja be. A tárgyalt forgatókönyvek közé tartozik az üzenetsor-üzenetek beszúrása, bepillantása, beolvasása és törlése. A várólisták létrehozásához és törléséhez szükséges kód is szerepel.

A cikkben szereplő példák Pythonban íródtak, és a [Pythonhoz készült Azure Queue Storage ügyféloldali kódtárat](https://github.com/azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)használják. A várólistákkal kapcsolatos további információkért tekintse meg a [következő lépések](#next-steps) szakaszt.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>A Pythonhoz készült Azure Storage SDK letöltése és telepítése

A [Pythonhoz készült Azure Storage SDK](https://github.com/azure/azure-storage-python) használatához a Python v 2.7, v 3.3 vagy újabb verzió szükséges.

### <a name="install-via-pypi"></a>Telepítés a PyPI-on keresztül

A Python Package index (PyPI) használatával történő telepítéshez írja be a következőt:

# <a name="python-v12"></a>[Python V12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Ha a Python v 0.36 vagy korábbi verzióhoz készült Azure Storage SDK-ból frissít, távolítsa el a régebbi SDK-t a `pip uninstall azure-storage` legújabb csomag telepítése előtt.

Alternatív telepítési módszerekért lásd: [a Pythonhoz készült Azure SDK](https://github.com/Azure/Azure-SDK-for-Python).

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Az alkalmazás konfigurálása Queue Storage eléréséhez

# <a name="python-v12"></a>[Python V12](#tab/python)

Az [`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient) objektum lehetővé teszi, hogy a várólista segítségével működjön. Adja hozzá a következő kódot bármely olyan Python-fájlhoz, amelyben programozottan szeretné elérni az Azure-üzenetsor-t:

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Az [`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true) objektum lehetővé teszi a várólistákkal való munkavégzést. A következő kód létrehoz egy `QueueService` objektumot. Adja hozzá a következő kódot bármely olyan Python-fájlhoz, amelyben programozottan szeretné elérni az Azure Storage-t:

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

A `os` csomag támogatást nyújt egy környezeti változó lekéréséhez. A `uuid` csomag támogatást nyújt a várólista nevének egyedi azonosítójának létrehozásához.

## <a name="create-a-queue"></a>Üzenetsor létrehozása

A rendszer beolvassa a kapcsolatok karakterláncát a `AZURE_STORAGE_CONNECTION_STRING` korábban beállított környezeti változóból.

# <a name="python-v12"></a>[Python V12](#tab/python)

A következő kód egy objektumot hoz létre `QueueClient` a Storage-kapcsolatok karakterláncának használatával.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

A következő kód egy objektumot hoz létre `QueueService` a Storage-kapcsolatok karakterláncának használatával.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

Az Azure üzenetsor-üzenetei szövegként vannak tárolva. Ha bináris adatok tárolására van szüksége, állítson be Base64 kódolást és dekódolási függvényeket, mielőtt üzenetet hozna a várólistába.

# <a name="python-v12"></a>[Python V12](#tab/python)

Konfigurálja a Base64 kódolást és a dekódolási függvényeket az ügyfél-objektum létrehozásakor.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Base64 kódolás és dekódolási függvények konfigurálása Queue Storage objektumon.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="insert-a-message-into-a-queue"></a>Üzenet beszúrása egy üzenetsorba

# <a name="python-v12"></a>[Python V12](#tab/python)

Ha üzenetet szeretne beszúrni egy várólistába, használja a [`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) metódust.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Egy üzenet üzenetsorbe való beszúrásához használja az [`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) metódust egy új üzenet létrehozásához, és adja hozzá a várólistához.

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

## <a name="peek-at-messages"></a>Betekintés üzenetekben

# <a name="python-v12"></a>[Python V12](#tab/python)

Az üzenetekben anélkül is betekintést nyerhet, hogy a metódus meghívásával eltávolítja őket a várólistából [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) . Alapértelmezés szerint ez a metódus egyetlen üzenetbe van bepillantva.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Az üzenetekben anélkül is betekintést nyerhet, hogy a metódus meghívásával eltávolítja őket a várólistából [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#peek-messages-queue-name--num-messages-none--timeout-none-) . Alapértelmezés szerint ez a metódus egyetlen üzenetbe van bepillantva.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Üzenetsorban található üzenet tartalmának módosítása

Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet egy feladatot jelöl, ezt a funkciót használhatja a feladat állapotának frissítéséhez.

# <a name="python-v12"></a>[Python V12](#tab/python)

A következő kód a [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metódust használja egy üzenet frissítéséhez. A láthatósági időkorlát 0 értékre van állítva, ami azt jelenti, hogy az üzenet azonnal megjelenik, és a tartalom frissül.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

A következő kód a [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) metódust használja egy üzenet frissítéséhez. A láthatósági időkorlát 0 értékre van állítva, ami azt jelenti, hogy az üzenet azonnal megjelenik, és a tartalom frissül.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

Megbecsülheti egy üzenetsorban található üzenetek számát.

# <a name="python-v12"></a>[Python V12](#tab/python)

A [get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) metódus a várólista-tulajdonságokat adja vissza, beleértve a értéket `approximate_message_count` .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

A [`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-queue-metadata-queue-name--timeout-none-) metódus a várólista tulajdonságait adja vissza, beleértve a következőket: `approximate_message_count` .

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

Az eredmény csak hozzávetőleges, mert az üzenetek hozzáadhatók vagy eltávolíthatók, miután a szolgáltatás válaszol a kérelmére.

## <a name="dequeue-messages"></a>Üzenetek várólistára helyezése

Üzenetsor eltávolítása két lépésben. Ha a kód nem tud feldolgozni egy üzenetet, ez a kétlépéses folyamat biztosítja, hogy ugyanazt az üzenetet kapja, és próbálkozzon újra. `delete_message`Az üzenet sikeres feldolgozását követően hívja meg a hívást.

# <a name="python-v12"></a>[Python V12](#tab/python)

[Receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-)hívásakor a rendszer alapértelmezés szerint a következő üzenetet kapja a várólistában. A visszaadott üzenet a `receive_messages` várólistából beolvasott más kódokba is láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívnia a [delete_messaget](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-)is.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[Get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)hívásakor a rendszer alapértelmezés szerint a következő üzenetet kapja a várólistában. A visszaadott üzenet a `get_messages` várólistából beolvasott más kódokba is láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívnia a [delete_messaget](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-message-queue-name--message-id--pop-receipt--timeout-none-)is.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket.

# <a name="python-v12"></a>[Python V12](#tab/python)

A következő kódrészlet a metódus használatával kéri le az [`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) üzeneteket a kötegekben. Ezután az egyes kötegekben lévő üzeneteket egy beágyazott hurok használatával dolgozza fel `for` . Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

A következő kódrészlet a [`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) metódus használatával 16 üzenetet kap egy hívásban. Ezután az összes üzenetet feldolgozza egy `for` hurok használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>Üzenetsor törlése

# <a name="python-v12"></a>[Python V12](#tab/python)

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) metódust.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-queue-queue-name--fail-not-exist-false--timeout-none-) metódust.

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte Queue Storage alapjait, kövesse az alábbi hivatkozásokat további információért.

- [Azure Queue Storage Python API-referenciák](/python/api/azure-storage-queue)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
- [Az Azure Storage REST API referenciája](/rest/api/storageservices/)
