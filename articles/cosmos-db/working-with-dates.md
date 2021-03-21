---
title: Dátumok használata Azure Cosmos DB
description: Útmutató dátumból/időből-objektumok tárolásához, indexeléséhez és lekérdezéséhez Azure Cosmos DB
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4d4e63bdd1e795bc1c599d0eae8a595aa0d643
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359523"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Dátumok használata Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB a séma rugalmasságát és a gazdag indexelést egy natív [JSON](https://www.json.org) -adatmodellen keresztül biztosítja. Az összes Azure Cosmos DB erőforrás, beleértve az adatbázisokat, a tárolókat, a dokumentumokat és a tárolt eljárásokat JSON-dokumentumként modellezik és tárolja. A hordozható, JSON-(és Azure Cosmos DB-) követelmények csak az alaptípusok kis készletét támogatják: karakterlánc, szám, logikai, tömb, objektum és null. A JSON azonban rugalmas, és lehetővé teszi a fejlesztők és keretrendszerek számára, hogy összetettebb típusokat képviselnek ezen primitívek használatával, és objektumokként vagy tömbökként is megkomponálják őket.

Az alaptípusokon kívül számos alkalmazásnak szüksége van a DateTime típusra a dátumok és időbélyegek megjelenítéséhez. Ez a cikk azt ismerteti, hogyan tárolhatók a fejlesztők a Azure Cosmos DB a .NET SDK használatával a dátumok tárolásához, lekéréséhez és lekérdezéséhez.

## <a name="storing-datetimes"></a>Dátum és idő tárolása

Azure Cosmos DB támogatja a JSON-típusokat, például a-string, a number, a Boolean, a null, a Array, az Object. Nem támogatja közvetlenül a DateTime típust. A Azure Cosmos DB jelenleg nem támogatja a dátumok honosítását. Ezért a DateTimes karakterláncként kell tárolnia. A DateTime karakterláncok ajánlott formátuma Azure Cosmos DB, `yyyy-MM-ddTHH:mm:ss.fffffffZ` amely az ISO 8601 UTC szabványt követi. Azt javasoljuk, hogy a Azure Cosmos DB összes dátumát UTC-ként tárolja. A dátum sztringek erre a formátumra való konvertálása lehetővé teszi a rendezési dátumok lexicographically. Ha a nem UTC formátumú dátumok vannak tárolva, a logikát az ügyféloldali helyen kell kezelni. Ha a helyi DateTime értéket UTC-re szeretné átalakítani, az eltolásnak ismertnek kell lennie, és a JSON tulajdonságként kell tárolnia, és az ügyfél az eltolás használatával kiszámíthatja az UTC DateTime értékét.

A DateTime sztringekkel rendelkező címtartomány-lekérdezések csak akkor támogatottak, ha a DateTime karakterláncok mind UTC, mind pedig azonos hosszúságú. Azure Cosmos DB a [GetCurrentDateTime](sql-query-getcurrentdatetime.md) System függvény az aktuális UTC dátum és idő ISO 8601 karakterlánc értékét fogja visszaadni a (z) formátumban: `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

A legtöbb alkalmazás a következő okok miatt használhatja a DateTime alapértelmezett karakterlánc-ábrázolását:

* A karakterláncok összehasonlíthatók, a DateTime értékek relatív sorrendje pedig megmarad, ha karakterlánccá alakítják át őket.
* Ehhez a megközelítéshez nem szükséges egyéni kód vagy attribútum a JSON-átalakításhoz.
* A JSON-ban tárolt dátumok emberi olvashatók.
* Ez a megközelítés kihasználhatja Azure Cosmos DB indexét a gyors lekérdezési teljesítmény érdekében.

Az alábbi kódrészlet például egy olyan objektumot tárol, `Order` amely két datetime tulajdonságot tartalmaz – `ShipDate` és a `OrderDate` .net SDK-t használó dokumentumként:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14),
            Total = 113.39
        });
```

Ezt a dokumentumot a Azure Cosmos DB a következőképpen tárolja:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Azt is megteheti, hogy a dátum/idő karakterláncot UNIX-időbélyegként tárolja, azaz az eltelt másodpercek számát jelölő számként, amely a 1970. január 1. A Azure Cosmos DB belső timestamp ( `_ts` ) tulajdonsága ezt a megközelítést követi. A [UnixDateTimeConverter](/dotnet/api/microsoft.azure.documents.unixdatetimeconverter) osztály használatával számként szerializálhatja a dátum és idő értéket.

## <a name="querying-datetimes-in-linq"></a>Dátum és idő lekérdezése a LINQ-ben

Az SQL .NET SDK automatikusan támogatja a Azure Cosmos DB a LINQ használatával tárolt adatlekérdezéseket. Az alábbi kódrészlet például egy LINQ-lekérdezést mutat be, amely az elmúlt három napban szállított rendeléseket szűri:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Lefordítva a következő SQL-utasításra, és a Azure Cosmos DBon hajtja végre:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

A Azure Cosmos DB SQL-lekérdezési nyelvét és a LINQ-szolgáltatót a [LINQ-Cosmos db lekérdezése](sql-query-linq-to-sql.md)című témakörben olvashatja.

## <a name="indexing-datetimes-for-range-queries"></a>Dátum-és időértékek indexelése a tartomány lekérdezéséhez

A lekérdezések gyakran DateTime értékekkel vannak meghatározva. A lekérdezések hatékony végrehajtásához a lekérdezés szűrője bármely tulajdonságában definiálni kell egy indexet.

További információ az indexelési házirendek konfigurálásáról [Azure Cosmos db indexelési házirendekben](index-policy.md). 

## <a name="next-steps"></a>Következő lépések

* A [kód mintáinak letöltése és futtatása a githubon](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples)
* További információ az [SQL-lekérdezésekről](sql-query-getting-started.md)
* További információ az [Azure Cosmos db indexelési házirendekről](index-policy.md)