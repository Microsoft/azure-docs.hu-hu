---
title: Nem található kivételek Azure Cosmos DB hibáinak megoldása
description: Ismerje meg, hogyan diagnosztizálhatja és javíthatja a nem található kivételeket.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7b112cc80984a761e780f134731476f9dff4f687
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525771"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Azure Cosmos DB nem található kivételek diagnosztizálása és megoldása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A 404-es HTTP-állapotkódot azt jelzi, hogy az erőforrás már nem létezik.

## <a name="expected-behavior"></a>Várt viselkedés
Számos érvényes helyzet áll fenn, amikor egy alkalmazás 404 kódot vár, és megfelelően kezeli a forgatókönyvet.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>A rendszer nem talált kivételt adott vissza egy létező vagy létező elemmel kapcsolatban
Az alábbi lehetséges okok miatt előfordulhat, hogy a 404-as állapotkódot kell visszaadnia, ha az adott elemmel létezik vagy létezik.

### <a name="the-read-session-is-not-available-for-the-input-session-token"></a>Az olvasási munkamenet nem érhető el a bemeneti munkamenet jogkivonatához

#### <a name="solution"></a>Megoldás:
1. Frissítse az aktuális SDK-t az elérhető legújabb verzióra. Az adott hiba leggyakoribb okai a legújabb SDK-verziókban lettek kijavítva.

### <a name="race-condition"></a>Versenyhelyzet
Több SDK-ügyfél példánya van, és az írás előtt történt az olvasás.

#### <a name="solution"></a>Megoldás:
1. Azure Cosmos DB esetén az alapértelmezett fiók konzisztenciája a munkamenet konzisztenciája. Egy elem létrehozásakor vagy frissítésekor a válasz egy munkamenet-tokent ad vissza, amely az SDK-példányok között átadható, így garantálható, hogy az olvasási kérelem egy adott módosítással rendelkező replikáról olvas.
1. Módosítsa a [konzisztencia szintjét](./consistency-levels.md) [erősebb szintre](./consistency-levels.md).

### <a name="invalid-partition-key-and-id-combination"></a>A partíciós kulcs és az azonosító kombinációja érvénytelen
A partíciós kulcs és az azonosító kombinációja nem érvényes.

#### <a name="solution"></a>Megoldás:
Javítsa ki a helytelen kombinációt okozó alkalmazás-logikát. 

### <a name="invalid-character-in-an-item-id"></a>Érvénytelen karakter szerepel egy adott AZONOSÍTÓban
A rendszer beszúr egy elemeket Azure Cosmos DBba, és az AZONOSÍTÓban [érvénytelen karakter](/dotnet/api/microsoft.azure.documents.resource.id?preserve-view=true&view=azure-dotnet#remarks) szerepel.

#### <a name="solution"></a>Megoldás:
Módosítsa az azonosítót egy másik értékre, amely nem tartalmaz speciális karaktereket. Ha az azonosító módosítása nem lehetséges, Base64-kódolással kódolhatja az azonosítót, hogy elkerülje a speciális karaktereket. A Base64 továbbra is létrehozhatja a "/" karaktert tartalmazó nevet, amelyet cserélni kell.

A tárolóban az AZONOSÍTÓhoz már beszúrt elemek a név-alapú hivatkozások helyett a RID-értékek használatával cserélhetők le.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet&preserve-view=true#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes).Replace('/', '!');

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Élő kiürítés ideje
Az objektum élettartama [(TTL)](./time-to-live.md) tulajdonsága be lett állítva. Az objektum törölve lett, mert a TTL tulajdonság lejárt.

#### <a name="solution"></a>Megoldás:
Módosítsa az élettartam tulajdonságot, hogy megakadályozza az elem kiürítését.

### <a name="lazy-indexing"></a>Lusta indexelés
Nem történt meg a [lusta indexelés](index-policy.md#indexing-mode) .

#### <a name="solution"></a>Megoldás:
Várjon, amíg az indexelés felveszi vagy megváltoztatja az indexelési házirendet.

### <a name="parent-resource-deleted"></a>Szülő erőforrás törölve
Az az adatbázis vagy tároló, amelyben az adott eleme található, törölve lett.

#### <a name="solution"></a>Megoldás:
1. [Állítsa vissza](./configure-periodic-backup-restore.md#request-restore) a szülő erőforrást, vagy hozza létre újra az erőforrásokat.
1. Hozzon létre egy új erőforrást a törölt erőforrás cseréjéhez.

### <a name="7-containercollection-names-are-case-sensitive"></a>7. a tárolók/gyűjtemények nevei megkülönböztetik a kis-és nagybetűket
A tárolók/gyűjtemények nevei megkülönböztetik a kis-és nagybetűket a Cosmos DBban.

#### <a name="solution"></a>Megoldás:
Ügyeljen arra, hogy a pontos nevet használja a Cosmos DBhoz való csatlakozáskor.

## <a name="next-steps"></a>Következő lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md) .
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md)teljesítményére vonatkozó irányelvek ismertetése.
* A Azure Cosmos DB Java v4 SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-java-sdk-v4-sql.md) .
* A [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)teljesítményére vonatkozó irányelvek ismertetése.
