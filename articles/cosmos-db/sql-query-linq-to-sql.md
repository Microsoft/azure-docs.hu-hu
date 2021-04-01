---
title: LINQ to SQL fordítás Azure Cosmos DB
description: Ismerje meg, hogy a LINQ-operátorok támogatottak-e, és hogy a LINQ-lekérdezések hogyan vannak leképezve a Azure Cosmos DB SQL-
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: tisande
ms.openlocfilehash: 122c95fe9ac017ad7a6957dcdb8323837be34f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545383"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL fordítás
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB lekérdezési szolgáltató a LINQ-lekérdezésből származó legjobb műveletet hajtja végre egy Cosmos DB SQL-lekérdezésben. Ha a LINQ-ből lefordított SQL-lekérdezést szeretné beszerezni, használja a `ToString()` metódust a generált `IQueryable` objektumon. A következő leírás feltételezi, hogy a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries)alapvető ismerete. A LINQen kívül a Azure Cosmos DB támogatja az SQL API-val használható [Entity Framework Core](/ef/core/providers/cosmos/?tabs=dotnet-core-cli) -t is.

A lekérdezés-szolgáltató típusa a rendszeren csak a JSON egyszerű típusokat támogatja: numerikus, logikai, karakterlánc és null.

A lekérdezési szolgáltató a következő skaláris kifejezéseket támogatja:

- Állandó értékek, beleértve a primitív adattípusok állandó értékeit a lekérdezés kiértékelési idején.
  
- Tulajdonság/tömb index kifejezés, amely egy objektum vagy egy tömb elem tulajdonságára hivatkozik. Például:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetikai kifejezések, beleértve a közös aritmetikai kifejezéseket a numerikus és a logikai értékeken. A teljes listát lásd: [Azure Cosmos db SQL-specifikáció](sql-query-aggregate-functions.md).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Karakterlánc-összehasonlító kifejezések, amelyek között szerepel egy karakterlánc-érték összehasonlítása egy konstans karakterlánc-értékkel.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Objektum/tömb létrehozási kifejezése, amely összetett értéktípus vagy névtelen típus vagy ilyen objektumok tömbjét adja vissza. Ezeket az értékeket beágyazhatja.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="using-linq"></a>A LINQ használata

LINQ-lekérdezést hozhat létre a használatával `GetItemLinqQueryable` . Ez a példa a LINQ-lekérdezés létrehozását és aszinkron végrehajtását mutatja be a következővel `FeedIterator` :

```csharp
using (FeedIterator<Book> setIterator = container.GetItemLinqQueryable<Book>()
                      .Where(b => b.Title == "War and Peace")
                      .ToFeedIterator<Book>())
 {
     //Asynchronous query execution
     while (setIterator.HasMoreResults)
     {
         foreach(var item in await setIterator.ReadNextAsync()){
         {
             Console.WriteLine(item.cost);
         }
       }
     }
 }
```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Támogatott LINQ-operátorok

Az SQL .NET SDK-ban található LINQ Provider a következő operátorokat támogatja:

- **Válassza ki** a kivetítéseket a [kiválasztáshoz](sql-query-select.md), beleértve az objektumok kialakítását.
- **Hol**: a szűrők a [](sql-query-where.md)és a, `&&` `||` valamint `!` az SQL-operátorok közötti fordítást támogatják.
- **SelectMany**: lehetővé teszi a tömbök felcsévélését az [JOIN](sql-query-join.md) záradékba. A paranccsal a tömb elemeinek szűréséhez használhatja a kifejezéseket.
- **OrderBy** és **OrderByDescending**: lefordítható az ASC vagy a desc [szerinti sorrendbe](sql-query-order-by.md) .
- Az [összesítéshez](sql-query-aggregate-functions.md)a **Count**, a **Sum**, a **min**, a **Max** és az **átlag** operátor, valamint a **CountAsync**, a **SumAsync**, a **MinAsync**, a **MaxAsync** és a **AverageAsync**.
- **Compareto metódus végrehajtása**: a tartomány-összehasonlításokra fordít. Általában a sztringek esetében használatos, mivel azok nem összehasonlíthatóak a .NET-ben.
- **Kihagyás** és **végrehajtás:** lefordítja az [eltolást, és korlátozza](sql-query-offset-limit.md) a lekérdezés eredményeinek korlátozását és tördelését.
- **Matematikai függvények**: a .net,,,,,,,,,,,,,,,, `Abs` `Acos` `Asin` `Atan` `Ceiling` `Cos` `Exp` `Floor` `Log` `Log10` `Pow` `Round` `Sign` `Sin` `Sqrt` `Tan` és `Truncate` az egyenértékű [beépített matematikai funkciókhoz](sql-query-mathematical-functions.md)való fordítást támogatja.
- **Karakterlánc-függvények**: támogatja a .net,,,,,,,,,, `Concat` `Contains` `Count` és, `EndsWith` `IndexOf` `Replace` `Reverse` `StartsWith` `SubString` `ToLower` `ToUpper` `TrimEnd` és `TrimStart` az egyenértékű [beépített karakterlánc-függvények](sql-query-string-functions.md)fordítását.
- **Array functions**: támogatja a .net-ről való fordítást, `Concat` `Contains` valamint `Count` a [beépített Array függvények](sql-query-array-functions.md)használatát.
- **Térinformatikai bővítmény függvények**: a helyettes metódusokból `Distance` , `IsValid` , `IsValidDetailed` és `Within` a [beépített térinformatikai függvényekbe](sql-query-geospatial-query.md)való fordítást támogatja.
- **Felhasználó által definiált függvény kiterjesztési funkciója**: a helyettes metódusból `UserDefinedFunctionProvider.Invoke` a megfelelő [felhasználó által definiált függvénybe](sql-query-udfs.md)való fordítást támogatja.
- **Egyéb**: támogatja a `Coalesce` és a feltételes [operátorok](sql-query-operators.md)fordítását. A (z `Contains` ) karakterláncot a (z), ARRAY_CONTAINS, vagy a (z) sztringre fordíthatja a kontextustól függően

## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan fordítja le a standard LINQ Query operátorok a Azure Cosmos DB lekérdezéseit.

### <a name="select-operator"></a>Operátor kiválasztása

A szintaxis az `input.Select(x => f(x))` , ahol a egy `f` skaláris kifejezés. A `input` , ebben az esetben egy `IQueryable` objektum lenne.

**Operátor kiválasztása, 1. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Operátor kiválasztása, 2. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Operátor kiválasztása, 3. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>SelectMany operátor

A szintaxis az `input.SelectMany(x => f(x))` , ahol a egy `f` skaláris kifejezés, amely egy tároló típusát adja vissza.

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Where operátor

A szintaxis az `input.Where(x => f(x))` , ahol a egy `f` skaláris kifejezés, amely egy logikai értéket ad vissza.

**Where operátor, 1. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Where operátor, 2. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Összetett SQL-lekérdezések

Az előző operátorokat az erősebb lekérdezések létrehozásához is létrehozhatja. Mivel a Cosmos DB támogatja a beágyazott tárolókat, összefűzheti vagy beágyazhatja az összeállítást.

### <a name="concatenation"></a>Összefűzés

A szintaxis a következő: `input(.|.SelectMany())(.Select()|.Where())*`. Az összefűzött lekérdezések egy opcionális `SelectMany` lekérdezéssel kezdődhetnek, amelyet több `Select` vagy operátor is követ `Where` .

**Összefűzés, 1. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Összefűzés, 2. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Összefűzés, 3. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Összefűzés, 4. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Fészkelő

A szintaxis az `input.SelectMany(x=>x.Q())` `Q` a, a `Select` `SelectMany` vagy az `Where` operátor.

Egy beágyazott lekérdezés a belső lekérdezést alkalmazza a külső tároló minden elemére. Az egyik fontos funkció, hogy a belső lekérdezés hivatkozhat a külső tárolóban található elemek mezőire, például egy önillesztésre.

**Beágyazás, 1. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Beágyazás, 2. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Beágyazás, 3. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a name="next-steps"></a>Következő lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentumadatok modellezése](modeling-data.md)