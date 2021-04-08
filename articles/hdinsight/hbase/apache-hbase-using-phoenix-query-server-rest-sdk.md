---
title: Phoenix Query Server REST SDK – Azure HDInsight
description: Telepítse és használja a REST SDK-t az Azure HDInsight Phoenix Query Serverához.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/01/2020
ms.openlocfilehash: 5ce459e2820cfd2ea5cacb036f5d6885c9c5afa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933373"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Apache Phoenix lekérdezési kiszolgáló – REST SDK

A [Apache Phoenix](https://phoenix.apache.org/) egy nyílt forráskódú, nagymértékben párhuzamos, az [Apache HBase](apache-hbase-overview.md)-t tartalmazó, nagymértékben párhuzamos, összehasonlítható adatbázis-réteg. A Phoenix lehetővé teszi, hogy az SQL-szerű lekérdezéseket az HBase SSH-eszközökkel, például a [az sqlline használata](apache-hbase-query-with-phoenix.md)használatával használja. A Phoenix egy Phoenix Query Server (PQS) nevű HTTP-kiszolgálót is biztosít, amely egy olyan vékony ügyfél, amely két átviteli mechanizmust támogat az ügyfél-kommunikációhoz: JSON-és protokoll-pufferek. A protokoll-pufferek az alapértelmezett mechanizmus, amely hatékonyabb kommunikációt biztosít, mint a JSON.

Ez a cikk azt ismerteti, hogyan lehet a PQS REST SDK-val táblákat létrehozni, upsert a sorokat és tömegesen, és az SQL-utasítások használatával kijelölni az adatokat. A példák a [Apache Phoenix lekérdezési kiszolgáló Microsoft .net illesztőprogramját](https://www.nuget.org/packages/Microsoft.Phoenix.Client)használják. Ez az SDK az [Apache kalcit Avatica](https://calcite.apache.org/avatica/) API-kra épül, amelyek kizárólag a szerializálási formátumhoz használják a protokollok puffereit.

További információ: [Apache kalcit Avatica Protocol-pufferek referenciája](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Az SDK telepítése

Apache Phoenix Query Server Microsoft .NET illesztőprogramja NuGet-csomagként van megadva, amely a Visual Studio **NuGet Package Manager konzolról** telepíthető a következő paranccsal:

```console
Install-Package Microsoft.Phoenix.Client
```

## <a name="instantiate-new-phoenixclient-object"></a>Új PhoenixClient objektumának példánya

A könyvtár használatának megkezdéséhez hozzon létre egy új `PhoenixClient` objektumot, `ClusterCredentials` amely a `Uri` fürtöt és a fürt Apache Hadoop felhasználónevét és jelszavát adja meg.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Cserélje le a CLUSTERNAME-t a HDInsight HBase-fürt nevére, valamint a Felhasználónév és a jelszó helyére a fürt létrehozásához megadott Hadoop hitelesítő adatokkal. Az alapértelmezett Hadoop-Felhasználónév a **rendszergazda**.

## <a name="generate-unique-connection-identifier"></a>Egyedi kapcsolatazonosító létrehozása

Egy vagy több kérelem PQS való elküldéséhez meg kell adnia egy egyedi kapcsolatazonosító, hogy társítsa a kérés (eke) t a kapcsolódáshoz.

```csharp
string connId = Guid.NewGuid().ToString();
```

Mindegyik példa először egy hívást kezdeményez a `OpenConnectionRequestAsync` metódushoz, amely az egyedi kapcsolatazonosító alapján halad. Ezután definiálja `ConnectionProperties` és adja `RequestOptions` át ezeket az objektumokat és a generált kapcsolatazonosító a `ConnectionSyncRequestAsync` metódusnak. A PQS `ConnectionSyncRequest` objektuma segít biztosítani, hogy az ügyfél és a kiszolgáló is konzisztens képet biztosítson az adatbázis tulajdonságairól.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest és az ConnectionProperties

A híváshoz  `ConnectionSyncRequestAsync` adjon meg egy `ConnectionProperties` objektumot.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Íme néhány érdekes tulajdonság:

| Tulajdonság | Leírás |
| -- | -- |
| Autocommit | Logikai `autoCommit` érték, amely azt jelzi, hogy engedélyezve van-e a Phoenix-tranzakciókhoz. |
| ReadOnly | Logikai érték, amely azt jelzi, hogy a hálózat írásvédett-e. |
| TransactionIsolation | Egy egész szám, amely a tranzakciós elkülönítés szintjét jelöli a JDBC-specifikáció alapján – lásd a következő táblázatot.|
| Katalógus | A kapcsolódási tulajdonságok beolvasásakor használandó katalógus neve. |
| Séma | A kapcsolódási tulajdonságok beolvasásakor használandó séma neve. |
| IsDirty | Logikai érték, amely azt jelzi, hogy a tulajdonságok módosultak-e. |

Az értékek a következők `TransactionIsolation` :

| Elkülönítési érték | Leírás |
| -- | -- |
| 0 | A tranzakciók nem támogatottak. |
| 1 | A piszkos olvasások, a nem ismételhető olvasások és a látszólagos olvasások is előfordulhatnak. |
| 2 | A piszkos olvasások nem érhetők el, de előfordulhat, hogy a nem ismételhető olvasások és a látszólagos olvasások is előfordulhatnak. |
| 4 | A piszkos olvasási és nem ismételhető olvasások nem érhetők el, de a látszólagos olvasások is előfordulhatnak. |
| 8 | A piszkos olvasások, a nem ismételhető olvasások és a látszólagos olvasások mind megelőzve vannak. |

## <a name="create-a-new-table"></a>Új tábla létrehozása

A HBase, mint bármely más RDBMS, a táblákban tárolja az adattárolást. A Phoenix szabványos SQL-lekérdezéseket használ új táblák létrehozásához az elsődleges kulcs és az oszlopok típusának meghatározásakor.

Ez a példa és az összes későbbi példa az `PhoenixClient` [új PhoenixClient objektum létrehozása](#instantiate-new-phoenixclient-object)című részében definiált példányt használja.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

Az előző példa egy nevű új táblát hoz létre `Customers` a `IF NOT EXISTS` kapcsoló használatával. A `CreateStatementRequestAsync` hívás új utasítást hoz létre a Avitica-(PQS-) kiszolgálón. A `finally` blokk lezárja a visszaadott `CreateStatementResponse` és az `OpenConnectionResponse` objektumokat.

## <a name="insert-data-individually"></a>Az adatbeszúrás egyenként

Ez a példa egy egyéni adatbeszúrást mutat be, amely az `List<string>` amerikai állam és a területi rövidítések gyűjteményére hivatkozik:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

A tábla `StateProvince` oszlopának értékét egy későbbi kiválasztási műveletben fogjuk használni.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Az INSERT utasítás végrehajtásának szerkezete hasonló egy új tábla létrehozásához. A `try` blokk végén a tranzakció explicit módon véglegesítve lesz. Ez a példa egy INSERT tranzakció 300-szor ismétlődik. Az alábbi példa egy hatékonyabb kötegelt beszúrási folyamatot mutat be.

## <a name="batch-insert-data"></a>Kötegek beszúrása

A következő kód majdnem azonos az adatbeszúrási kóddal. Ez a példa az `UpdateBatch` objektumot egy hívásban használja `ExecuteBatchRequestAsync` , nem pedig többször `ExecuteRequestAsync` is előkészített utasítással.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Egy tesztkörnyezetben a 300-es új rekordok egyenként történő beszúrása csaknem 2 percet vett igénybe. Ezzel szemben a 300-as rekordok kötegként való beszúrása csak 6 másodpercet igényel.

## <a name="select-data"></a>Adatok kiválasztása

Ebből a példából megtudhatja, hogyan használhatja újra az egyik kapcsolódást több lekérdezés végrehajtásához:

1. Jelölje ki az összes rekord elemet, majd a visszaadott alapértelmezett 100 után olvassa be a fennmaradó rekordokat.
2. Az egyetlen skaláris eredmény beolvasásához használja az összes sor száma SELECT utasítást.
3. Egy SELECT utasítás végrehajtása, amely az összes ügyfél/állam vagy terület teljes számát adja vissza.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Az utasítások kimenetének `select` a következő eredménynek kell lennie:

```output
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Következő lépések

* [Apache Phoenix a HDInsightban](../hdinsight-phoenix-in-hdinsight.md)
* [Az Apache HBase REST SDK használata](apache-hbase-rest-sdk.md)
