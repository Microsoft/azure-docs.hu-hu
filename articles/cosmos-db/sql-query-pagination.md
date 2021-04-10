---
title: Tördelés Azure Cosmos DB
description: Tudnivalók a lapozással kapcsolatos fogalmakról és a folytatólagos tokenekről
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: d47a7b9e2229ffbd747b1ff7b9491ce99e9b190f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492234"
---
# <a name="pagination-in-azure-cosmos-db"></a>Tördelés Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB a lekérdezések több oldalnyi eredményt is tartalmazhatnak. Ez a dokumentum ismerteti a Azure Cosmos DB lekérdezési motorjának feltételeit, hogy eldöntse, hogy több oldalra kell-e bontani a lekérdezési eredményeket. A folytatási tokeneket igény szerint a több oldalra kiterjedő lekérdezési eredmények kezelésére is használhatja.

## <a name="understanding-query-executions"></a>A lekérdezés-végrehajtás ismertetése

Időnként a lekérdezési eredmények több oldalra lesznek felosztva. Az egyes lapok eredményeit külön lekérdezési végrehajtással hozza létre a rendszer. Ha a lekérdezési eredményeket egyetlen végrehajtással nem lehet visszaadni, Azure Cosmos DB automatikusan több oldalra osztja el az eredményeket.

A lekérdezés által visszaadott elemek maximális számát a következő beállításával adhatja meg: `MaxItemCount` . A a `MaxItemCount` kérelem szerint van megadva, és azt jelzi, hogy a lekérdezési motor az elemek számát vagy kevesebbet adja vissza. Beállíthatja `MaxItemCount` , hogy `-1` Ha nem szeretné korlátozni az eredmények számát a lekérdezés-végrehajtás során.

Emellett más okokból is előfordulhat, hogy a lekérdezési motornak több oldalra kell bontania a lekérdezési eredményeket. Ezek a következők:

- A tároló szabályozása megtörtént, és nem volt elérhető RUs további lekérdezési eredmények visszaadásához
- A lekérdezés-végrehajtás válasza túl nagy volt
- A lekérdezés végrehajtásának ideje túl hosszú volt.
- A lekérdezési motor hatékonyabban jeleníti meg az eredményeket további végrehajtások esetén

A lekérdezés végrehajtásakor visszaadott elemek száma mindig kisebb vagy egyenlő lesz `MaxItemCount` . Előfordulhat azonban, hogy más feltételek korlátozták a lekérdezés által visszaadott eredmények számát. Ha többször hajtja végre ugyanazt a lekérdezést, előfordulhat, hogy a lapok száma nem állandó. Ha például egy lekérdezés szabályozva van, előfordulhat, hogy a lapon kevesebb találat érhető el, ami azt jelenti, hogy a lekérdezés további lapokkal fog rendelkezni. Bizonyos esetekben előfordulhat, hogy a lekérdezés az eredmények üres oldalát is visszaadja.

## <a name="handling-multiple-pages-of-results"></a>Az eredmények több oldalának kezelésére

A lekérdezés pontos eredményeinek biztosítása érdekében az összes oldalt át kell haladnia. A lekérdezéseket továbbra is végre kell hajtania, amíg nincs további oldal.

Íme néhány példa a több oldallal rendelkező lekérdezések eredményeinek feldolgozására:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Folytatási tokenek

A .NET SDK-ban és a Java SDK-ban igény szerint a folytatási tokeneket is használhatja könyvjelzőként a lekérdezés előrehaladásához. Azure Cosmos DB lekérdezés-végrehajtás állapot nélküli a kiszolgáló oldalán, és bármikor folytatható a folytatási token használatával. A folytatási tokenek nem támogatottak a Node.js SDK-ban. A Python SDK esetében az egyetlen partíciós lekérdezések esetében támogatott, és a PK-t meg kell adni a beállítások objektumban, mert nem elegendő a lekérdezéshez.

Íme néhány példa a folytatási tokenek használatára:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/test/test_query.py#L533)

Ha a lekérdezés folytatási tokent ad vissza, akkor további lekérdezési eredmények is megtalálhatók.

Azure Cosmos DB REST API a folytatási tokeneket a `x-ms-continuation` fejléctel kezelheti. Mint a .NET vagy a Java SDK lekérdezésével, ha a `x-ms-continuation` Válasz fejléce nem üres, akkor a lekérdezés további eredményeket tartalmaz.

Ha ugyanazt az SDK-verziót használja, a folytatási tokenek soha nem járnak le. Lehetősége van a [folytatási token méretének korlátozására](/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb)is. Az adatmennyiségtől vagy a tároló fizikai partícióinak számától függetlenül a lekérdezések egyetlen folytatási tokent adnak vissza.

A folytatási tokenek nem használhatók a [Group By](sql-query-group-by.md) vagy [DISTINCT](sql-query-keywords.md#distinct) lekérdezésekhez, mert ezek a lekérdezések jelentős mennyiségű állapot tárolását igénylik. A alkalmazással végzett lekérdezésekhez használhatja `DISTINCT` a folytatási jogkivonatokat, ha hozzáadja `ORDER BY` a lekérdezéshez.

Az alábbi példa egy, a `DISTINCT` folytatási tokent használó lekérdezésre mutat:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY záradék](sql-query-order-by.md)
