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
ms.openlocfilehash: 31704e705b828cc0070e3b79f5d527cfa9deb0c3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386749"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzá kezd, győződjön meg a következő lépésekről:

- Hozzon létre egy aktív előfizetéssel működő Azure-fiókot. Részletekért lásd: [Ingyenes fiók létrehozása.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A [Python telepítése](https://www.python.org/downloads/)
- Hozzon létre egy Azure Communication Services erőforrást. Részletekért lásd: [Azure Communication-erőforrás létrehozása.](../../create-communication-resource.md) Ehhez a rövid útmutatóhoz rögzítenie kell **az** erőforrás-végpontot
- Egy [felhasználói hozzáférési jogkivonat.](../../access-tokens.md) Ügyeljen arra, hogy a hatókört "chat" (csevegés) beállításra állítsa, és jegyezze fel a jogkivonat-sztringet és a userId sztringet.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Nyissa meg a terminált vagy a parancsablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Egy szövegszerkesztővel hozzon  létre egy start-chat.py nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a program struktúráját, beleértve az alapszintű kivételkezelést is. Ehhez a fájlhoz a rövid útmutató összes forráskódját a következő szakaszokban adjuk hozzá.

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

Az alábbi osztályok és felületek kezelik a Pythonhoz készült Azure Communication Services Chat SDK néhány fő funkcióját.

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Erre az osztályra a Csevegés funkcióhoz van szükség. Példányosithatja az előfizetési adataival, majd a használatával létrehozhat, lekért és törölhet szálakat. |
| ChatThreadClient | Erre az osztályra a csevegési szál funkcióhoz van szükség. Egy példányt a ChatClienten keresztül szerezhet be, majd üzenetek küldésére,fogadására/frissítésére/törlésére, felhasználók hozzáadására,eltávolítására/leküldésére, gépelési értesítések küldésére és nyugták olvasására használhatja. |

## <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása

Csevegési ügyfél létrehozásához a Communications Service végpontját és az előfeltételként létrehozott végpontot fogja `Access Token` használni az előfeltételként szükséges lépések részeként. További információ a [felhasználói hozzáférési jogkivonatokkal kapcsolatban.](../../access-tokens.md)

Ez a rövid útmutató nem terjed ki a csevegőalkalmazás jogkivonatai kezelésére vonatkozó szolgáltatási szint létrehozására, bár ajánlott. A csevegési architektúra további részleteiért tekintse meg az alábbi [dokumentációt](../../../concepts/chat/concepts.md)

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Csevegési szál elindítani

A `create_chat_thread` metódussal hozzon létre egy csevegési szálat.

- Egy `topic` témakört a használatával adhat meg; A témakör a csevegési szál függvény használatával való létrehozása után `update_thread` frissíthető.
- A használatával listába sorolja a csevegési szálhoz hozzáadni szükséges et. A típus a `thread_participants` `ChatParticipant` `ChatParticipant` `CommunicationUserIdentifier` `user` következő: , [](../../access-tokens.md#create-an-identity) amit a felhasználó létrehozása után kapott meg

`CreateChatThreadResult` A egy szál létrehozásából visszaadott eredmény. A használatával lekérheti a létrehozott `id` csevegési szál 1-ét. Ezzel `id` lekérhet egy objektumot a `ChatThreadClient` `get_chat_thread_client` metódussal. `ChatThreadClient` más csevegési műveletek elvégzésére is használható a csevegőszálon.

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének lekérte
A `get_chat_thread_client` metódus egy szál ügyfelét adja vissza egy már létező szálhoz. Használható műveletek végrehajtásához a létrehozott szálon: résztvevők hozzáadása, üzenet küldése stb. thread_id a meglévő csevegési szál egyedi azonosítója.

`ChatThreadClient` más csevegési műveletek elvégzésére is használható a csevegőszálon.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Az összes csevegési szál listása
A `list_chat_threads` metódus típusú iterátort ad `ChatThreadItem` vissza. Az összes csevegési szál listázásra használható.

- A `start_time` használatával megadhatja a legkorábbi időpontban a csevegési szálak beállítását.
- A használatával adhatja meg az egy oldalon visszaadott `results_per_page` csevegési szálak maximális számát.

A iterátora `[ChatThreadItem]` a listázási szálak válasza

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése egy csevegési szálnak

A metódussal küldhet üzenetet egy most létrehozott csevegési szálnak, amelyet a `send_message` thread_id.

- A `content` használatával adja meg a csevegőüzenet tartalmát;
- Az `chat_message_type` üzenet tartalmának típusának megadásához használja a parancsot. A lehetséges értékek a "text" és a "html"; ha nincs megadva a "text" alapértelmezett értéke, a rendszer hozzárendeli.
- A `sender_display_name` küldő megjelenítendő nevének megadásához használja a következőt:

`SendChatMessageResult` A az üzenetküldés által visszaadott válasz, amely egy azonosítót tartalmaz, amely az üzenet egyedi azonosítója.

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


## <a name="receive-chat-messages-from-a-chat-thread"></a>Csevegőüzenetek fogadása egy csevegési szálból

A csevegőüzenetek lekérése a metódus megadott időközönkénti `list_messages` lekérdezésével történik.

- A használatával adhatja meg az egy oldalonként `results_per_page` visszaadott üzenetek maximális számát.
- A használatával adhatja meg a `start_time` legkorábbi időpontban küldött üzeneteket.

A iterátora `[ChatMessage]` a listázási üzenetekből visszaadott válasz

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` az üzenet legújabb verzióját adja vissza, beleértve az üzenettel történt módosításokat vagy törléseket a és `update_message` a `delete_message` használatával. A törölt üzenetekhez `ChatMessage.deleted_on` egy datetime értéket ad vissza, amely jelzi, hogy az üzenet mikor lett törölve. Szerkesztett üzenetekhez a visszaad egy dátum/idő értéket, amely az üzenet `ChatMessage.edited_on` szerkesztésének időpontját jelzi. Az üzenetek létrehozásának eredeti ideje a használatával érhető el, amely az `ChatMessage.created_on` üzenetek megrendelésére használható.

`list_messages` A a által azonosítható különböző típusú üzeneteket ad `ChatMessage.type` vissza. 

Az üzenettípusokról itt olvashat bővebben: [Üzenettípusok.](../../../concepts/chat/concepts.md#message-types)

## <a name="send-read-receipt"></a>Olvasási nyugták küldése
A metódussal olvasási nyugtát közzétenhet egy szálon egy `send_read_receipt` felhasználó nevében.

- A használatával adhatja meg az aktuális felhasználó által `message_id` beolvasott legújabb üzenet azonosítóját.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Felhasználó hozzáadása résztvevőként a csevegési szálhoz

A csevegési szál létrehozása után felhasználókat adhat hozzá és távolíthat el róla. A felhasználók hozzáadásával hozzáférést ad nekik, hogy üzeneteket tudjanak küldeni a csevegési szálnak, és további résztvevőket adhatnak hozzá vagy távolíthatnak el. A metódus hívása előtt győződjön meg arról, hogy beszerezte az adott felhasználó új hozzáférési `add_participants` jogkivonatát és identitását. A felhasználónak szüksége lesz erre a hozzáférési jogkivonatra a csevegő ügyfelének inicializálása érdekében.

A metódussal egy vagy több felhasználó is hozzáadható a csevegőszálhoz, feltéve, hogy az új hozzáférési jogkivonat és azonosítás minden felhasználó `add_participants` számára elérhető.

A `list(tuple(ChatParticipant, CommunicationError))` visszaadott érték. Ha a résztvevő hozzáadása sikeres volt, üres lista várható. Ha hiba történik a résztvevő hozzáadása során, a listán a sikertelen résztvevők és az észlelt hiba is fel lesz töltve.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
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
# participant = ChatParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
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


## <a name="list-thread-participants-in-a-chat-thread"></a>Csevegési szál résztvevőinek listása

A résztvevők hozzáadásához hasonlóan egy szál résztvevőit is listába sorolhatja.

A `list_participants` használatával lekéri a szál résztvevőit.
- Használja a következőt: , nem kötelező, a laponként `results_per_page` visszaadott résztvevők maximális száma.
- A , nem kötelező használatával a résztvevőket a válaszban `skip` megadott pozícióra ugorhatja.

A iterátora `[ChatParticipant]` a résztvevőktől visszaadott válasz

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazáskönyvtárból az `python` paranccsal.

```console
python start-chat.py
```
