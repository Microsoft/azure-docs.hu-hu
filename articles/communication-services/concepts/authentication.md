---
title: Hitelesítés az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg, hogy az alkalmazás vagy szolgáltatás milyen módon tud hitelesíteni a kommunikációs szolgáltatásokban.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 928737608ae3e3e44b352724713a284ff9a45da9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939609"
---
# <a name="authenticate-to-azure-communication-services"></a>Hitelesítés az Azure kommunikációs szolgáltatásokban

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Ez a cikk tájékoztatást nyújt az ügyfelek Azure kommunikációs szolgáltatásokhoz való hitelesítéséről *hozzáférési kulcsok* és *felhasználói hozzáférési tokenek*használatával. Az Azure kommunikációs szolgáltatásokkal való kommunikációt minden ügyfélnek hitelesíteni kell.

Az alábbi táblázat ismerteti, hogy az Azure kommunikációs szolgáltatások ügyféloldali kódtárai milyen hitelesítési lehetőségeket támogatnak:

| Ügyfélkódtár | Hozzáférési kulcs    | Felhasználói hozzáférési tokenek |
| -------------- | ------------- | ------------------ |
| Felügyelet | Támogatott     | Nem támogatott      |
| SMS            | Támogatott     | Nem támogatott      |
| Csevegés           | Nem támogatott | Támogatott          |
| Hívó        | Nem támogatott | Támogatott          |

Az egyes engedélyezési beállítások rövid ismertetését az alábbiakban találja:

- **Hozzáférési kulcs** hitelesítése az SMS-és felügyeleti műveletekhez. A hozzáférési kulcs hitelesítése megbízható szolgáltatási környezetben futó alkalmazások esetében megfelelő. Egy hozzáférési kulccsal való hitelesítéshez az ügyfél létrehoz egy [kivonatoló módszerként szolgáló hitelesítési kódot (HMAC)](https://en.wikipedia.org/wiki/HMAC) , és az `Authorization` összes HTTP-kérelem fejlécében tartalmazza azt. További információ: [hitelesítés egy hozzáférési kulccsal](#authenticate-with-an-access-key).
- **Felhasználói hozzáférési jogkivonat** hitelesítése csevegéshez és híváshoz. A felhasználói hozzáférési tokenek lehetővé teszik az ügyfélalkalmazások számára, hogy közvetlenül az Azure kommunikációs szolgáltatásokkal hitelesítsék magukat. Ezek a tokenek a létrehozott kiszolgálóoldali jogkivonat-létesítési szolgáltatás alapján jönnek létre. Ezeket a rendszer a tokent használó ügyféleszközök számára adja meg a csevegés inicializálásához és az ügyféloldali kódtárak meghívásához. További információ: [hitelesítés felhasználói hozzáférési jogkivonattal](#authenticate-with-a-user-access-token).

## <a name="authenticate-with-an-access-key"></a>Hitelesítés hozzáférési kulccsal

Az elérési kulcs hitelesítése egy közös titkos kulccsal létrehoz egy HMAC a SHA256 algoritmus használatával kiszámított HTTP-kérelmekhez, és elküldi azt a `Authorization` fejlécben a `HMAC-SHA256` séma használatával.

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

A hozzáférési kulcs hitelesítését használó Azure kommunikációs szolgáltatások ügyféloldali kódtárait inicializálni kell az erőforrás kapcsolati karakterláncával. Ha nem ügyfél-függvénytárat használ, programozott módon hozhatja elő a HMACs az erőforrás elérési kulcsa alapján. A kapcsolódási karakterláncokkal kapcsolatos további tudnivalókért tekintse meg az [erőforrás-kiépítési](../quickstarts/create-communication-resource.md)útmutatót.

### <a name="sign-an-http-request"></a>HTTP-kérelem aláírása

Ha nem használ ügyféloldali kódtárat az Azure kommunikációs szolgáltatások REST API-khoz való HTTP-kérések elvégzéséhez, az egyes HTTP-kérelmekhez programozott módon kell létrehoznia a HMACs. A következő lépések azt ismertetik, hogyan kell létrehozni az engedélyezési fejlécet:

1. A kérelemben a `x-ms-date` fejlécben vagy a szabványos HTTP-fejlécben szereplő kérések egyezményes világidő (UTC) időbélyegét határozza meg `Date` . A szolgáltatás ellenőrzi, hogy van-e bizonyos biztonsági támadásokkal szembeni védelem, beleértve az újrajátszás elleni támadásokat is.
1. A HTTP-kérés törzsét a SHA256 algoritmus használatával végezze el, majd továbbítsa a kérelemmel a `x-ms-content-sha256` fejlécen keresztül.
1. Hozza létre az aláírandó karakterláncot a HTTP-művelet (pl. `GET` vagy `PUT` ), a HTTP-kérés elérési útja és a és a HTTP-fejléc értékeinek összefűzésével `Date` `Host` `x-ms-content-sha256` a következő formátumban:
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. Hozzon létre egy HMAC-256 aláírást az előző lépésben létrehozott UTF-8 kódolású karakterláncból. Ezután kódolja az eredményeket Base64-ként. Vegye figyelembe, hogy a Storage-fiók kulcsának Base64-dekódolására is szüksége van. A következő formátumot használja (pszeudo-kódként jelenik meg):
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_azure_storage_account_shared_key>)))
    ```
1. A következő módon adhatja meg az engedélyezési fejlécet:
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    Ahol a `<hmac-sha256-signature>` HMAC az előző lépésben számítja ki.

## <a name="authenticate-with-a-user-access-token"></a>Hitelesítés felhasználói hozzáférési jogkivonattal

A felhasználói hozzáférési tokenek lehetővé teszik az ügyfélalkalmazások számára, hogy közvetlenül az Azure kommunikációs szolgáltatásokkal hitelesítsék magukat. Ennek eléréséhez állítson be egy megbízható szolgáltatást, amely hitelesíti az alkalmazás felhasználóit, és kiadja a felhasználói hozzáférési jogkivonatokat a felügyeleti ügyféloldali kódtár használatával. Az architektúrával kapcsolatos szempontokkal kapcsolatos további információkért tekintse meg az [ügyfél és a kiszolgáló architektúra](./client-and-server-architecture.md) fogalmi dokumentációját.

Az `CommunicationClientCredential` osztály tartalmazza azt a logikát, amely a felhasználói hozzáférési jogkivonat hitelesítő adatainak megadására szolgál az ügyféloldali kódtárak számára, és kezeli az életciklusát.

### <a name="initialize-the-client-libraries"></a>Az ügyféloldali kódtárak inicializálása

A felhasználói hozzáférési jogkivonat hitelesítését igénylő Azure kommunikációs szolgáltatások ügyféloldali kódtárainak inicializálásához először létre kell hoznia a osztály egy példányát `CommunicationClientCredential` , majd egy API-ügyfél inicializálásához használhatja azt.

A következő kódrészletek bemutatják, hogyan inicializálhatja a csevegési ügyfél függvénytárát felhasználói hozzáférési jogkivonattal:

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>Felhasználói hozzáférési tokenek frissítése

A felhasználói hozzáférési tokenek olyan rövid élettartamú hitelesítő adatok, amelyeket újra kell adni ahhoz, hogy a felhasználók nem tapasztalják a szolgáltatások megszakadását. A `CommunicationUserCredential` konstruktor egy frissítési visszahívási függvényt fogad el, amely lehetővé teszi a felhasználói hozzáférési tokenek lejárata előtti frissítését. Ezt a visszahívást kell használnia ahhoz, hogy egy új felhasználói hozzáférési tokent beolvasson a megbízható szolgáltatásból.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

A `refreshProactively` beállítással eldöntheti, hogyan fogja kezelni a jogkivonat életciklusát. Alapértelmezés szerint, ha egy jogkivonat elavult, a visszahívás letiltja az API-kérelmeket, és megkísérli a frissítését. Ha a `refreshProactively` visszahívásra van beállítva, a `true` jogkivonat lejárata előtt aszinkron módon van ütemezve és végrehajtva.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)

További információkért tekintse át a következő cikkeket:
- [Az ügyfél és a kiszolgáló architektúrájának megismerése](../concepts/client-and-server-architecture.md)
