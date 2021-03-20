---
title: 'Gyors útmutató: Azure Queue Storage Client Library V12 – Python'
description: Megtudhatja, hogyan használhatja az Azure Queue Storage a Pythonhoz készült ügyféloldali kódtárat egy üzenetsor létrehozásához és az üzenetek hozzáadásához. Ebből a cikkből megtudhatja, hogyan olvashatja és törölheti az üzeneteket a várólistából. Azt is megtudhatja, hogyan törölhet egy várólistát.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-python
ms.openlocfilehash: 0eeb1333952533cdf392b93a926f256097557151
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585496"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Gyors útmutató: Azure Queue Storage a Pythonhoz készült ügyféloldali kódtár

Ismerkedés az Azure Queue Storage a Pythonhoz készült ügyféloldali kódtáraval. Az Azure Queue Storage egy olyan szolgáltatás, amely nagy mennyiségű üzenetet tárol a későbbi lekérésekhez és feldolgozáshoz. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja a programkódot az alapszintű feladatokhoz.

A Pythonhoz készült Azure Queue Storage ügyféloldali kódtár a következőre használható:

- Üzenetsor létrehozása
- Üzenetek hozzáadása egy várólistához
- Üzenetek betekintése egy várólistába
- Üzenetsor frissítése
- Üzenetek fogadása egy várólistából
- Üzenetek törlése egy várólistából
- Üzenetsor törlése

További források:

- [API-referenciadokumentáció](/python/api/azure-storage-queue/index)
- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-storage-queue/)
- [Példák](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- Azure Storage-fiók – [Storage-fiók létrehozása](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) az operációs rendszerhez – 2,7, 3,5 vagy újabb

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti a projekt előkészítésének folyamatán a Pythonhoz készült Azure Queue Storage ügyféloldali kódtár V12-es verziójának használatával.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy nevű Python-alkalmazást `queues-quickstart-v12` .

1. A konzol ablakban (például cmd, PowerShell vagy bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Váltson az újonnan létrehozott `queues-quickstart-v12` címtárra.

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>A csomag telepítése

Telepítse az Azure Blob Storage a Python-csomaghoz készült ügyféloldali kódtárat a `pip install` paranccsal.

```console
pip install azure-storage-queue
```

Ez a parancs telepíti az Azure Queue Storage ügyféloldali kódtárat a Python-csomaghoz, és az összes olyan kódtárat, amelytől függ. Ebben az esetben ez csak a Pythonhoz készült Azure Core Library.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

1. Új szövegfájl megnyitása a kódszerkesztő programban
1. `import`Utasítások hozzáadása
1. A program struktúrájának létrehozása, beleértve a nagyon egyszerű kivételek kezelését

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

1. Mentse az új fájlt `queues-quickstart-v12.py` a `queues-quickstart-v12` könyvtárba.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra. A Queue Storage háromféle típusú erőforrást kínál:

- A Storage-fiók
- A Storage-fiókban lévő üzenetsor
- Üzenetek a várólistán belül

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Üzenetsor-tárolási architektúra ábrája](./media/storage-queues-introduction/queue1.png)

A következő Python-osztályok használata az alábbi erőforrásokkal való interakcióhoz:

- [`QueueServiceClient`](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): A `QueueServiceClient` lehetővé teszi a Storage-fiók összes várólistájának kezelését.
- [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient): Az `QueueClient` osztály segítségével kezelheti és kezelheti az egyes várólistákat és az üzeneteiket.
- [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): Az `QueueMessage` osztály a várólista hívásakor visszaadott egyedi objektumokat jelöli [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) .

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő műveleteket a Pythonhoz készült Azure Queue Storage ügyféloldali kódtár használatával:

- [A kapcsolati sztring lekérése](#get-the-connection-string)
- [Üzenetsor létrehozása](#create-a-queue)
- [Üzenetek hozzáadása egy várólistához](#add-messages-to-a-queue)
- [Üzenetek betekintése egy várólistába](#peek-at-messages-in-a-queue)
- [Üzenetsor frissítése](#update-a-message-in-a-queue)
- [Üzenetek fogadása egy várólistából](#receive-messages-from-a-queue)
- [Üzenetek törlése egy várólistából](#delete-messages-from-a-queue)
- [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A következő kód lekéri a Storage-fiókhoz tartozó kapcsolatok karakterláncát. A rendszer tárolja a hálózati karakterláncot a [tárolási kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóban.

Adja hozzá ezt a kódot a `try` blokkon belül:

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

Döntse el az új üzenetsor nevét. A következő kód egy UUID értéket fűz hozzá a várólista neveként, hogy az egyedi legyen.

> [!IMPORTANT]
> A várólisták nevei csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3 – 63 karakter hosszúnak kell lennie. További információ: a [várólisták és a metaadatok elnevezése](/rest/api/storageservices/naming-queues-and-metadata).

Hozza létre a osztály egy példányát [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient) . Ezután hívja meg a [`create_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) metódust az üzenetsor létrehozásához a Storage-fiókban.

Adja hozzá ezt a kódot a blokk végéhez `try` :

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

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása egy várólistához

A következő kódrészlet üzeneteket hoz létre a várólistához a metódus meghívásával [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) . Emellett a [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) harmadik hívásból is menti a visszaadott értéket `send_message` . A az `saved_message` üzenet tartalmának későbbi, a programban való frissítésére szolgál.

Adja hozzá ezt a kódot a blokk végéhez `try` :

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Üzenetek betekintése egy várólistába

A metódus meghívásával megtekintheti a várólistában lévő üzeneteket [`peek_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) . Ez a metódus egy vagy több üzenetet kér le a várólista elejéről, de nem módosítja az üzenet láthatóságát.

Adja hozzá ezt a kódot a blokk végéhez `try` :

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Üzenetsor frissítése

Az üzenet tartalmának frissítéséhez hívja meg a [`update_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metódust. Ez a metódus módosíthatja az üzenet láthatósági időtúllépését és tartalmát. Az üzenet tartalmának UTF-8 kódolású karakterláncnak kell lennie, amely akár 64 KB méretű is lehet. Az új tartalommal együtt adja át a kódban korábban mentett üzenet értékeit. Az `saved_message` értékek határozzák meg, hogy melyik üzenetet kell frissíteni.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából

A korábban hozzáadott üzenetek letöltése a metódus meghívásával [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) .

Adja hozzá ezt a kódot a blokk végéhez `try` :

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Üzenetek törlése egy várólistából

Üzenetek törlése a sorból a fogadás és a feldolgozás után. Ebben az esetben a feldolgozás csak az üzenet megjelenítését jeleníti meg a konzolon.

Az alkalmazás a felhasználói bevitel szüneteltetését hívja `input` meg, mielőtt feldolgozza és törli az üzeneteket. A törlés előtt ellenőrizze, hogy az erőforrások megfelelően lettek-e létrehozva a [Azure Portal](https://portal.azure.com) . A nem explicit módon törölt üzenetek később ismét láthatóvá válnak a várólistában, hogy egy másik lehetőség is feldolgozza őket.

Adja hozzá ezt a kódot a blokk végéhez `try` :

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

A következő kód törli az alkalmazás által létrehozott erőforrásokat az üzenetsor törlésével a [`delete_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) metódus használatával.

Adja hozzá ezt a kódot a blokk végéhez `try` , és mentse a fájlt:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre és tesz hozzá egy Azure-várólistához. A kód felsorolja a várólistán lévő üzeneteket, majd lekéri és törli őket, mielőtt véglegesen törölné a várólistát.

A konzol ablakban navigáljon a fájlt tartalmazó könyvtárhoz `queues-quickstart-v12.py` , majd futtassa az alábbi parancsot az `python` alkalmazás futtatásához.

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

Ha az alkalmazás szünetelteti az üzenetek fogadását, akkor a [Azure Portalban](https://portal.azure.com)keresse meg a Storage-fiókját. Ellenőrizze, hogy az üzenetek szerepelnek-e a várólistán.

Kattintson a `Enter` kulcsra az üzenetek fogadásához és törléséhez. Amikor a rendszer kéri, nyomja le `Enter` ismét a gombot a várólista törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre üzenetsor-t, és hogyan adhat hozzá üzeneteket a Python-kóddal. Ezután megtanulta az üzenetek betekintését, beolvasását és törlését. Végezetül megtanulta, hogyan törölhet egy üzenetsor-várólistát.

Oktatóanyagok, minták, gyors indítás és egyéb dokumentáció:

> [!div class="nextstepaction"]
> [Azure Python-fejlesztőknek](/azure/python/)

- További információ: az [Azure Storage kódtárai a Pythonhoz](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
- További Azure Queue Storage-példákat az [azure Queue Storage a Pythonhoz készült ügyféloldali kódtárat](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)ismertető témakörben talál.
