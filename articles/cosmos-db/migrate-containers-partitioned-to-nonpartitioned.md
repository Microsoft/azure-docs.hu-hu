---
title: Nem particionált Azure Cosmos-tárolók áttelepíthetők particionált tárolók számára
description: Megtudhatja, hogyan telepítheti át az összes meglévő nem particionált tárolót particionált tárolóba.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/25/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: edb6114406922d55c439ae7426a2be933bba4aee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334089"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Nem particionált tárolók áttelepíthetők particionált tárolók számára
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB támogatja a tárolók partíciós kulcs nélküli létrehozását. Jelenleg nem particionált tárolókat hozhat létre az Azure CLI és a Azure Cosmos DB SDK-k (.net, Java, NodeJs) használatával, amelyek verziója kisebb vagy egyenlő, mint 2. x. Nem particionált tárolók nem hozhatók létre a Azure Portal használatával. Azonban az ilyen nem particionált tárolók nem rugalmasak, és a rögzített tárolókapacitás 20 GB és a maximális átviteli sebesség (10K RU/s).

A nem particionált tárolók elavultak, ezért migrálja meglévő nem particionált tárolóit particionált tárolókba a tárterület és az adatátviteli sebesség skálázása érdekében. Az Azure Cosmos DB tartalmaz egy, a rendszer által meghatározott mechanizmust, amellyel a nem particionált tárolók particionált tárolóba migrálhatók. Ez a dokumentum a meglévő, nem particionált tárolók particionált tárolókba való automatikus migrálásának menetét ismerteti. Az automatikus migrálási funkció csak akkor használható, ha az összes nyelven V3 verziójú SDK-kat használ.

> [!NOTE]
> Jelenleg nem telepíthet át Azure Cosmos DB MongoDB és Gremlin API-fiókokat a jelen dokumentumban ismertetett lépések segítségével.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Tároló átmigrálása a rendszer által definiált partíciós kulccsal

Az áttelepítés támogatásához a Azure Cosmos DB egy nevű rendszerpartíció-kulcsot biztosít az `/_partitionkey` összes olyan tárolón, amely nem rendelkezik partíciós kulccsal. A partíciós kulcs definíciója a tárolók migrálása után nem módosítható. Egy particionált tárolóba áttelepített tároló definíciója például a következő lesz:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

A tároló migrálása után létrehozhat dokumentumokat úgy, hogy kitölti a `_partitionKey` tulajdonságot a dokumentum többi tulajdonságával együtt. A `_partitionKey` tulajdonság a dokumentumok partíciós kulcsát jelöli.

A megfelelő partíciós kulcs kiválasztása fontos a kiépített átviteli sebesség optimális kihasználásához. További információ: [How to Choo an Partition Key](partitioning-overview.md) article.

> [!NOTE]
> Csak akkor használhatja a rendszerszintű partíciós kulcs előnyeit, ha az SDK-k legújabb vagy v3-as verzióját használja az összes nyelven.

Az alábbi példában egy mintakód látható egy olyan dokumentum létrehozásához, amely a rendszer által definiált partíciós kulccsal rendelkezik, és elolvasta a következő dokumentumot:

**A dokumentum JSON-ábrázolása**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

A teljes minta esetében tekintse meg a [.net-minták][1] GitHub-tárházát.
                      
## <a name="migrate-the-documents"></a>A dokumentumok migrálása

Amíg a tároló definíciója ki van bővítve egy Partition Key tulajdonsággal, a tárolóban található dokumentumok nem lesznek automatikusan áttelepítve. Ez azt jelenti, hogy a rendszerpartíciós kulcs tulajdonságának `/_partitionKey` elérési útja nincs automatikusan hozzáadva a meglévő dokumentumokhoz. A meglévő dokumentumokat úgy kell újraparticionálni, hogy beolvassa azokat a dokumentumokat, amelyeket nem partíciós kulcs nélkül hoztak létre, és visszaírják őket `_partitionKey` a dokumentumokban szereplő tulajdonsággal.

## <a name="access-documents-that-dont-have-a-partition-key"></a>A partíciós kulccsal nem rendelkező dokumentumok elérése

Az alkalmazások a "PartitionKey. None" nevű speciális rendszertulajdonságot használva érhetik el a meglévő dokumentumokat, amelyek nem rendelkeznek partíciós kulccsal, ez a nem áttelepített dokumentumok értéke. Ezt a tulajdonságot használhatja az összes szifilisz-és lekérdezési műveletben. Az alábbi példa egy olyan mintát mutat be, amely egyetlen dokumentumot olvas be a NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

A dokumentumok újraparticionálásával kapcsolatos teljes minta a [.net-minták][1] GitHub-tárházában található. 

## <a name="compatibility-with-sdks"></a>Az SDK-k kompatibilitása

Azure Cosmos DB SDK-k (például v2. x. x és v1. x. x) régebbi verziója nem támogatja a rendszer által definiált partíciós kulcs tulajdonságot. Tehát ha egy régebbi SDK-ból olvassa be a tároló definícióját, nem tartalmaz partíciós kulcs definícióját, és ezek a tárolók ugyanúgy működnek, mint korábban. Az SDK-k régebbi verziójával létrehozott alkalmazások továbbra is a nem particionált módon működnek, mivel nem módosulnak. 

Ha egy áttelepített tárolót az SDK legújabb/v3 verziója használ, és megkezdi a rendszer által definiált partíciós kulcs feltöltését az új dokumentumokon belül, akkor a régebbi SDK-k által már nem férhet hozzá (olvasási, frissítési, törlési, lekérdezési) ilyen dokumentumokat.

## <a name="known-issues"></a>Ismert problémák

**A v3 SDK használatával a partíciós kulcs nélkül beszúrt elemek száma nagyobb átviteli sebességgel járhat**

Ha a v2 SDK-val beszúrt elemek esetében a v3 SDK-val kérdez le, vagy a v3 SDK-val paraméterrel beszúrt elemeket `PartitionKey.None` , a Count lekérdezés több ru/s-t is felhasználhat, ha a `PartitionKey.None` paraméter a FeedOptions van megadva. Azt javasoljuk, hogy ne adja meg a `PartitionKey.None` paramétert, ha más elemeket nem szúrnak be partíciós kulccsal.

Ha az új elemek a partíciós kulcs különböző értékeivel vannak beszúrva, az ilyen elemek számának lekérdezése a megfelelő kulcs beadásával `FeedOptions` nem fog problémát okozni. Miután új dokumentumokat szúrt be a partíciós kulccsal, ha csak a dokumentumok darabszámát kell lekérdezni a partíciós kulcs értéke nélkül, a lekérdezés ismét magasabb RU/s-t eredményezhet a normál particionált gyűjteményekhez hasonló módon.

## <a name="next-steps"></a>Következő lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](./account-databases-containers-items.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration