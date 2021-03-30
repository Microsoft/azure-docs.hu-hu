---
title: Azure Cosmos DB erőforrás-tokenek használata a Gremlin SDK-val
description: Ismerje meg, hogyan hozhat létre erőforrás-jogkivonatokat, és hogyan használhatja őket a Graph-adatbázis eléréséhez.
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/06/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 22c048b748806404ccfa580e660552a1744f3781
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93361693"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Azure Cosmos DB erőforrás-tokenek használata a Gremlin SDK-val
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Ez a cikk azt ismerteti, hogyan használhatók [Azure Cosmos db erőforrás-tokenek](secure-access-to-data.md) a Graph-adatbázis eléréséhez a Gremlin SDK-val.

## <a name="create-a-resource-token"></a>Erőforrás-jogkivonat létrehozása

Az Apache TinkerPop Gremlin SDK nem rendelkezik olyan API-val, amely erőforrás-tokenek létrehozására használható. Az *erőforrás-jogkivonat* kifejezés Azure Cosmos db koncepció. Erőforrás-tokenek létrehozásához töltse le a [Azure Cosmos db SDK](sql-api-sdk-dotnet.md)-t. Ha az alkalmazásnak erőforrás-jogkivonatokat kell létrehoznia, és azokat a Graph-adatbázis eléréséhez kell használnia, két külön SDK-t igényel.

Az erőforrás-tokenek feletti objektummodell-hierarchia a következő vázlatban látható:

- **Azure Cosmos db fiók** – a legfelső szintű entitás, amelyhez DNS társítva van (például: `contoso.gremlin.cosmos.azure.com` ).
  - **Adatbázis Azure Cosmos DB**
    - **Felhasználó**
      - **Engedély**
        - **Token** – A Permission objektum tulajdonsága, amely azt jelzi, hogy milyen műveleteket lehet engedélyezni vagy megtagadni.

Az erőforrás-jogkivonat a következő formátumot használja: `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"` . Ez a karakterlánc nem átlátszó az ügyfelek számára, és a következőképpen kell használni módosítás vagy értelmezés nélkül.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<primary key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Erőforrás-jogkivonat használata
Az erőforrás-jogkivonatok közvetlenül "password" tulajdonságként használhatók a GremlinServer osztály összeállításakor.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a primary key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Ugyanez a módszer működik az összes TinkerPop Gremlin SDK-ban.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Korlát

Egyetlen Gremlin-fiókkal korlátlan számú tokent adhat ki. Ugyanakkor legfeljebb 100 tokent használhat egyszerre 1 órán belül. Ha egy alkalmazás túllépi a jogkivonat-korlátot óránként, a rendszer megtagadja a hitelesítési kérést, és a következő hibaüzenet jelenik meg: "túllépte az engedélyezett erőforrás-jogkivonat 100-os korlátját, amely egyidejűleg használható." Nem működik olyan aktív kapcsolatok bezárásához, amelyek adott jogkivonatokat használnak az új tokenekhez tartozó tárolóhelyek felszabadításához. A Azure Cosmos DB Gremlin adatbázismotor a hitelesítési kérelem előtt közvetlenül az óra alatt nyomon követi az egyedi jogkivonatokat.

## <a name="permission"></a>Engedély

Gyakori hiba, hogy az alkalmazások az erőforrás-jogkivonatok használata közben jelentkeznek, "nincs elegendő jogosultsága az engedélyezési fejlécben a megfelelő kéréshez. Próbálkozzon újra egy másik engedélyezési fejléccel. " Ezt a hibát akkor adja vissza a rendszer, amikor egy Gremlin bejárási kísérletet tesz egy Edge vagy egy csúcspont írására, de az erőforrás-jogkivonat csak *olvasási* jogosultságot biztosít. Vizsgálja meg a bejárást, és ellenőrizze, hogy a következő lépések bármelyikét tartalmazza-e: *. addV ()*, *. addE ()*, *. drop ()*, vagy *. Property ()*.

## <a name="next-steps"></a>Következő lépések
* [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](role-based-access-control.md) Azure Cosmos db
* [Megtudhatja, hogyan védheti meg Azure Cosmos db az adathozzáférését](secure-access-to-data.md)
