---
title: Skaláris kifejezések a Azure Cosmos DB SQL-lekérdezésekben
description: Ismerkedjen meg a skaláris kifejezés SQL-szintaxisával Azure Cosmos DB. Ez a cikk azt is ismerteti, hogyan lehet a skaláris kifejezéseket összetett kifejezésekkel egyesíteni a kezelők használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 34df2d2f4a2209cca93c3f7ac12dcd203bf4a089
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339631"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Skaláris kifejezések a Azure Cosmos DB SQL-lekérdezésekben
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A [Select záradék](sql-query-select.md) támogatja a skaláris kifejezéseket. A skaláris kifejezés olyan szimbólumok és operátorok kombinációja, amelyek kiértékelése egyetlen érték beszerzésére szolgál. Skaláris kifejezések például a következők: konstansok, tulajdonság-referenciák, tömb elemek hivatkozásai, alias-hivatkozások vagy függvények hívásai. A skaláris kifejezések összetett kifejezésekkel kombinálhatók a kezelők használatával.

## <a name="syntax"></a>Szintaxis
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argumentumok
  
- `<constant>`  
  
   Konstans értéket jelöl. Részletekért lásd az [állandók](sql-query-constants.md) szakaszt.  
  
- `input_alias`  
  
   A `input_alias` záradékban bemutatott értéket jelöli `FROM` .  
  Ez az érték nem **meghatározatlan** – a bemenetben nem **definiált** értékek vannak kihagyva.  
  
- `<scalar_expression>.property_name`  
  
   Egy objektum tulajdonságának értékét jelöli. Ha a tulajdonság nem létezik, vagy a tulajdonság egy értékre hivatkozik, amely nem objektum, a kifejezés kiértékelése nem **definiált** értékre történik.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Egy `property_name` tömb indexével rendelkező Name vagy Array elemet tartalmazó tulajdonság értékét jelöli `array_index` . Ha a tulajdonság/tömb indexe nem létezik, vagy a tulajdonság/tömb indexe nem objektum/tömb értékre hivatkozik, a kifejezés kiértékelése nem definiált értékre történik.  
  
- `unary_operator <scalar_expression>`  
  
   Egyetlen értékre alkalmazott operátort jelöl. Részletekért lásd: [operátorok](sql-query-operators.md) szakasz.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Két értékre alkalmazott operátort jelöl. Részletekért lásd: [operátorok](sql-query-operators.md) szakasz.  
  
- `<scalar_function_expression>`  
  
   Egy függvény hívásának eredményeképpen definiált értéket jelöli.  
  
- `udf_scalar_function`  
  
   A felhasználó által definiált skaláris függvény neve.  
  
- `builtin_scalar_function`  
  
   A beépített skaláris függvény neve.  
  
- `<create_object_expression>`  
  
   A megadott tulajdonságokkal és értékekkel rendelkező új objektum létrehozásával kapott értéket jelöli.  
  
- `<create_array_expression>`  
  
   A megadott értékekkel rendelkező új tömb létrehozásával kapott értéket jelöli.  
  
- `parameter_name`  
  
   A megadott paraméter nevének értékét jelöli. A paraméterek nevének első karakterének egyetlennek kell lennie \@ .  
  
## <a name="remarks"></a>Megjegyzések
  
  Beépített vagy felhasználó által definiált skaláris függvény meghívásakor az összes argumentumot meg kell adni. Ha az argumentumok bármelyike nincs meghatározva, a függvény nem lesz meghívva, és az eredmény nem lesz meghatározva.  
  
  Objektum létrehozásakor a rendszer kihagyja a nem definiált értékhez rendelt összes tulajdonságot, és a létrehozott objektumban nem fog szerepelni.  
  
  Tömb létrehozásakor a rendszer kihagyja a nem **definiált** értékhez rendelt elemet, és a létrehozott objektum nem tartalmazza azt. Ez azt eredményezi, hogy a következő definiált elem a helyére kerül, hogy a létrehozott tömb ne legyen kihagyva indexekkel.  

## <a name="examples"></a>Példák

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Az eredmény a következő:

```json
    [{
      "$1": 1.33333
    }]
```

A következő lekérdezésben a skaláris kifejezés eredménye egy logikai érték:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Az eredmény a következő:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Segédlekérdezések](sql-query-subquery.md)