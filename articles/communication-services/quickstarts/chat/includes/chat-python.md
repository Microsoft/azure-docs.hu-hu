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
ms.openlocfilehash: bcbf2137e578f703cf70b1b47952736aa50f7f17
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178309"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

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
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>Az SDK telepítése

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Pythonhoz készült Azure kommunikációs szolgáltatások csevegési SDK főbb funkcióit kezelik.

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
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

`create_chat_thread`Csevegési szál létrehozásához használja a metódust.

- A használatával `topic` adjon meg egy téma témakört; A témakör a funkció használatával frissíthető a csevegési szál létrehozása után `update_thread` .
- A a `thread_participants` `ChatThreadParticipant` csevegési szálba felvenni kívánt elemek listázásához használja `ChatThreadParticipant` a `CommunicationUserIdentifier` típust `user` , amelyet a [felhasználó létrehozása](../../access-tokens.md#create-an-identity) után kapott.

`CreateChatThreadResult` az eredmény egy szál létrehozásával tért vissza, és a `id` létrehozott csevegési szál beolvasására használható. Ezt `id` követően a metódus használatával lehet lekérni egy `ChatThreadClient` objektumot `get_chat_thread_client` . `ChatThreadClient` más csevegési műveletek elvégzésére is használható a csevegési szálhoz.

```python
from azure.communication.chat import ChatThreadParticipant

topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása
A `get_chat_thread_client` metódus egy szál-ügyfelet ad vissza egy már létező szálhoz. A művelet végrehajtásához használható a létrehozott szálon: résztvevők hozzáadása, üzenet küldése stb. thread_id a meglévő csevegési szál egyedi azonosítója.

`ChatThreadClient` más csevegési műveletek elvégzésére is használható a csevegési szálhoz.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Az összes csevegési szál listázása
A `list_chat_threads` metódus egy típusú iterációt ad vissza `ChatThreadItem` . Az összes csevegési szál listázására használható.

- Ezzel `start_time` a beállítással adhatja meg a legkorábbi időpontot, hogy a csevegési szálakat akár be lehessen olvasni.
- Ezzel `results_per_page` a beállítással adhatja meg, hogy legfeljebb hány csevegési szálat ad vissza oldalanként.

Az a válasz, amely a `[ChatThreadItem]` szálak listázása után tért vissza.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

A `send_message` metódus használatával küldhet üzenetet egy imént létrehozott csevegési szálra, amelyet thread_id azonosít.

- `content`A csevegési üzenet tartalmának megadásához használja a következőt:
- `chat_message_type`Az üzenet tartalomtípusának megadásához használja a következőt:. A lehetséges értékek a következők: "text" és "HTML"; Ha nincs megadva az alapértelmezett "text" érték, a rendszer hozzárendeli.
- A használatával `sender_display_name` adja meg a feladó megjelenítendő nevét;

`SendChatMessageResult` az üzenet elküldésekor kapott válasz egy azonosítót tartalmaz, amely az üzenet egyedi azonosítója.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_w_enum.id)
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

`list_messages` a által azonosítható különböző típusú üzeneteket ad vissza `ChatMessage.type` . 

További információ az üzenetek típusairól: [üzenetek típusai](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Olvasási visszaigazolás küldése
A `send_read_receipt` metódussal egy felhasználó nevében könyvelhető olvasási beérkezési esemény egy szálra.

- Ezzel a `message_id` beállítással adhatja meg az aktuális felhasználó által olvasott legújabb üzenet azonosítóját.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Felhasználó felvétele a csevegési szálba résztvevőként

A csevegési szál létrehozása után hozzáadhat és eltávolíthat felhasználókat. A felhasználók hozzáadásával hozzáférést biztosíthat számukra, hogy üzeneteket küldjön a csevegési szálba, és további résztvevőket vegyen fel/távolítson el. A metódus meghívása előtt `add_participants` Győződjön meg arról, hogy új hozzáférési jogkivonatot és identitást szerzett az adott felhasználó számára. A felhasználónak szüksége lesz erre a hozzáférési jogkivonatra ahhoz, hogy inicializálja a csevegési ügyfelet.

Egy vagy több felhasználó a metódus használatával adható hozzá a csevegési szálhoz `add_participants` , a megadott új hozzáférési jogkivonat és azonosítás minden felhasználó számára elérhető.

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

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>Hozzászóláslánc résztvevőinek listázása egy csevegési szálban

A résztvevők hozzáadásához hasonlóan egy szál résztvevőit is listázhatja.

A `list_participants` szál résztvevőinek beolvasására használható.
- Használat `results_per_page` , nem kötelező, a visszaadott résztvevők maximális száma oldalanként.
- A nem kötelező lehetőséggel `skip` kihagyhatja a résztvevőket egy adott pozícióra válaszként.

Az a `[ChatThreadParticipant]` válasza, hogy a résztvevők a lista résztvevőit adják vissza.

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatThreadParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `python` paranccsal.

```console
python start-chat.py
```
