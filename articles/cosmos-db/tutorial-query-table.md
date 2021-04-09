---
title: Hogyan kérdezhetők le táblaadatok az Azure Cosmos DB-ben?
description: Megtudhatja, hogyan kérdezheti le a Azure Cosmos DB Table API-fiókban tárolt adatlekérdezéseket OData szűrők és LINQ-lekérdezések használatával
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 06/05/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: e184d85e3daee41f530334aa0034fc98f40a8766
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93099220"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>Oktatóanyag: Az Azure Cosmos DB lekérdezése a Tábla API használatával
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Az Azure Cosmos DB [Tábla API](table-introduction.md) támogatja a kulcs/érték (tábla) adatok OData- és [LINQ](/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service)-lekérdezéseit.  

Ez a cikk a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatok lekérdezése a Tábla API-val

A cikkben szereplő lekérdezések a következő minta `People` táblát használják:

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 |

A [Táblák és entitások lekérdezése](/rest/api/storageservices/fileservices/querying-tables-and-entities) című szakaszban talál további részleteket a Tábla API-val való lekérdezés menetéről.

További információ az Azure Cosmos DB által nyújtott prémium képességekről: [Azure Cosmos DB Tábla API](table-introduction.md) és [Fejlesztés a Tábla API-val .NET-keretrendszerben](tutorial-develop-table-dotnet.md).

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések működéséhez Azure Cosmos DB-fiókkal kell rendelkeznie, és a tárolóban entitásadatoknak kell lennie. Nem rendelkezik ezekkel? A fiók létrehozásához és az adatbázis feltöltéséhez végezze el az [öt perces gyors útmutatót](create-table-dotnet.md) vagy a [fejlesztői oktatóanyagot](tutorial-develop-table-dotnet.md).

## <a name="query-on-partitionkey-and-rowkey"></a>Lekérdezés PartitionKey és RowKey tulajdonságok esetén

Mivel a PartitionKey és a RowKey tulajdonságok képezik az entitás fő kulcsát, a következő speciális szintaxis segítségével azonosíthatja az entitást:

**Lekérdezés**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```

**Results (Eredmények)**

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

A `$filter` lehetőség részeként is meghatározhatja ezeket a tulajdonságokat, a következő szakaszban látható módon. Vegye figyelembe, hogy a kulcstulajdonság nevei és az állandó értékek megkülönböztetik a kis- és nagybetűket. A PartitionKey és a RowKey tulajdonság típusa egyaránt sztring.

## <a name="query-by-using-an-odata-filter"></a>Lekérdezés OData-szűrővel

Szűrő sztringjének felépítésekor ne feledje ezeket a szabályokat:

* Az OData-protokollspecifikáció által definiált logikai operátorokat használja a tulajdonságok és az értékek összehasonlítására. Vegye figyelembe, hogy nem hasonlíthat össze tulajdonságot dinamikus értékkel. A kifejezés egyik oldalának állandónak kell lennie.
* A tulajdonság nevét, az operátort és az állandó értéket URL-kódolású szóközzel kell elválasztani. A szóköz URL-kódolása `%20`.
* A szűrési sztring minden része megkülönbözteti a kis- és nagybetűket.
* Az állandó érték adattípusának meg kell egyeznie a tulajdonságéval ahhoz, hogy a szűrő érvényes eredményeket adjon vissza. További információ a támogatott tulajdonságtípusokról: [A Table szolgáltatás adatmodelljének ismertetése](/rest/api/storageservices/understanding-the-table-service-data-model).

A következő példalekérdezés bemutatja, hogyan szűrhet a PartitionKey és az E-mail tulajdonságok alapján OData `$filter` használatával.

**Lekérdezés**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

További információ a szűrőkifejezések felépítéséről különböző adattípusokhoz:[Táblák és entitások lekérdezése](/rest/api/storageservices/querying-tables-and-entities).

**Results (Eredmények)**

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Smith |Ben | Ben@contoso.com| 425-555-0102 |

A DateTime tulajdonságok lekérdezései nem adnak vissza semmilyen értéket, ha Azure Cosmos DB Table API hajtja végre. Míg az Azure Table Storage a határidők időbeli részletességével tárolja a dátum értékeket, a Azure Cosmos DB Table API a  `_ts` tulajdonságot használja. A `_ts` tulajdonság második részletességi szinten van, amely nem OData-szűrő. Így az időbélyeg-tulajdonságok lekérdezéseit a Azure Cosmos DB blokkolja. Megkerülő megoldásként meghatározhat egy egyéni datetime vagy hosszú adattípus-tulajdonságot, és beállíthatja a Date értéket az ügyfélről.

## <a name="query-by-using-linq"></a>Lekérdezés a LINQ használatával 
A LINQ használatával is végezhet lekérdezést, amelyet a rendszer a megfelelő OData-lekérdezési kifejezésekre fordít. A következő példa bemutatja, hogyan építhetők fel lekérdezések a .NET SDK-val:

```csharp
IQueryable<CustomerEntity> linqQuery = table.CreateQuery<CustomerEntity>()
            .Where(x => x.PartitionKey == "4")
            .Select(x => new CustomerEntity() { PartitionKey = x.PartitionKey, RowKey = x.RowKey, Email = x.Email });
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Megtudta, hogyan végezhet lekérdezést a Tábla API-val

Továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan terjesztheti az adatait globálisan.

> [!div class="nextstepaction"]
> [Globális adatterjesztés](tutorial-global-distribution-table.md)
