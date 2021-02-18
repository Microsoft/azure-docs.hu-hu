---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/16/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 7e62bbc5929eaf23a9b7be12de222105bc2529cd
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653546"
---
## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt ügyeljen a következőre:

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört.
- Telepítse a [Android Studiot](https://developer.android.com/studio), majd a Android Studio használatával hozzon létre egy Android-alkalmazást a gyors üzembe helyezéshez a függőségek telepítéséhez.
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../../create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell **jegyeznie az erőforrás-végpontot** .
- Hozzon létre **két** kommunikációs szolgáltatást használó felhasználót, és adja ki a felhasználói hozzáférési jogkivonat [felhasználói hozzáférési jogkivonatát](../../access-tokens.md). Ügyeljen arra, hogy a hatókört a **csevegés** értékre állítsa, és **jegyezze fel a jogkivonat karakterláncát, valamint a userId karakterláncot**. Ebben a rövid útmutatóban egy kezdő résztvevővel hozunk létre egy szálat, majd egy második résztvevőt is hozzáadunk a szálhoz.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-android-application"></a>Új Android-alkalmazás létrehozása

1. Nyissa meg Android Studio és válassza a lehetőséget `Create a new project` . 
2. A következő ablakban válassza ki `Empty Activity` a projekt sablonját.
3. Amikor kiválasztja a beállításokat `ChatQuickstart` , adja meg a projekt nevét.
4. Kattintson a Tovább gombra, és válassza ki azt a könyvtárat, ahol létre szeretné hozni a projektet.

### <a name="install-the-libraries"></a>A kódtárak telepítése

A Gradle segítségével telepítjük a szükséges kommunikációs szolgáltatások függőségeit. A parancssorból navigáljon a projekt gyökérkönyvtárában `ChatQuickstart` . Nyissa meg az alkalmazás Build. gradle fájlját, és adja hozzá a következő függőségeket a `ChatQuickstart` célhoz:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.5'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.5'
```

Kattintson Android Studio a szinkronizálás most elemre.

#### <a name="alternative-to-install-libraries-through-maven"></a>Alternatív Könyvtárak telepítése a Maven használatával
Ha a gyűjteményt a [Maven](https://maven.apache.org/) Build rendszer használatával szeretné importálni a projektbe, adja hozzá az `dependencies` alkalmazás `pom.xml` fájljának szakaszához, adja meg az összetevő-azonosítóját és a használni kívánt verziót:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.5</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>A helyőrzők beállítása

Nyissa meg és szerkessze a fájlt `MainActivity.java` . Ebben a rövid útmutatóban hozzáadjuk a kódot a alkalmazáshoz `MainActivity` , és megtekintjük a kimenetet a konzolon. Ez a rövid útmutató nem foglalkozik a felhasználói felület létrehozásával. A fájl tetején importálja a és a `Communication common` `Communication chat` kódtárakat:

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Másolja a következő kódot a fájlba `MainActivity` :

```
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
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

A következő lépésekben az Azure kommunikációs szolgáltatások csevegési könyvtárával cseréljük ki a helyőrzőket a mintakód használatával.


### <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása

Cserélje le a megjegyzést a `<CREATE A CHAT CLIENT>` következő kódra (helyezze a fájl elejére az importálási utasításokat):

```java
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatClient client = new ChatClient.Builder()
    .endpoint(endpoint)
    .credentialInterceptor(chain -> chain.proceed(chain.request()
        .newBuilder()
        .header(HttpHeader.AUTHORIZATION, userAccessToken)
        .build());
```

1. A használatával `AzureCommunicationChatServiceAsyncClient.Builder` konfigurálhatja és létrehozhatja a példányát `AzureCommunicationChatClient` .
2. Cserélje le a- `<resource>` t a kommunikációs szolgáltatások erőforrására.
3. Cserélje le a `<user_access_token>` elemet egy érvényes kommunikációs szolgáltatás hozzáférési jogkivonatára.

## <a name="object-model"></a>Objektummodell
A következő osztályok és felületek az Azure kommunikációs szolgáltatások csevegési függvénytárának főbb funkcióit kezelik a JavaScripthez.

| Név                                   | Leírás                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozásához, lekéréséhez és törléséhez. |
| ChatThreadClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére és a beolvasási visszaigazolásokra |

## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

A használatával `ChatClient` új szálat hozunk létre egy kezdeti felhasználóval.

Az `<CREATE A CHAT THREAD>` megjegyzés helyére írja be az alábbi kódot:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
final String id = "<user_id>";
// The display name for the thread participant.
final String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setId(id)
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
    .setTopic(topic)
    .setParticipants(participants);

// optional, set a repeat request ID 
final String repeatabilityRequestID = '123';

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
    public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
        // MultiStatusResponse is the result returned from creating a thread.
        // It has a 'multipleStatus' property which represents a list of IndividualStatusResponse.
        String threadId;
        List<IndividualStatusResponse> statusList = result.getMultipleStatus();
        for (IndividualStatusResponse status : statusList) {
            if (status.getId().endsWith("@thread.v2")
                && status.getType().contentEquals("Thread")) {
                threadId = status.getId();
                break;
            }
        }
        // Take further action.
    }

    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
    }
});
```

Cserélje le `<user_id>` egy érvényes kommunikációs szolgáltatások felhasználói azonosítóját. A `threadId` későbbi lépések során a befejezési kezelőhöz visszaadott válaszból fogjuk használni.

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása

Most, hogy létrehozott egy csevegési szálat, beszerezhetünk egy `ChatThreadClient` műveletet a szálon belül. Az `<CREATE A CHAT THREAD CLIENT>` megjegyzés helyére írja be az alábbi kódot:

```
ChatThreadClient threadClient =
        new ChatThreadClient.Builder()
            .endpoint(<endpoint>))
            .build();
```

Cserélje le a `<endpoint>` t a kommunikációs szolgáltatási végpontra.

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

Az `<SEND A MESSAGE>` megjegyzés helyére írja be az alábbi kódot:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageRequest message = new SendChatMessageRequest()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// The unique ID of the thread.
final String threadId = "<thread_id>";
threadClient.sendChatMessage(threadId, message, new Callback<String>() {
    @Override
    public void onSuccess(String messageId, Response response) {
        // A string is the response returned from sending a message, it is an id, 
        // which is the unique ID of the message.
        final String chatMessageId = messageId;
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

A helyére írja `<thread_id>` be az üzenetet küldő szál azonosítóját.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Felhasználó felvétele a csevegési szálba résztvevőként

Az `<ADD A USER>` megjegyzés helyére írja be az alábbi kódot:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The CommunicationUser.identifier you created before, required.
final String id = "<user_id>";
// The display name for the thread participant.
final String displayName = "a new participant";
participants.add(new ChatParticipant().setId(id).setDisplayName(displayName));
// The model to pass to the add method.
AddChatParticipantsRequest participants = new AddChatParticipantsRequest()
    .setParticipants(participants);

// The unique ID of the thread.
final String threadId = "<thread_id>";
threadClient.addChatParticipants(threadId, participants, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

1. A helyére írja `<user_id>` be a hozzáadni kívánt felhasználó kommunikációs szolgáltatások felhasználói azonosítóját. 
2. A helyére írja `<thread_id>` be azt a szál-azonosítót, amelyet a felhasználó hozzáad a szolgáltatáshoz.

## <a name="list-users-in-a-thread"></a>Egy szál felhasználóinak listázása

Az `<LIST USERS>` megjegyzést írja felül az alábbi kóddal:

```java
// The unique ID of the thread.
final String threadId = "<thread_id>";

// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
    @Override
    public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> firstPage,
        Response response) {
        // pageCollection enables enumerating list of chat participants.
        pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> firstPage, Response response) {
                for (ChatParticipant participant : firstPage.getItems()) {
                    // Take further action.
                }
                retrieveNextParticipantsPages(firstPage.getPageId(), pageCollection);
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                // Handle error.
            }
         }
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});

void listChatParticipantsNext(String nextLink,
    AsyncPagedDataCollection<Page<ChatParticipant>> pageCollection) {
        @Override
        public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
            for (ChatParticipant participant : nextPage.getItems()) {
                // Take further action.
            }
            if (nextPage.getPageId() != null) {
                retrieveNextParticipantsPages(nextPage.getPageId(), pageCollection);
            }
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            // Handle error.
        }
}
```

Cserélje le `<thread_id>` a elemet arra a szál-azonosítóra, amellyel a felhasználókat listázni szeretné.

## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

Az `<REMOVE A USER>` megjegyzést írja felül az alábbi kóddal:

```java
// The unique ID of the thread.
final String threadId = "<thread_id>";
// The unique ID of the participant.
final String participantId = "<participant_id>";
threadClient.removeChatParticipant(threadId, participantId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

1. Cserélje le `<thread_id>` a elemet a szál-azonosítóra, amely eltávolítja a felhasználót a alkalmazásból.
1. Cserélje le az `<participant_id>` t az eltávolítandó résztvevő kommunikációs szolgáltatások felhasználói azonosítójával.

## <a name="run-the-code"></a>A kód futtatása

A Android Studio nyomja meg a Futtatás gombot a projekt létrehozásához és futtatásához. A konzolon megtekintheti a kód kimenetét és a ChatClient származó naplózó kimenetet.
