---
title: Date_Bucket (Transact-SQL) – Azure SQL Edge
description: Tudnivalók a Date_Bucket használatáról az Azure SQL Edge-ben
keywords: Date_Bucket, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 9d81419721e94a2e181f094c0e0e64b1b23544a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073519"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

Ez a függvény az egyes datetime gyűjtők kezdetének megfelelő DateTime értéket adja vissza, a paraméterben megadott időbélyegből `origin` vagy az alapértelmezett Origó értékével, `1900-01-01 00:00:00.000` Ha nincs megadva a Origin paraméter. 

Az [adattípusok és függvények &#40;Transact-sql&#41;](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/) című témakörben tekintse át az összes Transact-SQL dátum és idő adattípust és funkciót.

[Transact-SQL-szintaxis konvenciói](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

## <a name="syntax"></a>Szintaxis

```sql
DATE_BUCKET (datePart, number, date, origin)
```

## <a name="arguments"></a>Argumentumok

*datePart*

A "Number" paraméterrel használt *dátum* része. Pl. Év, hónap, perc, másodperc stb.

> [!NOTE]
> `DATE_BUCKET` a nem fogadja el a felhasználó által definiált változó megfelelőjét a *datepPart* argumentumokhoz.
  
|*datePart*|Rövidítéseket|  
|---|---|
|**nap**|**dd**, **d**|  
|**héten**|**hét**, **ww**| 
|**hónap**|**mm**, **m**|
|**negyedév**|**QQ**, **q**|  
|**év**|**éé**, **éééé**|  
|**óra**|**óó**|  
|**perc**|**mi**, **n**|  
|**másodperc**|**SS**, **s**|  
|**ezredmásodperces**|**MS**|  

*száma*

Az egész szám, amely a gyűjtő szélességét határozza meg a *datePart* argumentummal együtt. Ez a dataPart-gyűjtők szélességét jelöli a forrás időpontból. **`This argument cannot be a negative integer value`**. 

*dátum*

Egy kifejezés, amely a következő értékek egyikére képes feloldani:

+ **dátum**
+ **dátum/idő**
+ **DateTimeOffset**
+ **datetime2**
+ **idő adattípusúra**
+ **idő**

A *Date (dátum* `DATE_BUCKET` ) oszlop kifejezéseket, kifejezéseket vagy felhasználó által definiált változókat fogad el, ha azok a fent említett adattípusok bármelyikére feloldhatók.

**Származási** 

Egy opcionális kifejezés, amely a következő értékek egyikére oldható fel:

+ **dátum**
+ **dátum/idő**
+ **DateTimeOffset**
+ **datetime2**
+ **idő adattípusúra**
+ **idő**

A (z) adattípus adattípusának `Origin` meg kell egyeznie a paraméter adattípusával `Date` . 

`DATE_BUCKET` az alapértelmezett származási dátum értékét használja, `1900-01-01 00:00:00.000` azaz az 12:00-as hétfőt, január 1 1900, ha nincs megadva a függvényhez tartozó forrás érték.

## <a name="return-type"></a>Visszatérési típus

A metódus visszatérési értékének adattípusa dinamikus. A visszatérési típus a megadott argumentumtól függ `date` . Ha érvényes bemeneti adattípust `date` ad meg, `DATE_BUCKET` a visszaadja ugyanazt az adattípust. `DATE_BUCKET` hibát jelez, ha a paraméterhez karakterlánc van megadva `date` .

## <a name="return-values"></a>Visszatérési értékek

### <a name="understanding-the-output-from-date_bucket"></a>A kimenet ismertetése `DATE_BUCKET`

`Date_Bucket` a datePart és a Number paraméternek megfelelő legutóbbi dátum-vagy időértéket adja vissza. Az alábbi kifejezésekben például a a `Date_Bucket` kimeneti értéket adja vissza `2020-04-13 00:00:00.0000000` , mivel a kimenet kiszámítása az alapértelmezett származási időponttól számított egy hetes gyűjtő alapján történik `1900-01-01 00:00:00.000` . Az érték `2020-04-13 00:00:00.0000000` 6276 hét a forrás értékétől számítva `1900-01-01 00:00:00.000` . 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

Az alábbi kifejezésekhez a rendszer ugyanazt a kimeneti értéket `2020-04-13 00:00:00.0000000` adja vissza. Ennek az az oka, hogy `2020-04-13 00:00:00.0000000` az eredeti dátum 6276 hét, a 6276 pedig 2, 3, 4 és 6.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

Az alábbi kifejezés kimenete a következő: `2020-04-06 00:00:00.0000000` , amely az alapértelmezett origó 6275 hét `1900-01-01 00:00:00.000` .

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

Az alábbi kifejezés kimenete: `2020-06-09 00:00:00.0000000` , amely 75 héttel a megadott származási időben `2019-01-01 00:00:00` .

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(wk, 5, @date, @origin)
```

## <a name="datepart-argument"></a>DatePart argumentum

a **DAYOFYEAR**, a **nap** és a **hétköznap** ugyanazt az értéket adják vissza. Minden *DatePart* és a hozzá tartozó rövidítések ugyanazt az értéket adják vissza.
  
## <a name="number-argument"></a>szám argumentum

A *Number* argumentum nem lépheti túl a pozitív **int** értékek tartományát. Az alábbi utasításokban a *szám* argumentuma meghaladja az **int** tartományát (1). A következő utasítás a következő hibaüzenetet adja vissza: "`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

Ha a függvény negatív értéket ad a számnak `Date_Bucket` , a rendszer a következő hibát adja vissza. 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>dátum argumentum  

`DATE_BUCKET` az argumentum adattípusának megfelelő alapértéket adja vissza `date` . A következő példában egy datetime2 adattípusú kimeneti érték lesz visszaadva. 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="origin-argument"></a>forrás argumentum  

A `origin` és argumentumok adattípusának `date` meg kell egyeznie. Ha különböző adattípusokat használ, a rendszer hibát generál.

## <a name="remarks"></a>Megjegyzések

`DATE_BUCKET`A következő záradékokban használható:

+ GROUP BY
+ HAVING
+ ORDER BY
+ Válassza \<list>
+ WHERE

## <a name="examples"></a>Példák

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>A. Date_Bucket kiszámítása a forrás időpontjának 1. vastagságával

A fenti utasítások mindegyike a forrás időpontjától számított 1. számú gyűjtővel *date_bucket* .

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

Itt látható az eredményhalmaz.

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. Kifejezések használata argumentumként a szám és a dátum paraméterekhez

Ezek a példák különböző típusú kifejezéseket használnak argumentumként a *szám* és a *dátum* paraméterekhez. Ezek a példák az "AdventureWorksDW2017" adatbázis használatával készültek.
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>Felhasználó által definiált változók megadása számként és dátumként  

Ez a példa a felhasználó által definiált változókat adja meg a *szám* és a *dátum* argumentumként:
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

Itt látható az eredményhalmaz.

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>Oszlop megadásának dátuma

Az alábbi példában számításba vesszük az OrderQuantity összegét és az egységárat a heti dátum gyűjtők szerint csoportosítva.
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

Itt látható az eredményhalmaz.
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>Skaláris rendszerfüggvény meghatározása dátumként

Ez a példa `SYSDATETIME` a *dátumot* adja meg. A visszaadott pontos érték az utasítás végrehajtásának napjától és időpontjától függ:
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

Itt látható az eredményhalmaz.

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>Skaláris allekérdezések és skaláris függvények meghatározása számként és dátumként

Ez a példa skaláris allekérdezéseket használ, a `MAX(OrderDate)` *szám* és a *dátum* argumentumként. `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` mesterséges argumentumként szolgál a Number paraméterhez, hogy megmutassa, hogyan válasszon ki egy *Number* argumentumot egy érték listából.
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>Numerikus kifejezések és skaláris rendszerfüggvények meghatározása számként és dátumként

Ez a példa egy numerikus kifejezést ((10/2)) és egy skaláris rendszerfüggvényt (SYSDATETIME) használ a szám és a dátum argumentumként.
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>Összesítő ablak függvényének meghatározása számként

Ez a példa összesítő ablak függvényt használ a *szám* argumentumként.
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 
### <a name="c-using-a-non-default-origin-value"></a>C. Nem alapértelmezett Origin érték használata

Ez a példa egy nem alapértelmezett orgin értéket használ a dátum gyűjtők létrehozásához. 

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(hh, 2, @date, @origin)
```

## <a name="see-also"></a>Lásd még

[&#40;Transact-SQL-&#41;elküldése és konvertálása ](/sql/t-sql/functions/cast-and-convert-transact-sql/)
