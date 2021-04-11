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
ms.openlocfilehash: 447747fbf378c090f9dd09cde3b0452780039180
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957995"
---
## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt ügyeljen a következőre:

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört.
- Telepítse a [Android Studiot](https://developer.android.com/studio), majd a Android Studio használatával hozzon létre egy Android-alkalmazást a gyors üzembe helyezéshez a függőségek telepítéséhez.
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../../create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell **jegyeznie az erőforrás-végpontot** .
- Hozzon létre **két** kommunikációs szolgáltatást használó felhasználót, és adja ki a felhasználói hozzáférési jogkivonat [felhasználói hozzáférési jogkivonatát](../../access-tokens.md). Ügyeljen arra, hogy a hatókört a **csevegés** értékre állítsa, és **jegyezze fel a jogkivonat-karakterláncot és a userId karakterláncot**. Ebben a rövid útmutatóban egy kezdeti résztvevővel hozunk létre egy szálat, majd egy második résztvevőt is hozzáadunk a szálhoz.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-android-application"></a>Új Android-alkalmazás létrehozása

1. Nyissa meg Android Studio és válassza a lehetőséget `Create a new project` . 
2. A következő ablakban válassza ki `Empty Activity` a projekt sablonját.
3. Ha kiválasztja a beállításokat, adja meg `ChatQuickstart` a projekt nevét.
4. Kattintson a Tovább gombra, és válassza ki azt a könyvtárat, ahol létre szeretné hozni a projektet.

### <a name="install-the-libraries"></a>A kódtárak telepítése

A Gradle segítségével telepítjük a szükséges kommunikációs szolgáltatások függőségeit. A parancssorban navigáljon a projekt gyökérkönyvtárában `ChatQuickstart` . Nyissa meg az alkalmazás Build. gradle fájlját, és adja hozzá a következő függőségeket a `ChatQuickstart` célhoz:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.8'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Metaadatok kizárása a kiépítés csomagolási beállításaiban a root Build. gradle
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>Alternatív Könyvtárak telepítése a Maven használatával
Ha a gyűjteményt a [Maven](https://maven.apache.org/) Build rendszer használatával szeretné importálni a projektbe, adja hozzá az `dependencies` alkalmazás `pom.xml` fájljának szakaszához, adja meg az összetevő-azonosítóját és a használni kívánt verziót:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.8</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>A helyőrzők beállítása

Nyissa meg és szerkessze a fájlt `MainActivity.java` . Ebben a rövid útmutatóban hozzáadjuk a kódot a alkalmazáshoz `MainActivity` , és megtekintjük a kimenetet a konzolon. Ez a rövid útmutató nem foglalkozik a felhasználói felület létrehozásával. A fájl tetején importálja a és a `Communication common` `Communication chat` kódtárakat:

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Másolja a következő kódot a fájlba `MainActivity` :

```java
    private String secondUserId = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.8";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

A következő lépésekben az Azure kommunikációs szolgáltatások csevegési könyvtárával cseréljük ki a helyőrzőket a mintakód használatával.


### <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása

Cserélje le a megjegyzést a `<CREATE A CHAT CLIENT>` következő kódra (helyezze a fájl elejére az importálási utasításokat):

```java
import com.azure.android.communication.chat.ChatAsyncClient;
import com.azure.android.communication.chat.ChatClientBuilder;
import com.azure.android.core.credential.AccessToken;
import com.azure.android.core.http.HttpHeader;
import com.azure.android.core.http.okhttp.OkHttpAsyncClientProvider;
import com.azure.android.core.http.policy.BearerTokenAuthenticationPolicy;
import com.azure.android.core.http.policy.UserAgentPolicy;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .buildAsyncClient();

```

1. A használatával `ChatClientBuilder` konfigurálhatja és létrehozhatja a példányát `ChatAsyncClient` .
2. Cserélje le a- `<resource>` t a kommunikációs szolgáltatások erőforrására.
3. Cserélje le a `<user_access_token>` elemet egy érvényes kommunikációs szolgáltatás hozzáférési jogkivonatára.

## <a name="object-model"></a>Objektummodell
A következő osztályok és felületek a JavaScripthez készült Azure Communication Services csevegő SDK főbb funkcióit kezelik.

| Név                                   | Leírás                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozására, lekérésére és törlésére. |
| ChatThreadClient/ChatThreadAsyncClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére és a beolvasási visszaigazolásokra |

## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

A használatával `ChatAsyncClient` új szálat hozunk létre egy kezdeti felhasználóval.

Az `<CREATE A CHAT THREAD>` megjegyzés helyére írja be az alábbi kódot:

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(id))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

Cserélje le `<user_id>` egy érvényes kommunikációs szolgáltatások felhasználói azonosítóját. A `threadId` későbbi lépések során a befejezési kezelőnek visszaadott válaszból fogjuk használni, ezért az `<thread_id>` osztályt cserélje le a `threadId` kérelemből, majd futtassa újra az alkalmazást.

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása

Most, hogy létrehozott egy csevegési szálat, beszerezhetünk egy `ChatThreadAsyncClient` műveletet a szálon belül. Az `<CREATE A CHAT THREAD CLIENT>` megjegyzés helyére írja be az alábbi kódot:

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

Győződjön meg arról, hogy `<thread_id>` egy érvényes szál-azonosítóval lett lecserélve, és most küldünk üzenetet erre a szálra.

Az `<SEND A MESSAGE>` megjegyzés helyére írja be az alábbi kódot:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

A beolvasás után lecseréljük a-t a későbbi módszerekhez a gyors útmutatóban, `chatMessageId` `<chat_message_id>` `chatMessageId` majd újra futtatjuk az alkalmazást.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Felhasználó felvétele a csevegési szálba résztvevőként

Az `<ADD A USER>` megjegyzés helyére írja be az alábbi kódot:

```java
// The display name for the thread participant.
displayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```

Cserélje le a `<second_user_id>` osztályt a hozzáadni kívánt felhasználó kommunikációs szolgáltatások felhasználói azonosítójával. 

## <a name="list-users-in-a-thread"></a>Egy szál felhasználóinak listázása

Az `<LIST USERS>` megjegyzést írja felül az alábbi kóddal:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatParticipant> firstPageWithResponse =
    chatThreadAsyncClient.getParticipantsFirstPageWithResponse(listParticipantsOptions, Context.NONE).get();

for (ChatParticipant participant : firstPageWithResponse.getValue()) {
    // You code to handle participant
}

listParticipantsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Helyezze a következő Helper metódust a osztályba:

```java
void listParticipantsNextPage(String continuationToken, int pageNumber) {
if (continuationToken != null) {
    PagedResponse<ChatParticipant> nextPageWithResponse =
        chatThreadAsyncClient.getParticipantsNextPageWithResponse(continuationToken, Context.NONE).get();
        for (ChatParticipant participant : nextPageWithResponse.getValue()) {
            // You code to handle participant
        }
            
        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

Győződjön meg arról, hogy `<second_user_id>` érvényes felhasználói azonosítóra cseréli le a rendszer, ezért most eltávolítja a második felhasználót a szálból.

Az `<REMOVE A USER>` megjegyzést írja felül az alábbi kóddal:

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>Begépelési értesítés küldése

Az `<SEND A TYPING NOTIFICATION>` megjegyzést írja felül az alábbi kóddal:

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>Olvasási visszaigazolás küldése

Ügyeljen arra, hogy `<chat_message_id>` egy érvényes csevegési azonosítójú üzenettel helyettesítse be az üzenet olvasási visszaigazolását.

Az `<SEND A READ RECEIPT>` megjegyzést írja felül az alábbi kóddal:

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>Olvasási visszaigazolások listázása

Az `<READ RECEIPTS>` megjegyzést írja felül az alábbi kóddal:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatMessageReadReceipt> firstPageWithResponse =
    chatThreadAsyncClient.getReadReceiptsFirstPageWithResponse(listReadReceiptOptions, Context.NONE).get();

for (ChatMessageReadReceipt readReceipt : firstPageWithResponse.getValue()) {
    // You code to handle readReceipt
}

listReadReceiptsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Helyezze a következő Helper metódust a osztályba:
```java
void listReadReceiptsNextPage(String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        PagedResponse<ChatMessageReadReceipt> nextPageWithResponse =
            chatThreadAsyncClient.getReadReceiptsNextPageWithResponse(continuationToken, Context.NONE).get();

        for (ChatMessageReadReceipt readReceipt : nextPageWithResponse.getValue()) {
            // You code to handle readReceipt
        }

        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="run-the-code"></a>A kód futtatása

Android Studio a projekt létrehozásához és futtatásához nyomja le a Futtatás gombot. A-konzolon megtekintheti a kód kimenetét és a ChatClient származó naplózó kimenetet.
