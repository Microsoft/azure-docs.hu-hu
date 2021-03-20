---
title: ORDER BY záradék Azure Cosmos DB
description: Az SQL ORDER BY záradék megismerése Azure Cosmos DB. Az SQL használata Azure Cosmos DB JSON-lekérdezési nyelvként.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: 887dc13eb5e351688718d2a221e69499557b23e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338297"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY záradék Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A választható `ORDER BY` záradék a lekérdezés által visszaadott eredmények rendezési sorrendjét határozza meg.

## <a name="syntax"></a>Szintaxis
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentumok
  
- `<sort_specification>`  
  
   Megadja azt a tulajdonságot vagy kifejezést, amelyen a lekérdezési eredményhalmaz rendezése történik. A rendezési oszlop nevet vagy tulajdonság-aliasként is megadható.  
  
   Több tulajdonság is megadható. A tulajdonságok nevének egyedinek kell lennie. A rendezési tulajdonságok sorrendje a `ORDER BY` záradékban a rendezett eredményhalmaz szervezetét határozza meg. Ez azt jelenti, hogy az eredményhalmaz az első tulajdonság szerint van rendezve, és a rendezett listát a második tulajdonság rendezi, és így tovább.  
  
   A záradékban hivatkozott tulajdonságnév a kiválasztási `ORDER BY` listán szereplő tulajdonságnak vagy a záradékban megadott gyűjteményben, kétértelműség nélkül definiált tulajdonságnak kell lennie `FROM` .  
  
- `<sort_expression>`  
  
   Egy vagy több olyan tulajdonságot vagy kifejezést ad meg, amelyeken a lekérdezési eredményhalmaz sorba rendezésére kerül sor.  
  
- `<scalar_expression>`  
  
   A részletekért tekintse meg a [skaláris kifejezések](sql-query-scalar-expressions.md) szakaszt.  
  
- `ASC | DESC`  
  
   Megadja, hogy a megadott oszlopban szereplő értékeket növekvő vagy csökkenő sorrendbe kell rendezni. `ASC` a legalacsonyabb értékről a legmagasabb értékre rendezi a rendszer. `DESC` a legmagasabb értékről a legalacsonyabb értékre rendezi a rendszer. `ASC` az alapértelmezett rendezési sorrend. A null értékeket a lehető legalacsonyabb értékként kezeli a rendszer.  
  
## <a name="remarks"></a>Megjegyzések  
  
   A `ORDER BY` záradék megköveteli, hogy az indexelési házirend tartalmazzon egy indexet a rendezés alatt álló mezőkhöz. A Azure Cosmos DB lekérdezési futtatókörnyezet támogatja a tulajdonságok neve szerinti rendezést, és nem a számított tulajdonságokkal. Azure Cosmos DB több `ORDER BY` tulajdonságot is támogat. Ha több SORRENDet tartalmazó lekérdezést szeretne futtatni, definiálnia kell egy [összetett indexet](index-policy.md#composite-indexes) a rendezés alatt álló mezőkben.

> [!Note]
> Ha a rendezés alatt álló tulajdonságok egyes dokumentumok esetében nem definiálhatók, és lekérdezési sorrendben szeretné lekérni őket, explicit módon fel kell vennie ezt az útvonalat az indexbe. Az alapértelmezett indexelési házirend nem engedélyezi a dokumentumok lekérését, ha a rendezési tulajdonság nincs definiálva. [Tekintse át például a dokumentumok lekérdezéseit néhány hiányzó mezővel](#documents-with-missing-fields).

## <a name="examples"></a>Példák

Például itt egy olyan lekérdezés, amely a családokat a rezidens város nevének növekvő sorrendjében kéri le:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Az eredmény a következő:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

A következő lekérdezés lekéri a családot `id` az elemek létrehozási dátumának megfelelően. Az elem `creationDate` az a szám, amely az *időpontot* jelöli, vagy az eltelt idő a Jan. 1, 1970 másodpercben.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Az eredmény a következő:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Emellett több tulajdonság is megrendelhető. A több tulajdonság által megrendelt lekérdezéseknek [összetett indexre](index-policy.md#composite-indexes)van szükségük. Tekintse meg a következő lekérdezést:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Ez a lekérdezés a `id` város nevének növekvő sorrendjében kérdezi le a családot. Ha több elem neve megegyezik a város nevével, a lekérdezés sorrendjét `creationDate` csökkenő sorrendbe kell rendezni.

## <a name="documents-with-missing-fields"></a>Hiányzó mezőket tartalmazó dokumentumok

Azok a lekérdezések, `ORDER BY` amelyek az alapértelmezett indexelési házirenddel rendelkező tárolók ellen futnak, nem adnak vissza olyan dokumentumokat, amelyekben a rendezés tulajdonság nincs meghatározva. Ha olyan dokumentumokat szeretne szerepeltetni, amelyekben a rendezési tulajdonság nincs meghatározva, explicit módon fel kell vennie ezt a tulajdonságot az indexelési házirendbe.

Például itt található egy olyan indexelési szabályzattal rendelkező tároló, amely nem tartalmaz explicit módon olyan elérési utakat, mint a következő `"/*"` :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Ha olyan lekérdezést futtat, amely tartalmazza `lastName` a `Order By` záradékot, akkor az eredmények csak azokat a dokumentumokat tartalmazzák, amelyeken a `lastName` tulajdonság definiálva van. Nem határoztak meg explicit módon megadott elérési utat, hogy a `lastName` -t nem tartalmazó dokumentumok `lastName` ne jelenjenek meg a lekérdezés eredményei között.

Az alábbi két dokumentum alapján rendezi a lekérdezéseket `lastName` , amelyek közül az egyiknek nincs `lastName` definiálva:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Az eredmények csak a definiált dokumentumot tartalmazzák `lastName` :

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Ha a tároló indexelési házirendjét úgy frissítjük, hogy explicit módon tartalmazzon egy elérési utat `lastName` , a lekérdezés eredményei között a nem definiált rendezési tulajdonsággal rendelkező dokumentumok is szerepelni fognak. Explicit módon meg kell határozni az elérési utat a skaláris értékhez (és nem a fentiekhez képest). Az `?` indexelési házirendben az elérésiút-definícióban szereplő karaktert kell használnia, így biztosítva, hogy explicit módon indexelje a tulajdonságot `lastName` , és ne legyenek további beágyazott útvonalak. Ha a `Order By` lekérdezés [összetett indexet](index-policy.md#composite-indexes)használ, az eredmények mindig tartalmazzák a lekérdezés eredményeiben a nem definiált rendezési tulajdonsággal rendelkező dokumentumokat.

Itt látható egy minta indexelési házirend, amely lehetővé teszi, hogy a nem definiált dokumentumok `lastName` megjelenjenek a lekérdezés eredményei között:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Ha ugyanezt a lekérdezést is futtatja, akkor a hiányzó dokumentumok `lastName` először a lekérdezés eredményeiben jelennek meg:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Az eredmény a következő:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Ha módosítja a rendezési sorrendet `DESC` , a hiányzó dokumentumok `lastName` a lekérdezés eredményei között utolsóként jelennek meg:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Az eredmény a következő:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> Csak a .NET SDK 3.4.0 vagy újabb verziója támogatja a SORRENDet vegyes típusokkal. Ezért ha nem definiált és definiált értékek kombinációját szeretné rendezni, ezt a verziót (vagy újabb) kell használnia.

Nem szabályozhatja, hogy a különböző típusok milyen sorrendben jelenjenek meg az eredmények között. A fenti példában megmutatjuk, hogyan rendezték a nem definiált értékeket a karakterlánc értékei előtt. Ha például inkább a nem definiált értékek rendezését szeretné vezérelni, akkor a nem definiált tulajdonságokat hozzárendelhet egy "aaaaaaaaa" vagy "zzzzzzzz" karakterlánc-értéket, hogy az első vagy az utolsó legyen.

## <a name="next-steps"></a>Következő lépések

- [Bevezetés](sql-query-getting-started.md)
- [Az Azure Cosmos DB indexelési szabályzatai](index-policy.md)
- [ELTOLÁSi korlát záradéka](sql-query-offset-limit.md)
