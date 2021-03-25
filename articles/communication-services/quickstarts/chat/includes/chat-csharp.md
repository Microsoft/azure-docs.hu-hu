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
ms.openlocfilehash: 127031479d7ef414298d3096ebef814df1fe9a18
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027928"
---
## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt ügyeljen a következőre:
- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört.
- A [Visual Studio](https://visualstudio.microsoft.com/downloads/) telepítése
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../../create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell jegyeznie az erőforrás- **végpontot** .
- [Felhasználói hozzáférési jogkivonat](../../access-tokens.md). Ügyeljen arra, hogy a hatókört a "csevegés" értékre állítsa, és jegyezze fel a jogkivonat karakterláncát, valamint a userId karakterláncot.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `ChatQuickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: **program. cs**.

```console
dotnet new console -o ChatQuickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába, és használja az `dotnet build` parancsot az alkalmazás fordításához.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>A csomag telepítése

Az Azure kommunikációs csevegési ügyféloldali kódtár telepítése a .NET-hez

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.5
```

## <a name="object-model"></a>Objektummodell

A következő osztályok a C#-hoz készült Azure Communication Services csevegő függvénytárának főbb funkcióit kezelik.

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozásához, lekéréséhez és törléséhez. |
| ChatThreadClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a résztvevők hozzáadására/eltávolítására/beolvasására, valamint a beírási értesítések küldésére |

## <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása

Csevegési ügyfél létrehozásához használja a kommunikációs szolgáltatások végpontját és az előfeltételként szükséges lépések részeként létrehozott hozzáférési tokent. A `CommunicationIdentityClient` felhasználó létrehozásához és a csevegési ügyfélnek átadandó jogkivonat kiküldéséhez az Identity ügyféloldali függvénytár osztályát kell használnia.

További információ a [felhasználói hozzáférési tokenekről](../../access-tokens.md).

Ez a rövid útmutató nem fedi le a csevegési alkalmazás jogkivonatait kezelő szolgáltatási szintet, de ajánlott. További információ a [csevegési architektúráról](../../../concepts/chat/concepts.md)

Másolja a következő kódrészleteket, és illessze be a forrásfájl: **program. cs** fájlba
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

`createChatThread`Csevegési szál létrehozásához használja a chatClient metódusát
- A használatával `topic` témakört adhat ehhez a csevegéshez; A témakör a funkció használatával frissíthető a csevegési szál létrehozása után `UpdateTopic` .
- `participants`A tulajdonság használatával adja át a `ChatParticipant` csevegési szálhoz hozzáadandó objektumok listáját. Az `ChatParticipant` objektum egy objektummal van inicializálva `CommunicationIdentifier` . `CommunicationIdentifier` lehet típus `CommunicationUserIdentifier` `MicrosoftTeamsUserIdentifier` vagy `PhoneNumberIdentifier` . Egy objektum lekéréséhez például meg `CommunicationIdentifier` kell adnia egy hozzáférési azonosítót, amelyet a következő utasítások alapján hozott létre [egy felhasználó létrehozásához](../../access-tokens.md#create-an-identity) :

A metódus válasz objektuma `createChatThread` tartalmazza a `chatThread` részleteket. A csevegési szál műveleteivel, például a résztvevők hozzáadásával, üzenet küldésével, üzenet törlésével stb. az `chatThreadClient` ügyfél példányát az `GetChatThreadClient` ügyfél metódusának használatával kell létrehoznia `ChatClient` .

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása
A `GetChatThreadClient` metódus egy szál-ügyfelet ad vissza egy már létező szálhoz. Használható a létrehozott szálon végzett műveletek végrehajtásához: Tagok hozzáadása, üzenet küldése stb. a szálazonosító a meglévő csevegési szál egyedi azonosítója.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="list-all-chat-threads"></a>Az összes csevegési szál listázása
Ezzel a paranccsal `GetChatThreads` lekérheti az összes olyan csevegési szálat, amelyhez a felhasználó tartozik.

```csharp
AsyncPageable<ChatThreadItem> chatThreadItems = chatClient.GetChatThreadsAsync();
await foreach (ChatThreadItem chatThreadItem in chatThreadItems)
{
    Console.WriteLine($"{ chatThreadItem.Id}");
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

`SendMessage`Üzenet küldése egy szálnak.

- Az `content` üzenet tartalmának megadásához használja a következőt:.
- Használja az `type` üzenet tartalmának típusára, például "text" vagy "HTML". Ha nincs megadva, a rendszer a "text" értéket fogja beállítani.
- A (z `senderDisplayName` ) használatával adja meg a feladó megjelenítendő nevét. Ha nincs megadva, a rendszer üres karakterláncot állít be.

```csharp
var messageId = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Csevegési üzenetek fogadása csevegési szálból

A csevegési üzeneteket lekérheti úgy, hogy a `GetMessages` csevegési szál ügyfélprogramjában megadott időközönként lekérdezi a metódust.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages` egy opcionális `DateTimeOffset` paramétert használ. Ha az eltolás meg van adva, a rendszer a kapott, frissített vagy törölt üzeneteket fogja kapni. Vegye figyelembe, hogy a rendszer az eltolási idő előtt fogadta az üzeneteket, de azt a program a visszaadott érték után szerkesztette vagy eltávolította.

`GetMessages` az üzenet legújabb verzióját adja vissza, beleértve a és a használatával az üzenettel történt módosításokat és törléseket `UpdateMessage` is `DeleteMessage` . A törölt üzenetek esetében `chatMessage.DeletedOn` egy DateTime értéket ad vissza, amely azt jelzi, hogy az üzenet törölve lett. A szerkesztett üzenetek esetében `chatMessage.EditedOn` egy DateTime értéket ad vissza, amely azt jelzi, hogy mikor lett szerkesztve az üzenet. Az üzenet létrehozásának eredeti időpontja a használatával érhető el `chatMessage.CreatedOn` , és az üzenetek rendezésére is használható.

`GetMessages` a által azonosítható különböző típusú üzeneteket ad vissza `chatMessage.Type` . Ezek a típusok a következők:

- `Text`: Egy szál tagja által küldött normál csevegési üzenet.

- `Html`: Formázott szöveges üzenet. Vegye figyelembe, hogy a kommunikációs szolgáltatások felhasználói jelenleg nem küldhetnek RichText üzeneteket. Ezt az üzenetet a csapatok felhasználóitól a kommunikációs szolgáltatások felhasználóinak küldött üzenetek támogatják a csapatok együttműködési forgatókönyvekben.

- `TopicUpdated`: Az a Rendszerüzenet, amely azt jelzi, hogy a témakör frissítve lett. ReadOnly

- `ParticipantAdded`: Az a Rendszerüzenet, amely azt jelzi, hogy egy vagy több résztvevő hozzá lett adva a csevegési szálhoz. ReadOnly

- `ParticipantRemoved`: A résztvevőt jelző Rendszerüzenet el lett távolítva a csevegési szálból.

További részletek: [üzenetek típusai](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Felhasználó felvétele a csevegési szálba résztvevőként

Miután létrehozta a szálat, hozzáadhat és eltávolíthat felhasználókat. A felhasználók hozzáadásával hozzáférést biztosíthat számukra, hogy üzeneteket küldhessen a szálnak, illetve más résztvevőt vegyen fel/távolítson el. A hívás előtt `AddParticipants` Győződjön meg arról, hogy új hozzáférési jogkivonatot és identitást szerzett az adott felhasználó számára. A felhasználónak szüksége lesz erre a hozzáférési jogkivonatra ahhoz, hogy inicializálja a csevegési ügyfelet.

A használatával `AddParticipants` hozzáadhat egy vagy több résztvevőt a csevegési szálhoz. Az egyes szál-résztvevő (k) támogatott attribútumai a következők:
- `communicationUser`, kötelező, a szál résztvevő identitása.
- `displayName`, nem kötelező, a szál résztvevő megjelenítendő neve.
- `shareHistoryTime`, nem kötelező, a csevegési előzmények megosztásának időpontja a résztvevővel.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```

## <a name="get-thread-participants"></a>Hozzászóláslánc résztvevőinek beolvasása

A `GetParticipants` csevegési szál résztvevőinek beolvasására használható.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-read-receipt"></a>Olvasási visszaigazolás küldése

A használatával `SendReadReceipt` értesítheti a többi résztvevőt, hogy a felhasználó beolvassa az üzenetet.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```console
dotnet run
```
