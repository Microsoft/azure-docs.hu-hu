---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 1675ad802f3a117c26ee10218f3b3bedad9b0a00
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779684"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) 8-as vagy újabb verziója.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Egy üzembe helyezett kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- [Felhasználói hozzáférési jogkivonat](../../access-tokens.md). Ügyeljen arra, hogy a hatókört a "csevegés" értékre állítsa, és jegyezze fel a jogkivonat karakterláncát, valamint a userId karakterláncot.


## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-java-application"></a>Új Java-alkalmazás létrehozása

Nyissa meg a terminál vagy a parancssorablakot, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Java-alkalmazást. Futtassa az alábbi parancsot a Java-projekt létrehozásához a Maven-archetípus-Gyorsindítás sablonból.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Megfigyelheti, hogy a "létrehozás" cél létrehozta a artifactId megegyező nevű könyvtárat. Ebben a könyvtárban a `src/main/java directory` projekt forráskódját tartalmazza, a `src/test/java` könyvtár tartalmazza a teszt forrását, és a pom.xml fájl a projekt projekt-objektummodell vagy [Pom](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Az alkalmazás POM-fájljának frissítése a Java 8 vagy újabb verzió használatára:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-client-library"></a>A csevegési ügyféloldali függvénytárhoz tartozó csomagok hivatkozásainak hozzáadása

A POM-fájlban hivatkozzon a `azure-communication-chat` csomagra a csevegési API-kkal:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>
```

A hitelesítéshez az ügyfélnek a csomagra kell hivatkoznia `azure-communication-common` :

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>

```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik az Azure kommunikációs szolgáltatások csevegő ügyféloldali függvénytárának főbb funkcióit a Javához.

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozásához, lekéréséhez és törléséhez. |
| ChatAsyncClient | Ez az osztály szükséges az aszinkron csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozásához, lekéréséhez és törléséhez. |
| ChatThreadClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére |
| ChatThreadAsyncClient | Ez az osztály szükséges az aszinkron csevegési szál működéséhez. A példányokat a ChatAsyncClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére |

## <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása
Csevegési ügyfél létrehozásához használja a kommunikációs szolgáltatás végpontját és az előfeltételként létrehozott hozzáférési tokent. A felhasználói hozzáférési tokenek lehetővé teszik olyan ügyfélalkalmazások összeállítását, amelyek közvetlenül az Azure kommunikációs szolgáltatásokban vannak hitelesítve. Miután létrehozta ezeket a jogkivonatokat a kiszolgálón, továbbíthatja őket egy ügyfél-eszköznek. A CommunicationUserCredential osztályt kell használnia a közös ügyféloldali függvénytárban, hogy átadja a tokent a csevegési ügyfelének. 

Az importálási utasítások hozzáadásakor ügyeljen arra, hogy csak a com. Azure. Communication. chat és a com. Azure. Communication. chat. models névtérből vegyen fel importokat, nem pedig a com. Azure. Communication. chat. implementációs névtérből. A Maven használatával létrehozott app. Java-fájlban a következő kódot használhatja a kezdéshez:

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationUserCredential with the given access token, which is only valid until the token is valid
        CommunicationUserCredential userCredential = new CommunicationUserCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

`createChatThread`Csevegési szál létrehozásához használja a metódust.
`createChatThreadOptions` a szál kérelmének leírására szolgál.

- A használatával `topic` témakört adhat ehhez a csevegéshez; A témakör a funkció használatával frissíthető a csevegési szál létrehozása után `UpdateThread` .
- Ezzel a paranccsal `members` listázhatja a szálhoz hozzáadandó szál tagokat. `ChatThreadMember` a [felhasználói hozzáférési jogkivonat](../../access-tokens.md) rövid útmutatójában létrehozott felhasználót hozza létre.

A válasz a `chatThreadClient` létrehozott csevegési szálon végzett műveletek végrehajtásához használható: Tagok hozzáadása a csevegési szálhoz, üzenet küldése, üzenet törlése stb. Egy olyan `chatThreadId` tulajdonságot tartalmaz, amely a csevegési szál egyedi azonosítója. A tulajdonságot a nyilvános metódus. getChatThreadId () teszi elérhetővé.

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(firstUser)
    .setDisplayName("Member Display Name 1");
    
ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(secondUser)
    .setDisplayName("Member Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setMembers(members);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

A `sendMessage` metódus használatával üzenetet küldhet az imént létrehozott, chatThreadId által azonosított szálnak.
`sendChatMessageOptions` a csevegési üzenetre vonatkozó kérelem leírására szolgál.

- `content`A csevegési üzenet tartalmának megadásához használja a következőt:.
- Ezzel `priority` a beállítással adható meg a csevegés prioritási szintje, például a "NORMAL" vagy a "High"; Ez a tulajdonság használható felhasználói felületi mutatót az alkalmazásban lévő címzett felhasználó számára, hogy az üzenetre figyeljen, vagy egyéni üzleti logikát hajtson végre.
- A (z `senderDisplayName` ) használatával adja meg a feladó megjelenítendő nevét.

A válasz `sendChatMessageResult` tartalmaz egy `id` -t, amely az üzenet egyedi azonosítója.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setPriority(ChatMessagePriority.NORMAL)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása

A `getChatThreadClient` metódus egy szál-ügyfelet ad vissza egy már létező szálhoz. A létrehozott szálon végzett műveletek végrehajtásához használható: Tagok hozzáadása, üzenet küldése stb. `chatThreadId` a meglévő csevegési szál egyedi azonosítója.

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Csevegési üzenetek fogadása csevegési szálból

A csevegési üzeneteket lekérheti úgy, hogy a `listMessages` csevegési szál ügyfélprogramjában megadott időközönként lekérdezi a metódust.

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` az üzenet legújabb verzióját adja vissza, beleértve a. editMessage () és a. deleteMessage () használatával az üzenettel történt módosításokat és törléseket is. A törölt üzenetek esetében `chatMessage.getDeletedOn()` egy DateTime értéket ad vissza, amely azt jelzi, hogy az üzenet törölve lett. A szerkesztett üzenetek esetében `chatMessage.getEditedOn()` egy DateTime értéket ad vissza, amely azt jelzi, hogy mikor lett szerkesztve az üzenet. Az üzenet létrehozásának eredeti időpontja a használatával érhető el `chatMessage.getCreatedOn()` , és az üzenetek rendezésére is használható.

`listMessages` a által azonosítható különböző típusú üzeneteket ad vissza `chatMessage.getType()` . Ezek a típusok a következők:

- `Text`: Egy szál tagja által küldött normál csevegési üzenet.

- `ThreadActivity/TopicUpdate`: Az a Rendszerüzenet, amely azt jelzi, hogy a témakör frissítve lett.

- `ThreadActivity/AddMember`: Az a Rendszerüzenet, amely azt jelzi, hogy egy vagy több tag hozzá lett adva a csevegési szálhoz.

- `ThreadActivity/DeleteMember`: Az a Rendszerüzenet, amely azt jelzi, hogy a tag el lett távolítva a csevegési szálból.

További részletek: [üzenetek típusai](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Felhasználó hozzáadása a csevegési szálhoz tagként

A csevegési szál létrehozása után hozzáadhat és eltávolíthat felhasználókat. A felhasználók hozzáadásával hozzáférést biztosíthat számukra, hogy üzeneteket küldjön a csevegési szálba, és más tagokat adjon hozzá vagy távolítson el. Először új hozzáférési jogkivonatot és identitást kell beszereznie az adott felhasználó számára. A addMembers metódus meghívása előtt győződjön meg arról, hogy új hozzáférési jogkivonatot és identitást szerzett az adott felhasználó számára. A felhasználónak szüksége lesz erre a hozzáférési jogkivonatra ahhoz, hogy inicializálja a csevegési ügyfelet.

`addMembers`A metódus használatával a szálazonosító által azonosított szálat adhat hozzá a szálhoz.

- A használatával `members` listázhatja a csevegési szálba felvenni kívánt tagokat.
- `user`, kötelező, az a CommunicationUser, amelyet a CommunicationIdentityClient hozott létre a [felhasználói hozzáférési jogkivonat rövid útmutatójában](../../access-tokens.md) .
- `display_name`, nem kötelező, a szál tagja megjelenítendő neve.
- `share_history_time`, nem kötelező, az az idő, amelyből a csevegési előzmények meg vannak osztva a taggal. Ha meg szeretné osztani a beszélgetési szál kezdete óta megjelenő előzményeket, állítsa ezt a tulajdonságot bármilyen dátumra vagy kevesebbre, mint a szál létrehozási ideje. Ha meg szeretné osztani az előző előzményeket a tag hozzáadásakor, állítsa az aktuális dátumra. A részleges előzmények megosztásához állítsa azt a szükséges dátumra.

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(user1)
    .setDisplayName("Display Name 1");

ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(user2)
    .setDisplayName("Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

AddChatThreadMembersOptions addChatThreadMembersOptions = new AddChatThreadMembersOptions()
    .setMembers(members);
chatThreadClient.addMembers(addChatThreadMembersOptions);
```

## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

A felhasználók egy szálhoz való hozzáadásához hasonlóan a csevegési szálból is eltávolíthat felhasználókat. Ehhez nyomon kell követnie a felvett tagok felhasználói identitásait.

Használja `removeMember` , ahol `user` a a létrehozott CommunicationUser.

```Java
chatThreadClient.removeMember(user);
```

## <a name="run-the-code"></a>A kód futtatása

Navigáljon a *pom.xml* fájlt tartalmazó könyvtárra, és fordítsa le a projektet a következő `mvn` parancs használatával.

```console
mvn compile
```

Ezután hozza létre a csomagot.

```console
mvn package
```

Futtassa az alábbi `mvn` parancsot az alkalmazás végrehajtásához.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
