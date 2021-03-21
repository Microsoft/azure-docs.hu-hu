---
title: 'HTTP-kérelem aláírása C-vel #'
description: Ez az oktatóanyag a HTTP-kérelem aláírásának C# verzióját ismerteti az Azure kommunikációs szolgáltatások HMAC aláírásával.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 03/10/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 34c7df2b0e61536c0b5f0bc1e4a97d58d0d9c6a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490508"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört.
- A [Visual Studio](https://visualstudio.microsoft.com/downloads/) telepítése.
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. Részletekért lásd: [Azure kommunikációs szolgáltatások erőforrásának létrehozása](../../quickstarts/create-communication-resource.md). Ebben az oktatóanyagban rögzítenie kell a **resourceEndpoint** és a **resourceAccessKey** .

## <a name="sign-an-http-request-with-c"></a>HTTP-kérelem aláírása C-vel #

Az elérési kulcs hitelesítése egy közös titkos kulcsot használ az egyes HTTP-kérelmekhez tartozó HMAC aláírás létrehozásához. Ez az aláírás a SHA256 algoritmussal jön létre, és a `Authorization` fejlécben a séma használatával lesz elküldve `HMAC-SHA256` . Például:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

A a `hmac-sha256-signature` következőkből áll:

- HTTP-művelet (például `GET` vagy `PUT` )
- HTTP-kérelem elérési útja
- Date
- Gazdagép
- x-MS-Content-sha256

## <a name="setup"></a>Beállítás

Az alábbi lépések azt ismertetik, hogyan kell létrehozni az engedélyezési fejlécet.

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Egy konzolablak, például a cmd, a PowerShell vagy a bash használatával `dotnet new` hozzon létre egy új, a nevű Console-alkalmazást a paranccsal `SignHmacTutorial` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: **program. cs**.

```console
dotnet new console -o SignHmacTutorial
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az `dotnet build` alkalmazás fordításához használja az parancsot.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>A csomag telepítése

Telepítse a `Newtonsoft.Json` törzs szerializálásához használt csomagot.

```console
dotnet add package Newtonsoft.Json
```

Frissítse a `Main` metódus deklarációját az aszinkron kód támogatásához. A következő kód használatával kezdheti meg a műveletet.

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here.
        }
    }
}

```

## <a name="create-a-request-message"></a>Kérelem üzenet létrehozása

Ebben a példában egy új identitás létrehozásához a kommunikációs szolgáltatások hitelesítési API (verzió) használatával fogunk aláírni egy kérést `2021-03-07` .

Adja hozzá a következő kódot a `Main` metódushoz:

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create a uri you are going to call.
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
//Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Cserélje le a `resourceEndpoint` értéket a valódi erőforrás-végpont értékére.

## <a name="create-a-content-hash"></a>Tartalom kivonatának létrehozása

A tartalom kivonata a HMAC aláírása részét képezi. A tartalom kivonatának kiszámításához használja a következő kódot. Ezt a metódust `Progam.cs` a `Main` metódusban adhatja hozzá.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Aláírás kiszámítása

A következő kód használatával hozhat létre egy metódust a HMAC aláírásához.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Cserélje le a- `resourceAccessKey` t a valós kommunikációs szolgáltatások erőforrásának hozzáférési kulcsára.

## <a name="create-an-authorization-header-string"></a>Engedélyezési fejléc karakterláncának létrehozása

Most létrehozjuk az engedélyezési fejléchez hozzáadni kívánt karakterláncot.

1. Tartalom kivonatának kiszámítása
1. Az egyezményes világidő (UTC) időbélyegének meghatározása.
1. Készítse elő az aláíráshoz használandó karakterláncot.
1. Az aláírás kiszámítása.
1. Összefűzi a karakterláncot, amelyet a rendszer az engedélyezési fejlécben fog használni.
 
Adja hozzá a következő kódot a `Main` metódushoz:

```csharp
// Compute a content hash.
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp.
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign.
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature.
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in the authorization header.
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Fejlécek hozzáadása a requestMessage

A következő kód használatával adja hozzá a szükséges fejléceket a számára `requestMessage` .

```csharp
//Add a content hash header.
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//Add a date header.
requestMessage.Headers.Add("Date", date);
//Add an authorization header.
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Az ügyfél tesztelése

Hívja meg a végpontot a használatával `HttpClient` , és vizsgálja meg a választ.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
