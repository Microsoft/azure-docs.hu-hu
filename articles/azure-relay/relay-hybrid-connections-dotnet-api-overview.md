---
title: Az Azure Relay .NET Standard API-k áttekintése | Microsoft Docs
description: Ez a cikk az Azure Relay Hibrid kapcsolatok .NET Standard API áttekintését tartalmazza.
ms.topic: article
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 724fb1a62b82036b4a0fa8b9f4f3608293f608a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625131"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Azure Relay Hibrid kapcsolatok .NET Standard API – áttekintés

Ez a cikk néhány fontos Azure Relay Hibrid kapcsolatok .NET Standard [ügyféloldali API](/dotnet/api/microsoft.azure.relay)-t összegzi.
  
## <a name="relay-connection-string-builder-class"></a>Továbbítói kapcsolatok karakterlánc-készítő osztálya

A [RelayConnectionStringBuilder][RelayConnectionStringBuilder] osztály a továbbítási hibrid kapcsolatokra vonatkozó kapcsolódási karakterláncokat formázza. A használatával ellenőrizheti a kapcsolódási karakterlánc formátumát, vagy létrehozhat egy teljesen új, a kapcsolódási sztringet. Tekintse meg a következő kódot egy példához:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

A metódushoz közvetlenül is átadhat egy kapcsolódási karakterláncot `RelayConnectionStringBuilder` . Ez a művelet lehetővé teszi annak ellenőrzését, hogy a kapcsolati karakterlánc érvényes formátumú-e. Ha a paraméterek bármelyike érvénytelen, a Konstruktor létrehoz egy `ArgumentException` .

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Hibrid kapcsolatok streamje

A [HybridConnectionStream][HCStream] osztály az adatok küldésére és fogadására használt elsődleges objektum egy Azure Relay-végpontról, függetlenül attól, hogy [HybridConnectionClient][HCClient]vagy [HybridConnectionListener][HCListener]dolgozik.

### <a name="getting-a-hybrid-connection-stream"></a>Hibrid kapcsolatok adatfolyamának beolvasása

#### <a name="listener"></a>Figyelő

[HybridConnectionListener][HCListener] objektum használatával az alábbi módon szerezheti be az `HybridConnectionStream` objektumokat:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Ügyfél

[HybridConnectionClient][HCClient] objektum használatával az alábbi módon szerezheti be az `HybridConnectionStream` objektumokat:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Adatfogadás

A [HybridConnectionStream][HCStream] osztály kétirányú kommunikációt tesz lehetővé. A legtöbb esetben folyamatosan megkapja az adatfolyamot. Ha szöveget olvas be a streamből, érdemes lehet egy [StreamReader](/dotnet/api/system.io.streamreader) objektumot használni, amely lehetővé teszi az adatok egyszerűbb elemzését. Például szövegként is beolvashatja az adatolvasást, nem pedig a következőt: `byte[]` .

A következő kód beolvassa az adatfolyam egyes sorait, amíg le nem kéri a megszakítást:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Adatok küldése

Miután létrejött a kapcsolatok, küldhet egy üzenetet a továbbítási végpontnak. Mivel a kapcsolatok objektum örökli a [streamet](/dotnet/api/system.io.stream), az adatait küldje el `byte[]` . Az alábbi példa bemutatja, hogyan teheti meg ezt:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Ha azonban közvetlenül szeretne szöveget küldeni, anélkül, hogy minden alkalommal kódolnia kellene a sztringet, akkor az `hybridConnectionStream` objektumot [StreamWriter](/dotnet/api/system.io.streamwriter) objektummal is becsomagolhatja.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Relayről, tekintse meg a következő hivatkozásokat:

* [Microsoft. Azure. Relay – dokumentáció](/dotnet/api/microsoft.azure.relay)
* [Mi az Azure Relay?](relay-what-is-it.md)
* [Elérhető Relay API-k](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
