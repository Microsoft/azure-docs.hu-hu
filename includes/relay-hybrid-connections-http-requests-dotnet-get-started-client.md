---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 08/16/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 8d73a22473ffff358c7424249c7581f6af740718
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116133"
---
### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Ha letiltotta az „Ügyfél-hitelesítés szükséges” beállítást a továbbító létrehozásakor, akkor bármilyen böngészővel küldhet kérelmeket a hibrid kapcsolat URL-címére. A védett végpontok eléréséhez létre kell hoznia és továbbítania kell egy tokent a `ServiceBusAuthorization` fejlécben, az itt bemutatott módon.

Hozzon létre egy új **Konzolalkalmazás- (.NET-keretrendszer-)** projektet a Visual Studióban.

### <a name="add-the-relay-nuget-package"></a>A Relay NuGet-csomag hozzáadása

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.
2. Válassza az **Előzetes verzió belefoglalása** lehetőséget. 
3. Válassza a **Tallózás** elemet, majd keressen rá a **Microsoft.Azure.Relay** kifejezésre. Válassza ki a **Microsoft Azure Relay** elemet a keresési eredmények közül.
4. Válassza a **2.0.0-preview1-20180523** lehetőséget a verzió megadásakor. 
5. Kattintson a **Telepítés** gombra a telepítés befejezéséhez. Zárja be a párbeszédpanelt.

### <a name="write-code-to-send-requests"></a>Kód írása kérelmek küldéséhez

1. A Program.cs fájl elején cserélje le a meglévő `using`-utasításokat az alábbi `using`-utasításokra:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Net.Http;
    using Microsoft.Azure.Relay;
    ```
2. Adjon állandókat a `Program` osztályhoz a hibrid kapcsolat részleteivel. Cserélje le a zárójelben lévő helyőrzőket a hibrid kapcsolat létrehozásakor beszerzett megfelelő értékekre. Ügyeljen arra, hogy a teljes névtérnevet használja.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Adja hozzá a következő metódust a `Program` osztályhoz:
   
    ```csharp
    private static async Task RunAsync()
    {
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());        Console.ReadLine();
    }
    ```
4. Adja hozzá a következő kódsort a `Main` metódushoz a `Program` osztályban.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    A Program.cs fájlnak így kell kinéznie:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```

