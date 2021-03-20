---
title: Azure Cosmos DB Graph-adatbázis regionális végpontjai
description: Megtudhatja, hogyan csatlakozhat az alkalmazáshoz a legközelebbi gráf-adatbázis-végponthoz
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/09/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3e30252d8f5e80538139f8100f1070385c1b6016
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93361787"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Regionális végpontok Azure Cosmos DB Graph-fiókhoz
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB Graph-adatbázis [globálisan elosztott](distribute-data-globally.md) , így az alkalmazások több olvasási végpontot is használhatnak. Azok az alkalmazások, amelyeknek több helyen írási hozzáféréssel kell rendelkezniük, engedélyezniük kell a [többrégiós](how-to-multi-master.md) írási képességet.

Az egynél több régió kiválasztásának okai:
1. **Horizontális olvasási méretezhetőség** – az alkalmazás terhelésének növekedésével körültekintő lehet az olvasási forgalom átirányítása különböző Azure-régiókba.
2. **Alacsonyabb késés** – az egyes bejárások hálózati késését az olvasási és írási forgalom továbbításával csökkentheti a legközelebbi Azure-régióra.

Az **adattárolásra** vonatkozó követelményt úgy érheti el, ha Cosmos db fiókra vonatkozó Azure Resource Manager házirendet állít be. Az ügyfél korlátozhatja azokat a régiókat, amelyekben Cosmos DB replikálja az adatforrásokat.

## <a name="traffic-routing"></a>Forgalom útválasztása

Cosmos DB gráf-adatbázismotor több régióban fut, amelyek mindegyike több fürtöt tartalmaz. Minden fürt több száz gépet tartalmaz. Cosmos DB Graph-fiók DNS CNAME *accountname.Gremlin.Cosmos.Azure.com* feloldja A fürt egy rekordját a DNS-ben. A terheléselosztó egyetlen IP-címe elrejti a belső fürt topológiáját.

A rendszer egy regionális DNS CNAME rekordot hoz létre Cosmos DB gráf-fiók minden régiójához. A regionális végpont formátuma *accountname-Region.Gremlin.Cosmos.Azure.com*. A regionális végpont területi szegmensét az [Azure-régió](https://azure.microsoft.com/global-infrastructure/regions) nevéből származó összes szóköz eltávolításával szerzi be a rendszer. Például a `"East US 2"` `"contoso"` globális adatbázis-fiók régiójának DNS CNAME *contoso-eastus2.Gremlin.Cosmos.Azure.com* kell lennie.

A TinkerPop Gremlin-ügyfél egyetlen kiszolgálóval való együttműködésre lett tervezve. Az alkalmazás globális írható DNS CNAME-t használhat az olvasási és írási forgalomhoz. A régiót támogató alkalmazásoknak regionális végpontot kell használniuk az olvasási forgalomhoz. Csak akkor használja a regionális végpontot írási forgalomhoz, ha az adott régió az írások fogadására van konfigurálva. 

> [!NOTE]
> Cosmos DB gráf-kezelő írási műveletet tud fogadni az olvasási régióban az írási régióba irányuló adatforgalom proxyval történő elküldésével. Nem ajánlott írásvédett régióba írni az írásokat, mivel ez növeli a bejárási késést, és a jövőben korlátozásokra is vonatkozik.

A globális adatbázis-fiók CNAME Always egy érvényes írási régióra mutat. Az írási régió kiszolgálóoldali feladatátvétele során Cosmos DB frissíti a globális adatbázis-fiók CNAME rekordját, hogy az új régióra mutasson. Ha az alkalmazás nem tudja kezelni a forgalom újrairányítását feladatátvétel után, akkor a globális adatbázis-fiók DNS-CNAME értékét kell használnia.

> [!NOTE]
> A Cosmos DB nem irányítja át a forgalmat a hívó földrajzi közelsége alapján. Az alkalmazás minden egyes alkalmazásnál kiválasztja a megfelelő régiót az alkalmazások egyedi igényeinek megfelelően.

## <a name="portal-endpoint-discovery"></a>Portál végpontjának felderítése

Az Azure Cosmos DB Graph-fiókhoz tartozó régiók listájának beszerzésének legegyszerűbb módja az Áttekintés panel Azure Portal. Működni fog olyan alkalmazások esetében, amelyek nem módosítják a régiókat gyakran, vagy úgy, hogy az alkalmazás konfigurációján keresztül frissítik a listát.

:::image type="content" source="./media/how-to-use-regional-gremlin/get-end-point-portal.png " alt-text="Cosmos DB Graph-fiók régióinak beolvasása a portálról":::

Az alábbi példa a regionális Gremlin-végpont elérésére vonatkozó általános alapelveket mutatja be. Az alkalmazásnak meg kell fontolnia, hogy hány régióban kell elküldeni a forgalmat a megfelelő Gremlin-ügyfelek számára a példányok számára.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>SDK-végpont felderítése

Az alkalmazás az [Azure Cosmos db SDK](sql-api-sdk-dotnet.md) -val képes felderíteni az olvasási és írási helyet a Graph-fiók számára. Ezek a helyszínek a kiszolgálóoldali vagy automatikus feladatátvétel manuális újrakonfigurálásával bármikor megváltoztathatók.

A TinkerPop Gremlin SDK-nak nincs olyan API-je, amely felderíti Cosmos DB Graph adatbázis-fiók régióit. A futásidejű végpontok felderítését igénylő alkalmazásoknak 2 külön SDK-t kell futtatniuk a folyamat területén.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>Következő lépések
* Az [adatbázis-fiókok felügyeletének kezelése](how-to-manage-database-account.md) a Azure Cosmos DBban
* [Magas rendelkezésre állás](high-availability.md) a Azure Cosmos DBban
* [Globális terjesztés Azure Cosmos DBokkal – a motorháztető alatt](global-dist-under-the-hood.md)
* [Azure CLI-minták](cli-samples.md) a Azure Cosmos DBhoz
