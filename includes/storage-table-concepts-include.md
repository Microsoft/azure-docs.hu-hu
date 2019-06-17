---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 042aedf1a043cd89d74ff099554642d38a3c7dd3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66120649"
---
## <a name="what-is-table-storage"></a>A Table Storage ismertetése
Az Azure Table Storage nagy mennyiségű strukturált adat tárolására alkalmas. A szolgáltatás egy NoSQL-adattár, amely az Azure-felhőből és azon kívülről érkező hitelesített hívásokat fogadja. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak. A Table Storage gyakori használati módjai:

* Több TB-nyi, webes méretű alkalmazások kiszolgálására alkalmas strukturált adat tárolása
* Olyan adatkészletek tárolása, amelyekhez nincs szükség bonyolult illesztésekre, külső kulcsokra vagy tárolt eljárásokra, és a gyors hozzáférés érdekében denormalizálhatók
* Adatok gyors lekérdezése fürtözött indexszel
* Adathozzáférés az OData protokoll és a LINQ-lekérdezések WCF Data Service .NET-kódtárakkal való használatával

A Table Storage strukturált, nem relációs adatok hatalmas készleteinek tárolására és lekérdezésére alkalmas, a táblák pedig az igények növekedésével együtt nőnek.

## <a name="table-storage-concepts"></a>A Table Storage szolgáltatással kapcsolatos alapfogalmak
A Table Storage a következő összetevőkből áll:

![Table Storage-összetevők ábrája][Table1]

* **URL-formátum:** Azure Table Storage-fiókok ezt a formátumot használja: `http://<storage account>.table.core.windows.net/<table>`

  Az Azure Cosmos DB Table API-fiókok formátuma a következő: `http://<storage account>.table.cosmosdb.azure.com/<table>`  

  Az Azure-táblákat közvetlenül is elérheti, ha a fenti címet használja az OData protokollal. További információk az [OData.org][OData.org] webhelyen találhatók.
* **Fiókok:** Minden, az Azure Storage-hozzáférés tárfiókon keresztül történik. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Azure Storage Scalability and Performance Targets](../articles/storage/common/storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai). 

    Minden Azure Cosmos DB-hozzáférés Table API-fiókon keresztül valósítható meg. A Table API-fiókok létrehozásával kapcsolatos információkért lásd a [Table API-fiók létrehozását](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account) ismertető témakört.
* **tábla**: Egy tábla az entitások gyűjteményét. A táblák nem kényszerítenek sémát az entitásokra, ami azt jelenti, hogy egyetlen tábla különböző tulajdonságkészletekkel rendelkező entitásokat is tartalmazhat.  
* **Entitás**: Egy entitás egy sor tulajdonságból, hasonlóan egy adatbázis sor. Egy Azure Storage-entitás legfeljebb 1 MB méretű lehet. Egy Azure Cosmos DB-entitás legfeljebb 2 MB méretű lehet.
* **Tulajdonságok**: A tulajdonság egy név-érték pár. Minden entitás legfeljebb 252 tulajdonságot tartalmazhat adattárolás céljából. Minden entitás három rendszertulajdonsággal rendelkezik, amelyek egy partíciókulcsot, egy sorkulcsot és egy időbélyegzőt adnak meg. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, illetve szűrhatók be/frissíthetők atomi műveletek során. Egy entitás sorkulcsa a partíción belüli azonosítója.

A táblák és tulajdonságok elnevezésével kapcsolatos további információkért lásd: [Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (A Table szolgáltatás adatmodelljének ismertetése).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
