---
title: Helytelen kérelmekkel Azure Cosmos DB-kivételek hibáinak megoldása
description: Megtudhatja, hogyan diagnosztizálhatja és javíthatja a hibás kérelmek kivételeit, például a bemeneti tartalmat vagy a partíciós kulcsot, a partíciós kulcs nem egyezik Azure Cosmos DBban.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e0ce00331eb524d064fd6e7c5205be8b66d4dbc2
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556124"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>A Azure Cosmos DB helytelen kérelmekkel kapcsolatban felmerülő kivételek diagnosztizálása és megoldása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A 400-es HTTP-állapotkód azt jelenti, hogy a kérelem érvénytelen adatértéket tartalmaz, vagy hiányzó kötelező paraméterek.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>Hiányzik az ID tulajdonság
Ebben az esetben gyakori, hogy a következő hibaüzenetet látja:

*A bemeneti tartalom érvénytelen, mert a szükséges tulajdonságok-azonosító; – hiányoznak*

A hibával kapcsolatos válasz azt jelenti, hogy a szolgáltatásnak elküldött JSON-dokumentum nem rendelkezik a szükséges azonosító tulajdonsággal.

### <a name="solution"></a>Megoldás
Adja meg a `id` dokumentum részeként a [Rest-specifikációnak](/rest/api/cosmos-db/documents) megfelelő tulajdonságot karakterlánc értékkel, az SDK-k nem hoznak be automatikusan értéket ehhez a tulajdonsághoz.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>A partíciós kulcs típusa érvénytelen
Ebben a forgatókönyvben gyakori, hogy az alábbi hibákat látja:

*Partíciós kulcs... érvénytelen.*

A hibával kapcsolatos válasz azt jelenti, hogy a partíciós kulcs értéke érvénytelen típusú.

### <a name="solution"></a>Megoldás
A partíciós kulcs értékének sztringnek vagy számnak kell lennie, ügyeljen arra, hogy az érték a várt típusú legyen.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>A partíciós kulcs értéke helytelen
Ebben az esetben gyakori, hogy a következő hibaüzenetet látja:

*A dokumentumból kinyert PartitionKey nem felel meg a fejlécben megadott számnak*

Az ezzel a hibával kapcsolatos válasz azt jelenti, hogy egy műveletet hajt végre, és olyan partíciós kulcs értékét továbbítja, amely nem egyezik a dokumentum szövegtörzsének értékével a várt tulajdonság esetében. Ha a gyűjtemény partíciós kulcsának elérési útja `/myPartitionKey` , a dokumentum egy olyan `myPartitionKey` értékkel rendelkezik, amely nem egyezik meg az SDK-metódus meghívásakor a partíciós kulcs értékeként megadott értékkel.

### <a name="solution"></a>Megoldás
A dokumentum-tulajdonság értékének megfelelő Partition Key Value paraméter elküldése.

## <a name="next-steps"></a>Következő lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md) .
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md)teljesítményére vonatkozó irányelvek ismertetése.
* A Azure Cosmos DB Java v4 SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-java-sdk-v4-sql.md) .
* A [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)teljesítményére vonatkozó irányelvek ismertetése.
