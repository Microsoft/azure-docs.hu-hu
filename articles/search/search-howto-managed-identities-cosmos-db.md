---
title: Cosmos DB-fiókkal létesített kapcsolatok beállítása felügyelt identitás használatával
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan állíthat be indexelő-kapcsolatokat egy Cosmos DB-fiókhoz felügyelt identitás használatával
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2a1744feedc3e0ffae6cf2cd45cd090a6c2f06d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422093"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>Indexelő-kapcsolatok beállítása egy Cosmos DB-adatbázishoz felügyelt identitás használatával

Ez a lap azt ismerteti, hogyan állítható be egy indexelő-kapcsolódás egy Azure Cosmos DB-adatbázishoz felügyelt identitás használatával ahelyett, hogy hitelesítő adatokat kellene megadnia az adatforrás-objektum kapcsolódási karakterláncában.

Mielőtt többet szeretne megtudni a szolgáltatásról, javasoljuk, hogy Ismerje meg, mi az indexelő, és hogyan állítható be egy indexelő az adatforráshoz. További információt az alábbi hivatkozásokon talál:

* [Az indexelő áttekintése](search-indexer-overview.md)
* [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>A kapcsolatok beállítása felügyelt identitás használatával

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – a rendszerhez rendelt felügyelt identitás bekapcsolása

Ha egy rendszerhez rendelt felügyelt identitás engedélyezve van, az Azure létrehoz egy identitást a keresési szolgáltatáshoz, amely az ugyanazon a bérlőn és előfizetésen belüli más Azure-szolgáltatásokban való hitelesítéshez használható. Ezt az identitást használhatja az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) hozzárendeléseiben, amelyek lehetővé teszik az adatokhoz való hozzáférést az indexelés során.

![A rendszerhez rendelt felügyelt identitás bekapcsolása](./media/search-managed-identities/turn-on-system-assigned-identity.png "A rendszerhez rendelt felügyelt identitás bekapcsolása")

A **Mentés** gombra kattintva megjelenik egy objektumazonosító, amely hozzá van rendelve a keresési szolgáltatáshoz.

![Objektumazonosító](./media/search-managed-identities/system-assigned-identity-object-id.png "Objektumazonosító")
 
### <a name="2---add-a-role-assignment"></a>2 – szerepkör-hozzárendelés hozzáadása

Ebben a lépésben az Azure Cognitive Search-szolgáltatás engedélyt ad az adatok olvasására a Cosmos DB-adatbázisból.

1. A Azure Portal navigáljon ahhoz a Cosmos DB-fiókhoz, amely az indexelni kívánt adattartalomot tartalmazza.
2. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd **adja hozzá a szerepkör-hozzárendelést**

    ![Szerepkör-hozzárendelés hozzáadása](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Szerepkör-hozzárendelés hozzáadása")

4. **Cosmos db fiók-olvasó szerepkör** kiválasztása
5. Az **Azure ad-felhasználó,-csoport vagy-szolgáltatásnév** **hozzáférésének** elhagyása
6. Keresse meg a keresési szolgáltatást, jelölje ki, majd kattintson a **Mentés** gombra.

    ![Olvasó-és adathozzáférési szerepkör-hozzárendelés hozzáadása](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Olvasó-és adathozzáférési szerepkör-hozzárendelés hozzáadása")

### <a name="3---create-the-data-source"></a>3 – az adatforrás létrehozása

A [REST API](/rest/api/searchservice/create-data-source), Azure Portal és a [.net SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) támogatja a felügyelt identitás-kapcsolatok karakterláncát. Az alábbi példa bemutatja, hogyan hozhat létre egy adatforrást a Cosmos DB adatainak indexeléséhez a [REST API](/rest/api/searchservice/create-data-source) és egy felügyelt identitás-kapcsolódási karakterlánc használatával. A felügyelt identitás-kapcsolatok karakterlánc-formátuma megegyezik a REST API, a .NET SDK és a Azure Portal esetében.

Ha felügyelt identitásokat használ a hitelesítéshez, a **hitelesítő adatok** nem tartalmazzák a fiók kulcsát.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

A kérelem törzse tartalmazza az adatforrás definícióját, amelynek tartalmaznia kell a következő mezőket:

| Mező   | Leírás |
|---------|-------------|
| **név** | Kötelező. Válasszon egy tetszőleges nevet az adatforrás-objektum megjelenítéséhez. |
|**típusa**| Kötelező. Kell lennie `cosmosdb` . |
|**hitelesítő adatok** | Kötelező. <br/><br/>Felügyelt identitással való kapcsolódás esetén a **hitelesítő adatok** formátumának a következőket kell tartalmaznia: *adatbázis = [adatbázis-név]; ResourceId = [erőforrás-azonosító-string];(ApiKind = [API-Kind];)*<br/> <br/>A ResourceId formátuma: *ResourceId =/Subscriptions/**az előfizetés-azonosítója**/resourceGroups/**az erőforráscsoport neve**/Providers/Microsoft.DocumentDB/databaseAccounts/**a Cosmos db-fiók neve**/;*<br/><br/>SQL-gyűjtemények esetén a ApiKind nem szükséges a kapcsolatok karakterlánca.<br/><br/>A MongoDB-gyűjtemények esetében adja hozzá a **ApiKind = MongoDB** karakterláncot a kapcsolódási sztringhez. <br/><br/>A Gremlin gráfok és a Cassandra-táblázatok esetében regisztráljon a [GateD indexelő előzetes](https://aka.ms/azure-cognitive-search/indexer-preview) verziójára, és kérjen hozzáférést az előzetes verzióhoz, és tájékozódjon a hitelesítő adatok formázásáról.<br/>|
| **tároló** | A következő elemeket tartalmazza: <br/>**név**: kötelező. Az indexelni kívánt adatbázis-gyűjtemény AZONOSÍTÓjának meghatározása.<br/>**lekérdezés**: nem kötelező. Megadhat egy lekérdezést, amely egy tetszőleges JSON-dokumentumot lelapul egy olyan egyszerű sémába, amelyet az Azure Cognitive Search tud indexelni.<br/>A MongoDB API, a Gremlin API és a Cassandra API esetében a lekérdezések nem támogatottak. |
| **dataChangeDetectionPolicy** | Ajánlott |
|**dataDeletionDetectionPolicy** | Választható |

### <a name="4---create-the-index"></a>4 – az index létrehozása

Az index meghatározza a dokumentumok, attribútumok és más, a keresési élményt formáló szerkezetek mezőit.

Ebből a témakörből megtudhatja, hogyan hozhat létre egy indexet egy kereshető `booktitle` mezővel:

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

További információk az indexek létrehozásáról: [create index](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 – az indexelő létrehozása

Az indexelő egy adatforrást hoz létre a cél keresési indexszel, és az Adatfrissítés automatizálására szolgáló ütemtervet biztosít.

Miután létrehozta az indexet és az adatforrást, készen áll az indexelő létrehozására.

Példa indexelő definícióra:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Ez az indexelő két óránként fut (az ütemezett időköz értéke "PT2H"). Az indexelő 30 percenkénti futtatásához állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz 5 perc. Az ütemterv nem kötelező – ha nincs megadva, az indexelő csak egyszer fut a létrehozáskor. Az indexelő igény szerinti futtatása azonban bármikor elvégezhető.   

Az indexelő API létrehozásával kapcsolatos további információkért tekintse meg az [Indexelő létrehozása](/rest/api/searchservice/create-indexer)című leírást.

Az indexelő-ütemtervek definiálásával kapcsolatos további információkért lásd: [Az Azure Cognitive Search indexelő szolgáltatásának beosztása](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha azt tapasztalja, hogy nem tudja indexelni a Cosmos DB adatait, vegye figyelembe a következőket:

1. Ha nemrég elforgatta a Cosmos DB-fiók kulcsait, akkor a felügyelt identitás-kapcsolódási karakterlánc működéséhez akár 15 percet is várnia kell.

1. Ellenőrizze, hogy a Cosmos DB-fiók hozzáférése korlátozott-e a hálózatok kiválasztásához. Ha igen, tekintse át az [Indexelő hozzáférését az Azure hálózati biztonsági funkciói által védett tartalmakhoz](search-indexer-securing-resources.md)című témakört.

## <a name="next-steps"></a>Következő lépések

* [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)