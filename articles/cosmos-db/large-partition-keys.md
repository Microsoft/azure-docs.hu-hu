---
title: Azure Cosmos-tárolók létrehozása nagyméretű partíciós kulccsal
description: Megtudhatja, hogyan hozhat létre tárolót Azure Cosmos DB nagyméretű partíciós kulccsal Azure Portal és különböző SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ad26d63ca06f5a46a4a1f77d329d04896e96c52
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339295"
---
# <a name="create-containers-with-large-partition-key"></a>Nagyméretű partíciós kulccsal rendelkező tárolók létrehozása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB kivonat-alapú particionálási sémát használ az adathorizontális skálázás eléréséhez. Az 3 2019. május előtt létrehozott összes Azure Cosmos-tároló kivonatoló függvényt használ, amely a partíciós kulcs első 100 bájtja alapján számítja ki a kivonatot. Ha az első 100 bájtnál több partíciós kulcs is van, akkor ezek a logikai partíciók a szolgáltatással azonos logikai partíciónak tekintendők. Ez olyan problémákhoz vezethet, mint például a partíciók méretére vonatkozó kvóta helytelen, és a partíciós kulcsok között egyedi indexeket kell alkalmazni. A probléma megoldásához nagy méretű partíciós kulcsok vannak bevezetve. A Azure Cosmos DB mostantól legfeljebb 2 KB-os értékkel támogatja a nagyméretű partíciós kulcsokat.

A nagyméretű partíciós kulcsokat a kivonatoló függvény továbbfejlesztett verziójának funkciói támogatják, amelyek egyedi kivonatot hozhatnak létre a nagyméretű partíciós kulcsokból akár 2 KB-ra. Ezt a kivonatoló verziót ajánlott olyan forgatókönyvek esetén is használni, amelyek a partíciós kulcs méretétől függetlenül nagy méretűek. A particionálási kulcs számos egyedi logikai partícióként van definiálva, például egy tárolóban lévő ~ 30000 logikai partíciók sorrendjében. Ez a cikk azt ismerteti, hogyan hozhat létre nagyméretű partíciós kulccsal rendelkező tárolót a Azure Portal és a különböző SDK-k használatával.

## <a name="create-a-large-partition-key-azure-portal"></a>Nagy partíciós kulcs létrehozása (Azure Portal)

Ha nagy méretű partíciót szeretne létrehozni, amikor a Azure Portal használatával hoz létre egy új tárolót, győződjön meg arról, hogy a **saját partíciós kulcs nagyobb, mint 100 bájtos** beállítás. Törölje a jelölőnégyzet jelölését, ha nincs szüksége nagyméretű partíciós kulcsokra, vagy ha olyan alkalmazásokkal rendelkezik, amelyek 1,18-nál régebbi SDK-verzión futnak.

:::image type="content" source="./media/large-partition-keys/large-partition-key-with-portal.png" alt-text="Nagyméretű partíciós kulcsok létrehozása Azure Portal használatával":::

## <a name="create-a-large-partition-key-powershell"></a>Nagy partíciós kulcs létrehozása (PowerShell)

Nagyméretű partíciós kulcsot támogató tároló létrehozásához lásd:

* [Azure Cosmos-tároló létrehozása nagyméretű partíciós kulcs méretével](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Nagyméretű partíciós kulcs (.net SDK) létrehozása

Ha nagyméretű partíciós kulccsal rendelkező tárolót szeretne létrehozni a .NET SDK használatával, akkor a tulajdonságot kell megadnia `PartitionKeyDefinitionVersion.V2` . Az alábbi példa bemutatja, hogyan adhatja meg a Version tulajdonságot a PartitionKeyDefinition objektumon belül, és beállíthatja a PartitionKeyDefinitionVersion. v2 értékre.

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```
---

## <a name="supported-sdk-versions"></a>Támogatott SDK-verziók

A nagyméretű partíciós kulcsokat a következő SDK-k minimális verziói támogatják:

|SDK típusa  | Minimális verzió   |
|---------|---------|
|.Net     |    1,18     |
|Java-szinkronizálás     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | `2017-05-03`a kérelem fejlécének használatával magasabb a verziószáma `x-ms-version` .|
| Resource Manager-sablon | 2. verzió az `"version":2` objektumon belüli tulajdonság használatával `partitionKey` . |

Jelenleg nem használhat nagyméretű partíciós kulccsal rendelkező tárolókat Power BI és Azure Logic Apps. Ezekből az alkalmazásokból nagyméretű partíciós kulcs nélkül is használhat tárolókat.

## <a name="next-steps"></a>Következő lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](./account-databases-containers-items.md)