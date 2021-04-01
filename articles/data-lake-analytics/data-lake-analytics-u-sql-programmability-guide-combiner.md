---
title: A U-SQL felhasználó által definiált combining Programming Guide for Azure Data Lake
description: Ismerje meg az U-SQL UDO-alapú programozhatóság útmutatóját – a felhasználó által definiált kombináló kombinációt.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512602"
---
# <a name="use-user-defined-combiner"></a>Felhasználó által definiált kombináló használata

## <a name="u-sql-udo-user-defined-combiner"></a>U-SQL UDO: felhasználó által definiált Combiner
A felhasználó által definiált Combiner vagy UDC lehetővé teszi, hogy egyéni logika alapján egyesítse a bal és a jobb oldali sorhalmaz sorait. A felhasználó által definiált kombinált szolgáltatás kombináló kifejezéssel van használatban.

## <a name="how-to-define-and-use-user-defined-combiner"></a>Felhasználó által definiált kombinálás meghatározása és használata

A rendszer összevonást hív meg a COMBINing kifejezéssel, amely biztosítja a szükséges információkat a bemeneti sorhalmazokkal, a csoportosítási oszlopokkal, a várt eredmény sémával és további információkkal.

A következő szintaxissal hívhat egy Combiner-et egy Base U-SQL-parancsfájlban:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

További információ: [Combine Expression (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Felhasználó által definiált kombinálás definiálásához létre kell hozni a `ICombiner` felületet a [ `SqlUserDefinedCombiner` ] attribútummal, amely nem kötelező a felhasználó által definiált Combiner-definíció esetében.

`ICombiner`Alaposztály definíciója:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Egy felület egyéni implementációjában `ICombiner` szerepelnie kell egy `IEnumerable<IRow>` kombinált felülbírálás definíciójának.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

A **SqlUserDefinedCombiner** attribútum azt jelzi, hogy a típust felhasználó által definiált kombináló kell regisztrálni. Ez az osztály nem örökölhető.

A **SqlUserDefinedCombiner** a Combiner Mode tulajdonság definiálására szolgál. A felhasználó által definiált Combiner-definíció opcionális attribútuma.

CombinerMode mód

A CombinerMode Enum a következő értékeket veheti fel:

* A teljes (0) minden kimeneti sor a bal és a jobb oldali bemeneti soroktól függ.

* A bal oldali (1) minden kimeneti sor a bal oldali bemeneti sorból (és a jobb oldali, azonos kulccsal rendelkező sorokból) függ.

* A jobb oldali (2) minden kimeneti sor a jobb oldaliból egy bemeneti sorból, a bal oldalitól pedig az azonos Key értékkel rendelkező soroktól függ.

* A belső (3) minden kimeneti sor a bal és a jobb oldali, azonos értékű bemeneti sorból függ.

Példa: [ `SqlUserDefinedCombiner(Mode=CombinerMode.Left)` ]


A fő programozható objektumok a következők:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

A bemeneti sorhalmazok a **bal** és a **jobb oldali** illesztőfelület-típus szerint lesznek átadva `IRowset` . Mindkét sorhalmazt fel kell sorolni a feldolgozáshoz. Az egyes felületeket csak egyszer lehet enumerálni, ezért szükség esetén fel kell sorolni és gyorsítótárazni kell.

Gyorsítótárazási célokra létrehozhatunk egy \<T\> adattípust a LINQ-lekérdezés végrehajtásának eredményeképpen, különösen<`IRow`>. A névtelen adattípus az enumerálás során is használható.

A IEnumerable felülettel kapcsolatos további információkért lásd: a [LINQ-lekérdezések bemutatása (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) . További információ a LINQ-lekérdezésekről és a [IEnumerable \<T\> felületről](/dotnet/api/system.collections.generic.ienumerable-1) \<T\> .

A bejövő adatok tényleges értékének lekéréséhez `IRowset` használja a Get () metódust az `IRow` illesztőfelületen.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Az egyes oszlopnevek meghatározása a séma metódusának meghívásával lehetséges `IRow` .

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Vagy a séma oszlopának neve alapján:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

A LINQ-sel való általános számbavétel a következőhöz hasonlóan néz ki:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Mindkét sorhalmaz enumerálása után az összes sort át fogjuk venni. A bal oldali sorhalmaz minden egyes sorában megtalálja az összes olyan sort, amely megfelel a kombinációnk feltételének.

A kimeneti értékeket a kimenet értékkel kell beállítani `IUpdatableRow` .

```csharp
output.Set<int>("mycolumn", mycolumn)
```

A tényleges kimenet a következő hívásával aktiválódik: `yield return output.AsReadOnly();` .

A következő egy kombináló példa:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

Ebben a használati helyzetben a kiskereskedelmi elemzési jelentést készítünk. A cél az, hogy megkeresse az összes olyan terméket, amely több mint $20 000, és hogy a webhelyhez képest gyorsabb, mint a normál kiskereskedő egy adott időszakon belül.

Itt látható az alap U-SQL-szkript. Összehasonlíthatja a logikát egy normál illesztés és egy kombináló között:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

A felhasználó által definiált kombináció hívható a (z) objektum új példánya:

```csharp
USING new MyNameSpace.MyCombiner();
```


Vagy egy burkoló gyári módszer meghívásával:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>Következő lépések
* [A U-SQL programozható útmutatója – áttekintés](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programozható útmutató – UDT és UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)