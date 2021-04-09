---
title: Paraméteres lekérdezések a Azure Cosmos DBban
description: Ismerje meg, hogy az SQL-paraméterek lekérdezései Hogyan biztosítják a felhasználói adatok hatékony kezelését és elkerülését, valamint az SQL-injektálással való véletlen adatexpozíciót.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: dc32aab89e50b500001fd2267f62e3031154be62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96549157"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Paraméteres lekérdezések a Azure Cosmos DBban
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB támogatja a ismerős @ jelöléssel kifejezett paramétereket tartalmazó lekérdezéseket. A paraméteres SQL lehetővé teszi a felhasználói adatbevitel robusztus kezelését és megmenekülését, és megakadályozza az adatok véletlen kisugárzását az SQL-injektáláson keresztül.

## <a name="examples"></a>Példák

Írhat például egy olyan lekérdezést, amely `lastName` `address.state` paramétereket fogad el, és paraméterként végrehajtja azt, és a `lastName` `address.state` felhasználói bevitelen alapuló különböző értékekre hajtja végre.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Ezt a kérést elküldheti Azure Cosmos DB paraméteres JSON-lekérdezésként, például az alábbiak szerint:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

A következő példa a felső argumentumot egy paraméteres lekérdezéssel állítja be:

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

A paraméter értéke lehet bármilyen érvényes JSON: karakterlánc, szám, logikai érték, null, páros vagy beágyazott JSON. Mivel Azure Cosmos DB séma nélküli, a paraméterek nem ellenőrizhetők semmilyen típuson.

Az alábbiakban példákat talál a paraméteres lekérdezésekre az egyes Azure Cosmos DB SDK-ban:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>Következő lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentumadatok modellezése](modeling-data.md)
