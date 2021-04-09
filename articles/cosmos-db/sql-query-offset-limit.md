---
title: ELTOLÁSi korlát záradéka Azure Cosmos DB
description: Megtudhatja, hogyan használhatja az ELTOLÁSi korlát záradékot, hogy kiugorjon bizonyos értékeket a lekérdezésekben Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 459bd8511577067766cf488f53df57c1dc33fad1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338294"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>ELTOLÁSi korlát záradéka Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az ELTOLÁSi korlát záradék egy opcionális záradék, amely a lekérdezésből néhány értéket igénybe vehet. Az eltolási korlátot és a HATÁRÉRTÉKek számát kötelező megadni az ofszet LIMIT záradékban.

Ha az ELTOLÁSi KORLÁTot ORDER BY záradékkal együtt használja, az eredményhalmaz a kihagyás és a megrendelt értékek megtételével jön létre. Ha nem használ ORDER BY záradékot, a rendszer determinisztikus sorrendet eredményez.

## <a name="syntax"></a>Szintaxis
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentumok

- `<offset_amount>`

   Meghatározza, hogy hány elemből kell kihagyni a lekérdezés eredményeit.

- `<limit_amount>`
  
   Meghatározza, hogy hány elemnek kell szerepelnie a lekérdezés eredményei között.

## <a name="remarks"></a>Megjegyzések
  
  A `OFFSET` számnak és a `LIMIT` számnak is `OFFSET LIMIT` szerepelnie kell a záradékban. Ha nem kötelező `ORDER BY` záradékot használ, az eredményhalmaz a megrendelt értékek kihagyásával jön létre. Ellenkező esetben a lekérdezés az értékek rögzített sorrendjét fogja visszaadni.

  A lekérdezés RU-díja `OFFSET LIMIT` növekedni fog, ahogy az eltolási feltételek száma növekszik. Az [eredmények több oldalát](sql-query-pagination.md)tartalmazó lekérdezések esetében általában a [folytatási tokenek](sql-query-pagination.md#continuation-tokens)használatát javasoljuk. A folytatási tokenek egy "könyvjelző" azon hely számára, ahol a lekérdezés később folytatható. Ha használja `OFFSET LIMIT` , nincs "könyvjelző". Ha a lekérdezés következő lapját szeretné visszaadni, az elejétől kell kezdődnie.
  
  `OFFSET LIMIT`Olyan esetekre érdemes használni, amikor teljes egészében szeretné kihagyni az elemeket, és menteni az ügyfelek erőforrásait. `OFFSET LIMIT`Ha például a 1000th-lekérdezés eredményét szeretné kihagyni, és nem kell megtekintenie az 1 – 999 eredményt. A háttérben `OFFSET LIMIT` továbbra is minden elem betöltődik, beleértve a kihagyott elemeket is. A teljesítmény előnye az ügyfelek erőforrásainak megtakarítása, a nem szükséges elemek feldolgozásának elkerülésével.

## <a name="examples"></a>Példák

Például itt egy olyan lekérdezés, amely kihagyja az első értéket, és a második értéket adja vissza (a rezidens város nevének sorrendjében):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Az eredmény a következő:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Íme egy olyan lekérdezés, amely kihagyja az első értéket, és a második értéket adja vissza (megrendelés nélkül):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Az eredmény a következő:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [ORDER BY záradék](sql-query-order-by.md)
