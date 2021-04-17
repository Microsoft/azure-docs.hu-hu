---
title: 'Rövid útmutató: Azure Queue Storage kódtár v12- Python-kódtárának létrehozása'
description: Megtudhatja, hogyan használhatja Azure Queue Storage Pythonhoz készült ügyféloldali kódtár v12-t üzenetsor létrehozásához és üzenetek hozzáadásához. Ezután megtudhatja, hogyan olvashatja és törölheti az üzeneteket az üzenetsorból. Azt is megtudhatja, hogyan törölhet egy üzenetsort.
author: twooley
ms.author: twooley
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 68f68c32b160757e82d59b1dd5ee4847a06ec698
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534367"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Rövid útmutató: Azure Queue Storage Pythonhoz készült v12-es ügyféloldali kódtár létrehozása

Első lépések a Azure Queue Storage Pythonhoz készült v12 ügyféloldali kódtárával. Azure Queue Storage egy nagy számú üzenet tárolására szolgáló szolgáltatás későbbi lekéréshez és feldolgozáshoz. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz szükséges példakódot.

Használja a Azure Queue Storage Pythonhoz készült v12 ügyféloldali kódtárat a következőre:

- Üzenetsor létrehozása
- Üzenetek hozzáadása üzenetsorhoz
- Betekintés az üzenetsor üzeneteibe
- Üzenet frissítése egy üzenetsorban
- Üzenetek fogadása üzenetsorból
- Üzenetek törlése egy üzenetsorból
- Üzenetsor törlése

További források:

- [API-referenciadokumentáció](/python/api/azure-storage-queue/index)
- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Csomag (Python-csomagindex)](https://pypi.org/project/azure-storage-queue/)
- [Példák](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés [– hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/)
- Azure Storage-fiók [– tárfiók létrehozása](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) az operációs rendszerhez – 2.7 vagy 3.6+

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti egy projekt előkészítésén a Pythonhoz készült Azure Queue Storage ügyféloldali kódtárával való munkához.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy nevű `queues-quickstart-v12` Python-alkalmazást.

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Váltson az újonnan létrehozott `queues-quickstart-v12` könyvtárra.

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>A csomag telepítése

Telepítse a Azure Blob Storage Python-csomaghoz készült ügyféloldali kódtárat az `pip install` paranccsal.

```console
pip install azure-storage-queue
```

Ez a parancs telepíti Azure Queue Storage Python-csomaghoz készült ügyféloldali kódtárat és az összes kódtárat, amelyektől függ. Ebben az esetben ez csak a Pythonhoz készült Azure alapkönyvtár.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

1. Új szövegfájl megnyitása a kódszerkesztőben
1. Utasítások `import` hozzáadása
1. A program struktúrájának létrehozása, beleértve a nagyon egyszerű kivételkezelést

    A kód a következő:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Mentse az új fájlt a `queues-quickstart-v12.py` `queues-quickstart-v12` könyvtárban a következőként: .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. Az üzenetsor-üzenetek mérete legfeljebb 64 KB lehet. Az üzenetsorok több millió üzenetet tartalmazhatnak, a tárfiókok maximális kapacitásán belül. Az üzenetsorokat gyakran használják hátralékok létrehozásához aszinkron feldolgozáshoz. Queue Storage háromféle erőforrást kínál:

- A tárfiók
- Üzenetsor a tárfiókban
- Üzenetsoron belüli üzenetek

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![A Queue Storage architektúrájának ábrája](./media/storage-queues-introduction/queue1.png)

Az alábbi Python-osztályokkal használhatja ezeket az erőforrásokat:

- [`QueueServiceClient`](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): A `QueueServiceClient` lehetővé teszi a tárfiókban lévő összes üzenetsor kezelését.
- [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient): A osztály lehetővé teszi az egyes üzenetsorok és üzeneteik `QueueClient` kezelését és kezelését.
- [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): A `QueueMessage` osztály az üzenetsoron való híváskor visszaadott [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) egyes objektumokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek a következő műveleteket mutatják be a Pythonhoz készült Azure Queue Storage kódtárával:

- [A kapcsolati sztring lekérése](#get-the-connection-string)
- [Üzenetsor létrehozása](#create-a-queue)
- [Üzenetek hozzáadása üzenetsorhoz](#add-messages-to-a-queue)
- [Betekintés az üzenetsor üzeneteibe](#peek-at-messages-in-a-queue)
- [Üzenet frissítése egy üzenetsorban](#update-a-message-in-a-queue)
- [Üzenetek fogadása üzenetsorból](#receive-messages-from-a-queue)
- [Üzenetek törlése egy üzenetsorból](#delete-messages-from-a-queue)
- [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A következő kód lekéri a tárfiók kapcsolati sztringét. A kapcsolati sztringet a rendszer a Tároló kapcsolati sztring konfigurálása szakaszban létrehozott környezeti [változót tárolja.](#configure-your-storage-connection-string)

Adja hozzá ezt a kódot a `try` blokkhoz:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Üzenetsor létrehozása

Döntse el az új üzenetsor nevét. Az alábbi kód egy UUID-értéket fűz hozzá az üzenetsor nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> Az üzenetsorok nevei csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3–63 karakter hosszúságúnak kell lennie. További információ: [Üzenetsorok és metaadatok elnevezése.](/rest/api/storageservices/naming-queues-and-metadata)

Hozza létre a osztály egy [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient) példányát. Ezután hívja meg a [`create_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) metódust az üzenetsor létrehozásához a tárfiókban.

Adja hozzá ezt a kódot a blokk `try` véghez:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása üzenetsorhoz

Az alábbi kódrészlet üzeneteket ad hozzá az üzenetsorhoz a metódus [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) hívásával. A harmadik hívás által [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) visszaadott et is `send_message` menti. A `saved_message` az üzenet tartalmának frissítésére lesz használva a program későbbi részen.

Adja hozzá ezt a kódot a blokk `try` véghez:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Betekintés az üzenetsor üzeneteibe

A metódus hívásával bepillantás az üzenetsor [`peek_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) üzeneteibe. Ez a metódus egy vagy több üzenetet ad vissza az üzenetsor elejéről, de nem módosítja az üzenet láthatóságát.

Adja hozzá ezt a kódot a blokk `try` véghez:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Üzenetsor üzenetének frissítése

Frissítse egy üzenet tartalmát a metódus [`update_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) hívásával. Ez a metódus módosíthatja az üzenetek láthatósági időkorlátját és tartalmát. Az üzenet tartalmának UTF-8 kódolású sztringnek kell lennie, amely legfeljebb 64 KB méretű lehet. Az új tartalommal együtt adja meg a kódban korábban mentett üzenet értékeit. Az `saved_message` értékek azonosítják, hogy melyik üzenetet kell frissíteni.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása üzenetsorból

Töltse le a korábban hozzáadott üzeneteket a metódus [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) hívásával.

Adja hozzá ezt a kódot a blokk `try` véghez:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Üzenetek törlése egy üzenetsorból

Üzenetek törlése az üzenetsorból azok fogadása és feldolgozása után. Ebben az esetben a feldolgozás csak az üzenetet jeleníti meg a konzolon.

Az alkalmazás a hívással szünetelteti a felhasználói bevitelt, mielőtt feldolgozza és törli `input` az üzeneteket. A fiókban [Azure Portal,](https://portal.azure.com) hogy az erőforrások megfelelően, a törlés előtt létrejöttek-e. A explicit módon nem törölt üzenetek végül ismét láthatóvá válnak az üzenetsorban, hogy egy másik lehetőség legyen azok feldolgozására.

Adja hozzá ezt a kódot a blokk `try` véghez:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Üzenetsor törlése

A következő kód törli az alkalmazás által létrehozott erőforrásokat az üzenetsor a metódussal való [`delete_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) törlésével.

Adja hozzá ezt a kódot a blokk `try` véghez, és mentse a fájlt:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre és ad hozzá egy Azure-üzenetsorhoz. A kód listázza az üzenetsorban lévő üzeneteket, majd lekéri és törli őket, mielőtt végül töröl egy üzenetsort.

A konzolablakban keresse meg a fájlt tartalmazó könyvtárat, majd futtassa az alkalmazást a következő `queues-quickstart-v12.py` `python` paranccsal.

```console
python queues-quickstart-v12.py
```

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Queue Storage client library v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Amikor az alkalmazás szünetel, mielőtt üzeneteket kap, ellenőrizze a tárfiókot a [Azure Portal.](https://portal.azure.com) Ellenőrizze, hogy az üzenetek az üzenetsorban vannak-e.

Nyomja le `Enter` a billentyűt az üzenetek fogadására és törlésére. Amikor a rendszer kéri, nyomja le újra a billentyűt az üzenetsor `Enter` törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre üzenetsort, és hogyan adhat hozzá üzeneteket Python-kóddal. Ezután megtanulta, hogyan lehet betekintésbe, lekérni és törölni az üzeneteket. Végül megtanulta, hogyan törölhet üzenetsort.

Oktatóanyagok, minták, rövid útmutatók és egyéb dokumentáció:

> [!div class="nextstepaction"]
> [Azure Python-fejlesztőknek](/azure/python/)

- További tudnivalókért lásd a [Pythonhoz készült Azure Storage-kódtárakat.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
- A mintaalkalmazásokkal Azure Queue Storage lásd: Azure Queue Storage Pythonhoz készült [v12-es](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)ügyféloldali kódtár – minták.
