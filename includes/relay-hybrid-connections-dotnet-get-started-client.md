---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 2d6836b2bf667e4170e67a95dc1daad72a769eb9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179353"
---
### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Hozzon létre egy új **Konzolalkalmazás- (.NET-keretrendszer-)** projektet a Visual Studióban.

### <a name="add-the-relay-nuget-package"></a>A Relay NuGet-csomag hozzáadása

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.
2. Válassza a **Tallózás** elemet, majd keressen rá a **Microsoft.Azure.Relay** kifejezésre. Válassza ki a **Microsoft Azure Relay** elemet a keresési eredmények közül. 
3. Kattintson a **Telepítés** gombra a telepítés befejezéséhez. Zárja be a párbeszédpanelt.

### <a name="write-code-to-send-messages"></a>Kód írása az üzenetek küldéséhez

1. A Program.cs fájl elején cserélje le a meglévő `using`-utasításokat az alábbi `using`-utasításokra:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
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
        Console.WriteLine("Enter lines of text to send to the server with ENTER");
   
        // Create a new hybrid connection client.
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var client = new HybridConnectionClient(new Uri(String.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Initiate the connection.
        var relayConnection = await client.CreateConnectionAsync();
   
        // Run two concurrent loops on the connection. One 
        // reads input from the console and writes it to the connection 
        // with a stream writer. The other reads lines of input from the 
        // connection with a stream reader and writes them to the console. 
        // Entering a blank line shuts down the write task after 
        // sending it to the server. The server then cleanly shuts down
        // the connection, which terminates the read task.
   
        var reads = Task.Run(async () => {
            // Initialize the stream reader over the connection.
            var reader = new StreamReader(relayConnection);
            var writer = Console.Out;
            do
            {
                // Read a full line of UTF-8 text up to newline.
                string line = await reader.ReadLineAsync();
                // If the string is empty or null, you are done.
                if (String.IsNullOrEmpty(line))
                    break;
                // Write to the console.
                await writer.WriteLineAsync(line);
            }
            while (true);
        });
   
        // Read from the console and write to the hybrid connection.
        var writes = Task.Run(async () => {
            var reader = Console.In;
            var writer = new StreamWriter(relayConnection) { AutoFlush = true };
            do
            {
                // Read a line from the console.
                string line = await reader.ReadLineAsync();
                // Write the line out, also when it's empty.
                await writer.WriteLineAsync(line);
                // Quit when the line is empty,
                if (String.IsNullOrEmpty(line))
                    break;
            }
            while (true);
        });
   
        // Wait for both tasks to finish.
        await Task.WhenAll(reads, writes);
        await relayConnection.CloseAsync(CancellationToken.None);
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
                Console.WriteLine("Enter lines of text to send to the server with ENTER");
   
                // Create a new hybrid connection client.
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var client = new HybridConnectionClient(new Uri(String.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
                // Initiate the connection.
                var relayConnection = await client.CreateConnectionAsync();
   
                // Run two concurrent loops on the connection. One 
                // reads input from the console and then writes it to the connection 
                // with a stream writer. The other reads lines of input from the 
                // connection with a stream reader and then writes them to the console. 
                // Entering a blank line shuts down the write task after 
                // sending it to the server. The server then cleanly shuts down
                // the connection, which terminates the read task.
   
                var reads = Task.Run(async () => {
                    // Initialize the stream reader over the connection.
                    var reader = new StreamReader(relayConnection);
                    var writer = Console.Out;
                    do
                    {
                        // Read a full line of UTF-8 text up to newline.
                        string line = await reader.ReadLineAsync();
                        // If the string is empty or null, you are done.
                        if (String.IsNullOrEmpty(line))
                            break;
                        // Write to the console.
                        await writer.WriteLineAsync(line);
                    }
                    while (true);
                });
   
                // Read from the console and write to the hybrid connection.
                var writes = Task.Run(async () => {
                    var reader = Console.In;
                    var writer = new StreamWriter(relayConnection) { AutoFlush = true };
                    do
                    {
                        // Read a line from the console.
                        string line = await reader.ReadLineAsync();
                        // Write the line out, also when it's empty.
                        await writer.WriteLineAsync(line);
                        // Quit when the line is empty.
                        if (String.IsNullOrEmpty(line))
                            break;
                    }
                    while (true);
                });
   
                // Wait for both tasks to finish.
                await Task.WhenAll(reads, writes);
                await relayConnection.CloseAsync(CancellationToken.None);
            }
        }
    }
    ```

