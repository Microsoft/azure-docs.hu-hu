---
title: Bevezetés a Azure Cosmos DB Table API
description: Az Azure Tables API használatával megtudhatja, hogyan tárolhatja és kérdezheti le a nagy mennyiségű kulcs-érték típusú, alacsony késésű, a Azure Cosmos DB használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 07/26/2019
ms.author: sngun
ms.openlocfilehash: 9f44279e47de737bd23fcd0266b7fb73e6245b13
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096053"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Alapvető ismeretek az Azure Cosmos DB tábla API szolgáltatásáról
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Az [Azure Cosmos DB](introduction.md) Table API-t biztosít olyan, az Azure Table Storage-hoz írt alkalmazások számára, amelyekhez a következő prémium képességek szükségesek:

* [Kulcsrakész globális terjesztés](distribute-data-globally.md).
* [Dedikált teljesítmény](partitioning-overview.md) világszerte.
* Az esetek 99%-ában egyszámjegyű ezredmásodperces késés.
* Garantáltan magas szintű rendelkezésre állás.
* Automatikus másodlagos indexelés.

Ezek az Azure Table Storage számára írt alkalmazások kódmódosítás nélkül migrálhatók az Azure Cosmos DB-be a Table API-val, és ki tudják használni a prémium képességek előnyeit. A Table API a .NET, a Java, a Python és a Node.js környezetekhez is rendelkezik ügyfél-SDK-val.

> [!IMPORTANT]
> A .NET-keretrendszer SDK [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) karbantartási módban van, és hamarosan elavulttá válik. Frissítsen a [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) új .NET Standard Library webhelyre, és folytassa a Table API által támogatott legújabb funkciók beszerzésével.

## <a name="table-offerings"></a>Table ajánlatok
Ha jelenleg az Azure Table Storage szolgáltatást használja, az alábbi előnyökben részesülhet az Azure Cosmos DB Table API-ra való áttéréskor:

| Funkció | Azure Table Storage | Azure Cosmos DB Table API |
| --- | --- | --- |
| Késés | Gyors, de nincs felső korlátja a késésnek. | Az olvasások és írások egyszámjegyű ezredmásodperces késése <10 ms-os késéssel, az olvasási és írási műveletek esetében a esetek 99% percentilis esetében, bármilyen méretben, bárhol a világon. |
| Átviteli sebesség | Változó teljesítménymodell. A táblák skálázhatósági korlátja másodpercenként 20 000 művelet. | Hatékonyan skálázható a [táblánként dedikált és fenntartott átviteli sebességgel](request-units.md), amelynek rendelkezésre állását SLA-k szavatolják. A fiókokban nincs korlátozva az átviteli sebesség felső határa, és a szolgáltatás táblánként és másodpercenként legalább 10 millió műveletet támogat. |
| Globális terjesztés | Egyetlen régió egyetlen választható olvasható, másodlagos olvasási régióval a magas szintű rendelkezésre állásért. | [Kulcsrakész globális eloszlás](distribute-data-globally.md) egy vagy több régióból. Támogatja az [automatikus és manuális feladatátvételt](high-availability.md) bármikor és bárhol a világon. Több írási régió lehetővé teszi, hogy bármely régió fogadja az írási műveleteket. |
| Indexelés | Csak elsődleges indexelés a PartitionKey és a RowKey tulajdonságok esetén. Nincsenek másodlagos indexek. | Az összes tulajdonság automatikus és teljes indexelése alapértelmezés szerint, indexek kezelése nélkül. |
| Lekérdezés | A lekérdezés végrehajtásakor az elsődleges kulcshoz tartozó indexet használja, és egyéb esetben csak vizsgálati műveletet végez. | A lekérdezések a gyorsaság céljából kihasználhatják a tulajdonságok automatikus indexelését. |
| Konzisztencia | Erős az elsődleges régióban, végleges a másodlagos régióban. | [Öt jól meghatározott konzisztencia-szint](consistency-levels.md) a rendelkezésre állás, a késés, az átviteli sebesség és a konzisztencia kikapcsolásához az alkalmazás igényei alapján. |
| Díjszabás | Tárolásra optimalizált. | Átviteli sebességre optimalizált. |
| SLA-k | 99,9% – 99,99% rendelkezésre állás a replikációs stratégiától függően. | 99,999%-os olvasási rendelkezésre állás, 99,99% írási rendelkezésre állás egy egyrégiós fiókon és 99,999% írási rendelkezésre állás a többrégiós fiókokon. A rendelkezésre állást, a késést, az átviteli sebességet és a konzisztenciát [átfogó SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) . |

## <a name="get-started"></a>Bevezetés

Hozzon létre egy Azure Cosmos DB-fiókot az [Azure Portalon](https://portal.azure.com). Ezután első lépésként hajtsa végre a [Table API .NET-keretrendszerrel való használatának rövid útmutatóját](create-table-dotnet.md). 

> [!IMPORTANT]
> Ha az előzetes verzióban hozta létre a Table API-fiókot, hozzon létre egy [új Table API-fiókot](create-table-dotnet.md#create-a-database-account), amely használható az általánosan elérhető Table API SDK-kkal.
>

## <a name="next-steps"></a>Következő lépések

Íme, pár hivatkozás az első lépések megtételéhez:
* [.NET-alkalmazás létrehozása a Table API-val](create-table-dotnet.md)
* [Fejlesztés a Table API-val .NET-keretrendszerben](tutorial-develop-table-dotnet.md)
* [Táblaadatok lekérdezése a Table API-val](tutorial-query-table.md)
* [Tudnivalók az Azure Cosmos DB globális terjesztésének beállításáról a Table API-val](tutorial-global-distribution-table.md)
* [Azure Cosmos DB table .NET Standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB table .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB Table Java SDK](table-sdk-java.md)
* [Azure Cosmos DB Table Node.js SDK](table-sdk-nodejs.md)
* [Azure Cosmos DB Table SDK Pythonhoz](table-sdk-python.md)
