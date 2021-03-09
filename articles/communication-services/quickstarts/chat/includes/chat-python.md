---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 9e83203e937d794451dfb91fe0403117df72c8c0
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489701"
---
## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt ügyeljen a következőre:

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört.
- A [Python](https://www.python.org/downloads/) telepítése
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../../create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell jegyeznie az erőforrás- **végpontot**
- [Felhasználói hozzáférési jogkivonat](../../access-tokens.md). Ügyeljen arra, hogy a hatókört a "csevegés" értékre állítsa, és jegyezze fel a jogkivonat karakterláncát, valamint a userId karakterláncot.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Szövegszerkesztő használatával hozzon létre egy **Start-chat.py** nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a program struktúráját, beleértve az alapszintű kivételek kezelését. Ehhez a rövid útmutatóhoz tartozó forráskódot a következő részekben adja hozzá ehhez a fájlhoz.

```python
import os
# Add required client library components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Az ügyféloldali kódtár telepítése

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Pythonhoz készült Azure kommunikációs szolgáltatások csevegési függvénytárának főbb funkcióit.

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozásához, lekéréséhez és törléséhez. |
| ChatThreadClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére |

## <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása

Csevegési ügyfél létrehozásához használja a kommunikációs szolgáltatás végpontját, és a `Access Token` rendszer az előfeltételként szükséges lépések részeként generálta. További információ a [felhasználói hozzáférési tokenekről](../../access-tokens.md).

Ez a rövid útmutató nem fedi le a csevegési alkalmazás jogkivonatait kezelő szolgáltatási szintet, de ajánlott. További részletek a [csevegési architektúrával](../../../concepts/chat/concepts.md) kapcsolatban az alábbi dokumentációban olvashatók.

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential, CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

`create_chat_thread`Csevegési szál létrehozásához használja a metódust.

- A használatával `topic` adjon meg egy téma témakört; A témakör a funkció használatával frissíthető a csevegési szál létrehozása után `update_thread` .
- A a `thread_participants` `ChatThreadParticipant` csevegési szálba felvenni kívánt elemek listázásához használja `ChatThreadParticipant` a `CommunicationUserIdentifier` típust `user` , amelyet a [felhasználó létrehozása](../../access-tokens.md#create-an-identity) után kapott.
- `repeatability_request_id`A paranccsal irányíthatja a kérést, hogy megismételhető. Az ügyfél többször is elvégezheti a kérést ugyanazzal az ismételhetőségi kéréssel, és visszaállíthatja a megfelelő választ anélkül, hogy a kiszolgáló többször is végrehajtja a kérést.

A válasz az `chat_thread_client` újonnan létrehozott csevegési szálon végez műveleteket, például a résztvevők hozzáadását a csevegési szálhoz, üzenet küldéséhez, üzenet törléséhez stb. Egy olyan `thread_id` tulajdonságot tartalmaz, amely a csevegési szál egyedi azonosítója.

#### <a name="without-repeatability-request-id"></a>Ismételhetőség nélkül – kérelem-azonosító
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

chat_thread_client = chat_client.create_chat_thread(topic, participants)
```

#### <a name="with-repeatability-request-id"></a>Ismételhetőséggel – kérelem-azonosító
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, participants, repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása
A `get_chat_thread_client` metódus egy szál-ügyfelet ad vissza egy már létező szálhoz. A művelet végrehajtásához használható a létrehozott szálon: résztvevők hozzáadása, üzenet küldése stb. thread_id a meglévő csevegési szál egyedi azonosítója.

```python
thread_id = chat_thread_client.thread_id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="list-all-chat-threads"></a>Az összes csevegési szál listázása
A `list_chat_threads` metódus egy típusú iterációt ad vissza `ChatThreadInfo` . Az összes csevegési szál listázására használható.

- Ezzel `start_time` a beállítással adhatja meg a legkorábbi időpontot, hogy a csevegési szálakat akár be lehessen olvasni.
- Ezzel `results_per_page` a beállítással adhatja meg, hogy legfeljebb hány csevegési szálat ad vissza oldalanként.

```python
from datetime import datetime, timedelta
import pytz

start_time = datetime.utcnow() - timedelta(days=2)
start_time = start_time.replace(tzinfo=pytz.utc)
chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)

for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        # Iterate over all chat threads
        print("thread id:", chat_thread_info.id)
```

## <a name="delete-a-chat-thread"></a>Csevegési szál törlése
A a `delete_chat_thread` csevegési szál törlésére szolgál.

- `thread_id`A segítségével megadhatja egy meglévő csevegési szál thread_idét, amelyet törölni kell

```python
thread_id = chat_thread_client.thread_id
chat_client.delete_chat_thread(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

A `send_message` metódus használatával küldhet üzenetet egy imént létrehozott csevegési szálra, amelyet thread_id azonosít.

- `content`A csevegési üzenet tartalmának megadásához használja a következőt:
- `chat_message_type`Az üzenet tartalomtípusának megadásához használja a következőt:. A lehetséges értékek a következők: "text" és "HTML"; Ha nincs megadva az alapértelmezett "text" érték, a rendszer hozzárendeli.
- A használatával `sender_display_name` adja meg a feladó megjelenítendő nevét;

A válasz "id" típusú `str` , amely az üzenet egyedi azonosítója.

#### <a name="message-type-not-specified"></a>Nincs megadva az üzenet típusa
```python
chat_thread_client = chat_client.create_chat_thread(topic, participants)

content='hello world'
sender_display_name='sender name'

send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)
```

#### <a name="message-type-specified"></a>Megadott üzenet típusa
```python
from azure.communication.chat import ChatMessageType

content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>Csevegési szálból származó adott csevegési üzenet beolvasása
A `get_message` függvény egy adott üzenet lekérésére használható, message_id azonosítva

- `message_id`Az üzenet azonosítójának megadásához használja a következőt:.

A típus válasza `ChatMessage` az egyetlen üzenettel kapcsolatos összes információt tartalmazza.

```python
message_id = send_message_result_id
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Csevegési üzenetek fogadása csevegési szálból

A csevegési üzeneteket lekérheti a `list_messages` metódus megadott időközönkénti lekérdezésével.

- Ezzel `results_per_page` a beállítással adhatja meg, hogy legfeljebb hány üzenetet adjon vissza oldalanként.
- Ezzel `start_time` a beállítással adhatja meg a legkorábbi időpontot, hogy az üzenetek fel legyenek használva.

```python
chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print('ChatMessage: ', chat_message)
        print('ChatMessage: ', chat_message.content.message)
```

`list_messages` az üzenet legújabb verzióját adja vissza, beleértve a és a használatával az üzenettel történt módosításokat és törléseket `update_message` is `delete_message` . A törölt üzenetek esetében `ChatMessage.deleted_on` egy DateTime értéket ad vissza, amely azt jelzi, hogy az üzenet törölve lett. A szerkesztett üzenetek esetében `ChatMessage.edited_on` egy DateTime értéket ad vissza, amely azt jelzi, hogy mikor lett szerkesztve az üzenet. Az üzenetek létrehozásának eredeti időpontja elérhető a használatával `ChatMessage.created_on` , amely az üzenetek rendezésére használható.

`list_messages` a által azonosítható különböző típusú üzeneteket ad vissza `ChatMessage.type` . Ezek a típusok a következők:

- `ChatMessageType.TEXT`: Egy szál résztvevője által küldött normál csevegési üzenet.

- `ChatMessageType.HTML`: Egy hozzászóláslánc résztvevője által küldött HTML-csevegési üzenet.

- `ChatMessageType.TOPIC_UPDATED`: Az a Rendszerüzenet, amely azt jelzi, hogy a témakör frissítve lett.

- `ChatMessageType.PARTICIPANT_ADDED`: Az a Rendszerüzenet, amely azt jelzi, hogy egy vagy több résztvevő hozzá lett adva a csevegési szálhoz.

- `ChatMessageType.PARTICIPANT_REMOVED`: A résztvevőt jelző Rendszerüzenet el lett távolítva a csevegési szálból.

További részletek: [üzenetek típusai](../../../concepts/chat/concepts.md#message-types).

## <a name="update-topic-of-a-chat-thread"></a>Csevegési szál témakörének frissítése
A csevegési szál témakörét a metódus használatával frissítheti `update_topic`

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)
updated_topic = chat_client.get_chat_thread(chat_thread_client.thread_id).topic
print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>Üzenet frissítése
Egy meglévő üzenet tartalmát a `update_message` message_id által azonosított metódus használatával frissítheti.

- `message_id`A message_id megadására használatos
- Az `content` üzenet új tartalmának beállításához használja a következőt

```python
content = 'Hello world!'
send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)

content = 'Hello! I am updated content'
chat_thread_client.update_message(message_id=send_message_result_id, content=content)

chat_message = chat_thread_client.get_message(send_message_result_id)
print('Updated message content: ', chat_message.content.message)
```

## <a name="send-read-receipt-for-a-message"></a>Üzenet olvasási visszaigazolásának küldése
A `send_read_receipt` metódussal egy felhasználó nevében könyvelhető olvasási beérkezési esemény egy szálra.

- Ezzel a `message_id` beállítással adhatja meg az aktuális felhasználó által olvasott legújabb üzenet azonosítóját.

```python
message_id=send_message_result_id
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>Egy csevegési szál olvasási visszaigazolásának listázása
A `list_read_receipts` metódussal lekérheti a szál olvasási visszaigazolásait.

- Ezzel `results_per_page` a beállítással adhatja meg, hogy legfeljebb hány csevegési visszaigazolást adjon vissza oldalanként.
- A használatával `skip` megadhatja a csevegési üzenetek kihagyása olvasási visszaigazolásokat egy adott pozícióra válaszban.

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=2, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
```

## <a name="send-typing-notification"></a>Begépelési értesítés küldése
A `send_typing_notification` metódussal egy felhasználó nevében teheti közzé a begépelési eseményeket egy szálon.

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>Üzenet törlése
A `delete_message` metódust egy message_id által azonosított üzenet törlésére használhatja.

- `message_id`A message_id megadására használatos

```python
message_id=send_message_result_id
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Felhasználó felvétele a csevegési szálba résztvevőként

A csevegési szál létrehozása után hozzáadhat és eltávolíthat felhasználókat. A felhasználók hozzáadásával hozzáférést biztosíthat számukra, hogy üzeneteket küldjön a csevegési szálba, és további résztvevőket vegyen fel/távolítson el. A metódus meghívása előtt `add_participant` Győződjön meg arról, hogy új hozzáférési jogkivonatot és identitást szerzett az adott felhasználó számára. A felhasználónak szüksége lesz erre a hozzáférési jogkivonatra ahhoz, hogy inicializálja a csevegési ügyfelet.

`add_participant`A metódussal a thread_id által azonosított szálhoz adhatja hozzá a szál résztvevőit.

- A használatával `thread_participant` adja meg a csevegési szálhoz hozzáadandó résztvevőt;
- `user`, kötelező, a `CommunicationUserIdentifier` által létrehozott `CommunicationIdentityClient` [felhasználó létrehozása](../../access-tokens.md#create-an-identity)
- `display_name`, nem kötelező, a szál résztvevő megjelenítendő neve.
- `share_history_time`, nem kötelező, az az idő, amely alapján a csevegési előzmények megoszthatók a résztvevővel. Ha meg szeretné osztani a beszélgetési szál kezdete óta megjelenő előzményeket, állítsa ezt a tulajdonságot bármilyen dátumra vagy kevesebbre, mint a szál létrehozási ideje. Ha a résztvevő hozzáadását megelőzően meg szeretné osztani a korábbi előzményeket, állítsa azt az aktuális dátumra. A részleges előzmények megosztásához állítsa azt egy közbenső dátumra.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())

chat_thread_client.add_participant(new_chat_thread_participant)
```

Az metódus használatával több felhasználó is hozzáadható a csevegési szálhoz `add_participants` , a megadott új hozzáférési jogkivonat és azonosítás pedig minden felhasználó számára elérhető.

```python
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
        user=self.new_user,
        display_name='name',
        share_history_time=datetime.utcnow())
thread_participants = [new_chat_thread_participant] # instead of passing a single participant, you can pass a list of participants
chat_thread_client.add_participants(thread_participants)
```


## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

A résztvevők hozzáadásához hasonlóan egy szálból is eltávolíthatja a résztvevőket. Az eltávolításhoz követnie kell a felvett résztvevők azonosítóit.

`remove_participant`A szálazonosító által azonosított szálból távolítsa el a szál résztvevőjét a metódus használatával.
- `user` az `CommunicationUserIdentifier` eltávolításra kerül a szálból.

```python
chat_thread_client.remove_participant(new_user)
```

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `python` paranccsal.

```console
python start-chat.py
```
