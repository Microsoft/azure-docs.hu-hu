---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 2d1c3d3be412f6f11f9d2e300b3a97cf5634f5e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495414"
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
from azure.communication.chat import ChatClient
from azure.communication.identity._shared.user_credential import CommunicationTokenCredential
from azure.communication.chat._shared.user_token_refresh_options import CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

`create_chat_thread`Csevegési szál létrehozásához használja a metódust.

- A használatával `topic` adjon meg egy téma témakört; A témakör a funkció használatával frissíthető a csevegési szál létrehozása után `update_thread` .
- A a `thread_participants` `ChatThreadParticipant` csevegési szálba felvenni kívánt elemek listázásához használja `ChatThreadParticipant` a `CommunicationUserIdentifier` típust `user` , amelyet a [felhasználó létrehozása](../../access-tokens.md#create-an-identity) után kapott.
- `repeatability_request_id`A paranccsal irányíthatja a kérést, hogy megismételhető. Az ügyfél többször is elvégezheti a kérést ugyanazzal az ismételhetőségi kéréssel, és visszaállíthatja a megfelelő választ anélkül, hogy a kiszolgáló többször is végrehajtja a kérést.

`CreateChatThreadResult` az eredmény egy szál létrehozásával tért vissza, és a `id` létrehozott csevegési szál beolvasására használható. Ezt `id` követően a metódus használatával lehet lekérni egy `ChatThreadClient` objektumot `get_chat_thread_client` . `ChatThreadClient` más csevegési műveletek elvégzésére is használható a csevegési szálhoz.

#### <a name="without-repeatability-request-id"></a>Ismételhetőség nélkül – kérelem-azonosító
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

#### <a name="with-repeatability-request-id"></a>Ismételhetőséggel – kérelem-azonosító
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, 
                                                    thread_participants=participants, 
                                                    repeatability_request_id=repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása
A `get_chat_thread_client` metódus egy szál-ügyfelet ad vissza egy már létező szálhoz. A művelet végrehajtásához használható a létrehozott szálon: résztvevők hozzáadása, üzenet küldése stb. thread_id a meglévő csevegési szál egyedi azonosítója.

`ChatThreadClient` más csevegési műveletek elvégzésére is használható a csevegési szálhoz.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="get-a-chat-thread"></a>Csevegési szál beszerzése

Használja a `get_chat_thread` metódust a szolgáltatásból való beolvasására `ChatThread` ; a `thread_id` szál egyedi azonosítója.
- A `thread_id` szál egyedi azonosítójának megadásához használja a (kötelező) lehetőséget. 
```python
chat_thread = chat_client.get_chat_thread(thread_id=thread_id)
```

## <a name="list-all-chat-threads"></a>Az összes csevegési szál listázása
A `list_chat_threads` metódus egy típusú iterációt ad vissza `ChatThreadInfo` . Az összes csevegési szál listázására használható.

- Ezzel `start_time` a beállítással adhatja meg a legkorábbi időpontot, hogy a csevegési szálakat akár be lehessen olvasni.
- Ezzel `results_per_page` a beállítással adhatja meg, hogy legfeljebb hány csevegési szálat ad vissza oldalanként.

Az a válasz, amely a `[ChatThreadInfo]` szálak listázása után tért vissza.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        print(chat_thread_info)
```

## <a name="delete-a-chat-thread"></a>Csevegési szál törlése
A a `delete_chat_thread` csevegési szál törlésére szolgál.

- `thread_id`A segítségével megadhatja egy meglévő csevegési szál thread_idét, amelyet törölni kell

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_client.delete_chat_thread(thread_id=thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

A `send_message` metódus használatával küldhet üzenetet egy imént létrehozott csevegési szálra, amelyet thread_id azonosít.

- `content`A csevegési üzenet tartalmának megadásához használja a következőt:
- `chat_message_type`Az üzenet tartalomtípusának megadásához használja a következőt:. A lehetséges értékek a következők: "text" és "HTML"; Ha nincs megadva az alapértelmezett "text" érték, a rendszer hozzárendeli.
- A használatával `sender_display_name` adja meg a feladó megjelenítendő nevét;

A válasz "id" típusú `str` , amely az üzenet egyedi azonosítója.

#### <a name="message-type-not-specified"></a>Nincs megadva az üzenet típusa
```python
topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)

content='hello world'

send_message_result_id = chat_thread_client.send_message(content)
```

#### <a name="message-type-specified"></a>Megadott üzenet típusa
```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_id_w_enum)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
print("Message sent: id: ", send_message_result_id_w_str)
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

Az az üzenet, amely az `[ChatMessage]` üzenetek listázása által visszaadott válasz.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
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

chat_thread = chat_client.get_chat_thread(chat_thread_client.thread_id)
updated_topic = chat_thread.topic

print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>Üzenet frissítése
Egy meglévő üzenet tartalmát a `update_message` message_id által azonosított metódus használatával frissítheti.

- A használata `message_id` kötelező, az üzenet egyedi azonosítója.
- `content`A (választható) érték a frissítendő üzenet tartalma; ha nincs megadva, a rendszer üresként rendeli hozzá a tartalmat.

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

`[ChatMessageReadReceipt]`A a válasza az olvasási visszaigazolások listázási eredményét adja vissza.

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=5, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
        print('Sender', read_receipt.sender)
        print('Message Id', read_receipt.chat_message_id)
        print('Read On Timestamp', read_receipt.read_on)
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

A résztvevő sikeres hozzáadásakor a rendszer nem dobott hibát. Ha hiba történt a résztvevő hozzáadásakor, a `RuntimeError` rendszer kidobja

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create an user
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_user = identity_client.create_user()

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

participant = ChatThreadParticipant(
    user=new_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow())

try:
    chat_thread_client.add_participant(thread_participant=participant)
except RuntimeError as e:
    if e is not None and decide_to_retry(error=e):
        chat_thread_client.add_participant(thread_participant=participant)
```

Az metódus használatával több felhasználó is hozzáadható a csevegési szálhoz `add_participants` , a megadott új hozzáférési jogkivonat és azonosítás pedig minden felhasználó számára elérhető.

A értéket `list(tuple(ChatThreadParticipant, CommunicationError))` adja vissza. A résztvevő sikeres hozzáadása után a rendszer üres listát vár. Ha hiba történt a résztvevő hozzáadásakor, a lista a sikertelen résztvevőkkel együtt jelenik meg, az észlelt hiba mellett.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatThreadParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(thread_participants=participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if len(retry) > 0:
    chat_thread_client.add_participants(retry)
```


## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

A résztvevők hozzáadásához hasonlóan egy szálból is eltávolíthatja a résztvevőket. Az eltávolításhoz követnie kell a felvett résztvevők azonosítóit.

`remove_participant`A szálazonosító által azonosított szálból távolítsa el a szál résztvevőjét a metódus használatával.
- `user` az `CommunicationUserIdentifier` eltávolításra kerül a szálból.

```python
chat_thread_client.remove_participant(user=new_user)

# # converesely you can also do the following; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
# 
# user_id = 'some user id'
# chat_thread_client.remove_participant(user=CommunincationUserIdentfier(new_user))
```

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `python` paranccsal.

```console
python start-chat.py
```
