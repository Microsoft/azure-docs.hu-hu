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
ms.openlocfilehash: dedea2a622cb0eece92bb8b57871c76daa05fb68
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495415"
---
## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt ügyeljen a következőre:

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört. 
- Telepítse a [Xcode](https://developer.apple.com/xcode/) -t és a [Cocoapods](https://cocoapods.org/)-t, majd a Xcode használatával hozzon létre egy IOS-alkalmazást a gyors üzembe helyezéshez és a Cocoapods a függőségek telepítéséhez.
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../../create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell **jegyeznie az erőforrás-végpontot** .
- Hozzon létre **két** ACS-felhasználót, és adja ki a felhasználói hozzáférési jogkivonat [felhasználói hozzáférési jogkivonatát](../../access-tokens.md). Ügyeljen arra, hogy a hatókört a **csevegés** értékre állítsa, és **jegyezze fel a jogkivonat karakterláncát, valamint a userId karakterláncot**. Ebben a rövid útmutatóban egy kezdő résztvevővel hozunk létre egy szálat, majd egy második résztvevőt is hozzáadunk a szálhoz.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-ios-application"></a>Új iOS-alkalmazás létrehozása

Nyissa meg a Xcode, és válassza a lehetőséget `Create a new Xcode project` .

A következő ablakban válassza `iOS` a platform és a sablon lehetőséget `App` .

Amikor kiválasztja a beállításokat `ChatQuickstart` , adja meg a projekt nevét. Válassza ki `Storyboard` a felületet, `UIKit App Delegate` a életciklust és `Swift` a nyelvet.

Kattintson a Tovább gombra, és válassza ki azt a könyvtárat, ahol létre szeretné hozni a projektet.

### <a name="install-the-libraries"></a>A kódtárak telepítése

A Cocoapods segítségével telepítjük a szükséges kommunikációs szolgáltatások függőségeit.

A parancssorból navigáljon az iOS-projekt gyökérkönyvtárában `ChatQuickstart` .

Hozzon létre egy Cocoapods: `pod init`

Nyissa meg a Cocoapods, és adja hozzá a következő függőségeket a `ChatQuickstart` célhoz:
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

Telepítse a függőségeket, ez egy Xcode-munkaterületet is létrehoz: `pod install`

**A pod telepítésének futtatása után nyissa meg újra a projektet a Xcode-ben az újonnan létrehozott lehetőség kiválasztásával `.xcworkspace` .**

### <a name="setup-the-placeholders"></a>A helyőrzők beállítása

Nyissa meg a munkaterületet a `ChatQuickstart.xcworkspace` Xcode, majd nyissa meg `ViewController.swift` .

Ebben a rövid útmutatóban felvesszük a kódját `viewController` , és megtekintjük a kimenetet a Xcode-konzolon. Ez a rövid útmutató nem foglalkozik a felhasználói felület iOS-ben való létrehozásával. 

A `viewController.swift` `AzureCommunication` és a kódtárak importálásának felső részén `AzureCommunicatonChat` :

```
import AzureCommunication
import AzureCommunicationChat
```

Másolja a következő kódot a `viewDidLoad()` metódusba `ViewController` :

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

A kód a bemutató célokra való szinkronizálásához egy szemafort fogunk használni. A következő lépésekben az Azure kommunikációs szolgáltatások csevegési könyvtárával cseréljük ki a helyőrzőket a mintakód használatával.


### <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása

Az `<CREATE A CHAT CLIENT>` megjegyzés helyére írja be az alábbi kódot:

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
    let credential =
    try CommunicationTokenCredential(
        token: "<ACCESS_TOKEN>"
    )
    let options = AzureCommunicationChatClientOptions()

    let chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: options
    )
```

Cserélje le az helyére az `<ACS_RESOURCE_ENDPOINT>` ACS-erőforrás végpontját.
Cserélje le `<ACCESS_TOKEN>` egy érvényes ACS hozzáférési tokent.

Ez a rövid útmutató nem fedi le a csevegési alkalmazás jogkivonatait kezelő szolgáltatási szintet, de ajánlott. További részletek a [csevegési architektúrával](../../../concepts/chat/concepts.md) kapcsolatban az alábbi dokumentációban olvashatók.

További információ a [felhasználói hozzáférési tokenekről](../../access-tokens.md).

## <a name="object-model"></a>Objektummodell 
A következő osztályok és felületek az Azure kommunikációs szolgáltatások csevegési függvénytárának főbb funkcióit kezelik a JavaScripthez.

| Név                                   | Leírás                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozásához, lekéréséhez és törléséhez. |
| ChatThreadClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére és a beolvasási visszaigazolásokra |

## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

Most a `ChatClient` segítségével hozunk létre egy új szálat egy kezdeti felhasználóval.

Az `<CREATE A CHAT THREAD>` megjegyzés helyére írja be az alábbi kódot:

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Cserélje le `<USER_ID>` egy érvényes kommunikációs szolgáltatások felhasználói azonosítóját.

Itt egy szemafort használunk, hogy megvárja a befejezési kezelőt a folytatás előtt. A `threadId` következő lépésekben a befejezési kezelőhöz visszaadott válaszból fogjuk használni.

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása

Most, hogy létrehozott egy csevegési szálat, beszerezhetünk egy `ChatThreadClient` műveletet a szálon belül.

Az `<CREATE A CHAT THREAD CLIENT>` megjegyzés helyére írja be az alábbi kódot:

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

Az `<SEND A MESSAGE>` megjegyzés helyére írja be az alábbi kódot:

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Először is `SendChatMessageRequest` létrehozjuk a tartalmat és a küldőket tartalmazó megjelenítendő nevet (amely opcionálisan tartalmazhatja a megosztási előzmények időpontját is). A befejezési kezelőnek visszaadott válasz tartalmazza az elküldött üzenet AZONOSÍTÓját.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Felhasználó felvétele a csevegési szálba résztvevőként

Az `<ADD A USER>` megjegyzés helyére írja be az alábbi kódot:

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

A helyére írja `<USER_ID>` be a hozzáadni kívánt felhasználó ACS felhasználói azonosítóját.

Ha a résztvevőt egy szálhoz adja hozzá, a válasz a befejezést követően hibákat tartalmazhat. Ezek a hibák nem felelnek meg bizonyos résztvevők hozzáadásának.

## <a name="list-users-in-a-thread"></a>Egy szál felhasználóinak listázása

Az `<LIST USERS>` megjegyzést írja felül az alábbi kóddal:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

Az `<REMOVE A USER>` megjegyzést írja felül az alábbi kóddal:

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

Cserélje le az helyére az `<USER ID>` eltávolítandó résztvevő kommunikációs szolgáltatások felhasználói azonosítóját.

## <a name="run-the-code"></a>A kód futtatása

A Xcode nyomja meg a Futtatás gombot a projekt felépítéséhez és futtatásához. A konzolon megtekintheti a kód kimenetét és a ChatClient származó naplózó kimenetet.


