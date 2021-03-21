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
ms.openlocfilehash: 4a281e547691b7dee2f1a6d3e57b1c0030dbbd57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612357"
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

#### <a name="add-a-maven-resource-in-root-buildgradle"></a>Maven-erőforrás hozzáadása a root Build. gradle
```
allprojects {
    repositories {
        ...
        maven {
            url 'https://trouterpublicpackages.z13.web.core.windows.net'
        }
    }
```

Kattintson Android Studio a szinkronizálás most elemre.

#### <a name="alternative-to-install-libraries-through-maven"></a>Alternatív Könyvtárak telepítése a Maven használatával
Ha a gyűjteményt a [Maven](https://maven.apache.org/) Build rendszer használatával szeretné importálni a projektbe, adja hozzá az `dependencies` alkalmazás `pom.xml` fájljának szakaszához, adja meg az összetevő-azonosítóját és a használni kívánt verziót:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.7</version>
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
    private String second_user_id = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.7";
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
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient client = new ChatAsyncClient.Builder()
        .endpoint(endpoint)
        .credentialInterceptor(chain -> chain.proceed(chain.request()
                .newBuilder()
                .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                .build()))
        .build();
```

1. A használatával `ChatAsyncClient.Builder` konfigurálhatja és létrehozhatja a példányát `ChatAsyncClient` .
2. Cserélje le a- `<resource>` t a kommunikációs szolgáltatások erőforrására.
3. Cserélje le a `<user_access_token>` elemet egy érvényes kommunikációs szolgáltatás hozzáférési jogkivonatára.

## <a name="object-model"></a>Objektummodell
A következő osztályok és felületek az Azure kommunikációs szolgáltatások csevegési függvénytárának főbb funkcióit kezelik a JavaScripthez.

| Név                                   | Leírás                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozására, lekérésére és törlésére. |
| ChatThreadClient/ChatThreadAsyncClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére és a beolvasási visszaigazolásokra |

## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

A használatával `ChatClient` új szálat hozunk létre egy kezdeti felhasználóval.

Az `<CREATE A CHAT THREAD>` megjegyzés helyére írja be az alábbi kódot:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
        .setCommunicationIdentifier(new CommunicationIdentifierModel().setCommunicationUser(new CommunicationUserIdentifierModel().setId(id)))
        .setDisplayName(displayName)
);

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
  .setTopic(topic)
  .setParticipants(participants);

// optional, set a repeat request ID
final String repeatabilityRequestID = "";

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
      public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
              ChatThread chatThread = result.getChatThread();
              threadId = chatThread.getId();
              // take further action
              Log.i(TAG, "threadId: " + threadId);
      }

      public void onFailure(Throwable throwable, okhttp3.Response response) {
              // Handle error.
              Log.e(TAG, throwable.getMessage());
      }
});
```

Cserélje le `<user_id>` egy érvényes kommunikációs szolgáltatások felhasználói azonosítóját. A `threadId` későbbi lépések során a befejezési kezelőnek visszaadott válaszból fogjuk használni, ezért az `<thread_id>` osztályt cserélje le a `threadId` kérelemből, majd futtassa újra az alkalmazást.

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása

Most, hogy létrehozott egy csevegési szálat, beszerezhetünk egy `ChatThreadClient` műveletet a szálon belül. Az `<CREATE A CHAT THREAD CLIENT>` megjegyzés helyére írja be az alábbi kódot:

```
ChatThreadAsyncClient threadClient =
        new ChatThreadAsyncClient.Builder()
                .endpoint(endpoint)
                .credentialInterceptor(chain -> chain.proceed(chain.request()
                    .newBuilder()
                    .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                    .build()))
                .build();
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

Győződjön meg arról, hogy `<thread_id>` egy érvényes szál-azonosítóval lett lecserélve, és most küldünk üzenetet erre a szálra.

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

threadClient.sendChatMessage(threadId, message, new Callback<String>() {
        @Override
        public void onSuccess(String messageId, okhttp3.Response response) {
                // A string is the response returned from sending a message, it is an id,
                // which is the unique ID of the message.
                chatMessageId = messageId;
                // Take further action.
                Log.i(TAG, "chatMessageId: " + chatMessageId);
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

A beolvasás után lecseréljük a-t a későbbi módszerekhez a gyors útmutatóban, `chatMessageId` `<chat_message_id>` `chatMessageId` majd újra futtatjuk az alkalmazást.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Felhasználó felvétele a csevegési szálba résztvevőként

Az `<ADD A USER>` megjegyzés helyére írja be az alábbi kódot:

```java
//  The list of ChatParticipant to be added to the thread.
participants = new ArrayList<>();
// The display name for the thread participant.
displayName = "a new participant";
participants.add(new ChatParticipant().setCommunicationIdentifier(
          new CommunicationIdentifierModel().setCommunicationUser(
              new CommunicationUserIdentifierModel().setId(second_user_id)
          )).setDisplayName(secondUserDisplayName));
// The model to pass to the add method.
AddChatParticipantsRequest addParticipantsRequest = new AddChatParticipantsRequest()
  .setParticipants(participants);

threadClient.addChatParticipants(threadId, addParticipantsRequest, new Callback<AddChatParticipantsResult>() {
        @Override
        public void onSuccess(AddChatParticipantsResult result, okhttp3.Response response) {
                // Take further action.
                Log.i(TAG, "add chat participants success");
        }
        
        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Cserélje le a `<second_user_id>` osztályt a hozzáadni kívánt felhasználó kommunikációs szolgáltatások felhasználói azonosítójával. 

## <a name="list-users-in-a-thread"></a>Egy szál felhasználóinak listázása

Az `<LIST USERS>` megjegyzést írja felül az alábbi kóddal:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection,
            okhttp3.Response response) {
                // pageCollection enables enumerating list of chat participants.
                pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
                    @Override
                    public void onSuccess(Page<ChatParticipant> firstPage, okhttp3.Response response) {
                        for (ChatParticipant participant : firstPage.getItems()) {
                            // Take further action.
                            Log.i(TAG, "participant: " + participant.getDisplayName());
                        }
                        listChatParticipantsNext(firstPage.getNextPageId(), pageCollection);
                    }

                    @Override
                    public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                    }
                });

                @Override
                public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                }
                });
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Helyezze a következő Helper metódust a osztályba:

```java
void listChatParticipantsNext(String nextLink, AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
                for (ChatParticipant participant : nextPage.getItems()) {
                    // Take further action.
                    Log.i(TAG, "participant: " + participant.getDisplayName());
                }
                if (nextPage.getPageId() != null) {
                    listChatParticipantsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}
```


## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

Győződjön meg arról, hogy `<second_user_id>` érvényes felhasználói azonosítóra cseréli le a rendszer, ezért most eltávolítja a második felhasználót a szálból.

Az `<REMOVE A USER>` megjegyzést írja felül az alábbi kóddal:

```java
CommunicationIdentifierModel communicationIdentifierModel = new CommunicationIdentifierModel().setCommunicationUser(new CommunicationUserIdentifierModel().setId(second_user_id));
threadClient.removeChatParticipant(threadId, communicationIdentifierModel, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, okhttp3.Response response) {
        // Take further action.
        Log.i(TAG, "remove a user successfully");
    }

    @Override
    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-typing-notification"></a>Begépelési értesítés küldése

Az `<SEND A TYPING NOTIFICATION>` megjegyzést írja felül az alábbi kóddal:

```java
threadClient.sendTypingNotification(threadId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a typing notification successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-read-receipt"></a>Olvasási visszaigazolás küldése

Ügyeljen arra, hogy `<chat_message_id>` egy érvényes csevegési azonosítójú üzenettel helyettesítse be az üzenet olvasási visszaigazolását.

Az `<SEND A READ RECEIPT>` megjegyzést írja felül az alábbi kóddal:

```java
SendReadReceiptRequest readReceipt = new SendReadReceiptRequest()
    .setChatMessageId(chatMessageId);
threadClient.sendChatReadReceipt(threadId, readReceipt, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a read receipt successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="list-read-receipts"></a>Olvasási visszaigazolások listázása

Az `<READ RECEIPTS>` megjegyzést írja felül az alábbi kóddal:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
threadClient.listChatReadReceiptsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection,
                              Response response) {
            // pageCollection enables enumerating list of chat participants.
            pageCollection.getFirstPage(new Callback<Page<ChatMessageReadReceipt>>() {
                @Override
                public void onSuccess(Page<ChatMessageReadReceipt> firstPage, Response response) {
                    for (ChatMessageReadReceipt receipt : firstPage.getItems()) {
                        Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                    }
                    listChatReadReceiptsNext(firstPage.getNextPageId(), pageCollection);
                }

                @Override
                public void onFailure(Throwable throwable, Response response) {
                    Log.e(TAG, throwable.getMessage());
                }
            });
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            Log.e(TAG, throwable.getMessage());
        }
});
```

Helyezze a következő Helper metódust a osztályba:
```java
void listChatReadReceiptsNext(String nextLink, AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatMessageReadReceipt>>() {
            @Override
            public void onSuccess(Page<ChatMessageReadReceipt> nextPage, Response response) {
                for (ChatMessageReadReceipt receipt : nextPage.getItems()) {
                    Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                }
                if (nextPage.getPageId() != null) {
                    listChatReadReceiptsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}

```


## <a name="run-the-code"></a>A kód futtatása

Android Studio a projekt létrehozásához és futtatásához nyomja le a Futtatás gombot. A-konzolon megtekintheti a kód kimenetét és a ChatClient származó naplózó kimenetet.
