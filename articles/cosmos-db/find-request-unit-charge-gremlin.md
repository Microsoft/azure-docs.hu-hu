---
title: Gremlin API-lekérdezésekre vonatkozó kérési egység (RU) díja Azure Cosmos DB
description: Megtudhatja, hogyan lehet megkeresni az Azure Cosmos-tárolón végrehajtott Gremlin-lekérdezésekre vonatkozó kérési egység (RU) díját. A Azure Portal, .NET, Java-illesztőprogramokat használva megkeresheti az RU díját.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 295ed3eb2312a5f614a4214b5b627f5657fba1eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201344"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>A kérések egységének megkeresése a Azure Cosmos DB Gremlin API-ban végrehajtott műveletekhez
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB számos olyan API-t támogat, mint például az SQL, a MongoDB, a Cassandra, a Gremlin és a table. Mindegyik API saját adatbázis-műveletekkel rendelkezik. Ezek a műveletek az egyszerű pont olvasási és írási műveleteiből származnak összetett lekérdezésekre. Minden adatbázis-művelet a művelet bonyolultsága alapján használja a rendszererőforrásokat.

Az összes adatbázis-művelet költségét az Azure Cosmos DB normalizálja, és ezek kérelemegységben (röviden RU) vannak kifejezve. A kérelem díja az összes adatbázis-művelet által felhasznált kérelmek egysége. Azt is megteheti, hogy az RUs olyan teljesítmény pénznemként van kiértékelve, amely a Azure Cosmos DB által támogatott adatbázis-műveletek végrehajtásához szükséges rendszererőforrásokat, például a PROCESSZORt, a IOPS és a memóriát is elvégzi. A rendszer mindig kérelemegységben számítja a költségeket attól függetlenül, hogy melyik API segítségével kommunikál az Azure Cosmos-tárolóval. Azt határozza meg, hogy az adatbázis-művelet írási, olvasási pont vagy lekérdezés-e, a költségeket mindig az RUs méri. További tudnivalókat a [kérelmek egységei](request-units.md) című cikkben talál.

Ez a cikk azokat a különböző módszereket ismerteti, amelyekkel megkeresheti a [kérési egység](request-units.md) (ru) használatát a Azure Cosmos db Gremlin API-ban lévő tárolón végrehajtott bármely művelethez. Ha más API-t használ, tekintse meg a [MongoDB](find-request-unit-charge-mongodb.md), az [Cassandra API](find-request-unit-charge-cassandra.md), az [SQL API](find-request-unit-charge.md)és a [Table API](find-request-unit-charge-table.md) cikkek című témakört az ru/s díj megkereséséhez.

A Gremlin API által visszaadott fejlécek egyéni állapot-attribútumokra vannak leképezve, amelyek jelenleg a Gremlin .NET és a Java SDK felületén vannak. A kérés díja a kulcs alatt érhető el `x-ms-request-charge` . Ha a Gremlin API-t használja, több lehetőség is rendelkezésre áll, amelyekkel megkeresheti egy művelet RU-felhasználását egy Azure Cosmos-tárolón.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-graph-gremlin-console.md#create-a-database-account) , és adja meg az adatgyűjtést, vagy válasszon ki egy olyan meglévő fiókot, amely már tartalmaz információt.

1. Lépjen a **adatkezelő** ablaktáblára, majd válassza ki a használni kívánt tárolót.

1. Adjon meg egy érvényes lekérdezést, majd válassza az **Gremlin-lekérdezés végrehajtása** lehetőséget.

1. A **lekérdezési statisztikák** lehetőség kiválasztásával jelenítheti meg a tényleges kérelmek díját a végrehajtott kérelemért.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Képernyőkép a Gremlin-lekérdezési kérelmekért a Azure Portal":::

## <a name="use-the-net-sdk-driver"></a>A .NET SDK illesztőprogramjának használata

Az [GREMLIN.net SDK](https://www.nuget.org/packages/Gremlin.Net/)használatakor az állapot attribútumai az `StatusAttributes` objektum tulajdonsága alatt érhetők el `ResultSet<>` :

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

További információ: gyors útmutató [: .NET-keretrendszer vagy Core-alkalmazás létrehozása Azure Cosmos db GREMLIN API-fiók használatával](create-graph-dotnet.md).

## <a name="use-the-java-sdk-driver"></a>A Java SDK-illesztőprogram használata

Ha a [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)-t használja, az állapot attribútumait az `statusAttributes()` objektum metódusának meghívásával kérheti le `ResultSet` :

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

További információ: gyors útmutató [: Graph-adatbázis létrehozása Azure Cosmos DBban a Java SDK használatával](create-graph-java.md).

## <a name="next-steps"></a>Következő lépések

Az RU-felhasználás optimalizálásával kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [A lekérdezési költségek optimalizálása az Azure Cosmos DB-ben](./optimize-cost-reads-writes.md)