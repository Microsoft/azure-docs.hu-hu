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
ms.openlocfilehash: e5b5433be4a95a9df9d3b3527473c3004d24acac
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327514"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [A Java fejlesztői készlet (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) 8-as vagy újabb verziója.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Egy üzembe helyezett Communication Services erőforrás és kapcsolati sztring. [Hozzon létre egy Communication Services erőforrást.](../../create-communication-resource.md)
- Egy [felhasználói hozzáférési jogkivonat.](../../access-tokens.md) Ügyeljen arra, hogy a hatókört "chat" (csevegés) beállításra állítsa, és jegyezze fel a jogkivonat-sztringet és a userId sztringet.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-java-application"></a>Új Java-alkalmazás létrehozása

Nyissa meg a terminált vagy a parancsablakot, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Java-alkalmazást. Az alábbi parancs futtatásával hozza létre a Java-projektet a maven-archetype-quickstart sablonból.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Figyelje meg, hogy a "létrehozási" cél létrehozott egy könyvtárat, amely az artifactId nevével azonos. Ebben a könyvtárban a tartalmazza a projekt forráskódját, a könyvtár a tesztforrást, a pom.xml-fájl pedig a projekt `src/main/java directory` `src/test/java` Projektobjektum-modellje vagy [POM fájlja.](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)

Frissítse az alkalmazás POM-fájlját a Java 8-as vagy újabb verzió használatára:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Csomaghivatkozások hozzáadása a Chat SDK-hoz

A POM-fájlban hivatkozhat a `azure-communication-chat` csomagra a csevegő API-jaival:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0</version>
</dependency>
```

A hitelesítéshez az ügyfélnek hivatkozni kell a `azure-communication-common` csomagra:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek a Javához készült Chat SDK néhány fő funkcióját Azure Communication Services kezelik.

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Erre az osztályra a Csevegés funkcióhoz van szükség. Példányosithatja az előfizetési adataival, majd a használatával létrehozhat, lekért és törölhet szálakat. |
| ChatAsyncClient | Erre az osztályra az aszinkron csevegési funkcióhoz van szükség. Példányosithatja az előfizetési adataival, majd a használatával létrehozhat, lekért és törölhet szálakat. |
| ChatThreadClient | Erre az osztályra a csevegési szál funkcióhoz van szükség. Egy példányt a ChatClienten keresztül szerezhet be, majd üzenetek küldésére/fogadására/frissítésére/törlésére, felhasználók hozzáadására,eltávolítására/leküldésére, gépelési értesítések küldésére és nyugták olvasására használhatja. |
| ChatThreadAsyncClient | Erre az osztályra az aszinkron csevegési szál funkcióhoz van szükség. A példányt a ChatAsyncClient használatával szerezheti be, majd üzenetek küldésére/fogadására/frissítésére/törlésére, felhasználók hozzáadására,eltávolítására/leküldésére, gépelési értesítések küldésére és nyugták olvasására használhatja. |

## <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása
Csevegési ügyfél létrehozásához a Kommunikációs szolgáltatás végpontját és az előfeltételként létrehozott hozzáférési jogkivonatot fogja használni az előfeltételként szükséges lépések részeként. A felhasználói hozzáférési jogkivonatokkal olyan ügyfélalkalmazásokat építhet, amelyek közvetlenül hitelesítik magukat Azure Communication Services. Miután generálta ezeket a jogkivonatokat a kiszolgálón, továbbadja azokat egy ügyféleszköznek. Ahhoz, hogy a jogkivonatot továbbadja a csevegési ügyfélnek, a Common SDK CommunicationTokenCredential osztályát kell használnia.

További információ a [csevegési architektúráról](../../../concepts/chat/concepts.md)

Importálási utasítások hozzáadásakor győződjön meg arról, hogy csak a com.azure.communication.chat és a com.azure.communication.chat.models névterek importálásait adja hozzá, a com.azure.communication.chat.implementation névtérből nem. A Maven használatával létrehozott App.java fájlban a következő kóddal kezdje a műveletet:

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>Csevegés elindítása

A `createChatThread` metódussal hozzon létre egy csevegési szálat.
`createChatThreadOptions` A a szálkérés leírására használatos.

- A `topic` konstruktor paraméterének használatával adjon egy témakört a csevegéshez; A témakör a csevegési szál függvény használatával való létrehozása után `UpdateThread` frissíthető.
- A használatával listába sorolja a szálhoz `participants` hozzáadni a résztvevőket. `ChatParticipant` A a felhasználói hozzáférési jogkivonat [gyorsútmutatóban létrehozott](../../access-tokens.md) felhasználót veszi fel.

`CreateChatThreadResult` A egy csevegési szál létrehozásából visszaadott válasz.
Tartalmaz egy metódust, amely visszaadja a objektumot, amellyel lekérte a szál ügyfelét, amelyből lekérte a műveletet a létrehozott szálon: résztvevők hozzáadása, üzenet küldése `getChatThread()` `ChatThread` `ChatThreadClient` stb. A `ChatThread` objektum tartalmazza a `getId()` metódust is, amely lekéri a szál egyedi azonosítóját.

```Java
CommunicationUserIdentifier identity1 = new CommunicationUserIdentifier("<USER_1_ID>");
CommunicationUserIdentifier identity2 = new CommunicationUserIdentifier("<USER_2_ID>");

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Csevegési szálak listása

A `listChatThreads` metódussal lekérhető a meglévő csevegőszálak listája.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének lekérte

A `getChatThreadClient` metódus egy szál ügyfelét adja vissza egy már létező szálhoz. Használható műveletek végrehajtásához a létrehozott szálon: résztvevők hozzáadása, üzenet küldése stb. `chatThreadId` A a meglévő csevegési szál egyedi azonosítója.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése egy csevegési szálnak

A metódussal küldjön üzenetet az éppen létrehozott `sendMessage` szálnak, amelyet a chatThreadId azonosít.
`sendChatMessageOptions` A a csevegőüzenet-kérés leírására használatos.

- A `content` használatával adja meg a csevegőüzenet tartalmát.
- A `type` csevegőüzenet tartalomtípusának (TEXT vagy HTML) megadásához használható.
- A `senderDisplayName` küldő megjelenítendő nevének megadásához használja a következőt: .

A válasz `sendChatMessageResult` tartalmaz egy `id` et, amely az üzenet egyedi azonosítója.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Csevegési üzenetek fogadása egy csevegési szálból

A csevegési üzenetek lekérése a metódus lekérdezésével történik a csevegési szál `listMessages` ügyfelén megadott időközönként.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` az üzenet legújabb verzióját adja vissza, beleértve az üzenettel történt módosításokat vagy törléseket az .editMessage() és a .deleteMessage() használatával. Törölt üzeneteknél a egy datetime értéket ad vissza, amely `chatMessage.getDeletedOn()` jelzi, hogy az üzenet mikor lett törölve. Szerkesztett üzenetekhez a visszaad egy dátum/idő értéket, amely az üzenet `chatMessage.getEditedOn()` szerkesztésének időpontját jelzi. Az üzenetek létrehozásának eredeti ideje a használatával érhető el, és az üzenetek `chatMessage.getCreatedOn()` megrendelésére használható.

További információ az üzenettípusokról: [Üzenettípusok.](../../../concepts/chat/concepts.md#message-types)

## <a name="send-read-receipt"></a>Olvasási nyugták küldése

A metódussal olvasási nyugtát közzétevhet egy csevegési szálon egy `sendReadReceipt` felhasználó nevében.
`chatMessageId` A a beolvasott csevegési üzenet egyedi azonosítója.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Csevegés résztvevőinek felsorolása

A használatával lekérhet egy lapozásos gyűjteményt, amely a chatThreadId által azonosított csevegési szál `listParticipants` résztvevőit tartalmazza.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Felhasználó hozzáadása résztvevőként a csevegési szálhoz

Miután létrejött egy csevegési szál, hozzáadhat és eltávolíthat felhasználókat. A felhasználók hozzáadásával hozzáférést adhat nekik, hogy üzeneteket küldjenek a csevegési szálnak, és további résztvevőket adhat hozzá vagy távolíthat el. Először egy új hozzáférési jogkivonatot és identitást kell lekérte az adott felhasználóhoz. Az addParticipants metódus hívása előtt győződjön meg arról, hogy beszerezte az adott felhasználó új hozzáférési jogkivonatát és identitását. A felhasználónak szüksége lesz erre a hozzáférési jogkivonatra a csevegő ügyfelének inicializálása érdekében.

A `addParticipants` metódussal vegyen fel résztvevőket a szálba.

- `communicationIdentifier`, kötelező: a CommunicationIdentityClient által a felhasználói hozzáférési jogkivonat rövid útmutatóban létrehozott CommunicationIdentifier. [](../../access-tokens.md)
- `displayName`, nem kötelező: a szál résztvevőjének megjelenítendő neve.
- `shareHistoryTime`, nem kötelező: az az időpont, amelyből a csevegési előzmények megoszthatóak a résztvevővel. Ha a csevegési szál kezdetétől fogva meg kell osztania az előzményeket, állítsa ezt a tulajdonságot bármely olyan dátumra, amely a szál létrehozási ideje vagy annál rövidebb. Ha nem oszt meg korábbi előzményeket a résztvevő hozzáadása előtt, állítsa az aktuális dátumra. A részleges előzmények megosztásához állítsa be a kívánt dátumot.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

CommunicationUserIdentifier identity3 = new CommunicationUserIdentifier("<USER_3_ID>");
CommunicationUserIdentifier identity4 = new CommunicationUserIdentifier("<USER_4_ID>");

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>A kód futtatása

Keresse meg apom.xml *fájlt* tartalmazó könyvtárat, és fordítsa le a projektet a következő `mvn` paranccsal.

```console
mvn compile
```

Ezután készítse el a csomagot.

```console
mvn package
```

Futtassa a `mvn` következő parancsot az alkalmazás végrehajtásához.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
