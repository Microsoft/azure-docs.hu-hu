---
title: 'HTTP-kérelem aláírása C-vel #'
description: Egy HTTP-kérelem aláírásának C# verziója a kommunikációs szolgáltatások HMAC aláírásával.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 3c1b56f81e5164bbdfa94fdaeca5f5f1f55b3b51
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552348"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:
- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört. 
- A [Visual Studio](https://visualstudio.microsoft.com/downloads/) telepítése 
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../../quickstarts/create-communication-resource.md). Ebben az oktatóanyagban rögzítenie kell a **resourceEndpoint** és a  **resourceAccessKey** .



## <a name="sign-an-http-request-with-c"></a>HTTP-kérelem aláírása C-vel #
Az elérési kulcs hitelesítése egy közös titkos kulcsot használ az egyes HTTP-kérelmekhez tartozó HMAC aláírás létrehozásához. Ezt az aláírást a rendszer a SHA256 algoritmussal hozza létre, és a `Authorization` fejlécben a séma használatával elküldi `HMAC-SHA256` . Például:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

A a `hmac-sha256-signature` következőkből áll: 

- HTTP-művelet (például `GET` vagy `PUT` )
- HTTP-kérelem elérési útja
- Date
- Gazdagép
- x-MS-Content-sha256

## <a name="setting-up"></a>Beállítás
A következő lépések azt ismertetik, hogyan kell létrehozni az engedélyezési fejlécet:

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `SignHmacTutorial` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájlban: **program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába, és használja az `dotnet build` parancsot az alkalmazás fordításához.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>A csomag telepítése

Telepítse a `Newtonsoft.Json` törzs szerializálásához használt csomagot:

```console
dotnet add package Newtonsoft.Json
```

Frissítse a `Main` metódus deklarációját az aszinkron kód támogatásához. A kezdéshez használja a következő kódot:

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
            // Tutorial code goes here
        }
    }
}

```
## <a name="create-a-request-message"></a>Kérelem üzenet létrehozása

Ebben a példában a kommunikációs szolgáltatások hitelesítési API-ját (verzió) használó új identitás létrehozására vonatkozó kérést fogunk aláírni `2021-03-07` .

Adja hozzá a következő kódot a `Main` metódushoz:

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create an uri you are going to call
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

## <a name="create-content-hash"></a>Tartalom kivonatának létrehozása

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
A következő kód használatával hozhat létre egy metódust a HMAC aláírásának a feldolgozásához.

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

Cserélje le a (z `resourceAccessKey` )-t a valós Azure kommunikációs szolgáltatások erőforrásának hozzáférési kulcsára.

## <a name="create-an-authorization-header-string"></a>Engedélyezési fejléc karakterláncának létrehozása

Most létrehozjuk az engedélyezési fejlécbe felvenni kívánt karakterláncot:

1. Tartalom kivonatának kiszámítása
2. Az egyezményes világidő (UTC) időbélyegének meghatározása
3. Aláírási sztring előkészítése
4. Az aláírás kiszámítása
5. A karakterlánc összefűzése, amelyet a rendszer az engedélyezési fejlécben fog használni.
 
Adja hozzá a következő kódot a `Main` metódushoz:

```csharp
// Compute a content hash
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in authorization header
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Fejlécek hozzáadása a requestMessage

A következő kód használatával adja hozzá a szükséges fejléceket a számára `requestMessage` :

```csharp
//Add content hash header
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//add date header
requestMessage.Headers.Add("Date", date);
//add Authorization header
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Az ügyfél tesztelése
Hívja meg a végpontot a használatával `HttpClient` , és keresse meg a választ.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
