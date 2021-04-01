---
title: A U-SQL felhasználó által definiált betekintő útmutatója Azure Data Lake
description: Ismerje meg az U-SQL UDO-alapú programozhatóság útmutatóját – a felhasználó által definiált bevezetőt.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512571"
---
# <a name="use-user-defined-applier"></a>Felhasználó által definiált alkalmazó használata 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL UDO: felhasználó által megadott gyártó
Egy U-SQL felhasználó által definiált szolgáltató lehetővé teszi egy egyéni C#-függvény meghívását minden olyan sorhoz, amelyet a lekérdezés külső tábla kifejezése adott vissza. A rendszer kiértékeli a megfelelő bemenetet a bal oldali bemenet minden egyes sorára, és a létrehozott sorok a végső kimenethez kombinálhatók. Az APPLY operátor által létrehozott oszlopok listája a bal oldali és a jobb oldali bemenet oszlopainak kombinációja.


## <a name="how-to-define-and-use-user-defined-applier"></a>Felhasználó által definiált szolgáltató definiálása és használata
A felhasználó által definiált bejelentkezőt a USQL SELECT kifejezés részeként kell meghívni.

A felhasználó által definiált betekintő a következőhöz hasonlóan néz ki:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Ha további információt szeretne arról, hogyan használhatók a megjelölések a SELECT kifejezésben, tekintse meg a [U-SQL kiválasztási lehetőséget a Cross Apply és a Outer alkalmazásban](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

A felhasználó által megadott, a következő alaposztály definíciója a következő:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Felhasználó által definiált hozzárendelő megadásakor létre kell hoznia a `IApplier` felületet a [ `SqlUserDefinedApplier` ] attribútummal, amely nem kötelező a felhasználó által definiált hozzárendelt-definícióhoz.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* A rendszer a külső tábla minden egyes sorára alkalmazza a kérelmet. A `IUpdatableRow` kimeneti sorhalmazt adja vissza.
* A konstruktor osztály paraméterek átadására szolgál a felhasználó által megadott előfizetéshez.

A **SqlUserDefinedApplier** azt jelzi, hogy a típust felhasználó által definiált fogadóként kell regisztrálni. Ez az osztály nem örökölhető.

A felhasználó által definiált **SqlUserDefinedApplier** megadása nem **kötelező** .


A fő programozható objektumok a következők:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

A bemeneti sorhalmazok bemenetként lesznek átadva `IRow` . A kimeneti sorok `IUpdatableRow` kimeneti illesztőfelületként jönnek létre.

Az egyes oszlopnevek meghatározása a séma metódusának meghívásával lehetséges `IRow` .

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

A bejövő adatok tényleges értékének lekéréséhez `IRow` használja a Get () metódust az `IRow` illesztőfelületen.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Vagy a séma oszlop nevét használjuk:

```csharp
row.Get<int>(row.Schema[0].Name)
```

A kimeneti értékeket a `IUpdatableRow` kimenettel kell beállítani:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Fontos tisztában lennie azzal, hogy az egyéni használóknak csak a metódus hívásával megadott kimeneti oszlopokat és értékeket kell megadniuk `output.Set` .

A tényleges kimenet meghívásával aktiválódik `yield return output.AsReadOnly();` .

A felhasználó által megadott, a kivitelezőnek átadandó paraméterek a konstruktornak adhatók át. A berendelő olyan változó oszlopok számát adja vissza, amelyeket a rendszer az alap U-SQL-parancsfájlban definiált, a beléptetési hívás során meg kell határozni.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

A felhasználó által definiált példa a következő:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

A következő a felhasználó által definiált előtagja számára az alap U-SQL-szkript:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Ebben a használati esetben a felhasználó által definiált szolgáltató vesszővel tagolt érték-elemzőként működik az autó-flotta tulajdonságainál. A bemeneti fájl sorai a következőhöz hasonlóan néznek ki:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Ez egy tipikus, tabulátorral tagolt TSV-fájl, amely egy olyan tulajdonságlapot tartalmaz, amely az autó tulajdonságait, például a make és a Model tulajdonságot tartalmazza. Ezeket a tulajdonságokat a táblázat oszlopaiba kell elemezni. A megadott szolgáltató azt is lehetővé teszi, hogy dinamikus számú tulajdonságot hozzon az eredmény-sorhalmazban az átadott paraméter alapján. Létrehozhatja az összes tulajdonságot, illetve a tulajdonságok egy adott halmazát is.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

A felhasználó által definiált berendelő a (z) rendszerű alkalmazás új példányának hívható:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Vagy egy burkoló gyári módszer meghívásával:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>Következő lépések
* [A U-SQL programozható útmutatója – áttekintés](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programozható útmutató – UDT és UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)