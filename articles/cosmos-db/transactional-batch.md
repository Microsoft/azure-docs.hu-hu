---
title: Tranzakciós batch-műveletek Azure Cosmos DB a .NET SDK használatával
description: Megtudhatja, hogyan használhatja a TransactionalBatch-t a Azure Cosmos DB .NET SDK-ban, hogy a sikeres vagy sikertelen műveletek csoportjait hajtsa végre.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9f6692db2da3722507136a468d1dcbdc2985e73f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347557"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Tranzakciós batch-műveletek Azure Cosmos DB a .NET SDK használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A tranzakciós köteg olyan pont típusú műveletek egy csoportját írja le, amelyeknek egy tárolóban ugyanazzal a partíciós kulccsal kell rendelkezniük. A .NET SDK-ban az `TransactionalBatch` osztály a műveletek kötegének meghatározására szolgál. Ha az összes művelet a tranzakciós köteg műveletben ismertetett sorrendben sikeres, a tranzakció véglegesítve lesz. Ha azonban valamelyik művelet meghiúsul, a teljes tranzakció vissza lesz állítva.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Mi a tranzakció a Azure Cosmos DBban?

Egy tipikus adatbázisban lévő tranzakció meghatározható egyetlen logikai egységként végrehajtott műveletek sorozatából. Minden tranzakció a sav (atomi, konzisztencia, elkülönítés, tartósság) tulajdonságának garanciáit biztosítja.

* Az **atomenergia** garantálja, hogy a tranzakción belül végrehajtott összes művelet egyetlen egységként lesz kezelve, és ezek mindegyike véglegesítve van, vagy egyik sem.
* A **konzisztencia** gondoskodik arról, hogy az adategységek mindig érvényes állapotban legyenek a tranzakciók között.
* Az **elkülönítés** garantálja, hogy két tranzakció sem zavarja egymást – számos kereskedelmi rendszer több elkülönítési szintet is biztosít, amelyeket az alkalmazás igényei alapján használhat.
* A **tartósság** biztosítja, hogy az adatbázisban véglegesített módosítások mindig jelen lesznek.
A Azure Cosmos DB támogatja a [teljes sav-kompatibilis tranzakciókat, amelyek pillanatkép-elkülönítéssel rendelkeznek](database-transactions-optimistic-concurrency.md) ugyanazon [logikai partíciós kulcson](partitioning-overview.md)belüli műveletekhez.

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Tranzakciós batch-műveletek és tárolt eljárások

Azure Cosmos DB jelenleg a tárolt eljárásokat támogatja, amelyek a tranzakciós hatókört is biztosítják a műveletekhez. A tranzakciós kötegelt műveletek azonban a következő előnyöket nyújtják:

* **Nyelvi beállítás** – a tranzakciós köteg támogatott az SDK-ban és a nyelvben, amelyet már használ, míg a tárolt eljárásokat JavaScript nyelven kell megírni.
* A **kód verziószámozása** – az alkalmazás kódjának és a CI/CD-folyamatba való bevezetésének sokkal természetesebb, mint egy tárolt eljárás frissítése, és így a megfelelő időben történik a váltás. Emellett egyszerűbbé teszi a működés közbeni változások visszaállítását.
* **Teljesítmény** – a tárolt eljárás végrehajtásához képest akár 30%-kal csökkenthető az egyenértékű műveletek késése.
* **Tartalom szerializálása** – a tranzakciós kötegen belüli minden művelet kihasználhatja a hasznos adatokhoz tartozó egyéni szerializálási beállításokat.

## <a name="how-to-create-a-transactional-batch-operation"></a>Tranzakciós batch-művelet létrehozása

Tranzakciós batch-művelet létrehozásakor egy tároló-példányból kell kezdődnie, és hívnia kell a következőt `CreateTransactionalBatch` :

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

Ezután meg kell hívnia a `ExecuteAsync` köteget:

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

A válasz fogadása után vizsgálja meg, hogy sikeres-e vagy sem, és bontsa ki az eredményeket:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Ha hiba lép fel, a meghiúsult művelet a megfelelő hibához tartozó állapotkódot fogja tartalmazni. Az összes többi művelethez tartozik egy 424 állapotkód (sikertelen függőség). Az alábbi példában a művelet meghiúsul, mert kísérletet tesz egy már létező elem létrehozására (409 HttpStatusCode. Conflict). Az állapotkód lehetővé teszi, hogy az egyik azonosítsa a tranzakciós hiba okát.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Hogyan történik a tranzakciós batch-műveletek végrehajtása

A `ExecuteAsync` metódus meghívásakor a rendszer az objektum összes műveletét `TransactionalBatch` csoportosítja, egyetlen hasznos adatba szerializálja, és egyetlen kérelemként küldi el a Azure Cosmos db szolgáltatásnak.

A szolgáltatás megkapja a kérést, és végrehajtja a tranzakciós hatókörön belüli összes műveletet, és egy választ ad vissza ugyanazzal a szerializálási protokollal. Ez a válasz sikeres vagy sikertelen, és az egyes műveletek válaszait egy művelettel biztosítja.

Az SDK kijelöli a választ az eredmény ellenőrzéséhez, és szükség esetén kinyeri az összes belső művelet eredményét.

## <a name="limitations"></a>Korlátozások

Jelenleg két ismert korlát létezik:

* A Azure Cosmos DB kérések méretének korlátja korlátozza a `TransactionalBatch` hasznos adatok méretét, hogy ne lépje túl a 2 MB-ot, a maximális végrehajtási idő pedig 5 másodperc.
* A jelenlegi korlátja 100 művelet `TransactionalBatch` , amely biztosítja, hogy a teljesítmény a vártnál és a SLA-n belül legyen.

## <a name="next-steps"></a>Következő lépések

* További információ a TransactionalBatch-mel kapcsolatos [lehetőségekről](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)

* A Cosmos DB .NET SDK használatának további módjairól a [mintákban](sql-api-dotnet-v3sdk-samples.md) talál további információt
