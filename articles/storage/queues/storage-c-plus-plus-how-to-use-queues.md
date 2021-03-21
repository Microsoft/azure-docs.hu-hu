---
title: A Queue Storage (C++) használata – Azure Storage
description: Ismerje meg, hogyan használhatja a Queue Storage szolgáltatást az Azure-ban. A minták C++ nyelven íródnak.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 07/16/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 44d64c54049c02b6602f01b97effcc33b03dbcfe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97591327"
---
# <a name="how-to-use-queue-storage-from-c"></a>A Queue Storage használata C++-szal

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés

Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure Queue Storage szolgáltatás használatával. A minták C++ nyelven íródtak, és az [Azure Storage ügyféloldali kódtárat használják a C++ nyelvhez](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). A tárgyalt forgatókönyvek közé tartozik például a várólista-üzenetek **beszúrása**, **bepillantása**, **beolvasása** és **törlése** , valamint a **várólisták létrehozása és törlése**.

> [!NOTE]
> Ez az útmutató az Azure Storage ügyféloldali függvénytárát célozza meg a C++ v 1.0.0 vagy újabb verzióra. Az ajánlott verzió az Azure Storage ügyféloldali kódtár v 2.2.0, amely a [NuGet](https://www.nuget.org/packages/wastorage) vagy a [githubon](https://github.com/Azure/azure-storage-cpp/)keresztül érhető el.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása

Ebben az útmutatóban a tárolási funkciókat fogja használni, amelyek egy C++-alkalmazáson belül futtathatók.

Ehhez telepítenie kell az Azure Storage ügyféloldali kódtárat a C++-hoz, és létre kell hoznia egy Azure Storage-fiókot az Azure-előfizetésében.

<!-- docutune:casing "Getting Started on Linux" -->

Ha az Azure Storage ügyféloldali kódtárat szeretné telepíteni a C++ nyelvre, a következő módszereket használhatja:

- **Linux:** Kövesse az [Azure Storage ügyféloldali függvénytárában](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) , a következő témakörben ismertetett utasításokat: első lépések Linuxon lapon.
- **Windows:** Windows rendszeren használja a [vcpkg](https://github.com/microsoft/vcpkg) -t a függőség-kezelőként. [Az inicializáláshoz kövesse a rövid](https://github.com/microsoft/vcpkg#quick-start) útmutatót `vcpkg` . Ezután használja a következő parancsot a könyvtár telepítéséhez:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

A következő útmutatóból megtudhatja, hogyan hozhatja létre a forráskódot, és hogyan exportálhat NuGet a [readme](https://github.com/Azure/azure-storage-cpp#download--install) fájlban.

## <a name="configure-your-application-to-access-queue-storage"></a>Az alkalmazás konfigurálása Queue Storage eléréséhez

Adja hozzá a következő include utasításokat a C++ fájl elejéhez, ahol az Azure Storage API-kat szeretné használni a várólisták eléréséhez:

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage kapcsolati sztring beállítása

Az Azure Storage-ügyfelek tárolási kapcsolódási karakterláncot használnak az adatkezelési szolgáltatások eléréséhez szükséges végpontok és hitelesítő adatok tárolásához. Ügyfélalkalmazás esetén a tárolási kapcsolati karakterláncot a következő formátumban kell megadnia, a Storage-fiók neve és a (z) és értékek [Azure Portal](https://portal.azure.com) lévő Storage-fiókhoz tartozó Storage-hozzáférési kulcs használatával `AccountName` `AccountKey` . További információ a Storage-fiókokról és a hozzáférési kulcsokról: [Tudnivalók az Azure Storage-fiókokról](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Az alkalmazás helyi Windows-számítógépeken való teszteléséhez használhatja a [Azurite Storage emulatort](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). A Azurite egy olyan segédprogram, amely szimulálja az Azure Blob Storage és Queue Storage a helyi fejlesztési gépen. A következő példa bemutatja, hogyan deklarálhat statikus mezőt a helyi Storage Emulatorhoz használható kapcsolati sztring tárolására:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

A Azurite megkezdéséhez lásd: [a Azurite-emulátor használata a helyi Azure Storage-fejlesztéshez](../common/storage-use-azurite.md).

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.

## <a name="retrieve-your-connection-string"></a>A kapcsolati sztring lekérése

A `cloud_storage_account` Storage-fiók adatainak ábrázolásához használhatja az osztályt. A Storage-fiók adatainak a Storage-kapcsolódási karakterláncból való lekéréséhez használhatja a `parse` metódust.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Útmutató: üzenetsor létrehozása

Az `cloud_queue_client` objektumok lehetővé teszi a várólisták hivatkozási objektumainak lekérését. A következő kód létrehoz egy `cloud_queue_client` objektumot.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Használja az `cloud_queue_client` objektumot a használni kívánt üzenetsor hivatkozásának beolvasásához. Ha nem létezik, létrehozhat egy várólistát.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: üzenet beszúrása egy várólistába

Ha egy üzenetet egy meglévő várólistába szeretne beszúrni, először hozzon létre egy újat `cloud_queue_message` . Ezután hívja meg a `add_message` metódust. A `cloud_queue_message` létrehozható egy karakterláncból (UTF-8 formátumban) vagy egy bájtos tömbből. Az alábbi kód létrehoz egy várólistát (ha nem létezik), és beszúrja az üzenetet `Hello, World` :

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: betekintés a következő üzenetbe

A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistába `peek_message` .

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: várólistán lévő üzenet tartalmának módosítása

Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Elmenti az üzenethez társított feladat állapotát, és az ügyfél számára további egy percet biztosít az üzenet használatának folytatására. Ezzel a technikával nyomon követheti a várólista-üzenetek többlépéses munkafolyamatait anélkül, hogy az elejétől kellene kezdenie, ha a feldolgozási lépés hardverhiba vagy szoftver meghibásodása miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel n alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: a következő üzenet elküldése

A kód két lépésben elvégez egy üzenetsor-várólistán lévő üzenetet. A hívásakor `get_message` a következő üzenet jelenik meg egy várólistában. A visszaadott üzenet a `get_message` várólistából beolvasott más kódokba is láthatatlanná válik. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívni a következőt is: `delete_message` . Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód meghívása `delete_message` közvetlenül az üzenet feldolgozása után történik.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-use-additional-options-for-dequeuing-messages"></a>Útmutató: a dequeuing üzenetek további beállításainak használata

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. A következő példában a metódus használatával `get_messages` 20 üzenetet kap egy hívásban. Ezután az összes üzenetet feldolgozza egy `for` hurok használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az öt perc egyszerre indul el az összes üzenethez, így a hívása óta öt perc elteltével a `get_messages` rendszer a nem törölt üzeneteket ismét láthatóvá válik.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Útmutató: a várólista hosszának beolvasása

Megbecsülheti egy üzenetsorban található üzenetek számát. A `download_attributes` metódus a várólista tulajdonságait adja vissza, beleértve az üzenetek darabszámát is. A `approximate_message_count` metódus lekéri az üzenetek hozzávetőleges számát a várólistában.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Útmutató: üzenetsor törlése

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja `delete_queue_if_exists` meg a metódust a várólista-objektumon.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte Queue Storage alapjait, kövesse az alábbi hivatkozásokat, és ismerkedjen meg az Azure Storage szolgáltatással.

- [A Blob Storage használata a C++-ból](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
- [A Table Storage használata a C++-ból](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Azure Storage-erőforrások listázása C++ nyelven](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Azure Storage ügyféloldali kódtár a C++-hoz – dokumentáció](https://azure.github.io/azure-storage-cpp)
- [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
