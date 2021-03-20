---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 91a52e7eac40c0ac2ab682f251a2ae0013259b25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77013947"
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

* **URL-formátum:** Az Azure Table Storage-fiókok a következő formátumot használják: `http://<storage account>.table.core.windows.net/<table>`

  Az Azure Cosmos DB Table API-fiókok formátuma a következő: `http://<storage account>.table.cosmosdb.azure.com/<table>`  

  Az Azure-táblákat közvetlenül is elérheti, ha a fenti címet használja az OData protokollal. További információ: [OData.org][OData.org].
* **Fiókok:** Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. A Storage-fiókokkal kapcsolatos további információkért lásd: a [Storage-fiók áttekintése](../articles/storage/common/storage-account-overview.md).

    Minden Azure Cosmos DB-hozzáférés Table API-fiókon keresztül valósítható meg. A Table API-fiókok létrehozásával kapcsolatos információkért lásd a [Table API-fiók létrehozását](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account) ismertető témakört.
* **Tábla:** A tábla az entitások gyűjteményét tartalmazza. A táblák nem kényszerítenek sémát az entitásokra, ami azt jelenti, hogy egyetlen tábla különböző tulajdonságkészletekkel rendelkező entitásokat is tartalmazhat.  
* **Entitás:** Az entitás egy adatbázissorhoz hasonló tulajdonságkészlet. Egy Azure Storage-entitás legfeljebb 1 MB méretű lehet. Egy Azure Cosmos DB-entitás legfeljebb 2 MB méretű lehet.
* **Tulajdonságok:** A tulajdonság egy név-érték pár. Minden entitás legfeljebb 252 tulajdonságot tartalmazhat adattárolás céljából. Minden entitás három rendszertulajdonsággal rendelkezik, amelyek egy partíciókulcsot, egy sorkulcsot és egy időbélyegzőt adnak meg. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, illetve szűrhatók be/frissíthetők atomi műveletek során. Egy entitás sorkulcsa a partíción belüli azonosítója.

A táblák és tulajdonságok elnevezésével kapcsolatos további információkért lásd: [Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (A Table szolgáltatás adatmodelljének ismertetése).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
